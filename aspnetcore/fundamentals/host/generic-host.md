---
title: Generischer .NET-Host
author: rick-anderson
description: Erfahren Sie mehr über den generischen Host in .NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 5a2d39af6c921323ae9113fd4aca27dcdedd44a5
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793471"
---
# <a name="net-generic-host"></a><span data-ttu-id="4b5a3-103">Generischer .NET-Host</span><span class="sxs-lookup"><span data-stu-id="4b5a3-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="4b5a3-104">Die ASP.NET Core-Vorlagen erstellen einen generischen .NET Core-Host: <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="4b5a3-105">Hostdefinition</span><span class="sxs-lookup"><span data-stu-id="4b5a3-105">Host definition</span></span>

<span data-ttu-id="4b5a3-106">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4b5a3-107">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="4b5a3-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="4b5a3-108">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-108">Logging</span></span>
* <span data-ttu-id="4b5a3-109">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-109">Configuration</span></span>
* <span data-ttu-id="4b5a3-110">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-110">`IHostedService` implementations</span></span>

<span data-ttu-id="4b5a3-111">Beim Starten eines Hosts wird <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> aufgerufen, die in der Sammlung gehosteter Dienste des Dienstcontainers registriert ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-111">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4b5a3-112">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4b5a3-113">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4b5a3-114">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-114">Set up a host</span></span>

<span data-ttu-id="4b5a3-115">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4b5a3-116">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-116">The `Main` method:</span></span>

* <span data-ttu-id="4b5a3-117">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4b5a3-118">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4b5a3-119">Die ASP.NET Core-Webvorlagen generieren den folgenden Code zum Erstellen eines generischen Hosts:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="4b5a3-120">Der folgende Code erstellt einen generischen Host mithilfe einer Nicht-HTTP-Workload.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="4b5a3-121">Die `IHostedService`-Implementierung wird dem DI-Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4b5a3-122">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4b5a3-123">Der voranstehende Code wird von den ASP.NET Core-Vorlagen generiert.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="4b5a3-124">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4b5a3-125">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4b5a3-126">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4b5a3-127">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="4b5a3-127">Default builder settings</span></span>

<span data-ttu-id="4b5a3-128">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4b5a3-129">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4b5a3-130">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="4b5a3-131">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4b5a3-132">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-132">Command-line arguments.</span></span>
* <span data-ttu-id="4b5a3-133">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="4b5a3-134">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4b5a3-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="4b5a3-135">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="4b5a3-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4b5a3-136">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="4b5a3-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4b5a3-137">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-137">Environment variables.</span></span>
  * <span data-ttu-id="4b5a3-138">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-138">Command-line arguments.</span></span>
* <span data-ttu-id="4b5a3-139">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4b5a3-140">Konsole</span><span class="sxs-lookup"><span data-stu-id="4b5a3-140">Console</span></span>
  * <span data-ttu-id="4b5a3-141">Debug</span><span class="sxs-lookup"><span data-stu-id="4b5a3-141">Debug</span></span>
  * <span data-ttu-id="4b5a3-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="4b5a3-142">EventSource</span></span>
  * <span data-ttu-id="4b5a3-143">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4b5a3-144">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4b5a3-145">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4b5a3-146">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4b5a3-147">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4b5a3-148">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4b5a3-149">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4b5a3-150">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4b5a3-151">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-151">Enables IIS integration.</span></span> <span data-ttu-id="4b5a3-152">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4b5a3-153">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4b5a3-154">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="4b5a3-154">Framework-provided services</span></span>

<span data-ttu-id="4b5a3-155">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4b5a3-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4b5a3-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4b5a3-158">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4b5a3-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4b5a3-159">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4b5a3-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="4b5a3-161">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4b5a3-162">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4b5a3-163">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4b5a3-164">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4b5a3-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-165">IHostLifetime</span></span>

<span data-ttu-id="4b5a3-166">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4b5a3-167">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-167">The last implementation registered is used.</span></span>

<span data-ttu-id="4b5a3-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4b5a3-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4b5a3-170">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4b5a3-171">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4b5a3-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4b5a3-172">IHostEnvironment</span></span>

<span data-ttu-id="4b5a3-173">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4b5a3-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4b5a3-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4b5a3-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4b5a3-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4b5a3-177">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4b5a3-178">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-178">Host configuration</span></span>

<span data-ttu-id="4b5a3-179">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4b5a3-180">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4b5a3-181">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4b5a3-182">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4b5a3-183">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-184">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4b5a3-185">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4b5a3-186">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4b5a3-187">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4b5a3-188">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4b5a3-189">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4b5a3-190">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-190">App configuration</span></span>

<span data-ttu-id="4b5a3-191">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4b5a3-192">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-193">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4b5a3-194">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4b5a3-195">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4b5a3-196">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4b5a3-197">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-197">Settings for all app types</span></span>

<span data-ttu-id="4b5a3-198">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4b5a3-199">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4b5a3-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-200">ApplicationName</span></span>

<span data-ttu-id="4b5a3-201">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4b5a3-202">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4b5a3-203">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-203">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-204">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4b5a3-205">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4b5a3-206">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4b5a3-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-207">ContentRoot</span></span>

<span data-ttu-id="4b5a3-208">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4b5a3-209">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4b5a3-210">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4b5a3-211">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-211">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-212">**Standard**: Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4b5a3-213">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4b5a3-214">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4b5a3-215">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-215">For more information, see:</span></span>

* [<span data-ttu-id="4b5a3-216">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4b5a3-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4b5a3-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4b5a3-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-218">EnvironmentName</span></span>

<span data-ttu-id="4b5a3-219">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4b5a3-220">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4b5a3-221">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4b5a3-222">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-222">**Key**: `environment`</span></span>  
<span data-ttu-id="4b5a3-223">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-223">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-224">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-224">**Default**: `Production`</span></span>  
<span data-ttu-id="4b5a3-225">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4b5a3-226">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4b5a3-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4b5a3-227">ShutdownTimeout</span></span>

<span data-ttu-id="4b5a3-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4b5a3-229">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-229">The default value is five seconds.</span></span>  <span data-ttu-id="4b5a3-230">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="4b5a3-231">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4b5a3-232">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4b5a3-233">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4b5a3-234">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4b5a3-235">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4b5a3-236">**Schlüssel**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4b5a3-237">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-237">**Type**: `int`</span></span>  
<span data-ttu-id="4b5a3-238">**Standard**: 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="4b5a3-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4b5a3-239">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4b5a3-240">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4b5a3-241">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="4b5a3-242">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="4b5a3-242">Settings for web apps</span></span>

<span data-ttu-id="4b5a3-243">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4b5a3-244">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4b5a3-245">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4b5a3-246">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4b5a3-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4b5a3-247">CaptureStartupErrors</span></span>

<span data-ttu-id="4b5a3-248">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4b5a3-249">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4b5a3-250">**Schlüssel**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4b5a3-251">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-252">**Standard**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4b5a3-253">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4b5a3-254">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4b5a3-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4b5a3-255">DetailedErrors</span></span>

<span data-ttu-id="4b5a3-256">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4b5a3-257">**Schlüssel**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4b5a3-258">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-259">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-259">**Default**: `false`</span></span>  
<span data-ttu-id="4b5a3-260">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4b5a3-261">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4b5a3-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4b5a3-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="4b5a3-263">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4b5a3-264">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4b5a3-265">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4b5a3-266">**Schlüssel**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4b5a3-267">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-267">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-268">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4b5a3-268">**Default**: Empty string</span></span>  
<span data-ttu-id="4b5a3-269">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4b5a3-270">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4b5a3-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4b5a3-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4b5a3-272">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4b5a3-273">**Schlüssel**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4b5a3-274">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-274">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-275">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4b5a3-275">**Default**: Empty string</span></span>  
<span data-ttu-id="4b5a3-276">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4b5a3-277">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4b5a3-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4b5a3-278">HTTPS_Port</span></span>

<span data-ttu-id="4b5a3-279">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-279">The HTTPS redirect port.</span></span> <span data-ttu-id="4b5a3-280">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4b5a3-281">**Schlüssel**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="4b5a3-282">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-282">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-283">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4b5a3-284">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4b5a3-285">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4b5a3-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4b5a3-286">PreferHostingUrls</span></span>

<span data-ttu-id="4b5a3-287">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4b5a3-288">**Schlüssel**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4b5a3-289">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-290">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-290">**Default**: `true`</span></span>  
<span data-ttu-id="4b5a3-291">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4b5a3-292">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4b5a3-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4b5a3-293">PreventHostingStartup</span></span>

<span data-ttu-id="4b5a3-294">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4b5a3-295">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4b5a3-296">**Schlüssel**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4b5a3-297">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-298">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-298">**Default**: `false`</span></span>  
<span data-ttu-id="4b5a3-299">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4b5a3-300">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4b5a3-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4b5a3-301">StartupAssembly</span></span>

<span data-ttu-id="4b5a3-302">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4b5a3-303">**Schlüssel**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4b5a3-304">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-304">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-305">**Standard**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="4b5a3-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4b5a3-306">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4b5a3-307">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4b5a3-308">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4b5a3-309">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4b5a3-310">URLs</span><span class="sxs-lookup"><span data-stu-id="4b5a3-310">URLs</span></span>

<span data-ttu-id="4b5a3-311">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4b5a3-312">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4b5a3-313">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4b5a3-314">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4b5a3-315">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="4b5a3-316">**Schlüssel**: `urls`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-316">**Key**: `urls`</span></span>  
<span data-ttu-id="4b5a3-317">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-317">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-318">**Standard**: `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4b5a3-319">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4b5a3-320">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4b5a3-321">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4b5a3-322">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4b5a3-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-323">WebRoot</span></span>

<span data-ttu-id="4b5a3-324">Die [IWebHostEnvironment.WebRootPath-](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)-Eigenschaft bestimmt den relativen Pfad zu den statischen Objekten der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4b5a3-325">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4b5a3-326">**Schlüssel**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="4b5a3-327">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-327">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-328">**Standard**: Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4b5a3-329">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4b5a3-330">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4b5a3-331">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` für `IWebHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4b5a3-332">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-332">For more information, see:</span></span>

* [<span data-ttu-id="4b5a3-333">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4b5a3-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4b5a3-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4b5a3-335">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-335">Manage the host lifetime</span></span>

<span data-ttu-id="4b5a3-336">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4b5a3-337">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4b5a3-338">Run</span><span class="sxs-lookup"><span data-stu-id="4b5a3-338">Run</span></span>

<span data-ttu-id="4b5a3-339">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4b5a3-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-340">RunAsync</span></span>

<span data-ttu-id="4b5a3-341">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4b5a3-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-342">RunConsoleAsync</span></span>

<span data-ttu-id="4b5a3-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4b5a3-344">Starten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-344">Start</span></span>

<span data-ttu-id="4b5a3-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4b5a3-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-346">StartAsync</span></span>

<span data-ttu-id="4b5a3-347">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4b5a3-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4b5a3-349">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4b5a3-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-350">StopAsync</span></span>

<span data-ttu-id="4b5a3-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4b5a3-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4b5a3-352">WaitForShutdown</span></span>

<span data-ttu-id="4b5a3-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4b5a3-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="4b5a3-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4b5a3-356">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-356">External control</span></span>

<span data-ttu-id="4b5a3-357">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b5a3-358">Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="4b5a3-359">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4b5a3-360">In diesem Artikel wird der generische ASP.NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert, der für das Hosten von Apps verwendet wird, die keine HTTP-Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="4b5a3-361">Das Ziel des generischen Hosts besteht darin, die HTTP-Pipeline von der Webhost-API zu entkoppeln, um mehr Hostszenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="4b5a3-362">Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads, die auf dem generischen Host basieren, profitieren von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="4b5a3-363">Der generische Host ist neu in ASP.NET Core 2.1 und ist nicht für Webhostingszenarios geeignet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="4b5a3-364">Verwenden Sie den [Webhost](xref:fundamentals/host/web-host) für Webhostingszenarios.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4b5a3-365">Der generische Host wird den Webhost in einem zukünftigen Release ersetzen und als primäre Host-API in HTTP- und Nicht-HTTP-Szenarios fungieren.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="4b5a3-366">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4b5a3-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4b5a3-367">Wenn Sie die Beispiel-App in [Visual Studio Code](https://code.visualstudio.com/) ausführen, verwenden Sie ein *externes oder integriertes Terminal*.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="4b5a3-368">Führen Sie das Beispiel nicht in `internalConsole` aus.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="4b5a3-369">So legen Sie die Konsole in Visual Studio Code fest:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="4b5a3-370">Öffnen Sie die Datei *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="4b5a3-371">Suchen Sie in der Konfiguration **.NET Core-Start (Konsole)** den Eintrag **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="4b5a3-372">Legen Sie den Wert auf `externalTerminal` oder `integratedTerminal` fest.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="4b5a3-373">Einführung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-373">Introduction</span></span>

<span data-ttu-id="4b5a3-374">Die generische Hostbibliothek ist im Namespace <xref:Microsoft.Extensions.Hosting> verfügbar und wird vom Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="4b5a3-375">Das Paket `Microsoft.Extensions.Hosting` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="4b5a3-376"><xref:Microsoft.Extensions.Hosting.IHostedService> ist der Einstiegspunkt für die Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="4b5a3-377">Jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> wird in der Reihenfolge der [Dienstregistrierung in ConfigureServices](#configureservices) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="4b5a3-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> wird in jeder <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle aufgerufen, wenn der Host gestartet wird, und <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> wird in umgekehrter Registrierungsreihenfolge aufgerufen, wenn der Host ordnungsgemäß heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4b5a3-379">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-379">Set up a host</span></span>

<span data-ttu-id="4b5a3-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> ist die Hauptkomponente, die Bibliotheken und Apps für die Initialisierung, Erstellung und Ausführung des Hosts verwenden:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="4b5a3-381">Optionen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-381">Options</span></span>

<span data-ttu-id="4b5a3-382"><xref:Microsoft.Extensions.Hosting.HostOptions>-Konfigurationsoptionen für <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4b5a3-383">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="4b5a3-383">Shutdown timeout</span></span>

<span data-ttu-id="4b5a3-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> legt den Timeout für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4b5a3-385">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-385">The default value is five seconds.</span></span>

<span data-ttu-id="4b5a3-386">Die folgende Optionskonfiguration in `Program.Main` erhöht den standardmäßigen Timeout beim Herunterfahren von 5 Sekunden auf 20 Sekunden:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="4b5a3-387">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="4b5a3-387">Default services</span></span>

<span data-ttu-id="4b5a3-388">Die folgenden Dienste werden bei der Hostinitialisierung registriert:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="4b5a3-389">[Umgebung](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="4b5a3-390">[Konfiguration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="4b5a3-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="4b5a3-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="4b5a3-393">[Optionen](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="4b5a3-394">[Protokollierung](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4b5a3-395">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-395">Host configuration</span></span>

<span data-ttu-id="4b5a3-396">Die Konfiguration des Hosts wird durch Folgendes erstellt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-396">Host configuration is created by:</span></span>

* <span data-ttu-id="4b5a3-397">Aufrufen von Erweiterungsmethoden in <xref:Microsoft.Extensions.Hosting.IHostBuilder>, um das [Inhaltsstammverzeichnis](#content-root) und die [Umgebung](#environment) festzulegen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="4b5a3-398">Lesen der Konfiguration von Konfigurationsanbietern in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*></span><span class="sxs-lookup"><span data-stu-id="4b5a3-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="4b5a3-399">Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="4b5a3-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4b5a3-400">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-400">Application key (name)</span></span>

<span data-ttu-id="4b5a3-401">Die Eigenschaft [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hostkonstruktion festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="4b5a3-402">Um den Wert explizit festzulegen, verwenden Sie den [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="4b5a3-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="4b5a3-403">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4b5a3-404">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-404">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-405">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4b5a3-406">**Festlegen mit:** `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="4b5a3-407">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4b5a3-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="4b5a3-408">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4b5a3-408">Content root</span></span>

<span data-ttu-id="4b5a3-409">Diese Einstellung bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="4b5a3-410">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4b5a3-411">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-411">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-412">**Standard**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4b5a3-413">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4b5a3-414">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4b5a3-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4b5a3-415">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="4b5a3-416">Weitere Informationen finden Sie unter [Grundlagen: Inhaltsstammverzeichnis](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="4b5a3-417">Umgebung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-417">Environment</span></span>

<span data-ttu-id="4b5a3-418">Legt die [Umgebung](xref:fundamentals/environments) der App fest.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4b5a3-419">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-419">**Key**: `environment`</span></span>  
<span data-ttu-id="4b5a3-420">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-420">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-421">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-421">**Default**: `Production`</span></span>  
<span data-ttu-id="4b5a3-422">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4b5a3-423">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="4b5a3-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4b5a3-424">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-424">The environment can be set to any value.</span></span> <span data-ttu-id="4b5a3-425">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4b5a3-426">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="4b5a3-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="4b5a3-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für den Host zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="4b5a3-429">Die Hostkonfiguration dient zum Initialisieren der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> für die Verwendung im App-Buildprozesses.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="4b5a3-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-431">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4b5a3-432">Standardmäßig sind keine Anbieter enthalten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-432">No providers are included by default.</span></span> <span data-ttu-id="4b5a3-433">Sie müssen explizit angeben, welche Konfigurationsanbieter die App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> erfordert, einschließlich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="4b5a3-434">Dateikonfiguration (z.B. von einer Datei namens *hostsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="4b5a3-435">Konfiguration von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-435">Environment variable configuration.</span></span>
* <span data-ttu-id="4b5a3-436">Konfiguration von Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="4b5a3-437">Alle anderen erforderlichen Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4b5a3-437">Any other required configuration providers.</span></span>

<span data-ttu-id="4b5a3-438">Die Dateikonfiguration des Hosts erfolgt durch Angabe des App-Basispfads mit `SetBasePath` gefolgt von einem Aufruf eines der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="4b5a3-439">Die Beispiel-App verwendet die JSON-Datei *hostsettings.json* und ruft <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> auf, um die Hostkonfigurationseinstellungen der Datei zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="4b5a3-440">Um die [Umgebungsvariablenkonfiguration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) des Hosts hinzuzufügen, rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> im Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="4b5a3-441">`AddEnvironmentVariables` akzeptiert ein optionales benutzerdefiniertes Präfix.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="4b5a3-442">Die Beispiel-App verwendet das Präfix `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="4b5a3-443">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4b5a3-444">Wenn der Host der Beispiel-App konfiguriert wird, wird der Wert der Umgebungsvariable für `PREFIX_ENVIRONMENT` der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4b5a3-445">Wenn Sie [Visual Studio](https://visualstudio.microsoft.com) oder eine App mit `dotnet run` während der Entwicklung verwenden, können Umgebungsvariablen in der Datei *Properties/launchSettings.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="4b5a3-446">In [Visual Studio Code](https://code.visualstudio.com/) können Umgebungsvariablen während der Entwicklung in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="4b5a3-447">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4b5a3-448">Die [Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) wird durch Aufrufen von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="4b5a3-449">Die Befehlszeilenkonfiguration wird zuletzt hinzugefügt, damit Befehlszeilenargumente die Konfiguration überschreiben können, die von früheren Konfigurationsanbietern bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="4b5a3-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="4b5a3-451">Weitere Konfigurationen können durch die Schlüssel [applicationName](#application-key-name) und [contentRoot](#content-root) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="4b5a3-452">Beispielkonfiguration für `HostBuilder` mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="4b5a3-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4b5a3-454">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> in der <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="4b5a3-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="4b5a3-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-457">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="4b5a3-458">Die von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und in <xref:Microsoft.Extensions.Hosting.IHost.Services*> verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="4b5a3-459">Auf die App-Konfiguration wird automatisch die Hostkonfiguration angewendet, die von [ConfigureHostConfiguration](#configurehostconfiguration) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="4b5a3-460">Beispielkonfiguration für die App mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="4b5a3-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="4b5a3-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="4b5a3-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="4b5a3-464">Um Einstellungsdateien in das Ausgabeverzeichnis zu verschieben, geben Sie die Einstellungsdateien als [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) in der Projektdatei an.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="4b5a3-465">Die Beispiel-App verschiebt ihre JSON-App-Einstellungsdateien und *hostsettings.json* mit dem folgenden `<Content>`-Element:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="4b5a3-466">Konfigurationserweiterungsmethoden wie <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> und <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> erfordern zusätzliche NuGet-Pakete, z.B. [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) und [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="4b5a3-467">Wenn die App nicht das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) verwendet, müssen diese Pakete dem Projekt zusätzlich zum [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)-Kernpaket hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="4b5a3-468">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="4b5a3-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="4b5a3-469">ConfigureServices</span></span>

<span data-ttu-id="4b5a3-470">Mithilfe von <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> werden Dienste zum Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4b5a3-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4b5a3-472">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="4b5a3-473">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="4b5a3-474">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Erweiterungsmethode `AddHostedService`, um einen Dienst für Lebensdauerereignisse (`LifetimeEventsHostedService`) und einen zeitgesteuerten Hintergrundtask (`TimedHostedService`) zur App hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="4b5a3-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="4b5a3-475">ConfigureLogging</span></span>

<span data-ttu-id="4b5a3-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> fügt einen Delegaten für die Konfiguration des bereitgestellten <xref:Microsoft.Extensions.Logging.ILoggingBuilder> hinzu.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="4b5a3-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="4b5a3-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-478">UseConsoleLifetime</span></span>

<span data-ttu-id="4b5a3-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="4b5a3-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="4b5a3-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist vorab als Standardimplementierung für die Lebensdauer registriert.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="4b5a3-482">Die letzte registrierte Lebensdauer wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="4b5a3-483">Containerkonfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-483">Container configuration</span></span>

<span data-ttu-id="4b5a3-484">Der Host kann eine <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>-Schnittstelle verwenden, um die Integration in andere Container zu unterstützten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="4b5a3-485">Das Bereitstellen einer Factory ist kein Teil der DI-Containerregistrierung, sondern ein Host, der systemintern verwendet wird, um den entsprechenden DI-Container zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="4b5a3-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) überschreibt die Standardfactory, die zum Erstellen des Dienstanbieters der App verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="4b5a3-487">Die benutzerdefinierte Containerkonfiguration wird von der Methode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="4b5a3-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> stellt eine stark typisierte Möglichkeit für das Konfigurieren des Containers auf Basis der zugrunde liegenden Host-API bereit.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="4b5a3-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4b5a3-490">Erstellen eines Dienstcontainers für die App:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="4b5a3-491">Bereitstellen einer Factory für den Dienstcontainer:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="4b5a3-492">Verwenden Sie die Factory, und konfigurieren Sie den benutzerdefinierten Dienstcontainer für die App:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="4b5a3-493">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-493">Extensibility</span></span>

<span data-ttu-id="4b5a3-494">Die Erweiterung des Hosts wird mit der Erweiterungsmethode in <xref:Microsoft.Extensions.Hosting.IHostBuilder> durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="4b5a3-495">Im folgenden Beispiel wird dargestellt, wie eine Erweiterungsmethode eine <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung mit dem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks)-Beispiel erweitert, das in <xref:fundamentals/host/hosted-services> gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="4b5a3-496">Eine App richtet die `UseHostedService`-Erweiterungsmethode zum Registrieren des in `T` übergebenen gehosteten Diensts ein:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="4b5a3-497">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-497">Manage the host</span></span>

<span data-ttu-id="4b5a3-498">Die Implementierung von <xref:Microsoft.Extensions.Hosting.IHost> ist für das Starten und Beenden der Implementierungen von <xref:Microsoft.Extensions.Hosting.IHostedService> verantwortlich, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4b5a3-499">Run</span><span class="sxs-lookup"><span data-stu-id="4b5a3-499">Run</span></span>

<span data-ttu-id="4b5a3-500">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="4b5a3-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-501">RunAsync</span></span>

<span data-ttu-id="4b5a3-502">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="4b5a3-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-503">RunConsoleAsync</span></span>

<span data-ttu-id="4b5a3-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="4b5a3-505">Start und StopAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-505">Start and StopAsync</span></span>

<span data-ttu-id="4b5a3-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="4b5a3-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="4b5a3-508">StartAsync und StopAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="4b5a3-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> startet die App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="4b5a3-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> hält die App an.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="4b5a3-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4b5a3-511">WaitForShutdown</span></span>

<span data-ttu-id="4b5a3-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wird über die <xref:Microsoft.Extensions.Hosting.IHostLifetime> ausgelöst, z. B. `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="4b5a3-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="4b5a3-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="4b5a3-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="4b5a3-516">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-516">External control</span></span>

<span data-ttu-id="4b5a3-517">Die externe Steuerung des Hosts kann mithilfe von Methoden durchgeführt werden, die extern aufgerufen werden können:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="4b5a3-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4b5a3-519">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4b5a3-520">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="4b5a3-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="4b5a3-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt Informationen über die Hostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="4b5a3-522">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="4b5a3-523">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4b5a3-524">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="4b5a3-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="4b5a3-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren (einschließlich Anforderungen für ordnungsgemäßes Herunterfahren).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="4b5a3-526">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von <xref:System.Action>-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4b5a3-527">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="4b5a3-527">Cancellation Token</span></span> | <span data-ttu-id="4b5a3-528">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="4b5a3-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="4b5a3-529">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="4b5a3-530">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4b5a3-531">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-531">All requests should be processed.</span></span> <span data-ttu-id="4b5a3-532">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="4b5a3-533">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4b5a3-534">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-534">Requests may still be processing.</span></span> <span data-ttu-id="4b5a3-535">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="4b5a3-536">Fügen Sie den <xref:Microsoft.Extensions.Hosting.IApplicationLifetime>-Dienst über einen Konstruktor in eine beliebige Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="4b5a3-537">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Konstruktorinjektion für eine `LifetimeEventsHostedService`-Klasse (eine <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierung), um die Ereignisse zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="4b5a3-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="4b5a3-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> fordert das Beenden der App an.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="4b5a3-540">Die folgende Klasse verwendet <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4b5a3-541">Die ASP.NET Core-Vorlagen erstellen einen generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4b5a3-542">Hostdefinition</span><span class="sxs-lookup"><span data-stu-id="4b5a3-542">Host definition</span></span>

<span data-ttu-id="4b5a3-543">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4b5a3-544">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="4b5a3-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="4b5a3-545">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-545">Logging</span></span>
* <span data-ttu-id="4b5a3-546">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-546">Configuration</span></span>
* <span data-ttu-id="4b5a3-547">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-547">`IHostedService` implementations</span></span>

<span data-ttu-id="4b5a3-548">Beim Starten eines Hosts wird <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> aufgerufen, die in der Sammlung gehosteter Dienste des Dienstcontainers registriert ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-548">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4b5a3-549">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4b5a3-550">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4b5a3-551">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-551">Set up a host</span></span>

<span data-ttu-id="4b5a3-552">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4b5a3-553">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-553">The `Main` method:</span></span>

* <span data-ttu-id="4b5a3-554">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4b5a3-555">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4b5a3-556">Die ASP.NET Core-Webvorlagen generieren den folgenden Code zum Erstellen eines Hosts:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="4b5a3-557">Der folgende Code erstellt eine Nicht-HTTP-Workload mit einer dem DI-Container hinzugefügten `IHostedService`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4b5a3-558">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4b5a3-559">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4b5a3-560">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4b5a3-561">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4b5a3-562">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="4b5a3-562">Default builder settings</span></span>

<span data-ttu-id="4b5a3-563">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4b5a3-564">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4b5a3-565">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="4b5a3-566">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4b5a3-567">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-567">Command-line arguments.</span></span>
* <span data-ttu-id="4b5a3-568">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="4b5a3-569">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4b5a3-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="4b5a3-570">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="4b5a3-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4b5a3-571">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="4b5a3-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4b5a3-572">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-572">Environment variables.</span></span>
  * <span data-ttu-id="4b5a3-573">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-573">Command-line arguments.</span></span>
* <span data-ttu-id="4b5a3-574">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4b5a3-575">Konsole</span><span class="sxs-lookup"><span data-stu-id="4b5a3-575">Console</span></span>
  * <span data-ttu-id="4b5a3-576">Debug</span><span class="sxs-lookup"><span data-stu-id="4b5a3-576">Debug</span></span>
  * <span data-ttu-id="4b5a3-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="4b5a3-577">EventSource</span></span>
  * <span data-ttu-id="4b5a3-578">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4b5a3-579">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4b5a3-580">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4b5a3-581">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4b5a3-582">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4b5a3-583">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4b5a3-584">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4b5a3-585">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4b5a3-586">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-586">Enables IIS integration.</span></span> <span data-ttu-id="4b5a3-587">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4b5a3-588">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4b5a3-589">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="4b5a3-589">Framework-provided services</span></span>

<span data-ttu-id="4b5a3-590">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4b5a3-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4b5a3-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4b5a3-593">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4b5a3-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4b5a3-594">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4b5a3-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="4b5a3-596">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4b5a3-597">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4b5a3-598">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4b5a3-599">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4b5a3-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4b5a3-600">IHostLifetime</span></span>

<span data-ttu-id="4b5a3-601">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4b5a3-602">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-602">The last implementation registered is used.</span></span>

<span data-ttu-id="4b5a3-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4b5a3-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4b5a3-605">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4b5a3-606">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4b5a3-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4b5a3-607">IHostEnvironment</span></span>

<span data-ttu-id="4b5a3-608">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4b5a3-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4b5a3-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4b5a3-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4b5a3-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4b5a3-612">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4b5a3-613">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-613">Host configuration</span></span>

<span data-ttu-id="4b5a3-614">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4b5a3-615">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4b5a3-616">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4b5a3-617">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4b5a3-618">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-619">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4b5a3-620">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4b5a3-621">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4b5a3-622">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4b5a3-623">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4b5a3-624">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4b5a3-625">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4b5a3-625">App configuration</span></span>

<span data-ttu-id="4b5a3-626">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4b5a3-627">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4b5a3-628">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4b5a3-629">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4b5a3-630">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4b5a3-631">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4b5a3-632">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-632">Settings for all app types</span></span>

<span data-ttu-id="4b5a3-633">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4b5a3-634">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4b5a3-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-635">ApplicationName</span></span>

<span data-ttu-id="4b5a3-636">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4b5a3-637">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4b5a3-638">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-638">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-639">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4b5a3-640">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4b5a3-641">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4b5a3-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-642">ContentRoot</span></span>

<span data-ttu-id="4b5a3-643">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4b5a3-644">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4b5a3-645">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4b5a3-646">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-646">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-647">**Standard**: Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4b5a3-648">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4b5a3-649">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4b5a3-650">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-650">For more information, see:</span></span>

* [<span data-ttu-id="4b5a3-651">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4b5a3-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4b5a3-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4b5a3-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4b5a3-653">EnvironmentName</span></span>

<span data-ttu-id="4b5a3-654">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4b5a3-655">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4b5a3-656">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4b5a3-657">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-657">**Key**: `environment`</span></span>  
<span data-ttu-id="4b5a3-658">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-658">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-659">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-659">**Default**: `Production`</span></span>  
<span data-ttu-id="4b5a3-660">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4b5a3-661">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4b5a3-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4b5a3-662">ShutdownTimeout</span></span>

<span data-ttu-id="4b5a3-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4b5a3-664">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-664">The default value is five seconds.</span></span>  <span data-ttu-id="4b5a3-665">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="4b5a3-666">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4b5a3-667">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4b5a3-668">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4b5a3-669">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4b5a3-670">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4b5a3-671">**Schlüssel**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4b5a3-672">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-672">**Type**: `int`</span></span>  
<span data-ttu-id="4b5a3-673">**Standard**: 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="4b5a3-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4b5a3-674">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4b5a3-675">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4b5a3-676">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="4b5a3-677">Deaktivieren des Neuladens der App-Konfiguration bei Änderungen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="4b5a3-678">Die Dateien *appsettings.json* und *appsettings.{Environment}.json* werden [standardmäßig](xref:fundamentals/configuration/index#default) neu geladen, wenn die Datei geändert wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="4b5a3-679">Zum Deaktivieren dieses Verhaltens in ASP.NET Core 5.0 Preview 3 oder höher müssen Sie den `hostBuilder:reloadConfigOnChange`-Schlüssel auf `false` festlegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="4b5a3-680">**Schlüssel**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4b5a3-681">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-682">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-682">**Default**: `true`</span></span>  
<span data-ttu-id="4b5a3-683">**Befehlszeilenargument:** `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4b5a3-684">**Umgebungsvariable:** `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="4b5a3-685">Das Trennzeichen `:` funktioniert nicht auf allen Plattformen mit den hierarchischen Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="4b5a3-686">Weitere Informationen finden Sie unter [Umgebungsvariablen](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="4b5a3-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="4b5a3-687">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="4b5a3-687">Settings for web apps</span></span>

<span data-ttu-id="4b5a3-688">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4b5a3-689">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4b5a3-690">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4b5a3-691">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4b5a3-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4b5a3-692">CaptureStartupErrors</span></span>

<span data-ttu-id="4b5a3-693">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4b5a3-694">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4b5a3-695">**Schlüssel**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4b5a3-696">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-697">**Standard**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4b5a3-698">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4b5a3-699">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4b5a3-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4b5a3-700">DetailedErrors</span></span>

<span data-ttu-id="4b5a3-701">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4b5a3-702">**Schlüssel**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4b5a3-703">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-704">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-704">**Default**: `false`</span></span>  
<span data-ttu-id="4b5a3-705">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4b5a3-706">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4b5a3-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4b5a3-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="4b5a3-708">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4b5a3-709">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4b5a3-710">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4b5a3-711">**Schlüssel**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4b5a3-712">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-712">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-713">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4b5a3-713">**Default**: Empty string</span></span>  
<span data-ttu-id="4b5a3-714">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4b5a3-715">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4b5a3-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4b5a3-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4b5a3-717">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4b5a3-718">**Schlüssel**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4b5a3-719">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-719">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-720">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="4b5a3-720">**Default**: Empty string</span></span>  
<span data-ttu-id="4b5a3-721">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4b5a3-722">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4b5a3-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4b5a3-723">HTTPS_Port</span></span>

<span data-ttu-id="4b5a3-724">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-724">The HTTPS redirect port.</span></span> <span data-ttu-id="4b5a3-725">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4b5a3-726">**Schlüssel**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="4b5a3-727">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-727">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-728">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4b5a3-729">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4b5a3-730">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4b5a3-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4b5a3-731">PreferHostingUrls</span></span>

<span data-ttu-id="4b5a3-732">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4b5a3-733">**Schlüssel**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4b5a3-734">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-735">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-735">**Default**: `true`</span></span>  
<span data-ttu-id="4b5a3-736">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4b5a3-737">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4b5a3-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4b5a3-738">PreventHostingStartup</span></span>

<span data-ttu-id="4b5a3-739">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4b5a3-740">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4b5a3-741">**Schlüssel**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4b5a3-742">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="4b5a3-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4b5a3-743">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-743">**Default**: `false`</span></span>  
<span data-ttu-id="4b5a3-744">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4b5a3-745">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4b5a3-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4b5a3-746">StartupAssembly</span></span>

<span data-ttu-id="4b5a3-747">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4b5a3-748">**Schlüssel**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4b5a3-749">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-749">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-750">**Standard**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="4b5a3-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4b5a3-751">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4b5a3-752">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4b5a3-753">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4b5a3-754">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4b5a3-755">URLs</span><span class="sxs-lookup"><span data-stu-id="4b5a3-755">URLs</span></span>

<span data-ttu-id="4b5a3-756">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4b5a3-757">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4b5a3-758">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4b5a3-759">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4b5a3-760">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="4b5a3-761">**Schlüssel**: `urls`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-761">**Key**: `urls`</span></span>  
<span data-ttu-id="4b5a3-762">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-762">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-763">**Standard**: `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4b5a3-764">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4b5a3-765">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4b5a3-766">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4b5a3-767">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4b5a3-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-768">WebRoot</span></span>

<span data-ttu-id="4b5a3-769">Die [IWebHostEnvironment.WebRootPath-](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath)-Eigenschaft bestimmt den relativen Pfad zu den statischen Objekten der App.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4b5a3-770">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4b5a3-771">**Schlüssel**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="4b5a3-772">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-772">**Type**: `string`</span></span>  
<span data-ttu-id="4b5a3-773">**Standard**: Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4b5a3-774">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4b5a3-775">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4b5a3-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4b5a3-776">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` für `IWebHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4b5a3-777">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-777">For more information, see:</span></span>

* [<span data-ttu-id="4b5a3-778">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4b5a3-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4b5a3-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4b5a3-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4b5a3-780">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="4b5a3-780">Manage the host lifetime</span></span>

<span data-ttu-id="4b5a3-781">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4b5a3-782">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4b5a3-783">Run</span><span class="sxs-lookup"><span data-stu-id="4b5a3-783">Run</span></span>

<span data-ttu-id="4b5a3-784">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4b5a3-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-785">RunAsync</span></span>

<span data-ttu-id="4b5a3-786">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4b5a3-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-787">RunConsoleAsync</span></span>

<span data-ttu-id="4b5a3-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4b5a3-789">Starten</span><span class="sxs-lookup"><span data-stu-id="4b5a3-789">Start</span></span>

<span data-ttu-id="4b5a3-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4b5a3-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-791">StartAsync</span></span>

<span data-ttu-id="4b5a3-792">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4b5a3-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4b5a3-794">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4b5a3-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-795">StopAsync</span></span>

<span data-ttu-id="4b5a3-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4b5a3-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4b5a3-797">WaitForShutdown</span></span>

<span data-ttu-id="4b5a3-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4b5a3-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4b5a3-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="4b5a3-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="4b5a3-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4b5a3-801">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="4b5a3-801">External control</span></span>

<span data-ttu-id="4b5a3-802">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="4b5a3-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4b5a3-803">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4b5a3-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
