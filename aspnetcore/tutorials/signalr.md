---
title: Erste Schritte mit ASP.NET Core SignalR
author: bradygaster
description: In diesem Tutorial erstellen Sie eine Chat-App, die ASP.NET Core SignalR verwendet.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: 9a4296550a17ac2c348f2406e9f5b39877b02b59
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555931"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="1d46c-103">Tutorial: Erste Schritte mit ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1d46c-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="1d46c-104">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1d46c-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="1d46c-105">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1d46c-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1d46c-106">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-106">Create a web project.</span></span>
> * <span data-ttu-id="1d46c-107">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="1d46c-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="1d46c-108">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="1d46c-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="1d46c-109">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="1d46c-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="1d46c-110">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d46c-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="1d46c-111">Am Ende verfügen Sie über eine funktionierende Chat-App:</span><span class="sxs-lookup"><span data-stu-id="1d46c-111">At the end, you'll have a working chat app:</span></span>

![SignalR-Beispiel-App](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="1d46c-113">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1d46c-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1d46c-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="1d46c-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1d46c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d46c-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1d46c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d46c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1d46c-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1d46c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="1d46c-118">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="1d46c-118">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1d46c-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d46c-119">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1d46c-120">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-120">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="1d46c-121">Wählen Sie im Dialogfeld **Neues Projekt** **Installiert > Visual C# > Web > ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-121">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1d46c-122">Geben Sie als Projektname *SignalRChat* ein.</span><span class="sxs-lookup"><span data-stu-id="1d46c-122">Name the project *SignalRChat*.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="1d46c-124">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d46c-124">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="1d46c-125">Wählen Sie **.NET Core** als Zielframework sowie **ASP.NET Core 2.2** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-125">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1d46c-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d46c-127">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1d46c-128">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1d46c-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="1d46c-129">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1d46c-129">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1d46c-130">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1d46c-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d46c-131">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-131">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="1d46c-132">Klicken Sie auf **.NET Core > App > ASP.NET Core-Web-App** (Wählen Sie nicht **ASP.NET Core-Web-App (MVC)** aus).</span><span class="sxs-lookup"><span data-stu-id="1d46c-132">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="1d46c-133">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-133">Select **Next**.</span></span>

* <span data-ttu-id="1d46c-134">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-134">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="1d46c-135">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="1d46c-135">Add the SignalR client library</span></span>

<span data-ttu-id="1d46c-136">Die SignalR-Serverbibliothek ist im Metapaket `Microsoft.AspNetCore.App` enthalten.</span><span class="sxs-lookup"><span data-stu-id="1d46c-136">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="1d46c-137">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="1d46c-137">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="1d46c-138">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d46c-138">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="1d46c-139">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="1d46c-139">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1d46c-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d46c-140">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="1d46c-141">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-141">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="1d46c-142">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-142">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="1d46c-143">Geben Sie `@aspnet/signalr@1` für die **Bibliothek** ein, und wählen Sie die neueste Version aus, die keine Vorschauversion ist.</span><span class="sxs-lookup"><span data-stu-id="1d46c-143">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/libman1.png)

* <span data-ttu-id="1d46c-145">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-145">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="1d46c-146">Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-146">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Dateien und des Zielspeicherorts](signalr/_static/libman2.png)

  <span data-ttu-id="1d46c-148">Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="1d46c-148">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1d46c-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d46c-149">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="1d46c-150">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="1d46c-150">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1d46c-151">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d46c-151">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="1d46c-152">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1d46c-152">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1d46c-153">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="1d46c-153">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1d46c-154">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="1d46c-154">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1d46c-155">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="1d46c-155">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="1d46c-156">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="1d46c-156">Copy only the specified files.</span></span>

  <span data-ttu-id="1d46c-157">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="1d46c-157">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1d46c-158">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1d46c-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d46c-159">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="1d46c-159">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="1d46c-160">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="1d46c-160">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="1d46c-161">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d46c-161">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="1d46c-162">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="1d46c-162">The parameters specify the following options:</span></span>
  * <span data-ttu-id="1d46c-163">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="1d46c-163">Use the unpkg provider.</span></span>
  * <span data-ttu-id="1d46c-164">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="1d46c-164">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="1d46c-165">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="1d46c-165">Copy only the specified files.</span></span>

  <span data-ttu-id="1d46c-166">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="1d46c-166">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="1d46c-167">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="1d46c-167">Create a SignalR hub</span></span>

<span data-ttu-id="1d46c-168">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="1d46c-168">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="1d46c-169">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="1d46c-169">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="1d46c-170">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="1d46c-170">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="1d46c-171">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="1d46c-171">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="1d46c-172">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="1d46c-172">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="1d46c-173">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="1d46c-173">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="1d46c-174">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-174">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="1d46c-175">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d46c-175">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="1d46c-176">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="1d46c-176">Configure SignalR</span></span>

<span data-ttu-id="1d46c-177">Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.</span><span class="sxs-lookup"><span data-stu-id="1d46c-177">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="1d46c-178">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="1d46c-178">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="1d46c-179">Durch diese Änderungen wird SignalR zum Dependency Injection-System von ASP.NET Core sowie der Middlewarepipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-179">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="1d46c-180">Hinzufügen des SignalR-Clientcodes</span><span class="sxs-lookup"><span data-stu-id="1d46c-180">Add SignalR client code</span></span>

* <span data-ttu-id="1d46c-181">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1d46c-181">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="1d46c-182">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="1d46c-182">The preceding code:</span></span>

  * <span data-ttu-id="1d46c-183">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="1d46c-183">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="1d46c-184">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="1d46c-184">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="1d46c-185">schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein</span><span class="sxs-lookup"><span data-stu-id="1d46c-185">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="1d46c-186">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="1d46c-186">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="1d46c-187">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="1d46c-187">The preceding code:</span></span>

  * <span data-ttu-id="1d46c-188">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="1d46c-188">Creates and starts a connection.</span></span>
  * <span data-ttu-id="1d46c-189">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="1d46c-189">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="1d46c-190">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="1d46c-190">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="1d46c-191">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="1d46c-191">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1d46c-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d46c-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1d46c-193">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="1d46c-193">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1d46c-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d46c-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1d46c-195">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1d46c-195">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1d46c-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1d46c-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1d46c-197">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="1d46c-197">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="1d46c-198">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="1d46c-198">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="1d46c-199">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="1d46c-199">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="1d46c-200">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-200">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR-Beispiel-App](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="1d46c-202">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="1d46c-202">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="1d46c-203">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-203">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="1d46c-204">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="1d46c-204">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="1d46c-205">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-205">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="1d46c-206">![Fehler „signalr.js nicht gefunden“](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="1d46c-206">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="1d46c-207">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="1d46c-207">Next steps</span></span>

<span data-ttu-id="1d46c-208">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1d46c-208">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1d46c-209">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="1d46c-209">Create a web app project.</span></span>
> * <span data-ttu-id="1d46c-210">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="1d46c-210">Add the SignalR client library.</span></span>
> * <span data-ttu-id="1d46c-211">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="1d46c-211">Create a SignalR hub.</span></span>
> * <span data-ttu-id="1d46c-212">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="1d46c-212">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="1d46c-213">Hinzufügen von Code, der den Hub verwendet, um Nachrichten von einem beliebigen Client an alle verbundenen Clients zu senden</span><span class="sxs-lookup"><span data-stu-id="1d46c-213">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="1d46c-214">Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:</span><span class="sxs-lookup"><span data-stu-id="1d46c-214">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1d46c-215">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1d46c-215">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
