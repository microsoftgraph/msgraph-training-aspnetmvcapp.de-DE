<!-- markdownlint-disable MD002 MD041 -->

In dieser Demo werden Sie Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft Graph-Client Bibliothek für .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Aufrufe von Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen aus Outlook

Erweitern Sie zunächst die `GraphHelper` Klasse, die Sie im letzten Modul erstellt haben. Fügen Sie zunächst die folgenden `using` Anweisungen am Anfang der `Helpers/GraphHelper.cs` Datei hinzu.

```cs
using graph_tutorial.TokenStorage;
using Microsoft.Identity.Client;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Claims;
using System.Web;
```

Fügen Sie dann den folgenden Code zur `GraphHelper` Klasse hinzu.

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

Überlegen Sie sich, was dieser Code tut.

- Die `GetAuthenticatedClient` Funktion initialisiert eine `GraphServiceClient` mit einem Authentifizierungsanbieter, `AcquireTokenSilentAsync`der aufruft.
- In der `GetEventsAsync` -Funktion:
  - Die URL, die aufgerufen wird, `/v1.0/me/events`lautet.
  - Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die die Ansicht tatsächlich verwendet.
  - Die `OrderBy` Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu denen Sie erstellt wurden, wobei das neueste Element zuerst angezeigt wird.

Erstellen Sie nun einen Controller für die Kalenderansichten. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **_GT_ Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus. Benennen Sie den `CalendarController` Controller, und wählen Sie **Hinzufügen**. Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.

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

Jetzt können Sie dies testen. Starten Sie die APP, melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** . Wenn alles funktioniert, sollte ein JSON-Dump von Ereignissen im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie eine Ansicht hinzufügen, um die Ergebnisse benutzerfreundlicher anzuzeigen. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **views/Calendar** , und wählen Sie **_GT_ Ansicht hinzufügen**.... Benennen Sie die `Index` Ansicht, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den gesamten Inhalt der neuen Datei durch den folgenden Code.

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

, Der eine Auflistung von Ereignissen durchläuft und jeweils eine Tabellenzeile hinzufügt. Entfernen Sie `return Json(events, JsonRequestBehavior.AllowGet);` die- `Index` Funktion in `Controllers/CalendarController.cs`, und ersetzen Sie Sie durch den folgenden Code.

```cs
return View(events);
```

Starten Sie die APP, melden Sie sich an, und klicken Sie auf den Link **Kalender** . Die APP sollte jetzt eine Tabelle mit Ereignissen rendern.

![Screenshot der Ereignistabelle](./images/add-msgraph-01.png)