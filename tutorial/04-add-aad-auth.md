<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zu erhalten, um die Microsoft Graph-API aufzurufen. In diesem Schritt werden Sie die OWIN-Middleware und die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung integrieren.

1. Klicken Sie mit der rechten Maustaste auf das **Graph-Tutorial-** Projekt im Projektmappen-Explorer, und wählen Sie **#a0 neues Element hinzufügen aus...**. Wählen **** Sie Webkonfigurationsdatei aus, benennen Sie `PrivateSettings.config` die Datei, und wählen Sie **Hinzufügen**aus. Ersetzen sie den gesamten Inhalt durch den folgenden Code.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie durch den von Ihnen generierten Client Schlüssel. Wenn Ihr geheimer Client Schlüssel kaufmännische`&`und-Zeichen enthält (), müssen `&amp;` Sie `PrivateSettings.config`Sie unbedingt durch in ersetzen. Stellen Sie außerdem sicher, dass `PORT` Sie den Wert `ida:RedirectUri` für die an die URL der Anwendung anpassen.

    > [!IMPORTANT]
    > Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `PrivateSettings.config` aus der Quellcodeverwaltung auszuschließen, damit versehentlich keine APP-ID und Ihr Kennwort verloren gehen.

1. Aktualisieren `Web.config` , um diese neue Datei zu laden. Ersetzen Sie `<appSettings>` die (Reihe 7) durch Folgendes:

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Beginnen Sie mit der Initialisierung der OWIN-Middleware, um Azure AD-Authentifizierung für die APP zu verwenden.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **App_Start** , und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den gesamten Inhalt durch den folgenden Code.

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
                var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                    .WithRedirectUri(redirectUri)
                    .WithClientSecret(appSecret)
                    .Build();

                string message;
                string debug;

                try
                {
                    string[] scopes = graphScopes.Split(' ');

                    var result = await idClient.AcquireTokenByAuthorizationCode(
                        scopes, notification.Code).ExecuteAsync();

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

    > [!NOTE]
    > Dieser Code konfiguriert die OWIN-Middleware mit den Werten aus `PrivateSettings.config` und definiert zwei Rückrufmethoden `OnAuthenticationFailedAsync` und. `OnAuthorizationCodeReceivedAsync` Diese Rückrufmethoden werden aufgerufen, wenn der Anmeldeprozess von Azure zurückgegeben wird.

1. Aktualisieren Sie nun `Startup.cs` die Datei, um `ConfigureAuth` die-Methode aufzurufen. Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.

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

1. Fügen Sie `Error` der `HomeController` -Klasse eine Aktion hinzu, `message` um `debug` die-und Abfrage `Alert` Parameter in ein-Objekt zu transformieren. Öffnen `Controllers/HomeController.cs` Sie und fügen Sie die folgende Funktion hinzu.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Hinzufügen eines Controllers zur Behandlung der Anmeldung Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **#a0 Controller hinzufügen**aus.... Wählen Sie **MVC 5 Controller – leer** , und wählen Sie **Hinzufügen**aus. Nennen Sie den `AccountController` Controller, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.

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

    Dies definiert eine `SignIn` and `SignOut` -Aktion. Die `SignIn` Aktion überprüft, ob die Anforderung bereits authentifiziert wurde. Wenn dies nicht der Fall ist, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren. Die `SignOut` Aktion ruft die OWIN-Middleware auf, um sich abzumelden.

1. Speichern Sie Ihre Änderungen, und starten Sie das Projekt. Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu. Der Browser wird an die APP umgeleitet, wobei das Token angezeigt wird.

### <a name="get-user-details"></a>Abrufen von Benutzer Details

Sobald der Benutzer angemeldet ist, können Sie seine Informationen aus Microsoft Graph abrufen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **#a0 neuen Ordner hinzufügen**aus. Nennen Sie den `Helpers`Ordner.

1. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `GraphHelper.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

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

    Dadurch wird die `GetUserDetails` Funktion implementiert, die das Microsoft Graph-SDK verwendet, `/me` um den Endpunkt aufzurufen und das Ergebnis zurückzugeben.

1. Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die- `App_Start/Startup.Auth.cs` Methode in, um diese Funktion aufzurufen. Fügen Sie die `using` folgende Anweisung am Anfang der Datei hinzu.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Ersetzen Sie den `try` vorhandenen Block `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.

    ```cs
    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCode(
            scopes, notification.Code).ExecuteAsync();

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        string email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail;

        message = "User info retrieved.";
        debug = $"User: {userDetails.DisplayName}, Email: {email}";
    }
    ```

1. Speichern Sie Ihre Änderungen, und starten Sie die APP, nachdem Sie sich angemeldet haben, sollten Sie den Namen und die e-Mail-Adresse des Benutzers anstelle des Zugriffstokens sehen.

## <a name="storing-the-tokens"></a>Speichern der Token

Nachdem Sie nun Token erhalten können, ist es an der Zeit, eine Möglichkeit zum Speichern in der APP zu implementieren. Da es sich hierbei um eine Beispiel-App handelt, verwenden Sie die Sitzung zum Speichern der Token. Eine reale APP würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden. In diesem Abschnitt erfahren Sie Folgendes:

- Implementieren Sie eine Tokenspeicher-Klasse, um den MSAL-Token-Cache und die Details des Benutzers in der Benutzersitzung zu serialisieren und zu speichern.
- Aktualisieren Sie den Authentifizierungscode für die Verwendung der Token-Speicherklasse.
- Aktualisieren Sie die Basis Controllerklasse, um die gespeicherten Benutzer Details für alle Ansichten in der Anwendung verfügbar zu machen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **#a0 neuen Ordner hinzufügen**aus. Nennen Sie den `TokenStorage`Ordner.

1. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **Hinzufügen**aus. Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT license.

    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using System.Security.Claims;
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

        public class SessionTokenStore
        {
            private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

            private HttpContext httpContext = null;
            private string tokenCacheKey = string.Empty;
            private string userCacheKey = string.Empty;

            public SessionTokenStore(ITokenCache tokenCache, HttpContext context, ClaimsPrincipal user)
            {
                httpContext = context;

                if (tokenCache != null)
                {
                    tokenCache.SetBeforeAccess(BeforeAccessNotification);
                    tokenCache.SetAfterAccess(AfterAccessNotification);
                }

                var userId = GetUsersUniqueId(user);
                tokenCacheKey = $"{userId}_TokenCache";
                userCacheKey = $"{userId}_UserCache";
            }

            public bool HasData()
            {
                return (httpContext.Session[tokenCacheKey] != null &&
                    ((byte[])httpContext.Session[tokenCacheKey]).Length > 0);
            }

            public void Clear()
            {
                sessionLock.EnterWriteLock();

                try
                {
                    httpContext.Session.Remove(tokenCacheKey);
                }
                finally
                {
                    sessionLock.ExitWriteLock();
                }
            }

            private void BeforeAccessNotification(TokenCacheNotificationArgs args)
            {
                sessionLock.EnterReadLock();

                try
                {
                    // Load the cache from the session
                    args.TokenCache.DeserializeMsalV3((byte[])httpContext.Session[tokenCacheKey]);
                }
                finally
                {
                    sessionLock.ExitReadLock();
                }
            }

            private void AfterAccessNotification(TokenCacheNotificationArgs args)
            {
                if (args.HasStateChanged)
                {
                    sessionLock.EnterWriteLock();

                    try
                    {
                        // Store the serialized cache in the session
                        httpContext.Session[tokenCacheKey] = args.TokenCache.SerializeMsalV3();
                    }
                    finally
                    {
                        sessionLock.ExitWriteLock();
                    }
                }
            }

            public void SaveUserDetails(CachedUser user)
            {

                sessionLock.EnterWriteLock();
                httpContext.Session[userCacheKey] = JsonConvert.SerializeObject(user);
                sessionLock.ExitWriteLock();
            }

            public CachedUser GetUserDetails()
            {
                sessionLock.EnterReadLock();
                var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[userCacheKey]);
                sessionLock.ExitReadLock();
                return cachedUser;
            }

            private string GetUsersUniqueId(ClaimsPrincipal user)
            {
                // Combine the user's object ID with their tenant ID

                if (user != null)
                {
                    var userObjectId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("oid").Value;

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("tid").Value;

                    if (!string.IsNullOrEmpty(userObjectId) && !string.IsNullOrEmpty(userTenantId))
                    {
                        return $"{userObjectId}.{userTenantId}";
                    }
                }

                return null;
            }
        }
    }
    ```

1. Fügen Sie die `using` folgende Anweisung am Anfang der `App_Start/Startup.Auth.cs` Datei hinzu.

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Ersetzen Sie die vorhandene `OnAuthorizationCodeReceivedAsync`-Funktion durch Folgendes.

    ```cs
    private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
    {
        var idClient = ConfidentialClientApplicationBuilder.Create(appId)
            .WithRedirectUri(redirectUri)
            .WithClientSecret(appSecret)
            .Build();

        var signedInUser = new ClaimsPrincipal(notification.AuthenticationTicket.Identity);
        var tokenStore = new SessionTokenStore(idClient.UserTokenCache, HttpContext.Current, signedInUser);

        try
        {
            string[] scopes = graphScopes.Split(' ');

            var result = await idClient.AcquireTokenByAuthorizationCode(
                scopes, notification.Code).ExecuteAsync();

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
        catch (Microsoft.Graph.ServiceException ex)
        {
            string message = "GetUserDetailsAsync threw an exception";
            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
        }
    }
    ```

    > [!NOTE]
    > Die Änderungen in dieser neuen Version von `OnAuthorizationCodeReceivedAsync` gehen wie folgt vor:
    >
    > - Der Code umschließt nun den `ConfidentialClientApplication`standardmäßigen Benutzertoken-Cache `SessionTokenStore` mit der-Klasse. Die MSAL-Bibliothek verarbeitet die Logik, die Token zu speichern und bei Bedarf zu aktualisieren.
    > - Der Code übergibt nun die von Microsoft Graph erhaltenen Benutzer Details an `SessionTokenStore` das Objekt, das in der Sitzung gespeichert werden soll.
    > - Bei Erfolg wird der Code nicht mehr umgeleitet, sondern nur zurückgegeben. Auf diese Weise kann die OWIN-Middleware den Authentifizierungsprozess abschließen.

1. Aktualisieren Sie `SignOut` die Aktion, um den Tokenspeicher vor dem Abmelden zu löschen. Fügen Sie die `using` folgende Anweisung oben in hinzu `Controllers/AccountController.cs`.

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Ersetzen Sie die vorhandene `SignOut`-Funktion durch Folgendes.

    ```cs
    public ActionResult SignOut()
    {
        if (Request.IsAuthenticated)
        {
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

            tokenStore.Clear();

            Request.GetOwinContext().Authentication.SignOut(
                CookieAuthenticationDefaults.AuthenticationType);
        }

        return RedirectToAction("Index", "Home");
    }
    ```

1. Öffnen `Controllers/BaseController.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. Fügen Sie die folgende Funktion hinzu.

    ```cs
    protected override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        if (Request.IsAuthenticated)
        {
            // Get the user's token cache
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

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

1. Starten Sie den Server, und fahren Sie mit dem Anmeldevorgang fort. Sie sollten wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.

    ![Ein Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

1. Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers **** , um auf den Abmeldelink zuzugreifen. Durch **** klicken auf Abmelden wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite zurück.

    ![Screenshot des Dropdownmenüs mit dem Link zum Abmelden](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, mit dem die APP im Namen des Benutzers auf Microsoft Graph zugreifen kann.

Dieses Token ist jedoch nur kurzlebig. Das Token läuft eine Stunde nach seiner Ausgabe ab. Hier wird das Aktualisierungstoken nützlich. Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass sich der Benutzer erneut anmelden muss.

Da die APP die MSAL-Bibliothek verwendet und das `TokenCache` Objekt serialisiert, müssen Sie keine Logik für die Token-Aktualisierung implementieren. Die `ConfidentialClientApplication.AcquireTokenSilentAsync` -Methode führt die gesamte Logik für Sie aus. Zunächst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten. Sie verwenden diese Methode im folgenden Modul.
