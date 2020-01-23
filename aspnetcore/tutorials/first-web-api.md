---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294750"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Tutorial: Erstellen einer Web-API mit ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)

In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Web-API-Projekts
> * Hinzufügen einer Modellklasse und eines Datenbankkontexts
> * Erstellen eines Controllergerüsts mit CRUD-Methoden
> * Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte
> * Aufrufen der Web-API mit Postman

Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.

## <a name="overview"></a>Übersicht

In diesem Tutorial wird die folgende API erstellt:

|API | Beschreibung | Anforderungstext | Antworttext |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Alle To-do-Elemente abrufen | Keine | Array von To-do-Elementen|
|GET /api/TodoItems/{id} | Ein Element nach ID abrufen | Keine | To-do-Element|
|POST /api/TodoItems | Neues Element hinzufügen | To-do-Element | To-do-Element |
|PUT /api/TodoItems/{id} | Vorhandenes Element aktualisieren &nbsp; | To-do-Element | Keine |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Löschen eines Elements &nbsp; &nbsp; | Keine | Keine|

Das folgende Diagramm zeigt den Entwurf der App.

![Der Client ist das Feld ganz links. Er sendet eine Anforderung und erhält von der Anwendung (Feld auf der rechten Seite) eine Antwort. Im Anwendungsfeld stellen drei Felder den Controller, das Modell und die Datenzugriffsschicht dar. Die Anforderung geht im Controller der Anwendung ein, und Lese-/Schreibvorgänge erfolgen zwischen Controller und Datenzugriffsschicht. Das Modell wird serialisiert und in der Antwort an den Client zurückgegeben.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a>Erstellen eines Webprojekts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.
* Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.
* Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind. Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.
* Führen Sie die folgenden Befehle aus:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.

  Die obenstehenden Befehle haben folgende Konsequenzen:

  * Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.
  * Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren**für das **Zielframework** den Eintrag * *.NET Core 3.1* aus.

* Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testen der API

Die Projektvorlage erstellt eine `WeatherForecast`-API. Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Drücken Sie STRG+F5, um die App auszuführen. Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.

Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus. Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Drücken Sie STRG+F5, um die App auszuführen. Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten. Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt. Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben. Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.

---

Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a>Hinzufügen einer Modellklasse

Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet. Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Fügen Sie einen Ordner namens *Modelle* hinzu.

* Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.

* Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.

Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.

## <a name="add-a-database-context"></a>Hinzufügen eines Datenbankkontexts

Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert. Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer

* Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .
* Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.
* Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.
* Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.
* Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a>Hinzufügen des TodoContext-Datenbankkontexts

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.

---

* Geben Sie den folgenden Code ein:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden. Der Container stellt den Dienst für Controller bereit.

Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

Der vorangehende Code:

* Entfernt nicht benötigte `using`-Deklarationen
* Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu
* Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet

## <a name="scaffold-a-controller"></a>Erstellen eines Controllergerüsts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.
* Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.
* Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.
* Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:

  * Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.
  * Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.
  * Wählen Sie **Hinzufügen** aus.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Führen Sie die folgenden Befehle aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Die NuGet-Pakete für den Gerüstbau werden hinzufügt.
* Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.
* Das Gerüst für `TodoItemsController` wird erstellt.

---

Der generierte Code hat folgende Auswirkungen:

* Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut. Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert. Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.
* Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

## <a name="examine-the-posttodoitem-create-method"></a>Überblick über die PostTodoItem-Erstellungsmethode

Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben. Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.

Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:

* Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist. HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.
* Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu. Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest. Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers. Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.

### <a name="install-postman"></a>Installieren von Postman

Dieses Tutorial verwendet Postman zum Testen der Web-API.

* Installieren Sie [Postman](https://www.getpostman.com/downloads/).
* Starten Sie die Web-App.
* Starten Sie Postman.
* Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).
  * Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).
    > [!WARNING]
    > Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Testen von PostTodoItem mit Postman

* Erstellen Sie eine neue Anforderung.
* Legen Sie als HTTP-Methode `POST` fest.
* Wählen Sie die Registerkarte **Body** (Text) aus.
* Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.
* Legen Sie den Typ auf **JSON (application/json)** fest.
* Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wählen Sie **Send** (Senden) aus.

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a>Testen des Adressheader-URIs

* Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.
* Kopieren Sie den den Headerwert von **Location** (Speicherort):

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* Legen Sie die Methode auf „GET“ fest.
* Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.
* Wählen Sie **Send** (Senden) aus.

## <a name="examine-the-get-methods"></a>Überblick über die GET-Methoden

Diese Methoden implementieren zwei GET-Endpunkte:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen. Zum Beispiel:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Testen von GET mit Postman

* Erstellen Sie eine neue Anforderung.
* Legen Sie die HTTP-Methode auf **GET** fest.
* Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest. Beispielsweise `https://localhost:5001/api/TodoItems`.
* Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.
* Wählen Sie **Send** (Senden) aus.

Diese App verwendet eine In-Memory-Datenbank. Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben. Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.

## <a name="routing-and-url-paths"></a>Routing und URL-Pfade

Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet. Der URL-Pfad für jede Methode wird wie folgt erstellt:

* Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt. In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“. Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.
* Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an. In diesem Beispiel wird keine Vorlage verwendet. Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements. Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Rückgabewerte

Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht. Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler. Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.

`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen. Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:

* Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.
* Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück. Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.

## <a name="the-puttodoitem-method"></a>PutTodoItem-Methode

Untersuchen Sie die `PutTodoItem`-Methode.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT. Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet. Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.

Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.

### <a name="test-the-puttodoitem-method"></a>Testen der PutTodoItem-Methode

In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird. Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen. Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.

Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Die folgende Abbildung zeigt das Postman-Update:

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a>DeleteTodoItem-Methode

Untersuchen Sie die `DeleteTodoItem`-Methode.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a>Testen der DeleteTodoItem-Methode

So löschen Sie mit Postman eine Aufgabe

* Legen Sie die Methode auf `DELETE` fest.
* Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.
* Wählen Sie **Send** (Senden) aus.

## <a name="call-the-web-api-with-javascript"></a>Aufrufen der Web-API mit JavaScript

Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Web-API-Projekts
> * Hinzufügen einer Modellklasse und eines Datenbankkontexts
> * Hinzufügen eines Controllers
> * Hinzufügen von CRUD-Methoden
> * Konfigurieren von Routing und URL-Pfaden
> * Angeben von Rückgabewerten
> * Aufrufen der Web-API mit Postman
> * Aufrufen der Web-API mit JavaScript

Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.

## <a name="overview"></a>Übersicht

In diesem Tutorial wird die folgende API erstellt:

|API | Beschreibung | Anforderungstext | Antworttext |
|--- | ---- | ---- | ---- |
|GET /api/TodoItems | Alle To-do-Elemente abrufen | Keine | Array von To-do-Elementen|
|GET /api/TodoItems/{id} | Ein Element nach ID abrufen | Keine | To-do-Element|
|POST /api/TodoItems | Neues Element hinzufügen | To-do-Element | To-do-Element |
|PUT /api/TodoItems/{id} | Vorhandenes Element aktualisieren &nbsp; | To-do-Element | Keine |
|DELETE /api/TodoItems/{id} &nbsp; &nbsp; | Löschen eines Elements &nbsp; &nbsp; | Keine | Keine|

Das folgende Diagramm zeigt den Entwurf der App.

![Der Client ist das Feld ganz links. Er sendet eine Anforderung und erhält von der Anwendung (Feld auf der rechten Seite) eine Antwort. Im Anwendungsfeld stellen drei Felder den Controller, das Modell und die Datenzugriffsschicht dar. Die Anforderung geht im Controller der Anwendung ein, und Lese-/Schreibvorgänge erfolgen zwischen Controller und Datenzugriffsschicht. Das Modell wird serialisiert und in der Antwort an den Client zurückgegeben.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>Erstellen eines Webprojekts

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.
* Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.
* Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind. Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**. Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.
* Führen Sie die folgenden Befehle aus:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.

* Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie auf **Datei** > **Neue Projektmappe**.

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von * *.NET Core 2.2*.

* Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testen der API

Die Projektvorlage erstellt eine `values`-API. Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Drücken Sie STRG+F5, um die App auszuführen. Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.

Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus. Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Drücken Sie STRG+F5, um die App auszuführen. Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten. Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt. Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben. Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.

---

Die folgende JSON-Datei wird zurückgegeben:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Hinzufügen einer Modellklasse

Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet. Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Fügen Sie einen Ordner namens *Modelle* hinzu.

* Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.

* Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.

Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.

## <a name="add-a-database-context"></a>Hinzufügen eines Datenbankkontexts

Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert. Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.

---

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden. Der Container stellt den Dienst für Controller bereit.

Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

Der vorangehende Code:

* Entfernt nicht benötigte `using`-Deklarationen
* Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu
* Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.
* Wählen Sie **Hinzufügen** > **Neues Element** aus.
* Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.
* Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.

---

* Ersetzen Sie den Vorlagencode durch den folgenden Code:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

Der vorangehende Code:

* Definiert eine API-Controllerklasse ohne Methoden.
* Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut. Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert. Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.
* Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.
* Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu. Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt. Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`. So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.

## <a name="add-get-methods"></a>Hinzufügen von GET-Methoden

Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Diese Methoden implementieren zwei GET-Endpunkte:

* `GET /api/todo`
* `GET /api/todo/{id}`

Halten Sie die App an, falls diese noch ausgeführt wird. Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.

Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen. Zum Beispiel:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Routing und URL-Pfade

Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet. Der URL-Pfad für jede Methode wird wie folgt erstellt:

* Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt. Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“. Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.
* Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an. In diesem Beispiel wird keine Vorlage verwendet. Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements. Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Rückgabewerte

Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type). ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht. Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler. Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.

`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen. Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:

* Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.
* Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück. Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.

## <a name="test-the-gettodoitems-method"></a>Testen der GetTodoItems-Methode

Dieses Tutorial verwendet Postman zum Testen der Web-API.

* Installieren Sie [Postman](https://www.getpostman.com/downloads/).
* Starten Sie die Web-App.
* Starten Sie Postman.
* Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats). Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.

---
  
> [!WARNING]
> Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.

* Erstellen Sie eine neue Anforderung.
  * Legen Sie die HTTP-Methode auf **GET** fest.
  * Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest. Beispielsweise `https://localhost:5001/api/todo`.
* Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.
* Wählen Sie **Send** (Senden) aus.

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Hinzufügen einer Methode

Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu: 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben. Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.

Die `CreatedAtAction`-Methode:

* Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück. HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.
* Fügt der Antwort einen `Location`-Header hinzu. Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück. Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers. Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Testen der PostTodoItem-Methode

* Erstellen Sie das Projekt.
* Legen Sie in Postman die HTTP-Methode auf `POST` fest.
* Wählen Sie die Registerkarte **Body** (Text) aus.
* Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.
* Legen Sie den Typ auf **JSON (application/json)** fest.
* Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Wählen Sie **Send** (Senden) aus.

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.

### <a name="test-the-location-header-uri"></a>Testen des Adressheader-URIs

* Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.
* Kopieren Sie den den Headerwert von **Location** (Speicherort):

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* Legen Sie die Methode auf „GET“ fest.
* Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.
* Wählen Sie **Send** (Senden) aus.

## <a name="add-a-puttodoitem-method"></a>Hinzufügen einer PutTodoItem-Methode

Fügen Sie die folgende `PutTodoItem`-Methode hinzu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT. Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet. Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.

Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.

### <a name="test-the-puttodoitem-method"></a>Testen der PutTodoItem-Methode

In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird. Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen. Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.

Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

Die folgende Abbildung zeigt das Postman-Update:

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Hinzufügen einer DeleteTodoItem-Methode

Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testen der DeleteTodoItem-Methode

So löschen Sie mit Postman eine Aufgabe

* Legen Sie die Methode auf `DELETE` fest.
* Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.
* Wählen Sie **Send** (Senden) aus.

Sie können in der Beispiel-App alle Elemente löschen. Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.

## <a name="call-the-web-api-with-javascript"></a>Aufrufen der Web-API mit JavaScript

In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft. jQuery initiiert die Anforderung. JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.

Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.

Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu. Ersetzen Sie den Inhalt durch folgendes Markup:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu. Ersetzen Sie den Inhalt durch folgenden Code:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:

* Öffnen Sie *Properties\launchSettings.json*.
* Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.

Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf. Im Folgenden werden die API-Aufrufe erläutert.

### <a name="get-a-list-of-to-do-items"></a>Abrufen einer Liste von To-Do-Elementen

jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt. Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist. Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Hinzufügen eines To-Do-Elements

jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext. Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben. Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert. Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualisieren eines To-Do-Elements

Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe. Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Löschen eines To-Do-Elements

Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Hinzufügen der Authentifizierungsunterstützung zu einer Web-API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples) [Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=TTkhEyGBfAk)
