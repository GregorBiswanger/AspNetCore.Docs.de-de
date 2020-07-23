---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 2383934070a65b8131e890a170186b736d3fcec0
ms.sourcegitcommit: d00a200bc8347af794b24184da14ad5c8b6bba9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86869990"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="f3240-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3240-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="f3240-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f3240-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f3240-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="f3240-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3240-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f3240-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f3240-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f3240-107">Create a web API project.</span></span>
> * <span data-ttu-id="f3240-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f3240-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f3240-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f3240-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f3240-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f3240-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f3240-111">Call the web API with Postman.</span></span>

<span data-ttu-id="f3240-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="f3240-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f3240-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="f3240-113">Overview</span></span>

<span data-ttu-id="f3240-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="f3240-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f3240-115">API</span><span class="sxs-lookup"><span data-stu-id="f3240-115">API</span></span> | <span data-ttu-id="f3240-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f3240-116">Description</span></span> | <span data-ttu-id="f3240-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f3240-117">Request body</span></span> | <span data-ttu-id="f3240-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="f3240-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f3240-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="f3240-119">Get all to-do items</span></span> | <span data-ttu-id="f3240-120">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-120">None</span></span> | <span data-ttu-id="f3240-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f3240-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f3240-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="f3240-122">Get an item by ID</span></span> | <span data-ttu-id="f3240-123">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-123">None</span></span> | <span data-ttu-id="f3240-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f3240-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="f3240-125">Add a new item</span></span> | <span data-ttu-id="f3240-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-126">To-do item</span></span> | <span data-ttu-id="f3240-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f3240-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f3240-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-129">To-do item</span></span> | <span data-ttu-id="f3240-130">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-130">None</span></span> |
|<span data-ttu-id="f3240-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3240-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3240-133">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-133">None</span></span> | <span data-ttu-id="f3240-134">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-134">None</span></span>|

<span data-ttu-id="f3240-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="f3240-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f3240-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f3240-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f3240-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="f3240-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f3240-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f3240-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f3240-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f3240-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="f3240-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="f3240-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3240-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f3240-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f3240-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f3240-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f3240-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f3240-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f3240-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="f3240-158">The preceding commands:</span></span>

  * <span data-ttu-id="f3240-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="f3240-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f3240-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f3240-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3240-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="f3240-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f3240-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f3240-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="f3240-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f3240-167">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 3.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="f3240-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-168">Select **Next**.</span></span>

* <span data-ttu-id="f3240-169">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f3240-171">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="f3240-172">Testen der API</span><span class="sxs-lookup"><span data-stu-id="f3240-172">Test the API</span></span>

<span data-ttu-id="f3240-173">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="f3240-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="f3240-174">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="f3240-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3240-176">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3240-177">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f3240-178">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f3240-179">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3240-181">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3240-182">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f3240-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-183">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3240-184">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="f3240-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f3240-185">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f3240-186">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f3240-187">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f3240-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="f3240-188">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-188">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="f3240-189">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f3240-189">Add a model class</span></span>

<span data-ttu-id="f3240-190">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f3240-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f3240-191">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f3240-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-193">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f3240-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f3240-194">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f3240-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3240-195">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f3240-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="f3240-196">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3240-197">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f3240-198">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3240-200">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f3240-201">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3240-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3240-203">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f3240-203">Right-click the project.</span></span> <span data-ttu-id="f3240-204">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f3240-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3240-205">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f3240-205">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f3240-207">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f3240-208">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="f3240-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f3240-209">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f3240-210">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f3240-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f3240-211">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f3240-212">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-212">Add a database context</span></span>

<span data-ttu-id="f3240-213">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f3240-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f3240-214">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="f3240-216">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="f3240-216">Add NuGet packages</span></span>

* <span data-ttu-id="f3240-217">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="f3240-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f3240-218">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="f3240-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="f3240-219">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="f3240-220">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="f3240-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="f3240-221">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f3240-223">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="f3240-224">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3240-225">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f3240-226">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3240-227">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f3240-228">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="f3240-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f3240-229">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-229">Register the database context</span></span>

<span data-ttu-id="f3240-230">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f3240-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f3240-231">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="f3240-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="f3240-232">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f3240-233">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-233">The preceding code:</span></span>

* <span data-ttu-id="f3240-234">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="f3240-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f3240-235">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="f3240-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f3240-236">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="f3240-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f3240-237">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="f3240-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-239">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="f3240-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f3240-240">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f3240-241">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f3240-242">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f3240-243">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f3240-244">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f3240-245">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f3240-246">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f3240-247">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f3240-248">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="f3240-248">The preceding commands:</span></span>

* <span data-ttu-id="f3240-249">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="f3240-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f3240-250">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="f3240-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f3240-251">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f3240-252">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="f3240-252">The generated code:</span></span>

* <span data-ttu-id="f3240-253">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="f3240-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f3240-254">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="f3240-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f3240-255">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f3240-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f3240-256">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f3240-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f3240-257">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f3240-258">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="f3240-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f3240-259">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="f3240-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f3240-260">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="f3240-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f3240-261">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f3240-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f3240-262">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f3240-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="f3240-263">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="f3240-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="f3240-264">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="f3240-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f3240-265">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f3240-266">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="f3240-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f3240-267">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="f3240-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f3240-268">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="f3240-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="f3240-269">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f3240-270">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f3240-271">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f3240-272">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f3240-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f3240-273">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="f3240-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f3240-274">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f3240-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f3240-275">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="f3240-275">Install Postman</span></span>

<span data-ttu-id="f3240-276">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f3240-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f3240-277">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="f3240-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f3240-278">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="f3240-278">Start the web app.</span></span>
* <span data-ttu-id="f3240-279">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="f3240-279">Start Postman.</span></span>
* <span data-ttu-id="f3240-280">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f3240-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f3240-281">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f3240-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f3240-282">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="f3240-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f3240-283">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="f3240-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f3240-284">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f3240-284">Create a new request.</span></span>
* <span data-ttu-id="f3240-285">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f3240-286">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="f3240-287">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f3240-288">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f3240-289">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="f3240-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f3240-290">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-290">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f3240-292">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="f3240-292">Test the location header URI</span></span>

* <span data-ttu-id="f3240-293">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f3240-294">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="f3240-294">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="f3240-296">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-296">Set the method to GET.</span></span>
* <span data-ttu-id="f3240-297">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="f3240-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f3240-298">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f3240-299">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="f3240-299">Examine the GET methods</span></span>

<span data-ttu-id="f3240-300">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="f3240-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f3240-301">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f3240-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f3240-302">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3240-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f3240-303">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="f3240-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f3240-304">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="f3240-304">Test Get with Postman</span></span>

* <span data-ttu-id="f3240-305">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f3240-305">Create a new request.</span></span>
* <span data-ttu-id="f3240-306">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f3240-307">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f3240-308">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f3240-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f3240-309">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f3240-310">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-310">Select **Send**.</span></span>

<span data-ttu-id="f3240-311">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f3240-311">This app uses an in-memory database.</span></span> <span data-ttu-id="f3240-312">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f3240-313">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="f3240-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f3240-314">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="f3240-314">Routing and URL paths</span></span>

<span data-ttu-id="f3240-315">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="f3240-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f3240-316">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="f3240-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f3240-317">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="f3240-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f3240-318">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f3240-319">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="f3240-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f3240-320">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="f3240-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f3240-321">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="f3240-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f3240-322">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-322">This sample doesn't use a template.</span></span> <span data-ttu-id="f3240-323">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f3240-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f3240-324">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="f3240-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f3240-325">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f3240-326">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f3240-326">Return values</span></span>

<span data-ttu-id="f3240-327">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f3240-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f3240-328">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="f3240-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f3240-329">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="f3240-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f3240-330">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="f3240-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f3240-331">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="f3240-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f3240-332">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="f3240-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f3240-333">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f3240-334">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f3240-335">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f3240-336">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-336">The PutTodoItem method</span></span>

<span data-ttu-id="f3240-337">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f3240-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f3240-338">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f3240-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f3240-339">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f3240-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f3240-340">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f3240-341">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f3240-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f3240-342">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="f3240-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f3240-343">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="f3240-344">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f3240-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f3240-345">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f3240-346">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f3240-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f3240-347">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="f3240-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f3240-348">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="f3240-348">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f3240-350">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="f3240-351">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f3240-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f3240-352">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f3240-353">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="f3240-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f3240-354">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f3240-355">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f3240-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f3240-356">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f3240-357">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="f3240-357">Prevent over-posting</span></span>

<span data-ttu-id="f3240-358">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f3240-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f3240-359">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="f3240-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f3240-360">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="f3240-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f3240-361">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="f3240-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f3240-362">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="f3240-363">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f3240-363">A DTO may be used to:</span></span>

* <span data-ttu-id="f3240-364">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="f3240-364">Prevent over-posting.</span></span>
* <span data-ttu-id="f3240-365">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="f3240-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f3240-366">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="f3240-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f3240-367">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="f3240-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f3240-368">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="f3240-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f3240-369">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="f3240-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="f3240-370">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="f3240-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f3240-371">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="f3240-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f3240-372">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="f3240-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f3240-373">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="f3240-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f3240-374">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="f3240-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f3240-375">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f3240-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="f3240-376">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f3240-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3240-377">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f3240-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f3240-378">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f3240-378">Create a web API project.</span></span>
> * <span data-ttu-id="f3240-379">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f3240-380">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f3240-380">Add a controller.</span></span>
> * <span data-ttu-id="f3240-381">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f3240-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="f3240-382">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="f3240-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f3240-383">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="f3240-383">Specify return values.</span></span>
> * <span data-ttu-id="f3240-384">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f3240-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f3240-385">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f3240-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="f3240-386">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="f3240-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="f3240-387">Übersicht</span><span class="sxs-lookup"><span data-stu-id="f3240-387">Overview</span></span>

<span data-ttu-id="f3240-388">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="f3240-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f3240-389">API</span><span class="sxs-lookup"><span data-stu-id="f3240-389">API</span></span> | <span data-ttu-id="f3240-390">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f3240-390">Description</span></span> | <span data-ttu-id="f3240-391">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f3240-391">Request body</span></span> | <span data-ttu-id="f3240-392">Antworttext</span><span class="sxs-lookup"><span data-stu-id="f3240-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f3240-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f3240-393">GET /api/TodoItems</span></span> | <span data-ttu-id="f3240-394">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="f3240-394">Get all to-do items</span></span> | <span data-ttu-id="f3240-395">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-395">None</span></span> | <span data-ttu-id="f3240-396">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f3240-396">Array of to-do items</span></span>|
|<span data-ttu-id="f3240-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f3240-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="f3240-398">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="f3240-398">Get an item by ID</span></span> | <span data-ttu-id="f3240-399">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-399">None</span></span> | <span data-ttu-id="f3240-400">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-400">To-do item</span></span>|
|<span data-ttu-id="f3240-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f3240-401">POST /api/TodoItems</span></span> | <span data-ttu-id="f3240-402">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="f3240-402">Add a new item</span></span> | <span data-ttu-id="f3240-403">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-403">To-do item</span></span> | <span data-ttu-id="f3240-404">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-404">To-do item</span></span> |
|<span data-ttu-id="f3240-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f3240-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="f3240-406">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f3240-407">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f3240-407">To-do item</span></span> | <span data-ttu-id="f3240-408">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-408">None</span></span> |
|<span data-ttu-id="f3240-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3240-410">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3240-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3240-411">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-411">None</span></span> | <span data-ttu-id="f3240-412">Keine</span><span class="sxs-lookup"><span data-stu-id="f3240-412">None</span></span>|

<span data-ttu-id="f3240-413">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="f3240-413">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f3240-419">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f3240-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-422">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f3240-423">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="f3240-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-425">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f3240-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f3240-426">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f3240-427">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f3240-428">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="f3240-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="f3240-429">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="f3240-430">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-430">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3240-433">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f3240-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f3240-434">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f3240-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f3240-435">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f3240-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f3240-436">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f3240-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f3240-437">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-438">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3240-439">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="f3240-439">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f3240-441">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f3240-442">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="f3240-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="f3240-443">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die neueste .NET Core 2.x-Version als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="f3240-444">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f3240-444">Select **Next**.</span></span>

* <span data-ttu-id="f3240-445">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="f3240-447">Testen der API</span><span class="sxs-lookup"><span data-stu-id="f3240-447">Test the API</span></span>

<span data-ttu-id="f3240-448">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="f3240-448">The project template creates a `values` API.</span></span> <span data-ttu-id="f3240-449">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="f3240-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3240-451">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3240-452">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f3240-453">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f3240-454">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3240-456">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3240-457">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f3240-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-458">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3240-459">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="f3240-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f3240-460">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f3240-461">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f3240-462">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f3240-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f3240-463">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="f3240-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="f3240-464">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f3240-464">Add a model class</span></span>

<span data-ttu-id="f3240-465">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f3240-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f3240-466">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f3240-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-468">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f3240-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f3240-469">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f3240-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3240-470">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f3240-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="f3240-471">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3240-472">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f3240-473">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f3240-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3240-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3240-475">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f3240-476">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3240-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f3240-477">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3240-478">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f3240-478">Right-click the project.</span></span> <span data-ttu-id="f3240-479">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f3240-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3240-480">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f3240-480">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f3240-482">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f3240-483">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="f3240-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f3240-484">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f3240-485">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f3240-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f3240-486">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f3240-487">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-487">Add a database context</span></span>

<span data-ttu-id="f3240-488">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f3240-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f3240-489">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-491">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3240-492">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f3240-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f3240-493">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3240-494">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f3240-495">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f3240-496">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f3240-496">Register the database context</span></span>

<span data-ttu-id="f3240-497">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f3240-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f3240-498">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="f3240-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="f3240-499">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f3240-500">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-500">The preceding code:</span></span>

* <span data-ttu-id="f3240-501">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="f3240-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f3240-502">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="f3240-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f3240-503">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="f3240-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f3240-504">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f3240-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-506">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="f3240-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f3240-507">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f3240-508">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f3240-509">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f3240-511">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3240-512">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f3240-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f3240-513">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f3240-514">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-514">The preceding code:</span></span>

* <span data-ttu-id="f3240-515">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="f3240-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f3240-516">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="f3240-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f3240-517">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="f3240-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f3240-518">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f3240-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f3240-519">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f3240-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f3240-520">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f3240-521">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f3240-522">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f3240-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f3240-523">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="f3240-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f3240-524">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="f3240-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="f3240-525">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="f3240-525">Add Get methods</span></span>

<span data-ttu-id="f3240-526">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="f3240-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f3240-527">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="f3240-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f3240-528">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f3240-528">Stop the app if it's still running.</span></span> <span data-ttu-id="f3240-529">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="f3240-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="f3240-530">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f3240-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f3240-531">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3240-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f3240-532">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="f3240-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="f3240-533">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="f3240-533">Routing and URL paths</span></span>

<span data-ttu-id="f3240-534">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="f3240-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f3240-535">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="f3240-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f3240-536">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="f3240-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f3240-537">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="f3240-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f3240-538">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="f3240-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f3240-539">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="f3240-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f3240-540">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="f3240-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f3240-541">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-541">This sample doesn't use a template.</span></span> <span data-ttu-id="f3240-542">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f3240-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f3240-543">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="f3240-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f3240-544">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f3240-545">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f3240-545">Return values</span></span>

<span data-ttu-id="f3240-546">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f3240-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f3240-547">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="f3240-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f3240-548">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="f3240-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f3240-549">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="f3240-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f3240-550">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="f3240-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f3240-551">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="f3240-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f3240-552">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f3240-553">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f3240-554">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="f3240-555">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="f3240-556">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f3240-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f3240-557">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="f3240-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="f3240-558">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="f3240-558">Start the web app.</span></span>
* <span data-ttu-id="f3240-559">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="f3240-559">Start Postman.</span></span>
* <span data-ttu-id="f3240-560">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f3240-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3240-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3240-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3240-562">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f3240-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f3240-563">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f3240-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3240-564">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f3240-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="f3240-565">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="f3240-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="f3240-566">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="f3240-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f3240-567">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f3240-567">Create a new request.</span></span>
  * <span data-ttu-id="f3240-568">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f3240-569">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f3240-570">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f3240-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f3240-571">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f3240-572">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-572">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="f3240-574">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-574">Add a Create method</span></span>

<span data-ttu-id="f3240-575">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3240-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f3240-576">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f3240-577">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="f3240-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f3240-578">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f3240-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f3240-579">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f3240-580">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f3240-581">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f3240-582">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="f3240-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f3240-583">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f3240-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f3240-584">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="f3240-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f3240-585">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f3240-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="f3240-586">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="f3240-587">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f3240-587">Build the project.</span></span>
* <span data-ttu-id="f3240-588">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f3240-589">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="f3240-590">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f3240-591">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f3240-592">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="f3240-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f3240-593">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-593">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="f3240-595">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f3240-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f3240-596">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="f3240-596">Test the location header URI</span></span>

* <span data-ttu-id="f3240-597">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f3240-598">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="f3240-598">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f3240-600">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-600">Set the method to GET.</span></span>
* <span data-ttu-id="f3240-601">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="f3240-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="f3240-602">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="f3240-603">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="f3240-604">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3240-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f3240-605">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f3240-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f3240-606">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f3240-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f3240-607">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="f3240-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f3240-608">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f3240-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f3240-609">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="f3240-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f3240-610">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="f3240-611">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f3240-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f3240-612">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="f3240-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f3240-613">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f3240-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f3240-614">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="f3240-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f3240-615">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="f3240-615">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="f3240-617">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="f3240-618">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f3240-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f3240-619">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f3240-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f3240-620">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f3240-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f3240-621">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="f3240-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f3240-622">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="f3240-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f3240-623">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="f3240-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="f3240-624">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f3240-624">Select **Send**.</span></span>

<span data-ttu-id="f3240-625">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="f3240-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="f3240-626">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="f3240-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f3240-627">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f3240-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="f3240-628">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="f3240-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="f3240-629">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f3240-629">jQuery initiates the request.</span></span> <span data-ttu-id="f3240-630">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f3240-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f3240-631">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f3240-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="f3240-632">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f3240-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="f3240-633">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f3240-634">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="f3240-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f3240-635">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f3240-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f3240-636">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f3240-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f3240-637">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="f3240-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f3240-638">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f3240-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f3240-639">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="f3240-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f3240-640">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="f3240-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f3240-641">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="f3240-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f3240-642">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f3240-642">Get a list of to-do items</span></span>

<span data-ttu-id="f3240-643">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f3240-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f3240-644">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="f3240-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f3240-645">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f3240-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="f3240-646">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f3240-646">Add a to-do item</span></span>

<span data-ttu-id="f3240-647">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="f3240-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="f3240-648">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f3240-649">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="f3240-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f3240-650">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f3240-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="f3240-651">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f3240-651">Update a to-do item</span></span>

<span data-ttu-id="f3240-652">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="f3240-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f3240-653">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="f3240-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f3240-654">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f3240-654">Delete a to-do item</span></span>

<span data-ttu-id="f3240-655">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="f3240-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="f3240-656">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="f3240-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="f3240-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="f3240-657">Server4.md)]</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3240-658">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f3240-658">Additional resources</span></span>

<span data-ttu-id="f3240-659">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="f3240-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f3240-660">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="f3240-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f3240-661">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="f3240-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="f3240-662">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="f3240-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
