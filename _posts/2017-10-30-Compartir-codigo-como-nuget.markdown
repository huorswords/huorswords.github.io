---
layout: post
title:  "Compartir código como NuGet"
date:   2017-10-30 18:00:00
categories: .net NuGet opensource github
---
# >_

## Escenario

Hace unos meses, y a raíz de un proyecto en el que estoy trabajando, surgió la necesidad de utilizar [Log4Net](https://logging.apache.org/log4net/) desde un proyecto ASP.NET Core.

Así que buscando información al respecto, encontré un artículo de __Anuraj P__ <small>| <a href="https://twitter.com/anuraj"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@anuraj__</span></span></a></small> en el que contaba [cómo crear y configurar un Logger para Log4Net en ASP.NET Core](https://dotnetthoughts.net/how-to-use-log4net-with-aspnetcore-for-logging/).

Implementé (con alguna pequeña modificación) la solución que planteaba en el proyecto, y se me ocurrió que como a mi, igual había más desarrolladores que se pudiesen encontrar en esa misma situación:

> *Usar Log4net sin tener alguna librería que les permitiera hacerlo sin preocuparse de los detalles de implementación.*

Y por eso, decidí crear un NuGet package para resolver esta carencia.

## Crear un NuGet

Para crear un NuGet package no demasiado complejo en .NET Core que contenga una librería simple (con o sin dependencias) no hace falta configrar mucha cosa.

De hecho, desde **Visual Studio 2017** existe una opción que de un modo más o menos automático te permite generar un **NuGet package** y configurar los campos de metadatos que contendrá ese paquete.

Para ello, debes acceder, una vez creado el proyecto, a las propiedades del mismo y seleccionar la pestaña **Package**, marcando posteriormente la opción **Generate NuGet package on build**.

![NuGet-Core-Configuration](/assets/NuGet-Core-Configuration.jpg)

Al compilar tu proyecto, se generarán las librerías correspondientes y, inmediatamente después, el nuget package que las contiene para que cualquiera pueda consumirlas.

Ten en cuenta el detalle de compilar tus NuGet packages en Release antes de subirlos. Parece una tontería, pero... ;)

Por otro lado, si no tienes Visual Studio, o tienes que hacer un NuGet algo más complejo, no lo dudes y sigue el [tutorial que NuGet.org publica en su página web para crear NuGet packages](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package).

## Publicar un NuGet

Una vez generado el paquete NuGet, subirlo a [NuGet.org](https://www.nuget.org) es igual de sencillo.

[Accede a https://www.NuGet.org/packages/manage/upload](https://www.NuGet.org/packages/manage/upload) (requiere registro previo), selecciona **Browse...**, navega hasta donde hayas generado tu package (normalmente en la carpeta de binaries de tu proyecto, una vez compilado) y acepta la subida.

![NuGet-Upload](/assets/nuget-upload.jpg)

Al cabo de unos minutos, el paquete estará disponible para su descarga.

## Después de publicar

Lo más impresionante (para mi) de todo esto es que hace 4 meses que publiqué la primera versión del NuGet, hace un mes que publiqué una segunda versión y, para mi sorpresa, acumula más de **mil descargas** desde entonces, a 9 descargas diarias de media...

> **¡Hay gente que usa mi NuGet!**

Tengo que reconocer que mi ego se alimenta un poco de este mini-éxito. Me hace sentir orgulloso el pensar que hay alguien al que estoy ayudando de una forma indirecta.

## Publicar el código fuente a Github

La publicación del código fuente en algún repositorio accesible a cualquier desarrollador iba implícito en mi idea inicial: compartir.

Publicar un NuGet sin compartir tu código en paralelo sólo tiene sentido si:

* Tienes un acuerdo de confidencialidad o de cesión de derechos
* Eres muy proteccionista con tu código

En mi caso no aplicaban ninguno de los dos puntos, porque si bien el código que desarrollo para mi empresa es de su propiedad, en este caso no era código para mi empresa. Es código público que está en un blog post público al que todo el mundo tiene acceso. Del que yo me he aprovechado.

Así que, publiqué el [código fuente](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore) del NuGet en Github.

Y, al contrario de lo que pensaba, hay respuesta por parte de la comunidad Open Source.

Hace pocos días registraron la primera issue en el repositorio, de alguien a quien le había parecido interesante el proyecto, que había decidido utilizarlo y que además se había molestado en revisar el código para certificar que existía un bug (thank you very much @georgechr).

Por mi parte, he intentado (e intentaré) dar respuesta lo más rápido posible a cualquier feedback que reciba para este proyecto, porque creo que la persona que se molesta en abrir una issue y usar parte de su tiempo para reportarte un error, una observación o cualquier información que pueda hacer que mejore el código y de rebote me haga aprender algo (es egoísmo, lo sé), merece la pena el esfuerzo.

Así que, si alguno de vosotros utilizáis en algún momento algo de este proyecto, y tenéis algo que decir...

>**Any feedback is welcome!**
