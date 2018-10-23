---
layout: post
title:  "Azure DevOps | Ejecutar tests funcionales con Selenium y Docker (3/3)"
date:   2018-10-22 18:02:00
categories: AzureDevOps receta recipe build release pipeline functional tests docker selenium
---
# >_

## Escenario

Si no has leído la [parte 2](Ejecutar-tests-selenium-en-Azure-DevOps-mediante-docker-2.html), y quieres saber cómo definir y levantar tu Selenium Grid Hub en Docker, te recomiendo que la leas antes de continuar. Si por contra quieres ir al meollo de añadir todo esto a Azure DevOps... sigue leyendo. ;)

## Selenium en Docker para Azure DevOps

Así como ejecutar el docker-compose y acceder a vuestro Selenium Grid Hub en local es coser y cantar, porque la ruta de acceso es **localhost:xxxx**, en el contexto de **un Host agent de Azure DevOps el acceso a través de localhost no está disponible**.

Es por tanto necesario saber qué IP de entrada tiene vuestro contenedor de Selenium Grid Hub en el agente para poder actualizar la ruta de vuestro test y poder así hacer peticiones a ese endpoint.

Y para eso, no queda otra que recuperarla mediante `docker inspect`. Usando esa instrucción de Docker, es posible recuperar la dirección IP pública de tu contenedor, de manera que, si se la asignamos como valor a una variable de entorno (en el ejemplo, **SeleniumDockerHost:Host**), podemos utilizarla en nuestra ejecución de tests funcionales.

Para ello, podéis utilizar el siguiente snippet bash. Por ejemplo, creando un fichero llamado `get-ip`, que después utilizaréis en vuestras definiciones de Azure DevOps.

```bash
#!/bin/bash

IP=`docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' selenium-hub`
echo "##vso[task.setvariable variable=SeleniumDockerHost:Host;]$IP"
```

Sabiendo esto, ya tenemos todas las herramientas para definir nuestro CI / CD para incluir la ejecución de nuestros tests funcionales.

A continuación, las recetas...

## Build Definition

Esta **build definition** de ejemplo es muy sencilla. Únicamente compila la aplicación y publica ciertos elementos de nuestro repositorio para su posterior uso en la fase release.

    Disclaimer:

    El decidir que los tests funcionales se ejecutan en release es algo que debéis valorar en cada caso. No es un estándar de facto, así que investigad un poco antes de "tirar pa'lante".

### Build : Agent Job Steps

Los pasos de ejecución de esta definición son:

* Compilar el proyecto mediante un dotnet build.
* Publicar la carpeta de código fuente del proyecto.
* Publicar el fichero docker-compose.yml.
* Publicar el fichero get-ip.

Los tres pasos de publicación tienen como objetivo que la Release definition sea capaz de acceder a los elementos necesarios para ejecutar los tests.

![Screenshot de la pantalla de Build definition de Azure DevOps](/assets/selenium-docker-build-definition.jpg)

#### dotnet build

Este paso de ejecución es de tipo **.NET Core**.

| Property | Value |
|-|-|-|
| Command | build |
| Path to project(s) | **/*.csproj |

#### Publish Sources

Este paso de ejecución es de tipo **Publish Build Artifacts**.

| Property | Value |
|-|-|-|
| Path to publish | $(Build.SourcesDirectory) |
| Artifact name | drop |

#### Publish docker-compose.yml

Este paso de ejecución es de tipo **Publish Build Artifacts**.

| Property | Value |
|-|-|-|
| Path to publish | ruta/a/tu/fichero/docker-compose.yml |
| Artifact name | docker-compose.yml |

#### Publish get-ip

Este paso de ejecución es de tipo **Publish Build Artifacts**.

| Property | Value |
|-|-|-|
| Path to publish | ruta/a/tu/fichero/get-ip |
| Artifact name | get-ip |

## Release Definition

En este caso, la **release definition** de ejemplo contiene los pasos para ejecutar los tests funcionales.

La elección de utilizar esta estructura se basa en pensar en un escenario que implemente un esquema de [despliegue blue-green](https://martinfowler.com/bliki/BlueGreenDeployment.html). Así, en tiempo de despliegue, podemos validar que nuestra aplicación se ha desplegado correctamente y que todos los test funcionales se ejecutan correctamente, antes de hacer el swap entre las dos instancias de nuestra aplicación.

Otra cosa que debes tener en cuenta... Crea un Stage distinto para cada uno de los navegadores y sistemas operativos que quieras probar. En mi caso, he creado dos stages, que difieren únicamente en el valor de una variable de entorno llamada `SeleniumDockerHost:BrowserName`.

![Screenshot de la pantalla de Release definition variables de Azure DevOps](/assets/selenium-docker-release-definition-variables.jpg)

### Release : Agent Job Steps

Los pasos de ejecución de esta definición son:

* Levantar el contenedor de Selenium Grid Hub con docker-compose up en modo background.
* Ejecutar el script bash para recuperar la IP del contenedor.
* Ejecutar los tests mediante dotnet test --filter.
* Parar el contenedor de Selenium Grid Hub con docker-compose up.

![Screenshot de la pantalla de Release definition de Azure DevOps](/assets/selenium-docker-release-definition.jpg)

#### Run on Agent

**ATENCIÓN**: Es importante seleccionar el agente Hosted Ubuntu 1604 (a fecha de publicación de este post) ya que el Hosted Linux preview no permite acceder a la IP de entrada al contenedor que levantaremos con docker-compose en el agente.

| Property | Value |
|-|-|-|
| Agent pool | Hosted Ubuntu 1604 |

#### Run docker-compose up in background

Este paso de ejecución es de tipo **Docker Compose**.

| Property | Value |
|-|-|-|
| Docker Registry Service Connection | Docker Hub |
| Docker Compose File | ruta/a/tu/artefacto/docker-compose.yml/docker-compose.yml|
| Project Name | $(Build.Repository.Name) |
| Action | Run Services Images |
| Run in Background | Checked |

#### Bash script

Este paso de ejecución es de tipo **Bash**.

| Property | Value |
|-|-|-|
| Type | File Path|
| Script Path | ruta/a/tu/artefacto/get-ip/get-ip|

#### dotnet test

Este paso de ejecución es de tipo **.NET Core**.

| Property | Value |
|-|-|
| Command | test |
| Path to project(s) | **/*.csproj |
| Arguments | --filter *your_filter* |
| Publish test results and code coverage | checked |

Para más información de cómo utilizar el modificador **--filter**, podéis consultar la [documentación oficial de Microsoft sobre dotnet test](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-test?tabs=netcore21).

#### Run docker-compose down

Este paso de ejecución es de tipo **Docker Compose**.

| Property | Value |
|-|-|-|
| Docker Registry Service Connection | Docker Hub |
| Docker Compose File | ruta/a/tu/artefacto/docker-compose.yml/docker-compose.yml|
| Project Name | $(Build.Repository.Name) |
| Action | Run a Docker Compose command |
| Command | down |

## Conclusión

Tenemos la posibilidad de ejecutar tests funcionales, desde C#, sin necesidad de una infraestructura demasiado compleja, e incluir esa ejecución de forma automatizada en nuestros pipeline de CI / CD.

¿Qué excusa vas a poner ahora para no incluirlos en tus proyectos?