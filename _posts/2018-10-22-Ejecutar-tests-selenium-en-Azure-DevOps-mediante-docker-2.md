---
layout: post
title:  "Azure DevOps | Ejecutar tests funcionales con Selenium y Docker (2/3)"
date:   2018-10-22 18:01:00
categories: AzureDevOps receta recipe build release pipeline functional tests docker selenium
---
# >_

## Escenario

Si no has leído la [parte 1](Ejecutar-tests-selenium-en-Azure-DevOps-mediante-docker.html), y quieres saber qué son los tests funcionales o como Selenium permite crear pruebas funcionales con C#, te recomiendo que la leas antes de continuar. Si por contra quieres ir al meollo de Selenium Grid Hub y Docker... sigue leyendo. ;)

## Selenium Grid Hub

Uno de los tipos de tests funcionales que existen son los **tests de compatibilidad**.

Estas pruebas permiten certificar que la aplicación web funciona de la misma manera en diferentes navegadores y sistemas operativos.

Si leíste el post anterior, sabes que explicaba cómo definir una estructura básica de un test utilizando la clase driver correspondiente al navegador que queríamos probar. Pero claro, entre otras cosas, usar esos drivers nos  obligaría a que la creación de tests de compatibilidad fuese específica por driver o que, en su defecto, pudiésemos cambiar de algún modo entre un driver y otro "a mano".

La suerte es que el nuget de Selenium incluye un `IWebDriver` especial llamado `RemoteWebDriver` que nos permite conectar a un artefacto de Selenium llamado **Selenium Grid Hub**.

**Selenium Grid Hub** es una solución Selenium que proporcina una especie de servidor donde se pueden registrar **nodos** de Selenium, donde cada nodo usualmente se corresponde con un navegador y un sistema operativo. Cuando el hub de Selenium recibe una solicitud de ejecución para un motor de navegador y sistema operativo dado, busca entre los nodos registrados y le pasa la petición al mismo, manteniéndose a la espera del resultado para devolvérselo al cliente.

## C# y Selenium Grid Hub

Para usar el `RemoteWebDriver` desde tu aplicación .NET Core, te recomiendo que recuperes los valores de configuración del driver mediante **Microsoft.Extensions.Configuration**. Esto te permitirá poder generalizar la ejecución basándote en valores de appsettings.json, en vez de tener que montar tu propio código de switch entre navegadores y sistemas operativos.

Si lo haces via appsettings.json (y variables de entorno), tendrás algo tal que así.

```csharp
[TestClass]
public class YourWebShould
{
    protected IWebDriver webDriver;

    [TestInitialize]
    public virtual void Setup()
    {
      // Additional nuget packages:
      // - Microsoft.Extensions.Configuration.Binder
      // - Microsoft.Extensions.Configuration.EnvironmentVariables
      // - Microsoft.Extensions.Configuration.Json
      var configuration =
        new ConfigurationBuilder().SetBasePath(Environment.CurrentDirectory)
                      .AddJsonFile("appsettings.json")
                      .AddEnvironmentVariables()
                      .Build();

      var seleniumOptions = new SeleniumOptions();
      configuration.Bind("SeleniumDockerHost", seleniumOptions);
      this.webDriver = new RemoteWebDriver(
        seleniumOptions.WebDriverUri,
        seleniumOptions.DriverOptions);
    }

    [TestCleanup]
    public virtual void Cleanup()
    {
        this.webDriver?.Close();
        this.webDriver?.Quit();
    }

    [TestMethod]
    public void Navigate_Properly_To_Your_Home_Page()
    {
        // Your test code here
    }
}

internal class SeleniumOptions
{
  public string Host { get; set; }
  public int Port { get; set; }
  public string BrowserName { get; set; }

  public Uri WebDriverUri
  {
    get
    {
      string url = $"http://{this.Host}:{this.Port}/wd/hub";
      if (Uri.IsWellFormedUriString(url, UriKind.Absolute))
      {
        return new Uri(url);
      }

      return null;
    }
  }

  public DriverOptions DriverOptions
  {
    get
    {
      if (!string.IsNullOrEmpty(this.BrowserName))
      {
        switch (this.BrowserName.ToLower())
        {
          case "firefox":
            return new OpenQA.Selenium.Firefox.FirefoxOptions();

          case "chrome":
            return new OpenQA.Selenium.Chrome.ChromeOptions();

          default:
            throw new ArgumentOutOfRangeException(nameof(this.BrowserName));
        }
      }

      return null;
    }
  }
}
```

Esta estructura base de test te permite configurar un `RemoteWebDriver` pasándole una serie de opciones de configuración al driver.

## Selenium Grid Hub y Docker

Voy a centrarme en explicar cómo funciona **Selenium Grid Hub** y sus nodos usando contenedores Docker.

Independientemente de usar Docker o no, la gracia de usar Selenium Grid Hub es que permite utilizar un único punto de entrada al motor de Selenium y desde ahí, abstraerse de dónde provienen (nodos) los resultados de la ejecución del test.

Por suerte, [SeleniumHQ](https://github.com/SeleniumHQ) ha creado un [repositorio open source](https://github.com/SeleniumHQ/docker-selenium) donde ha almacenado todas las definiciones de las imágenes de docker oficiales de Selenium que hay disponibles, desde imágenes Standalone (sistema operativo + navegador X) hasta Selenium Grid Hub y sus nodos.

### Docker compose

Para levantar un grid de dos nodos (Chrome y Firefox) de Selenium, podemos utilizar el siguiente fichero `docker-compose.yml`.

```yml
# To execute this docker-compose yml file use `docker-compose -f <file_name> up`
# Add the `-d` flag at the end for detached execution
version: "3.7"
services:
  selenium-hub:
    image: selenium/hub
    container_name: selenium-hub
    ports:
      - "4444:4444"
    shm_size: '2gb'
  chrome:
    image: selenium/node-chrome
    depends_on:
      - selenium-hub
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
  firefox:
    image: selenium/node-firefox
    depends_on:
      - selenium-hub
    environment:
      - HUB_HOST=selenium-hub
      - HUB_PORT=4444
```

Teniendo este fichero creado, lo único que tenemos que hacer es invocar `docker-compose -f ruta/al/fichero/docker-compose.yml up`.

El conjunto de contenedores resultante incluirá:

* Una instancia de Selenium Grid Hub
* Un nodo de Chrome
* Un nodo de Firefox

A partir de ese momento, podremos atacar a la dirección http://localhost:4444/wd/hub desde nuestro equipo para acceder al Grid Hub de Selenium y ver el estado de los nodos y, además, ejecutar cualquier juego de pruebas.

## Continuará

Si quieres ir un paso más allá y probar *Selenium Grid Hub* y *Docker* desde *Azure DevOps*, sigue leyendo la [última parte](Ejecutar-tests-selenium-en-Azure-DevOps-mediante-docker-3.html) de este artículo.