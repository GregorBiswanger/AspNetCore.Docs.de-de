---
title: Softwareschlüsselspeicher-Anbieter in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu den softwareschlüsselspeicher-Anbieter in ASP.NET Core und wichtige Speicherorte zu konfigurieren.
ms.author: riande
ms.date: 12/06/2018
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: e10271d5979b503a8a842f8866a0e2a3fa040656
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121452"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="3437d-103">Softwareschlüsselspeicher-Anbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3437d-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="3437d-104">Das System zum Schutz von Daten [Discovery-Mechanismus in der Standardeinstellung setzt](xref:security/data-protection/configuration/default-settings) um zu bestimmen, in denen kryptografische Schlüssel beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="3437d-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="3437d-105">Der Entwickler kann außer Kraft setzen als standardmäßiger Ermittlungsmechanismus verwendet und geben Sie den Speicherort manuell.</span><span class="sxs-lookup"><span data-stu-id="3437d-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="3437d-106">Wenn Sie einem Standort der explizite schlüsselpersistenz angeben, hebt die System zum Schutz von Daten, sodass Schlüssel nicht mehr im Ruhezustand verschlüsselt sind die wichtigsten standardverschlüsselung auf Rest-Mechanismus, Registrierung.</span><span class="sxs-lookup"><span data-stu-id="3437d-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="3437d-107">Es wird empfohlen, die Sie darüber hinaus [Geben Sie einen Mechanismus für die explizite Schlüsselverschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest) für produktionsbereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="3437d-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="3437d-108">Dateisystem</span><span class="sxs-lookup"><span data-stu-id="3437d-108">File system</span></span>

<span data-ttu-id="3437d-109">Um eine Datei systembasierte Key Repository zu konfigurieren, rufen Sie die [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) Configuration-Routine, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="3437d-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="3437d-110">Geben Sie einen [DirectoryInfo](/dotnet/api/system.io.directoryinfo) auf das Repository, in dem Schlüssel gespeichert werden soll:</span><span class="sxs-lookup"><span data-stu-id="3437d-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="3437d-111">Die `DirectoryInfo` kann in ein Verzeichnis auf dem lokalen Computer, oder es kann auf einen Ordner auf einer Netzwerkfreigabe verweisen.</span><span class="sxs-lookup"><span data-stu-id="3437d-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="3437d-112">Wenn ein Verzeichnis auf dem lokalen Computer verweist (und das Szenario ist, dass nur die apps auf dem lokalen Computer Zugriff auf dieses Repository verwenden müssen) in Betracht [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (unter Windows), die Schlüssel im Ruhezustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="3437d-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="3437d-113">Erwägen Sie andernfalls die Verwendung einer [x. 509-Zertifikat](xref:security/data-protection/implementation/key-encryption-at-rest) Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="3437d-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="3437d-114">Azure und Redis</span><span class="sxs-lookup"><span data-stu-id="3437d-114">Azure and Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3437d-115">Die [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) und [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) ermöglichen, dass das Speichern von Data Protection-Schlüssel in Azure Storage oder eines Redis-Pakete Cache.</span><span class="sxs-lookup"><span data-stu-id="3437d-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="3437d-116">Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="3437d-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="3437d-117">Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.</span><span class="sxs-lookup"><span data-stu-id="3437d-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3437d-118">Die [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) und [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) Pakete ermöglichen das Speichern von Data Protection-Schlüssel in Azure Storage oder einem Redis-Cache.</span><span class="sxs-lookup"><span data-stu-id="3437d-118">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="3437d-119">Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="3437d-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="3437d-120">Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.</span><span class="sxs-lookup"><span data-stu-id="3437d-120">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

<span data-ttu-id="3437d-121">Zum Konfigurieren des Azure Blob Storage-Anbieters rufen Sie eine der der [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) Überladungen:</span><span class="sxs-lookup"><span data-stu-id="3437d-121">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3437d-122">Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) Überladungen:</span><span class="sxs-lookup"><span data-stu-id="3437d-122">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3437d-123">Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) Überladungen:</span><span class="sxs-lookup"><span data-stu-id="3437d-123">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="3437d-124">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="3437d-124">For more information, see the following topics:</span></span>

* [<span data-ttu-id="3437d-125">"Stackexchange.redis" ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="3437d-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="3437d-126">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="3437d-126">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="3437d-127">ASPNET/DataProtection-Beispiele</span><span class="sxs-lookup"><span data-stu-id="3437d-127">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="3437d-128">Registrierung</span><span class="sxs-lookup"><span data-stu-id="3437d-128">Registry</span></span>

<span data-ttu-id="3437d-129">**Gilt nur für Windows-Bereitstellungen.**</span><span class="sxs-lookup"><span data-stu-id="3437d-129">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="3437d-130">Die app möglicherweise manchmal nicht über Schreibzugriff auf das Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="3437d-130">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="3437d-131">Stellen Sie sich ein Szenario, in denen eine app als ein virtuelles Dienstkonto ausgeführt wird (z. B. *w3wp.exe*des app-Pool-Identität).</span><span class="sxs-lookup"><span data-stu-id="3437d-131">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="3437d-132">In diesen Fällen kann der Administrator einen Registrierungsschlüssel bereitstellen, der von der dienstkontoidentität zugänglich ist.</span><span class="sxs-lookup"><span data-stu-id="3437d-132">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="3437d-133">Rufen Sie die [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) Erweiterungsmethode wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="3437d-133">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="3437d-134">Geben Sie einen [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) verweist auf den Speicherort, in der kryptografischen Schlüssel gespeichert werden soll:</span><span class="sxs-lookup"><span data-stu-id="3437d-134">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="3437d-135">Es wird empfohlen, [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) den Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="3437d-135">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="3437d-136">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3437d-136">Entity Framework Core</span></span>

<span data-ttu-id="3437d-137">Die [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) -Paket bietet einen Mechanismus zum Speichern von Data Protection-Schlüssel in einer Datenbank mit Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3437d-137">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="3437d-138">Die `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet-Paket muss hinzugefügt werden der Projektdatei, es ist nicht Teil der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3437d-138">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3437d-139">Mit diesem Paket können Schlüssel über mehrere Instanzen einer Web-app hinweg freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3437d-139">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="3437d-140">Rufen Sie zum Konfigurieren des EF Core-Anbieters die [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) Methode:</span><span class="sxs-lookup"><span data-stu-id="3437d-140">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="3437d-141">Der generische Parameter, `TContext`, erben müssen ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) und [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="3437d-141">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="3437d-142">Benutzerdefinierte Schlüssel-repository</span><span class="sxs-lookup"><span data-stu-id="3437d-142">Custom key repository</span></span>

<span data-ttu-id="3437d-143">Wenn die integrierte Mechanismen nicht geeignet sind, kann Entwickler ihre eigenen Schlüssel persistenzmechanismus angeben, durch Bereitstellen eines benutzerdefinierten [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="3437d-143">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
