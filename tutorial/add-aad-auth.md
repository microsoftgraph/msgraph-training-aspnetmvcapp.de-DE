<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung zur Unterstützung der Authentifizierung mit Azure AD. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken für den Aufruf von Microsoft Graph abzurufen. In diesem Schritt integrieren Sie die OWIN-Middleware und die [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung.

Klicken Sie im projektMappen-Explorer mit der rechten Maustaste auf das **Graph-Tutorial-** Projekt, und wählen Sie **_GT_ neues Element hinzufügen...**. Wählen **** Sie Webkonfigurationsdatei, benennen Sie die `PrivateSettings.config` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen sie den gesamten Inhalt durch den folgenden Code.

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie durch den von Ihnen generierten Client Schlüssel. Wenn Ihr geheimer Client-Schlüssel ein`&`kaufmännisches und-Zeichen enthält () `&amp;` , `PrivateSettings.config`müssen Sie diese durch in ersetzen. Achten Sie auch darauf, den `PORT` Wert für die `ida:RedirectUri` an die URL der Anwendung anzupassen.

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, um die `PrivateSettings.config` Datei aus der Quellcodeverwaltung auszuschließen, um versehentlich Ihre APP-ID und Ihr Kennwort zu verlieren.

Aktualisieren `Web.config` Sie, um diese neue Datei zu laden. Ersetzen Sie `<appSettings>` die (Leitung 7) durch Folgendes.

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Beginnen Sie damit, die OWIN-Middleware für die Verwendung der Azure AD-Authentifizierung für die APP zu initialisieren. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **App_Start** , und wählen Sie **Add > Class.**... Benennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den gesamten Inhalt durch den folgenden Code.

```cs
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;
using Microsoft.IdentityModel.Tokens;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.Notifications;
using Microsoft.Owin.Security.OpenIdConnect;
using Owin;
using System.Configuration;
using System.Threading.Tasks;
using System.Web;

namespace graph_tutorial
{
    public partial class Startup
    {
        // Load configuration settings from PrivateSettings.config
        private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
        private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
        private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
        private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

        public void ConfigureAuth(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());

            app.UseOpenIdConnectAuthentication(
              new OpenIdConnectAuthenticationOptions
              {
                  ClientId = appId,
                  Authority = "https://login.microsoftonline.com/common/v2.0",
                  Scope = $"openid email profile offline_access {graphScopes}",
                  RedirectUri = redirectUri,
                  PostLogoutRedirectUri = redirectUri,
                  TokenValidationParameters = new TokenValidationParameters
                  {
                      // For demo purposes only, see below
                      ValidateIssuer = false

                      // In a real multi-tenant app, you would add logic to determine whether the
                      // issuer was from an authorized tenant
                      //ValidateIssuer = true,
                      //IssuerValidator = (issuer, token, tvp) =>
                      //{
                      //  if (MyCustomTenantValidation(issuer))
                      //  {
                      //    return issuer;
                      //  }
                      //  else
                      //  {
                      //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
                      //  }
                      //}
                  },
                  Notifications = new OpenIdConnectAuthenticationNotifications
                  {
                      AuthenticationFailed = OnAuthenticationFailedAsync,
                      AuthorizationCodeReceived = OnAuthorizationCodeReceivedAsync
                  }
              }
            );
        }

        private static Task OnAuthenticationFailedAsync(AuthenticationFailedNotification<OpenIdConnectMessage,
          OpenIdConnectAuthenticationOptions> notification)
        {
            notification.HandleResponse();
            string redirect = $"/Home/Error?message={notification.Exception.Message}";
            if (notification.ProtocolMessage != null && !string.IsNullOrEmpty(notification.ProtocolMessage.ErrorDescription))
            {
                redirect += $"&debug={notification.ProtocolMessage.ErrorDescription}";
            }
            notification.Response.Redirect(redirect);
            return Task.FromResult(0);
        }

        private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
        {
            var idClient = new ConfidentialClientApplication(
                appId, redirectUri, new ClientCredential(appSecret), null, null);

            string message;
            string debug;

            try
            {
                string[] scopes = graphScopes.Split(' ');

                var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
                    notification.Code, scopes);

                message = "Access token retrieved.";
                debug = result.AccessToken;
            }
            catch (MsalException ex)
            {
                message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
                debug = ex.Message;
            }

            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={debug}");
        }
    }
}
```

Dieser Code konfiguriert die OWIN-Middleware mit den Werten from `PrivateSettings.config` und definiert zwei Callback-Methoden `OnAuthenticationFailedAsync` und `OnAuthorizationCodeReceivedAsync`. Diese Rückrufmethoden werden aufgerufen, wenn der Anmeldeprozess von Azure zurückgegeben wird.

Aktualisieren Sie nun `Startup.cs` die Datei, um `ConfigureAuth` die Methode aufzurufen. Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.

```cs
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(graph_tutorial.Startup))]

namespace graph_tutorial
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

Fügen Sie `Error` der `HomeController` Klasse eine Aktion hinzu, um `message` die `debug` -und Abfrageparameter `Alert` in ein Objekt umzuwandeln. Öffnen `Controllers/HomeController.cs` Sie die folgende Funktion, und fügen Sie Sie hinzu.

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

Hinzufügen eines Controllers zur Verarbeitung der Anmeldung. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **_GT_ Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus. Benennen Sie den `AccountController` Controller, und wählen Sie **Hinzufügen**. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.

```cs
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using System.Security.Claims;
using System.Web;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public class AccountController : Controller
    {
        public void SignIn()
        {
            if (!Request.IsAuthenticated)
            {
                // Signal OWIN to send an authorization request to Azure
                Request.GetOwinContext().Authentication.Challenge(
                    new AuthenticationProperties { RedirectUri = "/" },
                    OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }

        public ActionResult SignOut()
        {
            if (Request.IsAuthenticated)
            {
                Request.GetOwinContext().Authentication.SignOut(
                    CookieAuthenticationDefaults.AuthenticationType);
            }

            return RedirectToAction("Index", "Home");
        }
    }
}
```

Dies definiert eine `SignIn` und `SignOut` -Aktion. Die `SignIn` Aktion überprüft, ob die Anforderung bereits authentifiziert wurde. Wenn dies nicht der Fall ist, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren. Durch `SignOut` die Aktion wird die OWIN-Middleware zur Abmeldung aufgerufen.

Speichern Sie die Änderungen, und starten Sie das Projekt. Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den erforderlichen Berechtigungen zu. Der Browser leitet zur App um, wobei das Token angezeigt wird.

### <a name="get-user-details"></a>Benutzer Details abrufen

Erstellen Sie zunächst eine neue Datei für alle Microsoft Graph-Aufrufe. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **_GT_ neuen Ordner hinzufügen**aus. Benennen Sie den `Helpers`Ordner. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **_GT_-Klasse hinzufügen**.... Benennen Sie die `GraphHelper.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

```cs
using Microsoft.Graph;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace graph_tutorial.Helpers
{
    public static class GraphHelper
    {
        public static async Task<User> GetUserDetailsAsync(string accessToken)
        {
            var graphClient = new GraphServiceClient(
                new DelegateAuthenticationProvider(
                    async (requestMessage) =>
                    {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", accessToken);
                    }));

            return await graphClient.Me.Request().GetAsync();
        }
    }
}
```

Dadurch wird die `GetUserDetails` Funktion implementiert, die das Microsoft Graph-SDK zum Aufrufen `/me` des Endpunkts und zum Zurückgeben des Ergebnisses verwendet.

Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die- `App_Start/Startup.Auth.cs` Methode in, um diese Funktion aufzurufen. Fügen Sie zunächst die folgende `using` Anweisung am Anfang der Datei hinzu.

```cs
using graph_tutorial.Helpers;
```

Ersetzen Sie den `try` vorhandenen Block `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.

```cs
try
{
    string[] scopes = graphScopes.Split(' ');

    var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
        notification.Code, scopes);

    var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

    string email = string.IsNullOrEmpty(userDetails.Mail) ?
        userDetails.UserPrincipalName : userDetails.Mail;

    message = "User info retrieved.";
    debug = $"User: {userDetails.DisplayName}, Email: {email}";
}
```

Wenn Sie nun Ihre Änderungen speichern und die app starten, sollte nach der Anmeldung der Benutzername und die e-Mail-Adresse anstelle des Zugriffstokens angezeigt werden.

## <a name="storing-the-tokens"></a>Speichern der Token

Da Sie jetzt Tokens abrufen können, ist es an der Zeit, eine Möglichkeit zum Speichern in der APP zu implementieren. Da es sich um eine Beispiel-App handelt, werden die Token in der Sitzung gespeichert. Eine echte APP würde eine zuverlässigere Secure Storage-Lösung wie eine Datenbank verwenden.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **_GT_ neuen Ordner hinzufügen**aus. Benennen Sie den `TokenStorage`Ordner. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **_GT_-Klasse hinzufügen**.... Benennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

```cs
using Microsoft.Identity.Client;
using Newtonsoft.Json;
using System.Threading;
using System.Web;

namespace graph_tutorial.TokenStorage
{
    // Simple class to serialize into the session
    public class CachedUser
    {
        public string DisplayName { get; set; }
        public string Email { get; set; }
        public string Avatar { get; set; }
    }

    // Adapted from https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-v2
    public class SessionTokenStore
    {
        private static ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);
        private readonly string userId = string.Empty;
        private readonly string cacheId = string.Empty;
        private readonly string cachedUserId = string.Empty;
        private HttpContextBase httpContext = null;

        TokenCache tokenCache = new TokenCache();

        public SessionTokenStore(string userId, HttpContextBase httpContext)
        {
            this.userId = userId;
            cacheId = $"{userId}_TokenCache";
            cachedUserId = $"{userId}_UserCache";
            this.httpContext = httpContext;
            Load();
        }

        public TokenCache GetMsalCacheInstance()
        {
            tokenCache.SetBeforeAccess(BeforeAccessNotification);
            tokenCache.SetAfterAccess(AfterAccessNotification);
            Load();
            return tokenCache;
        }

        public bool HasData()
        {
            return (httpContext.Session[cacheId] != null && ((byte[])httpContext.Session[cacheId]).Length > 0);
        }

        public void Clear()
        {
            httpContext.Session.Remove(cacheId);
        }

        private void Load()
        {
            sessionLock.EnterReadLock();
            tokenCache.Deserialize((byte[])httpContext.Session[cacheId]);
            sessionLock.ExitReadLock();
        }

        private void Persist()
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cacheId] = tokenCache.Serialize();
            sessionLock.ExitReadLock();
        }

        // Triggered right before MSAL needs to access the cache.
        private void BeforeAccessNotification(TokenCacheNotificationArgs args)
        {
            // Reload the cache from the persistent store in case it changed since the last access.
            Load();
        }

        // Triggered right after MSAL accessed the cache.
        private void AfterAccessNotification(TokenCacheNotificationArgs args)
        {
            // if the access operation resulted in a cache update
            if (args.HasStateChanged)
            {
                Persist();
            }
        }

        public void SaveUserDetails(CachedUser user)
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cachedUserId] = JsonConvert.SerializeObject(user);
            sessionLock.ExitReadLock();
        }

        public CachedUser GetUserDetails()
        {
            sessionLock.EnterReadLock();
            var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[cachedUserId]);
            sessionLock.ExitReadLock();
            return cachedUser;
        }
    }
}
```

Dieser Code erstellt eine `SessionTokenStore` Klasse, die mit der `TokenCache` Klasse der MSAL-Bibliothek funktioniert. Der größte Teil des Codes umfasst das Serialisieren und Deserialisieren der `TokenCache` in der Sitzung. Außerdem werden eine Klasse und Methoden zum Serialisieren und Deserialisieren der Details des Benutzers in die Sitzung bereitgestellt.

Fügen Sie nun die folgende `using` Anweisung am Anfang der `App_Start/Startup.Auth.cs` Datei hinzu.

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

Aktualisieren Sie nun `OnAuthorizationCodeReceivedAsync` die-Funktion, um eine Instanz `SessionTokenStore` der Klasse zu erstellen und diese dem Konstruktor für `ConfidentialClientApplication` das Objekt bereitzustellen. Dies führt dazu, dass MSAL ihre Cache Implementierung zum Speichern von Token verwendet. Ersetzen Sie die vorhandene `OnAuthorizationCodeReceivedAsync`-Funktion durch Folgendes.

```cs
private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
{
    // Get the signed in user's id and create a token cache
    string signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
    SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
        notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase);

    var idClient = new ConfidentialClientApplication(
        appId, redirectUri, new ClientCredential(appSecret), tokenStore.GetMsalCacheInstance(), null);

    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
            notification.Code, scopes);

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        var cachedUser = new CachedUser()
        {
            DisplayName = userDetails.DisplayName,
            Email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail,
            Avatar = string.Empty
        };

        tokenStore.SaveUserDetails(cachedUser);
    }
    catch (MsalException ex)
    {
        string message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
    catch(Microsoft.Graph.ServiceException ex)
    {
        string message = "GetUserDetailsAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
}
```

Zusammenfassung der Änderungen:

- Der Code übergibt jetzt ein `TokenCache` Objekt an den Konstruktor für `ConfidentialClientApplication`. Die MSAL-Bibliothek behandelt die Logik zum Speichern der Token und zum Aktualisieren bei Bedarf.
- Der Code übergibt nun die von Microsoft Graph abgerufenen Benutzer Details an `SessionTokenStore` das Objekt, das in der Sitzung gespeichert werden soll.
- Bei Erfolg wird der Code nicht mehr umgeleitet, sondern nur zurückgegeben. Dadurch kann die OWIN-Middleware den Authentifizierungsprozess abschließen.

Da der Token-Cache in der Sitzung gespeichert ist, aktualisieren `SignOut` Sie die `Controllers/AccountController.cs` Aktion in, um den Tokenspeicher zu löschen, bevor Sie sich abmelden. Fügen Sie zunächst die folgende `using` Anweisung am Anfang der Datei hinzu.

```cs
using graph_tutorial.TokenStorage;
```

Ersetzen Sie dann die vorhandene `SignOut` Funktion durch Folgendes.

```cs
public ActionResult SignOut()
{
    if (Request.IsAuthenticated)
    {
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        tokenStore.Clear();

        Request.GetOwinContext().Authentication.SignOut(
            CookieAuthenticationDefaults.AuthenticationType);
    }

    return RedirectToAction("Index", "Home");
}
```

Die zwischengespeicherten Benutzer Details sind etwas, das jede Ansicht in der Anwendung benötigt, und aktualisieren `BaseController` Sie daher die Klasse, um diese Informationen aus der Sitzung zu laden. Öffnen `Controllers/BaseController.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei.

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

Fügen Sie dann die folgende Funktion hinzu.

```cs
protected override void OnActionExecuting(ActionExecutingContext filterContext)
{
    if (Request.IsAuthenticated)
    {
        // Get the signed in user's id and create a token cache
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        if (tokenStore.HasData())
        {
            // Add the user to the view bag
            ViewBag.User = tokenStore.GetUserDetails();
        }
        else
        {
            // The session has lost data. This happens often
            // when debugging. Log out so the user can log back in
            Request.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            filterContext.Result = RedirectToAction("Index", "Home");
        }
    }

    base.OnActionExecuting(filterContext);
}
```

Starten Sie den Server, und gehen Sie durch den Anmeldeprozess. Sie sollten auf der Startseite zurückkehren, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.

![Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

Klicken Sie auf den Benutzer Avatar in der oberen rechten Ecke, **** um auf den Link abmelden zuzugreifen. Wenn **** Sie auf Abmelden klicken, wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite.

![Screenshot des Dropdownmenüs mit dem Link "abMelden"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, mit dem die APP auf Microsoft Graph im Namen des Benutzers zugreifen kann.

Dieses Token ist jedoch kurzlebig. Das Token läuft eine Stunde nach der Ausgabe ab. An dieser Stelle wird das Aktualisierungstoken nützlich. Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.

Da die APP die MSAL-Bibliothek und ein `TokenCache` Objekt verwendet, müssen Sie keine Token-Aktualisierungslogik implementieren. Die `ConfidentialClientApplication.AcquireTokenSilentAsync` -Methode erledigt die gesamte Logik für Sie. Zuerst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn es abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um eine neue zu erhalten. Diese Methode wird im folgenden Modul verwendet.