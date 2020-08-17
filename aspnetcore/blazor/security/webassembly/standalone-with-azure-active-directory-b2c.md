---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 300da88bfba5baaeee646111bf3fe50caad5d60e
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130365"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="77dde-102">Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="77dde-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="77dde-103">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77dde-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77dde-104">Für das Erstellen einer [eigenständigen Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly), die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="77dde-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="77dde-105">Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="77dde-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="77dde-106">Erstellen eines AAD B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="77dde-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="77dde-107">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="77dde-107">Record the following information:</span></span>

* <span data-ttu-id="77dde-108">AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist): Die Instanz ist das Schema und der Host einer Azure-B2B-App-Registrierung, die Sie im Fenster **Endpunkte** auf der Seite **App-Registrierungen** im Azure-Portal finden.</span><span class="sxs-lookup"><span data-stu-id="77dde-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="77dde-109">Primäre, Herausgeber- oder Mandantendomäne für AAD B2C (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="77dde-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="77dde-110">Befolgen Sie die Anweisungen unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) noch mal, um eine AAD-App für die *Client-App* zu registrieren, und gehen Sie dann wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="77dde-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="77dde-111">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="77dde-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="77dde-112">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="77dde-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="77dde-113">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="77dde-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="77dde-114">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="77dde-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="77dde-115">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="77dde-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="77dde-116">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="77dde-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="77dde-117">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="77dde-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="77dde-118">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="77dde-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="77dde-119">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="77dde-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="77dde-120">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="77dde-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="77dde-121">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="77dde-121">Select **Register**.</span></span>

<span data-ttu-id="77dde-122">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="77dde-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="77dde-123">Gehen Sie unter **Authentifizierung** > **Plattformkonfigurationen** > **Web** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="77dde-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="77dde-124">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="77dde-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="77dde-125">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="77dde-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="77dde-126">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="77dde-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="77dde-127">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="77dde-127">Select the **Save** button.</span></span>

<span data-ttu-id="77dde-128">Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="77dde-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="77dde-129">Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="77dde-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="77dde-130">Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="77dde-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="77dde-131">Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="77dde-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="77dde-132">Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="77dde-132">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="77dde-133">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="77dde-133">Placeholder</span></span>                   | <span data-ttu-id="77dde-134">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="77dde-134">Azure portal name</span></span>               | <span data-ttu-id="77dde-135">Beispiel</span><span class="sxs-lookup"><span data-stu-id="77dde-135">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="77dde-136">Instanz</span><span class="sxs-lookup"><span data-stu-id="77dde-136">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="77dde-137">Anwendungs-ID (Client)</span><span class="sxs-lookup"><span data-stu-id="77dde-137">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="77dde-138">Benutzerflow für die Registrierung oder Anmeldung</span><span class="sxs-lookup"><span data-stu-id="77dde-138">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="77dde-139">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="77dde-139">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="77dde-140">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="77dde-140">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="77dde-141">Im Azure-Portal ist der **Authentifizierung** > **Plattformkonfigurationen** > **Web** > **Umleitungs-URI** der App für Apps für Port 5001 konfiguriert, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="77dde-141">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="77dde-142">Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="77dde-142">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="77dde-143">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="77dde-143">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="77dde-144">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:</span><span class="sxs-lookup"><span data-stu-id="77dde-144">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="77dde-145">Anmelden bei der App mit einem AAD-Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="77dde-145">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="77dde-146">Anfordern von Zugriffstoken für Microsoft-APIs</span><span class="sxs-lookup"><span data-stu-id="77dde-146">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="77dde-147">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="77dde-147">For more information, see:</span></span>
  * [<span data-ttu-id="77dde-148">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="77dde-148">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="77dde-149">[Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="77dde-149">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="77dde-150">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="77dde-150">Authentication package</span></span>

<span data-ttu-id="77dde-151">Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="77dde-151">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="77dde-152">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="77dde-152">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="77dde-153">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="77dde-153">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="77dde-154">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="77dde-154">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="77dde-155">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="77dde-155">Authentication service support</span></span>

<span data-ttu-id="77dde-156">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="77dde-156">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="77dde-157">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="77dde-157">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="77dde-158">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="77dde-158">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="77dde-159">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="77dde-159">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="77dde-160">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="77dde-160">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="77dde-161">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="77dde-161">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="77dde-162">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="77dde-162">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="77dde-163">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="77dde-163">Access token scopes</span></span>

<span data-ttu-id="77dde-164">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="77dde-164">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="77dde-165">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="77dde-165">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="77dde-166">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="77dde-166">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="77dde-167">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="77dde-167">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="77dde-168">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="77dde-168">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="77dde-169">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="77dde-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="77dde-170">Indexseite</span><span class="sxs-lookup"><span data-stu-id="77dde-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="77dde-171">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="77dde-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="77dde-172">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="77dde-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="77dde-173">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="77dde-173">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="77dde-174">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="77dde-174">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="77dde-175">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="77dde-175">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="77dde-176">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="77dde-176">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="77dde-177">Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="77dde-177">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="77dde-178">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="77dde-178">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
