---
layout: post
title:  "VSTS Agents"
date:   2017-09-19 10:00:00
categories: VSTS CI
---
# >_

## Escenario

Si utilizas VSTS para realizar tu CI/CD, puede que en algún momento tengas la necesidad de tener más de un agente de ejecución de definiciones de build o de release.

Si eres un desarrollador con pocos recursos, o tienes un equipo que puedas o quieras utilizar para hacer la función de agente de VSTS, te interesa saber que puedes compartir un ordenador on-premises en un servidor preparado para ejecutar definiciones de VSTS.

## VSTS Agents

Un agente en VSTS es el componente principal de compilación y distribución en VSTS.

Desde un punto de vista genérico, es una máquina, virtual o no, que es la encargada de ejecutar todas las tareas definidas en una build definition o en una release definition.

#### Hosted agents

Por defecto, VSTS tiene tres tipos de agentes hosteados:

- Hosted
- Hosted Linux Preview
- Hosted VS2017

Cada agente tiene sus capacidades específicas, que determina el tipo de tareas que cada agente puede llevar a cabo. Por ejemplo, la diferencia entre Hosted y Hosted VS2017 es básicamente lo que su nombre indica. En uno, existe una "instalación" de Visual Studio 2017, que permite ejecutar soluciones o proyectos creados con este IDE.

En mi caso, suelo usar los Hosted VS2017 para ejecutar compilaciones de proyectos en .NET Core, mientras que los Hosted ejecuto los proyectos de full framework (.NET Framework 4.5 o superior).

La principal ventaja que veo yo a este tipo de agentes, es que cada build o release es limpia: El entorno en el que se ejecuta se crea y se destruye en el momento de iniciar la ejecución.

#### Agent Queues

No obstante, a veces no es suficiente con los agentes hosteados.

Cada agente es capaz de hacer una sola tarea a la vez, así que, si la situación lo permite, VSTS nos ofrece por defecto poder ejecutar hasta tres definiciones a la vez.

Esta limitación es salvable contratando nuevos agentes hosteados, pero si quieres ahorrarte ese gasto y tienes hierro disponible para hacer las veces de agente, sigue leyendo.

La buena noticia es que podemos añadir máquinas al pool Default, para agilizar las ejecuciones. Por defecto, VSTS crea este Agent Queue Pool, aunque puedes crearte tu propio pool y darle el nombre que te convenga para identificar qué pueden ejecutar los agentes asignados a ese pool.

## Instalación y configuración

Para configurar un agente on-premises, debes acceder a tu suscripción de VSTS y seleccionar el icono del engranaje, navegando posteriormente a la opción Agent Pools.

![vsts-agent-pools](/assets/vsts-agent-pools.jpg)

Una vez seleccionada la opción, se nos mostrará la ventana de configuración de Agent Pools.

Haz clic en la opción Download Agent, y seleccionad el sistema operativo de vuestro equipo (o del equipo que hará las veces de agente).

![vsts-agent-host-selection](/assets/vsts-agent-host-os.jpg)

Presta atención a las instrucciones de instalación, porque contienen las instrucciones que debemos ejecutar para instalar el software del agente.

Pero vamos por partes.

Descarga primero el agente, que básicamente es el contenido de un archivo zip. No lo descomprimas a mano.

Prepara la carpeta donde quieres que esté instalado el agente. En las instrucciones de VSTS te indican el path C:\agent, pero no es necesario que uses dicha ruta.

En mi equipo, por ejemplo, lo he instalado en c:\vsts\agents.

Abre una consola de powershell y escribe algo parecido a esto:

```powershell
Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$HOME\Downloads\vsts-agent-win7-x64-2.122.1.zip", "$PWD")
```

> Tip: Ten en cuenta que esta instrucción está descrita en las instrucciones de instalación de VSTS. Copia y pega esa instrucción en vez de la que te indico, que será tu mejor opción.

Una vez ejecutada la instrucción, se te habrá creado una estructura en la carpeta seleccionada que contendrá los ficheros ejecutables del agente.

Uno de los ficheros que contendrá la carpeta es el fichero de configuración. Ejecútalo.

```powershell
.\config.cmd
```

El proceso de configuración te pedirá los siguientes datos:

- Server URL: [https://{your-account}.visualstudio.com](https://{your-account}.visualstudio.com)
- Authentication type: **PAT** (o *Personal Access Token*)
- Personal Access Token: [El token personal que puedes conseguir siguiendo estas instrucciones](https://docs.microsoft.com/es-es/vsts/build-release/actions/agents/v2-windows).
- Agent Pool: Default, por defecto. ;)
- Agent Name: Lo que quieras, aunque si es un nombre descriptivo, mejor. Por ejemplo, **MiAgenteMolon**.
- Work folder: Enter (el valor por defecto están bien, a menos que quieras personalizar el nombre de la carpeta de trabajo)
- Run as a Service: Depende. Si lo quieres activo siempre, o es algo definitivo, igual te interesa que el agente arranque como servicio. En mi caso, no.
- Configure Autologon: No, porque en mi caso no va a ser un servicio.

¡Listos!

Para arrancar el agente, simplemente ejecutamos...

```powershell
.\run.cmd
```

... y nuestro agente estará preparado para recibir trabajos de compilación y despliegue.

Si quieres verificar que todo el proceso se ha llevado a cabo correctamente, vuelve a la página del gestión de Agentes y comprueba que tu agente está registrado y online.

![VSTS-Local-Agent-Done](/assets/vsts-local-agent-done.jpg)