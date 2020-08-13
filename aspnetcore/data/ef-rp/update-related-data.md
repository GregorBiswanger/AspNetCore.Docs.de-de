---
title: 'Teil 7: Razor Pages mit EF Core in ASP.NET Core – Aktualisieren relevanter Daten'
author: rick-anderson
description: Dies ist Teil 7 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/update-related-data
ms.openlocfilehash: 3807c52bb843c4d6403e8236fde50c034a8d1e2b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017739"
---
# <a name="part-7-no-locrazor-pages-with-ef-core-in-aspnet-core---update-related-data"></a><span data-ttu-id="85f29-103">Teil 7: Razor Pages mit EF Core in ASP.NET Core – Aktualisieren relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="85f29-103">Part 7, Razor Pages with EF Core in ASP.NET Core - Update Related Data</span></span>

<span data-ttu-id="85f29-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85f29-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85f29-105">Dieses Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="85f29-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="85f29-106">In den folgenden Abbildungen werden einige der abgeschlossenen Seiten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="85f29-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="85f29-107">![Bearbeitungsseite „Course“ (Kurs)](update-related-data/_static/course-edit30.png)
![Bearbeitungsseite „Instructor“ (Dozent)](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="85f29-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="85f29-108">Aktualisieren der Seiten „Course Create“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="85f29-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="85f29-109">Der Gerüstbaucode für die Seiten „Course Create“ und „Edit“ enthält eine Dropdownliste der Fachbereiche, in der eine „Department ID“ (ein Integerwert) angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="85f29-110">In der Dropdownliste sollte der Name des Fachbereichs angezeigt werden, sodass für beide Seiten eine Liste der Fachereichsnamen erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="85f29-111">Um diese Liste bereitzustellen, verwenden Sie eine Basisklasse für die Seiten „Create“ und „Edit“.</span><span class="sxs-lookup"><span data-stu-id="85f29-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="85f29-112">Erstellen einer Basisklasse für „Course Create“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="85f29-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="85f29-113">Erstellen Sie eine Datei *Pages/Courses/DepartmentNamePageModel.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="85f29-114">Der vorangehende Code erstellt eine [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), in der die Liste der Abteilungsnamen enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="85f29-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="85f29-115">Wenn `selectedDepartment` angegeben ist, wird diese Abteilung in `SelectList` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="85f29-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="85f29-116">Die Modellklassen der Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten) werden von `DepartmentNamePageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85f29-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="85f29-117">Aktualisieren des Seitenmodells „Course Create“</span><span class="sxs-lookup"><span data-stu-id="85f29-117">Update the Course Create page model</span></span>

<span data-ttu-id="85f29-118">Ein Kurs wird einem Fachbereich zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="85f29-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="85f29-119">Die Basisklasse für die Seite „Create“ und „Edit“ stellt `SelectList` zum Auswählen des Fachbereichs bereit.</span><span class="sxs-lookup"><span data-stu-id="85f29-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="85f29-120">In der Dropdownliste, die `SelectList` verwendet, wird die Fremdschlüsseleigenschaft `Course.DepartmentID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="85f29-121">EF Core verwendet den Fremdschlüssel `Course.DepartmentID` zum Laden der Navigationseigenschaft `Department`.</span><span class="sxs-lookup"><span data-stu-id="85f29-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Erstellen eines Kurses](update-related-data/_static/ddl30.png)

<span data-ttu-id="85f29-123">Aktualisieren Sie *Pages/Courses/Create.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="85f29-124">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-124">The preceding code:</span></span>

* <span data-ttu-id="85f29-125">Wird von `DepartmentNamePageModel`abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85f29-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="85f29-126">Verwendet `TryUpdateModelAsync`, um ein [Overposting](xref:data/ef-rp/crud#overposting) zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="85f29-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="85f29-127">Entfernt `ViewData["DepartmentID"]`</span><span class="sxs-lookup"><span data-stu-id="85f29-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="85f29-128">`DepartmentNameSL` aus der Basisklasse ist ein stark typisiertes Modell, das von der Razor-Seite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="85f29-129">Stark typisierte Modelle werden gegenüber schwach typisierten Modellen bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="85f29-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="85f29-130">Weitere Informationen hierzu finden Sie unter [Weakly typed data (ViewData and ViewBag) (Schwach typisierte Daten (ViewData und ViewBag))](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="85f29-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-no-locrazor-page"></a><span data-ttu-id="85f29-131">Aktualisieren der Razor-Seite zum Erstellen von Kursen</span><span class="sxs-lookup"><span data-stu-id="85f29-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="85f29-132">Aktualisieren Sie *Pages/Courses/Create.cshtml* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="85f29-133">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="85f29-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="85f29-134">Ändert die Beschriftung von **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="85f29-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="85f29-135">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="85f29-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="85f29-136">Fügt die Option „Abteilung auswählen“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="85f29-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="85f29-137">Durch diese Änderung wird die Option „Select Department“ (Fachbereich auswählen) in der Dropdownliste gerendert, wenn noch kein Fachbereich ausgewählt wurde, und nicht der erste Fachbereich.</span><span class="sxs-lookup"><span data-stu-id="85f29-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="85f29-138">Fügt eine Validierungsmeldung hinzu, wenn die Abteilung nicht ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="85f29-139">Die Razor Page verwendet die Option [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) (Taghilfsprogramm auswählen):</span><span class="sxs-lookup"><span data-stu-id="85f29-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="85f29-140">Testen Sie die Seite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="85f29-140">Test the Create page.</span></span> <span data-ttu-id="85f29-141">Auf der Seite „Create“ (Erstellen) wird anstelle der Abteilungs-ID der Abteilungsname angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="85f29-142">Aktualisieren des Seitenmodells „Course Edit“</span><span class="sxs-lookup"><span data-stu-id="85f29-142">Update the Course Edit page model</span></span>

<span data-ttu-id="85f29-143">Aktualisieren Sie *Pages/Courses/Edit.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="85f29-144">Die Änderungen ähneln den im Seitenmodell „Create“ (Erstellen) vorgenommenen Änderungen.</span><span class="sxs-lookup"><span data-stu-id="85f29-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="85f29-145">Im vorangehenden Code wird `PopulateDepartmentsDropDownList` an die Fachbereichs-ID übergeben, wodurch der in der Dropdownliste angegebene Fachbereich ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-no-locrazor-page"></a><span data-ttu-id="85f29-146">Aktualisieren der Razor-Seite zum Bearbeiten von Kursen</span><span class="sxs-lookup"><span data-stu-id="85f29-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="85f29-147">Aktualisieren Sie *Pages/Courses/Edit.cshtml* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="85f29-148">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="85f29-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="85f29-149">Zeigt die Kurs-ID an.</span><span class="sxs-lookup"><span data-stu-id="85f29-149">Displays the course ID.</span></span> <span data-ttu-id="85f29-150">In der Regel wird der Primärschlüssel (PS) einer Entität nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="85f29-151">PS sind für Benutzer normalerweise nicht von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="85f29-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="85f29-152">In diesem Fall handelt es sich bei dem PS um die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="85f29-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="85f29-153">Ändert die Beschriftung für die Dropdownliste für den Fachbereich aus **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="85f29-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="85f29-154">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="85f29-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="85f29-155">Die Seite enthält ein ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="85f29-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="85f29-156">Das Hinzufügen eines `<label>`-Taghilfsprogramms mit `asp-for="Course.CourseID"` bewirkt nicht, dass das ausgeblendete Feld nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="85f29-157">`<input type="hidden">` ist erforderlich, damit die Kursnummer in die bereitgestellten Daten eingeschlossen wird, wenn der Benutzer auf **Speichern** klickt.</span><span class="sxs-lookup"><span data-stu-id="85f29-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="85f29-158">Aktualisieren der Seiten „Course Details“ und „Delete“</span><span class="sxs-lookup"><span data-stu-id="85f29-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="85f29-159">Durch [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) kann die Leistung verbessert werden, wenn keine Nachverfolgung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="85f29-160">Aktualisieren der Seitenmodelle „Course“</span><span class="sxs-lookup"><span data-stu-id="85f29-160">Update the Course page models</span></span>

<span data-ttu-id="85f29-161">Aktualisieren Sie *Pages/Courses/Delete.cshtml.cs* mit dem folgenden Code, um `AsNoTracking` hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="85f29-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="85f29-162">Nehmen Sie dieselbe Änderung in der Datei *Pages/Courses/Details.cshtml.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="85f29-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-no-locrazor-pages"></a><span data-ttu-id="85f29-163">Aktualisieren der Razor-Seiten für Kurse</span><span class="sxs-lookup"><span data-stu-id="85f29-163">Update the Course Razor pages</span></span>

<span data-ttu-id="85f29-164">Aktualisieren Sie *Pages/Courses/Delete.cshtml* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="85f29-165">Nehmen Sie auf der Seite „Details“ die gleichen Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="85f29-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="85f29-166">Testen der Kursseiten</span><span class="sxs-lookup"><span data-stu-id="85f29-166">Test the Course pages</span></span>

<span data-ttu-id="85f29-167">Testen Sie die Seiten „Create“ (Erstellen), „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="85f29-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="85f29-168">Aktualisieren der Seiten „Create“ und „Edit“ von Dozenten</span><span class="sxs-lookup"><span data-stu-id="85f29-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="85f29-169">Dozenten können eine beliebige Anzahl von Kursen unterrichten.</span><span class="sxs-lookup"><span data-stu-id="85f29-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="85f29-170">In der folgenden Abbildung wird die Seite „Edit“ des Dozenten mit einem Reihe von Kurskontrollkästchen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Dozentenseite „Edit“ (Bearbeiten) mit Kursen](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="85f29-172">Über die Kontrollkästchen können Änderungen an Kursen vorgenommen werden, denen ein Dozent zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="85f29-173">Für jeden Kurs in der Datenbank wird ein Kontrollkästchen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="85f29-174">Kurse, denen der Dozent zugewiesen ist, sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="85f29-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="85f29-175">Der Benutzer kann Kontrollkästchen aktivieren oder deaktivieren, um Kurszuweisungen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="85f29-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="85f29-176">Wenn die Anzahl der Kurse viel größer ist, funktioniert eine andere Benutzeroberfläche möglicherweise besser.</span><span class="sxs-lookup"><span data-stu-id="85f29-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="85f29-177">Die hier gezeigte Methode zum Verwalten einer m:n-Beziehung würde sich jedoch nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="85f29-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="85f29-178">Um Beziehungen zu erstellen oder zu löschen, bearbeiten Sie eine Joinentität.</span><span class="sxs-lookup"><span data-stu-id="85f29-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="85f29-179">Erstellen einer Klasse für zugewiesene Kursdaten</span><span class="sxs-lookup"><span data-stu-id="85f29-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="85f29-180">Erstellen Sie *SchoolViewModels/AssignedCourseData.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="85f29-181">Die `AssignedCourseData`-Klasse enthält Daten zur Erstellung der Kontrollkästchen für Kurse, die einem Dozenten zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="85f29-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="85f29-182">Erstellen einer Basisklasse für das Seitenmodell „Instructor“</span><span class="sxs-lookup"><span data-stu-id="85f29-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="85f29-183">Erstellen Sie die Basisklasse *Pages/Instructors/InstructorCoursesPageModel.cs*:</span><span class="sxs-lookup"><span data-stu-id="85f29-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="85f29-184">Bei `InstructorCoursesPageModel` handelt es sich um die Basisklasse, die Sie für die Seitenmodelle „Edit“ (Bearbeiten) und „Create“ (Erstellen) verwenden.</span><span class="sxs-lookup"><span data-stu-id="85f29-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="85f29-185">`PopulateAssignedCourseData` liest alle `Course`-Entitäten, mit denen `AssignedCourseDataList` aufgefüllt werden soll.</span><span class="sxs-lookup"><span data-stu-id="85f29-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="85f29-186">Für jeden Kurs legt der Code die `CourseID` und den Titel fest. Zudem legt er fest, ob der Dozent einem Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="85f29-187">Ein [HashSet](/dotnet/api/system.collections.generic.hashset-1) wird für effiziente Suchvorgänge verwendet.</span><span class="sxs-lookup"><span data-stu-id="85f29-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="85f29-188">Da es auf der Razor-Seite keine Sammlung der Course-Entitäten gibt, kann durch die Modellbindung kein automatisches Update für die `CourseAssignments`-Navigationseigenschaft ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="85f29-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="85f29-189">Verwenden Sie dafür die neue `CourseAssignments`-Methode anstatt die Modellbindung zu verwenden, um ein Update für die `UpdateInstructorCourses`-Navigationseigenschaft auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85f29-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="85f29-190">Aus diesem Grund müssen Sie die `CourseAssignments`-Eigenschaft von der Modellbindung ausschließen.</span><span class="sxs-lookup"><span data-stu-id="85f29-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="85f29-191">Dafür muss der Code, der `TryUpdateModel` aufruft, nicht verändert werden, weil Sie die Überladung mit deklarierten Eigenschaften verwenden und `CourseAssignments` nicht in der Liste der einzuschließenden Elemente enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload with declared properties and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="85f29-192">Wenn keins der Kontrollkästchen aktiviert ist, initialisiert der Code in `UpdateInstructorCourses` die `CourseAssignments`-Navigationseigenschaft mit einer leeren Auflistung und gibt Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="85f29-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="85f29-193">Der Code durchläuft dann in einer Schleife alle Kurse in der Datenbank und vergleicht jeden Kurs, der zu diesem Zeitpunkt dem Dozenten zugewiesen ist, mit den Kursen, die auf der Seite aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="85f29-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="85f29-194">Die beiden letzten Auflistungen werden in `HashSet`-Objekten gespeichert, um Suchvorgänge effizienter zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="85f29-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="85f29-195">Wenn das Kontrollkästchen für einen Kurs aktiviert ist, dieser Kurs jedoch nicht in der `Instructor.CourseAssignments`-Navigationseigenschaft vorhanden ist, wird dieser der Auflistung in der Navigationseigenschaft hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="85f29-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="85f29-196">Wenn das Kontrollkästchen für einen Kurs aktiviert ist, dieser Kurs jedoch nicht in der `Instructor.CourseAssignments`-Navigationseigenschaft vorhanden ist, wird dieser aus der Auflistung in der Navigationseigenschaft gelöscht.</span><span class="sxs-lookup"><span data-stu-id="85f29-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="85f29-197">Verarbeiten des Bürostandorts</span><span class="sxs-lookup"><span data-stu-id="85f29-197">Handle office location</span></span>

<span data-ttu-id="85f29-198">Eine weitere Beziehung, die die Bearbeitungsseite verarbeiten muss, ist die 1:0- oder 1:1-Beziehung, die die Instructor-Entität mit der `OfficeAssignment`-Entität aufweist.</span><span class="sxs-lookup"><span data-stu-id="85f29-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="85f29-199">Der Dozentenbearbeitungscode muss die folgenden Szenarien verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="85f29-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="85f29-200">Löschen Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung aufhebt.</span><span class="sxs-lookup"><span data-stu-id="85f29-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="85f29-201">Das Erstellen einer neuen Entität `OfficeAssignment`, wenn der Benutzer eine Bürozuweisung eingibt und diese leer war.</span><span class="sxs-lookup"><span data-stu-id="85f29-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="85f29-202">Aktualisieren Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung ändert.</span><span class="sxs-lookup"><span data-stu-id="85f29-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="85f29-203">Aktualisieren des Seitenmodells „Edit“ des Dozenten</span><span class="sxs-lookup"><span data-stu-id="85f29-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="85f29-204">Aktualisieren Sie die Datei *Pages\Instructors\Edit.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="85f29-205">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-205">The preceding code:</span></span>

* <span data-ttu-id="85f29-206">Ruft die aktuelle Entität `Instructor` aus der Datenbank über Eager Loading für die Navigationseigenschaft `OfficeAssignment`, `CourseAssignment` und `CourseAssignment.Course` ab.</span><span class="sxs-lookup"><span data-stu-id="85f29-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="85f29-207">Aktualisiert die abgerufene Entität `Instructor` mit Werten aus der Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="85f29-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="85f29-208">`TryUpdateModel` verhindert ein [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="85f29-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="85f29-209">Wenn kein Bürostandort angegeben ist, wird `Instructor.OfficeAssignment` auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="85f29-210">Wenn `Instructor.OfficeAssignment` auf NULL festgelegt ist, wird die zugehörige Zeile in der `OfficeAssignment`-Tabelle gelöscht.</span><span class="sxs-lookup"><span data-stu-id="85f29-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="85f29-211">Ruft `PopulateAssignedCourseData` in `OnGetAsync` auf, um Informationen für die Kontrollkästchen mithilfe der `AssignedCourseData`-Ansichtsmodellklasse bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="85f29-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="85f29-212">Ruft `UpdateInstructorCourses` in `OnPostAsync` auf,um Informationen aus den Kontrollkästchen für die zu bearbeitende Instructor-Entität anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="85f29-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="85f29-213">Ruft `PopulateAssignedCourseData` und `UpdateInstructorCourses` in `OnPostAsync` auf, wenn `TryUpdateModel` fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="85f29-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="85f29-214">Diese Methodenaufrufe stellen die zugewiesenen Kursdaten wieder her, die auf der Seite eingegeben werden, wenn sie mit einer Fehlermeldung erneut angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-no-locrazor-page"></a><span data-ttu-id="85f29-215">Aktualisieren der Razor-Seite zum Bearbeiten von Dozenten</span><span class="sxs-lookup"><span data-stu-id="85f29-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="85f29-216">Aktualisieren Sie die Datei *Pages\Instructors\Edit.cshtml* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="85f29-217">Der vorangehende Code erstellt eine HTML-Tabelle mit drei Spalten.</span><span class="sxs-lookup"><span data-stu-id="85f29-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="85f29-218">Jede Spalte verfügt über ein Kontrollkästchen und eine Beschriftung mit der Nummer und dem Titel des Kurses.</span><span class="sxs-lookup"><span data-stu-id="85f29-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="85f29-219">Die Kontrollkästchen haben alle den gleichen Namen („selectedCourses“).</span><span class="sxs-lookup"><span data-stu-id="85f29-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="85f29-220">Durch die Verwendung des gleichen Namens weiß die Modellbindung, dass diese Kontrollkästchen wie eine Gruppe behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="85f29-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="85f29-221">Das value-Attribut der einzelnen Kontrollkästchen ist auf `CourseID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="85f29-222">Wenn die Seite zurückgesendet wird, übergibt die Modellbindung ein Array, das lediglich die `CourseID`-Werte der aktivierten Kontrollkästchen enthält.</span><span class="sxs-lookup"><span data-stu-id="85f29-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="85f29-223">Wenn die Kontrollkästchen anfänglich gerendert werden, sind dem Dozenten zugewiesene Kurse ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="85f29-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="85f29-224">Hinweis: Der hier gewählte Ansatz für die Bearbeitung der Kursdaten von Dozenten wird empfohlen, wenn eine begrenzte Anzahl von Kursen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="85f29-225">Bei umfangreicheren Sammlungen wären eine andere Benutzeroberfläche und eine andere Aktualisierungsmethode nützlicher und effizienter.</span><span class="sxs-lookup"><span data-stu-id="85f29-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="85f29-226">Führen Sie die App aus, und testen Sie die aktualisierte Seite „Edit“ des Dozenten.</span><span class="sxs-lookup"><span data-stu-id="85f29-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="85f29-227">Ändern Sie einige Kurszuweisungen.</span><span class="sxs-lookup"><span data-stu-id="85f29-227">Change some course assignments.</span></span> <span data-ttu-id="85f29-228">Die Änderungen werden auf der Seite „Index“ widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="85f29-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="85f29-229">Aktualisieren der Dozentenseite „Create“</span><span class="sxs-lookup"><span data-stu-id="85f29-229">Update the Instructor Create page</span></span>

<span data-ttu-id="85f29-230">Aktualisieren Sie das Modell der Dozentenseite „Create“ und die Razor-Seite mit Code, der der Seite „Edit“ ähnelt:</span><span class="sxs-lookup"><span data-stu-id="85f29-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="85f29-231">Testen Sie die Dozentenseite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="85f29-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="85f29-232">Aktualisieren der Dozentenseite „Delete“</span><span class="sxs-lookup"><span data-stu-id="85f29-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="85f29-233">Aktualisieren Sie die Datei *Pages\Instructors\Delete.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="85f29-234">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="85f29-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="85f29-235">Verwendet Eager Loading für die Navigationseigenschaft `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="85f29-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="85f29-236">`CourseAssignments` müssen eingeschlossen werden. Andernfalls werden diese nicht gelöscht, wenn der Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="85f29-237">Wenn Sie vermeiden möchten, diese lesen zu müssen, konfigurieren Sie in der Datenbank eine Löschweitergabe.</span><span class="sxs-lookup"><span data-stu-id="85f29-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="85f29-238">Wenn der zu löschende Dozent als Administrator einer beliebigen Abteilung zugewiesen ist, wird die Dozentenzuweisung aus diesen Abteilungen entfernt.</span><span class="sxs-lookup"><span data-stu-id="85f29-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="85f29-239">Führen Sie die App aus, und testen Sie die Seite „Delete“.</span><span class="sxs-lookup"><span data-stu-id="85f29-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85f29-240">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="85f29-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="85f29-241">[Vorheriges Tutorial](xref:data/ef-rp/read-related-data)
> [Nächstes Tutorial](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="85f29-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85f29-242">In diesem Tutorial wird veranschaulicht, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="85f29-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="85f29-243">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="85f29-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="85f29-244">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="85f29-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="85f29-245">In den folgenden Abbildungen werden einige der abgeschlossenen Seiten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="85f29-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="85f29-246">![Bearbeitungsseite „Course“ (Kurs)](update-related-data/_static/course-edit.png)
![Bearbeitungsseite „Instructor“ (Dozent)](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="85f29-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="85f29-247">Überprüfen und testen Sie die Seiten „Create Course“ (Kurs erstellen) und „Edit Course“ (Kurs bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="85f29-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="85f29-248">Erstellen Sie einen neuen Kurs.</span><span class="sxs-lookup"><span data-stu-id="85f29-248">Create a new course.</span></span> <span data-ttu-id="85f29-249">Die Abteilung wird nach dem zugehörigen Primärschlüssel (eine ganze Zahl) ausgewählt, nicht nach dem Namen.</span><span class="sxs-lookup"><span data-stu-id="85f29-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="85f29-250">Bearbeiten Sie den neuen Kurs.</span><span class="sxs-lookup"><span data-stu-id="85f29-250">Edit the new course.</span></span> <span data-ttu-id="85f29-251">Wenn Sie die Tests ausgeführt haben, können Sie den neuen Kurs löschen.</span><span class="sxs-lookup"><span data-stu-id="85f29-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="85f29-252">Erstellen einer Basisklasse für die gemeinsame Nutzung von allgemeinem Code</span><span class="sxs-lookup"><span data-stu-id="85f29-252">Create a base class to share common code</span></span>

<span data-ttu-id="85f29-253">Auf den Seiten „Courses/Create“ (Kurse/Erstellen) und „Courses/Edit“ (Kurse/Bearbeiten) ist jeweils eine Liste mit Abteilungsnamen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="85f29-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="85f29-254">Erstellen Sie die Basisklasse *Pages/Courses/DepartmentNamePageModel.cshtml.cs* für die Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten):</span><span class="sxs-lookup"><span data-stu-id="85f29-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="85f29-255">Der vorangehende Code erstellt eine [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), in der die Liste der Abteilungsnamen enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="85f29-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="85f29-256">Wenn `selectedDepartment` angegeben ist, wird diese Abteilung in `SelectList` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="85f29-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="85f29-257">Die Modellklassen der Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten) werden von `DepartmentNamePageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85f29-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="85f29-258">Anpassen der Kursseiten</span><span class="sxs-lookup"><span data-stu-id="85f29-258">Customize the Courses Pages</span></span>

<span data-ttu-id="85f29-259">Wenn eine neue Kursentität erstellt wird, muss diese in Beziehung zu einer vorhandenen Abteilung stehen.</span><span class="sxs-lookup"><span data-stu-id="85f29-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="85f29-260">Wenn Sie bei der Erstellung eines Kurses eine Abteilung hinzufügen möchten, können Sie aus einer Dropdownliste in der Basisklasse für „Erstellen“ und „Bearbeiten“ die Abteilung auswählen.</span><span class="sxs-lookup"><span data-stu-id="85f29-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="85f29-261">In der Dropdownliste wird die Fremdschlüsseleigenschaft `Course.DepartmentID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="85f29-262">EF Core verwendet den Fremdschlüssel `Course.DepartmentID` zum Laden der Navigationseigenschaft `Department`.</span><span class="sxs-lookup"><span data-stu-id="85f29-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Erstellen eines Kurses](update-related-data/_static/ddl.png)

<span data-ttu-id="85f29-264">Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="85f29-265">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-265">The preceding code:</span></span>

* <span data-ttu-id="85f29-266">Wird von `DepartmentNamePageModel`abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85f29-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="85f29-267">Verwendet `TryUpdateModelAsync`, um ein [Overposting](xref:data/ef-rp/crud#overposting) zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="85f29-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="85f29-268">Ersetzt `ViewData["DepartmentID"]` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="85f29-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="85f29-269">`ViewData["DepartmentID"]` wird durch das stark typisierte Modell `DepartmentNameSL` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="85f29-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="85f29-270">Stark typisierte Modelle werden gegenüber schwach typisierten Modellen bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="85f29-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="85f29-271">Weitere Informationen hierzu finden Sie unter [Weakly typed data (ViewData and ViewBag) (Schwach typisierte Daten (ViewData und ViewBag))](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="85f29-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="85f29-272">Aktualisieren der Seite „Courses Create“ (Kurse erstellen)</span><span class="sxs-lookup"><span data-stu-id="85f29-272">Update the Courses Create page</span></span>

<span data-ttu-id="85f29-273">Aktualisieren Sie *Pages/Courses/Create.cshtml* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="85f29-274">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="85f29-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="85f29-275">Ändert die Beschriftung von **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="85f29-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="85f29-276">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="85f29-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="85f29-277">Fügt die Option „Abteilung auswählen“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="85f29-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="85f29-278">Durch diese Änderung wird anstelle der ersten Abteilung die Option „Abteilung auswählen“ gerendert.</span><span class="sxs-lookup"><span data-stu-id="85f29-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="85f29-279">Fügt eine Validierungsmeldung hinzu, wenn die Abteilung nicht ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="85f29-280">Die Razor Page verwendet die Option [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) (Taghilfsprogramm auswählen):</span><span class="sxs-lookup"><span data-stu-id="85f29-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="85f29-281">Testen Sie die Seite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="85f29-281">Test the Create page.</span></span> <span data-ttu-id="85f29-282">Auf der Seite „Create“ (Erstellen) wird anstelle der Abteilungs-ID der Abteilungsname angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="85f29-283">Aktualisieren Sie die Seite „Courses Edit“ (Kurse bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="85f29-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="85f29-284">Ersetzen Sie den Code in *Pages/Courses/Edit.cshtml.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="85f29-285">Die Änderungen ähneln den im Seitenmodell „Create“ (Erstellen) vorgenommenen Änderungen.</span><span class="sxs-lookup"><span data-stu-id="85f29-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="85f29-286">Im vorangehenden Code wird `PopulateDepartmentsDropDownList` an die Abteilungs-ID übergeben, wodurch die in der Dropdownliste angegebene Abteilung ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="85f29-287">Aktualisieren Sie *Pages/Courses/Edit.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="85f29-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="85f29-288">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="85f29-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="85f29-289">Zeigt die Kurs-ID an.</span><span class="sxs-lookup"><span data-stu-id="85f29-289">Displays the course ID.</span></span> <span data-ttu-id="85f29-290">In der Regel wird der Primärschlüssel (PS) einer Entität nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="85f29-291">PS sind für Benutzer normalerweise nicht von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="85f29-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="85f29-292">In diesem Fall handelt es sich bei dem PS um die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="85f29-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="85f29-293">Ändert die Beschriftung von **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="85f29-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="85f29-294">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="85f29-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="85f29-295">Die Seite enthält ein ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="85f29-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="85f29-296">Das Hinzufügen eines `<label>`-Taghilfsprogramms mit `asp-for="Course.CourseID"` bewirkt nicht, dass das ausgeblendete Feld nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="85f29-297">`<input type="hidden">` ist erforderlich, damit die Kursnummer in die bereitgestellten Daten eingeschlossen wird, wenn der Benutzer auf **Speichern** klickt.</span><span class="sxs-lookup"><span data-stu-id="85f29-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="85f29-298">Testen Sie den aktualisierten Code.</span><span class="sxs-lookup"><span data-stu-id="85f29-298">Test the updated code.</span></span> <span data-ttu-id="85f29-299">Erstellen, bearbeiten und löschen Sie einen Kurs.</span><span class="sxs-lookup"><span data-stu-id="85f29-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="85f29-300">Hinzufügen von „AsNoTracking“ zu den Seitenmodellen „Details“ und „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="85f29-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="85f29-301">Durch [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) kann die Leistung verbessert werden, wenn keine Nachverfolgung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="85f29-302">Fügen Sie `AsNoTracking` zu den Seitenmodellen „Delete“ (Löschen) und „Details“ (Details) hinzu.</span><span class="sxs-lookup"><span data-stu-id="85f29-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="85f29-303">Im folgenden Code wird das aktualisierte Seitenmodell „Delete“ (Löschen) dargestellt:</span><span class="sxs-lookup"><span data-stu-id="85f29-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="85f29-304">Aktualisieren Sie die Methode `OnGetAsync` in der Datei *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="85f29-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="85f29-305">Ändern der Seiten „Delete“ (Löschen) und „Details“</span><span class="sxs-lookup"><span data-stu-id="85f29-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="85f29-306">Aktualisieren Sie die Razor-Seite „Delete“ (Löschen) mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="85f29-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="85f29-307">Nehmen Sie auf der Seite „Details“ die gleichen Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="85f29-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="85f29-308">Testen der Kursseiten</span><span class="sxs-lookup"><span data-stu-id="85f29-308">Test the Course pages</span></span>

<span data-ttu-id="85f29-309">Testen Sie das Erstellen, Bearbeiten, Löschen und Details.</span><span class="sxs-lookup"><span data-stu-id="85f29-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="85f29-310">Aktualisieren der Seiten für Dozenten</span><span class="sxs-lookup"><span data-stu-id="85f29-310">Update the instructor pages</span></span>

<span data-ttu-id="85f29-311">In den folgenden Abschnitten werden die Seiten für Dozenten aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="85f29-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="85f29-312">Hinzufügen eines Bürostandorts</span><span class="sxs-lookup"><span data-stu-id="85f29-312">Add office location</span></span>

<span data-ttu-id="85f29-313">Bei der Bearbeitung eines Datensatzes für Dozenten sollten Sie auch die Bürozuweisung des Dozenten aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="85f29-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="85f29-314">Die Entität `Instructor` verfügt über eine 1:0..1-Beziehung zur Entität `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="85f29-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="85f29-315">Der Code für „Instructor“ muss Folgendes verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="85f29-315">The instructor code must handle:</span></span>

* <span data-ttu-id="85f29-316">Löschen Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung aufhebt.</span><span class="sxs-lookup"><span data-stu-id="85f29-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="85f29-317">Das Erstellen einer neuen Entität `OfficeAssignment`, wenn der Benutzer eine Bürozuweisung eingibt und diese leer war.</span><span class="sxs-lookup"><span data-stu-id="85f29-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="85f29-318">Aktualisieren Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung ändert.</span><span class="sxs-lookup"><span data-stu-id="85f29-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="85f29-319">Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="85f29-320">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-320">The preceding code:</span></span>

* <span data-ttu-id="85f29-321">Ruft die aktuelle Entität `Instructor` von der Datenbank über Eager Loading für die Navigationseigenschaft `OfficeAssignment` ab.</span><span class="sxs-lookup"><span data-stu-id="85f29-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="85f29-322">Aktualisiert die abgerufene Entität `Instructor` mit Werten aus der Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="85f29-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="85f29-323">`TryUpdateModel` verhindert ein [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="85f29-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="85f29-324">Wenn kein Bürostandort angegeben ist, wird `Instructor.OfficeAssignment` auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="85f29-325">Wenn `Instructor.OfficeAssignment` auf NULL festgelegt ist, wird die zugehörige Zeile in der `OfficeAssignment`-Tabelle gelöscht.</span><span class="sxs-lookup"><span data-stu-id="85f29-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="85f29-326">Aktualisieren der Dozentenseite „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="85f29-326">Update the instructor Edit page</span></span>

<span data-ttu-id="85f29-327">Aktualisieren Sie *Pages/Instructors/Edit.cshtml* mit dem Bürostandort:</span><span class="sxs-lookup"><span data-stu-id="85f29-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="85f29-328">Stellen Sie sicher, dass Sie den Bürostandort eines Dozenten ändern können.</span><span class="sxs-lookup"><span data-stu-id="85f29-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="85f29-329">Hinzufügen von Kurszuweisungen zur Dozentenseite „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="85f29-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="85f29-330">Dozenten können eine beliebige Anzahl von Kursen unterrichten.</span><span class="sxs-lookup"><span data-stu-id="85f29-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="85f29-331">In diesem Abschnitt fügen Sie die Möglichkeit zum Ändern von Kurszuweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="85f29-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="85f29-332">Die folgende Abbildung zeigt die aktualisierte Dozentenseite „Edit“ (Bearbeiten):</span><span class="sxs-lookup"><span data-stu-id="85f29-332">The following image shows the updated instructor Edit page:</span></span>

![Dozentenseite „Edit“ (Bearbeiten) mit Kursen](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="85f29-334">`Course` und `Instructor` weisen eine m:n-Beziehung auf.</span><span class="sxs-lookup"><span data-stu-id="85f29-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="85f29-335">Wenn Sie Beziehungen hinzufügen und entfernen möchten, können Sie Entitäten aus der verknüpften `CourseAssignments`-Entitätenmenge hinzufügen und entfernen.</span><span class="sxs-lookup"><span data-stu-id="85f29-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="85f29-336">Über Kontrollkästchen können an Kursen vorgenommene Änderungen aktiviert werden, denen ein Dozent zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="85f29-337">Für jeden Kurs in der Datenbank wird ein Kontrollkästchen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="85f29-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="85f29-338">Kurse, denen der Dozent zugewiesen ist, sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="85f29-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="85f29-339">Der Benutzer kann Kontrollkästchen aktivieren oder deaktivieren, um Kurszuweisungen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="85f29-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="85f29-340">Wenn die Anzahl der Kurse viel größer wäre:</span><span class="sxs-lookup"><span data-stu-id="85f29-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="85f29-341">Würden Sie wahrscheinlich eine andere Benutzeroberfläche zum Anzeigen der Kurse verwenden.</span><span class="sxs-lookup"><span data-stu-id="85f29-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="85f29-342">Würde sich die Methode zum Bearbeiten einer verknüpften Entität zum Erstellen oder Löschen von Beziehungen nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="85f29-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="85f29-343">Hinzufügen von Klassen zur Unterstützung der Dozentenseite „Create“ (Erstellen) und „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="85f29-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="85f29-344">Erstellen Sie *SchoolViewModels/AssignedCourseData.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="85f29-345">Die `AssignedCourseData`-Klasse enthält Daten zur Erstellung der Kontrollkästchen für durch einen Dozenten zugewiesene Kurse.</span><span class="sxs-lookup"><span data-stu-id="85f29-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="85f29-346">Erstellen Sie die Basisklasse *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="85f29-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="85f29-347">Bei `InstructorCoursesPageModel` handelt es sich um die Basisklasse, die Sie für die Seitenmodelle „Edit“ (Bearbeiten) und „Create“ (Erstellen) verwenden.</span><span class="sxs-lookup"><span data-stu-id="85f29-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="85f29-348">`PopulateAssignedCourseData` liest alle `Course`-Entitäten, mit denen `AssignedCourseDataList` aufgefüllt werden soll.</span><span class="sxs-lookup"><span data-stu-id="85f29-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="85f29-349">Für jeden Kurs legt der Code die `CourseID` und den Titel fest. Zudem legt er fest, ob der Dozent einem Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="85f29-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="85f29-350">Mit einem [HashSet](/dotnet/api/system.collections.generic.hashset-1) werden effiziente Suchvorgänge erstellt.</span><span class="sxs-lookup"><span data-stu-id="85f29-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="85f29-351">Seitenmodell „Edit“ (Bearbeiten) für Dozenten</span><span class="sxs-lookup"><span data-stu-id="85f29-351">Instructors Edit page model</span></span>

<span data-ttu-id="85f29-352">Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="85f29-353">Der vorangehende Code behandelt an Bürozuweisungen vorgenommene Änderungen.</span><span class="sxs-lookup"><span data-stu-id="85f29-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="85f29-354">Aktualisieren der Razor-Ansicht „Dozent“</span><span class="sxs-lookup"><span data-stu-id="85f29-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="85f29-355">Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche so geändert, dass der Code unterbrochen wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="85f29-356">Drücken Sie einmal Strg+Z, um die automatische Formatierung rückgängig zu machen.</span><span class="sxs-lookup"><span data-stu-id="85f29-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="85f29-357">Mit Strg+Z werden die Zeilenumbrüche korrigiert, damit sie dem entsprechen, was Sie hier sehen.</span><span class="sxs-lookup"><span data-stu-id="85f29-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="85f29-358">Der Einzug muss nicht perfekt sein, die Zeilen `@:</tr><tr>`, `@:<td>`, `@:</td>` und `@:</tr>` müssen jedoch, wie dargestellt, jeweils in einer einzelnen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="85f29-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="85f29-359">Drücken Sie, nachdem Sie den Block mit dem neuen Code ausgewählt haben, dreimal auf die TAB-Taste, um den neuen Code am vorhandenen Code auszurichten.</span><span class="sxs-lookup"><span data-stu-id="85f29-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="85f29-360">Stimmen Sie über [folgenden Link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) über den Status dieses Fehlers ab, oder überprüfen Sie diesen.</span><span class="sxs-lookup"><span data-stu-id="85f29-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="85f29-361">Der vorangehende Code erstellt eine HTML-Tabelle mit drei Spalten.</span><span class="sxs-lookup"><span data-stu-id="85f29-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="85f29-362">Jede Spalte verfügt über ein Kontrollkästchen und eine Beschriftung mit der Nummer und dem Titel des Kurses.</span><span class="sxs-lookup"><span data-stu-id="85f29-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="85f29-363">Die Kontrollkästchen haben alle den gleichen Namen („selectedCourses“).</span><span class="sxs-lookup"><span data-stu-id="85f29-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="85f29-364">Durch die Verwendung des gleichen Namens weiß die Modellbindung, dass diese Kontrollkästchen wie eine Gruppe behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="85f29-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="85f29-365">Das Wertattribut der einzelnen Kontrollkästchen ist auf `CourseID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="85f29-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="85f29-366">Wenn die Seite zurückgesendet wird, übergibt die Modellbindung ein Array, das lediglich die `CourseID`-Werte der aktivierten Kontrollkästchen enthält.</span><span class="sxs-lookup"><span data-stu-id="85f29-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="85f29-367">Wenn die Kontrollkästchen ursprünglich gerendert wurden, weisen dem Dozenten zugewiesene Kurse überprüfte Attribute auf.</span><span class="sxs-lookup"><span data-stu-id="85f29-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="85f29-368">Führen Sie die App aus, und testen Sie die aktualisierte Dozentenseite „Edit“ (Bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="85f29-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="85f29-369">Ändern Sie einige Kurszuweisungen.</span><span class="sxs-lookup"><span data-stu-id="85f29-369">Change some course assignments.</span></span> <span data-ttu-id="85f29-370">Die Änderungen werden auf der Seite „Index“ widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="85f29-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="85f29-371">Hinweis: Der hier gewählte Ansatz für die Bearbeitung der Kursdaten von Dozenten wird empfohlen, wenn eine begrenzte Anzahl von Kursen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="85f29-372">Bei umfangreicheren Sammlungen wären eine andere Benutzeroberfläche und eine andere Aktualisierungsmethode nützlicher und effizienter.</span><span class="sxs-lookup"><span data-stu-id="85f29-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="85f29-373">Aktualisieren der Dozentenseite „Create“ (Erstellen)</span><span class="sxs-lookup"><span data-stu-id="85f29-373">Update the instructors Create page</span></span>

<span data-ttu-id="85f29-374">Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="85f29-375">Der vorangehende Code ähnelt dem Code *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="85f29-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="85f29-376">Aktualisieren Sie die Razor-Seite „Create“ (Erstellen) für Dozenten mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="85f29-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="85f29-377">Testen Sie die Dozentenseite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="85f29-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="85f29-378">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="85f29-378">Update the Delete page</span></span>

<span data-ttu-id="85f29-379">Aktualisieren Sie das Seitenmodell „Delete“ (Löschen) mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85f29-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="85f29-380">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="85f29-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="85f29-381">Verwendet Eager Loading für die Navigationseigenschaft `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="85f29-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="85f29-382">`CourseAssignments` müssen eingeschlossen werden. Andernfalls werden diese nicht gelöscht, wenn der Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="85f29-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="85f29-383">Wenn Sie vermeiden möchten, diese lesen zu müssen, konfigurieren Sie in der Datenbank eine Löschweitergabe.</span><span class="sxs-lookup"><span data-stu-id="85f29-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="85f29-384">Wenn der zu löschende Dozent als Administrator einer beliebigen Abteilung zugewiesen ist, wird die Dozentenzuweisung aus diesen Abteilungen entfernt.</span><span class="sxs-lookup"><span data-stu-id="85f29-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85f29-385">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85f29-385">Additional resources</span></span>

* [<span data-ttu-id="85f29-386">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="85f29-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="85f29-387">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="85f29-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="85f29-388">[Zurück](xref:data/ef-rp/read-related-data)
> [Weiter](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="85f29-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
