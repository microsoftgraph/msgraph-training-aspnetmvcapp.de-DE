<!-- markdownlint-disable MD002 MD041 -->

Öffnen Sie Visual Studio, und wählen Sie **Datei _GT_ neues >-Projekt**aus. Führen Sie im Dialogfeld **Neues Projekt** folgende Aktionen aus:

1. Wählen Sie **Vorlagen _GT_ Visual C# _GT_ Web**aus.
1. Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** aus.
1. Geben Sie **Graph-Tutorial** als Namen für das Projekt ein.

![Dialogfeld zum Erstellen eines neuen Projekts in Visual Studio 2017](./images/vs-new-project-01.png)

> [!NOTE]
> Stellen Sie sicher, dass Sie genau denselben Namen für das Visual Studio-Projekt eingeben, das in diesen Lab-Anweisungen angegeben ist. Der Visual Studio-Projektname wird Teil des Namespaces im Code. Der Code in diesen Anweisungen hängt vom Namespace ab, der dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen entspricht. Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie mit dem Visual Studio-Projektnamen übereinstimmen, den Sie beim Erstellen des Projekts eingeben.

Wählen Sie **OK** aus. Wählen Sie im Dialogfeld **neues ASP.NET-Webanwendungsprojekt** die Option **MVC** (unter **ASP.NET 4.7.2 Templates**) aus, und wählen Sie **OK**aus.

Drücken Sie **F5** , oder klicken Sie auf Debuggen **> Start Debugging**. Wenn alles funktioniert, sollte Ihr Standardbrowser eine ASP.NET-Standardseite öffnen und anzeigen.

Bevor Sie fortfahren, aktualisieren `bootstrap` Sie das NuGet-Paket, und installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.

- [Microsoft. Owin. Host. SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) , um die [Owin](http://owin.org/) -schnittstellen in der ASP.NET-Anwendung zu aktivieren.
- [Microsoft. Owin. Security. OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) zum Ausführen der OpenID Connect-Authentifizierung mit Azure.
- [Microsoft. Owin. Security. Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) zur Aktivierung der Cookie-basierten Authentifizierung.
- [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) zum Anfordern und Verwalten von Zugriffstoken.
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für Aufrufe von Microsoft Graph.

Wählen Sie **Extras _GT_ NuGet Paket-Manager _GT_ Paket-Manager-Konsole**aus. Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein.

```Powershell
Update-Package bootstrap
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Identity.Client -Version 2.7.0
Install-Package Microsoft.Graph -Version 1.11.0
```

Erstellen Sie eine grundlegende OWIN-Startklasse. Klicken Sie im Projekt `graph-tutorial` Mappen-Explorer mit der rechten Maustaste auf den Ordner, und wählen Sie **_GT_ neues Element hinzufügen**. Wählen Sie die **OWIN-Startklassen** Vorlage aus, `Startup.cs`nennen Sie die Datei, und wählen Sie **Hinzufügen**aus.

## <a name="design-the-app"></a>Entwerfen der APP

Erstellen Sie zunächst ein einfaches Modell für eine Fehlermeldung. Sie verwenden dieses Modell, um Fehlermeldungen in den Ansichten der APP zu flashen.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle** , und wählen Sie **_GT_-Klasse hinzufügen**.... Benennen Sie die `Alert` Klasse, und wählen Sie **Hinzufügen**. Fügen Sie den folgenden Code `Alert.cs`in hinzu.

```cs
namespace graph_tutorial.Models
{
    public class Alert
    {
        public const string AlertKey = "TempDataAlerts";
        public string Message { get; set; }
        public string Debug { get; set; }
    }
}
```

Aktualisieren Sie nun das globale Layout der app. Öffnen Sie `./Views/Shared/_Layout.cshtml` die Datei, und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

```html
@{
    var alerts = TempData.ContainsKey(graph_tutorial.Models.Alert.AlertKey) ?
        (List<graph_tutorial.Models.Alert>)TempData[graph_tutorial.Models.Alert.AlertKey] :
        new List<graph_tutorial.Models.Alert>();
}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ASP.NET Graph Tutorial</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")

    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
          integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt"
          crossorigin="anonymous">
</head>

<body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
        <div class="container">
            @Html.ActionLink("ASP.NET Graph Tutorial", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
                    aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        @Html.ActionLink("Home", "Index", "Home", new { area = "" },
                            new { @class = ViewBag.Current == "Home" ? "nav-link active" : "nav-link" })
                    </li>
                    @if (Request.IsAuthenticated)
                    {
                        <li class="nav-item" data-turbolinks="false">
                            @Html.ActionLink("Calendar", "Index", "Calendar", new { area = "" },
                                new { @class = ViewBag.Current == "Calendar" ? "nav-link active" : "nav-link" })
                        </li>
                    }
                </ul>
                <ul class="navbar-nav justify-content-end">
                    <li class="nav-item">
                        <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                            <i class="fas fa-external-link-alt mr-1"></i>Docs
                        </a>
                    </li>
                    @if (Request.IsAuthenticated)
                    {
                        <li class="nav-item dropdown">
                            <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                                @if (!string.IsNullOrEmpty(ViewBag.User.Avatar))
                                {
                                    <img src="@ViewBag.User.Avatar" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                                }
                                else
                                {
                                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                                }
                            </a>
                            <div class="dropdown-menu dropdown-menu-right">
                                <h5 class="dropdown-item-text mb-0">@ViewBag.User.DisplayName</h5>
                                <p class="dropdown-item-text text-muted mb-0">@ViewBag.User.Email</p>
                                <div class="dropdown-divider"></div>
                                @Html.ActionLink("Sign Out", "SignOut", "Account", new { area = "" }, new { @class = "dropdown-item" })
                            </div>
                        </li>
                    }
                    else
                    {
                        <li class="nav-item">
                            @Html.ActionLink("Sign In", "SignIn", "Account", new { area = "" }, new { @class = "nav-link" })
                        </li>
                    }
                </ul>
            </div>
        </div>
    </nav>
    <main role="main" class="container">
        @foreach (var alert in alerts)
        {
            <div class="alert alert-danger" role="alert">
                <p class="mb-3">@alert.Message</p>
                @if (!string.IsNullOrEmpty(alert.Debug))
                {
                    <pre class="alert-pre border bg-light p-2"><code>@alert.Debug</code></pre>
                }
            </div>
        }

        @RenderBody()
    </main>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

Dieser Code fügt [Bootstrap](https://getbootstrap.com/) für einfache Styling und [Font awesome](https://fontawesome.com/) für einige einfache Symbole. Außerdem wird ein globales Layout mit einer NAV-Leiste definiert, und `Alert` die Klasse wird verwendet, um Warnungen anzuzeigen.

Öffnen Sie `Content/Site.css` nun den gesamten Inhalt, und ersetzen Sie ihn durch den folgenden Code.

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Aktualisieren Sie nun die Standardseite. Öffnen Sie `Views/Home/index.cshtml` die Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.

```html
@{
    ViewBag.Current = "Home";
}

<div class="jumbotron">
    <h1>ASP.NET Graph Tutorial</h1>
    <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from ASP.NET</p>
    @if (Request.IsAuthenticated)
    {
        <h4>Welcome @ViewBag.User.DisplayName!</h4>
        <p>Use the navigation bar at the top of the page to get started.</p>
    }
    else
    {
        @Html.ActionLink("Click here to sign in", "SignIn", "Account", new { area = "" }, new { @class = "btn btn-primary btn-large" })
    }
</div>
```

Fügen Sie nun eine Hilfsfunktion hinzu, `Alert` um eine zu erstellen und an die Ansicht zu übermitteln. Definieren Sie eine Basis Controllerklasse, um Sie für jeden von uns erstellten Controller problemlos zur Verfügung zu stellen.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **_GT_ Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus. Benennen Sie den `BaseController` Controller, und wählen Sie **Hinzufügen**. Ersetzen Sie den Inhalt von `BaseController.cs` durch den folgenden Code.

```cs
using graph_tutorial.Models;
using System.Collections.Generic;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public abstract class BaseController : Controller
    {
        protected void Flash(string message, string debug=null)
        {
            var alerts = TempData.ContainsKey(Alert.AlertKey) ?
                (List<Alert>)TempData[Alert.AlertKey] :
                new List<Alert>();

            alerts.Add(new Alert
            {
                Message = message,
                Debug = debug
            });

            TempData[Alert.AlertKey] = alerts;
        }
    }
}
```

Jeder Controller kann von dieser Basis Controllerklasse erben, um Zugriff auf die `Flash` Funktion zu erhalten. Aktualisieren Sie `HomeController` die Klasse, von `BaseController`der geerbt werden soll. Öffnen `Controllers/HomeController.cs` Sie die folgende `public class HomeController : Controller` Codezeile, und ändern Sie Sie:

```cs
public class HomeController : BaseController
```

Speichern Sie alle Änderungen, und starten Sie den Server neu. Nun sollte die APP sehr unterschiedlich aussehen.

![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)