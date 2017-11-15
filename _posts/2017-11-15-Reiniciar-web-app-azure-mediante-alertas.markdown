---
layout: post
title:  "Reiniciar Azure Web App mediante alertas"
date:   2017-11-15 12:00:00
categories: azure webapp webapi alerts function serverless
---
# >_

## Escenario

Imagina que tienes una WebApp o Web API hosteada en Azure que, por algún motivo, de tanto en tanto se "engancha".

En mi caso, se trata de una WebApp que contiene un WebJob. Este WebJob está escuchando una subscripción de Azure Service Bus, de modo que se lanza un proceso de cálculo cada vez que la subscripción correspondiente recibe un evento.

Y por algún motivo, en contadas ocasiones el proceso se bloquea, manteniendo la CPU al 100%, sin otra solución que reiniciar la aplicación web y/o el WebJob afectado.

Siendo consciente que hay algún problema en el código o en la infraestructura escogida, mientras no se consigue aislar y resolver el problema, es necesario asegurar el máximo tiempo de *availability* posible del servicio web sin que alguien tenga que estar supervisando su estado 24x7.

Y para ello, he recurrido a implementar una solución de automatización que reinicia el servicio afectado utilizando ***App Service Alerts*** y ***Azure Functions***.

## App Service Alerts

Todo Azure Website dispone de una sección de **Alertas**.

![Azure Websites Alerts menu screenshot](/assets/azure-website-alerts-location.jpg)

Dichas alertas permiten configurar y controlar diferentes métricas, como el flujo de datos de entrada o de salida, el tiempo de CPU, el número de HTTP requests recibidas o las respuestas generadas.

Para un caso de bloqueo de CPU, el indicador o métrica que me parece más apropiado es el de ***CPU Time***. Esta métrica permite activar una alerta cuando el tiempo de CPU de la App sobrepasa, en segundos, el tiempo definido en el cuadro correspondiente (*Umbral*).

![Azure Website Alerts Edition screenshot](/assets/azure-website-alerts.jpg)
> **Nota**: No consideréis los valores que contiene la captura de pantalla: no son los originales, sino que son valores de demostración creados para este post.

De media, mi proceso tarda unos 15 segundos en procesar una petición, pero depende mucho de la carga en Azure Service Bus, por lo que he optado por configurar un valor de 120 segundos; el suficiente para que no sea demasiado restrictivo pero tampoco demasiado permisivo.

Cuando se lanza una alerta, es posible configurar el envío de un correo electrónico a los administradores del Site, a una lista de correos electrónicos configurable y/o mandar el evento a una URL donde esté escuchando un servicio Webhook.

Para este escenario, no necesito que se informe por correo electrónico a nadie, sino que se actúe automáticamente ante la activación de un evento concreto.

Así pues, si la aplicación sobrepasa los 120 segundos de CPU durante los últimos 5 minutos... se generará una alerta que publicará un evento en la URL del Webhook que hayas configurado. Concretamente, en este escenario, la URL de una ***Azure Function***.

Para conseguir dicha URL, sigue leyendo. ;)

## Azure Functions

**[Azure Functions](https://azure.microsoft.com/en-us/services/functions/)** es un servicio serverless de Microsoft, que permite desplegar aplicaciones de diversa tipología en un entorno altamente escalable, sin tener que preocuparse de los temas asociados de infraestructura.

Los tipos de aplicaciones que admite este servicio van desde servicios backend para Mobile hasta un servicio event-based suscrito a otros servicios de Azure como *Azure Service Bus* o *Azure Storage*.

Una Azure Function puede ser programada en C#, F#, Javascript, PHP, bash, batch... y Powershell. Además, el portal permite usar plantillas predefinidas para los diferentes tipos de escenarios: API & Webhooks, Procesado de datos, Supervisión, IoT Hub o Core.

En el caso del que trata este post, necesito una Azure Function que reaccione ante la invocación mediante Webhook. Hay tres tipos de plantillas en C# disponibles en este momento para este tipo de Azure Function: Github webhook, Slack webhook y **General webhook**, que es la que vamos a seleccionar.

El problema aquí es que no existe una plantilla para Powershell de este tipo, por lo que los pasos que se deben seguir para adaptar la plantilla de General Webhook en C# a Powershell son:

* Borrar el fichero **run.csx**
* Crear un fichero **run.ps1**
* Añadir el código necesario a este fichero **run.ps1**
* Reiniciar la instancia de Function Apps.

## URL de consumo de una Azure Function

En realidad la URL de servicio de la Azure Function, una vez creada, es sencilla de obtener.

Simplemente debes navegar a la instancia de Azure Function donde está tu función, seleccionar dicha función y en la parte superior derecha de la sección donde el portal de Azure te muestra el código, hacer clic en la opción **</> Obtener la dirección URL de la función**.

¿Por qué usar esta URL y no construirla a partir de la URL de la instancia? Pues básicamente porque desde aquí obtendrás una URL con un token de autenticación, que es necesario para poder invocar la Azure Function, sea desde el webhook de alertas o desde cualquier otro punto.

Esta URL es, por tanto, la que debemos utilizar en la configuración de la alerta.

## Automatización del reboot

Buscando cómo implementar la idea que planteaba en el escenario, encontré este fantástico [post acerca de cómo utilizar Azure Functions para gestionar VMs en Azure](https://blog.kloud.com.au/2017/01/06/how-to-create-an-azure-function-app-to-simultaneously-startstop-all-virtual-machines-in-a-resource-group/).

Gracias a la gran explicación de su autor, pude crear un script de aprovisionamiento basado en su idea original, pero pensado únicamente para gestionar WebSites en Azure.

Para ello, uno de los cambios necesarios es sustituir el rol `DevTest Labs User` por el rol `Website Contributor` en el código original. Este último rol dispone de autorización para arrancar, parar y reiniciar WebSites de la subscripción, por lo que para mi objetivo es el más adecuado. [Toda la documentación oficial sobre roles, aquí](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-built-in-roles).

El script resultante que permite registrar una Azure Active Directory Application, que sea la encargada de ejecutar la acción de reinicio del Website, es el siguiente.

```powershell
$url = "<your_azure_function_url>"
$plainPassword = "<your_very_secure_non_final_password>"
$path = "<filename>.key"

# Login with your Azure Admin Account
Add-AzureRmAccount

# Create an App that will be used by the Azure Automation Function App
$app = New-AzureRmADApplication -DisplayName "Swords Alert Function App" -HomePage $url -IdentifierUris $url -Password $plainPassword

# Copy the AppID to the clipboard
$app.ApplicationId.Guid

# Create the App Service Principal
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

# wait for replication
Start-Sleep 30

# Assign Role DevTest Labs User which provides the ability to view everything and connect, start, restart, and shutdown virtual machines
New-AzureRmRoleAssignment -RoleDefinitionName 'Website Contributor' -ServicePrincipalName $app.ApplicationId

# Get the TenantID (I only have one)
$tenant = (Get-AzureRmSubscription).TenantId
$tenant 

$AESKey = New-Object Byte[] 32
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey)

# file to output the encrypted key too
Set-Content $path $AESKey
Function Get-EncryptedPassword
{
  param (
    [Parameter(Mandatory=$true,HelpMessage='Please specify the key file path')][ValidateScript({Test-Path $_})][String]$KeyPath,
    [Parameter(Mandatory=$true,HelpMessage='Please specify password in clear text')][ValidateNotNullOrEmpty()][String]$Password
  )
  $secPw = ConvertTo-SecureString -AsPlainText $Password -Force
  $AESKey = Get-content $KeyPath
  $Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
  $Encryptedpassword
}

$encryptedpass = Get-EncryptedPassword -KeyPath $path -Password $plainPassword
# Get the encryption string and copy to the clipboard for use in app config 
$encryptedpass | clip

## TEST
# Application ID for our Azure Security Principal that we created and provided via Function Application Settings
$username = $app.ApplicationId.Guid

# Password for connection to Azure via Function Application Settings
$pw = $encryptedpass

# Credentials password (encrypted)
$keypath = $path
$password = $pw | ConvertTo-SecureString -key (Get-Content $keypath)

# Create PS Creds
$credentials = New-Object System.Management.Automation.PSCredential $username,$password

# Login
$AzureRMAccount = Add-AzureRmAccount -Credential $credentials -ServicePrincipal -TenantId $tenant
```

Por otra parte, tenemos que preparar la Azure Function que hará el restart del App Service que la invoque. Esta función está también escrita en Powershell:

```powershell
# Get the input request
$in = Get-Content $req -Raw
$json = ConvertFrom-Json $in

if ($json.status -eq "Resolved") {
   Write-output "Status is " $json.status
}
else {
    Write-Output $json.status
    Write-Output $json.context.resourceGroupName
    Write-Output $json.context.resourceName
    
    # Application ID for our Azure Security Principal that we created and provided via Function Application Settings
    $username = $env:AzureAutomationAppID

    # Password for connection to Azure via Function Application Settings
    $pw = $env:AzureAutomationPWD
    $key = Get-Content 'D:\home\site\wwwroot\<your_function>\<yourkeyfilename.key>'
    $password = $pw | ConvertTo-SecureString -key $key
    $credentials = New-Object System.Management.Automation.PSCredential $username,$password
    $AzureRMAccount = Add-AzureRmAccount -Credential $credentials -ServicePrincipal -TenantId $env:AzureAutomationTenantID
    If ($AzureRMAccount) { 
        Restart-AzureRmWebApp -ResourceGroupName $json.context.resourceGroupName -Name $json.context.resourceName
        write-output "==== WebApp restarted ====" $json.context.resourceName
    }
}
```

Puedes encontrar la [Azure Function completa en este repositorio de GitHub](https://github.com/huorswords/alert-function-reboot)

Recuerda que para que funcione, debes añadir al **Application Settings** de la instancia de Azure Function los valores correspondientes para:

| Nombre | Valor |
|-|-|
| AzureAutomationPWD | Password securizado generado en la variable `$Encryptedpassword` del script de aprovisionamiento. |
| AzureAutomationAppID | Identificador GUID de la Azure AD Application creada en el script de aprovisionamiento. |
| AzureAutomationTenantID | Tenant Id al que pertenece el App Service a reiniciar.|

Y, por último, debes configurar la alerta, e indicar como URL de Webhook la URL de la Azure Function creada con el código anterior.

Voilá!

## Proyecto demo: Forzar uso intensivo de CPU

Para probar la estructura y forzar el uso intensivo de CPU, recurrí a implementar una API en .NET Core que siguiese el [procedimiento sugerido en esta respuesta de StackOverflow](https://stackoverflow.com/a/13001749/982431), que básicamente pasa por calcular números primos. Contra más alto el valor de referencia, más tarda el proceso en calcular todos los números primos del rango indicado.

El [código de la WebApp de prueba lo puedes encontrar en éste repo de Github](https://github.com/huorswords/TooHighCpuSimulatorApi). Yo lo he desplegado en un App Service de Azure.

## Pruebas

Mediante **Postman**, preparamos una request GET contra la URL de la app de demostración que previamente debemos haber desplegado. Tal que así:

```bash
curl -X GET \
  http://high-cpu-demo-swords.azurewebsites.net/api/values/1000 \
  -H 'cache-control: no-cache' \
  -H 'postman-token: fe8b2d33-1363-0146-3d58-53140b869ffe'
```

Sustituye el valor 1000 por uno más alto (x100) para que la API de ejemplo empiece a tardar más de la cuenta.

Tras invocar la llamada, el resultado será que, en algún momento, se generará una alerta en la aplicación...

![Alerta por exceso de CPU](/assets/azure-website-alert-activated.jpg)

... que provocará una ejecución de la Azure Function encargada de reiniciar el servicio.

![Histórico de ejecuciones de Azure Function](/assets/azure-function-executed-log.jpg)

Todo esto quedará registrado en el registro de actividad de Azure.

![Log de eventos de Azure tras ejecución](/assets/azure-function-activity-log.jpg)

## Conclusión

Todo este montaje permite disponer de un sistema reactivo a alertas de rendimiento que reinicia el servicio que generar dicha alerta bajo ciertas condiciones.

Voilá!