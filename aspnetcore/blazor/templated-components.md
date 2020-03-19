---
title: ASP.NET Core-Komponentenvorlagen in Blazor
author: guardrex
description: Hier erfahren Sie, wie Komponentenvorlagen eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren können, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646531"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="2d152-103">ASP.NET Core-Komponentenvorlagen in Blazor</span><span class="sxs-lookup"><span data-stu-id="2d152-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="2d152-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2d152-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2d152-105">Bei Komponentenvorlagen geht es um Komponenten, die eine oder mehrere Benutzeroberflächenvorlagen als Parameter akzeptieren, die dann wiederum als Teil der Renderlogik der Komponente verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="2d152-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="2d152-106">Mit Komponentenvorlagen können Sie Komponenten höherer Ebenen erstellen, die einfacher wiederverwendet werden können als reguläre Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2d152-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="2d152-107">Nachstehend sind einige Beispiele angegeben:</span><span class="sxs-lookup"><span data-stu-id="2d152-107">A couple of examples include:</span></span>

* <span data-ttu-id="2d152-108">Eine Tabellenkomponente, mit der ein Benutzer Vorlagen für die Überschrift, die Zeilen und die Fußzeile einer Tabelle angeben kann</span><span class="sxs-lookup"><span data-stu-id="2d152-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="2d152-109">Eine Listenkomponente, mit der ein Benutzer eine Vorlage für Renderelemente in einer Liste angeben kann</span><span class="sxs-lookup"><span data-stu-id="2d152-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="2d152-110">Vorlagenparameter</span><span class="sxs-lookup"><span data-stu-id="2d152-110">Template parameters</span></span>

<span data-ttu-id="2d152-111">Eine Komponentenvorlage wird definiert, indem mindestens ein Komponentenparameter des Typs `RenderFragment` oder `RenderFragment<T>` angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="2d152-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="2d152-112">Ein Renderfragment stellt dabei ein Segment der Benutzeroberfläche dar, das gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="2d152-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="2d152-113">`RenderFragment<T>` akzeptiert einen Typparameter, der angegeben werden kann, wenn das Renderfragment aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="2d152-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="2d152-114">`TableTemplate`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2d152-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="2d152-115">Bei der Verwendung einer Komponentenvorlage werden die Vorlagenparameter mithilfe eines untergeordneten Elements angegeben, das mit den Namen der Parameter übereinstimmt (`TableHeader` und `RowTemplate` im folgenden Beispiel):</span><span class="sxs-lookup"><span data-stu-id="2d152-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="template-context-parameters"></a><span data-ttu-id="2d152-116">Vorlagenkontextparameter</span><span class="sxs-lookup"><span data-stu-id="2d152-116">Template context parameters</span></span>

<span data-ttu-id="2d152-117">Komponentenargument des Typs `RenderFragment<T>`, die als Elemente übergeben werden, verfügen über einen impliziten Parameter namens `context` (beispielsweise `@context.PetId` im vorherigen Codebeispiel). Sie können den Parameternamen jedoch mithilfe des `Context`-Attributs für das untergeordnete Element ändern.</span><span class="sxs-lookup"><span data-stu-id="2d152-117">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="2d152-118">Im folgenden Beispiel gibt das `Context`-Attribut des `RowTemplate`-Elements den `pet`-Parameter an:</span><span class="sxs-lookup"><span data-stu-id="2d152-118">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="2d152-119">Alternativ können Sie das `Context`-Attribut im Komponentenelement angeben.</span><span class="sxs-lookup"><span data-stu-id="2d152-119">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="2d152-120">Das angegebene `Context`-Attribut gilt für alle angegebenen Vorlagenparameter.</span><span class="sxs-lookup"><span data-stu-id="2d152-120">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="2d152-121">Dies ist hilfreich, wenn Sie den Inhaltsparametername für einen impliziten untergeordneten Inhalt angeben möchten, ohne das untergeordnete Elemente umschließen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="2d152-121">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="2d152-122">Im folgenden Beispiel wird das `Context`-Attribut auf dem `TableTemplate`-Element angezeigt und gilt für alle Vorlagenparameter:</span><span class="sxs-lookup"><span data-stu-id="2d152-122">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="2d152-123">Komponenten mit generischem Typ</span><span class="sxs-lookup"><span data-stu-id="2d152-123">Generic-typed components</span></span>

<span data-ttu-id="2d152-124">Komponentenvorlagen haben oft einen generischen Typ.</span><span class="sxs-lookup"><span data-stu-id="2d152-124">Templated components are often generically typed.</span></span> <span data-ttu-id="2d152-125">Eine generische `ListViewTemplate`-Komponenten kann beispielsweise verwendet werden, um `IEnumerable<T>`-Werte zu rendern.</span><span class="sxs-lookup"><span data-stu-id="2d152-125">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="2d152-126">Verwenden Sie die [`@typeparam`](xref:mvc/views/razor#typeparam)-Direktive, um Typenparameter anzugeben, um eine generische Komponente zu definieren:</span><span class="sxs-lookup"><span data-stu-id="2d152-126">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="2d152-127">Bei der Verwendung einer Komponente mit generischem Typ, wird der Typparameter wenn möglich abgeleitet:</span><span class="sxs-lookup"><span data-stu-id="2d152-127">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="2d152-128">Andernfalls muss der Typparameter explizit mithilfe eines Attributs angegeben werden, der mit dem Namen des Typparameters übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2d152-128">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="2d152-129">Im folgenden Beispiel wird der Typ von `TItem="Pet"` angegeben:</span><span class="sxs-lookup"><span data-stu-id="2d152-129">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```