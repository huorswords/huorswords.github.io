---
layout: post
title:  "Git | Cómo mover los commits locales de develop a otra rama"
date:   2018-07-12 18:00:00
categories: source-control git
---
# >_

## Escenario

Imagina que tu día a día se rige por el branching strategy de [Git-flow](https://nvie.com/posts/a-successful-git-branching-model/).

Llegas un día muy dormido e inicias una tarea nueva directamente en el código, sin cambiar de rama a *feature/una_nueva_funcionalidad*.

Haces un commit directo, sin querer, a tu branch develop local. Pero no es lo que querías. ¿Qué haces?

1. Te cargas lo que has hecho, abres la rama y vuelves a empezar.
1. Clonas el repo en otra parte, abres la rama y copias manualmente los cambios del repo original.
1. Renombras develop y...

## Rename de la branch

Aprovechando de que el histórico de tu branch develop local no ha sido modificado, ya que es tu primer o segundo commit sobre esa rama, puedes optar por hacer un renombrado de la rama para que sea tu nueva rama *feature/una_nueva_funcionalidad*.

Para hacerlo:

```bash
git branch -m feature/una_nueva_funcionalidad
```

Con esto hemos hecho "desaparecer" la rama develop, creando una nueva rama *feature/una_nueva_funcionalidad*.

Pero... en esa nueva rama, el remote branch asociado sigue siendo develop. ¡No hagas push todavía!

A partir de aquí, dos opciones.

### Opción A

#### Unset del remote branch de la rama local

Si has elegido la opción A, tu primer paso es eliminar la referencia a origin/develop de tu nueva rama feature/una_nueva_funcionalid.

Para ello, debes ejecutar el siguiente script.

```bash
git checkout feature/una_nueva_funcionalidad
git branch --unset-upstream
```

Este primer paso te habrá eliminado los valores de **branch.source.origin** y de **branch.source.merge**, que son los que le dicen a git a dónde debe subir los cambios al recibir un comando push, o desde donde bajarlos al recibir un comando pull.

Llegados a este punto, si ejecutas...

```bash
git push
```

... te saldrá el típico mensaje que aparece cuando creas una rama nueva en local que todavía no tiene su correspondiente en el remote.

¡Siguiente paso!

#### Push de los cambios

El paso razonable en este momento es generar la rama local en el remoto (o seguir trabajando en ella hasta terminar la funcionalidad), por si algún compañerx necesita acceder o modificar el código disponible en esta rama.

Para subir la rama al remoto, sólo hace falta ejecutar:

```bash
git push --set-upstream origin feature/una_nueva_funcionalidad
```

### Opción B

#### Set del remote branch de la rama local

Esta opción es más rápida, pero personalmente me siento más cómodo con la opción A. En realidad no tengo una opinión sólida formada al respecto, así que explico también esta opción.

Esta opción combina en un sólo paso el proceso de eliminar la referencia remota a develop y sustituirla por la nueva rama.

Para hacerlo, debes escribir esto.

```bash
git branch --set-upstream-to=origin/feature/una_nueva_funcionalidad
```

Una vez cambiada esta referencia, ya puedes hacer `push` de los cambios.

## Checkout del develop remoto en local

Lo único que falta es restaurar nuestra rama develop local. Sencillo:

```git
git checkout develop
```

Al lanzarlo, como pasaría con cualquier rama que no tengamos en nuestro local (porque no la tenemos, la hemos "renombrado"), git se conectará a nuestro origin y moverá el HEAD a la posición donde origin/develop se encuentre.

## Conclusión

He aquí un par de maneras (ignorando las acciones manuales que, si bien son lícitas, son menos limpias bajo mi punto de vista) de "mover" un commit o commits erróneos en develop a una rama aparte, sin que esto implique perder la referencia con la versión de la rama develop en remoto.

