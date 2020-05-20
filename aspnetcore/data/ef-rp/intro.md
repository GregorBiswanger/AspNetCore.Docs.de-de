---
<span data-ttu-id="c8e72-101">title: Razor Pages mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8 author: rick-anderson description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core ms.author: riande ms.custom: "mvc, seodec18" ms.date: 09/26/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/intro</span><span class="sxs-lookup"><span data-stu-id="c8e72-101">title: Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8 author: rick-anderson description: Shows how to create a Razor Pages app using Entity Framework Core ms.author: riande ms.custom: "mvc, seodec18" ms.date: 09/26/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/intro</span></span>
---

# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="c8e72-102">Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8</span><span class="sxs-lookup"><span data-stu-id="c8e72-102">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="c8e72-103">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c8e72-103">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c8e72-104">Dies ist das erste von vielen Tutorials, die zeigen, wie Entity Framework (EF) Core in einer [ASP.NET Core Razor Pages](xref:razor-pages/index)-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-104">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="c8e72-105">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-105">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c8e72-106">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-106">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c8e72-107">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-107">The tutorial uses the code first approach.</span></span> <span data-ttu-id="c8e72-108">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="c8e72-108">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="c8e72-109">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="c8e72-109">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="c8e72-110">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c8e72-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c8e72-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c8e72-111">Prerequisites</span></span>

* <span data-ttu-id="c8e72-112">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialserie [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-112">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-113">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-114">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-114">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="c8e72-115">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="c8e72-115">Database engines</span></span>

<span data-ttu-id="c8e72-116">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c8e72-116">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="c8e72-117">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="c8e72-117">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="c8e72-118">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="c8e72-118">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c8e72-119">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="c8e72-119">Troubleshooting</span></span>

<span data-ttu-id="c8e72-120">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="c8e72-120">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="c8e72-121">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-121">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="c8e72-122">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="c8e72-122">The sample app</span></span>

<span data-ttu-id="c8e72-123">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="c8e72-123">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="c8e72-124">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-124">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c8e72-125">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-125">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="c8e72-128">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-128">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="c8e72-129">Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="c8e72-129">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="c8e72-130">Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-130">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="c8e72-131">Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="c8e72-131">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="c8e72-132">Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-132">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8e72-134">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-134">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="c8e72-135">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-135">Build the project.</span></span>
* <span data-ttu-id="c8e72-136">Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="c8e72-137">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8e72-139">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="c8e72-140">Löschen Sie *ContosoUniversity.csproj*, und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.</span><span class="sxs-lookup"><span data-stu-id="c8e72-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="c8e72-141">Löschen Sie *Startup.cs*, und benennen Sie *StartupSQLite.cs* in *Startup.cs* um.</span><span class="sxs-lookup"><span data-stu-id="c8e72-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="c8e72-142">Löschen Sie *appSettings.json*, und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.</span><span class="sxs-lookup"><span data-stu-id="c8e72-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="c8e72-143">Löschen Sie den Ordner *Migrations*, und benennen Sie *MigrationsSQL* in *Migrations* um.</span><span class="sxs-lookup"><span data-stu-id="c8e72-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="c8e72-144">Führen Sie eine globale Suche nach `#if SQLiteVersion` aus, und entfernen Sie `#if SQLiteVersion` sowie die zugehörige `#endif`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-144">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="c8e72-145">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-145">Build the project.</span></span>
* <span data-ttu-id="c8e72-146">Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-146">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="c8e72-147">Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-147">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="c8e72-148">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-148">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="c8e72-149">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="c8e72-149">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-151">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-151">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c8e72-152">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-152">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="c8e72-153">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-153">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="c8e72-154">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-154">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="c8e72-155">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-155">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-156">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c8e72-157">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="c8e72-157">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="c8e72-158">Führen Sie die folgenden Befehle aus, um ein Razor Pages `cd`-Projekt und den neuen Projektordner zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-158">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="c8e72-159">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="c8e72-159">Set up the site style</span></span>

<span data-ttu-id="c8e72-160">Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:</span><span class="sxs-lookup"><span data-stu-id="c8e72-160">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="c8e72-161">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="c8e72-161">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="c8e72-162">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="c8e72-162">There are three occurrences.</span></span>

* <span data-ttu-id="c8e72-163">Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.</span><span class="sxs-lookup"><span data-stu-id="c8e72-163">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="c8e72-164">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-164">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="c8e72-165">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="c8e72-165">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="c8e72-166">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-166">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="c8e72-167">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="c8e72-167">The data model</span></span>

<span data-ttu-id="c8e72-168">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="c8e72-168">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="c8e72-170">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="c8e72-170">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="c8e72-171">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="c8e72-171">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="c8e72-173">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-173">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="c8e72-174">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-174">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="c8e72-175">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="c8e72-175">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="c8e72-176">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="c8e72-176">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="c8e72-177">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-177">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="c8e72-178">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="c8e72-178">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="c8e72-179">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c8e72-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c8e72-180">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="c8e72-181">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="c8e72-182">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="c8e72-183">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="c8e72-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="c8e72-184">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="c8e72-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="c8e72-185">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="c8e72-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="c8e72-186">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="c8e72-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="c8e72-187">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="c8e72-188">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="c8e72-189">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="c8e72-190">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="c8e72-190">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="c8e72-192">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="c8e72-193">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="c8e72-194">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="c8e72-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="c8e72-195">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="c8e72-196">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="c8e72-197">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c8e72-198">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="c8e72-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="c8e72-199">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="c8e72-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c8e72-200">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c8e72-201">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="c8e72-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="c8e72-202">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c8e72-203">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c8e72-204">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="c8e72-205">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="c8e72-206">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="c8e72-207">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="c8e72-208">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="c8e72-208">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="c8e72-210">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="c8e72-211">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c8e72-212">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c8e72-213">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="c8e72-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="c8e72-214">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="c8e72-215">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="c8e72-215">Scaffold Student pages</span></span>

<span data-ttu-id="c8e72-216">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="c8e72-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="c8e72-217">Eine EF Core *context*-Klasse.</span><span class="sxs-lookup"><span data-stu-id="c8e72-217">An EF Core *context* class.</span></span> <span data-ttu-id="c8e72-218">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="c8e72-219">Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="c8e72-220">Razor Pages, die Vorgänge zum Erstellen (Create), Lesen (Read), Aktualisieren (Update) und Löschen (Delete) (CRUD) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-222">Erstellen Sie einen Ordner *Students* im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="c8e72-223">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c8e72-224">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="c8e72-225">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="c8e72-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="c8e72-226">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="c8e72-227">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="c8e72-228">Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="c8e72-229">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-229">Select **Add**.</span></span>

<span data-ttu-id="c8e72-230">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="c8e72-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c8e72-232">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="c8e72-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="c8e72-233">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c8e72-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="c8e72-234">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="c8e72-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="c8e72-235">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="c8e72-236">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="c8e72-237">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="c8e72-238">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="c8e72-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="c8e72-239">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="c8e72-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="c8e72-240">Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="c8e72-241">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="c8e72-241">The scaffolding process:</span></span>

* <span data-ttu-id="c8e72-242">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="c8e72-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="c8e72-243">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="c8e72-244">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="c8e72-245">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="c8e72-246">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="c8e72-247">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="c8e72-248">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="c8e72-249">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="c8e72-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="c8e72-250">Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="c8e72-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="c8e72-251">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="c8e72-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8e72-253">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="c8e72-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="c8e72-254">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="c8e72-255">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8e72-257">Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="c8e72-258">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="c8e72-258">Update the database context class</span></span>

<span data-ttu-id="c8e72-259">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="c8e72-260">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c8e72-261">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="c8e72-262">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c8e72-263">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="c8e72-264">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="c8e72-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="c8e72-265">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c8e72-265">In EF Core terminology:</span></span>

* <span data-ttu-id="c8e72-266">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="c8e72-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="c8e72-267">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c8e72-268">Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="c8e72-269">Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="c8e72-270">Nehmen Sie eine globale Änderung im gesamten Projekt aus `_context.Student` in `_context.Students`vor, damit der Razor Pages-Code mit dem neuen DBSet-Namen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="c8e72-271">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-271">There are 8 occurrences.</span></span>

<span data-ttu-id="c8e72-272">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="c8e72-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c8e72-273">Startup.cs</span></span>

<span data-ttu-id="c8e72-274">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c8e72-275">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c8e72-276">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c8e72-277">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c8e72-278">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-280">In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c8e72-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c8e72-282">Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="c8e72-283">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c8e72-284">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="c8e72-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="c8e72-285">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c8e72-285">Create the database</span></span>

<span data-ttu-id="c8e72-286">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="c8e72-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="c8e72-287">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="c8e72-288">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="c8e72-289">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="c8e72-290">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c8e72-290">Delete the database.</span></span> <span data-ttu-id="c8e72-291">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-291">Any existing data is lost.</span></span>
* <span data-ttu-id="c8e72-292">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="c8e72-292">Change the data model.</span></span> <span data-ttu-id="c8e72-293">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="c8e72-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="c8e72-294">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-294">Run the app.</span></span>
* <span data-ttu-id="c8e72-295">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="c8e72-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="c8e72-296">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="c8e72-297">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="c8e72-298">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="c8e72-299">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="c8e72-300">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c8e72-301">Testen der App</span><span class="sxs-lookup"><span data-stu-id="c8e72-301">Test the app</span></span>

* <span data-ttu-id="c8e72-302">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-302">Run the app.</span></span>
* <span data-ttu-id="c8e72-303">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="c8e72-304">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="c8e72-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="c8e72-305">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="c8e72-305">Seed the database</span></span>

<span data-ttu-id="c8e72-306">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c8e72-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="c8e72-307">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="c8e72-308">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-308">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="c8e72-309">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="c8e72-310">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="c8e72-311">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="c8e72-312">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="c8e72-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8e72-314">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c8e72-316">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="c8e72-317">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="c8e72-317">Restart the app.</span></span>

* <span data-ttu-id="c8e72-318">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="c8e72-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="c8e72-319">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="c8e72-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-321">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c8e72-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="c8e72-322">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="c8e72-323">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="c8e72-324">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="c8e72-325">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="c8e72-326">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8e72-328">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="c8e72-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="c8e72-329">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="c8e72-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="c8e72-330">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-330">Asynchronous code</span></span>

<span data-ttu-id="c8e72-331">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="c8e72-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c8e72-332">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c8e72-333">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c8e72-334">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c8e72-335">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="c8e72-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c8e72-336">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="c8e72-337">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="c8e72-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="c8e72-338">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c8e72-339">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="c8e72-340">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="c8e72-341">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="c8e72-342">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="c8e72-343">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="c8e72-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="c8e72-344">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c8e72-345">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c8e72-346">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c8e72-347">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="c8e72-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c8e72-348">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="c8e72-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="c8e72-349">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="c8e72-350">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c8e72-351">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c8e72-352">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="c8e72-353">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="c8e72-354">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="c8e72-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c8e72-355">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c8e72-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c8e72-356">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="c8e72-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8e72-357">Die Beispiel-Web-App der Contoso University veranschaulicht, wie mit Entity Framework Core (EF Core) eine ASP.NET Core-App mit Razor Pages erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c8e72-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="c8e72-358">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="c8e72-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="c8e72-359">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="c8e72-360">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="c8e72-361">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="c8e72-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="c8e72-362">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c8e72-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c8e72-363">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c8e72-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="c8e72-366">Kenntnisse über [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c8e72-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="c8e72-367">Anfänger sollten den Artikel [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="c8e72-368">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="c8e72-368">Troubleshooting</span></span>

<span data-ttu-id="c8e72-369">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="c8e72-370">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="c8e72-371">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="c8e72-371">The Contoso University web app</span></span>

<span data-ttu-id="c8e72-372">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="c8e72-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="c8e72-373">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="c8e72-374">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-374">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="c8e72-377">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="c8e72-378">In diesem Tutorial wird Entity Framework Core mit Razor Pages thematisiert. Die Benutzeroberfläche wird nicht erläutert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="c8e72-379">Erstellen der Razor Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="c8e72-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-381">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c8e72-382">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="c8e72-383">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="c8e72-384">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="c8e72-385">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="c8e72-386">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="c8e72-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="c8e72-387">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="c8e72-389">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="c8e72-389">Set up the site style</span></span>

<span data-ttu-id="c8e72-390">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="c8e72-391">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="c8e72-392">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="c8e72-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="c8e72-393">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="c8e72-393">There are three occurrences.</span></span>

* <span data-ttu-id="c8e72-394">Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="c8e72-395">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-395">The changes are highlighted.</span></span> <span data-ttu-id="c8e72-396">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="c8e72-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="c8e72-397">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="c8e72-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="c8e72-398">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="c8e72-398">Create the data model</span></span>

<span data-ttu-id="c8e72-399">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="c8e72-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="c8e72-400">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="c8e72-400">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="c8e72-402">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="c8e72-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="c8e72-403">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="c8e72-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="c8e72-404">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="c8e72-405">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="c8e72-406">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="c8e72-407">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="c8e72-407">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="c8e72-409">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="c8e72-409">Create a *Models* folder.</span></span> <span data-ttu-id="c8e72-410">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8e72-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="c8e72-411">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="c8e72-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="c8e72-412">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="c8e72-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="c8e72-413">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="c8e72-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="c8e72-414">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="c8e72-415">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c8e72-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="c8e72-416">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="c8e72-417">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="c8e72-418">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="c8e72-419">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="c8e72-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="c8e72-420">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="c8e72-421">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="c8e72-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="c8e72-422">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="c8e72-423">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="c8e72-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="c8e72-424">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="c8e72-425">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="c8e72-426">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="c8e72-427">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="c8e72-427">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="c8e72-429">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="c8e72-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="c8e72-430">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="c8e72-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="c8e72-431">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="c8e72-432">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="c8e72-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="c8e72-433">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="c8e72-434">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="c8e72-435">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="c8e72-436">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="c8e72-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="c8e72-437">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="c8e72-438">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="c8e72-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="c8e72-439">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="c8e72-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="c8e72-440">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="c8e72-441">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="c8e72-442">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="c8e72-443">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="c8e72-444">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="c8e72-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="c8e72-445">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="c8e72-446">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="c8e72-446">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="c8e72-448">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="c8e72-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="c8e72-449">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c8e72-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="c8e72-450">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="c8e72-451">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="c8e72-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="c8e72-452">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="c8e72-452">Scaffold the student model</span></span>

<span data-ttu-id="c8e72-453">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="c8e72-454">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="c8e72-455">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-455">Build the project.</span></span>
* <span data-ttu-id="c8e72-456">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c8e72-458">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c8e72-459">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="c8e72-460">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="c8e72-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="c8e72-461">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="c8e72-462">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="c8e72-463">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="c8e72-464">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-464">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="c8e72-466">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="c8e72-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c8e72-468">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="c8e72-469">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="c8e72-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="c8e72-470">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="c8e72-470">Files created</span></span>

* <span data-ttu-id="c8e72-471">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="c8e72-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="c8e72-472">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="c8e72-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="c8e72-473">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="c8e72-473">File updates</span></span>

* <span data-ttu-id="c8e72-474">*Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="c8e72-475">*appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c8e72-476">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="c8e72-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c8e72-477">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c8e72-478">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c8e72-479">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c8e72-480">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c8e72-481">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="c8e72-482">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="c8e72-483">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c8e72-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="c8e72-484">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c8e72-485">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="c8e72-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="c8e72-486">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="c8e72-486">Update main</span></span>

<span data-ttu-id="c8e72-487">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="c8e72-488">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="c8e72-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="c8e72-489">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="c8e72-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="c8e72-490">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="c8e72-491">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="c8e72-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="c8e72-492">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="c8e72-493">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-493">If it exists, no action is taken.</span></span> <span data-ttu-id="c8e72-494">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="c8e72-495">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="c8e72-496">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="c8e72-497">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="c8e72-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="c8e72-498">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c8e72-498">Delete the DB.</span></span>
* <span data-ttu-id="c8e72-499">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="c8e72-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="c8e72-500">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c8e72-500">Run the app.</span></span>
* <span data-ttu-id="c8e72-501">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="c8e72-502">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="c8e72-503">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="c8e72-504">Testen der App</span><span class="sxs-lookup"><span data-stu-id="c8e72-504">Test the app</span></span>

<span data-ttu-id="c8e72-505">Führen Sie die App aus, und akzeptieren Sie die Cookierichtlinie.</span><span class="sxs-lookup"><span data-stu-id="c8e72-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="c8e72-506">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="c8e72-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="c8e72-507">Weitere Informationen zur Cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c8e72-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="c8e72-508">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="c8e72-509">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="c8e72-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="c8e72-510">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="c8e72-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="c8e72-511">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="c8e72-512">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c8e72-513">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="c8e72-514">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="c8e72-515">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="c8e72-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="c8e72-516">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="c8e72-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="c8e72-517">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="c8e72-517">In EF Core terminology:</span></span>

* <span data-ttu-id="c8e72-518">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="c8e72-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="c8e72-519">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c8e72-520">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="c8e72-521">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="c8e72-522">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="c8e72-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="c8e72-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="c8e72-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="c8e72-524">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="c8e72-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="c8e72-525">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="c8e72-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="c8e72-526">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="c8e72-527">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="c8e72-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="c8e72-528">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="c8e72-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="c8e72-529">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c8e72-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="c8e72-530">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="c8e72-531">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="c8e72-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="c8e72-532">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="c8e72-533">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="c8e72-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="c8e72-534">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="c8e72-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="c8e72-535">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c8e72-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="c8e72-536">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="c8e72-537">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="c8e72-538">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="c8e72-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="c8e72-539">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="c8e72-540">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="c8e72-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c8e72-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c8e72-542">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="c8e72-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c8e72-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c8e72-544">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="c8e72-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="c8e72-545">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c8e72-545">View the DB</span></span>

<span data-ttu-id="c8e72-546">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="c8e72-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="c8e72-547">Daher lautet der Name der Datenbank „SchoolContext-{GUID}“.</span><span class="sxs-lookup"><span data-stu-id="c8e72-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="c8e72-548">Die GUID ist für jeden Benutzer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="c8e72-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="c8e72-549">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c8e72-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="c8e72-550">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="c8e72-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="c8e72-551">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="c8e72-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="c8e72-552">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="c8e72-553">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="c8e72-553">Asynchronous code</span></span>

<span data-ttu-id="c8e72-554">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="c8e72-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="c8e72-555">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e72-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c8e72-556">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c8e72-557">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c8e72-558">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="c8e72-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c8e72-559">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c8e72-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="c8e72-560">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="c8e72-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="c8e72-561">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="c8e72-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c8e72-562">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="c8e72-563">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="c8e72-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="c8e72-564">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="c8e72-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="c8e72-565">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="c8e72-566">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="c8e72-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="c8e72-567">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="c8e72-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="c8e72-568">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="c8e72-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="c8e72-569">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="c8e72-570">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c8e72-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="c8e72-571">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="c8e72-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="c8e72-572">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="c8e72-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="c8e72-573">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="c8e72-574">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="c8e72-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="c8e72-575">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="c8e72-576">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c8e72-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="c8e72-577">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="c8e72-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="c8e72-578">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="c8e72-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="c8e72-579">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="c8e72-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="c8e72-580">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c8e72-580">Additional resources</span></span>

* [<span data-ttu-id="c8e72-581">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c8e72-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="c8e72-582">Nächste</span><span class="sxs-lookup"><span data-stu-id="c8e72-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
