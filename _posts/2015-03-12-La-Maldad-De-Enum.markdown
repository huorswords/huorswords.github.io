---
layout: post
title:  "La maldad de Enum"
date:   2015-03-12 13:00:00
categories: .net
---
Ayer surgió mi primera oportunidad de aplicar a la práctica parte de lo aprendido en la pasada [#dotNetSpain2015](https://www.desarrollaconmicrosoft.com/dotnetspain2015).

Retomando el [post]({% post_url 2015-03-03-Experiencia-dot-Net-Spain-2015 %}) anterior, durante su charla de _Effective C#_, __Leo Antoli__ <small>| <a href="https://twitter.com/lantoli"><span class="icon  icon--twitter"><svg viewBox="0 0 16 16"><path fill="#828282" d="M15.969,3.058c-0.586,0.26-1.217,0.436-1.878,0.515c0.675-0.405,1.194-1.045,1.438-1.809c-0.632,0.375-1.332,0.647-2.076,0.793c-0.596-0.636-1.446-1.033-2.387-1.033c-1.806,0-3.27,1.464-3.27,3.27 c0,0.256,0.029,0.506,0.085,0.745C5.163,5.404,2.753,4.102,1.14,2.124C0.859,2.607,0.698,3.168,0.698,3.767 c0,1.134,0.577,2.135,1.455,2.722C1.616,6.472,1.112,6.325,0.671,6.08c0,0.014,0,0.027,0,0.041c0,1.584,1.127,2.906,2.623,3.206 C3.02,9.402,2.731,9.442,2.433,9.442c-0.211,0-0.416-0.021-0.615-0.059c0.416,1.299,1.624,2.245,3.055,2.271 c-1.119,0.877-2.529,1.4-4.061,1.4c-0.264,0-0.524-0.015-0.78-0.046c1.447,0.928,3.166,1.469,5.013,1.469 c6.015,0,9.304-4.983,9.304-9.304c0-0.142-0.003-0.283-0.009-0.423C14.976,4.29,15.531,3.714,15.969,3.058z"/></svg><span class="username"> __@lantoli__</span></span></a></small> habló del comportamiento de las `enum` en C#.

> Mi recomendación con las enumeraciones es que no las uséis.

<figure>    
    <figcaption style="text-align: right;">Leo Antoli &copy;</figcaption>
</figure>

Pues bien... Ayer sufrí en primera persona uno de los motivos por los que hay que evitar las enumeraciones siempre que sea posible.

# Situación inicial

Dentro de una librería de proyecto con la que trabajo habitualmente -- _<small>pero que no puedo modificar</small>_ --, me encontré con una `enum` parecida a la siguiente.

{% highlight csharp %}
[Flags]
public enum OperatingSystem
{
    Any = 0,
    Windows7 = 1,
    WindowsServer2008 = 2,
    Windows8 = 4,
    WindowsServer2012 = 8,
    All = 15,
}
{% endhighlight %}

A raíz de una nueva solicitud funcional recibida por parte del cliente, es necesario incluir un valor de registro de Windows donde se almacene un valor numérico que se corresponda con lo que el usuario definió como:

> Un valor válido de sistema operativo.

Además, el usuario quería que se mostrara un mensaje en el visor de eventos del equipo que indicase el valor y advirtiera de que se utilizaría el valor por defecto `Any`.

#### Pero... ¿qué es un valor válido?

En este caso, para el usuario un valor válido se corresponde con un valor _simple_ o _compuesto_ de la enumeración `OperatingSystem`, donde:

* Un valor __simple__ se corresponde con un valor __directo__. Por ejemplo, el valor __1__, que se corresponde con `OperatingSystem.Windows7`.

* Un valor __compuesto__ se corresponde con aquel valor fruto de la __combinación__ de dos o más valores. Por ejemplo, el valor __3__, que se corresponde con `OperatingSystem.Windows7` + `OperatingSystem.WindowsServer2008`.

Además, como requisito, al almacenar el valor en el registro de Windows, este valor tiene que ser obligatoriamente un valor numérico.

# Problemas

¿Qué problema hay con esta solicitud que, en principio, parece sencilla de implementar?

El mayor problema aquí es la definición que da el usuario sobre lo que para él es _un valor válido_.

Mi primera aproximación fue utilizar el siguiente fragmento de código.

{% highlight csharp %}
bool isDefined = Enum.IsDefined(typeof(OperatingSystem), registryValue);
if (!isDefined)
{
	throw new InvalidCastException();
}
{% endhighlight %}

Este código no permite que la segunda premisa definida como valor válido se cumpla: ***no admite valores compuestos***.

Por tanto, fui más allá e implementé esto:

{% highlight csharp %}

OperatingSystem operatingSystem = (OperatingSystem)valorRegistro; // valorRegistro es de tipo int
// ...

bool found = false;
foreach (OperatingSystem item in typeof(OperatingSystem).GetEnumValues())
{       
    found = operatingSystem.HasFlag(item);
    if (found)
    {
        break;
    }
}

if (!found)
{
    throw new InvalidCastException();
}
{% endhighlight %}

Parece una solución correcta en principio, pero...

> ¿Qué pasa si ponemos un valor superior a 15? Por ejemplo, ¿qué sucede con el valor 16?

Pues lo que pasa es que si en el registro hay un `16`, que en teoría tendría que generar una excepción de tipo `InvalidCastException`, en realidad está siendo tratada como el valor `0` por culpa del casting explícito.

Cuando el código revisa si el valor recuperado contiene el valor mediante `Enum.HasFlag()`, en realidad sólo está comparando los bits significativos de los valores disponibles, que en este caso concreto son los 4 primeros bits.

|--------+------------------+--------------------------------|
|Integer |Binary (8 bits)           |Valor			                 |  
|-------:|-----------------:|--------------------------------|
|0	     |0000 0000	        |`Any`                           |
|1	     |0000 0001	        |`Windows7`                      |
|2	     |0000 0010	        |`WindowsServer2008`             |
|3	     |0000 0011	        |`Windows7` + `WindowsServer2008`|
|15	     |0000 1111	        |`All`                           |
|16	     |0001 <strong>0000</strong>	        |`Any`       |
|17	     |0001 <strong>0001</strong>	        |`Windows7`  |
|--------|------------------|--------------------------------|

<br />

Mola, ¿verdad?

# Solución

Creo que la solución más adecuada para implementar este comportamiento de forma correcta es almacenar los ___nombres___ en vez de los ___valores numéricos___ de cada elemento de la `enum` en un valor MULTI_SZ del registro de Windows. Pero el valor tenía que ser obligatoriamente numérico.

No obstante, en este caso, la solución ha pasado porque el usuario comprendiera que en realidad no necesitaba la composición, ya que en su modelo de negocio sólo se podrían dar tres de los casos directos que incluye la `enum`: `Any`, `Windows7` y `WindowsServer2008`.

Así, la solución adoptada ha sido finalmente utilizar la primera versión del código.

{% highlight csharp %}
bool isDefined = Enum.IsDefined(typeof(OperatingSystem), registryValue);
if (!isDefined)
{
	throw new InvalidCastException();
}
{% endhighlight %}

¡Ay que ver qué cosas!

# Moraleja 

> ¡Mucho cuidado con las `enums`, especialmente si tienen la propiedad `[Flags]` marcada!


