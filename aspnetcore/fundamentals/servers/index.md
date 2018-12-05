---
title: Webserverimplementierungen in ASP.NET Core
author: guardrex
description: Ermitteln Sie die Webserver Kestrel und HTTP.sys für ASP.NET Core. Erfahren Sie mehr über das Auswählen eines Servers und darüber, wann ein Reverseproxyserver zu verwenden ist.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: fundamentals/servers/index
ms.openlocfilehash: 965d69dd071ec71d283284d58e6e1a6e78604f90
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861355"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Webserverimplementierungen in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) und [Chris Ross](https://github.com/Tratcher)

Eine ASP.NET Core-App wird über eine In-Process-Implementierung eines HTTP-Servers ausgeführt. Die Serverimplementierung lauscht auf HTTP-Anforderungen und leitet diese als Gruppen von [Anforderungsfunktionen](xref:fundamentals/request-features), die in einem <xref:Microsoft.AspNetCore.Http.HttpContext> zusammengefasst werden, an die App weiter.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core wird mit folgendem Umfang ausgeliefert:

* [Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.
* IIS-HTTP-Server (`IISHttpServer`) ist eine [In-Process-IIS-Server](xref:fundamentals/servers/aspnet-core-module#in-process-hosting-model)-Implementierung, die mit dem [ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module) verwendet wird.
* [HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) basiert. HTTP.sys wird in ASP.NET Core 1.x als [WebListener](xref:fundamentals/servers/weblistener) bezeichnet.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core wird mit folgendem Umfang ausgeliefert:

* [Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.
* [HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) basiert. HTTP.sys wird in ASP.NET Core 1.x als [WebListener](xref:fundamentals/servers/weblistener) bezeichnet.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

Kestrel ist der Standardwebserver, der in ASP.NET Core-Projektvorlagen enthalten ist.

::: moniker range=">= aspnetcore-2.0"

Kestrel eignet sich für folgende Zwecke:

* Eigenständig als Edge-Server zur Verarbeitung von Anforderungen, die direkt aus einem Netzwerk, auch über das Internet, kommen.
* Mit einem *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/). Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

Jede der beiden Konfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; ist eine gültige und unterstützte Hostingkonfiguration für ASP.NET Core 2.0 oder neuere Apps. Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenn die App nur Anforderungen aus einem internen Netzwerk akzeptiert, kann Kestrel eigenständig verwendet werden.

![Kestrel kommuniziert direkt mit dem internen Netzwerk.](kestrel/_static/kestrel-to-internal.png)

Wenn die App für das Internet verfügbar gemacht ist, muss Kestrel einen *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) verwenden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

Der wichtigste Grund für die Verwendung eines Reverseproxys für öffentlich zugängliche Edge-Server-Bereitstellungen, die direkt im Internet verfügbar gemacht werden, ist die Sicherheit. Die 1.x-Versionen von Kestrel enthalten keine wesentlichen Sicherheitsfeatures zum Schutz vor Angriffen aus dem Internet. So sind z. B. keine geeigneten Timeouts, Größenlimits für Anforderungen sowie Beschränkungen der Anzahl gleichzeitiger Verbindungen vorhanden.

Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).

::: moniker-end

### <a name="iis-with-kestrel"></a>IIS und Kestrel

::: moniker range=">= aspnetcore-2.2"

Bei Verwendung von [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) wird die ASP.NET Core-App entweder im selben Prozess wie der IIS-Workerprozess ausgeführt (das *In-Process*-Hostingmodell) oder in einem vom IIS-Workerprozess getrennten Prozess (das *Out-of-Process*-Hostingmodell).

Das [ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module) ist ein natives IIS-Modul, das native IIS-Anforderungen zwischen dem In-Process-IIS-HTTP-Server oder dem Out-of-Process-Kestrel-Server verarbeitet. Weitere Informationen finden Sie unter <xref:fundamentals/servers/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Wenn [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) als Reverseproxy für ASP.NET Core verwendet wird, wird die ASP.NET Core-App in einem Prozess ausgeführt, der vom IIS-Workerprozess getrennt ist. Im IIS-Prozess steuert das [ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module) die Beziehung zum Reverseproxy. Die Hauptfunktionen des ASP.NET Core-Moduls bestehen darin, die App zu starten, sie nach einem Absturz neu zu starten und HTTP-Datenverkehr an die App weiterzuleiten. Weitere Informationen finden Sie unter <xref:fundamentals/servers/aspnet-core-module>.

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx und Kestrel

Informationen dazu, wie Nginx unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache und Kestrel

Informationen dazu, wie Apache unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

::: moniker range=">= aspnetcore-2.0"

Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist HTTP.sys eine Alternative zu Kestrel. Grundsätzlich empfiehlt sich Kestrel, um die beste Leistung zu erzielen. HTTP.sys kann in Szenarien verwendet werden, in denen die App mit dem Internet verbunden ist und erforderliche Funktionen von HTTP.sys, aber nicht von Kestrel unterstützt werden. Weitere Informationen finden Sie unter <xref:fundamentals/servers/httpsys>.

![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

Außerdem kann HTTP.sys auch bei Apss zum Einsatz kommen, die nur in einem internen Netzwerk verfügbar gemacht werden.

![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

HTTP.sys wird in ASP.NET Core 1.x [WebListener](xref:fundamentals/servers/weblistener) genannt. Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist WebListener eine Alternative für Szenarien, in denen Apps nicht von IIS gehostet werden können.

![WebListener kommuniziert direkt mit dem Internet](weblistener/_static/weblistener-to-internet.png)

WebListener kann auch anstelle von Kestrel für Apps verwendet werden, die nur in einem internen Netzwerk bereitgestellt werden, wenn erforderliche Funktionen durch WebListener, aber nicht durch Kestrel unterstützt werden. Informationen zu WebListener finden Sie unter [WebListener](xref:fundamentals/servers/weblistener).

![WebListener kommuniziert direkt mit dem internen Netzwerk](weblistener/_static/weblistener-to-internal.png)

::: moniker-end

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core-Serverinfrastruktur

Die [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder)-Schnittstelle, sie in der `Startup.Configure`-Methode verfügbar ist, macht die [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures)-Eigenschaft des Typs [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection) verfügbar. Kestrel und HTTP.sys (WebListener in ASP.NET Core 1.x) machen nur jeweils eine einzelne Funktion, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), verfügbar, andere Serverimplementierungen machen jedoch möglicherweise weitere Funktionalität verfügbar.

Mit `IServerAddressesFeature` kann ermittelt werden, an welchen Port die Serverimplementierung zur Laufzeit gebunden ist.

## <a name="custom-servers"></a>Benutzerdefinierte Server

Wenn die integrierten Server nicht den Anforderungen der App entsprechen, kann eine benutzerdefinierte Serverimplementierung erstellt werden. In der [Anleitung zu Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) wird erläutert, wie eine auf [Nowin](https://github.com/Bobris/Nowin) basierende [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver)-Implementierung erstellt wird. Nur die Feature-Schnittstellen, die von der App verwendet werden, erfordern Implementierung , wobei mindestens [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) und [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) unterstützt werden müssen.

## <a name="server-startup"></a>Serverstart

Wenn [Visual Studio](https://www.visualstudio.com/vs/), [Visual Studio für Mac](https://www.visualstudio.com/vs/mac/) oder [Visual Studio Code](https://code.visualstudio.com/) verwendet wird, wird der Server gestartet, wenn die App durch die integrierte Entwicklungsumgebung gestartet wird. In Visual Studio unter Windows können Startprofile verwendet werden, um die App und den Server mit [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core-Modul](xref:fundamentals/servers/aspnet-core-module) oder mit der Konsole zu starten. In Visual Studio Code werden die App und der Server durch [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) gestartet, das den CoreCLR-Debugger aktiviert. Wird Visual Studio für Mac verwendet, werden die App und der Server durch den [Mono Soft-Mode Debugger](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) gestartet.

Wird eine App über eine Eingabeaufforderung im Ordner des Projekts gestartet, startet [dotnet run](/dotnet/core/tools/dotnet-run) die App und den Server (nur Kestrel und HTTP.sys). Die Konfiguration wird durch die Option `-c|--configuration` angegeben, die entweder auf `Debug` (Standardwert) oder `Release` festgelegt ist. Sind in der Datei *launchSettings.json* Startprofile vorhanden, verwenden Sie die Option `--launch-profile <NAME>`, um das Startprofil festzulegen (z. B. `Development` oder `Production`). Weitere Informationen hierzu finden Sie in den Themen [dotnet run](/dotnet/core/tools/dotnet-run) und [Verpacken einer Verteilung von .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden Bereitstellungsszenarien unterstützt:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Betriebssystem
    * Windows Server 2016/Windows 10 oder höher&dagger;
    * Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)
    * HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.
  * Zielframework: .NET Core 2.2 oder höher
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 oder höher
  * Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.
* [IIS (In-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Zielframework: .NET Core 2.2 oder höher
* [IIS (Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.
  * Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.

&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1. Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist. Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 oder höher
  * Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.
* [IIS (Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.
  * Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.

::: moniker-end

Eine HTTP/2-Verbindung muss [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) und TLS 1.2 oder höher verwenden. Weitere Informationen finden Sie in den Themen, die Ihre Serverbereitstellungsszenarien betreffen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/servers/kestrel>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys> (für ASP.NET Core 1.x siehe<xref:fundamentals/servers/weblistener>)
