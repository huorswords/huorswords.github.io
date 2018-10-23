---
layout: post
title:  "Azure DevOps | Ejecutar tests funcionales con Selenium y Docker (1/3)"
date:   2018-10-22 18:00:00
categories: AzureDevOps receta recipe build release pipeline functional tests docker selenium
---
# >_

## Escenario

Véase un proyecto web tipo en el que, gracias a la alineación de los astros, hayamos podido incluir funcional testing en nuestro plan de pruebas integral. Imagina por un momento que pudiésemos incluir dichos tests dentro de nuestros procesos de continuous integration / continuous deployment. Imagina también que la ejecución de dichos tests se realice **ANTES** de aceptar o desplegar una versión de nuestra aplicación web, de manera que evitemos introducir errores de regresión funcional en nuestra aplicación.

Todo esto es extremadamente sencillo si tu escenario contempla las siguientes tecnologías:

* Selenium como framework de testing funcional
* Proyecto .NET Core 1.x o superior
* Functional tests escritos en C#
* Azure DevOps

## Functional testing

Dentro de un plan de testing no podemos olvidarnos de las pruebas funcionales o **functional tests**.

Los **pruebas funcionales** son aquellas pruebas que nos permiten realizar verificaciones sobre un sistema informático dado, de manera que dichas verificaciones nos indiquen si el producto no sólo hace lo que se espera de él sino que además se ajusta a una especificación dada.

Existen, según su tipología, diferentes tipos de test funcionales. Por ejemplo...

* **Tests de regresión**, que permiten evitar que cualquier modificación introducida en vuestras aplicaciones provoque un bug en funcionalidad previamente verificada en fases anteriores de testing.
* **Tests de compatibilidad**, que permiten asegurar que la aplicación funciona en diferentes entornos, navegadores…
* **Tests de aceptación**, que aunque se pueden considerar parte del functional testing, a menudo se separan en una categoría aparte.

La validación que un ingeniero de QA, o incluso el usuario final, realiza sobre una aplicación se puede considerar testing funcional (en tanto que se utilice, por ejemplo, como pruebas de aceptación), aunque en muchos casos esta validación se realiza de forma manual. No es el escenario que perseguimos.

## Selenium

Selenium es un motor de ejecución de tests funcionales para aplicaciones web. Permite, entre otras cosas, automatizar los procesos de navegación que realiza el usuario en una aplicación web.

De este modo, utilizando Selenium podemos incluir la ejecución de este conjunto de pruebas funcionales de forma automatizada, y para hacerlo podemos utilizar multitud de lenguajes de programación, entre los que se encuentra C#.

## Usar Selenium con C# y MSTest

Para utilizar Selenium desde C#, disponemos de un paquete nuget a importar desde nuestro proyecto de tests (MSTest).

* [Selenium.WebDriver](https://www.nuget.org/packages/Selenium.WebDriver/)

Este paquete nos permitirá utilizar los drivers de conexión de varios navegadores (Chrome, Firefox, Edge, Safari...).

La estructura básica de tu clase de test tendrá un aspecto similar a la siguiente:

```csharp
[TestClass]
public class YourWebShould
{
    protected IWebDriver webDriver;

    [TestInitialize]
    public virtual void Setup()
        => this.webDriver = new ChromeDriver(Environment.CurrentDirectory); // Replace the driver by the Firefox one to test with firefox

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
```

Para más información sobre cómo crear tus tests Selenium en C#, te recomiendo que visites su [página de documentación oficial](https://www.seleniumhq.org/docs/03_webdriver.jsp).

Si quieres utilizar estos tests, por ejemplo, para probar con los navegadores Chrome y Firefox instalados en tu equipo local, necesitarás además importar en tu proyecto de test estos dos otros paquetes:

* [Selenium.Chrome.WebDriver](https://www.nuget.org/packages/Selenium.Chrome.WebDriver/)
* [Selenium.Firefox.WebDriver](https://www.nuget.org/packages/Selenium.Firefox.WebDriver/)

En estos dos paquetes se incluyen las aplicaciones necesarias para acceder al [ChromeDriver interface](http://chromedriver.chromium.org/getting-started) y al [FirefoxDriver interface](https://developer.mozilla.org/en-US/docs/Web/WebDriver). Os permitirán que vuestro VS se conecte al navegador que seleccionéis y que levante una instancia manejada de dicho navegador, de manera que esa instancia estará bajo el control del driver de Selenium y será la que se usará para ejecutar las pruebas automáticas.

## Continuará

Sigue con la [segunda parte de este post](Ejecutar-tests-selenium-en-Azure-DevOps-mediante-docker-2.html) para saber más acerca de cómo utilizar Selenium Grid Hub y Docker.