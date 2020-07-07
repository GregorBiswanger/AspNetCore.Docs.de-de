---
title: ASP.NET Core Blazor-Lebenszyklus
author: guardrex
description: Erfahren Sie, wie die Lebenszyklusmethoden von Razor-Komponenten in ASP.NET Core Blazor-Apps verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 312a265dd251eadf876b4252e3d9f9858adcde1b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400985"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="3d1fc-103">ASP.NET Core Blazor-Lebenszyklus</span><span class="sxs-lookup"><span data-stu-id="3d1fc-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="3d1fc-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3d1fc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3d1fc-105">Das Blazor-Framework umfasst synchrone und asynchrone Lebenszyklusmethoden.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="3d1fc-106">Setzen Sie Lebenszyklusmethoden außer Kraft, um während der Komponenteninitialisierung und des Renderings zusätzliche Vorgänge mit Komponenten durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="3d1fc-107">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="3d1fc-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="3d1fc-108">Bevor die Parameter festgelegt werden</span><span class="sxs-lookup"><span data-stu-id="3d1fc-108">Before parameters are set</span></span>

<span data-ttu-id="3d1fc-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt Parameter fest, die vom übergeordneten Element der Komponente in der Renderstruktur bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="3d1fc-110"><xref:Microsoft.AspNetCore.Components.ParameterView> enthält bei jedem Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> den gesamten Satz von Parameterwerten.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="3d1fc-111">Die Standardimplementierung von <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> legt den Wert der einzelnen Eigenschaften mit dem Attribut [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) oder [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) fest, die einen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> aufweist.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="3d1fc-112">Parameter, die keinen entsprechenden Wert in <xref:Microsoft.AspNetCore.Components.ParameterView> haben, bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="3d1fc-113">Wenn [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) nicht aufgerufen wird, kann der benutzerdefinierte Code den eingehenden Parameterwert in jeder gewünschten Weise interpretieren.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="3d1fc-114">Beispielsweise ist es nicht erforderlich, die eingehenden Parameter den Eigenschaften der Klasse zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="3d1fc-115">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="3d1fc-116">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="3d1fc-117">Methoden zur Komponenteninitialisierung</span><span class="sxs-lookup"><span data-stu-id="3d1fc-117">Component initialization methods</span></span>

<span data-ttu-id="3d1fc-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> werden aufgerufen, wenn die Komponente initialisiert wird, nachdem sie ihre anfänglichen Parameter von ihrer übergeordneten Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> erhalten hat.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="3d1fc-119">Verwenden Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, wenn die Komponente einen asynchronen Vorgang ausführt und nach Abschluss des Vorgangs aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="3d1fc-120">Für einen synchronen Betrieb setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> außer Kraft:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="3d1fc-121">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, und verwenden Sie den Operator [`await`](/dotnet/csharp/language-reference/operators/await) für den Vorgang, um einen asynchronen Vorgang durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server<span data-ttu-id="3d1fc-122">-Apps, die [ihren Inhalt vorab rendern](xref:blazor/fundamentals/additional-scenarios#render-mode) rufen <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_zweimal_** auf:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-122"> apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="3d1fc-123">Einmal, wenn die Komponente anfänglich statisch als Teil der Seite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="3d1fc-124">Ein zweites Mal, wenn der Browser eine Verbindung mit dem Server herstellt.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="3d1fc-125">Informationen zum Verhindern, dass Entwicklercode in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zweimal ausgeführt wird, finden Sie im Abschnitt [Zustandsbehaftete erneute Verbindung nach dem Prerendering](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="3d1fc-126">Während für eine Blazor Server-App ein Prerendering durchgeführt wird, sind bestimmte Aktionen (z. B. Aufrufe in JavaScript) nicht möglich, da noch keine Verbindung mit dem Browser hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="3d1fc-127">Komponenten müssen wahrscheinlich unterschiedlich rendern, wenn dafür ein Prerendering durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="3d1fc-128">Weitere Informationen finden Sie im Abschnitt [Ermitteln des Prerenderings einer App](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="3d1fc-129">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="3d1fc-130">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="3d1fc-131">Nachdem die Parameter festgelegt wurden</span><span class="sxs-lookup"><span data-stu-id="3d1fc-131">After parameters are set</span></span>

<span data-ttu-id="3d1fc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> wird aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="3d1fc-133">Nachdem die Komponente in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> oder <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>.</span></span>
* <span data-ttu-id="3d1fc-134">Wenn die übergeordnete Komponente neu gerendert wird und Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="3d1fc-135">Nur bekannte einfache, unveränderliche Typen, von denen sich mindestens ein Parameter geändert hat.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="3d1fc-136">Beliebige Parameter mit komplexem Typ.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-136">Any complex-typed parameters.</span></span> <span data-ttu-id="3d1fc-137">Das Framework kann nicht wissen, ob die Werte eines Parameters mit komplexem Typ intern mutiert sind, also behandelt es den Parametersatz als geändert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="3d1fc-138">Die asynchrone Arbeit bei der Anwendung von Parametern und Eigenschaftswerten muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="3d1fc-139">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="3d1fc-140">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="3d1fc-141">Nach dem Rendern der Komponente</span><span class="sxs-lookup"><span data-stu-id="3d1fc-141">After component render</span></span>

<span data-ttu-id="3d1fc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> werden aufgerufen, nachdem eine Komponente das Rendering beendet hat.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="3d1fc-143">Element- und Komponentenverweise werden an dieser Stelle aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="3d1fc-144">In dieser Phase können Sie zusätzliche Initialisierungsschritte unter Verwendung des gerenderten Inhalts durchführen, z. B. die Aktivierung von JavaScript-Bibliotheken von Drittanbietern, die mit den gerenderten DOM-Elementen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="3d1fc-145">Der Parameter `firstRender` für <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="3d1fc-146">Wird auf `true` festgelegt, wenn die Komponenteninstanz zum ersten Mal gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="3d1fc-147">Kann verwendet werden, um sicherzustellen, dass die Initialisierung nur einmal durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="3d1fc-148">Die asynchrone Arbeit unmittelbar nach dem Rendering muss während des <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>-Lebenszyklusereignisses erfolgen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="3d1fc-149">Selbst wenn Sie ein <xref:System.Threading.Tasks.Task> von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> zurückgeben, plant das Framework keinen weiteren Renderzyklus für Ihre Komponente ein, sobald diese Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="3d1fc-150">Damit soll eine unendliche Renderschleife vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="3d1fc-151">Dies unterscheidet sich von den anderen Lebenszyklusmethoden, die einen weiteren Renderzyklus planen, sobald die zurückgegebene Aufgabe abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="3d1fc-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> und <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *werden beim Prerendering auf dem Server nicht aufgerufen.*</span><span class="sxs-lookup"><span data-stu-id="3d1fc-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="3d1fc-153">Wenn Ereignishandler eingerichtet wurden, sollten Sie ihre Einbindung bei der Beseitigung aufheben.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="3d1fc-154">Weitere Informationen finden Sie im Abschnitt [Beseitigung von Komponenten mit `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="3d1fc-155">Unterdrücken der UI-Aktualisierung</span><span class="sxs-lookup"><span data-stu-id="3d1fc-155">Suppress UI refreshing</span></span>

<span data-ttu-id="3d1fc-156">Setzen Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft, um die Aktualisierung der Benutzeroberfläche zu unterdrücken.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="3d1fc-157">Wenn die Implementierung `true` zurückgibt, wird die Benutzeroberfläche aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="3d1fc-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird jedes Mal aufgerufen, wenn die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="3d1fc-159">Selbst wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> außer Kraft gesetzt wird, wird die Komponente immer anfänglich gerendert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="3d1fc-160">Weitere Informationen finden Sie unter <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="3d1fc-161">Statusänderungen</span><span class="sxs-lookup"><span data-stu-id="3d1fc-161">State changes</span></span>

<span data-ttu-id="3d1fc-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> benachrichtigt die Komponente, dass sich ihr Zustand geändert hat.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="3d1fc-163">Gegebenenfalls bewirkt der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> das erneute Rendern der Komponente.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="3d1fc-164">Behandeln unvollständiger asynchroner Aktionen beim Rendern</span><span class="sxs-lookup"><span data-stu-id="3d1fc-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="3d1fc-165">Asynchrone Aktionen, die in Lebenszyklusereignissen ausgeführt werden, sind möglicherweise nicht abgeschlossen, bevor die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="3d1fc-166">Objekte können während der Ausführung der Lebenszyklusmethode `null` oder unvollständig mit Daten gefüllt sein.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="3d1fc-167">Stellen Sie eine Renderinglogik bereit, um zu bestätigen, dass die Objekte initialisiert sind.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="3d1fc-168">Rendern Sie UI-Elemente für Platzhalter (z. B. eine Nachricht zum Ladevorgang), während Objekte `null` sind.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="3d1fc-169">In der `FetchData`-Komponente der Blazor-Vorlagen wird <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> außer Kraft gesetzt, um Vorhersagedaten asynchron zu erhalten (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="3d1fc-169">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="3d1fc-170">Wenn `forecasts` gleich `null` ist, wird dem Benutzer eine Nachricht zum Ladevorgang angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="3d1fc-171">Nachdem die von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zurückgegebene `Task` abgeschlossen ist, wird die Komponente mit dem aktualisierten Zustand neu gerendert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-171">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="3d1fc-172">`Pages/FetchData.razor` in der Blazor Server-Vorlage:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-172">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="3d1fc-173">Beseitigung von Komponenten mit IDisposable</span><span class="sxs-lookup"><span data-stu-id="3d1fc-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="3d1fc-174">Wenn eine Komponente <xref:System.IDisposable> implementiert, wird die [`Dispose`-Methode](/dotnet/standard/garbage-collection/implementing-dispose) aufgerufen, wenn die Komponente aus der Benutzeroberfläche entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-174">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="3d1fc-175">Die folgende Komponente verwendet `@implements IDisposable` und die `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="3d1fc-176">Der Aufruf von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="3d1fc-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> könnte im Rahmen des Beendens des Renderers aufgerufen werden, sodass die Anforderung von UI-Updates an diesem Punkt nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="3d1fc-178">Kündigen Sie die .NET-Ereignisabonnements der Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="3d1fc-179">Die folgenden [Blazor-Formularbeispiele](xref:blazor/forms-validation) veranschaulichen das Aufheben der Einbindung eines Ereignishandlers in der `Dispose`-Methode:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="3d1fc-180">Ansatz mit einem privatem Feld und Lambdaausdruck</span><span class="sxs-lookup"><span data-stu-id="3d1fc-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="3d1fc-181">Ansatz mit einer privaten Methode</span><span class="sxs-lookup"><span data-stu-id="3d1fc-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="3d1fc-182">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="3d1fc-182">Handle errors</span></span>

<span data-ttu-id="3d1fc-183">Informationen zur Behandlung von Fehlern während der Ausführung von Lebenszyklusmethoden finden Sie unter <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-183">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="3d1fc-184">Zustandsbehaftete erneute Verbindung nach dem Prerendering</span><span class="sxs-lookup"><span data-stu-id="3d1fc-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="3d1fc-185">Wenn in einer Blazor Server-App <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> gleich <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> ist, wird die Komponente zunächst statisch als Teil der Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-185">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="3d1fc-186">Sobald der Browser eine Verbindung mit dem Server herstellt, wird die Komponente *wieder* gerendert, und die Komponente ist nun interaktiv.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="3d1fc-187">Wenn die [`OnInitialized{Async}`](#component-initialization-methods)-Lebenszyklusmethode zur Initialisierung der Komponente vorhanden ist, wird die Methode *zweimal* ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-187">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="3d1fc-188">Wenn die Komponente statisch vorab gerendert ist.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="3d1fc-189">Nachdem die Serververbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-189">After the server connection has been established.</span></span>

<span data-ttu-id="3d1fc-190">Dies kann zu einer spürbaren Änderung der auf der Benutzeroberfläche angezeigten Daten führen, wenn die Komponente schließlich gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="3d1fc-191">So vermeiden Sie das Szenario des doppelten Renderings in einer Blazor Server-App:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="3d1fc-192">Übergeben Sie einen Bezeichner, der zum Zwischenspeichern des Status während des Prerenderings und zum Abrufen des Zustands nach dem Neustart der App verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="3d1fc-193">Verwenden Sie den Bezeichner während des Prerenderings, um den Zustand der Komponenten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="3d1fc-194">Verwenden Sie den Bezeichner nach dem Prerendering, um den zwischengespeicherten Zustand abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="3d1fc-195">Der folgende Code veranschaulicht eine aktualisierte `WeatherForecastService` in einer vorlagenbasierten Blazor Server-App, die das doppelte Rendering vermeidet:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="3d1fc-196">Weitere Informationen zum <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> finden Sie unter <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-196">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="3d1fc-197">Erkennen, wenn für die App ein Prerendering durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="3d1fc-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="3d1fc-198">Abbrechbare Hintergrundarbeit</span><span class="sxs-lookup"><span data-stu-id="3d1fc-198">Cancelable background work</span></span>

<span data-ttu-id="3d1fc-199">Komponenten führen häufig Hintergrundaufgaben aus, die lange dauern, zum Beispiel die Durchführung von Netzwerkaufrufen (<xref:System.Net.Http.HttpClient>) und die Interaktion mit Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="3d1fc-200">Es ist wünschenswert, die Hintergrundarbeit zu unterbinden, um Systemressourcen in mehreren Situationen zu sparen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="3d1fc-201">Beispielsweise werden asynchrone Hintergrundvorgänge nicht automatisch beendet, wenn ein Benutzer von einer Komponente wegnavigiert.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="3d1fc-202">Andere Gründe, warum Arbeitselemente, die im Hintergrund ausgeführt werden, unterbrochen werden müssen, sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="3d1fc-203">Eine ausgeführte Hintergrundaufgabe wurde mit fehlerhaften Eingabedaten oder Verarbeitungsparametern gestartet.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="3d1fc-204">Die aktuellen Arbeitselemente, die im Hintergrund ausgeführt werden, müssen durch neue Arbeitselemente ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="3d1fc-205">Die Priorität der aktuell ausgeführten Aufgaben muss geändert werden.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="3d1fc-206">Die App muss herunterfahren werden, um sie erneut auf dem Server bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="3d1fc-207">Serverressourcen werden eingeschränkt und erfordern die Neuplanung von Arbeitselementen, die im Hintergrund ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-207">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="3d1fc-208">So implementieren Sie ein abbrechbares Hintergrundarbeitsmuster in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="3d1fc-209">Verwenden Sie eine <xref:System.Threading.CancellationTokenSource>-Klasse und eine <xref:System.Threading.CancellationToken>-Struktur.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="3d1fc-210">Es ist gewünscht, dass der Abbruch bei der [Löschung der Komponente](#component-disposal-with-idisposable) und zu jedem Zeitpunkt durch manuellen Abbruch des Tokens durchgeführt wird. Rufen Sie dazu [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) auf, um zu signalisieren, dass die Hintergrundarbeit abgebrochen werden soll.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="3d1fc-211">Rufen Sie nach Rückgabe des asynchronen Aufrufs <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> für das Token auf.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="3d1fc-212">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3d1fc-212">In the following example:</span></span>

* <span data-ttu-id="3d1fc-213">`await Task.Delay(5000, cts.Token);` stellt asynchrone Hintergrundaufgaben mit langer Ausführungszeit dar.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="3d1fc-214">`BackgroundResourceMethod` stellt eine Hintergrundmethode mit langer Ausführungszeit dar, die nicht gestartet werden sollte, wenn die `Resource` vor dem Aufruf der Methode verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="3d1fc-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
