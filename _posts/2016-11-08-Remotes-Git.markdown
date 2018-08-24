---
layout: post
title:  "Remotes en Git"
date:   2016-11-08 13:00:00
categories: source-control git
---

Este artículo pretende dejar un par de apuntes de cómo configurar un `remote` para un repositorio Git en diferentes escenarios.

Surge a raíz de un problema que mi amigo __Oriol Puig__ <small>| <a href="https://twitter.com/oriolpuigbuil"><span class="icon  icon--twitter"><svg style="width:16;height:16;"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@oriolpuigbuil__</span></span></a></small> experimentó hace unos días, donde tuvo la necesidad de configurar un `remote` no estándar para un proyecto que está desarrollando actualmente.

# Remote estándar (bidireccional)
Un remote de Git, por defecto, se crea de forma bidireccional.

Esto es, se define un mismo repositorio remoto tanto para lectura (`fetch`/`pull`) como escritura (`push`).

Crear este tipo de remotes no es complicado.

{% highlight batch %}
> git remote add estandar <url>
> git remote -v
estandar  <url> (fetch)
estandar  <url> (push)
{% endhighlight %}

Este tipo de `remote` permite tanto leer como escribir cambios desde el repositorio local. Nada nuevo para aquellos que usamos o hemos usado Git.

# Remote de sólo lectura
No obstante, en alguna ocasión nos puede interesar disponer de un repositorio clonado que sólo pueda 'leer' del repositorio remoto.

Para ello, hay que ejecutar los siguientes comandos.

{% highlight batch %}
> git remote add readonly <url>
> git remote -v
readonly  <url> (fetch)
readonly  <url> (push)
> git remote set-url --push readonly <invalid_URL>
> git remote -v
readonly  <url> (fetch)
readonly  <invalid_URL> (push)
{% endhighlight %}

Esta configuración evitará que, por ejemplo, podamos hacer `push` por error al repositorio remoto, o que tengamos configurado un repositorio cuya única finalidad es precisamente ser punto de sólo lectura.

# Remote de sólo escritura
Igual que en el caso anterior, es posible que pueda interesarnos tener un repositorio clonado que sólo pueda 'escribir', de manera que no se puedan sincronizar los cambios desde el mismo en el repositorio clonado.

En este caso, los comandos a ejecutar son los siguientes.

{% highlight batch %}
> git remote add writeonly <invalid_URL>
> git remote -v
writeonly  <invalid_URL> (fetch)
writeonly  <invalid_URL> (push)
> git remote set-url --push writeonly <url>
> git remote -v
writeonly  <invalid_url> (fetch)
writeonly  <url> (push)
{% endhighlight %}

Este escenario permite ignorar completamente los `commit` generados en el repositorio remoto, permitiendo publicar, por ejemplo, ramas de trabajo que no serán sincronizadas directamente en nuestro repositorio local en caso de que éstas cambien en remoto.