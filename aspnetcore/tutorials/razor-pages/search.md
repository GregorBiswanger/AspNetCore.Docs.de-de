---
title: 'Teil 6: Hinzufügen der Suche zu Razor Pages in ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 5e899929372697f7a381cd493655309fa3f70f79
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633850"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="604b3-103">Teil 6: Hinzufügen der Suche zu Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="604b3-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="604b3-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="604b3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="604b3-105">In den folgenden Abschnitten wird eine Suche nach Filmen anhand von *Genre* oder *Name* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="604b3-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="604b3-106">Fügen Sie *Pages/Movies/Index.cshtml.cs* folgende hervorgehobene Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="604b3-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="604b3-107">`SearchString`: enthält den Text, den Benutzer in das Suchtextfeld eingeben.</span><span class="sxs-lookup"><span data-stu-id="604b3-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="604b3-108">`SearchString` weist das Attribut [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) auf.</span><span class="sxs-lookup"><span data-stu-id="604b3-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="604b3-109">`[BindProperty]` bindet Formularwerte und Abfragezeichenfolgen mit dem gleichen Namen wie die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="604b3-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="604b3-110">`(SupportsGet = true)` ist für die Bindung in GET-Anforderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="604b3-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="604b3-111">`Genres`: enthält die Liste der Genres.</span><span class="sxs-lookup"><span data-stu-id="604b3-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="604b3-112">`Genres` ermöglicht es dem Benutzer, ein Genre in der Liste auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="604b3-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="604b3-113">`SelectList` erfordert `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="604b3-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="604b3-114">`MovieGenre`: enthält das spezifische Genre, das der Benutzer auswählt (z.B. Western).</span><span class="sxs-lookup"><span data-stu-id="604b3-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="604b3-115">`Genres` und `MovieGenre` werden später in diesem Tutorial verwendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="604b3-116">Aktualisieren Sie die `OnGetAsync`-Methode der Indexseite mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="604b3-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="604b3-117">Die erste Zeile der `OnGetAsync`-Methode erstellt eine [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfrage zum Auswählen der Filme:</span><span class="sxs-lookup"><span data-stu-id="604b3-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="604b3-118">Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="604b3-119">Wenn die `SearchString`-Eigenschaft nicht NULL oder leer ist, wird die Abfrage nach Filmen so geändert, dass die Suchzeichenfolge gefiltert wird:</span><span class="sxs-lookup"><span data-stu-id="604b3-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="604b3-120">Der Code `s => s.Title.Contains()` ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="604b3-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="604b3-121">Lambdaausdrücke werden in methodenbasierten [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)-Methode oder `Contains` verwendet (siehe den vorangehenden Code).</span><span class="sxs-lookup"><span data-stu-id="604b3-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="604b3-122">LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode geändert werden (z.B. `Where`, `Contains` oder `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="604b3-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="604b3-123">Stattdessen wird die Ausführung der Abfrage verzögert.</span><span class="sxs-lookup"><span data-stu-id="604b3-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="604b3-124">Dies bedeutet, dass die Auswertung eines Ausdrucks so lange hinausgezögert wird, bis dessen realisierter Wert durchlaufen oder die `ToListAsync`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="604b3-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="604b3-125">Weitere Informationen finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="604b3-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="604b3-126">Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im C#-Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="604b3-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="604b3-127">Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab.</span><span class="sxs-lookup"><span data-stu-id="604b3-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="604b3-128">In SQL Server wird `Contains` zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="604b3-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="604b3-129">In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="604b3-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="604b3-130">Navigieren Sie zur Seite „Movies“, und fügen Sie eine Abfragezeichenfolge wie z.B. `?searchString=Ghost` an die URL an (z.B. `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="604b3-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="604b3-131">Die gefilterten Filme werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="604b3-131">The filtered movies are displayed.</span></span>

![Indexansicht](search/_static/ghost.png)

<span data-ttu-id="604b3-133">Wenn die Routenvorlage der Indexseite hinzugefügt wird, kann die Suchzeichenfolge als URL-Segment (z.B. `https://localhost:5001/Movies/Ghost`) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="604b3-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="604b3-134">Die vorangehende Routeneinschränkung ermöglicht das Suchen des Titels als Routendaten (URL-Segment) anstatt als Wert einer Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="604b3-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="604b3-135">Das `?` in `"{searchString?}"` bedeutet, dass dies ein optionaler Routenparameter ist.</span><span class="sxs-lookup"><span data-stu-id="604b3-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Indexansicht mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="604b3-137">Die ASP.NET Core-Runtime verwendet die [Modellbindung](xref:mvc/models/model-binding), um den Wert der `SearchString`-Eigenschaft aus der Abfragezeichenfolge (`?searchString=Ghost`) oder aus Datenrouten (`https://localhost:5001/Movies/Ghost`) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="604b3-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="604b3-138">Bei der Modellbindung wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="604b3-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="604b3-139">Sie können jedoch von den Benutzern nicht erwarten, dass sie die URL ändern, um nach einem Film zu suchen.</span><span class="sxs-lookup"><span data-stu-id="604b3-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="604b3-140">In diesem Schritt wird eine Benutzeroberflächenoption hinzugefügt, um Filme zu filtern.</span><span class="sxs-lookup"><span data-stu-id="604b3-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="604b3-141">Wenn Sie die Routeneinschränkung `"{searchString?}"` hinzugefügt haben, entfernen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="604b3-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="604b3-142">Öffnen Sie die Datei *Pages/Movies/Index.cshtml*, und fügen Sie im folgenden Code das hervorgehobene `<form>`-Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="604b3-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="604b3-143">Das HTML-Tag `<form>` verwendet die folgenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="604b3-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="604b3-144">[Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="604b3-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="604b3-145">Bei der Übermittlung des Formulars wird die Filterzeichenfolge über die Abfragezeichenfolge an die Seite *Pages/Movies/Index* gesendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="604b3-146">Hilfsprogramm für Eingabetags</span><span class="sxs-lookup"><span data-stu-id="604b3-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="604b3-147">Speichern Sie die Änderungen, und testen Sie den Filter.</span><span class="sxs-lookup"><span data-stu-id="604b3-147">Save the changes and test the filter.</span></span>

![Indexansicht mit dem in das Filtertextfeld eingegebenen Wort „ghost“](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="604b3-149">Suche nach Genre</span><span class="sxs-lookup"><span data-stu-id="604b3-149">Search by genre</span></span>

<span data-ttu-id="604b3-150">Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="604b3-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="604b3-151">Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.</span><span class="sxs-lookup"><span data-stu-id="604b3-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="604b3-152">Die `SelectList` von Genres wird durch Projizieren der unterschiedlichen Genres erstellt.</span><span class="sxs-lookup"><span data-stu-id="604b3-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="604b3-153">Hinzufügen einer Suche anhand von Genre zur Razor Page</span><span class="sxs-lookup"><span data-stu-id="604b3-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="604b3-154">Aktualisieren Sie *Index.cshtml* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="604b3-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="604b3-155">Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem.</span><span class="sxs-lookup"><span data-stu-id="604b3-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="604b3-156">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="604b3-156">Additional resources</span></span>

* [<span data-ttu-id="604b3-157">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="604b3-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="604b3-158">[Zurück: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
> [Weiter: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="604b3-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="604b3-159">In den folgenden Abschnitten wird eine Suche nach Filmen anhand von *Genre* oder *Name* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="604b3-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="604b3-160">Fügen Sie *Pages/Movies/Index.cshtml.cs* folgende hervorgehobene Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="604b3-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="604b3-161">`SearchString`: enthält den Text, den Benutzer in das Suchtextfeld eingeben.</span><span class="sxs-lookup"><span data-stu-id="604b3-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="604b3-162">`SearchString` weist das Attribut [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) auf.</span><span class="sxs-lookup"><span data-stu-id="604b3-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="604b3-163">`[BindProperty]` bindet Formularwerte und Abfragezeichenfolgen mit dem gleichen Namen wie die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="604b3-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="604b3-164">`(SupportsGet = true)` ist für die Bindung in GET-Anforderungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="604b3-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="604b3-165">`Genres`: enthält die Liste der Genres.</span><span class="sxs-lookup"><span data-stu-id="604b3-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="604b3-166">`Genres` ermöglicht es dem Benutzer, ein Genre in der Liste auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="604b3-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="604b3-167">`SelectList` erfordert `using Microsoft.AspNetCore.Mvc.Rendering;`.</span><span class="sxs-lookup"><span data-stu-id="604b3-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="604b3-168">`MovieGenre`: enthält das spezifische Genre, das der Benutzer auswählt (z.B. Western).</span><span class="sxs-lookup"><span data-stu-id="604b3-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="604b3-169">`Genres` und `MovieGenre` werden später in diesem Tutorial verwendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="604b3-170">Aktualisieren Sie die `OnGetAsync`-Methode der Indexseite mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="604b3-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="604b3-171">Die erste Zeile der `OnGetAsync`-Methode erstellt eine [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfrage zum Auswählen der Filme:</span><span class="sxs-lookup"><span data-stu-id="604b3-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="604b3-172">Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="604b3-173">Wenn die `SearchString`-Eigenschaft nicht NULL oder leer ist, wird die Abfrage nach Filmen so geändert, dass die Suchzeichenfolge gefiltert wird:</span><span class="sxs-lookup"><span data-stu-id="604b3-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="604b3-174">Der Code `s => s.Title.Contains()` ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="604b3-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="604b3-175">Lambdaausdrücke werden in methodenbasierten [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)-Methode oder `Contains` verwendet (siehe den vorangehenden Code).</span><span class="sxs-lookup"><span data-stu-id="604b3-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="604b3-176">LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode geändert werden (z.B. `Where`, `Contains` oder `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="604b3-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="604b3-177">Stattdessen wird die Ausführung der Abfrage verzögert.</span><span class="sxs-lookup"><span data-stu-id="604b3-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="604b3-178">Dies bedeutet, dass die Auswertung eines Ausdrucks so lange hinausgezögert wird, bis dessen realisierter Wert durchlaufen oder die `ToListAsync`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="604b3-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="604b3-179">Weitere Informationen finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="604b3-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="604b3-180">**Hinweis**: Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im C#-Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="604b3-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="604b3-181">Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab.</span><span class="sxs-lookup"><span data-stu-id="604b3-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="604b3-182">In SQL Server wird `Contains` zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="604b3-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="604b3-183">In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="604b3-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="604b3-184">Navigieren Sie zur Seite „Movies“, und fügen Sie eine Abfragezeichenfolge wie z.B. `?searchString=Ghost` an die URL an (z.B. `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="604b3-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="604b3-185">Die gefilterten Filme werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="604b3-185">The filtered movies are displayed.</span></span>

![Indexansicht](search/_static/ghost.png)

<span data-ttu-id="604b3-187">Wenn die Routenvorlage der Indexseite hinzugefügt wird, kann die Suchzeichenfolge als URL-Segment (z.B. `https://localhost:5001/Movies/Ghost`) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="604b3-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="604b3-188">Die vorangehende Routeneinschränkung ermöglicht das Suchen des Titels als Routendaten (URL-Segment) anstatt als Wert einer Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="604b3-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="604b3-189">Das `?` in `"{searchString?}"` bedeutet, dass dies ein optionaler Routenparameter ist.</span><span class="sxs-lookup"><span data-stu-id="604b3-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Indexansicht mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="604b3-191">Die ASP.NET Core-Runtime verwendet die [Modellbindung](xref:mvc/models/model-binding), um den Wert der `SearchString`-Eigenschaft aus der Abfragezeichenfolge (`?searchString=Ghost`) oder aus Datenrouten (`https://localhost:5001/Movies/Ghost`) festzulegen.</span><span class="sxs-lookup"><span data-stu-id="604b3-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="604b3-192">Bei der Modellbindung wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="604b3-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="604b3-193">Sie können jedoch von den Benutzern nicht erwarten, dass sie die URL ändern, um nach einem Film zu suchen.</span><span class="sxs-lookup"><span data-stu-id="604b3-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="604b3-194">In diesem Schritt wird eine Benutzeroberflächenoption hinzugefügt, um Filme zu filtern.</span><span class="sxs-lookup"><span data-stu-id="604b3-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="604b3-195">Wenn Sie die Routeneinschränkung `"{searchString?}"` hinzugefügt haben, entfernen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="604b3-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="604b3-196">Öffnen Sie die Datei *Pages/Movies/Index.cshtml*, und fügen Sie im folgenden Code das hervorgehobene `<form>`-Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="604b3-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="604b3-197">Das HTML-Tag `<form>` verwendet die folgenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="604b3-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="604b3-198">[Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="604b3-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="604b3-199">Bei der Übermittlung des Formulars wird die Filterzeichenfolge über die Abfragezeichenfolge an die Seite *Pages/Movies/Index* gesendet.</span><span class="sxs-lookup"><span data-stu-id="604b3-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="604b3-200">Hilfsprogramm für Eingabetags</span><span class="sxs-lookup"><span data-stu-id="604b3-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="604b3-201">Speichern Sie die Änderungen, und testen Sie den Filter.</span><span class="sxs-lookup"><span data-stu-id="604b3-201">Save the changes and test the filter.</span></span>

![Indexansicht mit dem in das Filtertextfeld eingegebenen Wort „ghost“](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="604b3-203">Suche nach Genre</span><span class="sxs-lookup"><span data-stu-id="604b3-203">Search by genre</span></span>

<span data-ttu-id="604b3-204">Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="604b3-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="604b3-205">Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.</span><span class="sxs-lookup"><span data-stu-id="604b3-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="604b3-206">Die `SelectList` von Genres wird durch Projizieren der unterschiedlichen Genres erstellt.</span><span class="sxs-lookup"><span data-stu-id="604b3-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="604b3-207">Hinzufügen einer Suche anhand von Genre zur Razor Page</span><span class="sxs-lookup"><span data-stu-id="604b3-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="604b3-208">Aktualisieren Sie *Index.cshtml* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="604b3-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="604b3-209">Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem.</span><span class="sxs-lookup"><span data-stu-id="604b3-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="604b3-210">Der oben stehende Code verwendet das [Auswahl-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-select-tag-helper) und das Options-Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="604b3-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="604b3-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="604b3-211">Additional resources</span></span>

* [<span data-ttu-id="604b3-212">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="604b3-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="604b3-213">[Zurück: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
> [Weiter: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="604b3-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
