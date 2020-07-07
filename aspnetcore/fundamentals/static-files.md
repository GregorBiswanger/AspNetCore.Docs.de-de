---
title: Statische Dateien in ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie, wie statische Dateien bereitgestellt und gesichert werden und wie das Verhalten von Middleware beim Hosting statischer Dateien in einer ASP.NET Core-Web-App konfiguriert wird.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 39c5c5d4875e1d59abaa6d998a09dbffd723214d
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793416"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="07e00-103">Statische Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07e00-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07e00-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="07e00-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="07e00-105">Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App standardmäßig direkt bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="07e00-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07e00-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples/3x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="07e00-107">Bereitstellen statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="07e00-107">Serve static files</span></span>

<span data-ttu-id="07e00-108">Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="07e00-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="07e00-109">Das Standardverzeichnis heißt `{content root}/wwwroot`, kann jedoch mit der Methode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) geändert werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-109">The default directory is `{content root}/wwwroot`, but it can be changed with the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="07e00-110">Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="07e00-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="07e00-111">Die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:</span><span class="sxs-lookup"><span data-stu-id="07e00-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Program2.cs?name=snippet_Main)]

<span data-ttu-id="07e00-112">Der vorangehende Code wurde mit der Web-App-Vorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="07e00-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="07e00-113">Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="07e00-114">Die **Webanwendung**-Projektvorlagen verfügen beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="07e00-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="07e00-115">Erstellen Sie ggf. den *wwwroot/images*-Ordner, und fügen Sie die Datei *wwwroot/images/MyImage. pg* hinzu.</span><span class="sxs-lookup"><span data-stu-id="07e00-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="07e00-116">Das URI-Format für den Zugriff auf eine Datei im `images`-Ordner ist `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="07e00-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="07e00-117">Beispiel: `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="07e00-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="07e00-118">Verarbeiten von Dateien im Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="07e00-118">Serve files in web root</span></span>

<span data-ttu-id="07e00-119">Mit den Standard-Webappvorlagen wird die <xref:Owin.StaticFileExtensions.UseStaticFiles%2A>-Methode in `Startup.Configure` aufgerufen, die die Verarbeitung statischer Dateien ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="07e00-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/Startup.cs?name=snippet&highlight=14)]

<span data-ttu-id="07e00-120">Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar.</span><span class="sxs-lookup"><span data-stu-id="07e00-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="07e00-121">Folgendes Markup verweist auf *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="07e00-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg"  class="img" alt="My image." />
```

<span data-ttu-id="07e00-122">Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="07e00-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="07e00-123">Bereitstellen von Dateien außerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="07e00-123">Serve files outside of web root</span></span>

<span data-ttu-id="07e00-124">Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:</span><span class="sxs-lookup"><span data-stu-id="07e00-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="07e00-125">Über eine Anforderung kann auf die Datei `red-rose.jpg` zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="07e00-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupRose.cs?name=snippet&highlight=14-21)]

<span data-ttu-id="07e00-126">Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="07e00-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="07e00-127">Eine Anforderung an `https://<hostname>/StaticFiles/images/red-rose.jpg` dient der *red-rose.jpg*-Datei.</span><span class="sxs-lookup"><span data-stu-id="07e00-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="07e00-128">Folgendes Markup verweist auf *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="07e00-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose." />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="07e00-129">Festlegen von HTTP-Antwortheadern</span><span class="sxs-lookup"><span data-stu-id="07e00-129">Set HTTP response headers</span></span>

<span data-ttu-id="07e00-130">Mit einem <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>-Objekt können HTTP-Antwortheader festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="07e00-131">Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="07e00-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupAddHeader.cs?name=snippet&highlight=14-23)]

<span data-ttu-id="07e00-132">Statische Dateien können 600 Sekunden lang öffentlich zwischengespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="07e00-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="07e00-134">Autorisierung statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="07e00-134">Static file authorization</span></span>

<span data-ttu-id="07e00-135">Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen.</span><span class="sxs-lookup"><span data-stu-id="07e00-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="07e00-136">Alle über die Middleware bereitgestellten Dateien, Dateien unter `wwwroot` inbegriffen, sind öffentlich zugänglich.</span><span class="sxs-lookup"><span data-stu-id="07e00-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="07e00-137">So stellen Sie Dateien basierend auf der Autorisierung bereit:</span><span class="sxs-lookup"><span data-stu-id="07e00-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="07e00-138">Speichern Sie sie außerhalb von `wwwroot` sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="07e00-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="07e00-139">Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt, und geben Sie ein [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)-Objekt zurück:</span><span class="sxs-lookup"><span data-stu-id="07e00-139">Serve them via an action method to which authorization is applied and return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="directory-browsing"></a><span data-ttu-id="07e00-140">Verzeichnissuche</span><span class="sxs-lookup"><span data-stu-id="07e00-140">Directory browsing</span></span>

<span data-ttu-id="07e00-141">Die Verzeichnissuche ermöglicht die Verzeichnisauflistung in angegebenen Verzeichnissen.</span><span class="sxs-lookup"><span data-stu-id="07e00-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="07e00-142">Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="07e00-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="07e00-143">Weitere Informationen finden Sie unter [Überlegungen](#sc).</span><span class="sxs-lookup"><span data-stu-id="07e00-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="07e00-144">Aktivieren Sie die Verzeichnissuche mit:</span><span class="sxs-lookup"><span data-stu-id="07e00-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="07e00-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="07e00-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="07e00-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="07e00-146">[UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupBrowse.cs?name=snippet&highlight=4,20-34)]

<span data-ttu-id="07e00-147">Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL `https://<hostname>/MyImages` mit Links zu den einzelnen Dateien und Ordnern:</span><span class="sxs-lookup"><span data-stu-id="07e00-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![Verzeichnissuche](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="07e00-149">Bereitstellen von Standarddokumenten</span><span class="sxs-lookup"><span data-stu-id="07e00-149">Serve default documents</span></span>

<span data-ttu-id="07e00-150">Durch das Festlegen einer Standardseite wird Besuchern ein Ausgangspunkt auf einer Website bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="07e00-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="07e00-151">Um eine Standardseite von `wwwroot` ohne voll qualifizierten URI bereitzustellen, rufen Sie die <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="07e00-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty.cs?name=snippet&highlight=14)]

<span data-ttu-id="07e00-152">`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="07e00-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="07e00-153">`UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="07e00-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="07e00-154">Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner in `wwwroot` folgende Dateien durchsucht:</span><span class="sxs-lookup"><span data-stu-id="07e00-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="07e00-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="07e00-155">*default.htm*</span></span>
* <span data-ttu-id="07e00-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-156">*default.html*</span></span>
* <span data-ttu-id="07e00-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="07e00-157">*index.htm*</span></span>
* <span data-ttu-id="07e00-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-158">*index.html*</span></span>

<span data-ttu-id="07e00-159">Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI.</span><span class="sxs-lookup"><span data-stu-id="07e00-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="07e00-160">Die Browser-URL spiegelt weiterhin den angeforderten URI wider.</span><span class="sxs-lookup"><span data-stu-id="07e00-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="07e00-161">Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:</span><span class="sxs-lookup"><span data-stu-id="07e00-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet2)]

<span data-ttu-id="07e00-162">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="07e00-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupDefault.cs?name=snippet)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="07e00-163">UseFileServer für Standarddokumente</span><span class="sxs-lookup"><span data-stu-id="07e00-163">UseFileServer for default documents</span></span>

<span data-ttu-id="07e00-164">In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.</span><span class="sxs-lookup"><span data-stu-id="07e00-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="07e00-165">Rufen Sie `app.UseFileServer` auf, um die Bereitstellung statischer Dateien und der Standarddatei zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="07e00-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="07e00-166">Die Verzeichnissuche ist nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="07e00-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="07e00-167">Der folgende Code zeigt `Startup.Configure` mit `UseFileServer` an:</span><span class="sxs-lookup"><span data-stu-id="07e00-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty2.cs?name=snippet&highlight=14)]

<span data-ttu-id="07e00-168">Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche aktiviert:</span><span class="sxs-lookup"><span data-stu-id="07e00-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="07e00-169">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="07e00-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupEmpty3.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="07e00-170">Betrachten Sie die folgende Verzeichnishierarchie:</span><span class="sxs-lookup"><span data-stu-id="07e00-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="07e00-171">Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche von `MyStaticFiles` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="07e00-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupUseFileServer.cs?name=snippet&highlight=4,20-30)]

<span data-ttu-id="07e00-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet.</span><span class="sxs-lookup"><span data-stu-id="07e00-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="07e00-173">Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="07e00-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="07e00-174">URI</span><span class="sxs-lookup"><span data-stu-id="07e00-174">URI</span></span>            |      <span data-ttu-id="07e00-175">Antwort</span><span class="sxs-lookup"><span data-stu-id="07e00-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="07e00-176">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="07e00-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="07e00-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="07e00-178">Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt `https://<hostname>/StaticFiles` die Verzeichnisliste mit klickbaren Links zurück:</span><span class="sxs-lookup"><span data-stu-id="07e00-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Liste der statischen Dateien](static-files/_static/db2.png)

<span data-ttu-id="07e00-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung vom Ziel-URI ohne nachstehenden `/` zum Ziel-URI mit nachstehendem `/` durch.</span><span class="sxs-lookup"><span data-stu-id="07e00-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="07e00-181">Beispielsweise von `https://<hostname>/StaticFiles` zu `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="07e00-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="07e00-182">Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich (`/`) als ungültig.</span><span class="sxs-lookup"><span data-stu-id="07e00-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="07e00-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="07e00-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="07e00-184">Die Klasse [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient.</span><span class="sxs-lookup"><span data-stu-id="07e00-184">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="07e00-185">Im folgenden Beispiel werden mehrere Dateierweiterungen bekannten MIME-Typen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="07e00-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="07e00-186">Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt:</span><span class="sxs-lookup"><span data-stu-id="07e00-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet2)]

<span data-ttu-id="07e00-187">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="07e00-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupFileExtensionContentTypeProvider.cs?name=snippet&highlight=14-42)]

<span data-ttu-id="07e00-188">Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="07e00-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="07e00-189">Inhaltstypen, die vom Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="07e00-189">Non-standard content types</span></span>

<span data-ttu-id="07e00-190">Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="07e00-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="07e00-191">Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="07e00-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="07e00-192">Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="07e00-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="07e00-193">Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="07e00-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="07e00-194">Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:</span><span class="sxs-lookup"><span data-stu-id="07e00-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet2)]

<span data-ttu-id="07e00-195">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="07e00-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3x/sample/StartupServeUnknownFileTypes.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="07e00-196">Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="07e00-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-197">Die Aktivierung von [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="07e00-197">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="07e00-198">Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="07e00-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="07e00-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.</span><span class="sxs-lookup"><span data-stu-id="07e00-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="07e00-200">Bereitstellen von Dateien aus mehreren Speicherorten</span><span class="sxs-lookup"><span data-stu-id="07e00-200">Serve files from multiple locations</span></span>

<span data-ttu-id="07e00-201">Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="07e00-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="07e00-202">Weitere Instanzen von `UseStaticFiles` und `UseFileServer` können mit anderen Dateianbietern bereitgestellt werden, um Dateien von anderen Speicherorten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="07e00-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="07e00-203">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="07e00-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="07e00-204">Sicherheitsüberlegungen für statische Dateien</span><span class="sxs-lookup"><span data-stu-id="07e00-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-205">`UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken.</span><span class="sxs-lookup"><span data-stu-id="07e00-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="07e00-206">Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="07e00-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="07e00-207">Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="07e00-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="07e00-208">Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht.</span><span class="sxs-lookup"><span data-stu-id="07e00-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="07e00-209">Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="07e00-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="07e00-210">Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages, Konfigurationsdateien usw.</span><span class="sxs-lookup"><span data-stu-id="07e00-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="07e00-211">Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems.</span><span class="sxs-lookup"><span data-stu-id="07e00-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="07e00-212">Bei Windows wird die Groß-/Kleinschreibung beispielsweise nicht beachtet, jedoch bei macOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="07e00-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="07e00-213">In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="07e00-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="07e00-214">Der IIS-Handler für statische Dateien wird nicht verwendet und hat keine Möglichkeit, Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="07e00-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="07e00-215">Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="07e00-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="07e00-216">Navigieren Sie zum Feature **Module**.</span><span class="sxs-lookup"><span data-stu-id="07e00-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="07e00-217">Wählen Sie **StaticFileModule** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="07e00-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="07e00-218">Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.</span><span class="sxs-lookup"><span data-stu-id="07e00-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-219">Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="07e00-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="07e00-220">Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.</span><span class="sxs-lookup"><span data-stu-id="07e00-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="07e00-221">Platzieren Sie Codedateien einschließlich *.cs* und *.cshtml* außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts.</span><span class="sxs-lookup"><span data-stu-id="07e00-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="07e00-222">Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen.</span><span class="sxs-lookup"><span data-stu-id="07e00-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="07e00-223">Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.</span><span class="sxs-lookup"><span data-stu-id="07e00-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e00-224">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="07e00-224">Additional resources</span></span>

* [<span data-ttu-id="07e00-225">Middleware</span><span class="sxs-lookup"><span data-stu-id="07e00-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="07e00-226">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07e00-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07e00-227">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="07e00-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="07e00-228">Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App direkt bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="07e00-229">Damit diese Dateien bereitgestellt werden können, sind einige Konfigurationsschritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="07e00-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="07e00-230">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07e00-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="07e00-231">Bereitstellen statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="07e00-231">Serve static files</span></span>

<span data-ttu-id="07e00-232">Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="07e00-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="07e00-233">Das Standardverzeichnis heißt *{Inhaltsstammverzeichnis}/wwwroot*, das jedoch mit der Methode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="07e00-233">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="07e00-234">Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="07e00-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="07e00-235">Der Web-Host der App muss über das Inhaltsstammverzeichnis informiert werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="07e00-236">Die Methode `WebHost.CreateDefaultBuilder` legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:</span><span class="sxs-lookup"><span data-stu-id="07e00-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="07e00-237">Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="07e00-238">Die Projektvorlage der **Webanwendung** verfügt beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="07e00-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="07e00-239">Das URI-Format für den Zugriff auf eine Datei im Unterordner *images* lautet *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="07e00-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="07e00-240">Beispiel: *http://localhost:9189/images/banner3.svg*</span><span class="sxs-lookup"><span data-stu-id="07e00-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="07e00-241">Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="07e00-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="07e00-242">Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="07e00-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="07e00-243">Konfigurieren Sie die [Middleware](xref:fundamentals/middleware/index), über die Sie statische Dateien bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="07e00-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="07e00-244">Bereitstellen von Dateien innerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="07e00-244">Serve files inside of web root</span></span>

<span data-ttu-id="07e00-245">Rufen Sie die Methode [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="07e00-245">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="07e00-246">Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar.</span><span class="sxs-lookup"><span data-stu-id="07e00-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="07e00-247">Folgendes Markup verweist auf *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="07e00-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="07e00-248">Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="07e00-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="07e00-249">Bereitstellen von Dateien außerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="07e00-249">Serve files outside of web root</span></span>

<span data-ttu-id="07e00-250">Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:</span><span class="sxs-lookup"><span data-stu-id="07e00-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="07e00-251">Über eine Anforderung kann auf die Datei *banner1.svg* zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="07e00-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="07e00-252">Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="07e00-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="07e00-253">Über eine Anforderung an *http://\<server_address>/StaticFiles/images/banner1.svg* wird die Datei *banner1.svg* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="07e00-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="07e00-254">Folgendes Markup verweist auf *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="07e00-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="07e00-255">Festlegen von HTTP-Antwortheadern</span><span class="sxs-lookup"><span data-stu-id="07e00-255">Set HTTP response headers</span></span>

<span data-ttu-id="07e00-256">Mit einem [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions)-Objekt können HTTP-Antwortheader festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="07e00-256">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="07e00-257">Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="07e00-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="07e00-258">Die Methode [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) ist im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="07e00-258">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="07e00-259">Die Dateien wurden 10 Minuten lang (600 Sekunden) in der Entwicklungsumgebung öffentlich zwischenspeicherbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="07e00-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="07e00-261">Autorisierung statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="07e00-261">Static file authorization</span></span>

<span data-ttu-id="07e00-262">Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen.</span><span class="sxs-lookup"><span data-stu-id="07e00-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="07e00-263">Alle über die Middleware bereitgestellten Dateien, Dateien unter *wwwroot* inbegriffen, sind öffentlich zugänglich.</span><span class="sxs-lookup"><span data-stu-id="07e00-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="07e00-264">So stellen Sie Dateien basierend auf der Autorisierung bereit:</span><span class="sxs-lookup"><span data-stu-id="07e00-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="07e00-265">Speichern Sie sie außerhalb von *wwwroot* sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="07e00-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="07e00-266">Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt.</span><span class="sxs-lookup"><span data-stu-id="07e00-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="07e00-267">Geben Sie ein [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)-Objekt zurück:</span><span class="sxs-lookup"><span data-stu-id="07e00-267">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="07e00-268">Aktivieren der Verzeichnissuche</span><span class="sxs-lookup"><span data-stu-id="07e00-268">Enable directory browsing</span></span>

<span data-ttu-id="07e00-269">Über die Verzeichnissuche können Benutzer Ihrer Web-App eine Verzeichnisliste und Dateien innerhalb eines angegebenen Verzeichnisses anzeigen.</span><span class="sxs-lookup"><span data-stu-id="07e00-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="07e00-270">Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#sc)).</span><span class="sxs-lookup"><span data-stu-id="07e00-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="07e00-271">Aktivieren Sie die Verzeichnissuche, indem Sie die Methode [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="07e00-271">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="07e00-272">Fügen Sie erforderliche Dienste hinzu, indem Sie die Methode <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> von `Startup.ConfigureServices` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="07e00-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="07e00-273">Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* mit Links zu den einzelnen Dateien und Ordnern:</span><span class="sxs-lookup"><span data-stu-id="07e00-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![Verzeichnissuche](static-files/_static/dir-browse.png)

<span data-ttu-id="07e00-275">Weitere Informationen zu den Sicherheitsrisiken bei der Aktivierung der Suche finden Sie unter [Überlegungen](#considerations).</span><span class="sxs-lookup"><span data-stu-id="07e00-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="07e00-276">Beachten Sie die beiden `UseStaticFiles`-Aufrufe im folgenden Beispiel.</span><span class="sxs-lookup"><span data-stu-id="07e00-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="07e00-277">Durch den ersten Aufruf wird die Bereitstellung statischer Dateien im Ordner *wwwroot* aktiviert.</span><span class="sxs-lookup"><span data-stu-id="07e00-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="07e00-278">Durch den zweiten Aufruf wird die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="07e00-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="07e00-279">Bereitstellen eines Standarddokuments</span><span class="sxs-lookup"><span data-stu-id="07e00-279">Serve a default document</span></span>

<span data-ttu-id="07e00-280">Durch das Festlegen einer Standardstartseite wird Besuchern Ihrer Website ein logischer Ausgangspunkt geboten.</span><span class="sxs-lookup"><span data-stu-id="07e00-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="07e00-281">Rufen Sie die Methode [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) über `Startup.Configure` auf, um eine Standardseite bereitzustellen, ohne dass der Benutzer den URI vollständig qualifizieren muss:</span><span class="sxs-lookup"><span data-stu-id="07e00-281">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="07e00-282">`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="07e00-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="07e00-283">`UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="07e00-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="07e00-284">Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles`, um die Datei bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="07e00-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="07e00-285">Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner folgende Dateien durchsucht:</span><span class="sxs-lookup"><span data-stu-id="07e00-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="07e00-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="07e00-286">*default.htm*</span></span>
* <span data-ttu-id="07e00-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-287">*default.html*</span></span>
* <span data-ttu-id="07e00-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="07e00-288">*index.htm*</span></span>
* <span data-ttu-id="07e00-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-289">*index.html*</span></span>

<span data-ttu-id="07e00-290">Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI.</span><span class="sxs-lookup"><span data-stu-id="07e00-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="07e00-291">Die Browser-URL spiegelt weiterhin den angeforderten URI wider.</span><span class="sxs-lookup"><span data-stu-id="07e00-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="07e00-292">Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:</span><span class="sxs-lookup"><span data-stu-id="07e00-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="07e00-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="07e00-293">UseFileServer</span></span>

<span data-ttu-id="07e00-294">In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.</span><span class="sxs-lookup"><span data-stu-id="07e00-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="07e00-295">Mit dem folgenden Code wird die Bereitstellung statischer Dateien und der Standarddatei aktiviert.</span><span class="sxs-lookup"><span data-stu-id="07e00-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="07e00-296">Die Verzeichnissuche ist nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="07e00-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="07e00-297">Der folgende Code baut auf der parameterlosen Überladung auf, indem die Verzeichnissuche aktiviert wird:</span><span class="sxs-lookup"><span data-stu-id="07e00-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="07e00-298">Betrachten Sie die folgende Verzeichnishierarchie:</span><span class="sxs-lookup"><span data-stu-id="07e00-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="07e00-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="07e00-299">**wwwroot**</span></span>
  * <span data-ttu-id="07e00-300">**css**</span><span class="sxs-lookup"><span data-stu-id="07e00-300">**css**</span></span>
  * <span data-ttu-id="07e00-301">**images**</span><span class="sxs-lookup"><span data-stu-id="07e00-301">**images**</span></span>
  * <span data-ttu-id="07e00-302">**js**</span><span class="sxs-lookup"><span data-stu-id="07e00-302">**js**</span></span>
* <span data-ttu-id="07e00-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="07e00-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="07e00-304">**images**</span><span class="sxs-lookup"><span data-stu-id="07e00-304">**images**</span></span>
    * <span data-ttu-id="07e00-305">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="07e00-305">*banner1.svg*</span></span>
  * <span data-ttu-id="07e00-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="07e00-306">*default.html*</span></span>

<span data-ttu-id="07e00-307">Mit dem folgenden Code werden statische Dateien, Standarddateien und die Verzeichnissuche von `MyStaticFiles` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="07e00-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="07e00-308">`AddDirectoryBrowser` muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet:</span><span class="sxs-lookup"><span data-stu-id="07e00-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="07e00-309">Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="07e00-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="07e00-310">URI</span><span class="sxs-lookup"><span data-stu-id="07e00-310">URI</span></span>            |                             <span data-ttu-id="07e00-311">Antwort</span><span class="sxs-lookup"><span data-stu-id="07e00-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="07e00-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="07e00-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="07e00-313">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="07e00-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="07e00-314">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="07e00-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="07e00-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="07e00-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="07e00-316">Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt *http://\<server_address>/StaticFiles* die Verzeichnisliste mit klickbaren Links zurück:</span><span class="sxs-lookup"><span data-stu-id="07e00-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="07e00-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung von `http://{SERVER ADDRESS}/StaticFiles` (ohne nachstehenden Schrägstrich) zu `http://{SERVER ADDRESS}/StaticFiles/` (mit nachstehendem Schrägstrich) durch.</span><span class="sxs-lookup"><span data-stu-id="07e00-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="07e00-319">Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich als ungültig.</span><span class="sxs-lookup"><span data-stu-id="07e00-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="07e00-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="07e00-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="07e00-321">Die Klasse [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient.</span><span class="sxs-lookup"><span data-stu-id="07e00-321">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="07e00-322">Im folgenden Beispiel werden mehrere Dateierweiterungen bei bekannten MIME-Typen registriert.</span><span class="sxs-lookup"><span data-stu-id="07e00-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="07e00-323">Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="07e00-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="07e00-324">Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="07e00-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="07e00-325">Inhaltstypen, die vom Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="07e00-325">Non-standard content types</span></span>

<span data-ttu-id="07e00-326">Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="07e00-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="07e00-327">Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="07e00-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="07e00-328">Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="07e00-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="07e00-329">Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="07e00-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="07e00-330">Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:</span><span class="sxs-lookup"><span data-stu-id="07e00-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="07e00-331">Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="07e00-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-332">Die Aktivierung von [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="07e00-332">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="07e00-333">Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="07e00-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="07e00-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.</span><span class="sxs-lookup"><span data-stu-id="07e00-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="07e00-335">Bereitstellen von Dateien aus mehreren Speicherorten</span><span class="sxs-lookup"><span data-stu-id="07e00-335">Serve files from multiple locations</span></span>

<span data-ttu-id="07e00-336">Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="07e00-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="07e00-337">Sie können weitere Instanzen von `UseStaticFiles` und `UseFileServer` mit anderen Dateianbietern bereitstellen, um Dateien von anderen Speicherorten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="07e00-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="07e00-338">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="07e00-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="07e00-339">Weitere Überlegungen</span><span class="sxs-lookup"><span data-stu-id="07e00-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-340">`UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken.</span><span class="sxs-lookup"><span data-stu-id="07e00-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="07e00-341">Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="07e00-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="07e00-342">Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="07e00-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="07e00-343">Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht.</span><span class="sxs-lookup"><span data-stu-id="07e00-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="07e00-344">Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="07e00-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="07e00-345">Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages (nur 2.x), Konfigurationsdateien usw.</span><span class="sxs-lookup"><span data-stu-id="07e00-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="07e00-346">Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems.</span><span class="sxs-lookup"><span data-stu-id="07e00-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="07e00-347">Bei Windows wird die Groß-/Kleinschreibung beispielsweise beachtet, bei macOS und Linux hingegen nicht.</span><span class="sxs-lookup"><span data-stu-id="07e00-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="07e00-348">In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="07e00-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="07e00-349">Der statische IIS-Dateihandler wird nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="07e00-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="07e00-350">Er kann Anforderungen erst verarbeiten, nachdem sie vom Modul verarbeitet wurden.</span><span class="sxs-lookup"><span data-stu-id="07e00-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="07e00-351">Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="07e00-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="07e00-352">Navigieren Sie zum Feature **Module**.</span><span class="sxs-lookup"><span data-stu-id="07e00-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="07e00-353">Wählen Sie **StaticFileModule** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="07e00-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="07e00-354">Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.</span><span class="sxs-lookup"><span data-stu-id="07e00-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="07e00-355">Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="07e00-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="07e00-356">Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.</span><span class="sxs-lookup"><span data-stu-id="07e00-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="07e00-357">Platzieren Sie Codedateien (einschließlich *.cs* und *.cshtml*) außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts.</span><span class="sxs-lookup"><span data-stu-id="07e00-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="07e00-358">Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen.</span><span class="sxs-lookup"><span data-stu-id="07e00-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="07e00-359">Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.</span><span class="sxs-lookup"><span data-stu-id="07e00-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e00-360">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="07e00-360">Additional resources</span></span>

* [<span data-ttu-id="07e00-361">Middleware</span><span class="sxs-lookup"><span data-stu-id="07e00-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="07e00-362">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07e00-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
