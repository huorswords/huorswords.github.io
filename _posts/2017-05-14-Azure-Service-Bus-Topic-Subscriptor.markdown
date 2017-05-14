---
layout: post
title:  "Azure Service Bus: Topic / Subscription"
date:   2017-05-14 23:00:00
categories: azure service-bus
---

En las últimas semanas, por necesidades de un proyecto en el que estoy inmerso, me he visto empezando a jugar con Microsoft Azure Service Bus.

## ¿Qué es Azure Service Bus?

Microsoft Azure Service Bus es un servicio de mensajería en la nube (**MaaS** o _Messaging as a Service_). Básicamente, y en lenguaje sencillo, permite establecer un canal de comunicación entre vuestra aplicación y el mundo exterior, o entre uno o  más tiers de vuestro sistema o aplicación.

### Estrategias de conexión en Azure SB
Actualmente, Azure SB proporciona 3 estrategias de conexión, en función del tipo de mensajería que quieras establecer entre tus aplicaciones y servicios.

- Comunicaciones _one-way_ mediante **Colas** (o *Queues*), en las que existe un emisor y un receptor, y donde la infraestructura Azure SB se encarga de almacenar los mensajes generados por el emisor hasta que éstos son consumidos por el receptor.
- Comunicaciones bidireccionales mediante **Relay**, en la que se establece un canal de comunicación entre dos aplicaciones o servicios, sin almacenaje de mensajes intermedio. Sólo se establece canal de comunicación.
- Comunicaciones unidireccionales de uno a N (un emisor, N receptores), mediante el uso de la estrategia **Topic/Subscription**, que es en la que voy a intentar profundizar un poco más.

### Topic/Subscription en Azure SB
La estrategia de _topic/subscription_ permite definir un canal de comunicación en el que un emisor publica mensajes en una cola especial (o **topic**) sobre la que se pueden definir uno o varios puntos de escucha (o **subscriptions**).

La gracia de esta aproximación es que mediante el uso de suscripciones puedes establecer una lógica de filtrado que en el paradigma de colas convencional no permite.

Los tipos de filtrado que admite una suscripción son:
- Filtros booleanos.
- Filtros correlativos.
- Filtros SQL.

No me voy a parar mucho para explicar las diferencias de cada uno de ellos, pero si queréis tener algo más de información de los mismos, seguid el siguiente [enlace](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/blob/master/TopicFilters/readme.md#what-are-subscription-filters).

## El ejemplo
### Escenario

En el escenario que tengo que resolver, necesito publicar un mensaje que llevará incrustada una propiedad personalizada llamada **EventType**.

Cada *EventType* tendrá un suscriptor asociado, de manera que a un suscriptor sólo le llegarán los mensajes de su *EventType*.

### .NET Standard vs .NET Framework
Os aviso. A día de hoy, el paquete <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/0.0.3-preview"> nuget</a> que Microsoft proporciona para utilizar Azure SB desde una aplicación .NET Core o .NET Standard **no permite la creación de suscripciones con filtros**. Es un proyecto que aún está en gestación... lo bueno es que es <svg viewBox="0 0 16 16" height="16" xmlns="http://www.w3.org/2000/svg" fill-rule="evenodd" clip-rule="evenodd" stroke-linejoin="round" stroke-miterlimit="1.414"><path d="M8 0C3.58 0 0 3.582 0 8c0 3.535 2.292 6.533 5.47 7.59.4.075.547-.172.547-.385 0-.19-.007-.693-.01-1.36-2.226.483-2.695-1.073-2.695-1.073-.364-.924-.89-1.17-.89-1.17-.725-.496.056-.486.056-.486.803.056 1.225.824 1.225.824.714 1.223 1.873.87 2.33.665.072-.517.278-.87.507-1.07-1.777-.2-3.644-.888-3.644-3.953 0-.873.31-1.587.823-2.147-.09-.202-.36-1.015.07-2.117 0 0 .67-.215 2.2.82.64-.178 1.32-.266 2-.27.68.004 1.36.092 2 .27 1.52-1.035 2.19-.82 2.19-.82.43 1.102.16 1.915.08 2.117.51.56.82 1.274.82 2.147 0 3.073-1.87 3.75-3.65 3.947.28.24.54.73.54 1.48 0 1.07-.01 1.93-.01 2.19 0 .21.14.46.55.38C13.71 14.53 16 11.53 16 8c0-4.418-3.582-8-8-8"/></svg> [open source](https://github.com/Azure/azure-service-bus-dotnet), así que si os animáis, podéis hacer que la criatura evolucione más rápido.

Así que, para la creación de la suscripción, no queda más remedio que seguir utilizando el <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/"> nuget</a> de .NET Framework.

Vamos al lío, ¿no?

### El código (y todo lo demás)

Antes de nada, debes disponer de un Azure Service Bus creado en tu suscripción. Para ello, busca por **Service Bus** en el gestor de recursos de Azure y deberá salir algo tal que así:

![Create Azure Service Bus Screenshot](/assets/AzureSB_Create.jpg)

Recordad que, durante el alta del servicio, donde tenéis que definir vuestro namespace y demás, debéis aseguraros de qué plan de precios estáis configurando para el servicio. Para probar, con el estándar ya tenéis suficiente... aunque si os sobra la pasta... ¿Qué son 668€ al mes de estimado? ;)

![Select correct Azure Service Bus pricing Screenshot](/assets/AzureSB_Pricing.jpg)

'Ojo cuidao', que el plan básico no nos sirve para Topic / Suscription, porque el plan de precios no lo soporta.

En mi caso no tenía que gestionar la creación de los Topic, con lo que lo he dado de alta directamente desde el portal.

![Create new Azure Service Bus Topic](/assets/AzureSB_CreateTopic.jpg)

Lo que sí que era requisito en el escenario es el crear de forma dinámica las suscripciones necesarias por tipo de evento.

Y para eso, he usado el siguiente código fuente.

{% highlight csharp %}

/// <summary>
/// Create the subscription.
/// </summary>
/// <param name="subscriptionName">The subscription name.</param>
/// <param name="sqlExpression">The SQL expression to be used as a filter.</param>
/// <returns>The <see cref="Task"/> with the asynchronous execution.</returns>
public async Task<EventTypeSettings> CreateSubscriptionAsync(
    string subscriptionName,
    string sqlExpression)
{
    SubscriptionDescription subscription;
    if (!this.namespaceManager.SubscriptionExists(
        this.configuration.TopicName,
        subscriptionName))
    {
        Filter rule = null;
        if (!string.IsNullOrEmpty(sqlExpression))
        {
            rule = new SqlFilter(sqlExpression);
            subscription = await this.namespaceManager.CreateSubscriptionAsync(
                this.configuration.TopicName,
                subscriptionName,
                rule);
        }
        else
        {
            subscription = await this.namespaceManager.CreateSubscriptionAsync(
                this.configuration.TopicName,
                subscriptionName);
        }
    }
    else
    {
        subscription = await this.namespaceManager.GetSubscriptionAsync(
            this.configuration.TopicName,
            subscriptionName);
    }

    return subscription.ToEventTypeSettings();
}

{% endhighlight %}

Éste método, pasando por todas las capas de aplicación "necesarias", es invocado desde una ASP.NET WebApi 2 RESTFul. Por ese motivo, devuelvo siempre *EventTypeSettings*, que contiene básicamente la connection string, el topic y el nombre de la suscripción, para de esa manera permitir al que ha consumido mi API usar las propiedades de la suscripción que ha creado desde un cliente de SB.

{% highlight csharp %}

/// <summary>
/// Posts a new event type.
/// </summary>
/// <param name="model">The event type data to be posted.</param>
/// <returns>The <see cref="T:Task{IHttpActionResult}"/> instance.</returns>
[HttpPost]
[Route("")]
public async Task<IHttpActionResult> Post([FromBody]EventTypePostRequestModel model)
{
    if (model == null || !this.ModelState.IsValid)
    {
        return this.BadRequest();
    }

    var result = (await this.managementService.CreateListenerAsync(
        model.EventTypeName,
        model.FilterSqlExpression)).ToModel();
    return this.CreatedAtRoute("GetByName", new { name = result.Name }, result);
}
        
{% endhighlight %}

¿Cómo montamos el cliente?

En mi caso, he escogido usar el nuget "inestable" del que hablaba antes... sí... lo sé... muy hípster todo.

{% highlight csharp %}
/// <summary>
/// Subscribes to a given subscription name.
/// </summary>
/// <param name="subscriptionName">The subscription name.</param>
/// <param name="messageHandler">The message handler.</param>
public void Subscribe(
    string subscriptionName,
    Func<EventType, CancellationToken, Task> messageHandler)
{
    var subscriptionClient = new SubscriptionClient(
        this.configuration.ConnectionString,
        this.configuration.TopicName,
        subscriptionName);
    Func<Message, CancellationToken, Task> handler = async (message, token) =>
    {
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);
        await messageHandler(message.ToEventType(), token);
    };

    subscriptionClient.RegisterMessageHandler(handler);
}
{% endhighlight %}

Como habrás observado, estoy "completando" el mensaje al recibirlo. Esto es porque, después de hacer alguna prueba, tuve problemas con la gestión del número de copias (campo **MAX DELIVERY COUNT** de la configuración de la suscripción) que SB envía antes de considerar el mensaje como completado. Por defecto el valor de este parámetro es 10, así que... me llegaban de forma random hasta 10 copias del mismo mensaje, porque el proceso de gestión del mensaje tardaba más que el tiempo de reintento de envío por parte de SB.

Y, por último, la forma de pasarle el handler al método de suscripción, para indicarle qué debe hacer al recibir un mensaje. En el ejemplo, un simple **Console.WriteLine** con los datos del mensaje.

{% highlight csharp %}

/// <summary>
/// Subscribes to a given <paramref name="subscriptionName"/> name.
/// </summary>
/// <param name="subscriptionName">The subscription name.</param>
public void SubscribeTo(string subscriptionName)
{
    this.messagingAgent.Subscribe(
        subscriptionName,
        async (eventType, token) =>
        {
            await new ConsoleProvider().ExecuteAsync(eventType, token);
        });
}

/// <summary>
/// The console provider class.
/// </summary>
private class ConsoleProvider
{
    /// <summary>
    /// Executes the provider action.
    /// </summary>
    /// <param name="eventType">The event type.</param>
    /// <param name="token">The cancellation token.</param>
    /// <returns>The <see cref="Task"/>.</returns>
    public async Task ExecuteAsync(EventType eventType, CancellationToken token)
    {
        Console.WriteLine($"EventType.Id: {eventType.Id}");
        Console.WriteLine($"EventType.Type: {eventType.Type}");
        await Task.Run(() => { });
    }
}
{% endhighlight %}

Y eso es todo. :)