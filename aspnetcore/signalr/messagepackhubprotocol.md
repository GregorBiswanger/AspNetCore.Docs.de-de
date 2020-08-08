---
title: Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core
author: bradygaster
description: Fügen Sie ASP.net Core das messagepack-Hub-Protokoll hinzu SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 8e590c87f75d35cbafde1adbc87dea9c45eac92d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022549"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="7de81-103">Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7de81-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7de81-104">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7de81-105">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="7de81-105">What is MessagePack?</span></span>

<span data-ttu-id="7de81-106">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="7de81-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7de81-107">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7de81-108">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7de81-109">SignalRverfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7de81-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7de81-110">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="7de81-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="7de81-111">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="7de81-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7de81-112">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-113">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-113">JSON is enabled by default.</span></span> <span data-ttu-id="7de81-114">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="7de81-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7de81-115">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="7de81-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7de81-116">In diesem Delegaten kann die- `SerializerOptions` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7de81-117">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="7de81-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7de81-118">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7de81-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="7de81-119">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7de81-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7de81-120">Zum Beispiel wird `.WithSecurity(MessagePackSecurity.UntrustedData)` beim Ersetzen von aufgerufen `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="7de81-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7de81-121">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="7de81-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-122">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7de81-123">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7de81-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="7de81-124">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="7de81-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7de81-125">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-125">.NET client</span></span>

<span data-ttu-id="7de81-126">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7de81-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7de81-127">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="7de81-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7de81-128">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-128">JavaScript client</span></span>

<span data-ttu-id="7de81-129">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7de81-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7de81-130">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="7de81-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="7de81-131">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="7de81-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7de81-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7de81-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="7de81-133">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7de81-134">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7de81-135">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-136">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="7de81-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7de81-137">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7de81-138">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7de81-139">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7de81-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7de81-140">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="7de81-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="7de81-141">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="7de81-141">MessagePack quirks</span></span>

<span data-ttu-id="7de81-142">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="7de81-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7de81-143">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="7de81-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7de81-144">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="7de81-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7de81-145">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="7de81-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7de81-146">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="7de81-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7de81-147">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7de81-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7de81-148">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="7de81-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7de81-149">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7de81-150">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="7de81-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7de81-151">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="7de81-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7de81-152">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7de81-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7de81-153">Folglich konvertiert das messagepack-Hub-Protokoll beim Deserialisieren eines Datums in das UTC-Format, wenn der `DateTime.Kind` andernfalls die `DateTimeKind.Local` Zeit nicht berührt und unverändert übergibt.</span><span class="sxs-lookup"><span data-stu-id="7de81-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="7de81-154">Wenn Sie mit Werten arbeiten `DateTime` , empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="7de81-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7de81-155">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="7de81-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7de81-156">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7de81-157">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="7de81-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7de81-158">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="7de81-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7de81-159">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7de81-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7de81-160">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7de81-161">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="7de81-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7de81-162">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="7de81-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7de81-163">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7de81-164">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7de81-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7de81-165">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="7de81-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7de81-166">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7de81-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7de81-167">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7de81-168">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7de81-169">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="7de81-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="7de81-170">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7de81-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7de81-171">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="7de81-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7de81-172">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="7de81-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7de81-173">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7de81-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7de81-174">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="7de81-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7de81-175">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7de81-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="7de81-176">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7de81-176">Related resources</span></span>

* [<span data-ttu-id="7de81-177">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="7de81-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7de81-178">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7de81-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7de81-180">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7de81-181">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="7de81-181">What is MessagePack?</span></span>

<span data-ttu-id="7de81-182">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="7de81-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7de81-183">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7de81-184">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7de81-185">SignalRverfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7de81-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7de81-186">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="7de81-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="7de81-187">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="7de81-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7de81-188">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-189">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-189">JSON is enabled by default.</span></span> <span data-ttu-id="7de81-190">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="7de81-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7de81-191">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="7de81-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7de81-192">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7de81-193">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="7de81-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7de81-194">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7de81-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="7de81-195">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7de81-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7de81-196">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="7de81-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="7de81-197">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="7de81-198">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="7de81-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="7de81-199">Wenn `MessagePackSecurity.Active` nicht auf festgelegt ist `MessagePackSecurity.UntrustedData` , kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="7de81-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="7de81-200">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7de81-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7de81-201">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="7de81-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-202">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7de81-203">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7de81-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="7de81-204">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="7de81-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7de81-205">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-205">.NET client</span></span>

<span data-ttu-id="7de81-206">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7de81-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7de81-207">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="7de81-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7de81-208">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-208">JavaScript client</span></span>

<span data-ttu-id="7de81-209">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7de81-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7de81-210">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="7de81-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="7de81-211">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="7de81-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7de81-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7de81-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="7de81-213">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7de81-214">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7de81-215">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-216">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="7de81-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7de81-217">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7de81-218">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7de81-219">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7de81-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7de81-220">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="7de81-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="7de81-221">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="7de81-221">MessagePack quirks</span></span>

<span data-ttu-id="7de81-222">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="7de81-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7de81-223">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="7de81-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7de81-224">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="7de81-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7de81-225">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="7de81-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7de81-226">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="7de81-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7de81-227">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7de81-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7de81-228">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="7de81-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7de81-229">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7de81-230">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="7de81-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7de81-231">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="7de81-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7de81-232">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7de81-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7de81-233">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="7de81-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="7de81-234">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="7de81-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7de81-235">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="7de81-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="7de81-236">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="7de81-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7de81-237">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7de81-238">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="7de81-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7de81-239">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="7de81-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7de81-240">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7de81-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7de81-241">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7de81-242">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="7de81-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7de81-243">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="7de81-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7de81-244">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7de81-245">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7de81-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7de81-246">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="7de81-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7de81-247">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7de81-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7de81-248">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7de81-249">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7de81-250">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="7de81-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="7de81-251">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7de81-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7de81-252">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="7de81-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7de81-253">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="7de81-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7de81-254">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7de81-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7de81-255">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="7de81-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7de81-256">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7de81-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="7de81-257">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7de81-257">Related resources</span></span>

* [<span data-ttu-id="7de81-258">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="7de81-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7de81-259">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7de81-260">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7de81-261">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="7de81-262">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="7de81-262">What is MessagePack?</span></span>

<span data-ttu-id="7de81-263">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="7de81-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="7de81-264">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="7de81-265">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="7de81-266">SignalRverfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7de81-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="7de81-267">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="7de81-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="7de81-268">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="7de81-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="7de81-269">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-270">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-270">JSON is enabled by default.</span></span> <span data-ttu-id="7de81-271">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="7de81-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="7de81-272">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="7de81-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="7de81-273">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7de81-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="7de81-274">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="7de81-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="7de81-275">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7de81-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="7de81-276">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7de81-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="7de81-277">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="7de81-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="7de81-278">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="7de81-279">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="7de81-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="7de81-280">Wenn `MessagePackSecurity.Active` nicht auf festgelegt ist `MessagePackSecurity.UntrustedData` , kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="7de81-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="7de81-281">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7de81-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="7de81-282">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="7de81-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-283">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="7de81-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="7de81-284">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7de81-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="7de81-285">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="7de81-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="7de81-286">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-286">.NET client</span></span>

<span data-ttu-id="7de81-287">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="7de81-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="7de81-288">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="7de81-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="7de81-289">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-289">JavaScript client</span></span>

<span data-ttu-id="7de81-290">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7de81-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="7de81-291">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="7de81-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="7de81-292">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="7de81-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="7de81-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="7de81-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="7de81-294">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="7de81-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="7de81-295">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="7de81-296">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="7de81-297">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="7de81-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="7de81-298">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7de81-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="7de81-299">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="7de81-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="7de81-300">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7de81-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="7de81-301">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="7de81-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="7de81-302">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="7de81-302">MessagePack quirks</span></span>

<span data-ttu-id="7de81-303">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="7de81-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="7de81-304">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="7de81-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="7de81-305">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="7de81-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="7de81-306">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="7de81-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="7de81-307">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="7de81-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="7de81-308">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7de81-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="7de81-309">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="7de81-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="7de81-310">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7de81-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="7de81-311">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="7de81-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="7de81-312">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="7de81-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="7de81-313">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7de81-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="7de81-314">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="7de81-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="7de81-315">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="7de81-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="7de81-316">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="7de81-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="7de81-317">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="7de81-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="7de81-318">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="7de81-319">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="7de81-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="7de81-320">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="7de81-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="7de81-321">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="7de81-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="7de81-322">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7de81-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="7de81-323">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="7de81-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="7de81-324">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="7de81-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="7de81-325">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7de81-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="7de81-326">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="7de81-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="7de81-327">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="7de81-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="7de81-328">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="7de81-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="7de81-329">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="7de81-330">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="7de81-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="7de81-331">Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="7de81-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="7de81-332">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="7de81-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="7de81-333">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="7de81-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="7de81-334">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="7de81-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="7de81-335">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7de81-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="7de81-336">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="7de81-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="7de81-337">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="7de81-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="7de81-338">Zugehörige Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7de81-338">Related resources</span></span>

* [<span data-ttu-id="7de81-339">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="7de81-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7de81-340">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7de81-341">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="7de81-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
