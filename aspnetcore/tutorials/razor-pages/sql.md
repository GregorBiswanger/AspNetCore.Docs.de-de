---
title: 'Teil 4: Arbeiten mit einer Datenbank und ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 7/22/2019
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: d2e18782411b1801c74fa33ba1b31bad9662f3b2
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627116"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="8e966-103">Teil 4: Arbeiten mit einer Datenbank und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e966-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="8e966-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="8e966-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="8e966-105">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8e966-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8e966-106">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="8e966-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8e966-108">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="8e966-109">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="8e966-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="8e966-110">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="8e966-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8e966-112">Der Name-Wert für die Datenbank (`Database={Database name}`) ist für den generierten Code anders.</span><span class="sxs-lookup"><span data-stu-id="8e966-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="8e966-113">Beim Name-Wert handelt es sich um einen beliebigen Wert.</span><span class="sxs-lookup"><span data-stu-id="8e966-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8e966-114">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="8e966-115">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8e966-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="8e966-116">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8e966-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8e966-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8e966-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8e966-119">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="8e966-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8e966-120">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="8e966-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8e966-121">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:\Users\<user>\`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8e966-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="8e966-122">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8e966-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="8e966-124">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/design.png)

  ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

<span data-ttu-id="8e966-127">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="8e966-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8e966-128">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8e966-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="8e966-129">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8e966-131">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="8e966-132">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="8e966-132">Seed the database</span></span>

<span data-ttu-id="8e966-133">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="8e966-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8e966-134">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8e966-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="8e966-135">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="8e966-135">Add the seed initializer</span></span>

<span data-ttu-id="8e966-136">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="8e966-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="8e966-137">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="8e966-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8e966-138">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="8e966-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="8e966-139">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="8e966-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="8e966-140">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8e966-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="8e966-141">Die folgende Ausnahme tritt auf, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="8e966-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="8e966-142">Testen der App</span><span class="sxs-lookup"><span data-stu-id="8e966-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8e966-144">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8e966-144">Delete all the records in the DB.</span></span> <span data-ttu-id="8e966-145">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="8e966-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="8e966-146">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8e966-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="8e966-147">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="8e966-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8e966-148">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="8e966-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="8e966-149">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="8e966-152">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8e966-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="8e966-153">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="8e966-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8e966-154">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8e966-155">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="8e966-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="8e966-156">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8e966-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="8e966-157">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="8e966-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="8e966-158">Im nächsten Tutorial wird die Präsentation der Daten verbessert.</span><span class="sxs-lookup"><span data-stu-id="8e966-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e966-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8e966-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8e966-160">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Updating the pages (Aktualisieren der Seiten)](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8e966-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="8e966-161">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8e966-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="8e966-162">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="8e966-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8e966-164">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="8e966-165">Weitere Informationen über die in `ConfigureServices` verwendeten Methoden finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="8e966-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="8e966-166">[Unterstützung für die Datenschutz-Grundverordnung (DSGVO) in ASP.NET Core](xref:security/gdpr) für `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="8e966-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="8e966-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="8e966-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="8e966-168">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="8e966-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="8e966-169">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="8e966-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8e966-171">Der Name-Wert für die Datenbank (`Database={Database name}`) ist für den generierten Code anders.</span><span class="sxs-lookup"><span data-stu-id="8e966-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="8e966-172">Beim Name-Wert handelt es sich um einen beliebigen Wert.</span><span class="sxs-lookup"><span data-stu-id="8e966-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8e966-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8e966-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8e966-174">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="8e966-175">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8e966-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="8e966-176">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8e966-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="8e966-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8e966-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8e966-179">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="8e966-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="8e966-180">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="8e966-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8e966-181">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8e966-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="8e966-182">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="8e966-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="8e966-184">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

<span data-ttu-id="8e966-187">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="8e966-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="8e966-188">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8e966-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="8e966-189">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8e966-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8e966-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8e966-192">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="8e966-193">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="8e966-193">Seed the database</span></span>

<span data-ttu-id="8e966-194">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="8e966-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="8e966-195">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8e966-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="8e966-196">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="8e966-196">Add the seed initializer</span></span>

<span data-ttu-id="8e966-197">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="8e966-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="8e966-198">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="8e966-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8e966-199">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="8e966-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="8e966-200">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="8e966-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="8e966-201">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8e966-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="8e966-202">Eine Produktions-App würde `Database.Migrate` nicht aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8e966-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="8e966-203">Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="8e966-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="8e966-204">SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="8e966-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="8e966-205">Die Anmeldung ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="8e966-205">The login failed.</span></span>
<span data-ttu-id="8e966-206">Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="8e966-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="8e966-207">Testen der App</span><span class="sxs-lookup"><span data-stu-id="8e966-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8e966-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e966-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8e966-209">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8e966-209">Delete all the records in the DB.</span></span> <span data-ttu-id="8e966-210">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="8e966-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="8e966-211">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seedmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8e966-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="8e966-212">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="8e966-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="8e966-213">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="8e966-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="8e966-214">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="8e966-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="8e966-217">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8e966-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="8e966-218">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="8e966-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8e966-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8e966-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8e966-220">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="8e966-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="8e966-221">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8e966-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="8e966-222">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="8e966-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8e966-223">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8e966-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8e966-224">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="8e966-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="8e966-225">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8e966-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="8e966-226">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="8e966-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="8e966-227">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="8e966-227">The app shows the seeded data:</span></span>

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55.png)

<span data-ttu-id="8e966-229">Im nächsten Tutorial wird die Präsentation der Daten bereinigt.</span><span class="sxs-lookup"><span data-stu-id="8e966-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e966-230">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8e966-230">Additional resources</span></span>

* [<span data-ttu-id="8e966-231">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="8e966-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="8e966-232">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Updating the pages (Aktualisieren der Seiten)](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="8e966-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
