---
layout: post
title:  "Azure DevOps | Build definition para .NET Core 2.2"
date:   2018-01-02 16:00:00
categories: AzureDevOps receta recipe build release pipeline net core 2.2
---
# >_

## Escenario

En la última release de [Microsoft.Extensions.Logging.Log4Net.AspNetCore](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore), una de las modificaciones incluídas es la de hacer completamente compatible con **.NET Core 2.2** el paquete publicado en [nuget.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Log4Net.AspNetCore/).

El problema es que, en el build pipeline que tengo definido en Azure DevOps para hacer el compilado y despliegue del nuget, utilizo un Hosted Agent. Y, hasta que Microsoft [no complete la inclusión de la versión 2.2.101](https://github.com/Microsoft/azure-pipelines-image-generation/issues/512) (la actual mientras escribo este post) en sus imágenes de Hosted Agents, la versión del SDK necesaria para completar una build de un proyecto de versión .NET Core 2.2 debe ser "instalada" a "mano".

## Build steps

Los **build steps** definidos en mi **build pipeline** del paquete nuget en Azure DevOps son:

* bash script (para copiar el certificado de [firma del paquete](https://docs.microsoft.com/es-es/nuget/create-packages/sign-a-package))
* dotnet build
* dotnet test
* dotnet pack
* Publish artifact

Con estos pasos, la versión máxima de .NET Core soportada es la 2.1.

## Añadir un SDK no instalado

Para instalar en tu agente una versión del SDK de .NET Core no disponible o experimental, debes incluir un step antes del paso *dotnet build*, de manera que cuando el compilador intente construir tu proyecto sea capaz de encontrar la versión del CLI correcta.

El step debe ser de tipo **.NET Core SDK installer**, y debes configurar la versión adecuada en el campo **Version**.

![Screenshot del step de instalación del SDK de .NET Core](/assets/net-core-sdk-installer-screenshot.jpg)

Si queréis saber cuál es la versión apropiada (en mi caso la última estable de .NET Core), id a la página de descarga de [.NET Core](https://dotnet.microsoft.com/download) y revisad el apartado **Other downloads** y escoged el número del último SDK estable.

![Screenshot de la página de descarga de .NET Core SDK](/assets/net-core-download-page-screenshot.jpg)

## Conclusión

De este modo, no importa si el **Hosted Agent** que utilicéis tiene o no la versión X del SDK de .NET Core. Si no la tiene, la instalará.

> **Disclaimer**: No he probado a instalar una versión del SDK ya instalada en el agente...
>
> Aunque imagino que ese escenario estará cubierto por el step definition, ya que lo ha desarrollado Microsoft directamente.