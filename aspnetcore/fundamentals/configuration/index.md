---
title: Konfiguration in ASP.NET Core
author: rick-anderson
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: fe0a0d3dbb87455be602234825d702fab02df936
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634591"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8739d-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8739d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8739d-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8739d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8739d-105">Die Konfiguration in ASP.NET Core erfolgt mithilfe eines oder mehrerer [Konfigurationsanbieter](#cp).</span><span class="sxs-lookup"><span data-stu-id="8739d-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8739d-106">Konfigurationsanbieter lesen Konfigurationsdaten aus Schlüssel-Wert-Paaren unter Verwendung verschiedener Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="8739d-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8739d-107">Einstellungsdateien, z. B. *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8739d-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8739d-108">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-108">Environment variables</span></span>
* <span data-ttu-id="8739d-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739d-109">Azure Key Vault</span></span>
* <span data-ttu-id="8739d-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8739d-110">Azure App Configuration</span></span>
* <span data-ttu-id="8739d-111">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8739d-111">Command-line arguments</span></span>
* <span data-ttu-id="8739d-112">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="8739d-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="8739d-113">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8739d-113">Directory files</span></span>
* <span data-ttu-id="8739d-114">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="8739d-114">In-memory .NET objects</span></span>

<span data-ttu-id="8739d-115">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8739d-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8739d-116">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-116">Default configuration</span></span>

<span data-ttu-id="8739d-117">ASP.NET Core-Web-Apps, die mit [dotnet new](/dotnet/core/tools/dotnet-new) oder Visual Studio erstellt wurden, generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8739d-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8739d-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="8739d-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8739d-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  Fügt eine vorhandene `IConfiguration` als Quelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="8739d-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8739d-120">Im Fall einer Standardkonfiguration wird die [Host](#hvac)-Konfiguration hinzugefügt und als erste Quelle für die _App-_ Konfiguration festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8739d-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8739d-121">[appsettings.json](#appsettingsjson) mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8739d-122">*appsettings.* `Environment` *.json* mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8739d-123">Beispielsweise *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="8739d-124">[App-Geheimnisse](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="8739d-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8739d-125">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="8739d-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8739d-126">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line) verwenden</span><span class="sxs-lookup"><span data-stu-id="8739d-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8739d-127">Später hinzugefügte Konfigurationsanbieter überschreiben vorherige Schlüsseleinstellungen.</span><span class="sxs-lookup"><span data-stu-id="8739d-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8739d-128">Wenn beispielsweise `MyKey` sowohl unter *appsettings.json* als auch unter Umgebung festgelegt wird, wird der Umgebungswert verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8739d-129">Bei Verwendung der Standardkonfigurationsanbieter überschreibt der [Befehlszeilen-Konfigurationsanbieter](#clcp) alle anderen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="8739d-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="8739d-130">Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter [Standardeinstellungen für den Generator](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="8739d-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8739d-131">Im folgenden Code werden die aktivierten Konfigurationsanbieter in der Reihenfolge angezeigt, in der sie hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="8739d-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="8739d-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8739d-132">appsettings.json</span></span>

<span data-ttu-id="8739d-133">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="8739d-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8739d-134">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="8739d-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-135">Der Standard-<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="8739d-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="8739d-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8739d-136">*appsettings.json*</span></span>
1. <span data-ttu-id="8739d-137">*appsettings.* `Environment` *.json*: Beispielsweise die Dateien *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="8739d-138">Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="8739d-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8739d-139">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8739d-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8739d-140">*appsettings*.`Environment`.*json*-Werte überschreiben Schlüssel in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8739d-141">Standardmäßig sind dies z. B.:</span><span class="sxs-lookup"><span data-stu-id="8739d-141">For example, by default:</span></span>

* <span data-ttu-id="8739d-142">In der Entwicklung überschreibt die *appsettings*.***Development***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="8739d-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8739d-143">In der Produktion überschreibt die *appsettings*.***Production***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="8739d-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8739d-144">Dies ist beispielsweise bei der Bereitstellung der App in Azure der Fall.</span><span class="sxs-lookup"><span data-stu-id="8739d-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8739d-145">Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster</span><span class="sxs-lookup"><span data-stu-id="8739d-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="8739d-146">Wenn die [Standard](#default)-Konfiguration verwendet wird, werden die Dateien *appsettings.json* und *appsettings.* `Environment` *.json* mit [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8739d-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8739d-147">Änderungen an den Dateien *appsettings.json* und *appsettings.* `Environment` *.json* ***nach*** dem Start der App werden vom [JSON-Konfigurationsanbieter](#jcp) gelesen.</span><span class="sxs-lookup"><span data-stu-id="8739d-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8739d-148">Weitere Informationen zum Hinzufügen zusätzlicher JSON-Konfigurationsdateien finden Sie unter [JSON-Konfigurationsanbieter](#jcp) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="8739d-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8739d-149">Sicherheits- und Secret-Manager</span><span class="sxs-lookup"><span data-stu-id="8739d-149">Security and secret manager</span></span>

<span data-ttu-id="8739d-150">Richtlinien für Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="8739d-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="8739d-151">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="8739d-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8739d-152">Der [Secret Manager](xref:security/app-secrets) kann zum Speichern von Geheimnissen in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8739d-153">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="8739d-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8739d-154">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="8739d-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8739d-155">[Standardmäßig](#default) liest [Secret Manager](xref:security/app-secrets) Konfigurationseinstellungen nach dem Lesen von *appsettings.json* und *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8739d-156">Weitere Informationen zum Speichern von Kennwörtern oder anderen vertraulichen Daten:</span><span class="sxs-lookup"><span data-stu-id="8739d-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8739d-157"><xref:security/app-secrets>:  Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="8739d-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8739d-158">Der Secret Manager verwendet den [Dateikonfigurationsanbieter](#fcp), um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="8739d-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8739d-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="8739d-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8739d-160">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8739d-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8739d-161">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-161">Environment variables</span></span>

<span data-ttu-id="8739d-162">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> die Konfiguration aus Schlüssel-Wert-Paaren der Umgebungsvariablen, nachdem *appsettings.json*, *appsettings.* `Environment` *.json* und [Secret Manager](xref:security/app-secrets) gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8739d-163">Daher überschreiben aus der Umgebung gelesene Schlüsselwerte Werte, die aus *appsettings.json*, *appsettings.* `Environment` *.json* und Secret Manager gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8739d-164">Die folgenden `set`-Befehle:</span><span class="sxs-lookup"><span data-stu-id="8739d-164">The following `set` commands:</span></span>

* <span data-ttu-id="8739d-165">Legen die Umgebungsschlüssel und -werte des [vorangehenden Beispiels](#appsettingsjson) unter Windows fest.</span><span class="sxs-lookup"><span data-stu-id="8739d-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8739d-166">Testen die Einstellungen bei Verwendung des [Beispieldownloads](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="8739d-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8739d-167">Der `dotnet run`-Befehl muss im Projektverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8739d-168">Die obigen Umgebungseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="8739d-168">The preceding environment settings:</span></span>

* <span data-ttu-id="8739d-169">Werden nur in Prozessen festgelegt, die über das Befehlsfenster gestartet werden, in dem sie festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8739d-170">Werden nicht von Browsern gelesen, die mit Visual Studio gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8739d-171">Die folgenden [setx](/windows-server/administration/windows-commands/setx)-Befehle können zum Festlegen der Umgebungsschlüssel und -werte unter Windows verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8739d-172">Anders als `set` werden `setx`-Einstellungen beibehalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8739d-173">`/M` legt die Variable in der Systemumgebung fest.</span><span class="sxs-lookup"><span data-stu-id="8739d-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8739d-174">Wenn der `/M`-Schalter nicht verwendet wird, wird eine Benutzerumgebungsvariable festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8739d-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8739d-175">Testen Sie wie folgt, ob die obigen Befehle *appsettings.json* und *appsettings.* `Environment` *.json* überschreiben:</span><span class="sxs-lookup"><span data-stu-id="8739d-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8739d-176">Mit Visual Studio: Beenden Sie Visual Studio, und starten Sie dann Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="8739d-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8739d-177">Mit der Befehlszeilenschnittstelle: Starten Sie ein neues Befehlsfenster, und geben Sie `dotnet run` ein.</span><span class="sxs-lookup"><span data-stu-id="8739d-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8739d-178">Rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> mit einer Zeichenfolge auf, um ein Präfix für Umgebungsvariablen anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8739d-179">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="8739d-179">In the preceding code:</span></span>

* <span data-ttu-id="8739d-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` wird nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8739d-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8739d-181">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8739d-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8739d-182">Umgebungsvariablen, die mit dem `MyCustomPrefix_`-Präfix festgelegt wurden, überschreiben die [Standardkonfigurationsanbieter](#default).</span><span class="sxs-lookup"><span data-stu-id="8739d-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8739d-183">Dies schließt Umgebungsvariablen ohne das Präfix ein.</span><span class="sxs-lookup"><span data-stu-id="8739d-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8739d-184">Das Präfix wird beim Lesen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8739d-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8739d-185">Mit den folgenden Befehlen wird das benutzerdefinierte Präfix getestet:</span><span class="sxs-lookup"><span data-stu-id="8739d-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8739d-186">Die [Standardkonfiguration](#default) lädt Umgebungsvariablen und Befehlszeilenargumente, die das Präfix `DOTNET_` und `ASPNETCORE_` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="8739d-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8739d-187">Die Präfixe `DOTNET_` und `ASPNETCORE_` werden von ASP.NET Core für die [Host- und App-Konfiguration](xref:fundamentals/host/generic-host#host-configuration), jedoch nicht für die Benutzerkonfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8739d-188">Weitere Informationen zur Host- und App-Konfiguration finden Sie unter [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8739d-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8739d-189">Wählen Sie in [Azure App Service](https://azure.microsoft.com/services/app-service/) auf der Seite **Einstellungen > Konfiguration** die Option **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8739d-190">Anwendungseinstellungen von Azure App Service werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8739d-191">Im Ruhezustand verschlüsselt und über einen verschlüsselten Kanal übermittelt.</span><span class="sxs-lookup"><span data-stu-id="8739d-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8739d-192">Als Umgebungsvariablen verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="8739d-192">Exposed as environment variables.</span></span>

<span data-ttu-id="8739d-193">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8739d-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8739d-194">Informationen zu Azure-Datenbankverbindungszeichenfolgen finden Sie unter [Präfixe für Verbindungszeichenfolgen](#constr).</span><span class="sxs-lookup"><span data-stu-id="8739d-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="8739d-195">In „launchSettings.json“ festgelegte Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="8739d-196">Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8739d-197">Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="8739d-197">Command-line</span></span>

<span data-ttu-id="8739d-198">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments nach den folgenden Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="8739d-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8739d-199">Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8739d-200">[App-Geheimnisse (Secret Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8739d-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739d-201">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8739d-201">Environment variables.</span></span>

<span data-ttu-id="8739d-202">[Standardmäßig](#default) überschreiben in der Befehlszeile festgelegte Konfigurationswerte die Konfigurationswerte, die mit allen anderen Konfigurationsanbietern festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8739d-203">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8739d-203">Command-line arguments</span></span>

<span data-ttu-id="8739d-204">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `=` fest:</span><span class="sxs-lookup"><span data-stu-id="8739d-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8739d-205">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `/` fest:</span><span class="sxs-lookup"><span data-stu-id="8739d-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8739d-206">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `--` fest:</span><span class="sxs-lookup"><span data-stu-id="8739d-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8739d-207">Der Schlüsselwert:</span><span class="sxs-lookup"><span data-stu-id="8739d-207">The key value:</span></span>

* <span data-ttu-id="8739d-208">Muss auf `=` folgen, oder der Schlüssel muss das Präfix `--` oder `/` aufweisen, wenn der Wert auf ein Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="8739d-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8739d-209">Ist nicht erforderlich, wenn `=` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="8739d-210">Beispielsweise `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="8739d-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="8739d-211">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare des Befehlszeilenarguments, die `=` verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="8739d-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8739d-212">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="8739d-212">Switch mappings</span></span>

<span data-ttu-id="8739d-213">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von **Schlüsselnamen**.</span><span class="sxs-lookup"><span data-stu-id="8739d-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8739d-214">Stellen Sie ein Wörterbuch mit Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="8739d-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8739d-215">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="8739d-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8739d-216">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8739d-217">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8739d-218">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="8739d-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8739d-219">Switches müssen mit `-` oder `--` beginnen.</span><span class="sxs-lookup"><span data-stu-id="8739d-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8739d-220">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8739d-221">Wenn Sie ein Switchmappingwörterbuch verwenden möchten, übergeben Sie es an den `AddCommandLine`-Abruf:</span><span class="sxs-lookup"><span data-stu-id="8739d-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8739d-222">Der folgende Code zeigt die Schlüsselwerte für die ersetzten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="8739d-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-223">Mit dem folgenden Befehl kann die Schlüsselersetzung getestet werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8739d-224">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-224">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8739d-225">Der `AddCommandLine`-Aufruf der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-225">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739d-226">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der `AddCommandLine`-Methode der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8739d-226">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8739d-227">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="8739d-227">Hierarchical configuration data</span></span>

<span data-ttu-id="8739d-228">Die Konfigurations-API liest hierarchische Konfigurationsdaten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="8739d-228">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8739d-229">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-229">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8739d-230">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt einige der Konfigurationseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="8739d-230">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-231">Die bevorzugte Methode zum Lesen hierarchischer Konfigurationsdaten ist die Verwendung des Optionsmusters.</span><span class="sxs-lookup"><span data-stu-id="8739d-231">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8739d-232">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten](#optpat) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="8739d-232">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8739d-233">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-233"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8739d-234">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-234">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8739d-235">Konfigurationsschlüssel und -werte</span><span class="sxs-lookup"><span data-stu-id="8739d-235">Configuration keys and values</span></span>

<span data-ttu-id="8739d-236">Konfigurationsschlüssel:</span><span class="sxs-lookup"><span data-stu-id="8739d-236">Configuration keys:</span></span>

* <span data-ttu-id="8739d-237">Groß-/Kleinschreibung wird beachtet.</span><span class="sxs-lookup"><span data-stu-id="8739d-237">Are case-insensitive.</span></span> <span data-ttu-id="8739d-238">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="8739d-238">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8739d-239">Wenn ein Schlüssel und ein Wert in mehreren Konfigurationsanbietern festgelegt ist, wird der Wert des zuletzt hinzugefügten Anbieters verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-239">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8739d-240">Weitere Informationen finden Sie unter [Standardkonfiguration](#default).</span><span class="sxs-lookup"><span data-stu-id="8739d-240">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8739d-241">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-241">Hierarchical keys</span></span>
  * <span data-ttu-id="8739d-242">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8739d-242">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8739d-243">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8739d-243">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8739d-244">Ein doppelter Unterstrich (`__`) wird von allen Plattformen unterstützt und automatisch in einen Doppelpunkt (`:`) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="8739d-244">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8739d-245">In Azure Key Vault verwenden hierarchische Schlüssel `--` als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="8739d-245">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8739d-246">Der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) ersetzt `--` automatisch durch `:`, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-246">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8739d-247"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8739d-247">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739d-248">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#boa) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-248">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8739d-249">Konfigurationswerte:</span><span class="sxs-lookup"><span data-stu-id="8739d-249">Configuration values:</span></span>

* <span data-ttu-id="8739d-250">Sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8739d-250">Are strings.</span></span>
* <span data-ttu-id="8739d-251">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-251">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8739d-252">Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-252">Configuration providers</span></span>

<span data-ttu-id="8739d-253">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="8739d-253">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8739d-254">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-254">Provider</span></span> | <span data-ttu-id="8739d-255">Bereitstellung der Konfiguration über</span><span class="sxs-lookup"><span data-stu-id="8739d-255">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="8739d-256">Azure Key Vault-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-256">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="8739d-257">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739d-257">Azure Key Vault</span></span> |
| [<span data-ttu-id="8739d-258">Azure-App-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-258">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="8739d-259">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8739d-259">Azure App Configuration</span></span> |
| [<span data-ttu-id="8739d-260">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-260">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="8739d-261">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="8739d-261">Command-line parameters</span></span> |
| [<span data-ttu-id="8739d-262">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-262">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8739d-263">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="8739d-263">Custom source</span></span> |
| [<span data-ttu-id="8739d-264">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-264">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="8739d-265">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-265">Environment variables</span></span> |
| [<span data-ttu-id="8739d-266">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-266">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8739d-267">INI-, JSON- und XML-Dateien</span><span class="sxs-lookup"><span data-stu-id="8739d-267">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="8739d-268">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-268">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8739d-269">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8739d-269">Directory files</span></span> |
| [<span data-ttu-id="8739d-270">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-270">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8739d-271">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="8739d-271">In-memory collections</span></span> |
| [<span data-ttu-id="8739d-272">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="8739d-272">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="8739d-273">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8739d-273">File in the user profile directory</span></span> |

<span data-ttu-id="8739d-274">Konfigurationsquellen werden in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-274">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8739d-275">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-275">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8739d-276">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="8739d-276">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8739d-277">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8739d-277">*appsettings.json*</span></span>
1. <span data-ttu-id="8739d-278">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="8739d-278">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8739d-279">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="8739d-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8739d-280">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="8739d-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8739d-281">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="8739d-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8739d-282">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle hinzugefügt, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="8739d-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8739d-283">Die obige Sequenz von Anbietern wird in der [Standardkonfiguration](#default) verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8739d-284">Präfixe für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="8739d-284">Connection string prefixes</span></span>

<span data-ttu-id="8739d-285">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8739d-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8739d-286">Diese Verbindungszeichenfolgen sind beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung beteiligt.</span><span class="sxs-lookup"><span data-stu-id="8739d-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8739d-287">Umgebungsvariablen mit den in der Tabelle aufgeführten Präfixen werden mit der [Standardkonfiguration](#default) in die App geladen bzw. wenn kein Präfix für `AddEnvironmentVariables` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8739d-288">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="8739d-288">Connection string prefix</span></span> | <span data-ttu-id="8739d-289">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8739d-290">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8739d-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="8739d-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8739d-292">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="8739d-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8739d-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8739d-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8739d-294">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="8739d-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8739d-295">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8739d-296">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="8739d-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8739d-297">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-297">Environment variable key</span></span> | <span data-ttu-id="8739d-298">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-298">Converted configuration key</span></span> | <span data-ttu-id="8739d-299">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="8739d-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-300">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-301">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-302">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-303">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-304">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-305">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-306">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8739d-307">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-307">File configuration provider</span></span>

<span data-ttu-id="8739d-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="8739d-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8739d-309">Die folgenden Konfigurationsanbieter leiten sich vom `FileConfigurationProvider` ab:</span><span class="sxs-lookup"><span data-stu-id="8739d-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8739d-310">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8739d-311">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8739d-312">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8739d-313">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-313">INI configuration provider</span></span>

<span data-ttu-id="8739d-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-315">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="8739d-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8739d-316">Im obigen Code werden Einstellungen in den Dateien *MyIniConfig.ini* und *MyIniConfig*.`Environment`.*ini* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="8739d-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8739d-317">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8739d-318">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="8739d-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739d-319">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="8739d-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8739d-320">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="8739d-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8739d-321">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-321">JSON configuration provider</span></span>

<span data-ttu-id="8739d-322">Der <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8739d-323">Überladungen können Folgendes angeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-323">Overloads can specify:</span></span>

* <span data-ttu-id="8739d-324">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8739d-324">Whether the file is optional.</span></span>
* <span data-ttu-id="8739d-325">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8739d-326">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="8739d-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8739d-327">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="8739d-327">The preceding code:</span></span>

* <span data-ttu-id="8739d-328">Konfiguriert den JSON-Konfigurationsanbieter, sodass dieser die Datei *MyConfig.json* mit den folgenden Optionen lädt:</span><span class="sxs-lookup"><span data-stu-id="8739d-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8739d-329">`optional: true`: Die Datei ist optional.</span><span class="sxs-lookup"><span data-stu-id="8739d-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8739d-330">`reloadOnChange: true` : Die Datei wird erneut geladen, wenn Änderungen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8739d-331">Liest die [Standardkonfigurationsanbieter](#default) vor der Datei *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8739d-332">Einstellungen in der Datei *MyConfig.json* überschreiben die Einstellung in den Standardkonfigurationsanbietern, einschließlich des [Umgebungsvariablen-Konfigurationsanbieters](#evcp) und des [Befehlszeilen-Konfigurationsanbieters](#clcp).</span><span class="sxs-lookup"><span data-stu-id="8739d-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739d-333">In der Regel möchten Sie ***nicht***, dass eine benutzerdefinierte JSON-Datei Werte überschreibt, die im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-333">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739d-334">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="8739d-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8739d-335">Im obigen Code gilt für Einstellungen in den Dateien *MyConfig.json* und *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-335">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8739d-336">Sie überschreiben Einstellungen in den Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-336">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8739d-337">Sie werden durch Einstellungen im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) überschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739d-338">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8739d-339">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="8739d-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8739d-340">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-340">XML configuration provider</span></span>

<span data-ttu-id="8739d-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-342">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="8739d-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8739d-343">Im obigen Code werden Einstellungen in den Dateien *MyXMLFile.xml* und *MyXMLFile*.`Environment`.*xml* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="8739d-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8739d-344">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8739d-345">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="8739d-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8739d-346">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="8739d-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8739d-347">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="8739d-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-348">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8739d-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8739d-349">Der folgende Code liest die vorherige Konfigurationsdatei und zeigt die Schlüssel und Werte an:</span><span class="sxs-lookup"><span data-stu-id="8739d-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-350">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8739d-351">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8739d-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739d-352">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739d-352">key:attribute</span></span>
* <span data-ttu-id="8739d-353">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739d-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8739d-354">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="8739d-355"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8739d-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8739d-356">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="8739d-356">The key is the file name.</span></span> <span data-ttu-id="8739d-357">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-357">The value contains the file's contents.</span></span> <span data-ttu-id="8739d-358">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarios verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8739d-359">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8739d-360">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="8739d-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8739d-361">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8739d-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739d-362">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="8739d-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8739d-363">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="8739d-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8739d-364">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8739d-365">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8739d-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8739d-366">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8739d-367">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-367">Memory configuration provider</span></span>

<span data-ttu-id="8739d-368"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8739d-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8739d-369">Der folgende Code fügt eine Arbeitsspeichersammlung zum Konfigurationssystem hinzu:</span><span class="sxs-lookup"><span data-stu-id="8739d-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8739d-370">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt die vorherigen Konfigurationseinstellungen an:</span><span class="sxs-lookup"><span data-stu-id="8739d-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-371">Im obigen Code wird `config.AddInMemoryCollection(Dict)` nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8739d-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8739d-372">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="8739d-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8739d-373">Ein weiteres Beispiel für die Verwendung von `MemoryConfigurationProvider` finden Sie unter [Binden eines Arrays](#boa).</span><span class="sxs-lookup"><span data-stu-id="8739d-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8739d-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="8739d-374">GetValue</span></span>

<span data-ttu-id="8739d-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ:</span><span class="sxs-lookup"><span data-stu-id="8739d-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-376">Wenn im obigen Code `NumberKey` nicht in der Konfiguration gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8739d-377">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="8739d-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8739d-378">Beachten Sie für die nachstehenden Beispiele die folgende Datei *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8739d-379">Der folgende Code fügt *MySubsection.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="8739d-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8739d-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="8739d-380">GetSection</span></span>

<span data-ttu-id="8739d-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) gibt einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel zurück.</span><span class="sxs-lookup"><span data-stu-id="8739d-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8739d-382">Der folgende Code gibt Werte für `section1` zurück:</span><span class="sxs-lookup"><span data-stu-id="8739d-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-383">Der folgende Code gibt Werte für `section2:subsection0` zurück:</span><span class="sxs-lookup"><span data-stu-id="8739d-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-384">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="8739d-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8739d-385">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8739d-386">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="8739d-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8739d-387">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8739d-388">GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="8739d-388">GetChildren and Exists</span></span>

<span data-ttu-id="8739d-389">Mit dem folgenden Code wird [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) abgerufen, und es werden Werte für `section2:subsection0` zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-390">Der obige Code ruft [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) auf, um zu überprüfen, ob der Abschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="8739d-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8739d-391">Binden eines Arrays</span><span class="sxs-lookup"><span data-stu-id="8739d-391">Bind an array</span></span>

<span data-ttu-id="8739d-392">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) unterstützt das Binden von Arrays an Objekte mithilfe von Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8739d-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739d-393">Jedes Arrayformat, das ein numerisches Schlüsselsegment verfügbar macht, kann ein Array an ein [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="8739d-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8739d-394">Beachten Sie *MyArray.json* aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="8739d-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8739d-395">Der folgende Code fügt *MyArray.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="8739d-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8739d-396">Der folgende Code liest die Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="8739d-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-397">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="8739d-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8739d-398">In der obigen Ausgabe hat Index 3 den Wert `value40`, der `"4": "value40",` in *MyArray.json* entspricht.</span><span class="sxs-lookup"><span data-stu-id="8739d-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8739d-399">Die gebundenen Arrayindizes sind kontinuierlich und nicht an den Konfigurationsschlüsselindex gebunden.</span><span class="sxs-lookup"><span data-stu-id="8739d-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8739d-400">Der Konfigurationsbinder ist nicht in der Lage, NULL-Werte zu binden oder NULL-Einträge in gebundenen Objekten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8739d-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8739d-401">Mit dem folgenden Code wird die `array:entries`-Konfiguration mit der <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode geladen:</span><span class="sxs-lookup"><span data-stu-id="8739d-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8739d-402">Der folgende Code liest die Konfiguration im `arrayDict` `Dictionary` und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="8739d-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-403">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="8739d-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8739d-404">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="8739d-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8739d-405">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8739d-406">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="8739d-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8739d-407">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`-Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das Schlüssel-Wert-Paar von Index &num;3 liest.</span><span class="sxs-lookup"><span data-stu-id="8739d-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8739d-408">Beachten Sie die folgende Datei *Value3.json* aus dem Beispieldownload:</span><span class="sxs-lookup"><span data-stu-id="8739d-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8739d-409">Der folgende Code enthält die Konfiguration für *Value3.json* und das `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="8739d-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8739d-410">Der folgende Code liest die obige Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="8739d-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-411">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="8739d-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8739d-412">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="8739d-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8739d-413">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-413">Custom configuration provider</span></span>

<span data-ttu-id="8739d-414">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="8739d-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8739d-415">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="8739d-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8739d-416">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8739d-417">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8739d-418">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8739d-419">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="8739d-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8739d-420">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8739d-421">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8739d-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8739d-422">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-422">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8739d-423">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8739d-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8739d-424">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-424">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8739d-425">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="8739d-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8739d-426">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-426">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8739d-427">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8739d-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8739d-428">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8739d-429">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="8739d-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8739d-430">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-430">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8739d-431">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8739d-432">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-432">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8739d-433">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8739d-434">Zugriffskonfiguration beim Start</span><span class="sxs-lookup"><span data-stu-id="8739d-434">Access configuration in Startup</span></span>

<span data-ttu-id="8739d-435">Der folgende Code zeigt Konfigurationsdaten in `Startup`-Methoden an:</span><span class="sxs-lookup"><span data-stu-id="8739d-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8739d-436">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8739d-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="8739d-437">Zugriffskonfiguration in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8739d-437">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8739d-438">Der folgende Code zeigt Konfigurationsdaten auf einer Razor-Seite an:</span><span class="sxs-lookup"><span data-stu-id="8739d-438">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="8739d-439">Im folgenden Code wird `MyOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="8739d-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="8739d-440">Das folgende Markup verwendet die [`@inject`](xref:mvc/views/razor#inject) Razor Direktive, um die Optionswerte aufzulösen und anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="8739d-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8739d-441">Zugriffskonfiguration in einer MVC-Ansichtsdatei</span><span class="sxs-lookup"><span data-stu-id="8739d-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8739d-442">Der folgende Code zeigt Konfigurationsdaten in einer MVC-Ansicht an:</span><span class="sxs-lookup"><span data-stu-id="8739d-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="8739d-443">Konfigurieren von Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="8739d-443">Configure options with a delegate</span></span>

<span data-ttu-id="8739d-444">In einem Delegaten konfigurierte Optionen überschreiben die in den Konfigurationsanbietern festgelegten Werte.</span><span class="sxs-lookup"><span data-stu-id="8739d-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="8739d-445">Das Konfigurieren von Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8739d-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="8739d-446">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8739d-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8739d-447">Er verwendet einen Delegaten, um Werte für `MyOptions` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="8739d-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="8739d-448">Im folgenden Code werden die Optionswerte angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8739d-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8739d-449">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` in *appsettings.json* angegeben und anschließend vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-449">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8739d-450">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="8739d-450">Host versus app configuration</span></span>

<span data-ttu-id="8739d-451">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="8739d-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8739d-452">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="8739d-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8739d-453">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8739d-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8739d-454">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8739d-455">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8739d-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8739d-456">Standardhostkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-456">Default host configuration</span></span>

<span data-ttu-id="8739d-457">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="8739d-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8739d-458">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8739d-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8739d-459">Umgebungsvariablen mit dem Präfix `DOTNET_` (z. B. `DOTNET_ENVIRONMENT`), die den [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8739d-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="8739d-460">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8739d-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8739d-461">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="8739d-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8739d-462">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="8739d-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8739d-463">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8739d-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8739d-464">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="8739d-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8739d-465">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8739d-466">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="8739d-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8739d-467">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-467">Other configuration</span></span>

<span data-ttu-id="8739d-468">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="8739d-468">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8739d-469">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8739d-470">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8739d-471">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="8739d-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8739d-472">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8739d-473">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="8739d-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8739d-474">Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="8739d-475">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="8739d-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8739d-476">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="8739d-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="8739d-477">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8739d-478">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8739d-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8739d-479">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8739d-479">Additional resources</span></span>

* [<span data-ttu-id="8739d-480">Quellcode der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8739d-481">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="8739d-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8739d-482">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="8739d-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8739d-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739d-483">Azure Key Vault</span></span>
* <span data-ttu-id="8739d-484">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8739d-484">Azure App Configuration</span></span>
* <span data-ttu-id="8739d-485">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8739d-485">Command-line arguments</span></span>
* <span data-ttu-id="8739d-486">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="8739d-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8739d-487">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8739d-487">Directory files</span></span>
* <span data-ttu-id="8739d-488">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-488">Environment variables</span></span>
* <span data-ttu-id="8739d-489">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="8739d-489">In-memory .NET objects</span></span>
* <span data-ttu-id="8739d-490">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="8739d-490">Settings files</span></span>

<span data-ttu-id="8739d-491">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8739d-492">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="8739d-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8739d-493">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="8739d-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8739d-494">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8739d-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8739d-495">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8739d-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8739d-496">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8739d-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8739d-497">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="8739d-497">Host versus app configuration</span></span>

<span data-ttu-id="8739d-498">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="8739d-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8739d-499">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="8739d-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8739d-500">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8739d-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8739d-501">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8739d-502">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8739d-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8739d-503">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-503">Other configuration</span></span>

<span data-ttu-id="8739d-504">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="8739d-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8739d-505">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8739d-506">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8739d-507">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="8739d-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8739d-508">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8739d-509">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="8739d-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8739d-510">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="8739d-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8739d-511">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-511">Default configuration</span></span>

<span data-ttu-id="8739d-512">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8739d-513">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="8739d-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8739d-514">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8739d-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8739d-515">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8739d-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8739d-516">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8739d-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8739d-517">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8739d-518">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8739d-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8739d-519">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8739d-520">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8739d-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="8739d-521">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-521">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8739d-522">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8739d-523">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8739d-524">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8739d-525">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8739d-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8739d-526">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="8739d-526">Security</span></span>

<span data-ttu-id="8739d-527">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="8739d-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8739d-528">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="8739d-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8739d-529">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="8739d-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8739d-530">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="8739d-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8739d-531">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="8739d-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8739d-532"><xref:security/app-secrets>: Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="8739d-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8739d-533">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="8739d-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8739d-534">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8739d-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="8739d-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8739d-536">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8739d-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8739d-537">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="8739d-537">Hierarchical configuration data</span></span>

<span data-ttu-id="8739d-538">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="8739d-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8739d-539">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="8739d-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="8739d-540">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8739d-541">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="8739d-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8739d-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-542">section0:key0</span></span>
* <span data-ttu-id="8739d-543">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-543">section0:key1</span></span>
* <span data-ttu-id="8739d-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-544">section1:key0</span></span>
* <span data-ttu-id="8739d-545">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-545">section1:key1</span></span>

<span data-ttu-id="8739d-546">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8739d-547">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8739d-548">Konventionen</span><span class="sxs-lookup"><span data-stu-id="8739d-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8739d-549">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-549">Sources and providers</span></span>

<span data-ttu-id="8739d-550">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8739d-551">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8739d-552">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="8739d-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8739d-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8739d-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8739d-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller> von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="8739d-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8739d-555">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="8739d-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="8739d-556">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8739d-557">Tasten</span><span class="sxs-lookup"><span data-stu-id="8739d-557">Keys</span></span>

<span data-ttu-id="8739d-558">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="8739d-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8739d-559">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="8739d-559">Keys are case-insensitive.</span></span> <span data-ttu-id="8739d-560">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="8739d-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8739d-561">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="8739d-562">Weitere Informationen zu doppelten JSON-Schlüsseln finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="8739d-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="8739d-563">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-563">Hierarchical keys</span></span>
  * <span data-ttu-id="8739d-564">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8739d-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8739d-565">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8739d-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8739d-566">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="8739d-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8739d-567">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="8739d-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8739d-568">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8739d-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8739d-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739d-570">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8739d-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8739d-571">Werte</span><span class="sxs-lookup"><span data-stu-id="8739d-571">Values</span></span>

<span data-ttu-id="8739d-572">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="8739d-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8739d-573">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8739d-573">Values are strings.</span></span>
* <span data-ttu-id="8739d-574">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8739d-575">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-575">Providers</span></span>

<span data-ttu-id="8739d-576">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="8739d-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8739d-577">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-577">Provider</span></span> | <span data-ttu-id="8739d-578">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="8739d-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8739d-579">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="8739d-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8739d-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739d-580">Azure Key Vault</span></span> |
| <span data-ttu-id="8739d-581">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="8739d-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8739d-582">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8739d-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="8739d-583">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8739d-584">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="8739d-584">Command-line parameters</span></span> |
| [<span data-ttu-id="8739d-585">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8739d-586">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="8739d-586">Custom source</span></span> |
| [<span data-ttu-id="8739d-587">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8739d-588">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-588">Environment variables</span></span> |
| [<span data-ttu-id="8739d-589">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8739d-590">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="8739d-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8739d-591">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8739d-592">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8739d-592">Directory files</span></span> |
| [<span data-ttu-id="8739d-593">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8739d-594">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="8739d-594">In-memory collections</span></span> |
| <span data-ttu-id="8739d-595">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="8739d-595">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8739d-596">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8739d-596">File in the user profile directory</span></span> |

<span data-ttu-id="8739d-597">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8739d-598">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="8739d-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8739d-599">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="8739d-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8739d-600">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="8739d-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8739d-601">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="8739d-601">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8739d-602">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8739d-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8739d-603">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="8739d-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8739d-604">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8739d-604">Environment variables</span></span>
1. <span data-ttu-id="8739d-605">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8739d-605">Command-line arguments</span></span>

<span data-ttu-id="8739d-606">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="8739d-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8739d-607">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8739d-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739d-608">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8739d-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8739d-609">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8739d-610">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="8739d-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8739d-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8739d-612">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8739d-613">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8739d-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8739d-614">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="8739d-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8739d-615">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="8739d-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8739d-616">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="8739d-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8739d-617">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="8739d-617">Consume configuration during app startup</span></span>

<span data-ttu-id="8739d-618">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8739d-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8739d-619">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="8739d-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8739d-620">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="8739d-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="8739d-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-622">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8739d-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739d-623">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8739d-624">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8739d-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739d-625">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8739d-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739d-626">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-626">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8739d-627">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8739d-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739d-628">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8739d-628">Environment variables.</span></span>

<span data-ttu-id="8739d-629">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="8739d-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8739d-630">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8739d-631">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="8739d-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8739d-632">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="8739d-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8739d-633">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8739d-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739d-634">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8739d-635">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8739d-635">**Example**</span></span>

<span data-ttu-id="8739d-636">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="8739d-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8739d-637">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="8739d-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8739d-638">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="8739d-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8739d-639">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8739d-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739d-640">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="8739d-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8739d-641">Argumente</span><span class="sxs-lookup"><span data-stu-id="8739d-641">Arguments</span></span>

<span data-ttu-id="8739d-642">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="8739d-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8739d-643">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="8739d-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8739d-644">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="8739d-644">Key prefix</span></span>               | <span data-ttu-id="8739d-645">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8739d-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8739d-646">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="8739d-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8739d-647">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="8739d-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="8739d-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8739d-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8739d-649">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="8739d-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="8739d-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8739d-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8739d-651">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="8739d-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8739d-652">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="8739d-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8739d-653">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="8739d-653">Switch mappings</span></span>

<span data-ttu-id="8739d-654">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="8739d-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8739d-655">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="8739d-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8739d-656">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="8739d-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8739d-657">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8739d-658">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8739d-659">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="8739d-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8739d-660">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="8739d-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8739d-661">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="8739d-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8739d-662">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="8739d-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8739d-663">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="8739d-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8739d-664">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="8739d-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8739d-665">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8739d-666">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739d-667">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8739d-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8739d-668">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="8739d-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8739d-669">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-669">Key</span></span>       | <span data-ttu-id="8739d-670">Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8739d-671">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="8739d-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8739d-672">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="8739d-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8739d-673">Key</span><span class="sxs-lookup"><span data-stu-id="8739d-673">Key</span></span>               | <span data-ttu-id="8739d-674">Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8739d-675">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8739d-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8739d-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-677">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8739d-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="8739d-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8739d-679">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8739d-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8739d-680">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8739d-681">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8739d-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739d-682">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8739d-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739d-683">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="8739d-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8739d-684">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-684">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8739d-685">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8739d-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739d-686">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8739d-686">Command-line arguments.</span></span>

<span data-ttu-id="8739d-687">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="8739d-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8739d-688">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8739d-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8739d-689">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="8739d-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8739d-690">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8739d-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8739d-691">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8739d-691">**Example**</span></span>

<span data-ttu-id="8739d-692">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="8739d-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8739d-693">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-693">Run the sample app.</span></span> <span data-ttu-id="8739d-694">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8739d-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739d-695">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="8739d-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8739d-696">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="8739d-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8739d-697">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8739d-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8739d-698">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="8739d-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8739d-699">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="8739d-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8739d-700">Präfixe</span><span class="sxs-lookup"><span data-stu-id="8739d-700">Prefixes</span></span>

<span data-ttu-id="8739d-701">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8739d-702">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="8739d-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8739d-703">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8739d-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8739d-704">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8739d-705">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8739d-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739d-706">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="8739d-706">**Connection string prefixes**</span></span>

<span data-ttu-id="8739d-707">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8739d-708">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8739d-709">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="8739d-709">Connection string prefix</span></span> | <span data-ttu-id="8739d-710">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8739d-711">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8739d-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="8739d-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8739d-713">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="8739d-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8739d-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8739d-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8739d-715">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="8739d-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8739d-716">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8739d-717">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="8739d-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8739d-718">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-718">Environment variable key</span></span> | <span data-ttu-id="8739d-719">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-719">Converted configuration key</span></span> | <span data-ttu-id="8739d-720">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="8739d-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-721">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-722">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-723">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-724">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-725">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8739d-726">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8739d-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8739d-727">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8739d-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="8739d-728">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8739d-728">**Example**</span></span>

<span data-ttu-id="8739d-729">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="8739d-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8739d-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8739d-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8739d-731">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8739d-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8739d-732">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="8739d-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8739d-733">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-733">File Configuration Provider</span></span>

<span data-ttu-id="8739d-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="8739d-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8739d-735">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="8739d-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8739d-736">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8739d-737">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8739d-738">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8739d-739">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-739">INI Configuration Provider</span></span>

<span data-ttu-id="8739d-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-741">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739d-742">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8739d-743">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8739d-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739d-744">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8739d-744">Whether the file is optional.</span></span>
* <span data-ttu-id="8739d-745">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739d-746">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739d-747">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739d-748">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="8739d-748">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="8739d-749">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8739d-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739d-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-750">section0:key0</span></span>
* <span data-ttu-id="8739d-751">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-751">section0:key1</span></span>
* <span data-ttu-id="8739d-752">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8739d-752">section1:subsection:key</span></span>
* <span data-ttu-id="8739d-753">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8739d-753">section2:subsection0:key</span></span>
* <span data-ttu-id="8739d-754">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8739d-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8739d-755">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-755">JSON Configuration Provider</span></span>

<span data-ttu-id="8739d-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8739d-757">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739d-758">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8739d-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739d-759">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8739d-759">Whether the file is optional.</span></span>
* <span data-ttu-id="8739d-760">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739d-761">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739d-762">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8739d-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8739d-763">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="8739d-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8739d-764">*appsettings.json*: Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="8739d-764">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="8739d-765">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8739d-765">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8739d-766">*appsettings.{Environment}.json*: Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="8739d-766">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8739d-767">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8739d-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8739d-768">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8739d-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8739d-769">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8739d-769">Environment variables.</span></span>
* <span data-ttu-id="8739d-770">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8739d-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8739d-771">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8739d-771">Command-line arguments.</span></span>

<span data-ttu-id="8739d-772">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="8739d-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8739d-773">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8739d-774">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739d-775">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8739d-775">**Example**</span></span>

<span data-ttu-id="8739d-776">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="8739d-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8739d-777">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-777">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8739d-778">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8739d-779">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="8739d-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8739d-780">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-780">Run the sample app.</span></span> <span data-ttu-id="8739d-781">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8739d-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8739d-782">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="8739d-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8739d-783">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8739d-784">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="8739d-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8739d-785">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8739d-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8739d-786">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="8739d-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8739d-787">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8739d-788">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="8739d-788">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8739d-789">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="8739d-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8739d-790">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-790">XML Configuration Provider</span></span>

<span data-ttu-id="8739d-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8739d-792">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739d-793">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8739d-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739d-794">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8739d-794">Whether the file is optional.</span></span>
* <span data-ttu-id="8739d-795">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8739d-796">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8739d-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8739d-797">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="8739d-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8739d-798">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="8739d-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8739d-799">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8739d-800">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="8739d-800">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="8739d-801">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8739d-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739d-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-802">section0:key0</span></span>
* <span data-ttu-id="8739d-803">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-803">section0:key1</span></span>
* <span data-ttu-id="8739d-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-804">section1:key0</span></span>
* <span data-ttu-id="8739d-805">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-805">section1:key1</span></span>

<span data-ttu-id="8739d-806">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8739d-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="8739d-807">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8739d-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739d-808">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-808">section:section0:key:key0</span></span>
* <span data-ttu-id="8739d-809">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-809">section:section0:key:key1</span></span>
* <span data-ttu-id="8739d-810">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-810">section:section1:key:key0</span></span>
* <span data-ttu-id="8739d-811">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-811">section:section1:key:key1</span></span>

<span data-ttu-id="8739d-812">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8739d-813">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8739d-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8739d-814">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739d-814">key:attribute</span></span>
* <span data-ttu-id="8739d-815">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8739d-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8739d-816">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8739d-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8739d-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8739d-818">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="8739d-818">The key is the file name.</span></span> <span data-ttu-id="8739d-819">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="8739d-819">The value contains the file's contents.</span></span> <span data-ttu-id="8739d-820">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8739d-821">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8739d-822">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="8739d-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8739d-823">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8739d-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="8739d-824">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="8739d-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8739d-825">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="8739d-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8739d-826">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8739d-827">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8739d-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8739d-828">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8739d-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8739d-829">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-829">Memory Configuration Provider</span></span>

<span data-ttu-id="8739d-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8739d-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8739d-831">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8739d-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8739d-832">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8739d-833">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8739d-834">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="8739d-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8739d-835">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="8739d-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8739d-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="8739d-836">GetValue</span></span>

<span data-ttu-id="8739d-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Sammlungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8739d-838">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="8739d-838">An overload accepts a default value.</span></span>

<span data-ttu-id="8739d-839">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8739d-839">The following example:</span></span>

* <span data-ttu-id="8739d-840">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="8739d-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8739d-841">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8739d-842">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8739d-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="8739d-843">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="8739d-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8739d-844">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="8739d-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8739d-845">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="8739d-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8739d-846">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="8739d-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="8739d-847">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="8739d-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8739d-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-848">section0:key0</span></span>
* <span data-ttu-id="8739d-849">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-849">section0:key1</span></span>
* <span data-ttu-id="8739d-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-850">section1:key0</span></span>
* <span data-ttu-id="8739d-851">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-851">section1:key1</span></span>
* <span data-ttu-id="8739d-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="8739d-853">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="8739d-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8739d-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="8739d-855">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8739d-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8739d-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="8739d-856">GetSection</span></span>

<span data-ttu-id="8739d-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8739d-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8739d-858">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="8739d-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8739d-859">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="8739d-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8739d-860">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="8739d-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8739d-861">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="8739d-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8739d-862">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8739d-863">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="8739d-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8739d-864">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8739d-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8739d-865">GetChildren</span></span>

<span data-ttu-id="8739d-866">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="8739d-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8739d-867">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="8739d-867">Exists</span></span>

<span data-ttu-id="8739d-868">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="8739d-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8739d-869">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8739d-870">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="8739d-870">Bind to an object graph</span></span>

<span data-ttu-id="8739d-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="8739d-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8739d-872">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="8739d-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8739d-873">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="8739d-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8739d-874">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="8739d-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8739d-875">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="8739d-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8739d-876">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="8739d-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8739d-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="8739d-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8739d-878">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8739d-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8739d-879">Der folgende Code zeigt, wie `Get<T>` in Bezug auf das vorherige Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8739d-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8739d-880">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="8739d-880">Bind an array to a class</span></span>

<span data-ttu-id="8739d-881">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="8739d-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8739d-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8739d-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8739d-883">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="8739d-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8739d-884">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="8739d-884">Binding is provided by convention.</span></span> <span data-ttu-id="8739d-885">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="8739d-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8739d-886">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="8739d-886">**In-memory array processing**</span></span>

<span data-ttu-id="8739d-887">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="8739d-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8739d-888">Key</span><span class="sxs-lookup"><span data-stu-id="8739d-888">Key</span></span>             | <span data-ttu-id="8739d-889">Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8739d-890">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="8739d-890">array:entries:0</span></span> | <span data-ttu-id="8739d-891">value0</span><span class="sxs-lookup"><span data-stu-id="8739d-891">value0</span></span> |
| <span data-ttu-id="8739d-892">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="8739d-892">array:entries:1</span></span> | <span data-ttu-id="8739d-893">value1</span><span class="sxs-lookup"><span data-stu-id="8739d-893">value1</span></span> |
| <span data-ttu-id="8739d-894">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="8739d-894">array:entries:2</span></span> | <span data-ttu-id="8739d-895">value2</span><span class="sxs-lookup"><span data-stu-id="8739d-895">value2</span></span> |
| <span data-ttu-id="8739d-896">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="8739d-896">array:entries:4</span></span> | <span data-ttu-id="8739d-897">value4</span><span class="sxs-lookup"><span data-stu-id="8739d-897">value4</span></span> |
| <span data-ttu-id="8739d-898">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="8739d-898">array:entries:5</span></span> | <span data-ttu-id="8739d-899">value5</span><span class="sxs-lookup"><span data-stu-id="8739d-899">value5</span></span> |

<span data-ttu-id="8739d-900">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="8739d-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8739d-901">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="8739d-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8739d-902">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="8739d-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8739d-903">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="8739d-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8739d-904">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="8739d-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8739d-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann ebenfalls verwendet werden, was zu kompakteren Code führt:</span><span class="sxs-lookup"><span data-stu-id="8739d-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8739d-906">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8739d-907">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="8739d-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8739d-908">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8739d-909">0</span><span class="sxs-lookup"><span data-stu-id="8739d-909">0</span></span>                            | <span data-ttu-id="8739d-910">value0</span><span class="sxs-lookup"><span data-stu-id="8739d-910">value0</span></span>                       |
| <span data-ttu-id="8739d-911">1</span><span class="sxs-lookup"><span data-stu-id="8739d-911">1</span></span>                            | <span data-ttu-id="8739d-912">value1</span><span class="sxs-lookup"><span data-stu-id="8739d-912">value1</span></span>                       |
| <span data-ttu-id="8739d-913">2</span><span class="sxs-lookup"><span data-stu-id="8739d-913">2</span></span>                            | <span data-ttu-id="8739d-914">value2</span><span class="sxs-lookup"><span data-stu-id="8739d-914">value2</span></span>                       |
| <span data-ttu-id="8739d-915">3</span><span class="sxs-lookup"><span data-stu-id="8739d-915">3</span></span>                            | <span data-ttu-id="8739d-916">value4</span><span class="sxs-lookup"><span data-stu-id="8739d-916">value4</span></span>                       |
| <span data-ttu-id="8739d-917">4</span><span class="sxs-lookup"><span data-stu-id="8739d-917">4</span></span>                            | <span data-ttu-id="8739d-918">value5</span><span class="sxs-lookup"><span data-stu-id="8739d-918">value5</span></span>                       |

<span data-ttu-id="8739d-919">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="8739d-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8739d-920">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8739d-921">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="8739d-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8739d-922">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="8739d-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8739d-923">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="8739d-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8739d-924">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8739d-924">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8739d-925">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8739d-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8739d-926">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="8739d-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8739d-927">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="8739d-927">Key</span></span>             | <span data-ttu-id="8739d-928">Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8739d-929">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8739d-929">array:entries:3</span></span> | <span data-ttu-id="8739d-930">value3</span><span class="sxs-lookup"><span data-stu-id="8739d-930">value3</span></span> |

<span data-ttu-id="8739d-931">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="8739d-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8739d-932">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="8739d-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8739d-933">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8739d-934">0</span><span class="sxs-lookup"><span data-stu-id="8739d-934">0</span></span>                            | <span data-ttu-id="8739d-935">value0</span><span class="sxs-lookup"><span data-stu-id="8739d-935">value0</span></span>                       |
| <span data-ttu-id="8739d-936">1</span><span class="sxs-lookup"><span data-stu-id="8739d-936">1</span></span>                            | <span data-ttu-id="8739d-937">value1</span><span class="sxs-lookup"><span data-stu-id="8739d-937">value1</span></span>                       |
| <span data-ttu-id="8739d-938">2</span><span class="sxs-lookup"><span data-stu-id="8739d-938">2</span></span>                            | <span data-ttu-id="8739d-939">value2</span><span class="sxs-lookup"><span data-stu-id="8739d-939">value2</span></span>                       |
| <span data-ttu-id="8739d-940">3</span><span class="sxs-lookup"><span data-stu-id="8739d-940">3</span></span>                            | <span data-ttu-id="8739d-941">value3</span><span class="sxs-lookup"><span data-stu-id="8739d-941">value3</span></span>                       |
| <span data-ttu-id="8739d-942">4</span><span class="sxs-lookup"><span data-stu-id="8739d-942">4</span></span>                            | <span data-ttu-id="8739d-943">value4</span><span class="sxs-lookup"><span data-stu-id="8739d-943">value4</span></span>                       |
| <span data-ttu-id="8739d-944">5</span><span class="sxs-lookup"><span data-stu-id="8739d-944">5</span></span>                            | <span data-ttu-id="8739d-945">value5</span><span class="sxs-lookup"><span data-stu-id="8739d-945">value5</span></span>                       |

<span data-ttu-id="8739d-946">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="8739d-946">**JSON array processing**</span></span>

<span data-ttu-id="8739d-947">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="8739d-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8739d-948">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="8739d-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8739d-949">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="8739d-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8739d-950">Key</span><span class="sxs-lookup"><span data-stu-id="8739d-950">Key</span></span>                     | <span data-ttu-id="8739d-951">Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8739d-952">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8739d-952">json_array:key</span></span>          | <span data-ttu-id="8739d-953">valueA</span><span class="sxs-lookup"><span data-stu-id="8739d-953">valueA</span></span> |
| <span data-ttu-id="8739d-954">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8739d-954">json_array:subsection:0</span></span> | <span data-ttu-id="8739d-955">valueB</span><span class="sxs-lookup"><span data-stu-id="8739d-955">valueB</span></span> |
| <span data-ttu-id="8739d-956">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8739d-956">json_array:subsection:1</span></span> | <span data-ttu-id="8739d-957">valueC</span><span class="sxs-lookup"><span data-stu-id="8739d-957">valueC</span></span> |
| <span data-ttu-id="8739d-958">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8739d-958">json_array:subsection:2</span></span> | <span data-ttu-id="8739d-959">valueD</span><span class="sxs-lookup"><span data-stu-id="8739d-959">valueD</span></span> |

<span data-ttu-id="8739d-960">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="8739d-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8739d-961">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="8739d-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8739d-962">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="8739d-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8739d-963">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="8739d-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8739d-964">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="8739d-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8739d-965">0</span><span class="sxs-lookup"><span data-stu-id="8739d-965">0</span></span>                                   | <span data-ttu-id="8739d-966">valueB</span><span class="sxs-lookup"><span data-stu-id="8739d-966">valueB</span></span>                              |
| <span data-ttu-id="8739d-967">1</span><span class="sxs-lookup"><span data-stu-id="8739d-967">1</span></span>                                   | <span data-ttu-id="8739d-968">valueC</span><span class="sxs-lookup"><span data-stu-id="8739d-968">valueC</span></span>                              |
| <span data-ttu-id="8739d-969">2</span><span class="sxs-lookup"><span data-stu-id="8739d-969">2</span></span>                                   | <span data-ttu-id="8739d-970">valueD</span><span class="sxs-lookup"><span data-stu-id="8739d-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8739d-971">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8739d-971">Custom configuration provider</span></span>

<span data-ttu-id="8739d-972">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="8739d-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8739d-973">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="8739d-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8739d-974">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="8739d-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8739d-975">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8739d-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8739d-976">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8739d-977">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="8739d-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8739d-978">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8739d-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8739d-979">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8739d-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8739d-980">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-980">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8739d-981">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8739d-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8739d-982">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-982">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8739d-983">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="8739d-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8739d-984">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-984">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8739d-985">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8739d-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8739d-986">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="8739d-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8739d-987">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-987">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8739d-988">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8739d-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8739d-989">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-989">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8739d-990">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8739d-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8739d-991">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="8739d-991">Access configuration during startup</span></span>

<span data-ttu-id="8739d-992">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="8739d-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8739d-993">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8739d-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="8739d-994">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8739d-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="8739d-995">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="8739d-995">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8739d-996">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="8739d-996">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8739d-997">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="8739d-997">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="8739d-998">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="8739d-998">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8739d-999">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="8739d-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="8739d-1000">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="8739d-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8739d-1001">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8739d-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8739d-1002">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8739d-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
