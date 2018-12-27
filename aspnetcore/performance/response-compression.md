---
title: Antwortkomprimierung in ASP.NET Core
author: guardrex
description: Informationen zur Antwortkomprimierung und zum Verwenden von Antworten komprimierender Middleware in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: performance/response-compression
ms.openlocfilehash: 51ab51652a7b3f9b4ef97b3abbffe2e398c0bfb5
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637754"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="3f32e-103">Antwortkomprimierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f32e-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="3f32e-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3f32e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3f32e-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3f32e-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3f32e-106">Die Netzwerkbandbreite ist eine eingeschränkte Ressource.</span><span class="sxs-lookup"><span data-stu-id="3f32e-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="3f32e-107">Verringern der Größe der Antwort in der Regel erhöht die Reaktionsfähigkeit einer App, häufig erheblich.</span><span class="sxs-lookup"><span data-stu-id="3f32e-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="3f32e-108">Eine Möglichkeit zum Reduzieren der Größe der Nutzlast ist zum Komprimieren von Antworten von der app.</span><span class="sxs-lookup"><span data-stu-id="3f32e-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="3f32e-109">Antworten komprimierende Middleware verwenden</span><span class="sxs-lookup"><span data-stu-id="3f32e-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="3f32e-110">Verwenden Sie Server-basierte Antwort komprimierungstechnologien in IIS, Apache oder Nginx.</span><span class="sxs-lookup"><span data-stu-id="3f32e-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="3f32e-111">Die Leistung der Middleware wird nicht mit dem der Servermodule wahrscheinlich überein.</span><span class="sxs-lookup"><span data-stu-id="3f32e-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="3f32e-112">[HTTP.sys-Server](xref:fundamentals/servers/httpsys) Server und [Kestrel](xref:fundamentals/servers/kestrel) Server keine integrierte komprimierungsunterstützung derzeit anbieten.</span><span class="sxs-lookup"><span data-stu-id="3f32e-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="3f32e-113">Verwenden Sie Antworten komprimierende Middleware, wenn Sie sich befinden:</span><span class="sxs-lookup"><span data-stu-id="3f32e-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="3f32e-114">Kann nicht die folgenden Server-basierten komprimierungstechnologien verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="3f32e-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="3f32e-115">Dynamische Komprimierung in IIS-Modul</span><span class="sxs-lookup"><span data-stu-id="3f32e-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="3f32e-116">Apache Mod_deflate-Modul</span><span class="sxs-lookup"><span data-stu-id="3f32e-116">Apache mod_deflate module</span></span>](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="3f32e-117">Nginx-Komprimierung und Dekomprimierung</span><span class="sxs-lookup"><span data-stu-id="3f32e-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="3f32e-118">Hosten direkt auf:</span><span class="sxs-lookup"><span data-stu-id="3f32e-118">Hosting directly on:</span></span>
  * <span data-ttu-id="3f32e-119">[HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als "WebListener" bezeichnet)</span><span class="sxs-lookup"><span data-stu-id="3f32e-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="3f32e-120">Kestrel-server</span><span class="sxs-lookup"><span data-stu-id="3f32e-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="3f32e-121">Antwortkomprimierung</span><span class="sxs-lookup"><span data-stu-id="3f32e-121">Response compression</span></span>

<span data-ttu-id="3f32e-122">In der Regel kann alle Antworten, die nicht komprimierte antwortkomprimierung nutzen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="3f32e-123">Antworten, die in der Regel nicht komprimiert sind: CSS, JavaScript, HTML, XML und JSON.</span><span class="sxs-lookup"><span data-stu-id="3f32e-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="3f32e-124">Sie sollten nicht systemintern komprimierter Assets, wie z. B. PNG-Dateien komprimieren.</span><span class="sxs-lookup"><span data-stu-id="3f32e-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="3f32e-125">Wenn Sie versuchen, eine systemintern komprimierte Antwort weiter komprimiert, wird keine zusätzliche kleine Verringerung Größe und die Übertragung wahrscheinlich mit der Zeit, die zum Verarbeiten der Komprimierung benötigten überholt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="3f32e-126">Komprimieren Sie Dateien, die kleiner als ungefähr 150 – 1000 Bytes (je nach Inhalt der Datei und die Effizienz der Komprimierung) nicht.</span><span class="sxs-lookup"><span data-stu-id="3f32e-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="3f32e-127">Der Mehraufwand für das Komprimieren von kleinen Dateien kann es sich um eine komprimierte Datei, die größer als die nicht komprimierte Datei führen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="3f32e-128">Wenn ein Client komprimierten Inhalte verarbeiten kann, muss der Client den Server seiner Funktionen per informieren die `Accept-Encoding` Header mit der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="3f32e-129">Wenn ein Server komprimierten Inhalte sendet, muss Informationen enthalten die `Content-Encoding` Header wie die komprimierte Antwort codiert wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="3f32e-130">In der folgenden Tabelle werden die Inhalte Codierung Bezeichnungen, die von der Middleware unterstützt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="3f32e-131">`Accept-Encoding` Headerwerte</span><span class="sxs-lookup"><span data-stu-id="3f32e-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="3f32e-132">Middleware unterstützt</span><span class="sxs-lookup"><span data-stu-id="3f32e-132">Middleware Supported</span></span> | <span data-ttu-id="3f32e-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="3f32e-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="3f32e-134">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="3f32e-134">Yes (default)</span></span>        | [<span data-ttu-id="3f32e-135">Format der Brotli-komprimierte Daten</span><span class="sxs-lookup"><span data-stu-id="3f32e-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="3f32e-136">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-136">No</span></span>                   | [<span data-ttu-id="3f32e-137">Komprimierte Daten DEFLATE-format</span><span class="sxs-lookup"><span data-stu-id="3f32e-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="3f32e-138">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-138">No</span></span>                   | [<span data-ttu-id="3f32e-139">W3C effiziente XML-Austausch</span><span class="sxs-lookup"><span data-stu-id="3f32e-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="3f32e-140">Ja</span><span class="sxs-lookup"><span data-stu-id="3f32e-140">Yes</span></span>                  | [<span data-ttu-id="3f32e-141">GZIP-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="3f32e-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="3f32e-142">Ja</span><span class="sxs-lookup"><span data-stu-id="3f32e-142">Yes</span></span>                  | <span data-ttu-id="3f32e-143">"Keine Codierung"-ID: Die Antwort muss nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="3f32e-144">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-144">No</span></span>                   | [<span data-ttu-id="3f32e-145">Netzwerk-Übertragungsformat für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="3f32e-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="3f32e-146">Ja</span><span class="sxs-lookup"><span data-stu-id="3f32e-146">Yes</span></span>                  | <span data-ttu-id="3f32e-147">Alle verfügbaren Inhalte, die Codierung wird nicht explizit angefordert</span><span class="sxs-lookup"><span data-stu-id="3f32e-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="3f32e-148">`Accept-Encoding` Headerwerte</span><span class="sxs-lookup"><span data-stu-id="3f32e-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="3f32e-149">Middleware unterstützt</span><span class="sxs-lookup"><span data-stu-id="3f32e-149">Middleware Supported</span></span> | <span data-ttu-id="3f32e-150">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="3f32e-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="3f32e-151">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-151">No</span></span>                   | [<span data-ttu-id="3f32e-152">Format der Brotli-komprimierte Daten</span><span class="sxs-lookup"><span data-stu-id="3f32e-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="3f32e-153">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-153">No</span></span>                   | [<span data-ttu-id="3f32e-154">Komprimierte Daten DEFLATE-format</span><span class="sxs-lookup"><span data-stu-id="3f32e-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="3f32e-155">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-155">No</span></span>                   | [<span data-ttu-id="3f32e-156">W3C effiziente XML-Austausch</span><span class="sxs-lookup"><span data-stu-id="3f32e-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="3f32e-157">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="3f32e-157">Yes (default)</span></span>        | [<span data-ttu-id="3f32e-158">GZIP-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="3f32e-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="3f32e-159">Ja</span><span class="sxs-lookup"><span data-stu-id="3f32e-159">Yes</span></span>                  | <span data-ttu-id="3f32e-160">"Keine Codierung"-ID: Die Antwort muss nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="3f32e-161">Nein</span><span class="sxs-lookup"><span data-stu-id="3f32e-161">No</span></span>                   | [<span data-ttu-id="3f32e-162">Netzwerk-Übertragungsformat für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="3f32e-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="3f32e-163">Ja</span><span class="sxs-lookup"><span data-stu-id="3f32e-163">Yes</span></span>                  | <span data-ttu-id="3f32e-164">Alle verfügbaren Inhalte, die Codierung wird nicht explizit angefordert</span><span class="sxs-lookup"><span data-stu-id="3f32e-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="3f32e-165">Weitere Informationen finden Sie unter den [IANA offizielle Codierung Inhaltsliste](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="3f32e-165">For more information, see the [IANA Official Content Coding List](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="3f32e-166">Die Middleware können Sie zusätzliche Komprimierung-Anbieter für benutzerdefinierte hinzufügen `Accept-Encoding` Headerwerte.</span><span class="sxs-lookup"><span data-stu-id="3f32e-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="3f32e-167">Weitere Informationen finden Sie unter [benutzerdefinierte Anbieter](#custom-providers) unten.</span><span class="sxs-lookup"><span data-stu-id="3f32e-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="3f32e-168">Die Middleware der Reaktion auf Qualitätswert fähig ist (Qvalue, `q`) Gewichtung, wenn vom Client gesendet werden, um Komprimierungsschemas zu priorisieren.</span><span class="sxs-lookup"><span data-stu-id="3f32e-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="3f32e-169">Weitere Informationen finden Sie unter [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="3f32e-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="3f32e-170">Komprimierungsalgorithmen unterliegen bildet einen Kompromiss zwischen Geschwindigkeit von Komprimierung und die Effektivität der Komprimierung aus.</span><span class="sxs-lookup"><span data-stu-id="3f32e-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="3f32e-171">*Effektivität* in diesem Kontext bezieht sich auf die Größe der Ausgabe nach der Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="3f32e-172">Die kleinste Größe wird erreicht, indem die am häufigsten *optimale* Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="3f32e-173">Die Header, die beim anfordern, werden das Senden und Zwischenspeichern von komprimierten Inhalten empfangen in der folgenden Tabelle beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3f32e-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="3f32e-174">Header</span><span class="sxs-lookup"><span data-stu-id="3f32e-174">Header</span></span>             | <span data-ttu-id="3f32e-175">Rolle</span><span class="sxs-lookup"><span data-stu-id="3f32e-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="3f32e-176">Vom Client an den Server an, dass die inhaltscodierung Schemas zulässig ist, an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="3f32e-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="3f32e-177">Vom Server an den Client, um anzugeben, die Codierung des Inhalts in der Nutzlast gesendet.</span><span class="sxs-lookup"><span data-stu-id="3f32e-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="3f32e-178">Bei der Komprimierung werden die `Content-Length` Header entfernt werden, da der Inhalt Text geändert, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="3f32e-179">Bei der Komprimierung werden die `Content-MD5` Header entfernt, da der Inhalt des Nachrichtentexts geändert hat und der Hash nicht mehr gültig ist.</span><span class="sxs-lookup"><span data-stu-id="3f32e-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="3f32e-180">Gibt den MIME-Typ des Inhalts an.</span><span class="sxs-lookup"><span data-stu-id="3f32e-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="3f32e-181">Jede Antwort geben sollte seine `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="3f32e-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="3f32e-182">Die Middleware überprüft diesen Wert, um zu bestimmen, ob die Antwort, komprimiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="3f32e-183">Die Middleware gibt einen Satz von [Standard-MIME-Typen](#mime-types) , die codiert werden können, aber Sie können ersetzen oder MIME-Typen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="3f32e-184">Wenn vom Server mit dem Wert gesendet `Accept-Encoding` für Clients und -Proxys der `Vary` Header gibt an, an den Client oder Proxy, der zwischengespeichert werden soll (variieren) Antworten basierend auf den Wert der `Accept-Encoding` -Header der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="3f32e-185">Das Ergebnis der Rückgabe von Inhalten mit der `Vary: Accept-Encoding` -Header ist, dass sowohl komprimierte und nicht komprimierte Antworten getrennt zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="3f32e-186">Erkunden Sie die Funktionen des die Antworten komprimierende Middleware mit der [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="3f32e-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="3f32e-187">Das Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="3f32e-187">The sample illustrates:</span></span>

* <span data-ttu-id="3f32e-188">Die Komprimierung von app-Antworten, die mithilfe von Gzip und benutzerdefinierte Komprimierung-Anbietern.</span><span class="sxs-lookup"><span data-stu-id="3f32e-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="3f32e-189">Wie Sie die Standardliste der MIME-Typen für die Komprimierung einen MIME-Typ hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="3f32e-190">Package</span><span class="sxs-lookup"><span data-stu-id="3f32e-190">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3f32e-191">Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), wozu die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.</span><span class="sxs-lookup"><span data-stu-id="3f32e-191">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3f32e-192">Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage), wozu die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.</span><span class="sxs-lookup"><span data-stu-id="3f32e-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3f32e-193">Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.</span><span class="sxs-lookup"><span data-stu-id="3f32e-193">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="3f32e-194">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3f32e-194">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3f32e-195">Der folgende Code zeigt, wie Sie die Antworten komprimierende Middleware für die Standard-MIME-Typen und Komprimierung Anbieter aktivieren ([Brotli](#brotli-compression-provider) und [Gzip](#gzip-compression-provider)):</span><span class="sxs-lookup"><span data-stu-id="3f32e-195">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3f32e-196">Der folgende Code zeigt, wie Sie die Antworten komprimierende Middleware für Standard-MIME-Typen zu aktivieren und die [Gzip-Komprimierung Anbieter](#gzip-compression-provider):</span><span class="sxs-lookup"><span data-stu-id="3f32e-196">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

> [!NOTE]
> <span data-ttu-id="3f32e-197">Mit einem Tool wie [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), oder [Postman](https://www.getpostman.com/) Festlegen der `Accept-Encoding` Anforderungsheader und Untersuchen der Antwortheader, Größe und Text.</span><span class="sxs-lookup"><span data-stu-id="3f32e-197">Use a tool like [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="3f32e-198">Senden Sie eine Anforderung zur Beispiel-app ohne die `Accept-Encoding` Header und beobachten Sie, dass die Antwort nicht komprimiert.</span><span class="sxs-lookup"><span data-stu-id="3f32e-198">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="3f32e-199">Die `Content-Encoding` und `Vary` Header nicht in der Antwort vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3f32e-199">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler-Fenster und Ergebnis einer Anforderung ohne den Accept-Encoding-Header.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3f32e-202">Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: br` Header (Brotli-Komprimierung), und beobachten Sie, dass die Antwort komprimiert werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-202">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="3f32e-203">Die `Content-Encoding` und `Vary` -Header in der Antwort vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3f32e-203">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und den Br-Wert.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3f32e-207">Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: gzip` Header und beobachten Sie, dass die Antwort komprimiert werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-207">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="3f32e-208">Die `Content-Encoding` und `Vary` -Header in der Antwort vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3f32e-208">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von Gzip.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="3f32e-212">Anbieter</span><span class="sxs-lookup"><span data-stu-id="3f32e-212">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="3f32e-213">Anbieter der Brotli-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="3f32e-213">Brotli Compression Provider</span></span>

<span data-ttu-id="3f32e-214">Verwenden der `BrotliCompressionProvider` zum Komprimieren von Antworten mit dem die [Brotli komprimiertes Datenformat](https://tools.ietf.org/html/rfc7932).</span><span class="sxs-lookup"><span data-stu-id="3f32e-214">Use the `BrotliCompressionProvider` to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="3f32e-215">Wenn keine Komprimierung Anbieter explizit hinzugefügt werden die <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="3f32e-215">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="3f32e-216">Der Anbieter der Brotli-Komprimierung wird standardmäßig hinzugefügt, in das Array der Komprimierung Anbieter zusammen mit den [Gzip-Komprimierung Anbieter](#gzip-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="3f32e-216">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="3f32e-217">Komprimierung standardmäßig auf Brotli-Komprimierung auf, wenn das Format der Brotli-komprimierte Daten vom Client unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-217">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="3f32e-218">Wenn Brotli vom Client unterstützt wird, standardmäßig Komprimierung Gzip an, wenn der Client die Gzip-Komprimierung unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-218">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="3f32e-219">Der Brotoli Komprimierung-Anbieter muss hinzugefügt werden, wenn alle Anbieter Komprimierung explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="3f32e-219">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

<span data-ttu-id="3f32e-220">Stellen Sie die Komprimierung mit `BrotliCompressionProviderOptions`.</span><span class="sxs-lookup"><span data-stu-id="3f32e-220">Set the compression level with `BrotliCompressionProviderOptions`.</span></span> <span data-ttu-id="3f32e-221">Der Anbieter der Brotli-Komprimierung ist standardmäßig die schnellste Komprimierung ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), die möglicherweise nicht die effizienteste Komprimierung erzeugen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-221">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="3f32e-222">Wenn die effizienteste Komprimierung gewünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-222">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="3f32e-223">Komprimierungsgrad</span><span class="sxs-lookup"><span data-stu-id="3f32e-223">Compression Level</span></span> | <span data-ttu-id="3f32e-224">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="3f32e-224">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="3f32e-225">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="3f32e-225">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-226">Komprimierung sollte so schnell wie möglich abgeschlossen werden, auch wenn die resultierende Ausgabe optimal komprimiert ist nicht.</span><span class="sxs-lookup"><span data-stu-id="3f32e-226">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="3f32e-227">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="3f32e-227">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-228">Es sollte keine Komprimierung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-228">No compression should be performed.</span></span> |
| [<span data-ttu-id="3f32e-229">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="3f32e-229">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-230">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-230">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a><span data-ttu-id="3f32e-231">Anbieter der Gzip-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="3f32e-231">Gzip Compression Provider</span></span>

<span data-ttu-id="3f32e-232">Verwenden der <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> zum Komprimieren von Antworten mit dem die [Gzip-Dateiformat](https://tools.ietf.org/html/rfc1952).</span><span class="sxs-lookup"><span data-stu-id="3f32e-232">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="3f32e-233">Wenn keine Komprimierung Anbieter explizit hinzugefügt werden die <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="3f32e-233">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="3f32e-234">Der Anbieter der Gzip-Komprimierung wird standardmäßig hinzugefügt, in das Array der Komprimierung Anbieter zusammen mit den [Brotli-Komprimierung Anbieter](#brotli-compression-provider).</span><span class="sxs-lookup"><span data-stu-id="3f32e-234">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="3f32e-235">Komprimierung standardmäßig auf Brotli-Komprimierung auf, wenn das Format der Brotli-komprimierte Daten vom Client unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-235">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="3f32e-236">Wenn Brotli vom Client unterstützt wird, standardmäßig Komprimierung Gzip an, wenn der Client die Gzip-Komprimierung unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-236">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="3f32e-237">Der Anbieter der Gzip-Komprimierung wird standardmäßig in das Array der Komprimierung Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-237">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="3f32e-238">Komprimierung wird standardmäßig auf Gzip, wenn der Client die Gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-238">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="3f32e-239">Der Anbieter der Gzip-Komprimierung muss hinzugefügt werden, wenn alle Anbieter Komprimierung explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="3f32e-239">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

<span data-ttu-id="3f32e-240">Stellen Sie die Komprimierung mit <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="3f32e-240">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="3f32e-241">Der Anbieter der Gzip-Komprimierung ist standardmäßig die schnellste Komprimierung ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), die möglicherweise nicht die effizienteste Komprimierung erzeugen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-241">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="3f32e-242">Wenn die effizienteste Komprimierung gewünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-242">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="3f32e-243">Komprimierungsgrad</span><span class="sxs-lookup"><span data-stu-id="3f32e-243">Compression Level</span></span> | <span data-ttu-id="3f32e-244">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="3f32e-244">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="3f32e-245">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="3f32e-245">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-246">Komprimierung sollte so schnell wie möglich abgeschlossen werden, auch wenn die resultierende Ausgabe optimal komprimiert ist nicht.</span><span class="sxs-lookup"><span data-stu-id="3f32e-246">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="3f32e-247">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="3f32e-247">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-248">Es sollte keine Komprimierung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-248">No compression should be performed.</span></span> |
| [<span data-ttu-id="3f32e-249">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="3f32e-249">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="3f32e-250">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-250">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="3f32e-251">Benutzerdefinierte Anbieter</span><span class="sxs-lookup"><span data-stu-id="3f32e-251">Custom providers</span></span>

<span data-ttu-id="3f32e-252">Erstellen von benutzerdefinierten Komprimierung Implementierungen mit <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="3f32e-252">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="3f32e-253">Die <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> stellt den Inhalt, Codierung, das von diesem `ICompressionProvider` erzeugt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-253">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="3f32e-254">Die Middleware verwendet diese Informationen, die basierend auf der Liste, die im angegebenen Anbieter auswählen der `Accept-Encoding` -Header der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="3f32e-254">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="3f32e-255">Verwenden die Beispiel-app, die der Client sendet einer Anforderung mit der `Accept-Encoding: mycustomcompression` Header.</span><span class="sxs-lookup"><span data-stu-id="3f32e-255">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="3f32e-256">Die Middleware die Implementierung von benutzerdefinierten Komprimierung verwendet und gibt die Antwort mit einem `Content-Encoding: mycustomcompression` Header.</span><span class="sxs-lookup"><span data-stu-id="3f32e-256">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="3f32e-257">Der Client muss dekomprimiert werden, die benutzerdefinierte Codierung in der Reihenfolge für die Implementierung eines benutzerdefinierten Komprimierung arbeiten können.</span><span class="sxs-lookup"><span data-stu-id="3f32e-257">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

```csharp
public class CustomCompressionProvider : ICompressionProvider
{
    public string EncodingName => "mycustomcompression";
    public bool SupportsFlush => true;

    public Stream CreateStream(Stream outputStream)
    {
        // Create a custom compression stream wrapper here
        return outputStream;
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6,12-15)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="3f32e-258">Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: mycustomcompression` Header, und beobachten Sie die Header der Antwort.</span><span class="sxs-lookup"><span data-stu-id="3f32e-258">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="3f32e-259">Die `Vary` und `Content-Encoding` -Header in der Antwort vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3f32e-259">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="3f32e-260">Der Text der Antwort (nicht gezeigt) wird nicht durch das Beispiel komprimiert.</span><span class="sxs-lookup"><span data-stu-id="3f32e-260">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="3f32e-261">Es ist nicht in eine Implementierung von Komprimierung das `CustomCompressionProvider` Klasse des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="3f32e-261">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="3f32e-262">Das Beispiel zeigt jedoch, in denen Sie solche einen Komprimierungsalgorithmus implementieren würden.</span><span class="sxs-lookup"><span data-stu-id="3f32e-262">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von Mycustomcompression.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="3f32e-265">MIME-Typen</span><span class="sxs-lookup"><span data-stu-id="3f32e-265">MIME types</span></span>

<span data-ttu-id="3f32e-266">Die Middleware gibt eine Reihe von MIME-Typen für die Komprimierung:</span><span class="sxs-lookup"><span data-stu-id="3f32e-266">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="3f32e-267">Ersetzen Sie oder fügen Sie die MIME-Typen mit den Antworten komprimierende Middleware-Optionen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-267">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="3f32e-268">Beachten Sie diese Platzhalter-MIME-Typen, z. B. `text/*` werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3f32e-268">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="3f32e-269">Die Beispiel-app Fügt einen MIME-Typ für `image/svg+xml` und komprimiert und dient dem ASP.NET Core Bannerbild (*banner.svg*).</span><span class="sxs-lookup"><span data-stu-id="3f32e-269">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
        options.MimeTypes = 
            ResponseCompressionDefaults.MimeTypes.Concat(
                new[] { "image/svg+xml" });
    });
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-2.1"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="3f32e-270">Komprimierung mit sicheren Protokolls</span><span class="sxs-lookup"><span data-stu-id="3f32e-270">Compression with secure protocol</span></span>

<span data-ttu-id="3f32e-271">Komprimierte Antworten über sichere Verbindungen können gesteuert werden, mit der `EnableForHttps` Option, die standardmäßig deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="3f32e-271">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="3f32e-272">Dynamisch generierten Seiten mit Komprimierung Sicherheitsprobleme führen kann, wie z. B. die [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) und [SICHERHEITSVERLETZUNG](https://wikipedia.org/wiki/BREACH_(security_exploit)) Angriffe.</span><span class="sxs-lookup"><span data-stu-id="3f32e-272">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="3f32e-273">Den Vary-Header hinzufügen</span><span class="sxs-lookup"><span data-stu-id="3f32e-273">Adding the Vary header</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3f32e-274">Beim Komprimieren von Antworten basierend auf den `Accept-Encoding` -Header, es gibt potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version.</span><span class="sxs-lookup"><span data-stu-id="3f32e-274">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="3f32e-275">Um den Client und Proxy-Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden soll, die `Vary` Header hinzugefügt, um mit einem `Accept-Encoding` Wert.</span><span class="sxs-lookup"><span data-stu-id="3f32e-275">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="3f32e-276">In ASP.NET Core 2.0 oder höher, fügt die Middleware die `Vary` Header automatisch, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-276">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3f32e-277">Beim Komprimieren von Antworten basierend auf den `Accept-Encoding` -Header, es gibt potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version.</span><span class="sxs-lookup"><span data-stu-id="3f32e-277">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="3f32e-278">Um den Client und Proxy-Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden soll, die `Vary` Header hinzugefügt, um mit einem `Accept-Encoding` Wert.</span><span class="sxs-lookup"><span data-stu-id="3f32e-278">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="3f32e-279">In ASP.NET Core 1.x, Hinzufügen der `Vary` Header in die Antwort erfolgt manuell:</span><span class="sxs-lookup"><span data-stu-id="3f32e-279">In ASP.NET Core 1.x, adding the `Vary` header to the response is accomplished manually:</span></span>

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="3f32e-280">Middleware-Problem, wenn der Server einen Nginx-reverse-proxy</span><span class="sxs-lookup"><span data-stu-id="3f32e-280">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="3f32e-281">Wenn eine Anforderung über einen Proxy von Nginx, ist die `Accept-Encoding` Header entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="3f32e-281">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="3f32e-282">Entfernen der `Accept-Encoding` Header wird verhindert, dass die Middleware die Antwort zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="3f32e-282">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="3f32e-283">Weitere Informationen finden Sie unter [NGINX: Komprimierung und Dekomprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="3f32e-283">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="3f32e-284">Dieses Problem wird nachverfolgt, indem [herausfinden, Pass-Through-Komprimierung für Nginx (Aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="3f32e-284">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="3f32e-285">Arbeiten mit dynamische Komprimierung in IIS</span><span class="sxs-lookup"><span data-stu-id="3f32e-285">Working with IIS dynamic compression</span></span>

<span data-ttu-id="3f32e-286">Wenn Sie ein aktives IIS dynamische Komprimierung Modul konfiguriert werden, auf der Serverebene, die Sie für eine app deaktivieren möchten haben, deaktivieren Sie das Modul mit der eine Ergänzung der *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="3f32e-286">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="3f32e-287">Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="3f32e-287">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3f32e-288">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="3f32e-288">Troubleshooting</span></span>

<span data-ttu-id="3f32e-289">Mit einem Tool wie [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), oder [Postman](https://www.getpostman.com/), mit denen Sie festlegen, die `Accept-Encoding` Anforderungsheader und Untersuchen der Antwortheader, Größe und Text.</span><span class="sxs-lookup"><span data-stu-id="3f32e-289">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="3f32e-290">Antworten komprimierende Middleware komprimiert standardmäßig Antworten, die die folgenden Bedingungen erfüllen:</span><span class="sxs-lookup"><span data-stu-id="3f32e-290">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="3f32e-291">Die `Accept-Encoding` Header mit dem Wert vorhanden ist `br`, `gzip`, `*`, oder benutzerdefinierte Codierung, die einen benutzerdefinierten Komprimierung Anbieter entspricht, die Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="3f32e-291">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="3f32e-292">Der Wert darf nicht sein `identity` oder über einen Qualitätswert (Qvalue, `q`) von 0 (null) festlegen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-292">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="3f32e-293">Der MIME-Typ (`Content-Type`) muss festgelegt sein und muss einen MIME-Typ auf konfiguriert entsprechen den <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="3f32e-293">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="3f32e-294">Die Anforderung dürfen keine der `Content-Range` Header.</span><span class="sxs-lookup"><span data-stu-id="3f32e-294">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="3f32e-295">Die Anforderung muss unsicheres Protokoll (http) verwenden, es sei denn, sicheres Protokoll (Https) in den Antworten komprimierende Middleware-Optionen konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="3f32e-295">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="3f32e-296">*Beachten Sie die Gefahr [oben beschriebenen](#compression-with-secure-protocol) beim Aktivieren der Komprimierung für sichere Inhalte.*</span><span class="sxs-lookup"><span data-stu-id="3f32e-296">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="3f32e-297">Die `Accept-Encoding` Header mit dem Wert vorhanden ist `gzip`, `*`, oder benutzerdefinierte Codierung, die einen benutzerdefinierten Komprimierung Anbieter entspricht, die Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="3f32e-297">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="3f32e-298">Der Wert darf nicht sein `identity` oder über einen Qualitätswert (Qvalue, `q`) von 0 (null) festlegen.</span><span class="sxs-lookup"><span data-stu-id="3f32e-298">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="3f32e-299">Der MIME-Typ (`Content-Type`) muss festgelegt sein und muss einen MIME-Typ auf konfiguriert entsprechen den <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span><span class="sxs-lookup"><span data-stu-id="3f32e-299">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="3f32e-300">Die Anforderung dürfen keine der `Content-Range` Header.</span><span class="sxs-lookup"><span data-stu-id="3f32e-300">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="3f32e-301">Die Anforderung muss unsicheres Protokoll (http) verwenden, es sei denn, sicheres Protokoll (Https) in den Antworten komprimierende Middleware-Optionen konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="3f32e-301">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="3f32e-302">*Beachten Sie die Gefahr [oben beschriebenen](#compression-with-secure-protocol) beim Aktivieren der Komprimierung für sichere Inhalte.*</span><span class="sxs-lookup"><span data-stu-id="3f32e-302">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3f32e-303">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3f32e-303">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="3f32e-304">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="3f32e-304">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="3f32e-305">RFC 7231 Abschnitt 3.1.2.1: Inhalt Codings</span><span class="sxs-lookup"><span data-stu-id="3f32e-305">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="3f32e-306">RFC 7230 Abschnitt 4.2.3: Gzip-Codierung</span><span class="sxs-lookup"><span data-stu-id="3f32e-306">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="3f32e-307">GZIP-Datei-Format Specification Version 4.3</span><span class="sxs-lookup"><span data-stu-id="3f32e-307">GZIP file format specification version 4.3</span></span>](http://www.ietf.org/rfc/rfc1952.txt)
