---
title: Anpassen von Modellbindungen in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr darüber, wie Controlleraktionen durch Modellbindungen direkt mit den Modelltypen in ASP.NET Core arbeiten.
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 6e52d9c19a3d9c16f980021000db0bdecca5ed58
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018909"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="6adb4-103">Anpassen von Modellbindungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6adb4-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6adb4-104">Von [Steve Smith](https://ardalis.com/) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6adb4-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="6adb4-105">Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6adb4-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="6adb4-106">Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="6adb4-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="6adb4-107">Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).</span><span class="sxs-lookup"><span data-stu-id="6adb4-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="6adb4-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6adb4-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="6adb4-109">Standardmodellbindungseinschränkungen</span><span class="sxs-lookup"><span data-stu-id="6adb4-109">Default model binder limitations</span></span>

<span data-ttu-id="6adb4-110">Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler.</span><span class="sxs-lookup"><span data-stu-id="6adb4-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="6adb4-111">Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können.</span><span class="sxs-lookup"><span data-stu-id="6adb4-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="6adb4-112">Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können.</span><span class="sxs-lookup"><span data-stu-id="6adb4-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="6adb4-113">Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6adb4-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="6adb4-114">Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="6adb4-115">Übersicht: Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-115">Model binding review</span></span>

<span data-ttu-id="6adb4-116">Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="6adb4-117">Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="6adb4-118">Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="6adb4-119">Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts.</span><span class="sxs-lookup"><span data-stu-id="6adb4-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="6adb4-120">Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="6adb4-121">Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="6adb4-122">Betrachten Sie <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="6adb4-123">Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="6adb4-124">Arbeiten mit ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="6adb4-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="6adb4-125">Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="6adb4-126">Beispielsweise kann ein Bild als Zeichenfolge codiert werden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="6adb4-127">Das Beispiel enthält ein Bild als Base64-codierte Zeichenfolge in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="6adb4-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="6adb4-128">ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="6adb4-129">Der <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> ordnet `byte[]` Argumente `ByteArrayModelBinder` zu:</span><span class="sxs-lookup"><span data-stu-id="6adb4-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="6adb4-130">Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> verwenden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="6adb4-131">In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:</span><span class="sxs-lookup"><span data-stu-id="6adb4-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="6adb4-132">Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="6adb4-133">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="6adb4-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="6adb4-134">Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="6adb4-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="6adb4-135">Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6adb4-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="6adb4-136">Beispiel für eine benutzerdefinierte Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-136">Custom model binder sample</span></span>

<span data-ttu-id="6adb4-137">In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:</span><span class="sxs-lookup"><span data-stu-id="6adb4-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="6adb4-138">Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente</span><span class="sxs-lookup"><span data-stu-id="6adb4-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="6adb4-139">Abrufen der verknüpften Entität mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6adb4-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="6adb4-140">Übergeben der verknüpften Entität als Argument an die Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="6adb4-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="6adb4-141">In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:</span><span class="sxs-lookup"><span data-stu-id="6adb4-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="6adb4-142">Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="6adb4-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="6adb4-143">Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:</span><span class="sxs-lookup"><span data-stu-id="6adb4-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="6adb4-144">Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="6adb4-145">Die Klasse stellt keine bewährte Methode für ein Suchszenario dar.</span><span class="sxs-lookup"><span data-stu-id="6adb4-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="6adb4-146">Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab.</span><span class="sxs-lookup"><span data-stu-id="6adb4-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="6adb4-147">Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="6adb4-148">Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6adb4-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="6adb4-149">Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="6adb4-150">Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="6adb4-151">Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="6adb4-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="6adb4-152">Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="6adb4-153">Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="6adb4-154">Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="6adb4-155">Implementieren von ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="6adb4-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="6adb4-156">Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="6adb4-157">So werden die integrierten Frameworkbindungen implementiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="6adb4-158">Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="6adb4-159">Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="6adb4-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="6adb4-160">Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="6adb4-161">Beachten Sie: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück.</span><span class="sxs-lookup"><span data-stu-id="6adb4-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="6adb4-162">`BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI).</span><span class="sxs-lookup"><span data-stu-id="6adb4-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="6adb4-163">`AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="6adb4-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="6adb4-164">Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="6adb4-165">Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:</span><span class="sxs-lookup"><span data-stu-id="6adb4-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="6adb4-166">Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="6adb4-167">Der erste Anbieter, der eine Bindung zurückgibt, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-167">The first provider that returns a binder is used.</span></span> <span data-ttu-id="6adb4-168">Wenn Sie Ihren Anbieter am Ende der Auflistung hinzufügen, kann es passieren, dass ein integrierter Modellbindung aufgerufen wird, bevor Ihre benutzerdefinierte Bindung an die Reihe kommt.</span><span class="sxs-lookup"><span data-stu-id="6adb4-168">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="6adb4-169">In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er auch tatsächlich für `Author`-Aktionsargumente verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6adb4-169">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="6adb4-170">Polymorphe Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-170">Polymorphic model binding</span></span>

<span data-ttu-id="6adb4-171">Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="6adb4-172">Eine benutzerdefinierte polymorphe Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss.</span><span class="sxs-lookup"><span data-stu-id="6adb4-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="6adb4-173">Polymorphe Modellbindung:</span><span class="sxs-lookup"><span data-stu-id="6adb4-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="6adb4-174">Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="6adb4-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="6adb4-175">Erschwert es, Informationen über die gebundenen Modelle zu bekommen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="6adb4-176">Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="6adb4-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="6adb4-177">Empfehlungen und bewährte Methoden</span><span class="sxs-lookup"><span data-stu-id="6adb4-177">Recommendations and best practices</span></span>

<span data-ttu-id="6adb4-178">Benutzerdefinierte Modellbindungen:</span><span class="sxs-lookup"><span data-stu-id="6adb4-178">Custom model binders:</span></span>

- <span data-ttu-id="6adb4-179">Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="6adb4-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="6adb4-180">Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.</span><span class="sxs-lookup"><span data-stu-id="6adb4-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="6adb4-181">Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.</span><span class="sxs-lookup"><span data-stu-id="6adb4-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="6adb4-182">Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. <xref:System.ComponentModel.TypeConverter> ist oft eine sinnvollere Option.</span><span class="sxs-lookup"><span data-stu-id="6adb4-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6adb4-183">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6adb4-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6adb4-184">Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6adb4-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="6adb4-185">Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="6adb4-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="6adb4-186">Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).</span><span class="sxs-lookup"><span data-stu-id="6adb4-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="6adb4-187">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6adb4-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="6adb4-188">Standardmodellbindungseinschränkungen</span><span class="sxs-lookup"><span data-stu-id="6adb4-188">Default model binder limitations</span></span>

<span data-ttu-id="6adb4-189">Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler.</span><span class="sxs-lookup"><span data-stu-id="6adb4-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="6adb4-190">Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können.</span><span class="sxs-lookup"><span data-stu-id="6adb4-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="6adb4-191">Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können.</span><span class="sxs-lookup"><span data-stu-id="6adb4-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="6adb4-192">Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6adb4-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="6adb4-193">Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="6adb4-194">Übersicht: Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-194">Model binding review</span></span>

<span data-ttu-id="6adb4-195">Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="6adb4-196">Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="6adb4-197">Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="6adb4-198">Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts.</span><span class="sxs-lookup"><span data-stu-id="6adb4-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="6adb4-199">Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="6adb4-200">Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="6adb4-201">Betrachten Sie <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="6adb4-202">Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="6adb4-203">Arbeiten mit ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="6adb4-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="6adb4-204">Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="6adb4-205">Beispielsweise kann ein Bild als Zeichenfolge codiert werden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="6adb4-206">Das Beispiel enthält ein Bild als Base64-codierte Zeichenfolge in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span><span class="sxs-lookup"><span data-stu-id="6adb4-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="6adb4-207">ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="6adb4-208">Der <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> ordnet `byte[]` Argumente `ByteArrayModelBinder` zu:</span><span class="sxs-lookup"><span data-stu-id="6adb4-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="6adb4-209">Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> verwenden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="6adb4-210">In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:</span><span class="sxs-lookup"><span data-stu-id="6adb4-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="6adb4-211">Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="6adb4-212">![Postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="6adb4-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="6adb4-213">Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="6adb4-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="6adb4-214">Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6adb4-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="6adb4-215">Beispiel für eine benutzerdefinierte Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-215">Custom model binder sample</span></span>

<span data-ttu-id="6adb4-216">In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:</span><span class="sxs-lookup"><span data-stu-id="6adb4-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="6adb4-217">Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente</span><span class="sxs-lookup"><span data-stu-id="6adb4-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="6adb4-218">Abrufen der verknüpften Entität mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6adb4-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="6adb4-219">Übergeben der verknüpften Entität als Argument an die Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="6adb4-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="6adb4-220">In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:</span><span class="sxs-lookup"><span data-stu-id="6adb4-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="6adb4-221">Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="6adb4-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="6adb4-222">Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:</span><span class="sxs-lookup"><span data-stu-id="6adb4-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="6adb4-223">Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="6adb4-224">Die Klasse stellt keine bewährte Methode für ein Suchszenario dar.</span><span class="sxs-lookup"><span data-stu-id="6adb4-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="6adb4-225">Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab.</span><span class="sxs-lookup"><span data-stu-id="6adb4-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="6adb4-226">Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="6adb4-227">Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="6adb4-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="6adb4-228">Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:</span><span class="sxs-lookup"><span data-stu-id="6adb4-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="6adb4-229">Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="6adb4-230">Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="6adb4-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="6adb4-231">Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="6adb4-232">Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="6adb4-233">Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6adb4-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="6adb4-234">Implementieren von ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="6adb4-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="6adb4-235">Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren.</span><span class="sxs-lookup"><span data-stu-id="6adb4-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="6adb4-236">So werden die integrierten Frameworkbindungen implementiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="6adb4-237">Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="6adb4-238">Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`.</span><span class="sxs-lookup"><span data-stu-id="6adb4-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="6adb4-239">Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.</span><span class="sxs-lookup"><span data-stu-id="6adb4-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="6adb4-240">Beachten Sie: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück.</span><span class="sxs-lookup"><span data-stu-id="6adb4-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="6adb4-241">`BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI).</span><span class="sxs-lookup"><span data-stu-id="6adb4-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="6adb4-242">`AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="6adb4-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="6adb4-243">Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.</span><span class="sxs-lookup"><span data-stu-id="6adb4-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="6adb4-244">Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:</span><span class="sxs-lookup"><span data-stu-id="6adb4-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="6adb4-245">Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="6adb4-246">Der erste Anbieter, der eine Bindung zurückgibt, wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="6adb4-247">Wenn Sie Ihren Anbieter am Ende der Auflistung hinzufügen, kann es passieren, dass ein integrierter Modellbindung aufgerufen wird, bevor Ihre benutzerdefinierte Bindung an die Reihe kommt.</span><span class="sxs-lookup"><span data-stu-id="6adb4-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="6adb4-248">In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er auch tatsächlich für `Author`-Aktionsargumente verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6adb4-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="6adb4-249">Polymorphe Modellbindung</span><span class="sxs-lookup"><span data-stu-id="6adb4-249">Polymorphic model binding</span></span>

<span data-ttu-id="6adb4-250">Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="6adb4-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="6adb4-251">Eine benutzerdefinierte polymorphe Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss.</span><span class="sxs-lookup"><span data-stu-id="6adb4-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="6adb4-252">Polymorphe Modellbindung:</span><span class="sxs-lookup"><span data-stu-id="6adb4-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="6adb4-253">Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="6adb4-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="6adb4-254">Erschwert es, Informationen über die gebundenen Modelle zu bekommen.</span><span class="sxs-lookup"><span data-stu-id="6adb4-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="6adb4-255">Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="6adb4-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="6adb4-256">Empfehlungen und bewährte Methoden</span><span class="sxs-lookup"><span data-stu-id="6adb4-256">Recommendations and best practices</span></span>

<span data-ttu-id="6adb4-257">Benutzerdefinierte Modellbindungen:</span><span class="sxs-lookup"><span data-stu-id="6adb4-257">Custom model binders:</span></span>

- <span data-ttu-id="6adb4-258">Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden).</span><span class="sxs-lookup"><span data-stu-id="6adb4-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="6adb4-259">Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.</span><span class="sxs-lookup"><span data-stu-id="6adb4-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="6adb4-260">Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.</span><span class="sxs-lookup"><span data-stu-id="6adb4-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="6adb4-261">Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. <xref:System.ComponentModel.TypeConverter> ist oft eine sinnvollere Option.</span><span class="sxs-lookup"><span data-stu-id="6adb4-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
