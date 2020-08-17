---
title: Dependency Injection in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130648"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="05a15-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05a15-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="05a15-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="05a15-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="05a15-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="05a15-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="05a15-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="05a15-107">Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="05a15-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="05a15-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05a15-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="05a15-109">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-109">Overview of dependency injection</span></span>

<span data-ttu-id="05a15-110">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="05a15-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="05a15-111">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="05a15-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="05a15-112">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="05a15-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="05a15-113">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="05a15-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="05a15-114">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="05a15-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="05a15-115">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="05a15-116">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="05a15-117">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="05a15-118">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="05a15-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="05a15-119">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="05a15-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="05a15-120">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="05a15-121">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="05a15-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="05a15-122">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="05a15-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="05a15-123">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="05a15-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="05a15-124">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="05a15-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="05a15-125">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="05a15-126">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="05a15-127">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="05a15-128">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="05a15-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="05a15-129">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="05a15-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="05a15-130">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="05a15-131">Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung.</span><span class="sxs-lookup"><span data-stu-id="05a15-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="05a15-132">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="05a15-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="05a15-133">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="05a15-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="05a15-134">Mit dem DI-Muster:</span><span class="sxs-lookup"><span data-stu-id="05a15-134">With the DI pattern:</span></span>

* <span data-ttu-id="05a15-135">Der Controller verwendet nicht den konkreten Typ `MyDependency`, sondern nur die Schnittstelle `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="05a15-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="05a15-136">Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="05a15-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="05a15-137">Der Controller erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="05a15-138">Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="05a15-139">Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="05a15-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="05a15-140">`MyDependency2` erfordert, dass <xref:Microsoft.Extensions.Logging.ILogger`1> im Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="05a15-141">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="05a15-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="05a15-142">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="05a15-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="05a15-143">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="05a15-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="05a15-144">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="05a15-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="05a15-145">`ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="05a15-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="05a15-146">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:</span><span class="sxs-lookup"><span data-stu-id="05a15-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="05a15-147">Im Dependency-Injection-Kontext ist ein Dienst:</span><span class="sxs-lookup"><span data-stu-id="05a15-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="05a15-148">ein Objekt, das anderem App-Code einen Dienst wie `IMyDependency` bereitstellt</span><span class="sxs-lookup"><span data-stu-id="05a15-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="05a15-149">kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann</span><span class="sxs-lookup"><span data-stu-id="05a15-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="05a15-150">Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="05a15-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="05a15-151">Die `IMyDependency`-Implementierungen wurden geschrieben, um die DI grundlegend zu veranschaulichen, ohne die Protokollierung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="05a15-152">Die meisten Apps müssen keine Protokollierungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="05a15-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="05a15-153">Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="05a15-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="05a15-154">Wenn Sie den vorangehenden Code verwenden, muss `ConfigureServices` nicht aktualisiert werden, da die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="05a15-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="05a15-155">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="05a15-155">Services injected into Startup</span></span>

<span data-ttu-id="05a15-156">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="05a15-157">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="05a15-158">Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="05a15-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="05a15-159">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="05a15-159">Register additional services with extension methods</span></span>

<span data-ttu-id="05a15-160">Wenn die Erweiterungsmethode einer Dienstsammlung einen Dienst registrieren kann:</span><span class="sxs-lookup"><span data-stu-id="05a15-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="05a15-161">Üblicherweise wird eine einzelne `Add{SERVICE_NAME}`-Erweiterungsmethode verwendet, um alle für diesen Dienst erforderlichen Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="05a15-162">Die abhängigen Dienste werden ebenfalls registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-162">The dependent services are also registered.</span></span>

<span data-ttu-id="05a15-163">Der folgende Code wird von der Razor Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> zusätzliche Dienste zum Container hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="05a15-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="05a15-164">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> und <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="05a15-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="05a15-165">Im Abschnitt [Kombinieren von Dienstsammlungen](#csc) finden Sie Anweisungen zum Schreiben einer Erweiterungsmethode für die Dienstregistrierung.</span><span class="sxs-lookup"><span data-stu-id="05a15-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="05a15-166">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-166">Service lifetimes</span></span>

<span data-ttu-id="05a15-167">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="05a15-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="05a15-168">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="05a15-169">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-169">Transient</span></span>

<span data-ttu-id="05a15-170">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="05a15-171">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="05a15-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="05a15-172">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="05a15-173">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="05a15-173">Scoped</span></span>

<span data-ttu-id="05a15-174">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="05a15-175">Wenn Sie Entity Framework Core verwenden, registriert die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode standardmäßig `DbContext`-Typen mit einer begrenzten Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="05a15-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="05a15-176">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="05a15-177">Verwenden Sie bereichsbezogene Dienste in Middleware mit einem der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="05a15-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="05a15-178">Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode ein.</span><span class="sxs-lookup"><span data-stu-id="05a15-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="05a15-179">Wenn Sie die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) verwenden, wird zur Laufzeit eine Ausnahme ausgelöst, da der Dienst gezwungen wird, sich wie ein Singleton zu verhalten.</span><span class="sxs-lookup"><span data-stu-id="05a15-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="05a15-180">Im Beispiel für die [Lebensdauer- und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="05a15-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="05a15-181">[Factorybezogene Middleware](<xref:fundamentals/middleware/extensibility>):</span><span class="sxs-lookup"><span data-stu-id="05a15-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="05a15-182">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> überprüft, ob der registrierte Typ einer Middleware <xref:Microsoft.AspNetCore.Http.IMiddleware> implementiert.</span><span class="sxs-lookup"><span data-stu-id="05a15-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="05a15-183">Falls dies der Fall ist, wird die im Container registrierte <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>-Instanz im Container verwendet, um die <xref:Microsoft.AspNetCore.Http.IMiddleware>-Implementierung aufzulösen, anstatt die konventionsbasierte Middlewareaktivierungslogik zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="05a15-184">Die Middleware wird als bereichsbezogener oder vorübergehender Dienst im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="05a15-185">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="05a15-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="05a15-186">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="05a15-187">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="05a15-188">Folgendes ist zulässig:</span><span class="sxs-lookup"><span data-stu-id="05a15-188">It's fine to:</span></span>

* <span data-ttu-id="05a15-189">das Auflösen eines Singletondiensts aus einem bereichsbezogenen oder temporären Diensts.</span><span class="sxs-lookup"><span data-stu-id="05a15-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="05a15-190">das Auflösen eines bereichsbezogenen Diensts aus einem anderen bereichsbezogenen oder temporären Dienst</span><span class="sxs-lookup"><span data-stu-id="05a15-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="05a15-191">Standardmäßig wird in der Entwicklungsumgebung eine Ausnahme ausgelöst, wenn ein Dienst von einem anderen Dienst mit längerer Lebensdauer aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="05a15-192">Weitere Informationen finden Sie unter [Bereichsvalidierung](#sv).</span><span class="sxs-lookup"><span data-stu-id="05a15-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="05a15-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-193">Singleton</span></span>

<span data-ttu-id="05a15-194">Dienste mit Singletonlebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="05a15-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="05a15-195">wenn sie zum ersten Mal angefordert werden</span><span class="sxs-lookup"><span data-stu-id="05a15-195">The first time they're requested.</span></span>
* <span data-ttu-id="05a15-196">vom Entwickler, wenn eine Implementierungsinstanz direkt im Container bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="05a15-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="05a15-197">(selten benötigter Ansatz)</span><span class="sxs-lookup"><span data-stu-id="05a15-197">This approach is rarely needed.</span></span>

<span data-ttu-id="05a15-198">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="05a15-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="05a15-199">Wenn für die App Singletonverhalten erforderlich ist, sollte der Dienstcontainer die Dienstlebensdauer verwalten.</span><span class="sxs-lookup"><span data-stu-id="05a15-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="05a15-200">Wenn Sie das Singletonentwurfsmuster implementieren, sollten Sie keinen Code zum Löschen des Singleton angeben.</span><span class="sxs-lookup"><span data-stu-id="05a15-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="05a15-201">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="05a15-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="05a15-202">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton vom Container automatisch verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="05a15-203">Singletondienste müssen threadsicher sein und werden häufig in zustandslosen Diensten verwendet.</span><span class="sxs-lookup"><span data-stu-id="05a15-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="05a15-204">In Apps, die Anforderungen verarbeiten, werden Singletondienste gelöscht, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="05a15-205">Da der Arbeitsspeicher erst freigegeben wird, wenn die App heruntergefahren wurde, muss der Arbeitsspeicherverbrauch eines Singleton berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="05a15-206">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="05a15-207">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="05a15-208">Sie können einen Singletondienst aus einem bereichsbezogenen oder temporären Dienst auflösen.</span><span class="sxs-lookup"><span data-stu-id="05a15-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="05a15-209">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="05a15-209">Service registration methods</span></span>

<span data-ttu-id="05a15-210">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="05a15-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="05a15-211">Methode</span><span class="sxs-lookup"><span data-stu-id="05a15-211">Method</span></span> | <span data-ttu-id="05a15-212">Automatische</span><span class="sxs-lookup"><span data-stu-id="05a15-212">Automatic</span></span><br><span data-ttu-id="05a15-213">Objekt</span><span class="sxs-lookup"><span data-stu-id="05a15-213">object</span></span><br><span data-ttu-id="05a15-214">bereinigung</span><span class="sxs-lookup"><span data-stu-id="05a15-214">disposal</span></span> | <span data-ttu-id="05a15-215">Mehrere</span><span class="sxs-lookup"><span data-stu-id="05a15-215">Multiple</span></span><br><span data-ttu-id="05a15-216">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="05a15-216">implementations</span></span> | <span data-ttu-id="05a15-217">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="05a15-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="05a15-218">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="05a15-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="05a15-219">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-219">Yes</span></span> | <span data-ttu-id="05a15-220">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-220">Yes</span></span> | <span data-ttu-id="05a15-221">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-222">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="05a15-223">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-223">Yes</span></span> | <span data-ttu-id="05a15-224">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-224">Yes</span></span> | <span data-ttu-id="05a15-225">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="05a15-226">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="05a15-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="05a15-227">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-227">Yes</span></span> | <span data-ttu-id="05a15-228">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-228">No</span></span> | <span data-ttu-id="05a15-229">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-230">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="05a15-231">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-231">No</span></span> | <span data-ttu-id="05a15-232">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-232">Yes</span></span> | <span data-ttu-id="05a15-233">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-234">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="05a15-235">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-235">No</span></span> | <span data-ttu-id="05a15-236">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-236">No</span></span> | <span data-ttu-id="05a15-237">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-237">Yes</span></span> |

<span data-ttu-id="05a15-238">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="05a15-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="05a15-239">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="05a15-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="05a15-240">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="05a15-241">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="05a15-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="05a15-242">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="05a15-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="05a15-243">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="05a15-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="05a15-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst, wenn nicht bereits eine Implementierung *desselben Typs* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="05a15-245">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="05a15-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="05a15-246">Beim Registrieren von Diensten sollte der Entwickler nur dann eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="05a15-247">Im Allgemeinen verwenden Bibliotheksautoren `TryAddEnumerable`, um zu vermeiden, dass mehrere Kopien einer Implementierung im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="05a15-248">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="05a15-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="05a15-249">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="05a15-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="05a15-250">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="05a15-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="05a15-251">Bei der Dienstregistrierung ist die Reihenfolge unerheblich, wenn mehrere Implementierungen desselben Typs registriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="05a15-252">`IServiceCollection` ist eine Sammlung von <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="05a15-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="05a15-253">Im folgenden Codebeispiel wird veranschaulicht, wie einem Dienst ein Konstruktor hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="05a15-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="05a15-254">Die `Add{LIFETIME}`-Methoden verwenden denselben Ansatz.</span><span class="sxs-lookup"><span data-stu-id="05a15-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="05a15-255">Ein Beispiel finden Sie im [Quellcode für AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="05a15-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="05a15-256">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="05a15-256">Constructor injection behavior</span></span>

<span data-ttu-id="05a15-257">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="05a15-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="05a15-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="05a15-259">erstellt Objekte ohne Dienstregistrierung im Dependency-Injection-Container</span><span class="sxs-lookup"><span data-stu-id="05a15-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="05a15-260">wird mit Frameworkfeatures wie [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro), MVC-Controllern und [Modellbindungen](xref:mvc/models/model-binding) verwendet</span><span class="sxs-lookup"><span data-stu-id="05a15-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="05a15-261">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="05a15-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="05a15-262">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="05a15-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="05a15-263">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="05a15-264">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="05a15-265">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="05a15-265">Entity Framework contexts</span></span>

<span data-ttu-id="05a15-266">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="05a15-267">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="05a15-268">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="05a15-269">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="05a15-269">Lifetime and registration options</span></span>

<span data-ttu-id="05a15-270">Ziehen Sie die folgenden Schnittstellen in Erwägung, die Aufgaben als Vorgänge mit einem Bezeichner (`OperationId`) darstellen, um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="05a15-271">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="05a15-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="05a15-272">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="05a15-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="05a15-273">Der `Operation`-Konstruktor generiert die letzten vier Zeichen einer GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="05a15-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="05a15-274">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="05a15-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="05a15-275">Die Beispiel-App zeigt die Objektlebensdauer innerhalb von und zwischen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="05a15-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="05a15-276">Die `IndexModel`-Klasse der Beispiel-App und die Middleware fordern alle verfügbaren `IOperation`-Typen an und protokollieren `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="05a15-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="05a15-277">Die Middleware ähnelt `IndexModel` und löst dieselben Dienste auf:</span><span class="sxs-lookup"><span data-stu-id="05a15-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="05a15-278">Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="05a15-279">Die Protokollierungsausgabe zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="05a15-279">The logger output shows:</span></span>

* <span data-ttu-id="05a15-280">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="05a15-280">*Transient* objects are always different.</span></span> <span data-ttu-id="05a15-281">Der temporäre Wert `OperationId` unterscheidet sich in `IndexModel` und der Middleware.</span><span class="sxs-lookup"><span data-stu-id="05a15-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="05a15-282">*Bereichsbezogene* Objekte sind innerhalb einer Anforderung identisch, aber unterscheiden sich für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="05a15-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="05a15-283">*Singletonobjekte* sind für jede Anforderung identisch.</span><span class="sxs-lookup"><span data-stu-id="05a15-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="05a15-284">Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:</span><span class="sxs-lookup"><span data-stu-id="05a15-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="05a15-285">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="05a15-285">Call services from main</span></span>

<span data-ttu-id="05a15-286">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="05a15-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="05a15-287">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungstasks auszuführen:</span><span class="sxs-lookup"><span data-stu-id="05a15-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="05a15-288">Der vorangehende Code stammt aus dem Abschnitt [Hinzufügen des Seedinginitialisierers](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) im Tutorial zu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="05a15-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="05a15-289">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `IMyDependency` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="05a15-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="05a15-290">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="05a15-290">Scope validation</span></span>

<span data-ttu-id="05a15-291">Wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="05a15-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="05a15-292">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="05a15-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="05a15-293">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="05a15-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="05a15-294">Temporäre Dienste nicht direkt oder indirekt in Singletons oder bereichsbezogene Dienste eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="05a15-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="05a15-295">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="05a15-296">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App, wenn der Anbieter beim Start der App erstellt und beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="05a15-297">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="05a15-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="05a15-298">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer quasi auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="05a15-299">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="05a15-300">Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="05a15-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="05a15-301">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="05a15-301">Request Services</span></span>

<span data-ttu-id="05a15-302">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="05a15-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="05a15-303">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="05a15-304">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="05a15-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="05a15-305">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="05a15-306">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="05a15-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="05a15-307">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="05a15-308">ASP.net Core erstellt einen Bereich pro Anforderung und `RequestServices` macht den bereichsbezogenen Dienstanbieter verfügbar.</span><span class="sxs-lookup"><span data-stu-id="05a15-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="05a15-309">Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="05a15-310">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="05a15-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="05a15-311">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-311">Design services for dependency injection</span></span>

<span data-ttu-id="05a15-312">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="05a15-312">Best practices are to:</span></span>

* <span data-ttu-id="05a15-313">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="05a15-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="05a15-314">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="05a15-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="05a15-315">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="05a15-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="05a15-316">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="05a15-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="05a15-317">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="05a15-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="05a15-318">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="05a15-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="05a15-319">Wenn in eine Klasse zu viele Abhängigkeiten eingefügt wurden, bedeutet das, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt.</span><span class="sxs-lookup"><span data-stu-id="05a15-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="05a15-320">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="05a15-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="05a15-321">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="05a15-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="05a15-322">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="05a15-322">Disposal of services</span></span>

<span data-ttu-id="05a15-323">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="05a15-324">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="05a15-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="05a15-325">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="05a15-326">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="05a15-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="05a15-327">In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="05a15-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="05a15-328">Nicht vom Dienstcontainer erstellte Dienste</span><span class="sxs-lookup"><span data-stu-id="05a15-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="05a15-329">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="05a15-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="05a15-330">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="05a15-330">In the preceding code:</span></span>

* <span data-ttu-id="05a15-331">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="05a15-332">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="05a15-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="05a15-333">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="05a15-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="05a15-334">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="05a15-335">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="05a15-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="05a15-336">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-336">Transient, limited lifetime</span></span>

<span data-ttu-id="05a15-337">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="05a15-337">**Scenario**</span></span>

<span data-ttu-id="05a15-338">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="05a15-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="05a15-339">Die Instanz wird im Stammbereich (Stammcontainer) aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="05a15-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="05a15-340">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="05a15-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="05a15-341">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="05a15-341">**Solution**</span></span>

<span data-ttu-id="05a15-342">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="05a15-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="05a15-343">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="05a15-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="05a15-344">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="05a15-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="05a15-345">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="05a15-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="05a15-346">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="05a15-347">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="05a15-348">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="05a15-348">**Scenario**</span></span>

<span data-ttu-id="05a15-349">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="05a15-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="05a15-350">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="05a15-350">**Solution**</span></span>

<span data-ttu-id="05a15-351">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="05a15-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="05a15-352">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="05a15-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="05a15-353">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="05a15-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="05a15-354">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="05a15-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="05a15-355">Allgemeine Richtlinien für IDisposable</span><span class="sxs-lookup"><span data-stu-id="05a15-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="05a15-356">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="05a15-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="05a15-357">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="05a15-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="05a15-358">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="05a15-359">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="05a15-360">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="05a15-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="05a15-361">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="05a15-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="05a15-362">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="05a15-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="05a15-363">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="05a15-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="05a15-364">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="05a15-364">Default service container replacement</span></span>

<span data-ttu-id="05a15-365">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="05a15-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="05a15-366">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="05a15-367">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-367">Property injection</span></span>
* <span data-ttu-id="05a15-368">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="05a15-368">Injection based on name</span></span>
* <span data-ttu-id="05a15-369">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="05a15-369">Child containers</span></span>
* <span data-ttu-id="05a15-370">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-370">Custom lifetime management</span></span>
* <span data-ttu-id="05a15-371">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="05a15-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="05a15-372">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="05a15-372">Convention-based registration</span></span>

<span data-ttu-id="05a15-373">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="05a15-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="05a15-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="05a15-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="05a15-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="05a15-376">Grace</span><span class="sxs-lookup"><span data-stu-id="05a15-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="05a15-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="05a15-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="05a15-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="05a15-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="05a15-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="05a15-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="05a15-380">Unity</span><span class="sxs-lookup"><span data-stu-id="05a15-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="05a15-381">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="05a15-381">Thread safety</span></span>

<span data-ttu-id="05a15-382">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="05a15-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="05a15-383">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="05a15-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="05a15-384">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="05a15-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="05a15-385">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="05a15-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="05a15-386">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="05a15-386">Recommendations</span></span>

* <span data-ttu-id="05a15-387">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="05a15-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="05a15-388">C# unterstützt keine asynchronen Konstruktoren.</span><span class="sxs-lookup"><span data-stu-id="05a15-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="05a15-389">Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="05a15-390">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="05a15-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="05a15-391">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="05a15-392">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="05a15-393">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="05a15-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="05a15-394">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="05a15-395">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="05a15-395">Avoid static access to services.</span></span> <span data-ttu-id="05a15-396">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="05a15-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="05a15-397">DI-Factorys sollten schnell und synchron sein.</span><span class="sxs-lookup"><span data-stu-id="05a15-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="05a15-398">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="05a15-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="05a15-399">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="05a15-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="05a15-400">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="05a15-400">**Incorrect:**</span></span>

    ![Falscher Code](dependency-injection/_static/bad.png)

  <span data-ttu-id="05a15-402">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="05a15-402">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="05a15-403">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="05a15-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="05a15-404">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="05a15-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="05a15-405">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="05a15-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="05a15-406">Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="05a15-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="05a15-407">`BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte.</span><span class="sxs-lookup"><span data-stu-id="05a15-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="05a15-408">Nehmen Sie beispielsweise an, dass Sie `LoginPath` aus der Konfiguration abrufen müssen.</span><span class="sxs-lookup"><span data-stu-id="05a15-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="05a15-409">Vermeiden Sie den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="05a15-409">Avoid the following code:</span></span>

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="05a15-411">Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="05a15-412">ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="05a15-413">Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.</span><span class="sxs-lookup"><span data-stu-id="05a15-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="05a15-414">Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="05a15-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="05a15-415">Sie sollten `LoginPath` über das DI-Optionsmuster abrufen:</span><span class="sxs-lookup"><span data-stu-id="05a15-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="05a15-416">Löschbare temporäre Dienste werden vom Container für die Löschung erfasst.</span><span class="sxs-lookup"><span data-stu-id="05a15-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="05a15-417">Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="05a15-418">Aktivieren Sie die Bereichsüberprüfung, damit die App keine bereichsbezogenen Dienste aufweist, die Singletons erfassen.</span><span class="sxs-lookup"><span data-stu-id="05a15-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="05a15-419">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="05a15-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="05a15-420">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="05a15-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="05a15-421">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="05a15-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="05a15-422">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="05a15-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="05a15-423">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="05a15-424">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="05a15-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="05a15-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) bietet die Mehrinstanzenfähigkeit.</span><span class="sxs-lookup"><span data-stu-id="05a15-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="05a15-426">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="05a15-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="05a15-427">Beispiele und Vorgehensweisen zum Erstellen modularer Apps und mehrinstanzenfähigen Apps nur mit dem Orchard Core-Framework aber ohne CMS-spezifische Features finden Sie in den Beispiel-Apps unter https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="05a15-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="05a15-428">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="05a15-428">Framework-provided services</span></span>

<span data-ttu-id="05a15-429">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="05a15-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="05a15-430">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="05a15-431">Apps, die auf einem ASP.NET Core-Vorlagen basieren, enthalten mehr als 250 Dienste, die vom Framework registriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="05a15-432">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="05a15-433">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="05a15-433">Service Type</span></span> | <span data-ttu-id="05a15-434">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="05a15-435">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="05a15-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="05a15-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="05a15-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="05a15-439">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="05a15-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="05a15-441">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="05a15-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="05a15-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="05a15-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="05a15-445">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="05a15-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="05a15-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="05a15-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="05a15-449">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="05a15-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="05a15-450">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05a15-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="05a15-451">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="05a15-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="05a15-452">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="05a15-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="05a15-453">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="05a15-453">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="05a15-454">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="05a15-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="05a15-455">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="05a15-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="05a15-456">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="05a15-457">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="05a15-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="05a15-458">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="05a15-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="05a15-459">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-459">Overview of dependency injection</span></span>

<span data-ttu-id="05a15-460">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="05a15-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="05a15-461">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="05a15-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="05a15-462">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="05a15-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="05a15-463">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="05a15-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="05a15-464">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="05a15-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="05a15-465">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="05a15-466">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="05a15-467">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="05a15-468">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="05a15-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="05a15-469">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="05a15-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="05a15-470">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="05a15-471">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="05a15-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="05a15-472">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="05a15-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="05a15-473">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="05a15-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="05a15-474">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="05a15-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="05a15-475">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="05a15-476">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="05a15-477">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="05a15-478">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="05a15-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="05a15-479">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="05a15-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="05a15-480">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="05a15-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="05a15-481">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="05a15-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="05a15-482">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="05a15-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="05a15-483">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="05a15-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="05a15-484">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="05a15-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="05a15-485">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="05a15-486">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="05a15-487">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="05a15-488">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="05a15-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="05a15-489">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="05a15-490">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="05a15-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="05a15-491">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="05a15-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="05a15-492">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="05a15-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="05a15-493">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="05a15-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="05a15-494">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="05a15-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="05a15-495">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="05a15-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="05a15-496">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="05a15-497">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="05a15-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="05a15-498">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="05a15-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="05a15-499">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="05a15-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="05a15-500">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="05a15-500">Services injected into Startup</span></span>

<span data-ttu-id="05a15-501">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="05a15-502">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="05a15-503">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="05a15-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="05a15-504">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="05a15-504">Framework-provided services</span></span>

<span data-ttu-id="05a15-505">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="05a15-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="05a15-506">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="05a15-507">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="05a15-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="05a15-508">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="05a15-509">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="05a15-509">Service Type</span></span> | <span data-ttu-id="05a15-510">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="05a15-511">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="05a15-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="05a15-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="05a15-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="05a15-515">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="05a15-516">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="05a15-517">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="05a15-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="05a15-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="05a15-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="05a15-521">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="05a15-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="05a15-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="05a15-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="05a15-525">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="05a15-525">Register additional services with extension methods</span></span>

<span data-ttu-id="05a15-526">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="05a15-527">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="05a15-528">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="05a15-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="05a15-529">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-529">Service lifetimes</span></span>

<span data-ttu-id="05a15-530">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="05a15-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="05a15-531">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="05a15-532">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="05a15-532">Transient</span></span>

<span data-ttu-id="05a15-533">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="05a15-534">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="05a15-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="05a15-535">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="05a15-536">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="05a15-536">Scoped</span></span>

<span data-ttu-id="05a15-537">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="05a15-538">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="05a15-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="05a15-539">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="05a15-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="05a15-540">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="05a15-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="05a15-541">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="05a15-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="05a15-542">Singleton</span><span class="sxs-lookup"><span data-stu-id="05a15-542">Singleton</span></span>

<span data-ttu-id="05a15-543">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="05a15-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="05a15-544">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="05a15-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="05a15-545">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="05a15-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="05a15-546">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="05a15-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="05a15-547">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="05a15-548">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="05a15-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="05a15-549">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="05a15-550">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="05a15-550">Service registration methods</span></span>

<span data-ttu-id="05a15-551">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="05a15-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="05a15-552">Methode</span><span class="sxs-lookup"><span data-stu-id="05a15-552">Method</span></span> | <span data-ttu-id="05a15-553">Automatische</span><span class="sxs-lookup"><span data-stu-id="05a15-553">Automatic</span></span><br><span data-ttu-id="05a15-554">Objekt</span><span class="sxs-lookup"><span data-stu-id="05a15-554">object</span></span><br><span data-ttu-id="05a15-555">bereinigung</span><span class="sxs-lookup"><span data-stu-id="05a15-555">disposal</span></span> | <span data-ttu-id="05a15-556">Mehrere</span><span class="sxs-lookup"><span data-stu-id="05a15-556">Multiple</span></span><br><span data-ttu-id="05a15-557">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="05a15-557">implementations</span></span> | <span data-ttu-id="05a15-558">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="05a15-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="05a15-559">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="05a15-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="05a15-560">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-560">Yes</span></span> | <span data-ttu-id="05a15-561">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-561">Yes</span></span> | <span data-ttu-id="05a15-562">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-563">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="05a15-564">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-564">Yes</span></span> | <span data-ttu-id="05a15-565">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-565">Yes</span></span> | <span data-ttu-id="05a15-566">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="05a15-567">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="05a15-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="05a15-568">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-568">Yes</span></span> | <span data-ttu-id="05a15-569">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-569">No</span></span> | <span data-ttu-id="05a15-570">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-571">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="05a15-572">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-572">No</span></span> | <span data-ttu-id="05a15-573">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-573">Yes</span></span> | <span data-ttu-id="05a15-574">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="05a15-575">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="05a15-576">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-576">No</span></span> | <span data-ttu-id="05a15-577">Nein</span><span class="sxs-lookup"><span data-stu-id="05a15-577">No</span></span> | <span data-ttu-id="05a15-578">Ja</span><span class="sxs-lookup"><span data-stu-id="05a15-578">Yes</span></span> |

<span data-ttu-id="05a15-579">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="05a15-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="05a15-580">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="05a15-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="05a15-581">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="05a15-582">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="05a15-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="05a15-583">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="05a15-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="05a15-584">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="05a15-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="05a15-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="05a15-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="05a15-586">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="05a15-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="05a15-587">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="05a15-588">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="05a15-589">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="05a15-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="05a15-590">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="05a15-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="05a15-591">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="05a15-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="05a15-592">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="05a15-592">Constructor injection behavior</span></span>

<span data-ttu-id="05a15-593">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="05a15-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="05a15-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="05a15-595">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="05a15-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="05a15-596">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="05a15-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="05a15-597">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="05a15-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="05a15-598">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="05a15-599">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="05a15-600">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="05a15-600">Entity Framework contexts</span></span>

<span data-ttu-id="05a15-601">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="05a15-602">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="05a15-603">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="05a15-604">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="05a15-604">Lifetime and registration options</span></span>

<span data-ttu-id="05a15-605">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="05a15-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="05a15-606">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="05a15-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="05a15-607">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="05a15-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="05a15-608">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="05a15-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="05a15-609">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="05a15-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="05a15-610">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="05a15-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="05a15-611">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="05a15-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="05a15-612">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="05a15-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="05a15-613">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="05a15-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="05a15-614">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="05a15-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="05a15-615">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="05a15-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="05a15-616">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="05a15-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="05a15-617">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="05a15-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="05a15-618">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="05a15-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="05a15-619">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="05a15-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="05a15-620">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="05a15-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="05a15-621">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="05a15-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="05a15-622">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="05a15-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="05a15-623">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="05a15-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="05a15-624">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="05a15-624">**First request:**</span></span>

<span data-ttu-id="05a15-625">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="05a15-625">Controller operations:</span></span>

<span data-ttu-id="05a15-626">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="05a15-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="05a15-627">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05a15-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05a15-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05a15-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05a15-629">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="05a15-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05a15-630">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="05a15-630">`OperationService` operations:</span></span>

<span data-ttu-id="05a15-631">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="05a15-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="05a15-632">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="05a15-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="05a15-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05a15-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05a15-634">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="05a15-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05a15-635">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="05a15-635">**Second request:**</span></span>

<span data-ttu-id="05a15-636">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="05a15-636">Controller operations:</span></span>

<span data-ttu-id="05a15-637">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="05a15-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="05a15-638">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05a15-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05a15-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05a15-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05a15-640">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="05a15-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05a15-641">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="05a15-641">`OperationService` operations:</span></span>

<span data-ttu-id="05a15-642">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="05a15-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="05a15-643">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="05a15-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="05a15-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="05a15-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="05a15-645">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="05a15-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="05a15-646">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="05a15-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="05a15-647">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="05a15-647">*Transient* objects are always different.</span></span> <span data-ttu-id="05a15-648">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="05a15-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="05a15-649">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="05a15-650">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="05a15-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="05a15-651">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="05a15-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="05a15-652">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="05a15-652">Call services from main</span></span>

<span data-ttu-id="05a15-653">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="05a15-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="05a15-654">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="05a15-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="05a15-655">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="05a15-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="05a15-656">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="05a15-656">Scope validation</span></span>

<span data-ttu-id="05a15-657">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="05a15-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="05a15-658">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="05a15-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="05a15-659">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="05a15-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="05a15-660">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="05a15-661">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="05a15-662">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="05a15-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="05a15-663">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="05a15-664">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="05a15-665">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="05a15-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="05a15-666">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="05a15-666">Request Services</span></span>

<span data-ttu-id="05a15-667">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="05a15-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="05a15-668">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="05a15-669">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="05a15-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="05a15-670">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="05a15-671">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="05a15-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="05a15-672">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="05a15-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="05a15-673">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="05a15-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="05a15-674">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-674">Design services for dependency injection</span></span>

<span data-ttu-id="05a15-675">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="05a15-675">Best practices are to:</span></span>

* <span data-ttu-id="05a15-676">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="05a15-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="05a15-677">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="05a15-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="05a15-678">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="05a15-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="05a15-679">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="05a15-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="05a15-680">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="05a15-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="05a15-681">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="05a15-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="05a15-682">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="05a15-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="05a15-683">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="05a15-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="05a15-684">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="05a15-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="05a15-685">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="05a15-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="05a15-686">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="05a15-686">Disposal of services</span></span>

<span data-ttu-id="05a15-687">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="05a15-688">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="05a15-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="05a15-689">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="05a15-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="05a15-690">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="05a15-690">In the following example:</span></span>

* <span data-ttu-id="05a15-691">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="05a15-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="05a15-692">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="05a15-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="05a15-693">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="05a15-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="05a15-694">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="05a15-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="05a15-695">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="05a15-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="05a15-696">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-696">Transient, limited lifetime</span></span>

<span data-ttu-id="05a15-697">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="05a15-697">**Scenario**</span></span>

<span data-ttu-id="05a15-698">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="05a15-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="05a15-699">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="05a15-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="05a15-700">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="05a15-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="05a15-701">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="05a15-701">**Solution**</span></span>

<span data-ttu-id="05a15-702">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="05a15-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="05a15-703">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="05a15-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="05a15-704">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="05a15-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="05a15-705">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="05a15-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="05a15-706">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="05a15-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="05a15-707">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="05a15-708">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="05a15-708">**Scenario**</span></span>

<span data-ttu-id="05a15-709">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="05a15-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="05a15-710">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="05a15-710">**Solution**</span></span>

<span data-ttu-id="05a15-711">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="05a15-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="05a15-712">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="05a15-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="05a15-713">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="05a15-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="05a15-714">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="05a15-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="05a15-715">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="05a15-715">General Guidelines</span></span>

* <span data-ttu-id="05a15-716">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="05a15-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="05a15-717">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="05a15-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="05a15-718">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="05a15-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="05a15-719">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="05a15-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="05a15-720">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="05a15-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="05a15-721">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="05a15-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="05a15-722">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="05a15-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="05a15-723">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="05a15-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="05a15-724">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="05a15-724">Default service container replacement</span></span>

<span data-ttu-id="05a15-725">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="05a15-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="05a15-726">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="05a15-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="05a15-727">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="05a15-727">Property injection</span></span>
* <span data-ttu-id="05a15-728">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="05a15-728">Injection based on name</span></span>
* <span data-ttu-id="05a15-729">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="05a15-729">Child containers</span></span>
* <span data-ttu-id="05a15-730">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="05a15-730">Custom lifetime management</span></span>
* <span data-ttu-id="05a15-731">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="05a15-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="05a15-732">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="05a15-732">Convention-based registration</span></span>

<span data-ttu-id="05a15-733">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="05a15-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="05a15-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="05a15-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="05a15-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="05a15-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="05a15-736">Grace</span><span class="sxs-lookup"><span data-stu-id="05a15-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="05a15-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="05a15-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="05a15-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="05a15-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="05a15-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="05a15-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="05a15-740">Unity</span><span class="sxs-lookup"><span data-stu-id="05a15-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="05a15-741">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="05a15-741">Thread safety</span></span>

<span data-ttu-id="05a15-742">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="05a15-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="05a15-743">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="05a15-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="05a15-744">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="05a15-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="05a15-745">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="05a15-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="05a15-746">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="05a15-746">Recommendations</span></span>

* <span data-ttu-id="05a15-747">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="05a15-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="05a15-748">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="05a15-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="05a15-749">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="05a15-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="05a15-750">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="05a15-751">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="05a15-752">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="05a15-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="05a15-753">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="05a15-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="05a15-754">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="05a15-754">Avoid static access to services.</span></span> <span data-ttu-id="05a15-755">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="05a15-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="05a15-756">Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="05a15-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="05a15-757">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="05a15-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="05a15-758">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="05a15-758">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="05a15-759">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="05a15-759">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="05a15-760">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="05a15-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="05a15-761">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="05a15-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="05a15-762">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="05a15-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="05a15-763">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="05a15-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="05a15-764">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="05a15-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="05a15-765">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="05a15-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05a15-766">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="05a15-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="05a15-767">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05a15-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="05a15-768">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="05a15-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="05a15-769">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="05a15-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="05a15-770">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="05a15-770">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="05a15-771">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="05a15-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
