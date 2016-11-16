---
layout: post
title:  "SourceTree y Git-Flow"
date:   2016-11-16 13:00:00
categories: source-control git
---

Si eres de los que...

- ... utiliza Git como sistema de control de código fuente.
- ... sigues el modelo de branching [GitFlow](http://nvie.com/posts/a-successful-git-branching-model/).
- ... usas como cliente GUI de Git la aplicación [SourceTree](https://www.sourcetreeapp.com/).

... igual te interesa esto.

## GitFlow
Ya debes saber que el modelo GitFlow define las siguientes ramas:

- **master**: Es la rama estable del proyecto, que está desplegado en producción.
- **develop**: Destinada a contener la siguiente versión en producción, esta rama debería ser también bastante estable, aunque puede llegar a romperse.
- **feature**: Cada funcionalidad o bugfix se corresponde con una rama *feature*, en la que se realizan todos los cambios necesarios para implementar dicha funcionalidad. Se sincroniza siempre con *develop*, pero no con *master*.
- **hotfix**: Cuando un error se detecta en producción, y dicho error no puede esperar a ser corregido hasta la siguiente *release* del proyecto, se crea una rama hotfix, que al finalizar se sincroniza con *master* y con *develop*.
- **release**: Cuando el ciclo o sprint de desarrollo finaliza y se debe pasar a producción el código disponible en *develop*, se crea una rama *release* que es la que QA debe utilizar para testear la aplicación antes de ser "liberada".

Esta es la idea, a grandes rasgos, de como se debe trabajar aplicando el modelo GitFlow original.

## GitFlow AVH Edition
No obstante, existe un modelo [GitFlow extendido](https://github.com/petervanderdoes/gitflow-avh), que entre otras cosas permite diferenciar una feature de un bugfix, asignándole una branch distinta en función del tipo de modificación que se trate.

No confundas bugfix con hotfix.

Así como el hotfix es un error que se detecta en producción (*master*) y que debe ser corregido antes de la siguiente *release*, un bugfix es aquel error de una funcionalidad ya finalizada pero que únicamente se encuentra en *develop*. En el modelo original se trataría de una feature adicional, pero en el modelo extendido semánticamente se denomina bugfix.

El utilizar el modelo de GitFlow extendido es más por semántica que por otra cosa, en mi opinión, pero hay algunos proyectos que siguen este modelo y conviene conocerlo.

## SourceTree y el modelo extendido
Por algún motivo hay desarrolladores que no se sienten cómodos con la línea de comandos de git, y que por ello utilizan SourceTree.

Usuarios de SourceTree: debéis saber que el plugin de GitFlow implementa el modelo original, no el modelo extendido.

![Git Flow Init SourceTree](/assets/GitFlowInitSourceTree.jpg)

Por eso, si el proyecto en el que estás trabajando sigue el modelo extendido, no te quedará más remedio que ir a línea de comandos para crear, por ejemplo, un bugfix.

## Abrir un bugfix

Si has inicializado gitflow tradicional desde SourceTree pero quieres pasarte al modelo extendido para poder usarlo desde el bash de Git, te aparecerá algo similar a esto:

{% highlight batch %}
> git flow bugfix start
Fatal: Bugfix prefix not set. Please run 'git flow init'.

{% endhighlight %}

No problem! Ejecuta esto...

{% highlight batch %}
> git flow init -d -f
Using default branch names.

Which branch should be used for bringing forth production releases?
   - develop
   - master
Branch name for production releases: [master]

Which branch should be used for integration of the "next release"?
   - develop
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Bugfix branches? [bugfix/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
Hooks and filters directory? [C:/github/test/.git/hooks]

{% endhighlight %}

... y de esta manera ya podrás utilizar el modelo extendido en tu día a día.