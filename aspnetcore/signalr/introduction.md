---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie die ASP.net Core SignalR Bibliothek das Hinzufügen von Echtzeitfunktionen zu apps vereinfacht.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022172"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="b4adc-103">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="b4adc-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="b4adc-104">Was ist SignalR?</span><span class="sxs-lookup"><span data-stu-id="b4adc-104">What is SignalR?</span></span>

<span data-ttu-id="b4adc-105">ASP.net Core SignalR ist eine Open-Source-Bibliothek, die das Hinzufügen von Echt Zeit Webfunktionen zu apps vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="b4adc-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="b4adc-106">Die Echt Zeit webolle ermöglicht es dem serverseitigen Code, Inhalte sofort per Push an Clients zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="b4adc-107">Gute Kandidaten für SignalR :</span><span class="sxs-lookup"><span data-stu-id="b4adc-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="b4adc-108">Apps, für die eine hohe Frequenz von Updates vom Server benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="b4adc-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="b4adc-109">Beispiele hierfür sind Apps aus den Bereichen Gaming, soziale Netzwerke, Voting, Auktionen, Karten und GPS.</span><span class="sxs-lookup"><span data-stu-id="b4adc-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="b4adc-110">Dashboards und Überwachungs-Apps.</span><span class="sxs-lookup"><span data-stu-id="b4adc-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="b4adc-111">Beispiele hierfür sind Unternehmensdashboards, Sofortupdates von Verkaufszahlen oder Reisehinweise.</span><span class="sxs-lookup"><span data-stu-id="b4adc-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="b4adc-112">Apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="b4adc-112">Collaborative apps.</span></span> <span data-ttu-id="b4adc-113">Whiteboard-Apps und Software für Teambesprechungen sind Beispiele für Apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="b4adc-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="b4adc-114">Apps, für die Benachrichtigungen benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="b4adc-114">Apps that require notifications.</span></span> <span data-ttu-id="b4adc-115">Soziale Netzwerke, E-Mail, Chat, Games, Reisehinweise und viele andere Arten von Apps nutzen Benachrichtigungen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="b4adc-116">SignalRstellt eine API zum Erstellen von [Remote Prozedur aufrufen (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)für Server-zu-Client bereit.</span><span class="sxs-lookup"><span data-stu-id="b4adc-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="b4adc-117">Die RPCs wenden JavaScript-Funktionen auf Clients aus dem serverseitigen .net Core-Code an.</span><span class="sxs-lookup"><span data-stu-id="b4adc-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="b4adc-118">Im folgenden sind einige Features von SignalR für ASP.net Core aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="b4adc-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="b4adc-119">Behandelt die Verbindungs Verwaltung automatisch.</span><span class="sxs-lookup"><span data-stu-id="b4adc-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="b4adc-120">Sendet Nachrichten gleichzeitig an alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="b4adc-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="b4adc-121">Beispielsweise ein Chatraum.</span><span class="sxs-lookup"><span data-stu-id="b4adc-121">For example, a chat room.</span></span>
* <span data-ttu-id="b4adc-122">Sendet Nachrichten an bestimmte Clients oder Client Gruppen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="b4adc-123">Skaliert, um zunehmenden Datenverkehr zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b4adc-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="b4adc-124">Die Quelle wird in einem [ SignalR Repository auf GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)gehostet.</span><span class="sxs-lookup"><span data-stu-id="b4adc-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="b4adc-125">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="b4adc-125">Transports</span></span>

<span data-ttu-id="b4adc-126">SignalRunterstützt die folgenden Verfahren für die Verarbeitung der Echtzeitkommunikation (in der Reihenfolge des ordnungsgemäßen Fallbacks):</span><span class="sxs-lookup"><span data-stu-id="b4adc-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="b4adc-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="b4adc-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="b4adc-128">Vom Server gesendete Ereignisse</span><span class="sxs-lookup"><span data-stu-id="b4adc-128">Server-Sent Events</span></span>
* <span data-ttu-id="b4adc-129">Langer Abruf</span><span class="sxs-lookup"><span data-stu-id="b4adc-129">Long Polling</span></span>

<span data-ttu-id="b4adc-130">SignalRwählt automatisch die beste Transportmethode aus, die sich innerhalb der Funktionen des Servers und des Clients befindet.</span><span class="sxs-lookup"><span data-stu-id="b4adc-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="b4adc-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="b4adc-131">Hubs</span></span>

<span data-ttu-id="b4adc-132">SignalRverwendet *Hubs* für die Kommunikation zwischen Clients und Servern.</span><span class="sxs-lookup"><span data-stu-id="b4adc-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="b4adc-133">Ein Hub ist eine Pipeline auf hoher Ebene, die es einem Client und einem Server ermöglicht, Methoden untereinander aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="b4adc-134">SignalRverarbeitet die Verteilung über die Computer Grenzen hinweg automatisch, sodass Clients Methoden auf dem Server aufrufen können und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="b4adc-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="b4adc-135">Sie können stark typisierte Parameter an Methoden übergeben, die die Modell Bindung ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="b4adc-136">SignalRstellt zwei integrierte Hub-Protokolle bereit: ein auf JSON basierendes Text Protokoll und ein auf [messagepack](https://msgpack.org/)basierendes binäres Protokoll.</span><span class="sxs-lookup"><span data-stu-id="b4adc-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="b4adc-137">Im Allgemeinen erstellt messagepack kleinere Nachrichten im Vergleich zu JSON.</span><span class="sxs-lookup"><span data-stu-id="b4adc-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="b4adc-138">Ältere Browser müssen [XHR Level 2](https://caniuse.com/#feat=xhr2) unterstützen, um Unterstützung für das messagepack-Protokoll bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="b4adc-139">Hubs wenden Client seitigen Code an, indem Sie Nachrichten senden, die den Namen und die Parameter der Client seitigen Methode enthalten.</span><span class="sxs-lookup"><span data-stu-id="b4adc-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="b4adc-140">Objekte, die als Methoden Parameter gesendet werden, werden mithilfe des konfigurierten Protokolls deserialisiert.</span><span class="sxs-lookup"><span data-stu-id="b4adc-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="b4adc-141">Der Client versucht, den Namen einer Methode im Client seitigen Code zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="b4adc-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="b4adc-142">Wenn der Client eine Entsprechung findet, ruft er die-Methode auf und übergibt die deserialisierten Parameterdaten an ihn.</span><span class="sxs-lookup"><span data-stu-id="b4adc-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4adc-143">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b4adc-143">Additional resources</span></span>

* [<span data-ttu-id="b4adc-144">Beginnen Sie mit der SignalR ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b4adc-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b4adc-145">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="b4adc-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="b4adc-146">Hub</span><span class="sxs-lookup"><span data-stu-id="b4adc-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b4adc-147">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="b4adc-147">JavaScript client</span></span>](xref:signalr/javascript-client)
