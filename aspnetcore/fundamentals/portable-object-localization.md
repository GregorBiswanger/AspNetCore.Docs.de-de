---
title: Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core
author: sebastienros
description: Dieser Artikel führt portable Objektdateien ein und erläutert die Schritte zu ihrer Verwendung in einer ASP.NET Core-Anwendung mit dem Framework Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: e4053bd5024914242db1755ca34ddbeb2f2d5013
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408122"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="7da55-103">Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7da55-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="7da55-104">Von [Sébastien Ros](https://github.com/sebastienros) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7da55-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7da55-105">Dieser Artikel erläutert die Schritte zur Verwendung von portablen Objektdateien (PO) in einer ASP.NET Core-Anwendung mit dem Framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="7da55-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="7da55-106">**Hinweis**: Orchard Core ist kein Microsoft-Produkt.</span><span class="sxs-lookup"><span data-stu-id="7da55-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="7da55-107">Microsoft bietet daher keinen Support für dieses Feature an.</span><span class="sxs-lookup"><span data-stu-id="7da55-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="7da55-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7da55-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="7da55-109">Was ist eine PO-Datei?</span><span class="sxs-lookup"><span data-stu-id="7da55-109">What is a PO file?</span></span>

<span data-ttu-id="7da55-110">PO-Dateien werden als Textdateien verteilt, die übersetzte Zeichenfolgen für eine bestimmte Sprache enthalten.</span><span class="sxs-lookup"><span data-stu-id="7da55-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="7da55-111">Einige Vorteile der Verwendung von PO-Dateien im Vergleich zu *RESX*-Dateien sind:</span><span class="sxs-lookup"><span data-stu-id="7da55-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="7da55-112">PO-Dateien unterstützen Pluralisierung; *RESX*-Dateien unterstützen sie nicht.</span><span class="sxs-lookup"><span data-stu-id="7da55-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="7da55-113">PO-Dateien werden nicht wie *RESX*-Dateien kompiliert.</span><span class="sxs-lookup"><span data-stu-id="7da55-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="7da55-114">Daher sind spezialisierte Tools und die Buildschritte nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7da55-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="7da55-115">PO-Dateien funktionieren gut mit gemeinsamen Onlinebearbeitungstools.</span><span class="sxs-lookup"><span data-stu-id="7da55-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="7da55-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="7da55-116">Example</span></span>

<span data-ttu-id="7da55-117">Hier sehen Sie eine PO-Beispieldatei, die die Übersetzung für zwei Zeichenfolgen in Französisch, einschließlich der Pluralform einer der Zeichenfolgen enthält:</span><span class="sxs-lookup"><span data-stu-id="7da55-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="7da55-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="7da55-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="7da55-119">In diesem Beispiel wird die folgende Syntax verwendet:</span><span class="sxs-lookup"><span data-stu-id="7da55-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="7da55-120">`#:`: ein Kommentar, der den Kontext der zu übersetzenden Zeichenfolge angibt.</span><span class="sxs-lookup"><span data-stu-id="7da55-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="7da55-121">Die gleiche Zeichenfolge wird möglicherweise anders übersetzt, je nachdem, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7da55-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="7da55-122">`msgid`: die nicht übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="7da55-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="7da55-123">`msgstr`: die übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="7da55-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="7da55-124">Wenn die Pluralisierung unterstützt wird, können mehrere Einträge definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7da55-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="7da55-125">`msgid_plural`: die nicht übersetzte Zeichenfolge im Plural.</span><span class="sxs-lookup"><span data-stu-id="7da55-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="7da55-126">`msgstr[0]`: die übersetzte Zeichenfolge für den Fall 0.</span><span class="sxs-lookup"><span data-stu-id="7da55-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="7da55-127">`msgstr[N]`: die übersetzte Zeichenfolge für den Fall N.</span><span class="sxs-lookup"><span data-stu-id="7da55-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="7da55-128">Die PO-Dateispezifikation finden Sie [hier](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="7da55-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="7da55-129">Konfigurieren der PO-Dateiunterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7da55-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="7da55-130">Dieses Beispiel beruht auf einer ASP.NET Core MVC-Anwendung, die aus einer Visual Studio 2017-Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="7da55-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="7da55-131">Verweisen auf das Paket</span><span class="sxs-lookup"><span data-stu-id="7da55-131">Referencing the package</span></span>

<span data-ttu-id="7da55-132">Fügen Sie einen Verweis auf das NuGet-Paket `OrchardCore.Localization.Core` hinzu.</span><span class="sxs-lookup"><span data-stu-id="7da55-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="7da55-133">Dieses ist auf [MyGet](https://www.myget.org/) unter der Paketquelle https://www.myget.org/F/orchardcore-preview/api/v3/index.json verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7da55-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="7da55-134">Die *CSPROJ*-Datei enthält nun eine Zeile, die der folgenden Zeile ähnelt (die Versionsnummer kann davon abweichen):</span><span class="sxs-lookup"><span data-stu-id="7da55-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="7da55-135">Registrieren des Diensts</span><span class="sxs-lookup"><span data-stu-id="7da55-135">Registering the service</span></span>

<span data-ttu-id="7da55-136">Fügen Sie die erforderlichen Dienste zur `ConfigureServices`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="7da55-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="7da55-137">Fügen Sie die erforderliche Middleware zur `Configure`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="7da55-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="7da55-138">Fügen Sie den folgenden Code zur Razor-Ansicht Ihrer Wahl hinzu.</span><span class="sxs-lookup"><span data-stu-id="7da55-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="7da55-139">In diesem Beispiel wird *About.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="7da55-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="7da55-140">Eine `IViewLocalizer`-Instanz wird eingefügt und zum Übersetzen des Texts „Hello world!“ verwendet.</span><span class="sxs-lookup"><span data-stu-id="7da55-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="7da55-141">Erstellen einer PO-Datei</span><span class="sxs-lookup"><span data-stu-id="7da55-141">Creating a PO file</span></span>

<span data-ttu-id="7da55-142">Erstellen Sie eine Datei mit dem Namen *\<culture code>.po* im Stammordner Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="7da55-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="7da55-143">In diesem Beispiel lautet der Dateiname *fr.po*, weil die französische Sprache verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7da55-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="7da55-144">Diese Datei speichert die zu übersetzende Zeichenfolge sowie die französische Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="7da55-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="7da55-145">Übersetzungen stellen ihre übergeordnete Kultur wieder her, wenn erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7da55-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="7da55-146">In diesem Beispiel wird die *fr.po*-Datei verwendet, wenn die angeforderte Kultur `fr-FR` oder `fr-CA` ist.</span><span class="sxs-lookup"><span data-stu-id="7da55-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="7da55-147">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="7da55-147">Testing the application</span></span>

<span data-ttu-id="7da55-148">Führen Sie Ihre Anwendung aus, und navigieren Sie zur URL`/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="7da55-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="7da55-149">Der Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="7da55-149">The text **Hello world!**</span></span> <span data-ttu-id="7da55-150">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7da55-150">is displayed.</span></span>

<span data-ttu-id="7da55-151">Navigieren Sie zur URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="7da55-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7da55-152">Der Text **Bonjour le Monde!**</span><span class="sxs-lookup"><span data-stu-id="7da55-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="7da55-153">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7da55-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="7da55-154">Pluralisierung</span><span class="sxs-lookup"><span data-stu-id="7da55-154">Pluralization</span></span>

<span data-ttu-id="7da55-155">PO-Dateien unterstützen Pluralformen. Dies ist nützlich, wenn die gleiche Zeichenfolge aufgrund einer Kardinalität unterschiedlich übersetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="7da55-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="7da55-156">Diese Aufgabe ist komplizierter, da jede Sprache benutzerdefinierte Regeln festlegt, um auf der Grundlage von Kardinalität auszuwählen, welche Zeichenfolge zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="7da55-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="7da55-157">Das Orchard Localization-Paket stellt eine API zur Verfügung, um diese anderen Pluralformen automatisch aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="7da55-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="7da55-158">Erstellen von PO-Pluraldateien</span><span class="sxs-lookup"><span data-stu-id="7da55-158">Creating pluralization PO files</span></span>

<span data-ttu-id="7da55-159">Fügen Sie den folgenden Inhalt zur zuvor erwähnten *fr.po*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="7da55-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="7da55-160">Unter [Was ist eine PO-Datei?](#what-is-a-po-file) finden Sie eine Erläuterung für das, was jeder Eintrag in diesem Beispiel darstellt.</span><span class="sxs-lookup"><span data-stu-id="7da55-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="7da55-161">Hinzufügen einer Sprache mit verschiedenen Pluralformen</span><span class="sxs-lookup"><span data-stu-id="7da55-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="7da55-162">Im vorherigen Beispiel wurden englische und französische Zeichenfolgen verwendet.</span><span class="sxs-lookup"><span data-stu-id="7da55-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="7da55-163">Englisch und Französisch haben nur zwei Pluralformen und teilen die gleichen Formregeln. Die Kardinalität der einen wird der ersten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="7da55-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="7da55-164">Alle anderen Kardinalitäten werden der zweiten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="7da55-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="7da55-165">Nicht alle Sprachen verfügen über die gleichen Regeln.</span><span class="sxs-lookup"><span data-stu-id="7da55-165">Not all languages share the same rules.</span></span> <span data-ttu-id="7da55-166">Tschechisch verfügt beispielsweise über drei Pluralformen.</span><span class="sxs-lookup"><span data-stu-id="7da55-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="7da55-167">Erstellen Sie die `cs.po`-Datei wie folgt, und beachten Sie die drei verschiedenen Übersetzungen der Pluralformen:</span><span class="sxs-lookup"><span data-stu-id="7da55-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="7da55-168">Fügen Sie für die Annahme der tschechischen Lokalisierungen `"cs"` zur Liste der unterstützten Kulturen in der `ConfigureServices`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="7da55-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="7da55-169">Bearbeiten Sie die *Views/Home/About.cshtml*-Datei zum Rendern von lokalisierten Pluralzeichenfolgen für mehrere Kardinalitäten:</span><span class="sxs-lookup"><span data-stu-id="7da55-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="7da55-170">**Hinweis**: In einem echten Szenario würde eine Variable die Anzahl darstellen.</span><span class="sxs-lookup"><span data-stu-id="7da55-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="7da55-171">Hier wiederholen wir den gleichen Code mit drei verschiedenen Werten, um einen sehr speziellen Fall verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="7da55-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="7da55-172">Beim Wechsel von Kulturen sehen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7da55-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="7da55-173">Für `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="7da55-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="7da55-174">Für `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="7da55-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="7da55-175">Für `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="7da55-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="7da55-176">Beachten Sie, dass die drei Übersetzungen für die tschechische Kultur unterschiedlich sind.</span><span class="sxs-lookup"><span data-stu-id="7da55-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="7da55-177">Die französische und englische Kultur teilen die gleiche Konstruktion für die beiden letzten übersetzten Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="7da55-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="7da55-178">Aufgaben für Fortgeschrittene</span><span class="sxs-lookup"><span data-stu-id="7da55-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="7da55-179">Kontextualisieren von Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="7da55-179">Contextualizing strings</span></span>

<span data-ttu-id="7da55-180">Anwendungen enthalten die zu übersetzenden Zeichenfolgen häufig an mehreren Stellen.</span><span class="sxs-lookup"><span data-stu-id="7da55-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="7da55-181">Dieselbe Zeichenfolge kann möglicherweise über unterschiedliche Übersetzungen in bestimmten Speicherorten innerhalb einer Anwendung verfügen (Razor-Ansichten oder Klassendateien).</span><span class="sxs-lookup"><span data-stu-id="7da55-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="7da55-182">Eine PO-Datei unterstützt das Konzept eines Dateikontexts, der zum Kategorisieren von dargestellten Zeichenfolgen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7da55-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="7da55-183">Bei der Verwendung eines Dateikontexts kann eine Zeichenfolge unterschiedlich übersetzt werden. Dies hängt vom Dateikontext (oder einem fehlenden Dateikontext) ab.</span><span class="sxs-lookup"><span data-stu-id="7da55-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="7da55-184">Die PO-Lokalisierungsdienste verwenden den Namen der vollständigen Klasse oder der Ansicht, die bei der Übersetzung einer Zeichenfolge verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7da55-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="7da55-185">Dies wird durch Festlegen des Werts im `msgctxt`-Eintrag erreicht.</span><span class="sxs-lookup"><span data-stu-id="7da55-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="7da55-186">Ziehen Sie eine geringfügige Hinzufügung zum vorherigen *fr.po*-Beispiel in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="7da55-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="7da55-187">Eine Razor-Ansicht in *Views/Home/About.cshtml* kann als Dateikontext definiert werden, indem der reservierte `msgctxt`-Eintragswert festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="7da55-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="7da55-188">Wenn `msgctxt` festgelegt ist, wird bei der Navigation zu `/Home/About?culture=fr-FR` eine Textübersetzung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7da55-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="7da55-189">Die Übersetzung wird nicht bei der Navigation zu `/Home/Contact?culture=fr-FR` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7da55-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="7da55-190">Wenn kein bestimmter Eintrag mit einem bestimmten Dateikontext abgeglichen wird, sucht der Fallbackmechanismus von Orchard Core nach einer entsprechenden PO-Datei ohne Kontext.</span><span class="sxs-lookup"><span data-stu-id="7da55-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="7da55-191">Vorausgesetzt, dass kein bestimmter Dateikontext für *Views/Home/Contact.cshtml* definiert ist, lädt eine Navigation zu `/Home/Contact?culture=fr-FR` eine PO-Datei, wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="7da55-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="7da55-192">Ändern des Speicherorts für PO-Dateien</span><span class="sxs-lookup"><span data-stu-id="7da55-192">Changing the location of PO files</span></span>

<span data-ttu-id="7da55-193">Der Standardspeicherort der PO-Dateien kann in `ConfigureServices` geändert werden:</span><span class="sxs-lookup"><span data-stu-id="7da55-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="7da55-194">In diesem Beispiel werden die PO-Dateien aus dem Ordner *Lokalisierung* geladen.</span><span class="sxs-lookup"><span data-stu-id="7da55-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="7da55-195">Implementieren einer benutzerdefinierten Logik für die Suche nach Lokalisierungsdateien</span><span class="sxs-lookup"><span data-stu-id="7da55-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="7da55-196">Wenn eine komplexere Logik erforderlich ist, um die PO-Dateien zu finden, kann die `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`-Schnittstelle implementiert und als Dienst registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7da55-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="7da55-197">Dies ist nützlich, wenn PO-Dateien an unterschiedlichen Speicherorten gespeichert oder die Dateien innerhalb einer Hierarchie von Ordnern gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="7da55-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="7da55-198">Verwenden einer anderen Standardpluralsprache</span><span class="sxs-lookup"><span data-stu-id="7da55-198">Using a different default pluralized language</span></span>

<span data-ttu-id="7da55-199">Das Paket enthält eine `Plural`-Erweiterungsmethode, die für zwei Pluralformen spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="7da55-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="7da55-200">Erstellen Sie eine Erweiterungsmethode für Sprachen, die weitere Pluralformen erfordern.</span><span class="sxs-lookup"><span data-stu-id="7da55-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="7da55-201">Mit einer Erweiterungsmethode müssen Sie keine Lokalisierungsdatei für die Standardsprache angeben &mdash; die ursprünglichen Zeichenfolgen sind bereits direkt im Code verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7da55-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="7da55-202">Sie können die generische `Plural(int count, string[] pluralForms, params object[] arguments)`-Überladung verwenden, die ein Zeichenfolgenarray von Übersetzungen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="7da55-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
