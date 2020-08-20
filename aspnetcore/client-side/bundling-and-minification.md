---
title: Bündelung und Minimierung statischer Ressourcen in ASP.NET Core
author: scottaddie
description: Hier erfahren Sie, wie Sie statische Ressourcen in einer ASP.NET Core-Webanwendung mithilfe von Bündelungs- und Minimierungsverfahren optimieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 84123464e8f01f8a3caa65035b3174cc04aea7cf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625855"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="f1715-103">Bündelung und Minimierung statischer Ressourcen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1715-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="f1715-104">Von [Scott Addie](https://twitter.com/Scott_Addie) und [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="f1715-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="f1715-105">In diesem Artikel lernen Sie die Vorteile der Funktionen Bündelung und Minimierung sowie ihre Verwendung mit ASP.NET Core-Web-Apps kennen.</span><span class="sxs-lookup"><span data-stu-id="f1715-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="f1715-106">Was versteht man unter Bündelung und Minimierung?</span><span class="sxs-lookup"><span data-stu-id="f1715-106">What is bundling and minification</span></span>

<span data-ttu-id="f1715-107">Die Bündelung und Minimierung sind zwei Methoden zur Leistungsoptimierung, die Sie unabhängig voneinander in einer Web-App anwenden können.</span><span class="sxs-lookup"><span data-stu-id="f1715-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="f1715-108">Zusammengenommen führen diese Funktionen zu einer Leistungssteigerung, indem die Anzahl der Serveranforderungen sowie die Größe der angeforderten statischen Ressourcen reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="f1715-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="f1715-109">Durch Bündelung und Minimierung wird in erster Linie die Ladezeit für die Anforderung der ersten Seite verkürzt.</span><span class="sxs-lookup"><span data-stu-id="f1715-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="f1715-110">Sobald eine Webseite angefordert wurde, werden die statischen Ressourcen (JavaScript, CSS und Bilder) vom Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="f1715-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="f1715-111">Die Bündelung und Minimierung haben folglich keine Auswirkungen auf die Leistung, wenn dieselbe Seite (oder Seiten) auf derselben Website angefordert wird, die ihrerseits dieselben Ressourcen anfordert.</span><span class="sxs-lookup"><span data-stu-id="f1715-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="f1715-112">Wurde der Expires-Header für die Ressourcen nicht korrekt festgelegt und die Bündelungs- und Minimierungsfunktionen nicht verwendet, kennzeichnen die Aktualitätsheuristiken des Browsers die Ressourcen nach ein paar Tagen als veraltet.</span><span class="sxs-lookup"><span data-stu-id="f1715-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="f1715-113">Zusätzlich benötigt der Browser für jede Ressource eine Überprüfungsanforderung.</span><span class="sxs-lookup"><span data-stu-id="f1715-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="f1715-114">In solchen Fällen führt die Bündelung und Minimierung auch nach der Anforderung der ersten Seite zu einer Leistungsverbesserung.</span><span class="sxs-lookup"><span data-stu-id="f1715-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="f1715-115">Bündelung</span><span class="sxs-lookup"><span data-stu-id="f1715-115">Bundling</span></span>

<span data-ttu-id="f1715-116">Bei der Bündelung werden mehrere Dateien zu einer einzelnen Datei kombiniert.</span><span class="sxs-lookup"><span data-stu-id="f1715-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="f1715-117">Durch die Bündelung wird die zum Rendern einer Webressource, z. B. einer Webseite, erforderliche Anzahl von Serveranforderungen reduziert.</span><span class="sxs-lookup"><span data-stu-id="f1715-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="f1715-118">Sie können beliebig viele Einzelbündel erstellen, insbesondere für CSS, JavaScript usw. Je kleiner die Anzahl von Dateien, desto weniger HTTP-Anforderungen müssen zwischen Browser und Server oder vom Dienst verarbeitet werden, der Ihre Anwendung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f1715-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="f1715-119">Dadurch kann die Leistung des Ladevorgangs der ersten Seite gesteigert werden.</span><span class="sxs-lookup"><span data-stu-id="f1715-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="f1715-120">Minimierung</span><span class="sxs-lookup"><span data-stu-id="f1715-120">Minification</span></span>

<span data-ttu-id="f1715-121">Bei der Minimierung werden unnötige Zeichen aus dem Code entfernt, ohne dass die Funktionalität geändert wird.</span><span class="sxs-lookup"><span data-stu-id="f1715-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="f1715-122">Dadurch lässt sich die Größe der angeforderten Ressourcen (wie CSS, Bilder oder JavaScript-Dateien) deutlich reduzieren.</span><span class="sxs-lookup"><span data-stu-id="f1715-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="f1715-123">Häufiger Nebeneffekt einer Minimierung sind auf ein Zeichen verkürzte Variablennamen und entfernte Kommentare und Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="f1715-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="f1715-124">Betrachten Sie die folgende JavaScript-Funktion:</span><span class="sxs-lookup"><span data-stu-id="f1715-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="f1715-125">Durch Minimierung kann die Funktion auf folgenden Code verkürzt werden:</span><span class="sxs-lookup"><span data-stu-id="f1715-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="f1715-126">Zusätzlich zur Entfernung von Kommentaren und unnötigen Leerzeichen werden die folgenden Parameter und Variablennamen folgendermaßen umbenannt:</span><span class="sxs-lookup"><span data-stu-id="f1715-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="f1715-127">Ursprünglich</span><span class="sxs-lookup"><span data-stu-id="f1715-127">Original</span></span> | <span data-ttu-id="f1715-128">Umbenannt</span><span class="sxs-lookup"><span data-stu-id="f1715-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="f1715-129">Auswirkungen der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f1715-129">Impact of bundling and minification</span></span>

<span data-ttu-id="f1715-130">In der folgenden Tabelle werden die Unterschiede bei den Ladezeiten für einzelne Ressourcen mit und ohne Bündelung und Minimierung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="f1715-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="f1715-131">Aktion</span><span class="sxs-lookup"><span data-stu-id="f1715-131">Action</span></span> | <span data-ttu-id="f1715-132">Mit B/M</span><span class="sxs-lookup"><span data-stu-id="f1715-132">With B/M</span></span> | <span data-ttu-id="f1715-133">Ohne B/M</span><span class="sxs-lookup"><span data-stu-id="f1715-133">Without B/M</span></span> | <span data-ttu-id="f1715-134">Änderung</span><span class="sxs-lookup"><span data-stu-id="f1715-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="f1715-135">Dateianforderungen</span><span class="sxs-lookup"><span data-stu-id="f1715-135">File Requests</span></span>  | <span data-ttu-id="f1715-136">7</span><span class="sxs-lookup"><span data-stu-id="f1715-136">7</span></span>   | <span data-ttu-id="f1715-137">18</span><span class="sxs-lookup"><span data-stu-id="f1715-137">18</span></span>     | <span data-ttu-id="f1715-138">157 %</span><span class="sxs-lookup"><span data-stu-id="f1715-138">157%</span></span>
<span data-ttu-id="f1715-139">Übertragene KB</span><span class="sxs-lookup"><span data-stu-id="f1715-139">KB Transferred</span></span> | <span data-ttu-id="f1715-140">156</span><span class="sxs-lookup"><span data-stu-id="f1715-140">156</span></span> | <span data-ttu-id="f1715-141">264,68</span><span class="sxs-lookup"><span data-stu-id="f1715-141">264.68</span></span> | <span data-ttu-id="f1715-142">70 %</span><span class="sxs-lookup"><span data-stu-id="f1715-142">70%</span></span>
<span data-ttu-id="f1715-143">Ladezeit (ms)</span><span class="sxs-lookup"><span data-stu-id="f1715-143">Load Time (ms)</span></span> | <span data-ttu-id="f1715-144">885</span><span class="sxs-lookup"><span data-stu-id="f1715-144">885</span></span> | <span data-ttu-id="f1715-145">2360</span><span class="sxs-lookup"><span data-stu-id="f1715-145">2360</span></span>   | <span data-ttu-id="f1715-146">167 %</span><span class="sxs-lookup"><span data-stu-id="f1715-146">167%</span></span>

<span data-ttu-id="f1715-147">Browser arbeiten in Bezug auf HTTP-Anforderungsheader ziemlich ausführlich.</span><span class="sxs-lookup"><span data-stu-id="f1715-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="f1715-148">Die Gesamtmetrik der gesendeten Bytes ergibt bei der Bündelung eine deutliche Verringerung.</span><span class="sxs-lookup"><span data-stu-id="f1715-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="f1715-149">Auch die Ladezeit wird deutlich verbessert. Und dies obwohl das vorangegangene Beispiel lokal ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f1715-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="f1715-150">Bei einer Übertragung der Ressourcen über ein Netzwerk können durch Bündelung und Minimierung noch größere Leistungssteigerungen erzielt werden.</span><span class="sxs-lookup"><span data-stu-id="f1715-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="f1715-151">Auswählen einer Bündelungs- und Minimierungsstrategie</span><span class="sxs-lookup"><span data-stu-id="f1715-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="f1715-152">Die MVC- und Razor Pages-Projektvorlagen enthalten eine Bündelungs- und Minimierungslösung, bestehend aus einer JSON-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="f1715-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="f1715-153">Drittanbietertools, wie die [Grunt](xref:client-side/using-grunt)-Aufgabenausführung, führen dieselben Aufgaben auf etwas komplexere Weise aus.</span><span class="sxs-lookup"><span data-stu-id="f1715-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="f1715-154">Ein Drittanbietertool eignet sich hervorragend in Fällen, in denen Ihr Entwicklungsworkflow Verarbeitungsschritte erfordert, die über die Bündelung und Minimierung hinausgehen, wie etwa Linting und Bildoptimierung.</span><span class="sxs-lookup"><span data-stu-id="f1715-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="f1715-155">Bei der Bündelung und Minimierung zur Entwurfszeit werden die minimierten Dateien vor Bereitstellung der App erstellt.</span><span class="sxs-lookup"><span data-stu-id="f1715-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="f1715-156">Werden die Bündelung und Minimierung vor der Bereitstellung durchgeführt, kann die Serverauslastung reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="f1715-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="f1715-157">Beachten Sie jedoch, dass eine Bündelung und Minimierung zur Entwurfszeit die Komplexität des Builds erhöht und auch nur mit statischen Dateien ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f1715-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="f1715-158">Konfigurieren der Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f1715-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f1715-159">In ASP.NET Core 2.0 oder früher enthalten die MVC- und Razor Pages-Projektvorlagen eine Konfigurationsdatei mit dem Namen *bundleconfig.json*, in der die Optionen für jedes Bündel definiert werden:</span><span class="sxs-lookup"><span data-stu-id="f1715-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f1715-160">Fügen Sie ab ASP.NET Core 2.1 dem MVC- oder Razor Pages-Projektstamm eine neue JSON-Datei mit dem Namen *bundleconfig.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f1715-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="f1715-161">Fügen Sie dieser Datei als Startpunkt den folgenden JSON-Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="f1715-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="f1715-162">In der Datei *bundleconfig.json* werden die Optionen für jedes Bündel definiert.</span><span class="sxs-lookup"><span data-stu-id="f1715-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="f1715-163">Im vorherigen Beispiel wird für die benutzerdefinierten JavaScript- (*wwwroot/js/site.js*) und Stylesheet-Dateien (*wwwroot/css/site.css*) eine einzelne Bündelkonfiguration definiert.</span><span class="sxs-lookup"><span data-stu-id="f1715-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="f1715-164">Zu den Konfigurationsoptionen gehören:</span><span class="sxs-lookup"><span data-stu-id="f1715-164">Configuration options include:</span></span>

* <span data-ttu-id="f1715-165">`outputFileName`: Name der Bündeldatei, die ausgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="f1715-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="f1715-166">Kann einen relativen Pfad der Datei *bundleconfig.json* enthalten.</span><span class="sxs-lookup"><span data-stu-id="f1715-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="f1715-167">**erforderlich**</span><span class="sxs-lookup"><span data-stu-id="f1715-167">**required**</span></span>
* <span data-ttu-id="f1715-168">`inputFiles`: Array von Dateien, die gebündelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f1715-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="f1715-169">Enthält relative Pfade zur Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="f1715-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="f1715-170">**Optional**, \*ein leerer Wert erzeugt eine leere Ausgabedatei.</span><span class="sxs-lookup"><span data-stu-id="f1715-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="f1715-171">[Globmuster](https://www.tldp.org/LDP/abs/html/globbingref.html) werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f1715-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="f1715-172">`minify`: Minimierungsoptionen für den Ausgabetyp.</span><span class="sxs-lookup"><span data-stu-id="f1715-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="f1715-173">**Optional**, *Standardwert: `minify: { enabled: true }`* .</span><span class="sxs-lookup"><span data-stu-id="f1715-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="f1715-174">Für jeden Ausgabedateityp stehen Konfigurationsoptionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f1715-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="f1715-175">CSS-Minifier</span><span class="sxs-lookup"><span data-stu-id="f1715-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="f1715-176">JavaScript-Minifier</span><span class="sxs-lookup"><span data-stu-id="f1715-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="f1715-177">HTML-Minifier</span><span class="sxs-lookup"><span data-stu-id="f1715-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="f1715-178">`includeInProject`: Flag, das angibt, ob der Projektdatei generierte Dateien hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f1715-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="f1715-179">**Optional**, *Standardwert: FALSE*.</span><span class="sxs-lookup"><span data-stu-id="f1715-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="f1715-180">`sourceMap`: Flag, das angibt, ob für die Bündeldatei eine Quellzuordnungsdatei generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f1715-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="f1715-181">**Optional**, *Standardwert: FALSE*.</span><span class="sxs-lookup"><span data-stu-id="f1715-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="f1715-182">`sourceMapRootPath`: Stammpfad zum Speichern der generierten Quellzuordnungsdatei.</span><span class="sxs-lookup"><span data-stu-id="f1715-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="f1715-183">Hinzufügen von Dateien zum Workflow</span><span class="sxs-lookup"><span data-stu-id="f1715-183">Add files to workflow</span></span>

<span data-ttu-id="f1715-184">Angenommen, Sie möchten eine zusätzliche *custom.css*-Datei hinzufügen, die etwa folgendermaßen aussieht:</span><span class="sxs-lookup"><span data-stu-id="f1715-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="f1715-185">Fügen Sie der Datei *bundleconfig.json* den relativen Pfad hinzu, um die Datei *custom.css* zu minimieren und zusammen mit *site.css* in die Datei *site.min.css* zu bündeln:</span><span class="sxs-lookup"><span data-stu-id="f1715-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="f1715-186">Alternativ dazu können Sie auch das folgende Globmuster verwenden:</span><span class="sxs-lookup"><span data-stu-id="f1715-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="f1715-187">Dieses Globmuster stimmt mit allen CSS-Dateien überein und schließt das minierte Dateimuster aus.</span><span class="sxs-lookup"><span data-stu-id="f1715-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="f1715-188">Erstellen Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f1715-188">Build the application.</span></span> <span data-ttu-id="f1715-189">Öffnen Sie die Datei *site.min.css*. Dabei werden Sie erkennen, dass der Inhalt von *custom.css* an das Ende der Datei angefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f1715-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="f1715-190">Umgebungsbasierte Bündelung und Minimierung</span><span class="sxs-lookup"><span data-stu-id="f1715-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="f1715-191">Als bewährte Methode wird empfohlen, die gebündelten und minimierten Dateien Ihrer App in einer Produktionsumgebung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f1715-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="f1715-192">Während der Entwicklung wird das Debuggen der App durch die Originaldateien erleichtert.</span><span class="sxs-lookup"><span data-stu-id="f1715-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="f1715-193">Geben Sie mithilfe des [Umgebungstaghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in Ihren Ansichten die Dateien an, die in Ihre Seiten einbezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f1715-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="f1715-194">Das Umgebungstaghilfsprogramm rendert die Inhalte nur, wenn es in bestimmten [Umgebungen](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f1715-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f1715-195">Das folgende `environment`-Tag rendert die nicht verarbeiteten CSS-Dateien bei der Ausführung in einer `Development`-Umgebung:</span><span class="sxs-lookup"><span data-stu-id="f1715-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="f1715-196">Das folgende `environment`-Tag rendert die gebündelten und minimierten CSS-Dateien nur bei der Ausführung in einer Umgebung, die keine `Development`-Umgebung darstellt.</span><span class="sxs-lookup"><span data-stu-id="f1715-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="f1715-197">Die Ausführung in einer `Production`- oder `Staging`-Umgebung löst beispielsweise das Rendern der folgenden Stylesheets aus:</span><span class="sxs-lookup"><span data-stu-id="f1715-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="f1715-198">Verarbeiten der Datei „bundleconfig.json“ mit Gulp</span><span class="sxs-lookup"><span data-stu-id="f1715-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="f1715-199">In einigen Fällen erfordert der Workflow für die Bündelung und Minimierung einer App zusätzliche Verarbeitungsschritte.</span><span class="sxs-lookup"><span data-stu-id="f1715-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="f1715-200">Dazu gehören Bildoptimierung, Cache-Busting und die Verarbeitung von CDN-Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="f1715-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="f1715-201">Zur Durchführung dieser Aufgaben können Sie den Workflow für die Bündelung und Minimierung so konvertieren, dass Gulp verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f1715-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="f1715-202">Manuelles Konvertieren des Workflows für die Bündelung und Minimierung, um Gulp zu verwenden</span><span class="sxs-lookup"><span data-stu-id="f1715-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="f1715-203">Fügen Sie dem Projektstamm eine *package.json*-Datei mit den folgenden `devDependencies` hinzu:</span><span class="sxs-lookup"><span data-stu-id="f1715-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="f1715-204">ECMAScript (ES) 2015/ES6 und höher wird vom `gulp-uglify`-Modul nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f1715-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="f1715-205">Installieren Sie anstelle von `gulp-uglify` das Plug-In [gulp-terser](https://www.npmjs.com/package/gulp-terser), wenn Sie ES2015/ES6 oder höher verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="f1715-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="f1715-206">Installieren Sie die Abhängigkeiten, indem Sie den folgenden Befehl auf der Ebene von *package.json* ausführen:</span><span class="sxs-lookup"><span data-stu-id="f1715-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="f1715-207">Installieren Sie die Gulp-CLI als globale Abhängigkeit:</span><span class="sxs-lookup"><span data-stu-id="f1715-207">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="f1715-208">Kopieren Sie die folgende *gulpfile.js*-Datei in den Projektstamm:</span><span class="sxs-lookup"><span data-stu-id="f1715-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="f1715-209">Ausführen von Gulp-Aufgaben</span><span class="sxs-lookup"><span data-stu-id="f1715-209">Run Gulp tasks</span></span>

<span data-ttu-id="f1715-210">Führen Sie die folgenden Schritte aus, um die Minimierungsaufgabe in Gulp vor dem Erstellen des Projekts in Visual Studio auszulösen:</span><span class="sxs-lookup"><span data-stu-id="f1715-210">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="f1715-211">Installieren Sie das NuGet-Paket [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier).</span><span class="sxs-lookup"><span data-stu-id="f1715-211">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="f1715-212">Fügen Sie der Projektdatei das folgende [MSBuild Target](/visualstudio/msbuild/msbuild-targets)-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="f1715-212">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="f1715-213">In diesem Beispiel werden alle im Ziel `MyPreCompileTarget` definierten Aufgaben vor dem vordefinierten `Build`-Ziel ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f1715-213">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="f1715-214">Etwa folgende Ausgabe wird im Visual Studio-Ausgabefenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f1715-214">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="f1715-215">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f1715-215">Additional resources</span></span>

* [<span data-ttu-id="f1715-216">Verwenden von Grunt</span><span class="sxs-lookup"><span data-stu-id="f1715-216">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="f1715-217">Verwenden mehrerer Umgebungen</span><span class="sxs-lookup"><span data-stu-id="f1715-217">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="f1715-218">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="f1715-218">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
