---
title: Erstellen einer Web-API mit ASP.NET Core und MongoDB
author: prkhandelwal
description: In diesem Tutorial wird beschrieben, wie Sie eine ASP.NET Core-Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 831479f04551441b079d3f34d043c7486bad7ac0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409019"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="d67d6-103">Erstellen einer Web-API mit ASP.NET Core und MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="d67d6-104">Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d67d6-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d67d6-105">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="d67d6-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="d67d6-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d67d6-107">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-107">Configure MongoDB</span></span>
> * <span data-ttu-id="d67d6-108">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="d67d6-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="d67d6-109">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="d67d6-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="d67d6-110">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="d67d6-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="d67d6-111">Anpassen der JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="d67d6-111">Customize JSON serialization</span></span>

<span data-ttu-id="d67d6-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d67d6-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d67d6-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d67d6-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d67d6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d67d6-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="d67d6-115">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="d67d6-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d67d6-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="d67d6-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d67d6-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d67d6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d67d6-119">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="d67d6-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d67d6-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d67d6-121">C# für Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="d67d6-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d67d6-123">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d67d6-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d67d6-124">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="d67d6-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d67d6-125">Visual Studio für Mac Version 7.7 oder höher</span><span class="sxs-lookup"><span data-stu-id="d67d6-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="d67d6-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="d67d6-127">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-127">Configure MongoDB</span></span>

<span data-ttu-id="d67d6-128">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="d67d6-129">Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<version_number>\\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="d67d6-130">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="d67d6-131">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="d67d6-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="d67d6-132">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="d67d6-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="d67d6-133">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="d67d6-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="d67d6-134">Beispiel: *C:\\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="d67d6-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="d67d6-135">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d67d6-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="d67d6-136">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="d67d6-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="d67d6-137">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="d67d6-137">Open a command shell.</span></span> <span data-ttu-id="d67d6-138">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="d67d6-139">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="d67d6-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="d67d6-140">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="d67d6-140">Open another command shell instance.</span></span> <span data-ttu-id="d67d6-141">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="d67d6-142">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d67d6-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="d67d6-143">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="d67d6-144">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d67d6-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="d67d6-145">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="d67d6-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="d67d6-146">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="d67d6-147">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="d67d6-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="d67d6-148">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="d67d6-149">Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="d67d6-150">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="d67d6-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="d67d6-151">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="d67d6-152">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="d67d6-153">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="d67d6-153">The database is ready.</span></span> <span data-ttu-id="d67d6-154">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="d67d6-155">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="d67d6-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d67d6-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d67d6-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d67d6-157">Klicken Sie auf **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="d67d6-158">Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-159">Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-160">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="d67d6-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="d67d6-161">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-162">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="d67d6-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d67d6-163">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d67d6-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="d67d6-164">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d67d6-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d67d6-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d67d6-166">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d67d6-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="d67d6-167">Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d67d6-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="d67d6-168">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'BooksApi' (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „BooksApi“ nicht vorhanden). Add them? (Sollen sie hinzugefügt werden?)** .</span><span class="sxs-lookup"><span data-stu-id="d67d6-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="d67d6-169">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-169">Select **Yes**.</span></span>
1. <span data-ttu-id="d67d6-170">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="d67d6-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d67d6-171">Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d67d6-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="d67d6-172">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d67d6-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d67d6-173">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d67d6-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d67d6-174">Klicken Sie in Visual Studio für Mac-Versionen vor Version 8.6 auf der Randleiste auf **Datei** > **Neue Projektmappe** >  **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="d67d6-175">Klicken Sie in Version 8.6 oder höher auf der Randleiste auf **Datei** > **Neue Projektmappe** > **Web and Console** > **App** (App und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d67d6-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="d67d6-176">Wählen Sie die C#-Projektvorlage **ASP.NET Core** > **API** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-177">Wählen Sie aus der Dropdownliste **Zielframework** die Option **.NET Core 3.1** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-178">Geben Sie als \**Projektnamen\*\*\*BooksApi* ein, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-179">Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="d67d6-180">Geben Sie in das Suchfeld *MongoDB.Driver* ein, wählen Sie das Paket *MongoDB.Driver* aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="d67d6-181">Klicken Sie im Dialogfeld **Zustimmung zur Lizenz** auf die Schaltfläche **Annehmen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="d67d6-182">Hinzufügen eines Entitätsmodells</span><span class="sxs-lookup"><span data-stu-id="d67d6-182">Add an entity model</span></span>

1. <span data-ttu-id="d67d6-183">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="d67d6-184">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="d67d6-185">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,</span><span class="sxs-lookup"><span data-stu-id="d67d6-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="d67d6-186">um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="d67d6-187">Sie wird mit [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="d67d6-188">Sie wird mit [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, um die Übergabe des Parameters als Typ `string` anstelle einer [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="d67d6-189">Mongo behandelt die Konvertierung von `string` zu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="d67d6-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="d67d6-190">Die Eigenschaft `BookName` ist mit dem Attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="d67d6-191">Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="d67d6-192">Hinzufügen eines Konfigurationsmodells</span><span class="sxs-lookup"><span data-stu-id="d67d6-192">Add a configuration model</span></span>

1. <span data-ttu-id="d67d6-193">Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="d67d6-194">Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="d67d6-195">Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="d67d6-196">Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="d67d6-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="d67d6-197">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="d67d6-198">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-198">In the preceding code:</span></span>

   * <span data-ttu-id="d67d6-199">Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Abhängigkeitsinjektionscontainer (DI, Dependency Injection) registriert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="d67d6-200">D. h. die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts wird beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="d67d6-201">Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d67d6-202">Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="d67d6-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="d67d6-203">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="d67d6-204">Hinzufügen eines CRUD-Vorgangsdiensts</span><span class="sxs-lookup"><span data-stu-id="d67d6-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="d67d6-205">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="d67d6-206">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="d67d6-207">Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="d67d6-208">Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="d67d6-209">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="d67d6-210">Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="d67d6-211">Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="d67d6-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="d67d6-212">Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="d67d6-213">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="d67d6-214">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="d67d6-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz für das Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="d67d6-216">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="d67d6-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="d67d6-218">In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d67d6-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="d67d6-219">Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="d67d6-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="d67d6-220">Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="d67d6-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="d67d6-221">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="d67d6-222">`TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="d67d6-223">`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="d67d6-224">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="d67d6-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="d67d6-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="d67d6-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="d67d6-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="d67d6-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d67d6-229">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="d67d6-229">Add a controller</span></span>

<span data-ttu-id="d67d6-230">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="d67d6-231">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="d67d6-231">The preceding web API controller:</span></span>

* <span data-ttu-id="d67d6-232">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="d67d6-233">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="d67d6-234">Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d67d6-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="d67d6-235">Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="d67d6-236">`CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="d67d6-237">Der `Location`-Header gibt den URI des neu erstellten Buchs an.</span><span class="sxs-lookup"><span data-stu-id="d67d6-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="d67d6-238">Testen der Web-API</span><span class="sxs-lookup"><span data-stu-id="d67d6-238">Test the web API</span></span>

1. <span data-ttu-id="d67d6-239">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d67d6-239">Build and run the app.</span></span>

1. <span data-ttu-id="d67d6-240">Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="d67d6-241">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="d67d6-242">Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="d67d6-243">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="d67d6-244">Konfigurieren von JSON-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="d67d6-244">Configure JSON serialization options</span></span>

<span data-ttu-id="d67d6-245">Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="d67d6-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="d67d6-246">Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.</span><span class="sxs-lookup"><span data-stu-id="d67d6-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="d67d6-247">Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="d67d6-248">Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="d67d6-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="d67d6-249">JSON.NET wurde aus dem freigegebenen ASP.NET-Framework entfernt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="d67d6-250">Fügen Sie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="d67d6-251">Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddControllers`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d67d6-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="d67d6-252">Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="d67d6-253">Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="d67d6-254">Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:</span><span class="sxs-lookup"><span data-stu-id="d67d6-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="d67d6-255">Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="d67d6-256">Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="d67d6-257">Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte.</span><span class="sxs-lookup"><span data-stu-id="d67d6-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="d67d6-258">Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d67d6-259">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="d67d6-260">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="d67d6-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d67d6-261">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-261">Configure MongoDB</span></span>
> * <span data-ttu-id="d67d6-262">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="d67d6-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="d67d6-263">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="d67d6-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="d67d6-264">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="d67d6-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="d67d6-265">Anpassen der JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="d67d6-265">Customize JSON serialization</span></span>

<span data-ttu-id="d67d6-266">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d67d6-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d67d6-267">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d67d6-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d67d6-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d67d6-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="d67d6-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d67d6-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d67d6-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="d67d6-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d67d6-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d67d6-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d67d6-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d67d6-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d67d6-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d67d6-275">C# für Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="d67d6-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d67d6-277">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d67d6-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d67d6-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d67d6-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d67d6-279">Visual Studio für Mac Version 7.7 oder höher</span><span class="sxs-lookup"><span data-stu-id="d67d6-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="d67d6-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="d67d6-281">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="d67d6-281">Configure MongoDB</span></span>

<span data-ttu-id="d67d6-282">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="d67d6-283">Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<version_number>\\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="d67d6-284">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="d67d6-285">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="d67d6-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="d67d6-286">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="d67d6-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="d67d6-287">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="d67d6-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="d67d6-288">Beispiel: *C:\\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="d67d6-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="d67d6-289">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d67d6-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="d67d6-290">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="d67d6-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="d67d6-291">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="d67d6-291">Open a command shell.</span></span> <span data-ttu-id="d67d6-292">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="d67d6-293">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="d67d6-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="d67d6-294">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="d67d6-294">Open another command shell instance.</span></span> <span data-ttu-id="d67d6-295">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="d67d6-296">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d67d6-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="d67d6-297">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="d67d6-298">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d67d6-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="d67d6-299">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="d67d6-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="d67d6-300">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="d67d6-301">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="d67d6-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="d67d6-302">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="d67d6-303">Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="d67d6-304">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="d67d6-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="d67d6-305">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="d67d6-306">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="d67d6-307">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="d67d6-307">The database is ready.</span></span> <span data-ttu-id="d67d6-308">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="d67d6-309">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="d67d6-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d67d6-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d67d6-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d67d6-311">Klicken Sie auf **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="d67d6-312">Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-313">Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-314">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="d67d6-315">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-316">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="d67d6-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d67d6-317">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d67d6-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="d67d6-318">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d67d6-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d67d6-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d67d6-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d67d6-320">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d67d6-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="d67d6-321">Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d67d6-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="d67d6-322">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'BooksApi' (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „BooksApi“ nicht vorhanden). Add them? (Sollen sie hinzugefügt werden?)** .</span><span class="sxs-lookup"><span data-stu-id="d67d6-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="d67d6-323">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-323">Select **Yes**.</span></span>
1. <span data-ttu-id="d67d6-324">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="d67d6-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d67d6-325">Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d67d6-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="d67d6-326">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d67d6-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d67d6-327">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d67d6-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d67d6-328">Klicken Sie in Visual Studio für Mac-Versionen vor Version 8.6 auf der Randleiste auf **Datei** > **Neue Projektmappe** >  **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="d67d6-329">Klicken Sie in Version 8.6 oder höher auf der Randleiste auf **Datei** > **Neue Projektmappe** > **Web and Console** > **App** (App und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d67d6-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="d67d6-330">Wählen Sie die C#-Projektvorlage **ASP.NET Core-Web-API**aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-331">Wählen Sie aus der Dropdownliste **Zielframework** die Option **.NET Core 2.2** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="d67d6-332">Geben Sie als \**Projektnamen\*\*\*BooksApi* ein, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="d67d6-333">Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="d67d6-334">Geben Sie in das Suchfeld *MongoDB.Driver* ein, wählen Sie das Paket *MongoDB.Driver* aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="d67d6-335">Klicken Sie im Dialogfeld **Zustimmung zur Lizenz** auf die Schaltfläche **Annehmen**.</span><span class="sxs-lookup"><span data-stu-id="d67d6-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="d67d6-336">Hinzufügen eines Entitätsmodells</span><span class="sxs-lookup"><span data-stu-id="d67d6-336">Add an entity model</span></span>

1. <span data-ttu-id="d67d6-337">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="d67d6-338">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="d67d6-339">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,</span><span class="sxs-lookup"><span data-stu-id="d67d6-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="d67d6-340">um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="d67d6-341">Sie wird mit [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="d67d6-342">Sie wird mit [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, um die Übergabe des Parameters als Typ `string` anstelle einer [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="d67d6-343">Mongo behandelt die Konvertierung von `string` zu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="d67d6-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="d67d6-344">Die Eigenschaft `BookName` ist mit dem Attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="d67d6-345">Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="d67d6-346">Hinzufügen eines Konfigurationsmodells</span><span class="sxs-lookup"><span data-stu-id="d67d6-346">Add a configuration model</span></span>

1. <span data-ttu-id="d67d6-347">Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="d67d6-348">Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="d67d6-349">Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="d67d6-350">Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="d67d6-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="d67d6-351">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="d67d6-352">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-352">In the preceding code:</span></span>

   * <span data-ttu-id="d67d6-353">Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Abhängigkeitsinjektionscontainer (DI, Dependency Injection) registriert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="d67d6-354">D. h. die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts wird beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="d67d6-355">Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d67d6-356">Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="d67d6-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="d67d6-357">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="d67d6-358">Hinzufügen eines CRUD-Vorgangsdiensts</span><span class="sxs-lookup"><span data-stu-id="d67d6-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="d67d6-359">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="d67d6-360">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="d67d6-361">Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="d67d6-362">Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="d67d6-363">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="d67d6-364">Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="d67d6-365">Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="d67d6-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="d67d6-366">Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="d67d6-367">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="d67d6-368">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="d67d6-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz für das Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="d67d6-370">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="d67d6-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="d67d6-372">In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d67d6-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="d67d6-373">Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="d67d6-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="d67d6-374">Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="d67d6-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="d67d6-375">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="d67d6-376">`TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="d67d6-377">`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="d67d6-378">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="d67d6-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="d67d6-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="d67d6-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="d67d6-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="d67d6-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d67d6-383">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="d67d6-383">Add a controller</span></span>

<span data-ttu-id="d67d6-384">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d67d6-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="d67d6-385">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="d67d6-385">The preceding web API controller:</span></span>

* <span data-ttu-id="d67d6-386">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="d67d6-387">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="d67d6-388">Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d67d6-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="d67d6-389">Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="d67d6-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="d67d6-390">`CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="d67d6-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="d67d6-391">Der `Location`-Header gibt den URI des neu erstellten Buchs an.</span><span class="sxs-lookup"><span data-stu-id="d67d6-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="d67d6-392">Testen der Web-API</span><span class="sxs-lookup"><span data-stu-id="d67d6-392">Test the web API</span></span>

1. <span data-ttu-id="d67d6-393">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d67d6-393">Build and run the app.</span></span>

1. <span data-ttu-id="d67d6-394">Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="d67d6-395">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="d67d6-396">Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="d67d6-397">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d67d6-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="d67d6-398">Konfigurieren von JSON-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="d67d6-398">Configure JSON serialization options</span></span>

<span data-ttu-id="d67d6-399">Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="d67d6-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="d67d6-400">Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.</span><span class="sxs-lookup"><span data-stu-id="d67d6-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="d67d6-401">Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d67d6-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="d67d6-402">Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="d67d6-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="d67d6-403">Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddMvc`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d67d6-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="d67d6-404">Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein.</span><span class="sxs-lookup"><span data-stu-id="d67d6-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="d67d6-405">Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.</span><span class="sxs-lookup"><span data-stu-id="d67d6-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="d67d6-406">Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:</span><span class="sxs-lookup"><span data-stu-id="d67d6-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="d67d6-407">Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.</span><span class="sxs-lookup"><span data-stu-id="d67d6-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="d67d6-408">Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="d67d6-409">Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte.</span><span class="sxs-lookup"><span data-stu-id="d67d6-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="d67d6-410">Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="d67d6-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="d67d6-411">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="d67d6-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="d67d6-412">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d67d6-412">Next steps</span></span>

<span data-ttu-id="d67d6-413">Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="d67d6-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="d67d6-414">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="d67d6-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
