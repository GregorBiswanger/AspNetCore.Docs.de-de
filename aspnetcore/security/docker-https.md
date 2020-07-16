---
title: Hosting von ASP.net Core Images mit docker über HTTPS
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-https
ms.openlocfilehash: 6a83695ff2a9ac7229d1d5086ed13594626476ee
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407657"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="74436-103">Hosting von ASP.net Core Images mit docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="74436-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="74436-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="74436-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="74436-105">ASP.net Core verwendet [standardmäßig HTTPS](/aspnet/core/security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="74436-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="74436-106">[Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="74436-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="74436-107">In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="74436-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="74436-108">Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="74436-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="74436-109">Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.</span><span class="sxs-lookup"><span data-stu-id="74436-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="74436-110">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="74436-110">Prerequisites</span></span>

<span data-ttu-id="74436-111">Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://dotnet.microsoft.com/download) oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="74436-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="74436-112">Zertifikate</span><span class="sxs-lookup"><span data-stu-id="74436-112">Certificates</span></span>

<span data-ttu-id="74436-113">Ein Zertifikat von einer [Zertifizierungs](https://wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich.</span><span class="sxs-lookup"><span data-stu-id="74436-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="74436-114">[Let's Encrypt](https://letsencrypt.org/)ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.</span><span class="sxs-lookup"><span data-stu-id="74436-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="74436-115">In diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://en.wikipedia.org/wiki/Self-signed_certificate) für das Hosting von vordefinierten Images verwendet `localhost` .</span><span class="sxs-lookup"><span data-stu-id="74436-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="74436-116">Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="74436-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="74436-117">Für produktionscerts:</span><span class="sxs-lookup"><span data-stu-id="74436-117">For production certs:</span></span>

* <span data-ttu-id="74436-118">Das `dotnet dev-certs` Tool ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="74436-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="74436-119">Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="74436-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="74436-120">Jeder Speicherort sollte funktionieren, obwohl das Speichern von Zertifikaten innerhalb Ihres Website Verzeichnisses nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="74436-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="74436-121">Die Anweisungen im folgenden Abschnitt enthalten Informationen zum Einbinden von Zertifikaten in Container mithilfe der Docker- `-v` Befehlszeilenoption.</span><span class="sxs-lookup"><span data-stu-id="74436-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="74436-122">Sie können Zertifikate in Container Images mit einem `COPY` Befehl in einer *dockerfile-Datei*hinzufügen, dies wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="74436-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="74436-123">Das Kopieren von Zertifikaten in ein Abbild wird aus den folgenden Gründen nicht empfohlen:</span><span class="sxs-lookup"><span data-stu-id="74436-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="74436-124">Es ist schwierig, das gleiche Image für Tests mit Entwickler Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="74436-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="74436-125">Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="74436-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="74436-126">Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="74436-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="74436-127">Ausführen von vorgefertigten Container Images mit HTTPS</span><span class="sxs-lookup"><span data-stu-id="74436-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="74436-128">Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="74436-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="74436-129">Windows mithilfe von Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="74436-129">Windows using Linux containers</span></span>

<span data-ttu-id="74436-130">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="74436-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="74436-131">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="74436-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="74436-132">Führen Sie das Container Image mit ASP.net Core für HTTPS in einer Befehlsshell konfiguriert aus:</span><span class="sxs-lookup"><span data-stu-id="74436-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="74436-133">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="74436-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="74436-134">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="74436-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="74436-135">macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="74436-135">macOS or Linux</span></span>

<span data-ttu-id="74436-136">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="74436-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="74436-137">`dotnet dev-certs https --trust`wird nur unter macOS und Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="74436-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="74436-138">Sie müssen Zertifikate unter Linux in der von Ihrer Distribution unterstützten Weise als vertrauenswürdig einstufen.</span><span class="sxs-lookup"><span data-stu-id="74436-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="74436-139">Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.</span><span class="sxs-lookup"><span data-stu-id="74436-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="74436-140">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="74436-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="74436-141">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="74436-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="74436-142">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="74436-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="74436-143">Windows mithilfe von Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="74436-143">Windows using Windows containers</span></span>

<span data-ttu-id="74436-144">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="74436-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="74436-145">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="74436-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="74436-146">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="74436-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="74436-147">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="74436-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="74436-148">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="74436-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="74436-149">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="74436-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
