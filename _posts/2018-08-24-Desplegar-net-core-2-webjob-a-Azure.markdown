---
layout: post
title:  "VSTS | Desplegar un webjob continuo en .NET Core 2.x a Azure"
date:   2018-07-12 18:00:00
categories: azure vsts webjob netcore receta recipe build pipeline
---
# >_

## Escenario

Imagina que quieres crear una aplicación de consola que se ejecuta de forma continua para desencolar eventos desde, por ejemplo, un Azure Service Bus.

Esta aplicación de consola la quieres crear en **.NET Core 2.x**, y la quieres ejecutar como un *Azure Webjob* dentro de un *Azure WebApp Service*.

Si alguna vez te has peleado con ello, sabes que compilar y desplegar un webjob escrito en .NET Framework es coser y cantar con VSTS. Hacerlo en .NET Core, no es tan inmediato, pero ha dejado de ser una tarea complicada que requiera acciones manuales o usar la opción de visual studio de Publish as webjob.

## Azure - Estructura de un webjob continuo

Antes de llegar a "la receta", es importante entender cómo es la estructura de directorios de un webjob en Azure. Es sencilla, pero si no se respeta, el artefacto no será reconocido y Azure no será capaz de gestionar el tiempo de vida de la aplicación.

La estructura de directorio para un webjob continuo en Azure es esta.

> app_data/jobs/continuous/_%nombre%_

Dentro de dicha carpeta, se deben ubicar todos los ficheros que sean necesarios para ejecutar tu aplicación de consola.

## VSTS - Build pipeline

### ... en .NET Framework

El pipeline en .NET Framework está formado por los siguientes steps básicos.

1. Nuget Restore
1. Visual Studio Build
1. Publish Build Artifact

#### TASK: Nuget Restore

Nada especial aquí. Es una tarea común a casi cualquier tipo de proyecto .NET Framework.

| Propiedad | Valor |
|:-|:-|
| Path to solution, packages.config or project.json | *<La ruta al fichero .sln donde se encuentra tu webjob>* |

#### TASK: Visual Studio Build

Para el paso de compilación son importantes los argumentos que le pasamos a MSBuild. Si repasas el valor correspondiente, verás que tienen sentido por si mismos.

| Propiedad | Valor |
|:-|:-|
| Solution | *<La ruta al fichero .sln donde se encuentra tu webjob>* |
| Visual Studio Version | **Visual Studio 2017** |
| MSBuild Arguments | **/p:DeployOnBuild=true /p:WebPublishMethod=Package /p:PackageAsSingleFile=true /p:SkipInvalidConfigurations=true /p:PackageLocation="$(build.artifactstagingdirectory)\\"** |
| Platform | **$(BuildPlatform)** |
| Configuration | **$(BuildConfiguration)** |

#### TASK: Publish Build Artifact

No requiere comentarios.

| Propiedad | Valor |
|:-|:-|
| Path to publish | **$(build.artifactstagingdirectory)** |

### ... en .NET Core

El pipeline en .NET core es un poco más complejo que el de .NET Framework

1. dotnet core build
1. dotnet core publish
1. Archive task
1. Command line

#### TASK: dotnet core build

Importante aquí definir la ruta de salida de tus binarios para que sigan la estructura de directorios del webjob (propiedad **Arguments**).

| Propiedad | Valor |
|:-|:-|
| Command | **build** |
| Path to project(s) | *<La ruta al fichero .csproj de tu webjob>* |
| Arguments | **-o "$(build.binariesDirectory)/app_data/jobs/continuous/_<nombre_de_tu_job>_"** |

#### TASK: dotnet core publish

El punto principal de esta task es precisamente los argumentos que le pasamos al comando dotnet publish. Es imprescindible que incluyas **--self-contained** para que el proceso de publicación te genere un .exe en vez de una dll, y por concatenación de parámetros, se exige que indiques también el runtime para el que quieres generar el ejecutable.

Los valores de runtime disponibles para esta opción los puedes consultar en la [documentación sobre identificadores de entorno de ejecución en .NET Core](https://docs.microsoft.com/es-es/dotnet/core/rid-catalog) de Microsoft.

| Propiedad | Valor |
|:-|:-|
| Command | **publish** |
| Publish web projects | **false** |
| Path to project(s) | *<La ruta al fichero .csproj de tu webjob>* |
| Arguments | **-o "$(build.artifactstagingdirectory)/app_data/jobs/continuous/_<nombre_de_tu_job>_" --self-contained --runtime win10-x64** |
| Zip published projects | **false** |
| Add project name to publish path | **false** |

#### TASK: Archive staging directory

Como hemos desactivado el zip en el step anterior, lo debemos hacer a mano.

| Propiedad | Valor |
|:-|:-|
| Root folder or file to archive | **$(Build.ArtifactStagingDirectory)**|
| Archive file to create | **$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip**|

#### TASK: Command Line: rmdir app_data /S /Q

El objetivo de este paso es limpiar el artefacto resultante, para dejar única y exclusivamente el zip generado en el step anterior.

| Propiedad | Valor |
|:-|:-|
|Script|**rmdir app_data /S /Q**|
|Working Directory| **$(Build.ArtifactStagingDirectory)**|

## Conclusión

Mediante estas dos recetas podéis compilar vuestras aplicaciones de consola (para desplegarlas posteriormente) mediante VSTS de una forma sencilla y rápida. ¡Di no al "Botón derecho > Publicar"!