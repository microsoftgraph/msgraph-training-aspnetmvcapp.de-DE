<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4b5b6-101">Öffnen Sie Visual Studio, und wählen Sie **Datei > neues >-Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="4b5b6-102">Führen Sie im Dialogfeld **Neues Projekt** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="4b5b6-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="4b5b6-103">Wählen Sie **Vorlagen > Visual C# > Web**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-103">Select **Templates > Visual C# > Web**.</span></span>
1. <span data-ttu-id="4b5b6-104">Wählen Sie **ASP.NET-Webanwendung (.NET Framework)** aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-104">Select **ASP.NET Web Application (.NET Framework)**.</span></span>
1. <span data-ttu-id="4b5b6-105">Geben Sie **Graph-Tutorial** als Namen für das Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-105">Enter **graph-tutorial** for the Name of the project.</span></span>

![Dialogfeld zum Erstellen eines neuen Projekts in Visual Studio 2017](./images/vs-new-project-01.png)

> [!NOTE]
> <span data-ttu-id="4b5b6-107">Stellen Sie sicher, dass Sie genau denselben Namen für das Visual Studio-Projekt eingeben, das in diesen Lab-Anweisungen angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="4b5b6-108">Der Visual Studio-Projektname wird Teil des Namespaces im Code.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="4b5b6-109">Der Code in diesen Anweisungen hängt vom Namespace ab, der dem in diesen Anweisungen angegebenen Visual Studio-Projektnamen entspricht.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="4b5b6-110">Wenn Sie einen anderen Projektnamen verwenden, wird der Code nicht kompiliert, es sei denn, Sie passen alle Namespaces so an, dass Sie mit dem Visual Studio-Projektnamen übereinstimmen, den Sie beim Erstellen des Projekts eingeben.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

<span data-ttu-id="4b5b6-111">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-111">Select **OK**.</span></span> <span data-ttu-id="4b5b6-112">Wählen Sie im Dialogfeld **Neues ASP.NET-Webanwendungsprojekt** die Option **MVC** (unter **ASP.NET 4.7.2 Templates**) aus, und wählen Sie **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-112">In the **New ASP.NET Web Application Project** dialog, select **MVC** (under **ASP.NET 4.7.2 Templates**) and select **OK**.</span></span>

<span data-ttu-id="4b5b6-113">Drücken Sie **F5** , oder klicken Sie auf Debuggen **> Start Debugging**.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-113">Press **F5** or select **Debug > Start Debugging**.</span></span> <span data-ttu-id="4b5b6-114">Wenn alles funktioniert, sollte Ihr Standardbrowser eine ASP.NET-Standardseite öffnen und anzeigen.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-114">If everything is working, your default browser should open and display a default ASP.NET page.</span></span>

<span data-ttu-id="4b5b6-115">Bevor Sie fortfahren, aktualisieren `bootstrap` Sie das NuGet-Paket, und installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-115">Before moving on, update the `bootstrap` NuGet package, and install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="4b5b6-116">[Microsoft. Owin. Host. SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) , um die [Owin](http://owin.org/) -Schnittstellen in der ASP.NET-Anwendung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-116">[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) to enable the [OWIN](http://owin.org/) interfaces in the ASP.NET application.</span></span>
- <span data-ttu-id="4b5b6-117">[Microsoft. Owin. Security. OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) zum Ausführen der OpenID Connect-Authentifizierung mit Azure.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-117">[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) for doing OpenID Connect authentication with Azure.</span></span>
- <span data-ttu-id="4b5b6-118">[Microsoft. Owin. Security. Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) zur Aktivierung der Cookie-basierten Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-118">[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) to enable cookie-based authentication.</span></span>
- <span data-ttu-id="4b5b6-119">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) zum Anfordern und Verwalten von Zugriffstoken.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-119">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="4b5b6-120">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) für Aufrufe von Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-120">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="4b5b6-121">Wählen Sie **Extras > NuGet Paket-Manager > Paket-Manager-Konsole**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-121">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="4b5b6-122">Geben Sie in der Paket-Manager-Konsole die folgenden Befehle ein.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-122">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Update-Package bootstrap
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Identity.Client -Version 2.7.0
Install-Package Microsoft.Graph -Version 1.11.0
```

<span data-ttu-id="4b5b6-123">Erstellen Sie eine grundlegende OWIN-Startklasse.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-123">Create a basic OWIN startup class.</span></span> <span data-ttu-id="4b5b6-124">Klicken Sie im Projekt `graph-tutorial` Mappen-Explorer mit der rechten Maustaste auf den Ordner, und wählen Sie **> neues Element hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-124">Right-click the `graph-tutorial` folder in Solution Explorer and choose **Add > New Item**.</span></span> <span data-ttu-id="4b5b6-125">Wählen Sie die **OWIN-Startklassen** Vorlage aus, `Startup.cs`nennen Sie die Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-125">Choose the **OWIN Startup Class** template, name the file `Startup.cs`, and choose **Add**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="4b5b6-126">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="4b5b6-126">Design the app</span></span>

<span data-ttu-id="4b5b6-127">Erstellen Sie zunächst ein einfaches Modell für eine Fehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-127">Start by creating a simple model for an error message.</span></span> <span data-ttu-id="4b5b6-128">Sie verwenden dieses Modell, um Fehlermeldungen in den Ansichten der APP zu flashen.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-128">You'll use this model to flash error messages in the app's views.</span></span>

<span data-ttu-id="4b5b6-129">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle** , und wählen Sie **>-Klasse hinzufügen**.... Benennen Sie die `Alert` Klasse, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-129">Right-click the **Models** folder in Solution Explorer and choose **Add > Class...**. Name the class `Alert` and choose **Add**.</span></span> <span data-ttu-id="4b5b6-130">Fügen Sie den folgenden Code `Alert.cs`in hinzu.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-130">Add the following code in `Alert.cs`.</span></span>

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

<span data-ttu-id="4b5b6-131">Aktualisieren Sie nun das globale Layout der app.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-131">Now update the global layout of the app.</span></span> <span data-ttu-id="4b5b6-132">Öffnen Sie `./Views/Shared/_Layout.cshtml` die Datei, und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-132">Open the `./Views/Shared/_Layout.cshtml` file, and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="4b5b6-133">Dieser Code fügt [Bootstrap](https://getbootstrap.com/) für einfache Styling und [Font awesome](https://fontawesome.com/) für einige einfache Symbole.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-133">This code adds [Bootstrap](https://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="4b5b6-134">Außerdem wird ein globales Layout mit einer NAV-Leiste definiert, und `Alert` die Klasse wird verwendet, um Warnungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-134">It also defines a global layout with a nav bar, and uses the `Alert` class to display any alerts.</span></span>

<span data-ttu-id="4b5b6-135">Öffnen Sie `Content/Site.css` nun den gesamten Inhalt, und ersetzen Sie ihn durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-135">Now open `Content/Site.css` and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="4b5b6-136">Aktualisieren Sie nun die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-136">Now update the default page.</span></span> <span data-ttu-id="4b5b6-137">Öffnen Sie `Views/Home/index.cshtml` die Datei, und ersetzen Sie Ihren Inhalt durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-137">Open the `Views/Home/index.cshtml` file and replace its contents with the following.</span></span>

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

<span data-ttu-id="4b5b6-138">Fügen Sie nun eine Hilfsfunktion hinzu, `Alert` um eine zu erstellen und an die Ansicht zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-138">Now add a helper function to create an `Alert` and pass it to the view.</span></span> <span data-ttu-id="4b5b6-139">Definieren Sie eine Basis Controllerklasse, um Sie für jeden von uns erstellten Controller problemlos zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-139">In order to make it easily available to any controller we create, define a base controller class.</span></span>

<span data-ttu-id="4b5b6-140">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **> Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-140">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="4b5b6-141">Benennen Sie den `BaseController` Controller, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-141">Name the controller `BaseController` and choose **Add**.</span></span> <span data-ttu-id="4b5b6-142">Ersetzen Sie den Inhalt von `BaseController.cs` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-142">Replace the contents of `BaseController.cs` with the following code.</span></span>

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

<span data-ttu-id="4b5b6-143">Jeder Controller kann von dieser Basis Controllerklasse erben, um Zugriff auf die `Flash` Funktion zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-143">Any controller can inherit from this base controller class to gain access to the `Flash` function.</span></span> <span data-ttu-id="4b5b6-144">Aktualisieren Sie `HomeController` die Klasse, von `BaseController`der geerbt werden soll.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-144">Update the `HomeController` class to inherit from `BaseController`.</span></span> <span data-ttu-id="4b5b6-145">Öffnen `Controllers/HomeController.cs` Sie die folgende `public class HomeController : Controller` Codezeile, und ändern Sie Sie:</span><span class="sxs-lookup"><span data-stu-id="4b5b6-145">Open `Controllers/HomeController.cs` and change the `public class HomeController : Controller` line to:</span></span>

```cs
public class HomeController : BaseController
```

<span data-ttu-id="4b5b6-146">Speichern Sie alle Änderungen, und starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-146">Save all of your changes and restart the server.</span></span> <span data-ttu-id="4b5b6-147">Nun sollte die APP sehr unterschiedlich aussehen.</span><span class="sxs-lookup"><span data-stu-id="4b5b6-147">Now, the app should look very different.</span></span>

![Screenshot der neu gestalteten Homepage](./images/create-app-01.png)