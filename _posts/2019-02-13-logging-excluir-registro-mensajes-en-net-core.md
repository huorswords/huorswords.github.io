---
layout: post
title:  "Logging | Excluir mensajes de logging en .NET Core"
date:   2019-02-13 16:00:00
categories: logging microsoft.extensions.logging net core 2.2
---
# >_

## Escenario

Hoy en día es habitual (además de recomendable) que tu aplicación incorpore algún tipo o herramienta de logging por defecto.

En el caso de .NET Core, cuando creamos una webapi, el sistema de logging de ASP .NET Core se activa de forma automática, de manera que cuando empiezas a consumir la API, consigues una salida de log similar a esta captura de pantalla.

![Screenshot de la consola de ejecución de una webapi con la configuración por defecto](/assets/logging-filters-webapi-default-logging-screenshot.jpg)

Imagina que quieres, por lo que sea, quieres eliminar de tu logging esas "molestas" líneas relacionadas con las requests que automáticamente se registran cuando una webapi en .NET core es consumida.

¿Cómo lo hacemos?

## El código

En realidad, es tremendamente sencillo hacer un filtrado (sin mucha personalización) de lo que desde ***Microsoft.Extensions.Logging*** se genera durante el logging de una aplicación ASP .NET Core.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureLogging((hostingContext, logging) =>
            {
                // Aquí se configura la 'magia'.
                logging.AddFilter("Microsoft", LogLevel.None);
            })
            .UseStartup<Startup>();
}
```

Si os fijáis, el fragmento de código se corresponde a la configuración inicial que en .NET Core 2.x se hace dentro de `Program.cs`.

Al introducir el uso del método `ConfigureLogging()`, podemos añadir filtros al sistema de logs de manera que podemos excluir aquellas líneas de log que contengan el texto indicado, únicamente seleccionando el nivel de log `LogLevel.None`.

En el ejemplo anterior, eliminamos cualquier mensaje con el texto `Microsoft`. Ese filtro implica dejar limpito el registro de cualquier mensaje de la captura anterior, distinto a los de inicialización de API.

![Screenshot de la consola de ejecución de una webapi con la configuración filtrada](/assets/logging-filters-webapi-filtered-logging-screenshot.jpg)

Intentémoslo con un filtrado más explícito: `Microsoft.AspNetCore.Hosting`.

![Screenshot de la consola de ejecución de una webapi con la configuración filtrada por Microsoft.AspNetCore.Hosting](/assets/logging-filters-webapi-filtered-hosting-logging-screenshot.jpg)

Esta configuración de filtrado, además, la puedes definir a nivel de Logging Provider, para que, por ejemplo, un tipo de mensaje específico sólo se registre en un Provider seleccionado, excluyéndose de los demás.

## Conclusión

¿Te molesta un tipo de mensaje específico de los que se registran en el logging de tu aplicación?

**ConfigureLogging to the rescue!**