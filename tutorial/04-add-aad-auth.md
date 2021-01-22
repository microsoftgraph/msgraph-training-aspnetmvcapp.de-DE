<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist notwendig, um das erforderliche OAuth-Zugriffstoken zum Aufruf der Microsoft Graph-API abzurufen. In diesem Schritt integrieren Sie die OWIN-Middleware und die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Graph-Lernprogramm-Projekt, und wählen **Sie > Neues Element... aus.** Wählen **Sie "Webkonfigurationsdatei"** aus, benennen Sie die `PrivateSettings.config` Datei, und wählen Sie **"Hinzufügen" aus.** Ersetzen sie den gesamten Inhalt durch den folgenden Code.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Ersetzen Sie diese durch die Anwendungs-ID aus dem Anwendungsregistrierungsportal, und ersetzen Sie sie durch den von `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` Ihnen generierten geheimen Client. Wenn Ihr geheimer Kundenschlüsse ein Und-Zeichen (`&`) enthält, stellen Sie sicher, dass Sie diese in `PrivateSettings.config` durch `&amp;` ersetzen. Vergewissern Sie sich außerdem, dass Sie den `PORT`-Wert für den `ida:RedirectUri` so ändern, dass er der URL der Anwendung entspricht.

    > [!IMPORTANT]
    > Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass versehentlich Ihre App-ID und Ihr Kennwort durch lecks `PrivateSettings.config` geht.

1. Aktualisieren `Web.config` Sie, um diese neue Datei zu laden. Ersetzen Sie den `<appSettings>`-Wert (Zeile 7) durch Folgendes:

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Beginnen Sie mit der Initialisierung der OWIN-Middleware, um die Azure AD-Authentifizierung für die App zu verwenden.

1. Klicken Sie im App_Start mit **der rechten** Maustaste auf den Ordner ">", und wählen Sie **"Klasse >" aus.** Benennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **"Hinzufügen" aus.** Ersetzen Sie den gesamten Inhalt durch den folgenden Code.

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
    > Dieser Code konfiguriert die OWIN Middleware mit den Werten aus und definiert zwei `PrivateSettings.config` Rückrufmethoden `OnAuthenticationFailedAsync` und `OnAuthorizationCodeReceivedAsync` . Diese Rückrufmethoden werden bei einer Umleitung über Azure im Rahmen des Anmeldevorgangs aufgerufen.

1. Aktualisieren Sie nun die `Startup.cs` Datei so, dass die Methode `ConfigureAuth` aufruft. Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.

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

1. Fügen Sie der `HomeController`-Klasse eine `Error`-Aktion hinzu, um die Abfrageparameter `message` und `debug` in ein `Alert`-Objekt umzuwandeln. Öffnen `Controllers/HomeController.cs` Sie die folgende Funktion, und fügen Sie sie hinzu.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Fügen Sie einen Controller zur Abwicklung der Anmeldung hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie **Hinzufügen > Controller...** aus. Wählen Sie **MVC 5-Controller – leer** aus, und wählen Sie **Hinzufügen** aus. Benennen Sie den Controller `AccountController` und wählen Sie **Hinzufügen** aus. Ersetzen Sie den gesamten Inhalt der Datei  durch den folgenden Code:

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

    Damit wird eine `SignIn`- und eine `SignOut`-Aktion definiert. Die `SignIn`-Aktion überprüft, ob die Anforderung bereits authentifiziert wurde. Ist das nicht der Fall, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren. Die `SignOut`-Aktion ruft die OWIN-Middleware zur Abmeldung auf.

1. Speichern Sie Ihre Änderungen und starten Sie das Projekt. Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu. Der Browser leitet zur App um, in der Sie das Token sehen.

### <a name="get-user-details"></a>Benutzerdetails abrufen

Sobald sich der Benutzer angemeldet hat, können Sie dessen Informationen über Microsoft Graph abrufen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **graph-tutorial** und wählen Sie **Hinzufügen > Neuer Ordner** aus. Benennen Sie den Ordner `Helpers`.

1. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen **Sie > Klasse hinzufügen...** aus. Benennen Sie die `GraphHelper.cs` Datei, und wählen Sie **"Hinzufügen" aus.** Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

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
                        (requestMessage) =>
                        {
                            requestMessage.Headers.Authorization =
                                new AuthenticationHeaderValue("Bearer", accessToken);
                            return Task.FromResult(0);
                        }));

                return await graphClient.Me.Request().GetAsync();
            }
        }
    }
    ```

    Dadurch wird die `GetUserDetails`-Funktion implementiert, die das Microsoft Graph-SDK verwendet, um den `/me`-Endpunkt aufzurufen und das Ergebnis zurückzugeben.

1. Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die Methode `App_Start/Startup.Auth.cs` in, um diese Funktion auf aufruft. Fügen Sie die folgende `using`-Anweisung zum Anfang der Datei hinzu.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Ersetzen Sie den vorhandenen `try`-Block in `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.

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

1. Speichern Sie Ihre Änderungen und starten Sie die App. Nach der Anmeldung sollten anstelle des Zugriffstokens der Name des Benutzers und dessen E-Mail-Adresse angezeigt werden.

## <a name="storing-the-tokens"></a>Speichern des Tokens

Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern. Da es sich um eine Beispiel-App handelt, verwenden Sie die Sitzung zum Speichern der Token. Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden. In diesem Abschnitt werden Sie Folgendes tun:

- Implementieren Sie eine Tokenspeicher-Klasse, um den Cache für MSAL-Token und die Details des Benutzers in der Benutzersitzung zu serialisieren und zu speichern.
- Aktualisieren Sie den Authentifizierungscode so, dass die Tokenspeicher-Klasse verwendet wird.
- Aktualisieren Sie die Base Controller-Klasse, um die gespeicherten Benutzerdetails für alle Ansichten in der Anwendung bereitzustellen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **graph-tutorial** und wählen Sie **Hinzufügen > Neuer Ordner** aus. Benennen Sie den Ordner `TokenStorage`.

1. Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen **Sie > Klasse hinzufügen...** aus. Benennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **"Hinzufügen" aus.** Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.

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

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value ??
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

1. Fügen Sie die `using` folgenden Anweisungen am Oberen Rand der Datei `App_Start/Startup.Auth.cs` hinzu.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
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
    > Die Änderungen in dieser neuen Version von `OnAuthorizationCodeReceivedAsync` haben folgenden Zweck:
    >
    > - Der Code umschließt nun den standardmäßigen Benutzertoken-Cache von `ConfidentialClientApplication` mit der `SessionTokenStore`-Klasse. Die MSAL-Bibliothek führt bei Bedarf die Logik zum Speichern und Aktualisieren des Tokens aus.
    > - Der Code übermittelt nun die Benutzerdetails, die von Microsoft Graph abgerufen wurden, an das `SessionTokenStore`-Objekt, um sie in der Sitzung zu speichern.
    > - Nach erfolgreicher Durchführung des Vorgangs löst der Code keine Weiterleitung aus, sondern wird lediglich zurückgegeben. Auf diese Weise kann die OWIN-Middleware den Authentifizierungsprozess abschließen.

1. Aktualisieren Sie `SignOut` die Aktion, um den Tokenspeicher vor dem Abschreiben zu löschen. Fügen Sie die `using` folgende Anweisung am oberen Rand von `Controllers/AccountController.cs` hinzu.

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

1. Öffnen `Controllers/BaseController.cs` Sie die folgenden `using` Anweisungen, und fügen Sie sie am oberen Rand der Datei hinzu.

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

1. Starten Sie den Server, und durchlaufen Sie den Anmeldevorgang. Sie sollten wieder auf der Startseite landen, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den Link **"Abmelden" zu** klicken. Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Aktualisieren von Token

An diesem Punkt verfügt Ihre Anwendung über ein Zugriffstoken, das im `Authorization` Header von API-Aufrufen gesendet wird. Dies ist das Token, durch das die App im Namen des Benutzers auf Microsoft Graph zugreifen kann.

Dieses Token ist jedoch nur kurzzeitig verfügbar. Das Token läuft eine Stunde nach seiner Entsprechung ab. An dieser Stelle kommt das Aktualisierungstoken ins Spiel. Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.

Da die App die MSAL-Bibliothek verwendet und das Objekt serialisiert, müssen Sie keine `TokenCache` Tokenaktualisierungslogik implementieren. Die `ConfidentialClientApplication.AcquireTokenSilentAsync`-Methode übernimmt die Logik-Aufgaben für Sie. Zuerst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn es abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues Token abzurufen. Sie verwenden diese Methode im folgenden Modul.
