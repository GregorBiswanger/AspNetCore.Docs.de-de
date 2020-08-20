---
title: SignalR-Konfiguration in ASP.NET Core
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core- SignalR apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625556"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="7e22a-103">SignalR-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e22a-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7e22a-104">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-105">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7e22a-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7e22a-106">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7e22a-107">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7e22a-108">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7e22a-109">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7e22a-110">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7e22a-111">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="7e22a-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7e22a-112">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7e22a-113">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7e22a-114">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-115">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7e22a-116">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="7e22a-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7e22a-117">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="7e22a-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7e22a-118">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-118">MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-119">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7e22a-120">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-121">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7e22a-122">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-122">Configure server options</span></span>

<span data-ttu-id="7e22a-123">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7e22a-124">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-124">Option</span></span> | <span data-ttu-id="7e22a-125">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-125">Default Value</span></span> | <span data-ttu-id="7e22a-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7e22a-127">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-127">30 seconds</span></span> | <span data-ttu-id="7e22a-128">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="7e22a-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7e22a-129">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7e22a-130">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7e22a-131">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-131">15 seconds</span></span> | <span data-ttu-id="7e22a-132">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7e22a-133">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-134">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-135">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-135">15 seconds</span></span> | <span data-ttu-id="7e22a-136">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7e22a-137">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="7e22a-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7e22a-138">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7e22a-139">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="7e22a-139">All installed protocols</span></span> | <span data-ttu-id="7e22a-140">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-140">Protocols supported by this hub.</span></span> <span data-ttu-id="7e22a-141">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7e22a-142">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7e22a-143">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7e22a-144">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7e22a-145">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7e22a-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-146">32 KB</span></span> | <span data-ttu-id="7e22a-147">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="7e22a-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7e22a-148">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="7e22a-149">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="7e22a-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7e22a-150">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7e22a-151">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7e22a-152">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="7e22a-153">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7e22a-154">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-154">Option</span></span> | <span data-ttu-id="7e22a-155">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-155">Default Value</span></span> | <span data-ttu-id="7e22a-156">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7e22a-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-157">32 KB</span></span> | <span data-ttu-id="7e22a-158">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7e22a-159">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7e22a-160">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7e22a-161">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7e22a-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-162">32 KB</span></span> | <span data-ttu-id="7e22a-163">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="7e22a-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7e22a-164">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7e22a-165">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-165">All Transports are enabled.</span></span> | <span data-ttu-id="7e22a-166">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7e22a-167">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-167">See below.</span></span> | <span data-ttu-id="7e22a-168">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="7e22a-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7e22a-169">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-169">See below.</span></span> | <span data-ttu-id="7e22a-170">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="7e22a-171">0</span><span class="sxs-lookup"><span data-stu-id="7e22a-171">0</span></span> | <span data-ttu-id="7e22a-172">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="7e22a-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="7e22a-173">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="7e22a-174">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7e22a-175">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-175">Option</span></span> | <span data-ttu-id="7e22a-176">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-176">Default Value</span></span> | <span data-ttu-id="7e22a-177">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7e22a-178">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-178">90 seconds</span></span> | <span data-ttu-id="7e22a-179">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7e22a-180">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7e22a-181">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7e22a-182">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-182">Option</span></span> | <span data-ttu-id="7e22a-183">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-183">Default Value</span></span> | <span data-ttu-id="7e22a-184">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7e22a-185">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-185">5 seconds</span></span> | <span data-ttu-id="7e22a-186">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7e22a-187">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7e22a-188">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7e22a-189">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-189">Configure client options</span></span>

<span data-ttu-id="7e22a-190">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="7e22a-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7e22a-191">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="7e22a-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7e22a-192">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-192">Configure logging</span></span>

<span data-ttu-id="7e22a-193">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7e22a-194">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7e22a-195">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-196">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7e22a-197">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="7e22a-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7e22a-198">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="7e22a-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7e22a-199">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7e22a-200">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7e22a-201">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7e22a-202">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7e22a-203">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7e22a-204">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7e22a-205">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-205">The following table lists the available log levels.</span></span> <span data-ttu-id="7e22a-206">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7e22a-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7e22a-207">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7e22a-208">String</span><span class="sxs-lookup"><span data-stu-id="7e22a-208">String</span></span>                      | <span data-ttu-id="7e22a-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7e22a-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7e22a-210">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="7e22a-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7e22a-211">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="7e22a-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7e22a-212">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="7e22a-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7e22a-213">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7e22a-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7e22a-214">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7e22a-215">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7e22a-216">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7e22a-217">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="7e22a-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7e22a-218">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7e22a-219">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e22a-219">Configure allowed transports</span></span>

<span data-ttu-id="7e22a-220">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7e22a-221">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7e22a-222">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-222">All transports are enabled by default.</span></span>

<span data-ttu-id="7e22a-223">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7e22a-224">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7e22a-225">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7e22a-226">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7e22a-227">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-228">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="7e22a-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7e22a-229">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-229">Configure bearer authentication</span></span>

<span data-ttu-id="7e22a-230">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7e22a-231">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7e22a-232">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="7e22a-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7e22a-233">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7e22a-234">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7e22a-235">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="7e22a-236">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7e22a-237">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7e22a-238">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7e22a-239">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7e22a-240">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7e22a-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-241">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-242">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-242">Option</span></span> | <span data-ttu-id="7e22a-243">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-243">Default value</span></span> | <span data-ttu-id="7e22a-244">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7e22a-245">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-246">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-246">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-247">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-248">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-249">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-250">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-250">15 seconds</span></span> | <span data-ttu-id="7e22a-251">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-252">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-253">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-254">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-255">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-255">15 seconds</span></span> | <span data-ttu-id="7e22a-256">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-257">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-258">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7e22a-259">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7e22a-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-261">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-261">Option</span></span> | <span data-ttu-id="7e22a-262">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-262">Default value</span></span> | <span data-ttu-id="7e22a-263">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7e22a-264">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-265">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-265">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-266">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7e22a-267">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-268">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7e22a-269">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-270">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-271">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-272">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-273">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-273">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-274">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-274">Option</span></span> | <span data-ttu-id="7e22a-275">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-275">Default value</span></span> | <span data-ttu-id="7e22a-276">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7e22a-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7e22a-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7e22a-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7e22a-278">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-279">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-279">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-280">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-281">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-282">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7e22a-283">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-283">15 seconds</span></span> | <span data-ttu-id="7e22a-284">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-285">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-286">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-287">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7e22a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7e22a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7e22a-289">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-290">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-291">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-292">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7e22a-293">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-293">Configure additional options</span></span>

<span data-ttu-id="7e22a-294">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7e22a-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-295">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-296">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-296">.NET Option</span></span> |  <span data-ttu-id="7e22a-297">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-297">Default value</span></span> | <span data-ttu-id="7e22a-298">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7e22a-299">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7e22a-300">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-301">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-302">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7e22a-303">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-303">Empty</span></span> | <span data-ttu-id="7e22a-304">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7e22a-305">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-305">Empty</span></span> | <span data-ttu-id="7e22a-306">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7e22a-307">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-307">Empty</span></span> | <span data-ttu-id="7e22a-308">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7e22a-309">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-309">5 seconds</span></span> | <span data-ttu-id="7e22a-310">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="7e22a-310">WebSockets only.</span></span> <span data-ttu-id="7e22a-311">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7e22a-312">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7e22a-313">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-313">Empty</span></span> | <span data-ttu-id="7e22a-314">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7e22a-315">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7e22a-316">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="7e22a-317">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="7e22a-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7e22a-318">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="7e22a-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7e22a-319">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="7e22a-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7e22a-320">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7e22a-321">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="7e22a-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7e22a-322">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7e22a-323">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7e22a-324">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7e22a-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-326">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-326">JavaScript Option</span></span> | <span data-ttu-id="7e22a-327">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-327">Default Value</span></span> | <span data-ttu-id="7e22a-328">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7e22a-329">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="7e22a-330">Das Wörterbuch der mit jeder HTTP-Anforderung gesendeten Header.</span><span class="sxs-lookup"><span data-stu-id="7e22a-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="7e22a-331">Das Senden von Headern im Browser funktioniert nicht für websockets oder den <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="7e22a-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7e22a-332">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7e22a-333">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-334">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-335">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="7e22a-336">Gibt an, ob Anmelde Informationen mit der cors-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="7e22a-337">Azure App Service verwendet cookie s für persistente Sitzungen und erfordert, dass diese Option ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="7e22a-338">Weitere Informationen zu cors mit SignalR finden Sie unter <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="7e22a-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-339">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-339">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-340">Java-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-340">Java Option</span></span> | <span data-ttu-id="7e22a-341">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-341">Default Value</span></span> | <span data-ttu-id="7e22a-342">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7e22a-343">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7e22a-344">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-345">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-346">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7e22a-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7e22a-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7e22a-348">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-348">Empty</span></span> | <span data-ttu-id="7e22a-349">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7e22a-350">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7e22a-351">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7e22a-352">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7e22a-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7e22a-353">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e22a-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7e22a-354">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-355">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7e22a-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7e22a-356">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7e22a-357">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7e22a-358">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7e22a-359">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7e22a-360">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7e22a-361">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="7e22a-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7e22a-362">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7e22a-363">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7e22a-364">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-365">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7e22a-366">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="7e22a-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7e22a-367">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="7e22a-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7e22a-368">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-368">MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-369">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7e22a-370">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-371">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7e22a-372">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-372">Configure server options</span></span>

<span data-ttu-id="7e22a-373">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7e22a-374">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-374">Option</span></span> | <span data-ttu-id="7e22a-375">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-375">Default Value</span></span> | <span data-ttu-id="7e22a-376">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7e22a-377">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-377">30 seconds</span></span> | <span data-ttu-id="7e22a-378">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="7e22a-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7e22a-379">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7e22a-380">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7e22a-381">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-381">15 seconds</span></span> | <span data-ttu-id="7e22a-382">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7e22a-383">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-384">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-385">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-385">15 seconds</span></span> | <span data-ttu-id="7e22a-386">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7e22a-387">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="7e22a-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7e22a-388">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7e22a-389">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="7e22a-389">All installed protocols</span></span> | <span data-ttu-id="7e22a-390">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-390">Protocols supported by this hub.</span></span> <span data-ttu-id="7e22a-391">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7e22a-392">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7e22a-393">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7e22a-394">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7e22a-395">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7e22a-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-396">32 KB</span></span> | <span data-ttu-id="7e22a-397">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="7e22a-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7e22a-398">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="7e22a-399">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="7e22a-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7e22a-400">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7e22a-401">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7e22a-402">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="7e22a-403">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7e22a-404">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-404">Option</span></span> | <span data-ttu-id="7e22a-405">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-405">Default Value</span></span> | <span data-ttu-id="7e22a-406">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7e22a-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-407">32 KB</span></span> | <span data-ttu-id="7e22a-408">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7e22a-409">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7e22a-410">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7e22a-411">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7e22a-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-412">32 KB</span></span> | <span data-ttu-id="7e22a-413">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="7e22a-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7e22a-414">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7e22a-415">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-415">All Transports are enabled.</span></span> | <span data-ttu-id="7e22a-416">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7e22a-417">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-417">See below.</span></span> | <span data-ttu-id="7e22a-418">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="7e22a-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7e22a-419">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-419">See below.</span></span> | <span data-ttu-id="7e22a-420">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="7e22a-421">0</span><span class="sxs-lookup"><span data-stu-id="7e22a-421">0</span></span> | <span data-ttu-id="7e22a-422">Geben Sie die Mindestversion des Aushandlungs Protokolls an.</span><span class="sxs-lookup"><span data-stu-id="7e22a-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="7e22a-423">Hiermit werden Clients auf neuere Versionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="7e22a-424">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7e22a-425">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-425">Option</span></span> | <span data-ttu-id="7e22a-426">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-426">Default Value</span></span> | <span data-ttu-id="7e22a-427">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7e22a-428">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-428">90 seconds</span></span> | <span data-ttu-id="7e22a-429">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7e22a-430">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7e22a-431">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7e22a-432">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-432">Option</span></span> | <span data-ttu-id="7e22a-433">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-433">Default Value</span></span> | <span data-ttu-id="7e22a-434">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7e22a-435">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-435">5 seconds</span></span> | <span data-ttu-id="7e22a-436">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7e22a-437">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7e22a-438">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7e22a-439">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-439">Configure client options</span></span>

<span data-ttu-id="7e22a-440">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="7e22a-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7e22a-441">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="7e22a-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7e22a-442">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-442">Configure logging</span></span>

<span data-ttu-id="7e22a-443">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7e22a-444">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7e22a-445">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-446">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7e22a-447">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="7e22a-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7e22a-448">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="7e22a-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7e22a-449">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7e22a-450">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7e22a-451">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7e22a-452">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7e22a-453">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7e22a-454">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7e22a-455">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-455">The following table lists the available log levels.</span></span> <span data-ttu-id="7e22a-456">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7e22a-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7e22a-457">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7e22a-458">String</span><span class="sxs-lookup"><span data-stu-id="7e22a-458">String</span></span>                      | <span data-ttu-id="7e22a-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7e22a-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7e22a-460">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="7e22a-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7e22a-461">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="7e22a-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7e22a-462">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="7e22a-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7e22a-463">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7e22a-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7e22a-464">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7e22a-465">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7e22a-466">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7e22a-467">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="7e22a-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7e22a-468">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7e22a-469">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e22a-469">Configure allowed transports</span></span>

<span data-ttu-id="7e22a-470">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7e22a-471">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7e22a-472">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-472">All transports are enabled by default.</span></span>

<span data-ttu-id="7e22a-473">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7e22a-474">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7e22a-475">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7e22a-476">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7e22a-477">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-478">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="7e22a-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7e22a-479">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-479">Configure bearer authentication</span></span>

<span data-ttu-id="7e22a-480">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7e22a-481">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7e22a-482">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="7e22a-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7e22a-483">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7e22a-484">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7e22a-485">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="7e22a-486">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7e22a-487">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7e22a-488">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7e22a-489">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7e22a-490">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7e22a-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-491">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-492">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-492">Option</span></span> | <span data-ttu-id="7e22a-493">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-493">Default value</span></span> | <span data-ttu-id="7e22a-494">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7e22a-495">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-496">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-496">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-497">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-498">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-499">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-500">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-500">15 seconds</span></span> | <span data-ttu-id="7e22a-501">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-502">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-503">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-504">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-505">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-505">15 seconds</span></span> | <span data-ttu-id="7e22a-506">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-507">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-508">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7e22a-509">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7e22a-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-511">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-511">Option</span></span> | <span data-ttu-id="7e22a-512">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-512">Default value</span></span> | <span data-ttu-id="7e22a-513">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7e22a-514">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-515">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-515">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-516">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7e22a-517">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-518">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7e22a-519">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-520">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-521">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-522">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-523">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-523">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-524">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-524">Option</span></span> | <span data-ttu-id="7e22a-525">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-525">Default value</span></span> | <span data-ttu-id="7e22a-526">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7e22a-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7e22a-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7e22a-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7e22a-528">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-529">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-529">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-530">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-531">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-532">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7e22a-533">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-533">15 seconds</span></span> | <span data-ttu-id="7e22a-534">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-535">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-536">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-537">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7e22a-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7e22a-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7e22a-539">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-540">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-541">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-542">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7e22a-543">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-543">Configure additional options</span></span>

<span data-ttu-id="7e22a-544">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7e22a-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-545">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-546">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-546">.NET Option</span></span> |  <span data-ttu-id="7e22a-547">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-547">Default value</span></span> | <span data-ttu-id="7e22a-548">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7e22a-549">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7e22a-550">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-551">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-552">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7e22a-553">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-553">Empty</span></span> | <span data-ttu-id="7e22a-554">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7e22a-555">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-555">Empty</span></span> | <span data-ttu-id="7e22a-556">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7e22a-557">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-557">Empty</span></span> | <span data-ttu-id="7e22a-558">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7e22a-559">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-559">5 seconds</span></span> | <span data-ttu-id="7e22a-560">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="7e22a-560">WebSockets only.</span></span> <span data-ttu-id="7e22a-561">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7e22a-562">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7e22a-563">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-563">Empty</span></span> | <span data-ttu-id="7e22a-564">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7e22a-565">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7e22a-566">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="7e22a-567">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="7e22a-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7e22a-568">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="7e22a-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7e22a-569">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="7e22a-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7e22a-570">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7e22a-571">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="7e22a-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7e22a-572">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7e22a-573">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7e22a-574">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7e22a-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-576">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-576">JavaScript Option</span></span> | <span data-ttu-id="7e22a-577">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-577">Default Value</span></span> | <span data-ttu-id="7e22a-578">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7e22a-579">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7e22a-580">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7e22a-581">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-582">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-583">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-584">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-584">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-585">Java-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-585">Java Option</span></span> | <span data-ttu-id="7e22a-586">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-586">Default Value</span></span> | <span data-ttu-id="7e22a-587">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7e22a-588">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7e22a-589">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-590">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-591">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7e22a-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7e22a-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7e22a-593">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-593">Empty</span></span> | <span data-ttu-id="7e22a-594">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7e22a-595">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7e22a-596">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7e22a-597">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7e22a-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7e22a-598">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e22a-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7e22a-599">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-600">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7e22a-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7e22a-601">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7e22a-602">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7e22a-603">`AddJsonProtocol`kann nach [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in hinzugefügt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7e22a-604">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7e22a-605">Die [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) -Eigenschaft für dieses Objekt ist ein- `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7e22a-606">Weitere Informationen finden Sie in den [System.Text.Jszur Dokumentation](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="7e22a-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7e22a-607">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass die Groß-/Kleinschreibung von Eigenschaftsnamen nicht geändert wird. verwenden Sie dazu den folgenden Code in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="7e22a-608">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7e22a-609">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-610">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7e22a-611">Zum Newtonsoft.Jswechseln</span><span class="sxs-lookup"><span data-stu-id="7e22a-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7e22a-612">Wenn Sie Features von benötigen `Newtonsoft.Json` , die in nicht unterstützt `System.Text.Json` werden, finden Sie weitere Informationen unter [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="7e22a-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7e22a-613">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-613">MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-614">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7e22a-615">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-616">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7e22a-617">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-617">Configure server options</span></span>

<span data-ttu-id="7e22a-618">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7e22a-619">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-619">Option</span></span> | <span data-ttu-id="7e22a-620">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-620">Default Value</span></span> | <span data-ttu-id="7e22a-621">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7e22a-622">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-622">30 seconds</span></span> | <span data-ttu-id="7e22a-623">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="7e22a-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7e22a-624">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7e22a-625">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7e22a-626">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-626">15 seconds</span></span> | <span data-ttu-id="7e22a-627">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7e22a-628">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-629">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-630">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-630">15 seconds</span></span> | <span data-ttu-id="7e22a-631">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7e22a-632">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="7e22a-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7e22a-633">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7e22a-634">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="7e22a-634">All installed protocols</span></span> | <span data-ttu-id="7e22a-635">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-635">Protocols supported by this hub.</span></span> <span data-ttu-id="7e22a-636">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7e22a-637">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7e22a-638">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7e22a-639">Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7e22a-640">Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7e22a-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-641">32 KB</span></span> | <span data-ttu-id="7e22a-642">Maximale Größe einer einzelnen eingehenden Hub-Nachricht.</span><span class="sxs-lookup"><span data-stu-id="7e22a-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="7e22a-643">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="7e22a-644">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="7e22a-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7e22a-645">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7e22a-646">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7e22a-647">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="7e22a-648">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7e22a-649">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-649">Option</span></span> | <span data-ttu-id="7e22a-650">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-650">Default Value</span></span> | <span data-ttu-id="7e22a-651">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7e22a-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-652">32 KB</span></span> | <span data-ttu-id="7e22a-653">Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7e22a-654">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7e22a-655">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7e22a-656">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7e22a-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-657">32 KB</span></span> | <span data-ttu-id="7e22a-658">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird</span><span class="sxs-lookup"><span data-stu-id="7e22a-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7e22a-659">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7e22a-660">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-660">All Transports are enabled.</span></span> | <span data-ttu-id="7e22a-661">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7e22a-662">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-662">See below.</span></span> | <span data-ttu-id="7e22a-663">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="7e22a-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7e22a-664">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-664">See below.</span></span> | <span data-ttu-id="7e22a-665">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7e22a-666">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7e22a-667">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-667">Option</span></span> | <span data-ttu-id="7e22a-668">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-668">Default Value</span></span> | <span data-ttu-id="7e22a-669">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7e22a-670">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-670">90 seconds</span></span> | <span data-ttu-id="7e22a-671">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7e22a-672">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7e22a-673">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7e22a-674">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-674">Option</span></span> | <span data-ttu-id="7e22a-675">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-675">Default Value</span></span> | <span data-ttu-id="7e22a-676">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7e22a-677">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-677">5 seconds</span></span> | <span data-ttu-id="7e22a-678">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7e22a-679">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7e22a-680">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7e22a-681">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-681">Configure client options</span></span>

<span data-ttu-id="7e22a-682">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="7e22a-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7e22a-683">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="7e22a-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7e22a-684">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-684">Configure logging</span></span>

<span data-ttu-id="7e22a-685">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7e22a-686">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7e22a-687">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-688">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7e22a-689">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="7e22a-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7e22a-690">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="7e22a-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7e22a-691">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7e22a-692">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7e22a-693">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7e22a-694">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="7e22a-695">Anstelle eines `LogLevel` Werts können Sie auch einen Wert angeben, der den `string` Namen einer Protokollebene darstellt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7e22a-696">Dies ist nützlich, wenn SignalR Sie die Protokollierung in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die `LogLevel` Konstanten haben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7e22a-697">In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-697">The following table lists the available log levels.</span></span> <span data-ttu-id="7e22a-698">Der von Ihnen bereitgestellte Wert, `configureLogging` der die **minimale** Protokollierung festlegt, die protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7e22a-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7e22a-699">Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7e22a-700">String</span><span class="sxs-lookup"><span data-stu-id="7e22a-700">String</span></span>                      | <span data-ttu-id="7e22a-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7e22a-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7e22a-702">`info`**oder**`information`</span><span class="sxs-lookup"><span data-stu-id="7e22a-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7e22a-703">`warn`**oder**`warning`</span><span class="sxs-lookup"><span data-stu-id="7e22a-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="7e22a-704">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="7e22a-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7e22a-705">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7e22a-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7e22a-706">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7e22a-707">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7e22a-708">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7e22a-709">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="7e22a-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7e22a-710">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7e22a-711">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e22a-711">Configure allowed transports</span></span>

<span data-ttu-id="7e22a-712">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7e22a-713">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7e22a-714">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-714">All transports are enabled by default.</span></span>

<span data-ttu-id="7e22a-715">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7e22a-716">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7e22a-717">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="7e22a-718">Im Java-Client wird der Transport mit der-Methode im ausgewählt `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7e22a-719">Der Java-Client verwendet standardmäßig den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-720">Der SignalR Java-Client unterstützt noch keinen Transport Fall Back.</span><span class="sxs-lookup"><span data-stu-id="7e22a-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7e22a-721">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-721">Configure bearer authentication</span></span>

<span data-ttu-id="7e22a-722">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7e22a-723">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7e22a-724">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="7e22a-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7e22a-725">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7e22a-726">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7e22a-727">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="7e22a-728">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7e22a-729">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7e22a-730">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7e22a-731">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7e22a-732">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7e22a-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-733">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-734">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-734">Option</span></span> | <span data-ttu-id="7e22a-735">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-735">Default value</span></span> | <span data-ttu-id="7e22a-736">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7e22a-737">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-738">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-738">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-739">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-740">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-741">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-742">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-742">15 seconds</span></span> | <span data-ttu-id="7e22a-743">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-744">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-745">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-746">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-747">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-747">15 seconds</span></span> | <span data-ttu-id="7e22a-748">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-749">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-750">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7e22a-751">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7e22a-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-753">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-753">Option</span></span> | <span data-ttu-id="7e22a-754">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-754">Default value</span></span> | <span data-ttu-id="7e22a-755">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7e22a-756">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-757">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-757">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-758">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7e22a-759">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-760">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7e22a-761">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-762">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-763">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-764">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-765">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-765">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-766">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-766">Option</span></span> | <span data-ttu-id="7e22a-767">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-767">Default value</span></span> | <span data-ttu-id="7e22a-768">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7e22a-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7e22a-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7e22a-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7e22a-770">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-771">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-771">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-772">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-773">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-774">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7e22a-775">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-775">15 seconds</span></span> | <span data-ttu-id="7e22a-776">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-777">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-778">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-779">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7e22a-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7e22a-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7e22a-781">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-782">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-783">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-784">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7e22a-785">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-785">Configure additional options</span></span>

<span data-ttu-id="7e22a-786">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7e22a-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-787">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-788">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-788">.NET Option</span></span> |  <span data-ttu-id="7e22a-789">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-789">Default value</span></span> | <span data-ttu-id="7e22a-790">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7e22a-791">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7e22a-792">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-793">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-794">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7e22a-795">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-795">Empty</span></span> | <span data-ttu-id="7e22a-796">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7e22a-797">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-797">Empty</span></span> | <span data-ttu-id="7e22a-798">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7e22a-799">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-799">Empty</span></span> | <span data-ttu-id="7e22a-800">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7e22a-801">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-801">5 seconds</span></span> | <span data-ttu-id="7e22a-802">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="7e22a-802">WebSockets only.</span></span> <span data-ttu-id="7e22a-803">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7e22a-804">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7e22a-805">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-805">Empty</span></span> | <span data-ttu-id="7e22a-806">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7e22a-807">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7e22a-808">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="7e22a-809">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="7e22a-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7e22a-810">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="7e22a-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7e22a-811">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="7e22a-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7e22a-812">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7e22a-813">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="7e22a-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7e22a-814">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7e22a-815">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7e22a-816">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7e22a-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-818">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-818">JavaScript Option</span></span> | <span data-ttu-id="7e22a-819">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-819">Default Value</span></span> | <span data-ttu-id="7e22a-820">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7e22a-821">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7e22a-822">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7e22a-823">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-824">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-825">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-826">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-826">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-827">Java-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-827">Java Option</span></span> | <span data-ttu-id="7e22a-828">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-828">Default Value</span></span> | <span data-ttu-id="7e22a-829">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7e22a-830">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7e22a-831">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-832">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-833">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7e22a-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7e22a-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7e22a-835">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-835">Empty</span></span> | <span data-ttu-id="7e22a-836">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7e22a-837">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7e22a-838">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7e22a-839">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7e22a-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7e22a-840">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e22a-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7e22a-841">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-842">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7e22a-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7e22a-843">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7e22a-844">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem SignalR Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7e22a-845">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7e22a-846">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7e22a-847">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="7e22a-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7e22a-848">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7e22a-849">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7e22a-850">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-851">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7e22a-852">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-852">MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-853">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7e22a-854">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-855">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7e22a-856">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-856">Configure server options</span></span>

<span data-ttu-id="7e22a-857">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7e22a-858">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-858">Option</span></span> | <span data-ttu-id="7e22a-859">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-859">Default Value</span></span> | <span data-ttu-id="7e22a-860">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7e22a-861">30 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-861">30 seconds</span></span> | <span data-ttu-id="7e22a-862">Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="7e22a-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7e22a-863">Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7e22a-864">Der empfohlene Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7e22a-865">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-865">15 seconds</span></span> | <span data-ttu-id="7e22a-866">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7e22a-867">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-868">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-869">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-869">15 seconds</span></span> | <span data-ttu-id="7e22a-870">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7e22a-871">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="7e22a-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7e22a-872">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7e22a-873">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="7e22a-873">All installed protocols</span></span> | <span data-ttu-id="7e22a-874">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-874">Protocols supported by this hub.</span></span> <span data-ttu-id="7e22a-875">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7e22a-876">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7e22a-877">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7e22a-878">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="7e22a-879">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="7e22a-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7e22a-880">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7e22a-881">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7e22a-882">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="7e22a-883">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7e22a-884">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-884">Option</span></span> | <span data-ttu-id="7e22a-885">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-885">Default Value</span></span> | <span data-ttu-id="7e22a-886">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7e22a-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-887">32 KB</span></span> | <span data-ttu-id="7e22a-888">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7e22a-889">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="7e22a-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7e22a-890">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7e22a-891">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7e22a-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-892">32 KB</span></span> | <span data-ttu-id="7e22a-893">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7e22a-894">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="7e22a-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7e22a-895">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-895">All Transports are enabled.</span></span> | <span data-ttu-id="7e22a-896">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7e22a-897">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-897">See below.</span></span> | <span data-ttu-id="7e22a-898">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="7e22a-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7e22a-899">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-899">See below.</span></span> | <span data-ttu-id="7e22a-900">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7e22a-901">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7e22a-902">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-902">Option</span></span> | <span data-ttu-id="7e22a-903">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-903">Default Value</span></span> | <span data-ttu-id="7e22a-904">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7e22a-905">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-905">90 seconds</span></span> | <span data-ttu-id="7e22a-906">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7e22a-907">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7e22a-908">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7e22a-909">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-909">Option</span></span> | <span data-ttu-id="7e22a-910">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-910">Default Value</span></span> | <span data-ttu-id="7e22a-911">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7e22a-912">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-912">5 seconds</span></span> | <span data-ttu-id="7e22a-913">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7e22a-914">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7e22a-915">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7e22a-916">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-916">Configure client options</span></span>

<span data-ttu-id="7e22a-917">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="7e22a-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7e22a-918">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="7e22a-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7e22a-919">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-919">Configure logging</span></span>

<span data-ttu-id="7e22a-920">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7e22a-921">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7e22a-922">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-923">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7e22a-924">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="7e22a-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7e22a-925">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="7e22a-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7e22a-926">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7e22a-927">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7e22a-928">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7e22a-929">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-930">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="7e22a-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7e22a-931">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7e22a-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7e22a-932">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7e22a-933">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7e22a-934">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7e22a-935">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="7e22a-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7e22a-936">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7e22a-937">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e22a-937">Configure allowed transports</span></span>

<span data-ttu-id="7e22a-938">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7e22a-939">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7e22a-940">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-940">All transports are enabled by default.</span></span>

<span data-ttu-id="7e22a-941">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7e22a-942">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="7e22a-943">In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7e22a-944">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-944">Configure bearer authentication</span></span>

<span data-ttu-id="7e22a-945">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7e22a-946">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7e22a-947">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="7e22a-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7e22a-948">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7e22a-949">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7e22a-950">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="7e22a-951">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7e22a-952">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7e22a-953">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7e22a-954">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7e22a-955">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7e22a-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-956">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-957">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-957">Option</span></span> | <span data-ttu-id="7e22a-958">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-958">Default value</span></span> | <span data-ttu-id="7e22a-959">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7e22a-960">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-961">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-961">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-962">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-963">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-964">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-965">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-965">15 seconds</span></span> | <span data-ttu-id="7e22a-966">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-967">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-968">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-969">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-970">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-970">15 seconds</span></span> | <span data-ttu-id="7e22a-971">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-972">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-973">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7e22a-974">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7e22a-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-976">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-976">Option</span></span> | <span data-ttu-id="7e22a-977">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-977">Default value</span></span> | <span data-ttu-id="7e22a-978">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7e22a-979">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-980">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-980">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-981">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7e22a-982">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-983">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7e22a-984">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-985">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-986">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-987">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-988">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-988">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-989">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-989">Option</span></span> | <span data-ttu-id="7e22a-990">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-990">Default value</span></span> | <span data-ttu-id="7e22a-991">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7e22a-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7e22a-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7e22a-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7e22a-993">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-994">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-994">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-995">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-996">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-997">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7e22a-998">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-998">15 seconds</span></span> | <span data-ttu-id="7e22a-999">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-1000">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-1001">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-1002">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7e22a-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7e22a-1004">15 Sekunden (15.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-1005">Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7e22a-1006">Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7e22a-1007">Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7e22a-1008">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1008">Configure additional options</span></span>

<span data-ttu-id="7e22a-1009">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-1011">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1011">.NET Option</span></span> |  <span data-ttu-id="7e22a-1012">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1012">Default value</span></span> | <span data-ttu-id="7e22a-1013">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7e22a-1014">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7e22a-1015">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1016">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1017">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7e22a-1018">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1018">Empty</span></span> | <span data-ttu-id="7e22a-1019">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7e22a-1020">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1020">Empty</span></span> | <span data-ttu-id="7e22a-1021">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7e22a-1022">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1022">Empty</span></span> | <span data-ttu-id="7e22a-1023">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7e22a-1024">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1024">5 seconds</span></span> | <span data-ttu-id="7e22a-1025">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1025">WebSockets only.</span></span> <span data-ttu-id="7e22a-1026">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7e22a-1027">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7e22a-1028">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1028">Empty</span></span> | <span data-ttu-id="7e22a-1029">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7e22a-1030">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7e22a-1031">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="7e22a-1032">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7e22a-1033">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7e22a-1034">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="7e22a-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7e22a-1035">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7e22a-1036">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7e22a-1037">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7e22a-1038">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7e22a-1039">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7e22a-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-1041">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1041">JavaScript Option</span></span> | <span data-ttu-id="7e22a-1042">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1042">Default Value</span></span> | <span data-ttu-id="7e22a-1043">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7e22a-1044">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7e22a-1045">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7e22a-1046">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1047">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1048">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-1049">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-1050">Java-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1050">Java Option</span></span> | <span data-ttu-id="7e22a-1051">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1051">Default Value</span></span> | <span data-ttu-id="7e22a-1052">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7e22a-1053">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7e22a-1054">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1055">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1056">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7e22a-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7e22a-1058">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1058">Empty</span></span> | <span data-ttu-id="7e22a-1059">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7e22a-1060">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7e22a-1061">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7e22a-1062">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7e22a-1063">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7e22a-1064">JSON/messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-1065">ASP.net Core SignalR unterstützt zwei Protokolle zum Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7e22a-1066">Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="7e22a-1067">Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [dem SignalR Hinzufügen](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in der Methode hinzugefügt werden kann `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7e22a-1068">Die- `AddJsonProtocol` Methode nimmt einen Delegaten an, der ein- `options` Objekt empfängt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7e22a-1069">Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.net- `JsonSerializerSettings` Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7e22a-1070">Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7e22a-1071">Verwenden Sie beispielsweise den folgenden Code in, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7e22a-1072">Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7e22a-1073">Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-1074">Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7e22a-1075">Messagepack-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1075">MessagePack serialization options</span></span>

<span data-ttu-id="7e22a-1076">Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7e22a-1077">Weitere Informationen finden Sie [unter SignalR messagepack in](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-1078">Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7e22a-1079">Konfigurieren von Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1079">Configure server options</span></span>

<span data-ttu-id="7e22a-1080">In der folgenden Tabelle werden die Optionen zum Konfigurieren von SignalR Hubs beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="7e22a-1081">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1081">Option</span></span> | <span data-ttu-id="7e22a-1082">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1082">Default Value</span></span> | <span data-ttu-id="7e22a-1083">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-1084">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1084">15 seconds</span></span> | <span data-ttu-id="7e22a-1085">Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7e22a-1086">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-1087">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7e22a-1088">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1088">15 seconds</span></span> | <span data-ttu-id="7e22a-1089">Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7e22a-1090">Ändern Sie `KeepAliveInterval` die `ServerTimeout` / `serverTimeoutInMilliseconds` Einstellung auf dem Client, wenn Sie sich ändern.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7e22a-1091">Der empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert ist Double- `KeepAliveInterval` Wert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7e22a-1092">Alle installierten Protokolle</span><span class="sxs-lookup"><span data-stu-id="7e22a-1092">All installed protocols</span></span> | <span data-ttu-id="7e22a-1093">Protokolle, die von diesem Hub unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="7e22a-1094">Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7e22a-1095">Wenn der Wert `true` ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7e22a-1096">Der Standardwert ist `false` , da diese Ausnahme Meldungen vertrauliche Informationen enthalten können.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="7e22a-1097">Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für den Aufruf in bereitgestellt wird `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="7e22a-1098">Optionen für einen einzelnen Hub überschreiben die in bereitgestellten globalen Optionen `AddSignalR` und können mithilfe von konfiguriert werden <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7e22a-1099">Erweiterte http-Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="7e22a-1100">Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7e22a-1101">Diese Optionen werden konfiguriert, indem ein [Delegat an \<T> maphub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="7e22a-1102">In der folgenden Tabelle werden die Optionen zum Konfigurieren der SignalR erweiterten http-Optionen von ASP.net Core beschrieben:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="7e22a-1103">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1103">Option</span></span> | <span data-ttu-id="7e22a-1104">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1104">Default Value</span></span> | <span data-ttu-id="7e22a-1105">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7e22a-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-1106">32 KB</span></span> | <span data-ttu-id="7e22a-1107">Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7e22a-1108">Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7e22a-1109">Daten, die automatisch von den `Authorize` auf die Hub-Klasse angewendeten Attributen gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7e22a-1110">Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7e22a-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="7e22a-1111">32 KB</span></span> | <span data-ttu-id="7e22a-1112">Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7e22a-1113">Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7e22a-1114">Alle Transporte sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1114">All Transports are enabled.</span></span> | <span data-ttu-id="7e22a-1115">Ein Bitflags-Enumeration von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7e22a-1116">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1116">See below.</span></span> | <span data-ttu-id="7e22a-1117">Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7e22a-1118">Siehe unten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1118">See below.</span></span> | <span data-ttu-id="7e22a-1119">Zusätzliche Optionen für den websockets-Transport.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="7e22a-1120">Der lange Abruf Transport bietet zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `LongPolling` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7e22a-1121">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1121">Option</span></span> | <span data-ttu-id="7e22a-1122">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1122">Default Value</span></span> | <span data-ttu-id="7e22a-1123">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7e22a-1124">90 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1124">90 seconds</span></span> | <span data-ttu-id="7e22a-1125">Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7e22a-1126">Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7e22a-1127">Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der-Eigenschaft konfiguriert werden können `WebSockets` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7e22a-1128">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1128">Option</span></span> | <span data-ttu-id="7e22a-1129">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1129">Default Value</span></span> | <span data-ttu-id="7e22a-1130">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7e22a-1131">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1131">5 seconds</span></span> | <span data-ttu-id="7e22a-1132">Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7e22a-1133">Ein Delegat, der verwendet werden kann, um den- `Sec-WebSocket-Protocol` Header auf einen benutzerdefinierten Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7e22a-1134">Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7e22a-1135">Konfigurieren von Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1135">Configure client options</span></span>

<span data-ttu-id="7e22a-1136">Client Optionen können für den Typ konfiguriert werden `HubConnectionBuilder` (verfügbar in den .net-und JavaScript-Clients).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7e22a-1137">Sie steht auch im Java-Client zur Verfügung, aber die `HttpHubConnectionBuilder` Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7e22a-1138">Konfigurieren der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-1138">Configure logging</span></span>

<span data-ttu-id="7e22a-1139">Die Protokollierung wird mithilfe der-Methode im .NET-Client konfiguriert `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7e22a-1140">Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7e22a-1141">Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7e22a-1142">Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7e22a-1143">Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7e22a-1144">Um z. b. die Konsolen Protokollierung zu aktivieren, installieren Sie das `Microsoft.Extensions.Logging.Console` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7e22a-1145">Die `AddConsole` Erweiterungsmethode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7e22a-1146">Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7e22a-1147">Geben `LogLevel` Sie einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7e22a-1148">Protokolle werden in das Browser Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7e22a-1149">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="7e22a-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7e22a-1150">Weitere Informationen zur Protokollierung finden Sie in der [ SignalR Diagnose Dokumentation](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7e22a-1151">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7e22a-1152">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7e22a-1153">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7e22a-1154">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7e22a-1155">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7e22a-1156">Zulässige Transporte konfigurieren</span><span class="sxs-lookup"><span data-stu-id="7e22a-1156">Configure allowed transports</span></span>

<span data-ttu-id="7e22a-1157">Die von verwendeten Transporte SignalR können im-Befehl `WithUrl` ( `withUrl` in JavaScript) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7e22a-1158">Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7e22a-1159">Alle Transporte sind standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="7e22a-1160">So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7e22a-1161">Im JavaScript-Client werden Transporte durch Festlegen des- `transport` Felds für das Options-Objekt konfiguriert, das für bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7e22a-1162">Konfigurieren der bearerauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="7e22a-1162">Configure bearer authentication</span></span>

<span data-ttu-id="7e22a-1163">Um Authentifizierungsdaten zusammen mit Anforderungen bereitzustellen SignalR , verwenden `AccessTokenProvider` Sie die-Option ( `accessTokenFactory` in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7e22a-1164">Im .NET-Client wird dieses Zugriffs Token als http-tokenauthentifizierungstoken (mit dem- `Authorization` Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7e22a-1165">Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7e22a-1166">In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert bereitgestellt `access_token` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7e22a-1167">Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in angegeben werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7e22a-1168">Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das- `accessTokenFactory` Feld für das Options-Objekt in festgelegt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="7e22a-1169">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7e22a-1170">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7e22a-1171">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7e22a-1172">Konfigurieren von Timeout-und Keep-Alive-Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7e22a-1173">Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für das `HubConnection` Objekt selbst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-1175">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1175">Option</span></span> | <span data-ttu-id="7e22a-1176">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1176">Default value</span></span> | <span data-ttu-id="7e22a-1177">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7e22a-1178">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-1179">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1179">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-1180">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-1181">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-1182">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7e22a-1183">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1183">15 seconds</span></span> | <span data-ttu-id="7e22a-1184">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-1185">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis `onclose` aus (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7e22a-1186">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-1187">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="7e22a-1188">Im .NET-Client werden Timeout Werte als Werte angegeben `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="7e22a-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-1190">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1190">Option</span></span> | <span data-ttu-id="7e22a-1191">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1191">Default value</span></span> | <span data-ttu-id="7e22a-1192">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7e22a-1193">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-1194">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1194">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-1195">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onclose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7e22a-1196">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-1197">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers `KeepAliveInterval` entspricht, um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-1198">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-1199">Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1199">Option</span></span> | <span data-ttu-id="7e22a-1200">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1200">Default value</span></span> | <span data-ttu-id="7e22a-1201">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7e22a-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7e22a-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7e22a-1203">30 Sekunden (30.000 Millisekunden)</span><span class="sxs-lookup"><span data-stu-id="7e22a-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7e22a-1204">Timeout für die Serveraktivität.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1204">Timeout for server activity.</span></span> <span data-ttu-id="7e22a-1205">Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst das `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-1206">Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7e22a-1207">Der empfohlene Wert ist eine Zahl, die mindestens dem Wert des Servers entspricht `KeepAliveInterval` , um Zeit für das Eintreffen von Pings zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7e22a-1208">15 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1208">15 seconds</span></span> | <span data-ttu-id="7e22a-1209">Timeout für den anfänglichen Server Hand Shake.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="7e22a-1210">Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das- `onClose` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7e22a-1211">Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7e22a-1212">Weitere Details zum Hand Shake Prozess finden Sie in der [ SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7e22a-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="7e22a-1213">Konfigurieren zusätzlicher Optionen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1213">Configure additional options</span></span>

<span data-ttu-id="7e22a-1214">Zusätzliche Optionen können in der- `WithUrl` Methode ( `withUrl` in JavaScript) für `HubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem `HttpHubConnectionBuilder` im Java-Client konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7e22a-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="7e22a-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="7e22a-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7e22a-1216">.Net-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1216">.NET Option</span></span> |  <span data-ttu-id="7e22a-1217">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1217">Default value</span></span> | <span data-ttu-id="7e22a-1218">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7e22a-1219">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7e22a-1220">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1221">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1222">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7e22a-1223">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1223">Empty</span></span> | <span data-ttu-id="7e22a-1224">Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7e22a-1225">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1225">Empty</span></span> | <span data-ttu-id="7e22a-1226">Eine Auflistung von http- cookie s, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7e22a-1227">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1227">Empty</span></span> | <span data-ttu-id="7e22a-1228">Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7e22a-1229">5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1229">5 seconds</span></span> | <span data-ttu-id="7e22a-1230">Nur websockets.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1230">WebSockets only.</span></span> <span data-ttu-id="7e22a-1231">Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7e22a-1232">Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7e22a-1233">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1233">Empty</span></span> | <span data-ttu-id="7e22a-1234">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7e22a-1235">Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7e22a-1236">Wird nicht für WebSocket-Verbindungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="7e22a-1237">Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7e22a-1238">Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7e22a-1239">**Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookie s und Header) nicht für den neuen Handler.**</span><span class="sxs-lookup"><span data-stu-id="7e22a-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7e22a-1240">Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7e22a-1241">Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden</span><span class="sxs-lookup"><span data-stu-id="7e22a-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7e22a-1242">Dies ermöglicht die Verwendung der Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7e22a-1243">Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7e22a-1244">Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="7e22a-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e22a-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7e22a-1246">JavaScript-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1246">JavaScript Option</span></span> | <span data-ttu-id="7e22a-1247">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1247">Default Value</span></span> | <span data-ttu-id="7e22a-1248">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7e22a-1249">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="7e22a-1250">Legen Sie auf fest, `true` um die Bytes/Zeichen der vom Client gesendeten und empfangenen Nachrichten zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7e22a-1251">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1252">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1253">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="7e22a-1254">Java</span><span class="sxs-lookup"><span data-stu-id="7e22a-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="7e22a-1255">Java-Option</span><span class="sxs-lookup"><span data-stu-id="7e22a-1255">Java Option</span></span> | <span data-ttu-id="7e22a-1256">Standardwert</span><span class="sxs-lookup"><span data-stu-id="7e22a-1256">Default Value</span></span> | <span data-ttu-id="7e22a-1257">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7e22a-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7e22a-1258">Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt</span><span class="sxs-lookup"><span data-stu-id="7e22a-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7e22a-1259">Legen Sie dies auf fest, `true` um den Aushandlungs Schritt zu überspringen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7e22a-1260">Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7e22a-1261">Diese Einstellung kann nicht aktiviert werden, wenn der Azure-Dienst verwendet wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="7e22a-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7e22a-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7e22a-1263">Leer</span><span class="sxs-lookup"><span data-stu-id="7e22a-1263">Empty</span></span> | <span data-ttu-id="7e22a-1264">Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7e22a-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7e22a-1265">Im .NET-Client können diese Optionen durch den für bereitgestellten Options Delegaten geändert werden `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7e22a-1266">Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, das für Folgendes bereitgestellt wird `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="7e22a-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7e22a-1267">Im Java-Client können diese Optionen mit den Methoden auf der konfiguriert werden, die `HttpHubConnectionBuilder` von der `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7e22a-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7e22a-1268">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7e22a-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
