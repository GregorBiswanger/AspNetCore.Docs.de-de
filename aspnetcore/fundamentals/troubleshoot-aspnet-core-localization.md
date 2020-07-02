---
title: Problembehandlung bei der ASP.NET Core-Lokalisierung
author: hishamco
description: Erfahren Sie, wie Sie Probleme mit der Lokalisierung von ASP.NET Core-Apps diagnostizieren können.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 48f75b4fbfdb2078f07efeffd8d4105366998876
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407082"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="d7039-103">Problembehandlung bei der ASP.NET Core-Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="d7039-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="d7039-104">Von [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="d7039-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="d7039-105">Dieser Artikel enthält Anweisungen für die Diagnose von Problemen mit der Lokalisierung von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="d7039-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="d7039-106">Probleme bei der Lokalisierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="d7039-106">Localization configuration issues</span></span>

<span data-ttu-id="d7039-107">**Reihenfolge der Lokalisierungsmiddleware**</span><span class="sxs-lookup"><span data-stu-id="d7039-107">**Localization middleware order**</span></span>  
<span data-ttu-id="d7039-108">Die App kann möglicherweise nicht lokalisiert werden, da die Lokalisierungsmiddleware nicht in der erwarteten Reihenfolge angeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="d7039-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="d7039-109">Um dieses Problem zu beheben, stellen Sie sicher, dass die Lokalisierungsmiddleware vor der MVC-Middleware registriert wurde.</span><span class="sxs-lookup"><span data-stu-id="d7039-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="d7039-110">Andernfalls wird die Lokalisierungsmiddleware nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="d7039-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="d7039-111">**Pfad der Lokalisierungsmiddleware nicht gefunden**</span><span class="sxs-lookup"><span data-stu-id="d7039-111">**Localization resources path not found**</span></span>

<span data-ttu-id="d7039-112">**Unterstützte Kulturen im RequestCultureProvider entsprechen nicht der einmaligen Registrierung**</span><span class="sxs-lookup"><span data-stu-id="d7039-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="d7039-113">Probleme mit dem Ressourcendateinamen</span><span class="sxs-lookup"><span data-stu-id="d7039-113">Resource file naming issues</span></span>

<span data-ttu-id="d7039-114">ASP.NET Core verfügt über vordefinierte Regeln und Richtlinien für die Benennung von Lokalisierungsressourcendateien, die [hier](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) im Detail beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="d7039-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="d7039-115">Fehlende Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d7039-115">Missing resources</span></span>

<span data-ttu-id="d7039-116">Häufige Ursachen, warum Ressourcen nicht gefundenen werden, sind:</span><span class="sxs-lookup"><span data-stu-id="d7039-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="d7039-117">Ressourcennamen sind entweder in der `resx`-Datei oder der Lokalisierungsanforderung falsch geschrieben.</span><span class="sxs-lookup"><span data-stu-id="d7039-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="d7039-118">Die Ressource fehlt für einige Sprachen in `resx`, ist für andere aber vorhanden.</span><span class="sxs-lookup"><span data-stu-id="d7039-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="d7039-119">Wenn weiterhin Probleme auftreten, überprüfen Sie die Protokollmeldungen der Lokalisierung (auf der `Debug`-Protokollebene) auf weitere Informationen zu den fehlenden Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="d7039-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="d7039-120">_**Hinweis:** Bei Verwendung von `CookieRequestCultureProvider` vergewissern Sie sich, dass mit den Kulturen im Lokalisierungscookiewert keine einfachen Anführungszeichen verwendet werden. `c='en-UK'|uic='en-US'` ist beispielsweise ein ungültiger Cookiewert, während `c=en-UK|uic=en-US` gültig ist._</span><span class="sxs-lookup"><span data-stu-id="d7039-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="d7039-121">Probleme mit Ressourcen und Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="d7039-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="d7039-122">ASP.NET Core stellt standardmäßig eine Methode zur Verfügung, Klassenbibliotheken zu ermöglichen, ihre Ressourcendateien über [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) zu finden.</span><span class="sxs-lookup"><span data-stu-id="d7039-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="d7039-123">Folgende Probleme mit Klassenbibliotheken treten häufig auf:</span><span class="sxs-lookup"><span data-stu-id="d7039-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="d7039-124">Das Fehlen von `ResourceLocationAttribute` in einer Klassenbibliothek verhindert, dass `ResourceManagerStringLocalizerFactory` die Ressourcen ermitteln kann.</span><span class="sxs-lookup"><span data-stu-id="d7039-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="d7039-125">Ressourcendateinamen.</span><span class="sxs-lookup"><span data-stu-id="d7039-125">Resource file naming.</span></span> <span data-ttu-id="d7039-126">Weitere Informationen finden Sie unter [Probleme mit dem Ressourcendateinamen](#resource-file-naming-issues).</span><span class="sxs-lookup"><span data-stu-id="d7039-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="d7039-127">Ändern des Stammnamespace der Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="d7039-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="d7039-128">Weitere Informationen finden Sie unter [Probleme mit dem Stammnamespace](#root-namespace-issues) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="d7039-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="d7039-129">CustomRequestCultureProvider funktioniert nicht wie erwartet</span><span class="sxs-lookup"><span data-stu-id="d7039-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="d7039-130">Die `RequestLocalizationOptions`-Klasse verfügt über drei Standardanbieter:</span><span class="sxs-lookup"><span data-stu-id="d7039-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="d7039-131">Mit [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) können Sie anpassen, wie die Lokalisierungskultur in Ihrer App bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d7039-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="d7039-132">`CustomRequestCultureProvider` wird verwendet, wenn die Standardanbieter Ihren Anforderungen nicht entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d7039-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="d7039-133">Ein häufiger Grund dafür, dass ein benutzerdefinierter Anbieter nicht ordnungsgemäß funktioniert, besteht darin, dass er nicht der erste Anbieter in der `RequestCultureProviders`-Liste ist.</span><span class="sxs-lookup"><span data-stu-id="d7039-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="d7039-134">So beheben Sie dieses Problem:</span><span class="sxs-lookup"><span data-stu-id="d7039-134">To resolve this issue:</span></span>

- <span data-ttu-id="d7039-135">Fügen Sie den benutzerdefinierten Anbieter wie folgt an der Position 0 in die `RequestCultureProviders` Liste ein:</span><span class="sxs-lookup"><span data-stu-id="d7039-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="d7039-136">Verwenden Sie die `AddInitialRequestCultureProvider`-Erweiterungsmethode, um den benutzerdefinierten Anbieter als ersten Anbieter festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d7039-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="d7039-137">Probleme mit dem Stammnamespace</span><span class="sxs-lookup"><span data-stu-id="d7039-137">Root Namespace issues</span></span>

<span data-ttu-id="d7039-138">Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, funktioniert die Lokalisierung standardmäßig nicht.</span><span class="sxs-lookup"><span data-stu-id="d7039-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="d7039-139">Um dieses Problem zu vermeiden, verwenden Sie [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), das [hier](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) im Detail beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="d7039-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="d7039-140">Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist.</span><span class="sxs-lookup"><span data-stu-id="d7039-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="d7039-141">Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt.</span><span class="sxs-lookup"><span data-stu-id="d7039-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="d7039-142">Ressourcen und Buildaktion</span><span class="sxs-lookup"><span data-stu-id="d7039-142">Resources & Build Action</span></span>

<span data-ttu-id="d7039-143">Wenn Sie Ressourcendateien für die Lokalisierung verwenden, ist es wichtig, dass sie über eine geeignete Buildaktion verfügen.</span><span class="sxs-lookup"><span data-stu-id="d7039-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="d7039-144">Sie sollten eine **eingebettete Ressource** sein, andernfalls kann `ResourceStringLocalizer` diese Ressourcen nicht finden.</span><span class="sxs-lookup"><span data-stu-id="d7039-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
