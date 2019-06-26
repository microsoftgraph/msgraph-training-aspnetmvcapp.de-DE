<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="78166-101">In dieser Demo werden Sie Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="78166-101">In this demo you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="78166-102">Für diese Anwendung verwenden Sie die [Microsoft Graph-Clientbibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="78166-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="78166-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="78166-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="78166-104">Erweitern Sie zunächst die `GraphHelper` Klasse, die Sie im letzten Modul erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="78166-104">Start by extending the `GraphHelper` class you created in the last module.</span></span>

1. <span data-ttu-id="78166-105">Fügen Sie am `using` Anfang der `Helpers/GraphHelper.cs` Datei die folgenden Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="78166-105">Add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using Microsoft.Identity.Client;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Claims;
    using System.Web;
    ```

1. <span data-ttu-id="78166-106">Fügen Sie der `GraphHelper` -Klasse den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="78166-106">Add the following code to the `GraphHelper` class.</span></span>

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
                    var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                        .WithRedirectUri(redirectUri)
                        .WithClientSecret(appSecret)
                        .Build();

                    string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
                    var tokenStore = new SessionTokenStore(signedInUserId, HttpContext.Current);
                    tokenStore.Initialize(idClient.UserTokenCache);

                    var accounts = await idClient.GetAccountsAsync();

                    // By calling this here, the token can be refreshed
                    // if it's expired right before the Graph call is made
                    var scopes = graphScopes.Split(' ');
                    var result = await idClient.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="78166-107">Überprüfen Sie, was dieser Code tut.</span><span class="sxs-lookup"><span data-stu-id="78166-107">Consider what this code is doing.</span></span>
    >
    > - <span data-ttu-id="78166-108">Die `GetAuthenticatedClient` Funktion initialisiert a `GraphServiceClient` mit einem Authentifizierungsanbieter, `AcquireTokenSilent`der ruft.</span><span class="sxs-lookup"><span data-stu-id="78166-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilent`.</span></span>
    > - <span data-ttu-id="78166-109">In der `GetEventsAsync` -Funktion:</span><span class="sxs-lookup"><span data-stu-id="78166-109">In the `GetEventsAsync` function:</span></span>
    >   - <span data-ttu-id="78166-110">Die URL, die aufgerufen wird `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="78166-110">The URL that will be called is `/v1.0/me/events`.</span></span>
    >   - <span data-ttu-id="78166-111">Die `Select` -Funktion schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78166-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="78166-112">Die `OrderBy` -Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="78166-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="78166-113">Erstellen Sie einen Controller für die Kalenderansichten.</span><span class="sxs-lookup"><span data-stu-id="78166-113">Create a controller for the calendar views.</span></span> <span data-ttu-id="78166-114">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **#a0 Controller hinzufügen**aus.... Wählen Sie **MVC 5 Controller – leer** , und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="78166-114">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="78166-115">Nennen Sie den `CalendarController` Controller, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="78166-115">Name the controller `CalendarController` and select **Add**.</span></span> <span data-ttu-id="78166-116">Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="78166-116">Replace the entire contents of the new file with the following code.</span></span>

    ```cs
    using System;
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

                // Change start and end dates from UTC to local time
                foreach (var ev in events)
                {
                    ev.Start.DateTime = DateTime.Parse(ev.Start.DateTime).ToLocalTime().ToString();
                    ev.Start.TimeZone = TimeZoneInfo.Local.Id;
                    ev.End.DateTime = DateTime.Parse(ev.End.DateTime).ToLocalTime().ToString();
                    ev.End.TimeZone = TimeZoneInfo.Local.Id;
                }

                return Json(events, JsonRequestBehavior.AllowGet);
            }
        }
    }
    ```

1. <span data-ttu-id="78166-117">Starten Sie die APP, melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="78166-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="78166-118">Wenn alles funktioniert, sollte ein JSON-Abbild der Ereignisse im Kalender des Benutzers angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="78166-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="78166-119">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="78166-119">Display the results</span></span>

<span data-ttu-id="78166-120">Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse auf eine benutzerfreundlichere Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="78166-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="78166-121">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Ansichten/Kalender** , und wählen Sie **#a0 Ansicht hinzufügen**aus. Nennen Sie die `Index` Ansicht, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="78166-121">In Solution Explorer, right-click the **Views/Calendar** folder and select **Add > View...**. Name the view `Index` and select **Add**.</span></span> <span data-ttu-id="78166-122">Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="78166-122">Replace the entire contents of the new file with the following code.</span></span>

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

    <span data-ttu-id="78166-123">Dadurch wird eine Auflistung von Ereignissen durchlaufen und für jeden eine Tabellenzeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="78166-123">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="78166-124">Entfernen Sie `return Json(events, JsonRequestBehavior.AllowGet);` die- `Index` Funktion in `Controllers/CalendarController.cs`, und ersetzen Sie Sie durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="78166-124">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

    ```cs
    return View(events);
    ```

1. <span data-ttu-id="78166-125">Starten Sie die APP, melden Sie sich an, und klicken Sie auf den Link **Kalender** .</span><span class="sxs-lookup"><span data-stu-id="78166-125">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="78166-126">Die APP sollte jetzt eine Tabelle mit Ereignissen rendern.</span><span class="sxs-lookup"><span data-stu-id="78166-126">The app should now render a table of events.</span></span>

    ![Ein Screenshot der Ereignistabelle](./images/add-msgraph-01.png)
