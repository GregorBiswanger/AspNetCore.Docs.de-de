---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: Ein kurzes Tutorial, in dem eine einfache Hallo Welt-App mit ASP.NET Core erstellt und ausgeführt wird.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: afded8890afe3b8f7b1d0b5634fc7764906bc9d7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635007"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="8ee46-103">Tutorial: Erste Schritte mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ee46-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="8ee46-104">Dieses Tutorial zeigt, wie Sie eine ASP.NET Core-Web-App über die .NET Core-CLI erstellen und ausführen.</span><span class="sxs-lookup"><span data-stu-id="8ee46-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="8ee46-105">Sie lernen, die folgende Aufgaben auszuführen:</span><span class="sxs-lookup"><span data-stu-id="8ee46-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ee46-106">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-106">Create a web app project.</span></span>
> * <span data-ttu-id="8ee46-107">Vertrauen Sie dem Entwicklungszertifikat.</span><span class="sxs-lookup"><span data-stu-id="8ee46-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="8ee46-108">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="8ee46-108">Run the app.</span></span>
> * <span data-ttu-id="8ee46-109">Bearbeiten Sie eine Razor-Seite.</span><span class="sxs-lookup"><span data-stu-id="8ee46-109">Edit a Razor page.</span></span>

<span data-ttu-id="8ee46-110">Am Schluss werden Sie eine funktionierende Web-App auf Ihrem lokalen Computer besitzen.</span><span class="sxs-lookup"><span data-stu-id="8ee46-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web-App-Startseite](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="8ee46-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8ee46-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="8ee46-113">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-113">Create a web app project</span></span>

<span data-ttu-id="8ee46-114">Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="8ee46-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="8ee46-115">Für den obigen Befehl gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8ee46-115">The preceding command:</span></span>

* <span data-ttu-id="8ee46-116">Erstellt eine neue Web-App.</span><span class="sxs-lookup"><span data-stu-id="8ee46-116">Creates a new web app.</span></span>  
* <span data-ttu-id="8ee46-117">Mit dem `-o aspnetcoreapp`-Parameter wird ein Verzeichnis mit dem Namen *aspnetcoreapp* und den Quelldateien für die App erstellt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="8ee46-118">Dem Entwicklungszertifikat vertrauen</span><span class="sxs-lookup"><span data-stu-id="8ee46-118">Trust the development certificate</span></span>

<span data-ttu-id="8ee46-119">Vertrauen Sie dem HTTPS-Entwicklungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="8ee46-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="8ee46-120">Windows</span><span class="sxs-lookup"><span data-stu-id="8ee46-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="8ee46-121">Über den vorherigen Befehl wird der folgende Dialog angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8ee46-121">The preceding command displays the following dialog:</span></span>

![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

<span data-ttu-id="8ee46-123">Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="8ee46-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8ee46-124">macOS</span><span class="sxs-lookup"><span data-stu-id="8ee46-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="8ee46-125">Über den vorherigen Befehl wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8ee46-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="8ee46-126">*Trusting the HTTPS development certificate was requested. Wenn das Zertifikat nicht bereits vertrauenswürdig ist, führen wir den folgenden Befehl aus:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="8ee46-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="8ee46-127">Dieser Befehl fordert Sie möglicherweise zur Eingabe Ihres Kennworts auf, um das Zertifikat in der Keychain für das System zu installieren.</span><span class="sxs-lookup"><span data-stu-id="8ee46-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="8ee46-128">Geben Sie Ihr Kennwort ein, wenn Sie die Vertrauenswürdigkeit des Entwicklungszertifikats bestätigen möchten.</span><span class="sxs-lookup"><span data-stu-id="8ee46-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="8ee46-129">Linux</span><span class="sxs-lookup"><span data-stu-id="8ee46-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="8ee46-130">Weitere Informationen zum Bestätigen der Vertrauenswürdigkeit eines HTTPS-Entwicklungszertifikats finden Sie in der Dokumentation zu Ihrer Linux-Distribution.</span><span class="sxs-lookup"><span data-stu-id="8ee46-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="8ee46-131">Weitere Informationen finden Sie unter [Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikat als vertrauenswürdig](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="8ee46-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="8ee46-132">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="8ee46-132">Run the app</span></span>

<span data-ttu-id="8ee46-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="8ee46-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="8ee46-134">Nachdem die Befehlsshell angibt, dass die App gestartet wurde, müssen Sie zu `https://localhost:5001` wechseln.</span><span class="sxs-lookup"><span data-stu-id="8ee46-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-no-locrazor-page"></a><span data-ttu-id="8ee46-135">Bearbeiten einer Razor-Seite</span><span class="sxs-lookup"><span data-stu-id="8ee46-135">Edit a Razor page</span></span>

<span data-ttu-id="8ee46-136">Öffnen Sie *Pages/Index.cshtml*, und ändern und speichern Sie die Seite mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="8ee46-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="8ee46-137">Navigieren Sie zu `https://localhost:5001`, aktualisieren Sie die Seite, und überprüfen Sie, ob die Änderungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="8ee46-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ee46-138">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="8ee46-138">Next steps</span></span>

<span data-ttu-id="8ee46-139">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8ee46-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ee46-140">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-140">Create a web app project.</span></span>
> * <span data-ttu-id="8ee46-141">Vertrauen Sie dem Entwicklungszertifikat.</span><span class="sxs-lookup"><span data-stu-id="8ee46-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="8ee46-142">Führen Sie das Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="8ee46-142">Run the project.</span></span>
> * <span data-ttu-id="8ee46-143">Führen Sie eine Änderung durch.</span><span class="sxs-lookup"><span data-stu-id="8ee46-143">Make a change.</span></span>

<span data-ttu-id="8ee46-144">Um mehr über ASP.NET Core zu lernen, machen Sie sich mit dem empfohlenen Lernpfad in der Einführung vertraut:</span><span class="sxs-lookup"><span data-stu-id="8ee46-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
