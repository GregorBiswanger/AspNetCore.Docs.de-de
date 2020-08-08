---
title: Migrieren von ASP.net-Web-API zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von der ASP.NET 4. x-Web-API zu ASP.net Core MVC migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 4888de6ad55037be540cb62b6e4f02878e2b57ab
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014814"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="1959b-103">Migrieren von ASP.net-Web-API zu ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="1959b-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="1959b-104">Von [Scott Adder](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1959b-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1959b-105">Eine ASP.NET 4. x-Web-API ist ein HTTP-Dienst, der eine breite Palette von Clients, einschließlich Browser und mobiler Geräte, erreicht.</span><span class="sxs-lookup"><span data-stu-id="1959b-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="1959b-106">ASP.net Core kombiniert die MVC-und Web-API-APP-Modelle von ASP.NET 4. x in einem einzelnen Programmiermodell, das als ASP.net Core MVC bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="1959b-107">In diesem Artikel werden die erforderlichen Schritte zum Migrieren von der ASP.NET 4. x-Web-API zu ASP.net Core MVC erläutert.</span><span class="sxs-lookup"><span data-stu-id="1959b-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="1959b-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1959b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="1959b-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1959b-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="1959b-110">ASP.NET 4. x-Web-API-Projekt überprüfen</span><span class="sxs-lookup"><span data-stu-id="1959b-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="1959b-111">In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="1959b-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="1959b-112">In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="1959b-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="1959b-113">In *Global.asax.cs*wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="1959b-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="1959b-114">Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:</span><span class="sxs-lookup"><span data-stu-id="1959b-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="1959b-115">Die vorherige-Klasse:</span><span class="sxs-lookup"><span data-stu-id="1959b-115">The preceding class:</span></span>

* <span data-ttu-id="1959b-116">Konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl es nicht tatsächlich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="1959b-117">Konfiguriert die Routing Tabelle.</span><span class="sxs-lookup"><span data-stu-id="1959b-117">Configures the routing table.</span></span>
<span data-ttu-id="1959b-118">Im Beispielcode wird erwartet, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` optional ist.</span><span class="sxs-lookup"><span data-stu-id="1959b-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="1959b-119">In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1959b-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="1959b-120">Erstellen des Ziel Projekts</span><span class="sxs-lookup"><span data-stu-id="1959b-120">Create the destination project</span></span>

<span data-ttu-id="1959b-121">Erstellen Sie eine neue leere Projekt Mappe in Visual Studio, und fügen Sie der Migration das ASP.NET 4. x-Web-API-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="1959b-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="1959b-122">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1959b-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="1959b-123">Wählen Sie die Vorlage **leere** Vorlage aus, und klicken Sie auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="1959b-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="1959b-124">Nennen Sie die Projekt Mappe *webapimigration*.</span><span class="sxs-lookup"><span data-stu-id="1959b-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="1959b-125">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1959b-125">Select **Create**.</span></span>
1. <span data-ttu-id="1959b-126">Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="1959b-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="1959b-127">Fügen Sie ein neues API-Projekt für die Migration hinzu:</span><span class="sxs-lookup"><span data-stu-id="1959b-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="1959b-128">Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="1959b-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="1959b-129">Benennen Sie im Dialogfeld **Neues Projekt konfigurieren** das Projekt *productscore*, und wählen Sie dann **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="1959b-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="1959b-130">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="1959b-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="1959b-131">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1959b-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="1959b-132">Entfernen Sie die Beispieldateien *WeatherForecast.cs* und *Controllers/weatherforecastcontroller. cs* aus dem neuen *productscore* -Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="1959b-133">Die Projekt Mappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="1959b-133">The solution now contains two projects.</span></span> <span data-ttu-id="1959b-134">In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="1959b-135">Migrating Configuration (Migrieren der Konfiguration)</span><span class="sxs-lookup"><span data-stu-id="1959b-135">Migrate configuration</span></span>

<span data-ttu-id="1959b-136">ASP.net Core verwendet nicht den Ordner *App_Start* oder die Datei *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="1959b-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="1959b-137">Außerdem wird die *web.config* -Datei zum Zeitpunkt der Veröffentlichung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1959b-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="1959b-138">Die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="1959b-138">The `Startup` class:</span></span>

* <span data-ttu-id="1959b-139">Ersetzt *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="1959b-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="1959b-140">Behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="1959b-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="1959b-141">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1959b-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="1959b-142">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="1959b-142">Migrate models and controllers</span></span>

<span data-ttu-id="1959b-143">Der folgende Code zeigt `ProductsController` , dass für ASP.net Core aktualisiert werden soll:</span><span class="sxs-lookup"><span data-stu-id="1959b-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="1959b-144">Aktualisieren Sie das- `ProductsController` ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="1959b-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="1959b-145">Kopieren Sie " *Controllers/productioncontroller. cs* " und den Ordner " *Models* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="1959b-146">Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="1959b-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="1959b-147">Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="1959b-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="1959b-148">Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:</span><span class="sxs-lookup"><span data-stu-id="1959b-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="1959b-149">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="1959b-149">`ApiController` class</span></span>
* <span data-ttu-id="1959b-150">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="1959b-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="1959b-151">`IHttpActionResult`-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="1959b-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="1959b-152">Nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="1959b-152">Make the following changes:</span></span>

1. <span data-ttu-id="1959b-153">Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1959b-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1959b-154">Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1959b-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="1959b-155">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="1959b-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="1959b-156">Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="1959b-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="1959b-157">Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:</span><span class="sxs-lookup"><span data-stu-id="1959b-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="1959b-158">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="1959b-158">Configure routing</span></span>

<span data-ttu-id="1959b-159">Die ASP.net Core- *API* -Projektvorlage enthält die Konfiguration des Endpunkt Routings im generierten Code.</span><span class="sxs-lookup"><span data-stu-id="1959b-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="1959b-160">Die folgenden <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> -und- <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Aufrufe:</span><span class="sxs-lookup"><span data-stu-id="1959b-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="1959b-161">Registrieren Sie den Routen Abgleich und die [middleware](xref:fundamentals/middleware/index) Endpunkt Ausführung in der middlewarepipeline.</span><span class="sxs-lookup"><span data-stu-id="1959b-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="1959b-162">Ersetzen Sie die *App_Start/webapiconfig.cs* -Datei des Projekts " *producungapp* ".</span><span class="sxs-lookup"><span data-stu-id="1959b-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="1959b-163">Konfigurieren Sie das Routing wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1959b-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="1959b-164">Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="1959b-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="1959b-165">Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers.</span><span class="sxs-lookup"><span data-stu-id="1959b-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="1959b-166">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1959b-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="1959b-167">Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und.</span><span class="sxs-lookup"><span data-stu-id="1959b-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="1959b-168">Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="1959b-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="1959b-169">Die Token:</span><span class="sxs-lookup"><span data-stu-id="1959b-169">The tokens:</span></span>
    * <span data-ttu-id="1959b-170">Reduzieren Sie die Anzahl der magischen Zeichen folgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="1959b-171">Stellen Sie sicher, dass die Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn Sie refacktoren für automatisches umbenennen anwenden.</span><span class="sxs-lookup"><span data-stu-id="1959b-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="1959b-172">HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:</span><span class="sxs-lookup"><span data-stu-id="1959b-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="1959b-173">Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="1959b-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="1959b-174">Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="1959b-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="1959b-175">Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="1959b-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="1959b-176">Es wird eine vollständige Liste der drei Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1959b-176">A full list of three products appears.</span></span> <span data-ttu-id="1959b-177">Navigieren Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="1959b-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="1959b-178">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1959b-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1959b-179">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1959b-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="1959b-180">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1959b-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="1959b-181">ASP.NET 4. x-Web-API-Projekt überprüfen</span><span class="sxs-lookup"><span data-stu-id="1959b-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="1959b-182">In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="1959b-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="1959b-183">In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="1959b-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="1959b-184">In *Global.asax.cs*wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="1959b-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="1959b-185">Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:</span><span class="sxs-lookup"><span data-stu-id="1959b-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="1959b-186">Diese Klasse konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl Sie nicht tatsächlich im Projekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="1959b-187">Außerdem wird die Routing Tabelle konfiguriert, die von ASP.net-Web-API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="1959b-188">In diesem Fall erwartet die ASP.NET 4. x-Web-API, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` Sie optional ist.</span><span class="sxs-lookup"><span data-stu-id="1959b-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="1959b-189">In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1959b-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="1959b-190">Erstellen des Ziel Projekts</span><span class="sxs-lookup"><span data-stu-id="1959b-190">Create the destination project</span></span>

<span data-ttu-id="1959b-191">Führen Sie in Visual Studio die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="1959b-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="1959b-192">Wechseln Sie zu **Datei**  >  **neu**  >  **Projekt**  >  **andere Projekttypen**  >  **Visual Studio**-Projektmappen.</span><span class="sxs-lookup"><span data-stu-id="1959b-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="1959b-193">Wählen Sie **leere**Projekt Mappe aus, und nennen Sie die Projekt Mappe *webapimigration*.</span><span class="sxs-lookup"><span data-stu-id="1959b-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="1959b-194">Klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="1959b-194">Click the **OK** button.</span></span>
* <span data-ttu-id="1959b-195">Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="1959b-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="1959b-196">Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="1959b-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="1959b-197">Wählen Sie in der Dropdown Liste das Ziel Framework **.net Core** aus, und wählen Sie die Vorlage **API** -Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="1959b-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="1959b-198">Nennen Sie das Projekt *productscore*, und klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="1959b-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="1959b-199">Die Projekt Mappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="1959b-199">The solution now contains two projects.</span></span> <span data-ttu-id="1959b-200">In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.</span><span class="sxs-lookup"><span data-stu-id="1959b-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="1959b-201">Migrating Configuration (Migrieren der Konfiguration)</span><span class="sxs-lookup"><span data-stu-id="1959b-201">Migrate configuration</span></span>

<span data-ttu-id="1959b-202">ASP.net Core nicht verwendet:</span><span class="sxs-lookup"><span data-stu-id="1959b-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="1959b-203">*App_Start* Ordner oder die Datei " *Global. asax* "</span><span class="sxs-lookup"><span data-stu-id="1959b-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="1959b-204">*web.config* Datei wird zum Zeitpunkt der Veröffentlichung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1959b-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="1959b-205">Die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="1959b-205">The `Startup` class:</span></span>

* <span data-ttu-id="1959b-206">Ersetzt *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="1959b-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="1959b-207">Behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="1959b-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="1959b-208">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1959b-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="1959b-209">In ASP.net Core MVC ist das Attribut Routing standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> in aufgerufen wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1959b-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="1959b-210">Der folgende `UseMvc` -Befehl ersetzt die *App_Start/webapiconfig.cs* -Datei des Projekts *producungapp* :</span><span class="sxs-lookup"><span data-stu-id="1959b-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="1959b-211">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="1959b-211">Migrate models and controllers</span></span>

<span data-ttu-id="1959b-212">Der folgende Code zeigt das `ProductsController` Update für ASP.net Core:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="1959b-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="1959b-213">Aktualisieren Sie das- `ProductsController` ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="1959b-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="1959b-214">Kopieren Sie " *Controllers/productioncontroller. cs* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="1959b-215">Kopieren Sie den Ordner *Models* aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="1959b-216">Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="1959b-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="1959b-217">Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="1959b-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="1959b-218">Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:</span><span class="sxs-lookup"><span data-stu-id="1959b-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="1959b-219">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="1959b-219">`ApiController` class</span></span>
* <span data-ttu-id="1959b-220">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="1959b-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="1959b-221">`IHttpActionResult`-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="1959b-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="1959b-222">Nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="1959b-222">Make the following changes:</span></span>

1. <span data-ttu-id="1959b-223">Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1959b-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1959b-224">Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="1959b-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="1959b-225">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="1959b-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="1959b-226">Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="1959b-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="1959b-227">Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:</span><span class="sxs-lookup"><span data-stu-id="1959b-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="1959b-228">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="1959b-228">Configure routing</span></span>

<span data-ttu-id="1959b-229">Konfigurieren Sie das Routing wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1959b-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="1959b-230">Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="1959b-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="1959b-231">Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers.</span><span class="sxs-lookup"><span data-stu-id="1959b-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="1959b-232">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1959b-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="1959b-233">Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und.</span><span class="sxs-lookup"><span data-stu-id="1959b-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="1959b-234">Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="1959b-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="1959b-235">Die Token reduzieren die Anzahl der magischen Zeichen folgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="1959b-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="1959b-236">Die Token stellen außerdem sicher, dass Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn die refactoren für automatisches umbenennen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="1959b-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="1959b-237">Legen Sie den Kompatibilitätsmodus des Projekts auf ASP.net Core 2,2 fest:</span><span class="sxs-lookup"><span data-stu-id="1959b-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="1959b-238">Die vorherige Änderung:</span><span class="sxs-lookup"><span data-stu-id="1959b-238">The preceding change:</span></span>

    * <span data-ttu-id="1959b-239">Ist erforderlich, um das- `[ApiController]` Attribut auf Controller Ebene zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1959b-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="1959b-240">Gibt an, dass potenziell wichtige Verhaltensweisen in ASP.net Core 2,2 eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1959b-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="1959b-241">HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:</span><span class="sxs-lookup"><span data-stu-id="1959b-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="1959b-242">Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="1959b-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="1959b-243">Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="1959b-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="1959b-244">Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="1959b-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="1959b-245">Es wird eine vollständige Liste der drei Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1959b-245">A full list of three products appears.</span></span> <span data-ttu-id="1959b-246">Navigieren Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="1959b-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="1959b-247">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1959b-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="1959b-248">Kompatibilitäts-Shim</span><span class="sxs-lookup"><span data-stu-id="1959b-248">Compatibility shim</span></span>

<span data-ttu-id="1959b-249">Die [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek stellt einen Kompatibilitäts-Shim bereit, um ASP.NET 4. x-Web-API-Projekte in ASP.net Core zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="1959b-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="1959b-250">Der Kompatibilitäts-Shim erweitert ASP.net Core, um eine Reihe von Konventionen von ASP.NET 4. x Web-API 2 zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1959b-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="1959b-251">Das zuvor in diesem Dokument portierte Beispiel ist so einfach, dass das Kompatibilitäts-Shim unnötig ist.</span><span class="sxs-lookup"><span data-stu-id="1959b-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="1959b-252">Bei größeren Projekten kann die Verwendung des Kompatibilitäts-Shims nützlich sein, um die API-Lücke zwischen ASP.net Core und ASP.NET 4. x-Web-API 2 vorübergehend zu überbrücken.</span><span class="sxs-lookup"><span data-stu-id="1959b-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="1959b-253">Das Web-API-Kompatibilitäts-Shim ist als temporäre Maßnahme zur Unterstützung der Migration von großen ASP.NET 4. x-Web-API-Projekten zu ASP.net Core gedacht.</span><span class="sxs-lookup"><span data-stu-id="1959b-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="1959b-254">Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.net Core Muster zu verwenden, anstatt sich auf den Kompatibilitäts-Shim zu verlassen.</span><span class="sxs-lookup"><span data-stu-id="1959b-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="1959b-255">Zu den in enthaltenen Kompatibilitäts Features `Microsoft.AspNetCore.Mvc.WebApiCompatShim` gehören:</span><span class="sxs-lookup"><span data-stu-id="1959b-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="1959b-256">Fügt einen `ApiController` Typ hinzu, sodass die Basis Typen der Controller nicht aktualisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="1959b-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="1959b-257">Ermöglicht die Modell Bindung im Web-API-Stil.</span><span class="sxs-lookup"><span data-stu-id="1959b-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="1959b-258">ASP.net Core die MVC-Modell Bindung ähnlich der von ASP.NET 4. x MVC 5 standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="1959b-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="1959b-259">Mit dem Kompatibilitäts-Shim wird die Modell Bindung so geändert, dass Sie den Modell Bindungs Konventionen von ASP.NET 4. x Web API 2 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="1959b-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="1959b-260">Komplexe Typen werden z. b. automatisch vom Anforderungs Text gebunden.</span><span class="sxs-lookup"><span data-stu-id="1959b-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="1959b-261">Erweitert die Modell Bindung, sodass Controller Aktionen Parameter des Typs annehmen können `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="1959b-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="1959b-262">Fügt nachrichtenformatierer hinzu, sodass Aktionen Ergebnisse vom Typ zurückgeben können `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="1959b-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="1959b-263">Fügt zusätzliche Antwort Methoden hinzu, die von Web-API 2-Aktionen verwendet werden können, um Antworten zu erfüllen:</span><span class="sxs-lookup"><span data-stu-id="1959b-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="1959b-264">`HttpResponseMessage`ger</span><span class="sxs-lookup"><span data-stu-id="1959b-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="1959b-265">Aktions Ergebnis Methoden:</span><span class="sxs-lookup"><span data-stu-id="1959b-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="1959b-266">Fügt `IContentNegotiator` dem Container für die Abhängigkeitsinjektion der App eine Instanz von hinzu und stellt die inhaltsaushandlungs bezogenen Typen von [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="1959b-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="1959b-267">Beispiele für solche Typen sind `DefaultContentNegotiator` und `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="1959b-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="1959b-268">So verwenden Sie den Kompatibilitäts-Shim:</span><span class="sxs-lookup"><span data-stu-id="1959b-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="1959b-269">Installieren Sie das nuget-Paket [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="1959b-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="1959b-270">Registrieren Sie die Dienste des Kompatibilitäts-Shims mit dem di-Container der APP, indem Sie `services.AddMvc().AddWebApiConventions()` in Aufrufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1959b-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="1959b-271">Definieren Sie Web-API-spezifische Routen mithilfe `MapWebApiRoute` von für den `IRouteBuilder` im App-Befehl `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="1959b-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1959b-272">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1959b-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
