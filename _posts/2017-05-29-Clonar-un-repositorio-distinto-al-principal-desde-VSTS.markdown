---
layout: post
title:  "Clonar un repositorio distinto al principal desde VSTS"
date:   2017-05-29 13:00:00
categories: VSTS CI
---
# >_

## Escenario

Cuando usamos CI desde *VSTS*, puede darse el caso de que sea necesario clonar un repositorio GIT distinto al principal como step previo a la compilación de nuestro proyecto. Si además éste repositorio está hosteado en el mismo servidor de VSTS desde donde lanzamos la *build*, sigue leyendo porque esto quizá te interesa.

## VSTS: Command Line Task con 'git clone'

Para configurar un step de clonado de un repo git "externo", simplemente tenéis que editar la build definition correspondiente e ir a la opción *'Add Task'*.

Del listado de tareas disponibles, seleccionad la pestaña *'Utility'* y, de la lista de *tasks*, ***Command line***

Una vez añadida, el campo *Tool* debe tener el valor **git**, y en el campo *Arguments*, debe indicarse el valor **clone https://%SYSTEM_ACCESSTOKEN%@[url-al-repo-git]**.

De ese modo, la instrucción que ejecutará la tarea definida será. 

> git clone https://%SYSTEM_ACCESSTOKEN%@[url-al-repo-git]

Si quieres que se clone una rama concreta distinta a la por defecto (normalmente master), añade al valor de *Arguments* el modificador **-b [nombre-de-rama]**, donde [nombre-de-rama] puede ser, por ejemplo, **develop**.

Además, y dependiendo de la profundidad del repositorio externo en tu directorio de trabajo local, deberás establecer también el *Working folder* con el siguiente valor: **$(Agent.BuildDirectory)** o la ruta apropiada según tu estructura de referencias (siempre es mala idea seguir esta solución).

Si necesitas información sobre cualquier variable predefinida, tienes toda la [información sobre las variables de compilado](https://www.visualstudio.com/en-us/docs/build/define/variables) en la web de documentación de Visual Studio.


## VSTS y el token OAuth

Si te has fijado en la instrucción de clonado del apartado anterior, habrás reparado en la variable ***%SYSTEM_ACCESSTOKEN%*** que precede la URL del repositorio git externo.

Esta variable se corresponde con el token de acceso que VSTS contiene, que le permite acceder a los repositorios que tengas configurados en la instancia de VSTS.

Aunque pueda parecer que indicando esta variable en la ruta de clonado ya es suficiente para que sustituya ese valor de variable por el token correspondiente, no es así. Hay que explicitar que la *build definition* tiene acceso a dicho token OAuth.

Para habilitarlo, debes acceder, de nuevo desde la edición de *Build Definition*, a la pestaña *Options* y marcar como **Enabled** la opción **Allow scripts to access OAuth token**.

De esa manera, y tras guardar los cambios, el repositorio git "externo" será accesible durante la ejecución de la build, y podrá ser clonado.