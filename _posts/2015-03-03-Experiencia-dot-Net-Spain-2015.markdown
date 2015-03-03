---
layout: post
title:  "Experiencia en la dotNet Spain Conference 2015"
date:   2015-03-03 13:00:00
categories: conferencia comunidad .net
---
El pasado 27 y 28 de febrero tuve la gran suerte de asistir al evento nacional .NET más grande al que había podido asistir hasta ahora: la [___dotNet Spain Conference 2015___][dotNetSpain2015Url].

![dotNetConferenceSpain2015Pass](/assets/dotNetSpain2015-Pass.jpg)

Este evento, celebrado en la Escuela Politécnica superior de la [Universidad de Alcalá](http://www.uah.es/), estuvo coordinado por el equipo de __Microsoft Developer and Platform Evangelism__ <small>| <a href="https://twitter.com/msdev_es"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@msdev_es__</span></span></a></small>, y contó con la presencia de multitud ponentes expertos que impartieron charlas y laboratorios (a mi parecer, muy interesantes) sobre temas como Desarrollo Web, _Cloud_, Data, Lenguajes, _Mobile Apps_, IoT (_Internet Of Things_)...

En mi experiencia personal, pude disfrutar de algunas charlas magistrales de Cloud, Desarrollo Web y Lenguajes, además de un _Hands On Lab_ de __Leo Antoli__ <small>| <a href="https://twitter.com/lantoli"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@lantoli__</span></span></a></small> en el que pude constatar que, además de no conocer gran parte de la tecnología que hoy en día ofrece Microsoft a través de .NET Framework y de sus servicios de _Cloud_, tampoco conozco cómo funciona de forma intrínseca el lenguage __C#__ ni el __CLR__ de .NET.

____

## Primer día

El viernes 27 estuve en 3 charlas, además del mencionado laboratorio, y de la estupenda __Keynote__ que nos ofreció José Bonnin <small>| <a href="https://twitter.com/wasat"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@wasat__</span></span></a></small> en la que nos habló de la historia de .NET y del futuro que está por venir.

Las charlas a las que asistí fueron:

1. ___Cloud-First: Creando aplicaciones pensando en el cloud___, por Ibon Landa <small>| <a href="https://twitter.com/ibonilm"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@ibonilm__</span></span></a></small>
1. ___Tengo mi aplicación web en el cloud y no escala. ¿Qué hago?___, por Quique Martínez <small>| <a href="https://twitter.com/quiqu3"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@quiqu3__</span></span></a></small>
1. ___Cómo hago yo ASP.NET MVC___, por Luis Ruiz Pavón <small>| <a href="https://twitter.com/luisruizpavon"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@luisruizpavon__</span></span></a></small>

<br />

### Cloud-First: Creando aplicaciones pensando en el cloud
Ibon nos contó muchas cosas en su charla, pero principalmente me quedé con un concepto que me pareció indispensable y que él enfatizó mucho: **automatizar**.

> Si una operación la tienes que hacer más de dos veces... ¡automatizar, automatizar, automatizar!

<figure>    
    <figcaption style="text-align: right;">Ibon Landa &copy;</figcaption>
</figure>

Comparto plenamente su visión en este sentido, y no solamente en el mundo _Cloud_, sino en cualquiera de los mundos de la operación / administración de TI. Aquellas tareas que se repiten no deben quedar para la artesanía del técnico que la realiza, sino que debe ser una tarea controlada y testeada para evitar errores humanos que, aunque todos sabemos que existen, se dan más frecuentemente de lo que nos gustaría.

### Tengo mi aplicación web en el cloud y no escala. ¿Qué hago?
Quique, por su parte, nos hizo conscientes de las diferencias que existen entre el desarrollo tradicional _on-demand_ y el nuevo paradigma de desarrollo orientado a la nube.

Cosas como entender que no existe un sistema de ficheros propiamente dicho -- <small>y que tenemos que utilizar el servicio de Storage</small> --, o que las referencias en el cloud y en un equipo físico se deben gestionar de forma diferente -- <small><em>Azure Websites</em> o <em>SQL Azure</em></small> --.

Gran charla, aunque la ventana -- <small><em>maldita ventana</em></small> -- de la sala no dejase de hacer ruido, molestando a los asistentes y, en especial, al ponente.

### Cómo hago yo ASP.NET MVC
Esta charla de Luis, acerca de cómo él afronta los proyectos MVC en su día a día, me pareció súper entretenida.

En general todas las charlas de la <em>dotNet Spain Conference 2015</em> me parecieron muy entretenidas, pero algunas de ellas se me hicieron más amenas, quizá por la forma de transmitir la información de los ponentes.

> Pon a dieta a tus controladores: Evitar el FaleteControler

<figure>
    <figcaption style="text-align: right;">Luis Ruiz Pavón &copy;</figcaption>
</figure>

![faleteControlerByLuisRuizPavon](/assets/dotNetSpain2015-faleteController.jpg)

Durante la charla, Luis nos explicó que él prefería, por ejemplo, orientar la organización del proyecto de forma diferente a la estándar que propone el modelo MVC. Su esquema de aplicación se basa en __funcionalidades__ en vez de hacer la división de Modelo-Vista-Controlador. Es algo muy lógico, según lo veo yo, porque en aplicaciones grandes, el tener gran cantidad de controladores en una carpeta, igual o más vistas en otra y los modelos en otra puede llegar a ser algo poco práctico.

En realidad, me pasa algo así con un proyecto en C++ al que doy mantenimiento actualmente: la división de ficheros por _Source Files_ y _Header Files_ me parece de risa, más teniendo en cuenta que C++ ya está pensado para utilizar la programación orientada a objetos.

Esta sesión fue grabada, por ser una de las que se hicieron en el auditorio de la _Universidad de Alcalá_, por lo que en el momento en que Microsoft las publique, intentaré actualizar el post para que quede enlazada.

____

## Segundo día

El sábado estuve en 5 charlas en total.

1. ___Async Best Practices___, por Lluis Franco <small>| <a href="https://twitter.com/lluisfranco"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@lluisfranco__</span></span></a></small> y Alex Casquete <small>| <a href="https://twitter.com/acasquete"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@acasquete__</span></span></a></small>.

1. ___Code Smells___, por Fernando Escolar <small>| <a href="https://twitter.com/fernandoescolar"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@fernandoescolar__</span></span></a></small>.

1. ___Por qué deberías plantearte F# para tu próximo proyecto___, por Alex Casquete <small>| <a href="https://twitter.com/acasquete"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@acasquete__</span></span></a></small>.

1. ___Introducción práctica a Roslyn___, por Juan Manuel Servera <small>| <a href="https://twitter.com/jmservera"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@jmservera__</span></span></a></small>.

1. ___Effective C#___, por Leo Antoli <small>| <a href="https://twitter.com/lantoli"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@lantoli__</span></span></a></small>.

Gran conocimiento adquirido en todas ellas, que voy a intentar resumir.


### Async Best Practices
Aunque la charla la dieron Lluis y Alex, en realidad el peso de casi toda la charla recayó en Lluis o al menos a mi me dió esa sensación.

Plantearon un conjunto de buenas prácticas para crear código asíncrono, haciendo de inicio un recorrido por la historia de la asincronía en .NET, y mostrando de una manera muy visual cómo evitar una serie de problemas comunes conocidos en el desarrollo asíncrono.

Hubo, además, una máxima que me caló, más allá de implementaciones técnicas concretas:

> Sed honestos y no digáis que vuestra librería es asíncrona si no lo es.

<figure>    
    <figcaption style="text-align: right;">Lluis Franco &copy;</figcaption>
</figure>

Y, aunque yo soy más de Star Wars que de Star Trek, el homenaje a [Leonard Nimoy][Leonard_Nimoy] fue bastante emotivo. Lástima que no eché foto del momento.

### Code Smells
En esta charla Fernando trató de hacer un paso rápido por los considerados __Code Smells__ que Martin Fowler describe en su libro [___Refactoring - Improving the Design of Existing Code___][MartinFowlerRefactoringUrl] y que todo desarrollador debería, al menos, conocer.

Fernando consiguió que me volviesen a entrar ganas de adquirir este libro y leerlo de una vez, ya que si bien he leído algún fragmento en formato digital, creo que será interesante leerlo de cabo a rabo para entender -- <small>y de ese modo evitar</small> -- lo que se consideran malos olores en el código de las aplicaciones desarrolladas mediante OO.

### Por qué deberías plantearte F# para tu próximo proyecto
Creo que, de las charlas a las que asistí el sábado, esta fue la que menos me llegó.

Si bien ya había visto algo sobre F# -- <small>en mi mini-colaboración en <a href="https://github.com/ploeh/ZeroToNine"><span class="icon  icon--github"><svg viewBox="0 0 16 16"><path fill="#828282" d="M7.999,0.431c-4.285,0-7.76,3.474-7.76,7.761 c0,3.428,2.223,6.337,5.307,7.363c0.388,0.071,0.53-0.168,0.53-0.374c0-0.184-0.007-0.672-0.01-1.32 c-2.159,0.469-2.614-1.04-2.614-1.04c-0.353-0.896-0.862-1.135-0.862-1.135c-0.705-0.481,0.053-0.472,0.053-0.472 c0.779,0.055,1.189,0.8,1.189,0.8c0.692,1.186,1.816,0.843,2.258,0.645c0.071-0.502,0.271-0.843,0.493-1.037 C4.86,11.425,3.049,10.76,3.049,7.786c0-0.847,0.302-1.54,0.799-2.082C3.768,5.507,3.501,4.718,3.924,3.65 c0,0,0.652-0.209,2.134,0.796C6.677,4.273,7.34,4.187,8,4.184c0.659,0.003,1.323,0.089,1.943,0.261 c1.482-1.004,2.132-0.796,2.132-0.796c0.423,1.068,0.157,1.857,0.077,2.054c0.497,0.542,0.798,1.235,0.798,2.082 c0,2.981-1.814,3.637-3.543,3.829c0.279,0.24,0.527,0.713,0.527,1.437c0,1.037-0.01,1.874-0.01,2.129 c0,0.208,0.14,0.449,0.534,0.373c3.081-1.028,5.302-3.935,5.302-7.362C15.76,3.906,12.285,0.431,7.999,0.431z"/></svg><span class="username"> ploeh/ZeroToNine</span></span></a></small> -- antes de que Alex nos explicara por encima los entresijos del lenguaje no entendía del todo bien como funcionaba.

Especialmente importante me pareció el concepto de `TypeProvider`. No era consciente de que existían componentes que permitían recuperar datos de tan diversas fuentes que ya estaban programados y que, además, podíamos programar más nosotros (aunque Alex insistió en que era una tarea complicada).

![dotNetSpain2015-Fsharp](/assets/dotNetSpain2015-Fsharp.jpg)

De todos modos, aunque me aportó mucho valor el contenido, no se me hizo tan amena como el resto de charlas del día -- y sin duda fue por mi predisposición, no por culpa de Alex.


### Introducción práctica a Roslyn
De todas las charlas del día, esta era una para las que no estaba especialmente motivado, pero que terminó por sorprenderme por la practicidad que Juan Manuel le dió a toda la sesión.

En ella, se nos explicaba qué era __<a href="https://github.com/dotnet/roslyn"><span class="icon  icon--github"><svg viewBox="0 0 16 16"><path fill="#828282" d="M7.999,0.431c-4.285,0-7.76,3.474-7.76,7.761 c0,3.428,2.223,6.337,5.307,7.363c0.388,0.071,0.53-0.168,0.53-0.374c0-0.184-0.007-0.672-0.01-1.32 c-2.159,0.469-2.614-1.04-2.614-1.04c-0.353-0.896-0.862-1.135-0.862-1.135c-0.705-0.481,0.053-0.472,0.053-0.472 c0.779,0.055,1.189,0.8,1.189,0.8c0.692,1.186,1.816,0.843,2.258,0.645c0.071-0.502,0.271-0.843,0.493-1.037 C4.86,11.425,3.049,10.76,3.049,7.786c0-0.847,0.302-1.54,0.799-2.082C3.768,5.507,3.501,4.718,3.924,3.65 c0,0,0.652-0.209,2.134,0.796C6.677,4.273,7.34,4.187,8,4.184c0.659,0.003,1.323,0.089,1.943,0.261 c1.482-1.004,2.132-0.796,2.132-0.796c0.423,1.068,0.157,1.857,0.077,2.054c0.497,0.542,0.798,1.235,0.798,2.082 c0,2.981-1.814,3.637-3.543,3.829c0.279,0.24,0.527,0.713,0.527,1.437c0,1.037-0.01,1.874-0.01,2.129 c0,0.208,0.14,0.449,0.534,0.373c3.081-1.028,5.302-3.935,5.302-7.362C15.76,3.906,12.285,0.431,7.999,0.431z"/></svg><span class="username"> dotnet/Roslyn</span></span></a>__ -- _<small>y dónde estaba la ciudad que le daba nombre, con camiseta de premio incluida para el que lo acertara</small>_ -- y cómo, por ejemplo, Visual Studio 2015 aprovechaba toda su potencia para ofrecer revisión de código directa durante la escritura del mismo.

Me quedó la sensación de que herramientas de soporte al desarrollo como __[R#][ReSharperUrl]__ que hasta ahora acaparaban gran parte del mercado iban a dejar de tener sentido con la salida de Visual Studio 2015, más después de que Juan Manuel me confirmara por Twitter que esta explotación de Roslyn iba a estar disponible en todas las versiones de Visual Studio.

Gran experiencia, y muy sorprendido, la verdad.

### Effective C#
Como colofón final, la charla de Leo de nuevo me ponía otra vez en mi sitio, haciéndome ver que no tenía ni idea realmente de lo que sucedía _under the hood_ cuando el compilador de C# pasaba el código a IL y que, por tanto, mi conocimiento del lenguaje era mucho más limitado de lo que de inicio a mi me podía parecer.

> Mi recomendación con las enumeraciones es que no las uséis.

<figure>    
    <figcaption style="text-align: right;">Leo Antoli &copy;</figcaption>
</figure>

> La herencia de clases está sobrevalorada. [...] Sólo utilizad la herencia cuando realmente exista una relación de herencia. En el resto de casos, usad la composición.

<figure>    
    <figcaption style="text-align: right;">Leo Antoli &copy;</figcaption>
</figure>

Esta charla se solapó en algunos puntos con el _HOL_ del día anterior, pero la verdad es que no me resultó aburrida, sino más bien todo lo contrario. Es más -- <small>y se lo dije a Leo</small> --, algunas de las preguntas que se habían planteado el día anterior... ¡volví a fallarlas!

## Experiencia global
La experiencia que he tenido durante estos dos días ha sido __excelente__.

He estado recientemente en dos eventos de grandes dimensiones: [CodeMotion España 2014][CodeMotionUrl] y dotNet Spain Conference 2015.

Son eventos similares, en cuanto a dimensiones y en cuanto a la selección del espacio para celebrarlo y de la organización en _Tracks_.

De ambas me he llevado sensaciones buenas, pero hubo algo que en CodeMotion no me gustó y que en dotNet Spain no se ha producido más que en una de las sesiones a las que he asistido: __la gente se marchaba de la sala antes de que el ponente terminara para llegar a la siguiente charla__.

Y no solo eso: se marchaba haciendo __ruido__.

Creo que durante la CodeMotion, el equipo de Microsoft que allí estaba tomó nota de esto y planificó las charlas para evitar este tipo de situaciones. O igual fue cosa de la casualidad... no lo sé.

Lo cierto es que creo que los ponentes se merecen un gran respeto, no sólo por lo que saben, sino por enseñárnoslo a los que asistimos a sus charlas de forma completamente desinteresada -- _<small>¿Cobran por dar la ponencia? Yo creo que no...</small>_ --.

<big><em>Gracias a todos vosotros, ponentes y organizadores, por permitirme pasar un fin de semana aprendiendo y conviviendo con gente que como yo, disfruta del desarrollo.</em></big>

Definitivamente, le estoy cogiendo el gustillo a esto de las conferencias... En la siguiente conferencia de este tipo que organice Microsoft en España, seguro que intentaré estar.

[dotNetSpain2015Url]: https://www.desarrollaconmicrosoft.com/Dotnetspain2015 "DotNet Spain Conference 2015"
[Leonard_Nimoy]: http://en.wikipedia.org/wiki/Leonard_Nimoy "Leonard Nimoy"
[MartinFowlerRefactoringUrl]: http://martinfowler.com/books/refactoring.html
[ReSharperUrl]: https://www.jetbrains.com/resharper/
[CodeMotionUrl]: http://2014.codemotion.es/en/