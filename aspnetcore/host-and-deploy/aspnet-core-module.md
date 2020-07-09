---
title: ASP.NET Core-Modul
author: rick-anderson
description: Erfahren Sie, wie Sie das ASP.NET Core-Modul so konfigurieren, dass es ASP.NET Core-Apps hosten kann.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 5e04d54bedf7cce8b7a6ab546920e7dec1213a20
ms.sourcegitcommit: d1fa3d69dda675d7a52c7100742dfa6297413376
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86093353"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="200a9-103">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-103">ASP.NET Core Module</span></span>

<span data-ttu-id="200a9-104">Von [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [ Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="200a9-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="200a9-105">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="200a9-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="200a9-106">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="200a9-107">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="200a9-108">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="200a9-108">Supported Windows versions:</span></span>

* <span data-ttu-id="200a9-109">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-109">Windows 7 or later</span></span>
* <span data-ttu-id="200a9-110">Windows Server 2012 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-110">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="200a9-111">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="200a9-112">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="200a9-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="200a9-113">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="200a9-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="200a9-114">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="200a9-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="200a9-115">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-115">In-process hosting model</span></span>

<span data-ttu-id="200a9-116">ASP.NET Core-Apps verwenden standardmäßig das In-Process-Hostingmodell.</span><span class="sxs-lookup"><span data-stu-id="200a9-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="200a9-117">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="200a9-118">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="200a9-119">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="200a9-120">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="200a9-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="200a9-121">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="200a9-122">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="200a9-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="200a9-123">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="200a9-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="200a9-124">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="200a9-125">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="200a9-125">Use one app pool per app.</span></span>

* <span data-ttu-id="200a9-126">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="200a9-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="200a9-127">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="200a9-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="200a9-128">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="200a9-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="200a9-129">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="200a9-129">Client disconnects are detected.</span></span> <span data-ttu-id="200a9-130">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="200a9-131">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="200a9-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="200a9-132">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="200a9-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="200a9-133">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="200a9-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="200a9-134">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="200a9-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="200a9-135">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="200a9-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="200a9-136">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="200a9-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="200a9-137">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="200a9-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="200a9-138">[Bereitstellungen von Webpaketen (Einzeldateien)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="200a9-139">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-139">Out-of-process hosting model</span></span>

<span data-ttu-id="200a9-140">Um eine App für Out-of-Process-Hosting zu konfigurieren, legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei ( *.csproj*) auf `OutOfProcess` fest:</span><span class="sxs-lookup"><span data-stu-id="200a9-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="200a9-141">Das In-Process-Hosting wird mithilfe von `InProcess` (Standardwert) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="200a9-142">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="200a9-143">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="200a9-144">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="200a9-145">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="200a9-146">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="200a9-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="200a9-147">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-147">Hosting model changes</span></span>

<span data-ttu-id="200a9-148">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="200a9-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="200a9-149">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="200a9-150">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="200a9-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="200a9-151">Prozessname</span><span class="sxs-lookup"><span data-stu-id="200a9-151">Process name</span></span>

<span data-ttu-id="200a9-152">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="200a9-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="200a9-153">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="200a9-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="200a9-154">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="200a9-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="200a9-155">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="200a9-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="200a9-156">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="200a9-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="200a9-157">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="200a9-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="200a9-158">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="200a9-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="200a9-159">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="200a9-160">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="200a9-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="200a9-161">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="200a9-161">Configuration with web.config</span></span>

<span data-ttu-id="200a9-162">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="200a9-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="200a9-163">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="200a9-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="200a9-164">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="200a9-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="200a9-165">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="200a9-166">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="200a9-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-167">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="200a9-168">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="200a9-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="200a9-169">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="200a9-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="200a9-170">Attribut</span><span class="sxs-lookup"><span data-stu-id="200a9-170">Attribute</span></span> | <span data-ttu-id="200a9-171">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="200a9-171">Description</span></span> | <span data-ttu-id="200a9-172">Standard</span><span class="sxs-lookup"><span data-stu-id="200a9-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="200a9-173">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-173">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-174">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="200a9-175">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-176">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="200a9-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="200a9-177">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-178">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="200a9-179">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="200a9-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="200a9-180">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-180">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-181">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="200a9-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="200a9-182">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-183">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="200a9-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="200a9-184">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="200a9-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="200a9-185">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="200a9-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="200a9-186">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="200a9-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="200a9-187">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-187">Default: `1`</span></span><br><span data-ttu-id="200a9-188">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-188">Min: `1`</span></span><br><span data-ttu-id="200a9-189">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="200a9-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="200a9-190">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-190">Required string attribute.</span></span></p><p><span data-ttu-id="200a9-191">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="200a9-192">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-192">Relative paths are supported.</span></span> <span data-ttu-id="200a9-193">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="200a9-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="200a9-194">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-195">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="200a9-196">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="200a9-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="200a9-197">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="200a9-198">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-198">Default: `10`</span></span><br><span data-ttu-id="200a9-199">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-199">Min: `0`</span></span><br><span data-ttu-id="200a9-200">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="200a9-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="200a9-201">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="200a9-202">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="200a9-203">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="200a9-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="200a9-204">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="200a9-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="200a9-205">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="200a9-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="200a9-206">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="200a9-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="200a9-207">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="200a9-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="200a9-208">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-208">Default: `00:02:00`</span></span><br><span data-ttu-id="200a9-209">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-209">Min: `00:00:00`</span></span><br><span data-ttu-id="200a9-210">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="200a9-211">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-212">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="200a9-213">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-213">Default: `10`</span></span><br><span data-ttu-id="200a9-214">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-214">Min: `0`</span></span><br><span data-ttu-id="200a9-215">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="200a9-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="200a9-216">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-217">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="200a9-218">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="200a9-219">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="200a9-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="200a9-220">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="200a9-221">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="200a9-221">Default: `120`</span></span><br><span data-ttu-id="200a9-222">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-222">Min: `0`</span></span><br><span data-ttu-id="200a9-223">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="200a9-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="200a9-224">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-225">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="200a9-226">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-226">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-227">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="200a9-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="200a9-228">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="200a9-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="200a9-229">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="200a9-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="200a9-230">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-231">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="200a9-232">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="200a9-233">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="200a9-233">Set environment variables</span></span>

<span data-ttu-id="200a9-234">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="200a9-235">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="200a9-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="200a9-236">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="200a9-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="200a9-237">Im folgenden Beispiel werden zwei Umgebungsvariablen in *web.config* festgelegt. `ASPNETCORE_ENVIRONMENT` legt die Umgebung der App auf `Development` fest.</span><span class="sxs-lookup"><span data-stu-id="200a9-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="200a9-238">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="200a9-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="200a9-239">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="200a9-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="200a9-240">Eine Alternative zum direkten Festlegen der Umgebung in *web.config* ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="200a9-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="200a9-241">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="200a9-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="200a9-242">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="200a9-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="200a9-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="200a9-243">app_offline.htm</span></span>

<span data-ttu-id="200a9-244">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="200a9-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="200a9-245">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="200a9-246">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="200a9-247">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="200a9-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="200a9-248">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="200a9-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="200a9-249">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="200a9-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="200a9-250">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="200a9-250">Start-up error page</span></span>

<span data-ttu-id="200a9-251">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="200a9-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="200a9-252">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-253">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-254">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-255">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="200a9-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="200a9-256">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="200a9-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="200a9-257">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="200a9-257">Log creation and redirection</span></span>

<span data-ttu-id="200a9-258">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="200a9-259">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-260">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="200a9-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="200a9-261">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="200a9-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="200a9-262">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="200a9-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="200a9-263">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="200a9-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="200a9-264">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="200a9-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="200a9-265">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="200a9-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="200a9-266">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="200a9-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="200a9-267">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="200a9-268">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="200a9-269">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="200a9-270">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="200a9-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="200a9-271">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="200a9-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="200a9-272">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="200a9-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="200a9-273">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="200a9-274">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="200a9-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-275">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="200a9-276">Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="200a9-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="200a9-277">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="200a9-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="200a9-278">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="200a9-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="200a9-279">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="200a9-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="200a9-280">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="200a9-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="200a9-281">Ordner, die im Pfad angegeben werden (*logs* im vorherigen Beispiel), werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-282">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="200a9-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="200a9-283">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="200a9-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="200a9-284">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="200a9-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="200a9-285">ERROR</span><span class="sxs-lookup"><span data-stu-id="200a9-285">ERROR</span></span>
* <span data-ttu-id="200a9-286">WARNING</span><span class="sxs-lookup"><span data-stu-id="200a9-286">WARNING</span></span>
* <span data-ttu-id="200a9-287">INFO</span><span class="sxs-lookup"><span data-stu-id="200a9-287">INFO</span></span>
* <span data-ttu-id="200a9-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="200a9-288">TRACE</span></span>

<span data-ttu-id="200a9-289">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="200a9-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="200a9-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="200a9-290">CONSOLE</span></span>
* <span data-ttu-id="200a9-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="200a9-291">EVENTLOG</span></span>
* <span data-ttu-id="200a9-292">DATEI</span><span class="sxs-lookup"><span data-stu-id="200a9-292">FILE</span></span>

<span data-ttu-id="200a9-293">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="200a9-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="200a9-294">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="200a9-294">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="200a9-295">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="200a9-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="200a9-296">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="200a9-296">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="200a9-297">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="200a9-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="200a9-298">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="200a9-298">The size of the log isn't limited.</span></span> <span data-ttu-id="200a9-299">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="200a9-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="200a9-300">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="200a9-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="200a9-301">Ändern der Stapelgröße</span><span class="sxs-lookup"><span data-stu-id="200a9-301">Modify the stack size</span></span>

<span data-ttu-id="200a9-302">*Gilt nur bei Verwendung des In-Process-Hostingmodells.*</span><span class="sxs-lookup"><span data-stu-id="200a9-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="200a9-303">Konfigurieren Sie die Größe des verwalteten Stapels in der Datei *web.config* mithilfe der Einstellung `stackSize` (in Byte). Die Standardgröße beträgt `1048576` Byte (1 MB).</span><span class="sxs-lookup"><span data-stu-id="200a9-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="200a9-304">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="200a9-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="200a9-305">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="200a9-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="200a9-306">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="200a9-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="200a9-307">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="200a9-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="200a9-308">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="200a9-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="200a9-309">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="200a9-310">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="200a9-311">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="200a9-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="200a9-312">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="200a9-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="200a9-313">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="200a9-314">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="200a9-315">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="200a9-316">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="200a9-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="200a9-317">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="200a9-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="200a9-318">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="200a9-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="200a9-319">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="200a9-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="200a9-320">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="200a9-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="200a9-321">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-321">Run the installer.</span></span>
1. <span data-ttu-id="200a9-322">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="200a9-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="200a9-323">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="200a9-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="200a9-324">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="200a9-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="200a9-325">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="200a9-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="200a9-326">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="200a9-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="200a9-327">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="200a9-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="200a9-328">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="200a9-329">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="200a9-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="200a9-330">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="200a9-331">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="200a9-332">Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-332">Module</span></span>

<span data-ttu-id="200a9-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="200a9-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="200a9-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="200a9-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="200a9-343">Schema</span><span class="sxs-lookup"><span data-stu-id="200a9-343">Schema</span></span>

<span data-ttu-id="200a9-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-344">**IIS**</span></span>

* <span data-ttu-id="200a9-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="200a9-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="200a9-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-347">**IIS Express**</span></span>

* <span data-ttu-id="200a9-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="200a9-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="200a9-350">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-350">Configuration</span></span>

<span data-ttu-id="200a9-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-351">**IIS**</span></span>

* <span data-ttu-id="200a9-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="200a9-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-353">**IIS Express**</span></span>

* <span data-ttu-id="200a9-354">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="200a9-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="200a9-355">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="200a9-356">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="200a9-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="200a9-357">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="200a9-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="200a9-358">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="200a9-359">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="200a9-360">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="200a9-360">Supported Windows versions:</span></span>

* <span data-ttu-id="200a9-361">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-361">Windows 7 or later</span></span>
* <span data-ttu-id="200a9-362">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="200a9-363">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="200a9-364">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="200a9-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="200a9-365">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="200a9-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="200a9-366">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="200a9-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="200a9-367">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-367">In-process hosting model</span></span>

<span data-ttu-id="200a9-368">Um eine App für In-Process-Hosting zu konfigurieren, fügen Sie der Projektdatei der App die Eigenschaft `<AspNetCoreHostingModel>` mit dem Wert `InProcess` hinzu (Out-of-Process-Hosting wird mit `OutOfProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="200a9-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="200a9-369">Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.</span><span class="sxs-lookup"><span data-stu-id="200a9-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="200a9-370">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="200a9-371">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="200a9-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="200a9-372">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="200a9-373">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="200a9-374">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="200a9-375">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="200a9-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="200a9-376">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="200a9-377">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="200a9-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="200a9-378">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="200a9-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="200a9-379">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="200a9-380">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="200a9-380">Use one app pool per app.</span></span>

* <span data-ttu-id="200a9-381">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="200a9-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="200a9-382">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="200a9-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="200a9-383">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="200a9-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="200a9-384">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="200a9-384">Client disconnects are detected.</span></span> <span data-ttu-id="200a9-385">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="200a9-386">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="200a9-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="200a9-387">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="200a9-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="200a9-388">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="200a9-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="200a9-389">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="200a9-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="200a9-390">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="200a9-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="200a9-391">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="200a9-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="200a9-392">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="200a9-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="200a9-393">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-393">Out-of-process hosting model</span></span>

<span data-ttu-id="200a9-394">Um eine App für Out-of-Process-Hosting zu konfigurieren, konfigurieren Sie die Projektdatei auf eine der folgenden Weisen:</span><span class="sxs-lookup"><span data-stu-id="200a9-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="200a9-395">Geben Sie die `<AspNetCoreHostingModel>`-Eigenschaft nicht an.</span><span class="sxs-lookup"><span data-stu-id="200a9-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="200a9-396">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="200a9-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="200a9-397">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft auf `OutOfProcess` fest (In-Process-Hosting wird mit `InProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="200a9-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="200a9-398">Beim Wert wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="200a9-399">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="200a9-400">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="200a9-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="200a9-401">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="200a9-402">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="200a9-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="200a9-403">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="200a9-403">Hosting model changes</span></span>

<span data-ttu-id="200a9-404">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="200a9-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="200a9-405">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="200a9-406">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="200a9-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="200a9-407">Prozessname</span><span class="sxs-lookup"><span data-stu-id="200a9-407">Process name</span></span>

<span data-ttu-id="200a9-408">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="200a9-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="200a9-409">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="200a9-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="200a9-410">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="200a9-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="200a9-411">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="200a9-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="200a9-412">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="200a9-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="200a9-413">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="200a9-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="200a9-414">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="200a9-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="200a9-415">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="200a9-416">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="200a9-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="200a9-417">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="200a9-417">Configuration with web.config</span></span>

<span data-ttu-id="200a9-418">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="200a9-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="200a9-419">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="200a9-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="200a9-420">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="200a9-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="200a9-421">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="200a9-422">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="200a9-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-423">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="200a9-424">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="200a9-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="200a9-425">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="200a9-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="200a9-426">Attribut</span><span class="sxs-lookup"><span data-stu-id="200a9-426">Attribute</span></span> | <span data-ttu-id="200a9-427">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="200a9-427">Description</span></span> | <span data-ttu-id="200a9-428">Standard</span><span class="sxs-lookup"><span data-stu-id="200a9-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="200a9-429">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-429">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-430">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="200a9-431">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-432">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="200a9-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="200a9-433">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-434">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="200a9-435">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="200a9-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="200a9-436">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-436">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-437">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="200a9-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="200a9-438">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-439">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="200a9-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="200a9-440">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="200a9-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="200a9-441">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="200a9-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="200a9-442">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="200a9-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="200a9-443">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-443">Default: `1`</span></span><br><span data-ttu-id="200a9-444">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-444">Min: `1`</span></span><br><span data-ttu-id="200a9-445">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="200a9-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="200a9-446">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-446">Required string attribute.</span></span></p><p><span data-ttu-id="200a9-447">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="200a9-448">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-448">Relative paths are supported.</span></span> <span data-ttu-id="200a9-449">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="200a9-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="200a9-450">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-451">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="200a9-452">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="200a9-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="200a9-453">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="200a9-454">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-454">Default: `10`</span></span><br><span data-ttu-id="200a9-455">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-455">Min: `0`</span></span><br><span data-ttu-id="200a9-456">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="200a9-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="200a9-457">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="200a9-458">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="200a9-459">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="200a9-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="200a9-460">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="200a9-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="200a9-461">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="200a9-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="200a9-462">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="200a9-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="200a9-463">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="200a9-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="200a9-464">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-464">Default: `00:02:00`</span></span><br><span data-ttu-id="200a9-465">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-465">Min: `00:00:00`</span></span><br><span data-ttu-id="200a9-466">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="200a9-467">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-468">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="200a9-469">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-469">Default: `10`</span></span><br><span data-ttu-id="200a9-470">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-470">Min: `0`</span></span><br><span data-ttu-id="200a9-471">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="200a9-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="200a9-472">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-473">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="200a9-474">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="200a9-475">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="200a9-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="200a9-476">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="200a9-477">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="200a9-477">Default: `120`</span></span><br><span data-ttu-id="200a9-478">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-478">Min: `0`</span></span><br><span data-ttu-id="200a9-479">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="200a9-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="200a9-480">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-481">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="200a9-482">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-482">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-483">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="200a9-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="200a9-484">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="200a9-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="200a9-485">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="200a9-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="200a9-486">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-487">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="200a9-488">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="200a9-489">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="200a9-489">Setting environment variables</span></span>

<span data-ttu-id="200a9-490">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="200a9-491">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="200a9-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="200a9-492">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="200a9-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="200a9-493">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-493">The following example sets two environment variables.</span></span> <span data-ttu-id="200a9-494">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="200a9-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="200a9-495">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="200a9-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="200a9-496">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="200a9-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="200a9-497">Eine Alternative zum direkten Festlegen der Umgebung in *web.config* ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="200a9-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="200a9-498">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="200a9-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="200a9-499">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="200a9-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="200a9-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="200a9-500">app_offline.htm</span></span>

<span data-ttu-id="200a9-501">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="200a9-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="200a9-502">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="200a9-503">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="200a9-504">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="200a9-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="200a9-505">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="200a9-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="200a9-506">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="200a9-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="200a9-507">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="200a9-507">Start-up error page</span></span>

<span data-ttu-id="200a9-508">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="200a9-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="200a9-509">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-510">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-511">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="200a9-512">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="200a9-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="200a9-513">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="200a9-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="200a9-514">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="200a9-514">Log creation and redirection</span></span>

<span data-ttu-id="200a9-515">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="200a9-516">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-517">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="200a9-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="200a9-518">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="200a9-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="200a9-519">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="200a9-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="200a9-520">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="200a9-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="200a9-521">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="200a9-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="200a9-522">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="200a9-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="200a9-523">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="200a9-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="200a9-524">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="200a9-525">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="200a9-526">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="200a9-527">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="200a9-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="200a9-528">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="200a9-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="200a9-529">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="200a9-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="200a9-530">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="200a9-531">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="200a9-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-532">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="200a9-533">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="200a9-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="200a9-534">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="200a9-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="200a9-535">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="200a9-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="200a9-536">Fügen Sie dem `<aspNetCore>`-Element in der *web.config*-Datei das `<handlerSettings>`-Element hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="200a9-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="200a9-537">Ordner im Pfad, die für den `<handlerSetting>`-Wert (*logs* im vorherigen Beispiel) bereitgestellt werden, werden nicht automatisch vom Modul erstellt und müssen bereits in der Bereitstellung vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="200a9-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="200a9-538">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="200a9-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="200a9-539">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="200a9-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="200a9-540">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="200a9-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="200a9-541">ERROR</span><span class="sxs-lookup"><span data-stu-id="200a9-541">ERROR</span></span>
* <span data-ttu-id="200a9-542">WARNING</span><span class="sxs-lookup"><span data-stu-id="200a9-542">WARNING</span></span>
* <span data-ttu-id="200a9-543">INFO</span><span class="sxs-lookup"><span data-stu-id="200a9-543">INFO</span></span>
* <span data-ttu-id="200a9-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="200a9-544">TRACE</span></span>

<span data-ttu-id="200a9-545">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="200a9-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="200a9-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="200a9-546">CONSOLE</span></span>
* <span data-ttu-id="200a9-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="200a9-547">EVENTLOG</span></span>
* <span data-ttu-id="200a9-548">DATEI</span><span class="sxs-lookup"><span data-stu-id="200a9-548">FILE</span></span>

<span data-ttu-id="200a9-549">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="200a9-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="200a9-550">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="200a9-550">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="200a9-551">(Standard: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="200a9-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="200a9-552">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="200a9-552">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="200a9-553">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="200a9-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="200a9-554">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="200a9-554">The size of the log isn't limited.</span></span> <span data-ttu-id="200a9-555">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="200a9-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="200a9-556">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="200a9-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="200a9-557">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="200a9-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="200a9-558">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="200a9-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="200a9-559">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="200a9-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="200a9-560">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="200a9-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="200a9-561">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="200a9-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="200a9-562">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="200a9-563">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="200a9-564">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="200a9-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="200a9-565">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="200a9-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="200a9-566">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="200a9-567">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="200a9-568">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="200a9-569">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="200a9-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="200a9-570">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="200a9-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="200a9-571">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="200a9-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="200a9-572">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="200a9-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="200a9-573">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="200a9-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="200a9-574">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-574">Run the installer.</span></span>
1. <span data-ttu-id="200a9-575">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="200a9-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="200a9-576">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="200a9-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="200a9-577">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="200a9-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="200a9-578">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="200a9-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="200a9-579">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="200a9-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="200a9-580">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="200a9-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="200a9-581">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="200a9-582">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="200a9-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="200a9-583">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="200a9-584">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="200a9-585">Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-585">Module</span></span>

<span data-ttu-id="200a9-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="200a9-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="200a9-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="200a9-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="200a9-596">Schema</span><span class="sxs-lookup"><span data-stu-id="200a9-596">Schema</span></span>

<span data-ttu-id="200a9-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-597">**IIS**</span></span>

* <span data-ttu-id="200a9-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="200a9-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="200a9-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-600">**IIS Express**</span></span>

* <span data-ttu-id="200a9-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="200a9-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="200a9-603">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-603">Configuration</span></span>

<span data-ttu-id="200a9-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-604">**IIS**</span></span>

* <span data-ttu-id="200a9-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="200a9-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-606">**IIS Express**</span></span>

* <span data-ttu-id="200a9-607">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="200a9-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="200a9-608">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="200a9-609">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="200a9-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="200a9-610">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um Webanforderungen an Back-End-ASP.NET Core-Apps weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="200a9-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="200a9-611">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="200a9-611">Supported Windows versions:</span></span>

* <span data-ttu-id="200a9-612">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-612">Windows 7 or later</span></span>
* <span data-ttu-id="200a9-613">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="200a9-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="200a9-614">Das Modul funktioniert nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="200a9-614">The module only works with Kestrel.</span></span> <span data-ttu-id="200a9-615">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="200a9-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="200a9-616">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul auch Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="200a9-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="200a9-617">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie abstürzt.</span><span class="sxs-lookup"><span data-stu-id="200a9-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="200a9-618">Dies ist im Prinzip das gleiche Verhalten wie bei ASP.NET 4.x-Apps, die prozessintern in IIS ausgeführt und durch [Windows Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="200a9-619">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer App:</span><span class="sxs-lookup"><span data-stu-id="200a9-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core-Modul](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="200a9-621">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="200a9-622">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="200a9-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="200a9-623">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="200a9-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="200a9-624">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="200a9-625">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="200a9-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="200a9-626">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="200a9-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="200a9-627">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="200a9-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="200a9-628">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="200a9-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="200a9-629">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="200a9-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="200a9-630">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="200a9-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="200a9-631">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="200a9-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="200a9-632">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="200a9-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="200a9-633">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="200a9-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="200a9-634">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="200a9-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="200a9-635">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="200a9-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="200a9-636">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="200a9-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="200a9-637">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="200a9-638">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="200a9-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="200a9-639">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="200a9-639">Configuration with web.config</span></span>

<span data-ttu-id="200a9-640">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="200a9-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="200a9-641">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="200a9-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="200a9-642">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="200a9-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="200a9-643">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="200a9-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-644">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="200a9-645">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="200a9-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="200a9-646">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="200a9-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="200a9-647">Attribut</span><span class="sxs-lookup"><span data-stu-id="200a9-647">Attribute</span></span> | <span data-ttu-id="200a9-648">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="200a9-648">Description</span></span> | <span data-ttu-id="200a9-649">Standard</span><span class="sxs-lookup"><span data-stu-id="200a9-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="200a9-650">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-650">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-651">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="200a9-652">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-653">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="200a9-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="200a9-654">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-655">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="200a9-656">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="200a9-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="200a9-657">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-658">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="200a9-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="200a9-659">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="200a9-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="200a9-660">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="200a9-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="200a9-661">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-661">Default: `1`</span></span><br><span data-ttu-id="200a9-662">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="200a9-662">Min: `1`</span></span><br><span data-ttu-id="200a9-663">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="200a9-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="200a9-664">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-664">Required string attribute.</span></span></p><p><span data-ttu-id="200a9-665">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="200a9-666">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="200a9-666">Relative paths are supported.</span></span> <span data-ttu-id="200a9-667">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="200a9-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="200a9-668">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-669">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="200a9-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="200a9-670">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="200a9-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="200a9-671">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-671">Default: `10`</span></span><br><span data-ttu-id="200a9-672">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-672">Min: `0`</span></span><br><span data-ttu-id="200a9-673">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="200a9-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="200a9-674">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="200a9-675">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="200a9-676">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="200a9-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="200a9-677">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-677">Default: `00:02:00`</span></span><br><span data-ttu-id="200a9-678">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-678">Min: `00:00:00`</span></span><br><span data-ttu-id="200a9-679">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="200a9-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="200a9-680">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-681">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="200a9-682">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="200a9-682">Default: `10`</span></span><br><span data-ttu-id="200a9-683">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-683">Min: `0`</span></span><br><span data-ttu-id="200a9-684">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="200a9-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="200a9-685">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="200a9-686">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="200a9-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="200a9-687">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="200a9-688">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="200a9-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="200a9-689">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="200a9-690">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="200a9-690">Default: `120`</span></span><br><span data-ttu-id="200a9-691">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="200a9-691">Min: `0`</span></span><br><span data-ttu-id="200a9-692">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="200a9-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="200a9-693">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="200a9-694">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="200a9-695">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="200a9-695">Optional string attribute.</span></span></p><p><span data-ttu-id="200a9-696">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="200a9-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="200a9-697">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="200a9-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="200a9-698">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="200a9-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="200a9-699">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="200a9-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="200a9-700">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="200a9-701">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="200a9-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="200a9-702">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="200a9-702">Setting environment variables</span></span>

<span data-ttu-id="200a9-703">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="200a9-704">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="200a9-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="200a9-705">In diesem Abschnitt festgelegte Umgebungsvariablen stehen im Konflikt mit Systemumgebungsvariablen gleichen Namens.</span><span class="sxs-lookup"><span data-stu-id="200a9-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="200a9-706">Wenn eine Umgebungsvariable sowohl in der Datei *web.config* als auch auf Systemebene in Windows festgelegt ist, wird der Wert aus der Datei *web.config* dem Wert der Systemumgebungsvariablen angefügt (z.B. `ASPNETCORE_ENVIRONMENT: Development;Development`), um zu verhindern, dass die App startet.</span><span class="sxs-lookup"><span data-stu-id="200a9-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="200a9-707">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-707">The following example sets two environment variables.</span></span> <span data-ttu-id="200a9-708">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="200a9-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="200a9-709">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="200a9-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="200a9-710">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="200a9-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="200a9-711">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="200a9-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="200a9-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="200a9-712">app_offline.htm</span></span>

<span data-ttu-id="200a9-713">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="200a9-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="200a9-714">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="200a9-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="200a9-715">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="200a9-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="200a9-716">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="200a9-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="200a9-717">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="200a9-717">Start-up error page</span></span>

<span data-ttu-id="200a9-718">Wenn das ASP.NET Core-Modul den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="200a9-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="200a9-719">Um diese Seite zu unterdrücken und zur IIS-502-Sandardstatuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="200a9-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="200a9-720">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="200a9-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Statuscodeseite „502.5: Prozessfehler“](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="200a9-722">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="200a9-722">Log creation and redirection</span></span>

<span data-ttu-id="200a9-723">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="200a9-724">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="200a9-725">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="200a9-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="200a9-726">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="200a9-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="200a9-727">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="200a9-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="200a9-728">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="200a9-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="200a9-729">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="200a9-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="200a9-730">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="200a9-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="200a9-731">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="200a9-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="200a9-732">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="200a9-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="200a9-733">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="200a9-734">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="200a9-735">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="200a9-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="200a9-736">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="200a9-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="200a9-737">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="200a9-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="200a9-738">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="200a9-739">Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="200a9-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="200a9-740">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="200a9-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="200a9-741">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="200a9-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="200a9-742">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="200a9-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="200a9-743">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="200a9-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="200a9-744">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="200a9-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="200a9-745">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="200a9-746">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="200a9-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="200a9-747">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="200a9-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="200a9-748">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="200a9-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="200a9-749">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="200a9-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="200a9-750">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="200a9-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="200a9-751">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="200a9-752">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="200a9-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="200a9-753">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="200a9-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="200a9-754">Wenn Sie eine IIS-Freigabekonfiguration verwenden, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="200a9-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="200a9-755">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="200a9-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="200a9-756">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-756">Run the installer.</span></span>
1. <span data-ttu-id="200a9-757">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="200a9-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="200a9-758">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="200a9-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="200a9-759">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="200a9-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="200a9-760">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="200a9-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="200a9-761">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="200a9-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="200a9-762">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="200a9-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="200a9-763">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="200a9-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="200a9-764">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="200a9-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="200a9-765">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="200a9-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="200a9-766">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="200a9-767">Modul</span><span class="sxs-lookup"><span data-stu-id="200a9-767">Module</span></span>

<span data-ttu-id="200a9-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="200a9-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="200a9-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="200a9-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="200a9-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="200a9-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="200a9-774">Schema</span><span class="sxs-lookup"><span data-stu-id="200a9-774">Schema</span></span>

<span data-ttu-id="200a9-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-775">**IIS**</span></span>

* <span data-ttu-id="200a9-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="200a9-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-777">**IIS Express**</span></span>

* <span data-ttu-id="200a9-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="200a9-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="200a9-779">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="200a9-779">Configuration</span></span>

<span data-ttu-id="200a9-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="200a9-780">**IIS**</span></span>

* <span data-ttu-id="200a9-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="200a9-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="200a9-782">**IIS Express**</span></span>

* <span data-ttu-id="200a9-783">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="200a9-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="200a9-784">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="200a9-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="200a9-785">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="200a9-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="200a9-786">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="200a9-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="200a9-787">[Referenzquelle für das ASP.NET Core-Modul (Masterbranch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Verwenden Sie die Dropdownliste **Branch**, um ein spezifisches Release auszuwählen (z. B. `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="200a9-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
