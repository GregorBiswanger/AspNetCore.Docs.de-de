---
title: Veröffentlichen einer ASP.NET Core-App in IIS
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App auf einem IIS-Server gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-iis
ms.openlocfilehash: 837a66ef36f1394df87d56132e146ef23a5d5659
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407888"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="eaa4a-103">Veröffentlichen einer ASP.NET Core-App in IIS</span><span class="sxs-lookup"><span data-stu-id="eaa4a-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="eaa4a-104">In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App auf einem IIS-Server gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="eaa4a-105">Dieses Tutorial behandelt die folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eaa4a-106">Installieren des Pakets „.NET Core Hosting“ unter Windows Server.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="eaa4a-107">Erstellen Sie einer IIS-Website in IIS-Manager.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="eaa4a-108">Bereitstellen einer ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eaa4a-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eaa4a-109">Prerequisites</span></span>

* <span data-ttu-id="eaa4a-110">[.Net Core SDK](/dotnet/core/sdk) auf dem Entwicklungs Computer.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="eaa4a-111">Windows Server, konfiguriert mit der Serverrolle **Webserver (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="eaa4a-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="eaa4a-112">Wenn Ihr Server nicht zum Hosten von Websites mit IIS konfiguriert ist, befolgen Sie die Anleitungen im Abschnitt *IIS-Konfiguration* des Artikels <xref:host-and-deploy/iis/index#iis-configuration>, und kehren Sie dann zu diesem Tutorial zurück.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="eaa4a-113">**Die IIS-Konfiguration und Websitesicherheit beinhalten Konzepte, die in diesem Tutorial nicht behandelt werden.**</span><span class="sxs-lookup"><span data-stu-id="eaa4a-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="eaa4a-114">Lesen Sie den Leitfaden für IIS in der [Microsoft IIS-Dokumentation](https://www.iis.net/) und den [ASP.NET Core-Artikel zum Hosting mit IIS](xref:host-and-deploy/iis/index), bevor Sie Produktions-Apps unter IIS hosten.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="eaa4a-115">Wichtige Szenarien für das IIS-Hosting, die in diesem Tutorial nicht behandelt werden, sind:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="eaa4a-116">Erstellen einer Registrierungsstruktur für den ASP.NET Core-Datenschutz</span><span class="sxs-lookup"><span data-stu-id="eaa4a-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="eaa4a-117">Konfiguration der Zugriffssteuerungsliste (Access Control List, ACL) des App-Pools</span><span class="sxs-lookup"><span data-stu-id="eaa4a-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="eaa4a-118">Um sich auf IIS-Bereitstellungskonzepte zu konzentrieren, stellt dieses Tutorial eine Anwendung ohne in IIS konfigurierte HTTPS-Sicherheit bereit.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="eaa4a-119">Weitere Informationen zum Hosting einer App, die für das HTTPS-Protokoll aktiviert ist, finden Sie in den Sicherheitsthemen im Abschnitt [Weitere Ressourcen](#additional-resources) in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="eaa4a-120">Weitere Anleitungen zum Hosting von ASP.NET Core-Apps finden Sie im Artikel <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="eaa4a-121">Installieren des .NET Core Hosting-Pakets</span><span class="sxs-lookup"><span data-stu-id="eaa4a-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="eaa4a-122">Installieren Sie das *Paket „.NET Core Hosting“* auf dem IIS-Server.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="eaa4a-123">Das Paket installiert die .NET Core-Runtime, die .NET Core-Bibliothek und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="eaa4a-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="eaa4a-124">Mit dem Modul können ASP.NET Core-Apps hinter IIS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="eaa4a-125">Laden Sie den Installer über folgenden Link herunter:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="eaa4a-126">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="eaa4a-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="eaa4a-127">Führen Sie das Installationsprogramm auf dem IIS-Server aus.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="eaa4a-128">Starten Sie den Server neu, oder führen Sie **net stop was /y**, gefolgt von **net start w3svc** in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="eaa4a-129">Erstellen der IIS-Website</span><span class="sxs-lookup"><span data-stu-id="eaa4a-129">Create the IIS site</span></span>

1. <span data-ttu-id="eaa4a-130">Erstellen Sie auf dem IIS-Server einen Ordner zum Speichern der veröffentlichten Ordner und Dateien der App.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="eaa4a-131">In einem späteren Schritt wird der Ordnerpfad als physischer App-Pfad in IIS angegeben.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="eaa4a-132">Öffnen Sie in IIS-Manager den Serverknoten im Bereich **Verbindungen**.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="eaa4a-133">Klicken Sie mit der rechten Maustaste auf den Ordner **Websites**.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="eaa4a-134">Klicken Sie im Kontextmenü auf **Website hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="eaa4a-135">Geben Sie einen **Websitenamen** an, und legen Sie den **physischen Pfad** auf den erstellten Bereitstellungsordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="eaa4a-136">Geben Sie die Konfiguration unter **Bindung** an, und erstellen Sie die Website, indem Sie auf **OK** klicken.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="eaa4a-137">Erstellen einer ASP.NET Core Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="eaa4a-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="eaa4a-138">Befolgen Sie das Tutorial <xref:getting-started> zum Erstellen einer Razor Pages-App.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="eaa4a-139">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="eaa4a-139">Publish and deploy the app</span></span>

<span data-ttu-id="eaa4a-140">*Veröffentlichen einer App* bedeutet, dass eine kompilierte App erstellt wird, die von einem Server gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="eaa4a-141">*Bereitstellen einer App* bedeutet, dass die veröffentlichte App auf ein Hostingsystem verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="eaa4a-142">Der Veröffentlichungsschritt wird vom [.NET Core SDK](/dotnet/core/sdk) ausgeführt, während der Bereitstellungsschritt mit einer Vielzahl von Ansätzen ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="eaa4a-143">In diesem Tutorial wird der Bereitstellungsansatz über einen *Ordner* verwendet. Dabei gilt:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="eaa4a-144">Die App wird in einem Ordner veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-144">The app is published to a folder.</span></span>
* <span data-ttu-id="eaa4a-145">Der Inhalt des Ordners wird in den Ordner der IIS-Website verschoben (der **physische Pfad** zur Website in IIS-Manager).</span><span class="sxs-lookup"><span data-stu-id="eaa4a-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4a-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4a-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eaa4a-147">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="eaa4a-148">Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Veröffentlichungsoptionen **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="eaa4a-149">Legen Sie den Pfad **des Ordners oder der Dateifreigabe** fest.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="eaa4a-150">Wenn Sie einen Ordner für die IIS-Website erstellt haben, der auf dem Entwicklungscomputer als Netzwerkfreigabe verfügbar ist, geben Sie den Pfad zur Freigabe an.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="eaa4a-151">Der aktuelle Benutzer muss über Schreibzugriff verfügen, um auf der Freigabe veröffentlichen zu können.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="eaa4a-152">Wenn Sie nicht in der Lage sind, die Bereitstellung direkt im Ordner der IIS-Website auf dem IIS-Server auszuführen, veröffentlichen Sie in einem Ordner auf entfernbaren Medien, und verschieben Sie die veröffentlichte App physisch in den Ordner der IIS-Website auf dem Server. Dies ist der **physische Pfad** der Website in IIS Manager.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="eaa4a-153">Verschieben Sie den Inhalt des Ordners *bin/Release/{ZIELFRAMEWORK}/publish* in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eaa4a-154">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa4a-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="eaa4a-155">Veröffentlichen Sie die App mit der Konfiguration „Release“ mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="eaa4a-156">Verschieben Sie den Inhalt des Ordners *bin/Release/{ZIELFRAMEWORK}/publish* in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eaa4a-157">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="eaa4a-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eaa4a-158">Klicken Sie in der **Projektmappe** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **Veröffentlichen** > **In Ordner veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="eaa4a-159">Legen Sie den Pfad unter **Pfad auswählen** fest.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="eaa4a-160">Wenn Sie einen Ordner für die IIS-Website erstellt haben, der auf dem Entwicklungscomputer als Netzwerkfreigabe verfügbar ist, geben Sie den Pfad zur Freigabe an.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="eaa4a-161">Der aktuelle Benutzer muss über Schreibzugriff verfügen, um auf der Freigabe veröffentlichen zu können.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="eaa4a-162">Wenn Sie nicht in der Lage sind, die Bereitstellung direkt im Ordner der IIS-Website auf dem IIS-Server auszuführen, veröffentlichen Sie in einem Ordner auf entfernbaren Medien, und verschieben Sie die veröffentlichte App physisch in den Ordner der IIS-Website auf dem Server. Dies ist der **physische Pfad** der Website in IIS Manager.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="eaa4a-163">Verschieben Sie den Inhalt des Ordners *bin/Release/{ZIELFRAMEWORK}/publish* in den Ordner der IIS-Website auf dem Server, der der **physische Pfad** der Website in IIS Manager ist.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="eaa4a-164">Navigieren auf der Website</span><span class="sxs-lookup"><span data-stu-id="eaa4a-164">Browse the website</span></span>

<span data-ttu-id="eaa4a-165">Auf die App kann in einem Browser zugegriffen werden, nachdem die erste Anforderung empfangen wurde.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="eaa4a-166">Nehmen Sie eine Anforderung an die App an der Endpunktbindung vor, die Sie in IIS-Manager für die Website eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eaa4a-167">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="eaa4a-167">Next steps</span></span>

<span data-ttu-id="eaa4a-168">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eaa4a-169">Installieren des Pakets „.NET Core Hosting“ unter Windows Server.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="eaa4a-170">Erstellen Sie einer IIS-Website in IIS-Manager.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="eaa4a-171">Bereitstellen einer ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="eaa4a-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="eaa4a-172">Weitere Informationen zum Hosting von ASP.NET Core-Apps unter IIS finden Sie im IIS-Übersichtsartikel:</span><span class="sxs-lookup"><span data-stu-id="eaa4a-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="eaa4a-173">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eaa4a-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="eaa4a-174">Artikel in der ASP.NET Core-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="eaa4a-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="eaa4a-175">Artikel zur Bereitstellung von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="eaa4a-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="eaa4a-176">Veröffentlichen einer Web-App in einem Ordner mithilfe von Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="eaa4a-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="eaa4a-177">Artikel zur IIS-HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa4a-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="eaa4a-178">Konfigurieren von SSL in IIS-Manager</span><span class="sxs-lookup"><span data-stu-id="eaa4a-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="eaa4a-179">Einrichten von SSL unter IIS</span><span class="sxs-lookup"><span data-stu-id="eaa4a-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="eaa4a-180">Artikel zu IIS und Windows Server</span><span class="sxs-lookup"><span data-stu-id="eaa4a-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="eaa4a-181">Die offizielle Microsoft IIS-Website</span><span class="sxs-lookup"><span data-stu-id="eaa4a-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="eaa4a-182">Bibliothek mit technischem Inhalt zu Windows Server</span><span class="sxs-lookup"><span data-stu-id="eaa4a-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
