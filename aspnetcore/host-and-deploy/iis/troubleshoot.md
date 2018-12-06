---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2018
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 2ff870623de43676be38c5de8f338a7913e885a8
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450709"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="2853f-103">Problembehandlung bei ASP.NET Core in IIS</span><span class="sxs-lookup"><span data-stu-id="2853f-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="2853f-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2853f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2853f-105">Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="2853f-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="2853f-106">Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="2853f-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2853f-107">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="2853f-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="2853f-108">Ein *502.5: Prozessfehler* oder ein *500.30: Startfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2853f-109">In Visual Studio entspricht ein ASP.NET Core-Projekt standardmäßig dem [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)-Hosting während des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="2853f-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="2853f-110">Ein *502.5: Prozessfehler*, der beim lokalen Debuggen auftritt, kann mithilfe der Hinweise in diesem Thema behoben werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="2853f-111">Weitere Themen zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="2853f-111">Additional troubleshooting topics:</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="2853f-112">Obwohl App Service das [ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module) und IIS für das Hosten von Apps verwendet, finden Sie im dedizierten Thema weitere, für App Service spezifische Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="2853f-112">Although App Service uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="2853f-113">Informationen zur Behandlung von Fehlern in ASP.NET Core-Apps während der Bereitstellung auf einem lokalen System.</span><span class="sxs-lookup"><span data-stu-id="2853f-113">Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

[<span data-ttu-id="2853f-114">Lernen Sie das Debuggen mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2853f-114">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)  
<span data-ttu-id="2853f-115">In diesem Thema werden die Features des Visual Studio-Debuggers vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="2853f-115">This topic introduces the features of the Visual Studio debugger.</span></span>

[<span data-ttu-id="2853f-116">Debuggen mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2853f-116">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)  
<span data-ttu-id="2853f-117">Hier finden Sie weitere Informationen zur integrierten Debuggingunterstützung in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2853f-117">Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="2853f-118">App-Startfehler</span><span class="sxs-lookup"><span data-stu-id="2853f-118">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="2853f-119">502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="2853f-119">502.5 Process Failure</span></span>

<span data-ttu-id="2853f-120">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="2853f-120">The worker process fails.</span></span> <span data-ttu-id="2853f-121">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="2853f-121">The app doesn't start.</span></span>

<span data-ttu-id="2853f-122">Das ASP.NET Core-Modul kann den .NET-Back-End-Prozess nicht starten.</span><span class="sxs-lookup"><span data-stu-id="2853f-122">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="2853f-123">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-123">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="2853f-124">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-124">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2853f-125">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="2853f-125">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

<span data-ttu-id="2853f-126">Die Fehlerseite *502.5: Prozessfehler* wird zurückgegeben, wenn ein falsch konfiguriertes Hosting oder eine falsch konfigurierte App bewirkt, dass der Workerprozess fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="2853f-126">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Browserfenster mit der Seite „502.5: Prozessfehler“](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="2853f-128">500.30: Prozessinterner Startupfehler</span><span class="sxs-lookup"><span data-stu-id="2853f-128">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="2853f-129">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="2853f-129">The worker process fails.</span></span> <span data-ttu-id="2853f-130">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="2853f-130">The app doesn't start.</span></span>

<span data-ttu-id="2853f-131">Das ASP.NET Core-Modul kann den .NET Core-CLR-In-Process nicht starten.</span><span class="sxs-lookup"><span data-stu-id="2853f-131">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="2853f-132">Die Ursache für einen Fehler beim Starten eines Prozesses kann in der Regel über Einträge im [Anwendungsereignisprotokoll](#application-event-log) und im [stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-132">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="2853f-133">Eine allgemeine Fehlerbedingung ist, dass die App aufgrund einer Version des freigegebenen ASP.NET Core-Frameworks falsch konfiguriert ist, die nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-133">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2853f-134">Überprüfen Sie, welche Versionen des freigegebenen ASP.NET Core-Frameworks auf dem Zielcomputer installiert sind.</span><span class="sxs-lookup"><span data-stu-id="2853f-134">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="2853f-135">500.0: Fehler beim Laden des prozessinternen Handlers</span><span class="sxs-lookup"><span data-stu-id="2853f-135">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="2853f-136">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="2853f-136">The worker process fails.</span></span> <span data-ttu-id="2853f-137">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="2853f-137">The app doesn't start.</span></span>

<span data-ttu-id="2853f-138">Das ASP.NET Core-Modul kann die .NET Core-CLR und den In-Process-Anforderungshandler (*aspnetcorev2_inprocess.dll*) nicht finden.</span><span class="sxs-lookup"><span data-stu-id="2853f-138">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="2853f-139">Überprüfen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2853f-139">Check that:</span></span>

* <span data-ttu-id="2853f-140">Diese App legt entweder das NuGet-Paket [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) oder das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="2853f-140">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="2853f-141">Die Version des freigegebenen ASP.NET Core-Frameworks, die von der App als Ziel festgelegt ist, ist auf dem Zielcomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="2853f-141">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="2853f-142">500.0: Fehler beim Laden des prozessexternen Handlers</span><span class="sxs-lookup"><span data-stu-id="2853f-142">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="2853f-143">Der Workerprozess schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="2853f-143">The worker process fails.</span></span> <span data-ttu-id="2853f-144">Die App wird nicht gestartet.</span><span class="sxs-lookup"><span data-stu-id="2853f-144">The app doesn't start.</span></span>

<span data-ttu-id="2853f-145">Das ASP.NET Core-Modul kann den Out-of-Process-Hostinganforderungshandler nicht finden.</span><span class="sxs-lookup"><span data-stu-id="2853f-145">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="2853f-146">Stellen Sie sicher, dass sich *aspnetcorev2_outofprocess.dll* in einem Unterordner mit *aspnetcorev2.dll* befindet.</span><span class="sxs-lookup"><span data-stu-id="2853f-146">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span> 

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="2853f-147">500: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="2853f-147">500 Internal Server Error</span></span>

<span data-ttu-id="2853f-148">Die App wird gestartet, aber ein Fehler verhindert, dass der Server auf die Anforderung eingeht.</span><span class="sxs-lookup"><span data-stu-id="2853f-148">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="2853f-149">Dieser Fehler tritt im Code der App während des Starts oder bei der Erstellung einer Antwort auf.</span><span class="sxs-lookup"><span data-stu-id="2853f-149">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="2853f-150">Die Antwort enthält möglicherweise keinen Inhalt oder die Antwort wird als *500: Interner Serverfehler* im Browser angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2853f-150">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="2853f-151">Das Anwendungsereignisprotokoll gibt normalerweise an, dass die Anwendung normal gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="2853f-151">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="2853f-152">Aus Sicht des Servers ist dies richtig.</span><span class="sxs-lookup"><span data-stu-id="2853f-152">From the server's perspective, that's correct.</span></span> <span data-ttu-id="2853f-153">Die App wurde gestartet, sie kann jedoch keine gültige Antwort generieren.</span><span class="sxs-lookup"><span data-stu-id="2853f-153">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="2853f-154">[Führen Sie die App in einer Eingabeaufforderung](#run-the-app-at-a-command-prompt) auf dem Server aus oder [aktivieren Sie das stdout-Protokoll des ASP.NET Core-Moduls](#aspnet-core-module-stdout-log), um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="2853f-154">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="2853f-155">Verbindungszurücksetzung</span><span class="sxs-lookup"><span data-stu-id="2853f-155">Connection reset</span></span>

<span data-ttu-id="2853f-156">Falls ein Fehler auftritt, nachdem die Header gesendet wurden, ist es zu spät für den Server, einen **500: Interner Serverfehler** zu senden, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="2853f-156">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="2853f-157">Dies ist häufig der Fall, wenn während der Serialisierung komplexer Objekte für eine Antwort ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="2853f-157">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="2853f-158">Diese Art von Fehler wird angezeigt, wenn ein *Verbindungszurücksetzungsfehler* auf dem Client auftritt.</span><span class="sxs-lookup"><span data-stu-id="2853f-158">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="2853f-159">Mithilfe der [Anwendungsprotokollierung](xref:fundamentals/logging/index) können diese Fehlertypen behoben werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-159">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="2853f-160">Standardstarteinschränkungen</span><span class="sxs-lookup"><span data-stu-id="2853f-160">Default startup limits</span></span>

<span data-ttu-id="2853f-161">Das ASP.NET Core-Modul wurde mit dem Standardwert 120 Sekunden für das *StartupTimeLimit* konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2853f-161">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="2853f-162">Wenn der Standardwert beibehalten wird, dauert der Start einer App möglicherweise bis zu zwei Minuten. Erst danach kann das Modul einen Prozessfehler protokollieren.</span><span class="sxs-lookup"><span data-stu-id="2853f-162">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="2853f-163">Weitere Informationen zum Konfigurieren des Moduls finden Sie unter [Attribute des aspNetCore-Elements](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="2853f-163">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="2853f-164">Problembehandlung bei App-Startfehlern</span><span class="sxs-lookup"><span data-stu-id="2853f-164">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="2853f-165">Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="2853f-165">Application Event Log</span></span>

<span data-ttu-id="2853f-166">Greifen Sie auf das Anwendungsereignisprotokoll zu:</span><span class="sxs-lookup"><span data-stu-id="2853f-166">Access the Application Event Log:</span></span>

1. <span data-ttu-id="2853f-167">Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="2853f-167">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="2853f-168">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="2853f-168">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="2853f-169">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="2853f-169">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="2853f-170">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="2853f-170">Search for errors associated with the failing app.</span></span> <span data-ttu-id="2853f-171">Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.</span><span class="sxs-lookup"><span data-stu-id="2853f-171">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="2853f-172">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="2853f-172">Run the app at a command prompt</span></span>

<span data-ttu-id="2853f-173">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="2853f-173">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2853f-174">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="2853f-174">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="2853f-175">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="2853f-175">Framework-dependent deployment</span></span>

<span data-ttu-id="2853f-176">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="2853f-176">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="2853f-177">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen.</span><span class="sxs-lookup"><span data-stu-id="2853f-177">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="2853f-178">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="2853f-178">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="2853f-179">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="2853f-179">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2853f-180">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-180">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2853f-181">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="2853f-181">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2853f-182">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, hängt das Problem wahrscheinlich eher mit der Reverseproxykonfiguration und weniger mit der App selbst zusammen.</span><span class="sxs-lookup"><span data-stu-id="2853f-182">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the reverse proxy configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="2853f-183">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="2853f-183">Self-contained deployment</span></span>

<span data-ttu-id="2853f-184">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="2853f-184">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="2853f-185">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus.</span><span class="sxs-lookup"><span data-stu-id="2853f-185">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="2853f-186">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="2853f-186">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="2853f-187">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="2853f-187">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2853f-188">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-188">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2853f-189">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="2853f-189">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2853f-190">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, hängt das Problem wahrscheinlich eher mit der Reverseproxykonfiguration und weniger mit der App selbst zusammen.</span><span class="sxs-lookup"><span data-stu-id="2853f-190">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the reverse proxy configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="2853f-191">stdout-Protokoll des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="2853f-191">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="2853f-192">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="2853f-192">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2853f-193">Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="2853f-193">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2853f-194">Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-194">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="2853f-195">Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="2853f-195">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="2853f-196">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2853f-196">Edit the *web.config* file.</span></span> <span data-ttu-id="2853f-197">Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="2853f-197">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="2853f-198">`stdout` im Pfad ist das Präfix des Protokolldateinamens.</span><span class="sxs-lookup"><span data-stu-id="2853f-198">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="2853f-199">Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2853f-199">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="2853f-200">Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.</span><span class="sxs-lookup"><span data-stu-id="2853f-200">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span> 
1. <span data-ttu-id="2853f-201">Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.</span><span class="sxs-lookup"><span data-stu-id="2853f-201">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="2853f-202">Speichern Sie die aktualisierte Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2853f-202">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2853f-203">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="2853f-203">Make a request to the app.</span></span>
1. <span data-ttu-id="2853f-204">Navigieren Sie zu dem Ordner *logs*.</span><span class="sxs-lookup"><span data-stu-id="2853f-204">Navigate to the *logs* folder.</span></span> <span data-ttu-id="2853f-205">Suchen und öffnen Sie das aktuelle stdout-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="2853f-205">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="2853f-206">Untersuchen Sie das Protokoll auf Fehler.</span><span class="sxs-lookup"><span data-stu-id="2853f-206">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2853f-207">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="2853f-207">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="2853f-208">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2853f-208">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="2853f-209">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="2853f-209">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2853f-210">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="2853f-210">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="2853f-211">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="2853f-211">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2853f-212">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2853f-212">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2853f-213">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="2853f-213">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2853f-214">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="2853f-214">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="2853f-215">Aktivieren der Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="2853f-215">Enable the Developer Exception Page</span></span>

<span data-ttu-id="2853f-216">Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2853f-216">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="2853f-217">Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2853f-217">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="2853f-218">Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-218">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="2853f-219">Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="2853f-219">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="2853f-220">Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="2853f-220">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="2853f-221">Häufige Startfehler</span><span class="sxs-lookup"><span data-stu-id="2853f-221">Common startup errors</span></span>

<span data-ttu-id="2853f-222">Siehe <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="2853f-222">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="2853f-223">Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="2853f-223">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="2853f-224">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="2853f-224">Obtain data from an app</span></span>

<span data-ttu-id="2853f-225">Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals.</span><span class="sxs-lookup"><span data-stu-id="2853f-225">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="2853f-226">Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="2853f-226">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="2853f-227">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="2853f-227">Slow or hanging app</span></span>

<span data-ttu-id="2853f-228">Wenn eine App bei einer Anforderung langsam reagiert oder hängt, rufen Sie eine [Dumpdatei](/visualstudio/debugger/using-dump-files) ab und analysieren diese.</span><span class="sxs-lookup"><span data-stu-id="2853f-228">When an app responds slowly or hangs on a request, obtain and analyze a [dump file](/visualstudio/debugger/using-dump-files).</span></span> <span data-ttu-id="2853f-229">Dumpdateien können mit einem der folgenden Tools abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="2853f-229">Dump files can be obtained using any of the following tools:</span></span>

* [<span data-ttu-id="2853f-230">ProcDump</span><span class="sxs-lookup"><span data-stu-id="2853f-230">ProcDump</span></span>](/sysinternals/downloads/procdump)
* [<span data-ttu-id="2853f-231">DebugDiag</span><span class="sxs-lookup"><span data-stu-id="2853f-231">DebugDiag</span></span>](https://www.microsoft.com/download/details.aspx?id=49924)
* <span data-ttu-id="2853f-232">WinDbg: [Herunterladen von Debugtools für Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [Debuggen mit WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span><span class="sxs-lookup"><span data-stu-id="2853f-232">WinDbg: [Download Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [Debugging Using WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="2853f-233">Remotedebuggen</span><span class="sxs-lookup"><span data-stu-id="2853f-233">Remote debugging</span></span>

<span data-ttu-id="2853f-234">Siehe [Remotedebuggen von ASP.NET Core auf einem IIS-Remotecomputer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in der Dokumentation zu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2853f-234">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="2853f-235">Application Insights</span><span class="sxs-lookup"><span data-stu-id="2853f-235">Application Insights</span></span>

<span data-ttu-id="2853f-236">[Application Insights](/azure/application-insights/) stellt Telemetriedaten von Apps bereit, die von IIS gehostet werden, einschließlich Features für die Fehlerprotokollierung und die Berichterstellung.</span><span class="sxs-lookup"><span data-stu-id="2853f-236">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="2853f-237">Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-237">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="2853f-238">Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="2853f-238">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="2853f-239">Zusätzliche Hinweise</span><span class="sxs-lookup"><span data-stu-id="2853f-239">Additional advice</span></span>

<span data-ttu-id="2853f-240">Manchmal schlägt eine funktionsfähige App direkt nach der Durchführung eines Upgrades des .NET Core SDK auf dem Entwicklungscomputer oder der Paketversionen in der App fehl.</span><span class="sxs-lookup"><span data-stu-id="2853f-240">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="2853f-241">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-241">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="2853f-242">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="2853f-242">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="2853f-243">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="2853f-243">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="2853f-244">Löschen Sie die Paketcaches unter *%UserProfile%\\.nuget\\packages* und *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="2853f-244">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="2853f-245">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="2853f-245">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="2853f-246">Überprüfen Sie, ob die vorherige Bereitstellung auf dem Server vollständig gelöscht wurde, bevor Sie die App erneut bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2853f-246">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="2853f-247">Sie können Paketcaches ganz einfach löschen, indem Sie `dotnet nuget locals all --clear` über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="2853f-247">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="2853f-248">Darüber hinaus können Paketcaches mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und durch Ausführung des Befehls `nuget locals all -clear` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-248">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="2853f-249">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="2853f-249">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2853f-250">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2853f-250">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
