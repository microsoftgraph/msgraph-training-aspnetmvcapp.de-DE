<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="42880-101">In dieser Demo werden Sie Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="42880-101">In this demo you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="42880-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Client Bibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Aufrufe von Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="42880-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="42880-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="42880-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="42880-104">Erweitern Sie zunächst die `GraphHelper` Klasse, die Sie im letzten Modul erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="42880-104">Start by extending the `GraphHelper` class you created in the last module.</span></span> <span data-ttu-id="42880-105">Fügen Sie zunächst die folgenden `using` Anweisungen am Anfang der `Helpers/GraphHelper.cs` Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="42880-105">First, add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using Microsoft.Identity.Client;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Claims;
using System.Web;
```

<span data-ttu-id="42880-106">Fügen Sie dann den folgenden Code zur `GraphHelper` Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="42880-106">Then add the following code to the `GraphHelper` class.</span></span>

```cs
// Load configuration settings from PrivateSettings.config
private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    var graphClient = GetAuthenticatedClient();

    var events = await graphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    return events.CurrentPage;
}

private static GraphServiceClient GetAuthenticatedClient()
{
    return new GraphServiceClient(
        new DelegateAuthenticationProvider(
            async (requestMessage) =>
            {
                // Get the signed in user's id and create a token cache
                string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
                SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
                    new HttpContextWrapper(HttpContext.Current));

                var idClient = new ConfidentialClientApplication(
                    appId, redirectUri, new ClientCredential(appSecret),
                    tokenStore.GetMsalCacheInstance(), null);

                var accounts = await idClient.GetAccountsAsync();

                // By calling this here, the token can be refreshed
                // if it's expired right before the Graph call is made
                var result = await idClient.AcquireTokenSilentAsync(
                    graphScopes.Split(' '), accounts.FirstOrDefault());

                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("Bearer", result.AccessToken);
            }));
}
```

<span data-ttu-id="42880-107">Überlegen Sie sich, was dieser Code tut.</span><span class="sxs-lookup"><span data-stu-id="42880-107">Consider what this code is doing.</span></span>

- <span data-ttu-id="42880-108">Die `GetAuthenticatedClient` Funktion initialisiert eine `GraphServiceClient` mit einem Authentifizierungsanbieter, `AcquireTokenSilentAsync`der aufruft.</span><span class="sxs-lookup"><span data-stu-id="42880-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilentAsync`.</span></span>
- <span data-ttu-id="42880-109">In der `GetEventsAsync` -Funktion:</span><span class="sxs-lookup"><span data-stu-id="42880-109">In the `GetEventsAsync` function:</span></span>
  - <span data-ttu-id="42880-110">Die URL, die aufgerufen wird, `/v1.0/me/events`lautet.</span><span class="sxs-lookup"><span data-stu-id="42880-110">The URL that will be called is `/v1.0/me/events`.</span></span>
  - <span data-ttu-id="42880-111">Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die die Ansicht tatsächlich verwendet.</span><span class="sxs-lookup"><span data-stu-id="42880-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
  - <span data-ttu-id="42880-112">Die `OrderBy` Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu denen Sie erstellt wurden, wobei das neueste Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="42880-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="42880-113">Erstellen Sie nun einen Controller für die Kalenderansichten.</span><span class="sxs-lookup"><span data-stu-id="42880-113">Now create a controller for the calendar views.</span></span> <span data-ttu-id="42880-114">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **> Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="42880-114">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="42880-115">Benennen Sie den `CalendarController` Controller, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="42880-115">Name the controller `CalendarController` and choose **Add**.</span></span> <span data-ttu-id="42880-116">Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="42880-116">Replace the entire contents of the new file with the following code.</span></span>

```cs
using graph_tutorial.Helpers;
using System.Threading.Tasks;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class CalendarController : BaseController
    {
        // GET: Calendar
        [Authorize]
        public async Task<ActionResult> Index()
        {
            var events = await GraphHelper.GetEventsAsync();
            return Json(events, JsonRequestBehavior.AllowGet);
        }
    }
}
```

<span data-ttu-id="42880-117">Jetzt können Sie dies testen.</span><span class="sxs-lookup"><span data-stu-id="42880-117">Now you can test this.</span></span> <span data-ttu-id="42880-118">Starten Sie die APP, melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="42880-118">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="42880-119">Wenn alles funktioniert, sollte ein JSON-Dump von Ereignissen im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="42880-119">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="42880-120">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="42880-120">Display the results</span></span>

<span data-ttu-id="42880-121">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42880-121">Now you can add a view to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="42880-122">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **views/Calendar** , und wählen Sie **> Ansicht hinzufügen**.... Benennen Sie die `Index` Ansicht, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="42880-122">In Solution Explorer, right-click the **Views/Calendar** folder and choose **Add > View...**. Name the view `Index` and choose **Add**.</span></span> <span data-ttu-id="42880-123">Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="42880-123">Replace the entire contents of the new file with the following code.</span></span>

```html
@model IEnumerable<Microsoft.Graph.Event>

@{
    ViewBag.Current = "Calendar";
}

<h1>Calendar</h1>
<table class="table">
    <thead>
        <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.Organizer.EmailAddress.Name</td>
                <td>@item.Subject</td>
                <td>@Convert.ToDateTime(item.Start.DateTime).ToString("M/d/yy h:mm tt")</td>
                <td>@Convert.ToDateTime(item.End.DateTime).ToString("M/d/yy h:mm tt")</td>
            </tr>
        }
    </tbody>
</table>
```

<span data-ttu-id="42880-124">, Der eine Auflistung von Ereignissen durchläuft und jeweils eine Tabellenzeile hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="42880-124">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="42880-125">Entfernen Sie `return Json(events, JsonRequestBehavior.AllowGet);` die- `Index` Funktion in `Controllers/CalendarController.cs`, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="42880-125">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

```cs
return View(events);
```

<span data-ttu-id="42880-126">Starten Sie die APP, melden Sie sich an, und klicken Sie auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="42880-126">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="42880-127">Die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="42880-127">The app should now render a table of events.</span></span>

![Screenshot der Ereignistabelle](./images/add-msgraph-01.png)