---
title: ASP.NET Core SignalR-JavaScript-client
author: tdykstra
description: Übersicht über ASP.NET Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/javascript-client
ms.openlocfilehash: cd64a65889227d84615768bc3d8fddcd362fbba4
ms.sourcegitcommit: eef99d14d96dc8c3c1bb0e2c4cb14da152f8a952
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53022478"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="f9c49-103">ASP.NET Core SignalR-JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="f9c49-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="f9c49-104">Von [Rachel Appel](http://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="f9c49-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="f9c49-105">Die ASP.NET Core SignalR JavaScript-Clientbibliothek ermöglicht Entwicklern von serverseitigen hubcode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f9c49-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="f9c49-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9c49-106">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="f9c49-107">Installieren Sie das Paket der SignalR-client</span><span class="sxs-lookup"><span data-stu-id="f9c49-107">Install the SignalR client package</span></span>

<span data-ttu-id="f9c49-108">Die SignalR-JavaScript-Clientbibliothek wird bereitgestellt, als ein [Npm](https://www.npmjs.com/) Paket.</span><span class="sxs-lookup"><span data-stu-id="f9c49-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="f9c49-109">Wenn Sie Visual Studio verwenden, führen Sie `npm install` aus der **-Paket-Manager-Konsole** während Sie sich in den Stammordner.</span><span class="sxs-lookup"><span data-stu-id="f9c49-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="f9c49-110">Führen Sie für Visual Studio Code, den Befehl in der **integriertes Terminal**.</span><span class="sxs-lookup"><span data-stu-id="f9c49-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="f9c49-111">Npm installiert den Inhalt des Pakets in der *"node_modules"\\@aspnet\signalr\dist\browser* Ordner.</span><span class="sxs-lookup"><span data-stu-id="f9c49-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="f9c49-112">Erstellen Sie einen neuen Ordner namens *Signalr* unter der *"Wwwroot"\\Lib* Ordner.</span><span class="sxs-lookup"><span data-stu-id="f9c49-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="f9c49-113">Kopieren der *signalr.js* -Datei in die *Wwwroot\lib\signalr* Ordner.</span><span class="sxs-lookup"><span data-stu-id="f9c49-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="f9c49-114">Verwenden Sie den SignalR JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="f9c49-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="f9c49-115">Verweisen auf den SignalR JavaScript-Client in der `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="f9c49-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="f9c49-116">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="f9c49-116">Connect to a hub</span></span>

<span data-ttu-id="f9c49-117">Der folgende Code erstellt und startet eine Verbindung.</span><span class="sxs-lookup"><span data-stu-id="f9c49-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="f9c49-118">Die Hub-Name ist Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="f9c49-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

### <a name="cross-origin-connections"></a><span data-ttu-id="f9c49-119">Cross-Origin-Verbindungen</span><span class="sxs-lookup"><span data-stu-id="f9c49-119">Cross-origin connections</span></span>

<span data-ttu-id="f9c49-120">Browser werden in der Regel Verbindungen aus der gleichen Domäne wie die angeforderte Seite laden.</span><span class="sxs-lookup"><span data-stu-id="f9c49-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="f9c49-121">Es gibt jedoch Situationen, wenn eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="f9c49-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="f9c49-122">Um zu verhindern, dass eine schädliche Website sensible Daten von einem anderen Standort lesen [Cross-Origin-Verbindungen](xref:security/cors) sind standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f9c49-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="f9c49-123">Um eine cors-Anforderung zu ermöglichen, aktivieren Sie ihn in das `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="f9c49-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="f9c49-124">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="f9c49-124">Call hub methods from client</span></span>

<span data-ttu-id="f9c49-125">JavaScript-Clients rufen Sie öffentliche Methoden für Hubs über die [Aufrufen](/javascript/api/%40aspnet/signalr/hubconnection#invoke) Methode der [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="f9c49-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="f9c49-126">Die `invoke` -Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="f9c49-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="f9c49-127">Der Name der hubmethode.</span><span class="sxs-lookup"><span data-stu-id="f9c49-127">The name of the hub method.</span></span> <span data-ttu-id="f9c49-128">Im folgenden Beispiel wird der Name der Methode auf dem Hub `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="f9c49-129">In der hubmethode definierten Argumente.</span><span class="sxs-lookup"><span data-stu-id="f9c49-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="f9c49-130">Im folgenden Beispiel wird der Name des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-130">In the following example, the argument name is `message`.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="f9c49-131">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="f9c49-131">Call client methods from hub</span></span>

<span data-ttu-id="f9c49-132">Zum Empfangen von Nachrichten aus dem Hub Definieren einer Methode mit dem [auf](/javascript/api/%40aspnet/signalr/hubconnection#on) Methode der `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-132">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="f9c49-133">Der Name des Client-JavaScript-Methode.</span><span class="sxs-lookup"><span data-stu-id="f9c49-133">The name of the JavaScript client method.</span></span> <span data-ttu-id="f9c49-134">Im folgenden Beispiel wird der Name der Methode `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-134">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="f9c49-135">Argumente übergibt der Hub, an die Methode.</span><span class="sxs-lookup"><span data-stu-id="f9c49-135">Arguments the hub passes to the method.</span></span> <span data-ttu-id="f9c49-136">Im folgenden Beispiel wird den Wert des Arguments `message`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-136">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="f9c49-137">Der vorangehende Code in `connection.on` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) Methode.</span><span class="sxs-lookup"><span data-stu-id="f9c49-137">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="f9c49-138">SignalR bestimmt, welche Clientmethode aufrufen, die nach übereinstimmenden Namen der Methode und Argumente, die in definierten `SendAsync` und `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-138">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="f9c49-139">Rufen Sie als bewährte Methode, die [starten](/javascript/api/%40aspnet/signalr/hubconnection#start) Methode für die `HubConnection` nach `on`.</span><span class="sxs-lookup"><span data-stu-id="f9c49-139">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="f9c49-140">Dadurch wird sichergestellt, dass die Handler registriert sind, bevor alle Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="f9c49-140">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="f9c49-141">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f9c49-141">Error handling and logging</span></span>

<span data-ttu-id="f9c49-142">Kette eine `catch` Methode am Ende der `start` Methode zum Behandeln von clientseitigen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="f9c49-142">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="f9c49-143">Verwendung `console.error` zu Ausgabefehler in der Browserkonsole.</span><span class="sxs-lookup"><span data-stu-id="f9c49-143">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=43-45)]

<span data-ttu-id="f9c49-144">Einrichten von clientseitigen Protokollierung, Ablaufverfolgung durch Übergeben einer Protokollierung und der Typ des Ereignisses zu protokollieren, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f9c49-144">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="f9c49-145">Nachrichten werden mit der angegebenen Ebene oder höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="f9c49-145">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="f9c49-146">Verfügbaren Protokollebenen lauten wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f9c49-146">Available log levels are as follows:</span></span>

* <span data-ttu-id="f9c49-147">`signalR.LogLevel.Error` &ndash; Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="f9c49-147">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="f9c49-148">Protokolle `Error` nur Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="f9c49-148">Logs `Error` messages only.</span></span>
* <span data-ttu-id="f9c49-149">`signalR.LogLevel.Warning` &ndash; Warnmeldungen zu potenziellen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="f9c49-149">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="f9c49-150">Protokolle `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="f9c49-150">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f9c49-151">`signalR.LogLevel.Information` &ndash; Statusmeldungen, die ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="f9c49-151">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="f9c49-152">Protokolle `Information`, `Warning`, und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="f9c49-152">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="f9c49-153">`signalR.LogLevel.Trace` &ndash; Ablaufverfolgungsmeldungen Sie aus.</span><span class="sxs-lookup"><span data-stu-id="f9c49-153">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="f9c49-154">Protokolliert alle Elemente einschließlich Daten, die zwischen Hub und dem Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="f9c49-154">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="f9c49-155">Verwenden der ["configurelogging"](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) Methode [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) so konfigurieren Sie die Protokollebene.</span><span class="sxs-lookup"><span data-stu-id="f9c49-155">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="f9c49-156">Nachrichten werden in der Browserkonsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="f9c49-156">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="f9c49-157">Verbinden von clients</span><span class="sxs-lookup"><span data-stu-id="f9c49-157">Reconnect clients</span></span>

<span data-ttu-id="f9c49-158">Für SignalR JavaScript-Client nicht automatisch erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="f9c49-158">The JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="f9c49-159">Sie müssen Code schreiben, die den Client manuell neu verbunden wird.</span><span class="sxs-lookup"><span data-stu-id="f9c49-159">You must write code that will reconnect your client manually.</span></span> <span data-ttu-id="f9c49-160">Der folgende Code veranschaulicht einen typischen verbindungswiederherstellungs-Ansatz:</span><span class="sxs-lookup"><span data-stu-id="f9c49-160">The following code demonstrates a typical reconnection approach:</span></span>

1. <span data-ttu-id="f9c49-161">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="f9c49-161">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="f9c49-162">Rufen Sie die `start` -Funktion in der Verbindungs `onclose` -Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="f9c49-162">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="f9c49-163">Eine Implementierung unter realen Bedingungen würde ein Exponentielles Backoff verwendet, oder Wiederholen Sie eine angegebene Anzahl von Malen, bevor aufgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f9c49-163">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="f9c49-164">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f9c49-164">Additional resources</span></span>

* [<span data-ttu-id="f9c49-165">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="f9c49-165">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="f9c49-166">JavaScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="f9c49-166">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f9c49-167">Webpack- und TypeScript-tutorial</span><span class="sxs-lookup"><span data-stu-id="f9c49-167">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="f9c49-168">Hubs</span><span class="sxs-lookup"><span data-stu-id="f9c49-168">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f9c49-169">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="f9c49-169">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f9c49-170">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="f9c49-170">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="f9c49-171">Cross-Origin Requests (CORS)</span><span class="sxs-lookup"><span data-stu-id="f9c49-171">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
