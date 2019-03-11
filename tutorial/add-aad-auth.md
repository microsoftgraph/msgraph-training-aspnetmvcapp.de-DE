<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="1b05f-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung zur Unterstützung der Authentifizierung mit Azure AD.</span><span class="sxs-lookup"><span data-stu-id="1b05f-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="1b05f-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken für den Aufruf von Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="1b05f-103">In diesem Schritt integrieren Sie die OWIN-Middleware und die [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="1b05f-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

<span data-ttu-id="1b05f-104">Klicken Sie im projektMappen-Explorer mit der rechten Maustaste auf das **Graph-Tutorial-** Projekt, und wählen Sie **_GT_ neues Element hinzufügen...**. Wählen \*\*\*\* Sie Webkonfigurationsdatei, benennen Sie die `PrivateSettings.config` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-104">Right-click the **graph-tutorial** project in Solution Explorer and choose **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and choose **Add**.</span></span> <span data-ttu-id="1b05f-105">Ersetzen sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-105">Replace its entire contents with the following code.</span></span>

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

<span data-ttu-id="1b05f-106">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie es durch das von Ihnen generierte Kennwort.</span><span class="sxs-lookup"><span data-stu-id="1b05f-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span> <span data-ttu-id="1b05f-107">Achten Sie auch darauf, den `PORT` Wert für die `ida:RedirectUri` an die URL der Anwendung anzupassen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-107">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1b05f-108">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, um die `PrivateSettings.config` Datei aus der Quellcodeverwaltung auszuschließen, um versehentlich Ihre APP-ID und Ihr Kennwort zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="1b05f-108">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="1b05f-109">Aktualisieren `Web.config` Sie, um diese neue Datei zu laden.</span><span class="sxs-lookup"><span data-stu-id="1b05f-109">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="1b05f-110">Ersetzen Sie `<appSettings>` die (Leitung 7) durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="1b05f-110">Replace the `<appSettings>` (line 7) with the following</span></span>

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a><span data-ttu-id="1b05f-111">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="1b05f-111">Implement sign-in</span></span>

<span data-ttu-id="1b05f-112">Beginnen Sie damit, die OWIN-Middleware für die Verwendung der Azure AD-Authentifizierung für die APP zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="1b05f-112">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span> <span data-ttu-id="1b05f-113">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **App_Start** , und wählen Sie **Add > Class.**... Benennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-113">Right-click the **App_Start** folder in Solution Explorer and choose **Add > Class...**. Name the file `Startup.Auth.cs` and choose **Add**.</span></span> <span data-ttu-id="1b05f-114">Ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-114">Replace the entire contents with the following code.</span></span>

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

<span data-ttu-id="1b05f-115">Dieser Code konfiguriert die OWIN-Middleware mit den Werten from `PrivateSettings.config` und definiert zwei Callback-Methoden `OnAuthenticationFailedAsync` und `OnAuthorizationCodeReceivedAsync`.</span><span class="sxs-lookup"><span data-stu-id="1b05f-115">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="1b05f-116">Diese Rückrufmethoden werden aufgerufen, wenn der Anmeldeprozess von Azure zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1b05f-116">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

<span data-ttu-id="1b05f-117">Aktualisieren Sie nun `Startup.cs` die Datei, um `ConfigureAuth` die Methode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-117">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="1b05f-118">Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-118">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

<span data-ttu-id="1b05f-119">Fügen Sie `Error` der `HomeController` Klasse eine Aktion hinzu, um `message` die `debug` -und Abfrageparameter `Alert` in ein Objekt umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="1b05f-119">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="1b05f-120">Öffnen `Controllers/HomeController.cs` Sie die folgende Funktion, und fügen Sie Sie hinzu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-120">Open `Controllers/HomeController.cs` and add the following function.</span></span>

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

<span data-ttu-id="1b05f-121">Hinzufügen eines Controllers zur Verarbeitung der Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="1b05f-121">Add a controller to handle sign-in.</span></span> <span data-ttu-id="1b05f-122">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **_GT_ Controller hinzufügen**.... Wählen Sie **MVC 5 Controller-Empty** aus, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-122">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="1b05f-123">Benennen Sie den `AccountController` Controller, und wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="1b05f-123">Name the controller `AccountController` and choose **Add**.</span></span> <span data-ttu-id="1b05f-124">Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-124">Replace the entire contents of the file with the following code.</span></span>

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

<span data-ttu-id="1b05f-125">Dies definiert eine `SignIn` und `SignOut` -Aktion.</span><span class="sxs-lookup"><span data-stu-id="1b05f-125">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="1b05f-126">Die `SignIn` Aktion überprüft, ob die Anforderung bereits authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="1b05f-126">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="1b05f-127">Wenn dies nicht der Fall ist, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="1b05f-127">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="1b05f-128">Durch `SignOut` die Aktion wird die OWIN-Middleware zur Abmeldung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-128">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

<span data-ttu-id="1b05f-129">Speichern Sie die Änderungen, und starten Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1b05f-129">Save your changes and start the project.</span></span> <span data-ttu-id="1b05f-130">Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="1b05f-130">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="1b05f-131">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den erforderlichen Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-131">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="1b05f-132">Der Browser leitet zur App um, wobei das Token angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1b05f-132">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="1b05f-133">Benutzer Details abrufen</span><span class="sxs-lookup"><span data-stu-id="1b05f-133">Get user details</span></span>

<span data-ttu-id="1b05f-134">Erstellen Sie zunächst eine neue Datei für alle Microsoft Graph-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="1b05f-134">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="1b05f-135">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **_GT_ neuen Ordner hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-135">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="1b05f-136">Benennen Sie den `Helpers`Ordner.</span><span class="sxs-lookup"><span data-stu-id="1b05f-136">Name the folder `Helpers`.</span></span> <span data-ttu-id="1b05f-137">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **_GT_-Klasse hinzufügen**.... Benennen Sie die `GraphHelper.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-137">Right click this new folder and choose **Add > Class...**. Name the file `GraphHelper.cs` and choose **Add**.</span></span> <span data-ttu-id="1b05f-138">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-138">Replace the contents of this file with the following code.</span></span>

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

<span data-ttu-id="1b05f-139">Dadurch wird die `GetUserDetails` Funktion implementiert, die das Microsoft Graph-SDK zum Aufrufen `/me` des Endpunkts und zum Zurückgeben des Ergebnisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="1b05f-139">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="1b05f-140">Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die- `App_Start/Startup.Auth.cs` Methode in, um diese Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-140">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="1b05f-141">Fügen Sie zunächst die folgende `using` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-141">First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.Helpers;
```

<span data-ttu-id="1b05f-142">Ersetzen Sie den `try` vorhandenen Block `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-142">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

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

<span data-ttu-id="1b05f-143">Wenn Sie nun Ihre Änderungen speichern und die app starten, sollte nach der Anmeldung der Benutzername und die e-Mail-Adresse anstelle des Zugriffstokens angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="1b05f-143">Now if you save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="1b05f-144">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="1b05f-144">Storing the tokens</span></span>

<span data-ttu-id="1b05f-145">Da Sie jetzt Tokens abrufen können, ist es an der Zeit, eine Möglichkeit zum Speichern in der APP zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="1b05f-145">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="1b05f-146">Da es sich um eine Beispiel-App handelt, werden die Token in der Sitzung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="1b05f-146">Since this is a sample app, we'll use the session to store the tokens.</span></span> <span data-ttu-id="1b05f-147">Eine echte APP würde eine zuverlässigere Secure Storage-Lösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="1b05f-147">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="1b05f-148">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **_GT_ neuen Ordner hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-148">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="1b05f-149">Benennen Sie den `TokenStorage`Ordner.</span><span class="sxs-lookup"><span data-stu-id="1b05f-149">Name the folder `TokenStorage`.</span></span> <span data-ttu-id="1b05f-150">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **_GT_-Klasse hinzufügen**.... Benennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="1b05f-150">Right click this new folder and choose **Add > Class...**. Name the file `SessionTokenStore.cs` and choose **Add**.</span></span> <span data-ttu-id="1b05f-151">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="1b05f-151">Replace the contents of this file with the following code.</span></span>

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

            // Optimistically set HasStateChanged to false.
            // We need to do it early to avoid losing changes made by a concurrent thread.
            tokenCache.HasStateChanged = false;

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
            if (tokenCache.HasStateChanged)
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

<span data-ttu-id="1b05f-152">Dieser Code erstellt eine `SessionTokenStore` Klasse, die mit der `TokenCache` Klasse der MSAL-Bibliothek funktioniert.</span><span class="sxs-lookup"><span data-stu-id="1b05f-152">This code creates a `SessionTokenStore` class that works with the MSAL library's `TokenCache` class.</span></span> <span data-ttu-id="1b05f-153">Der größte Teil des Codes umfasst das Serialisieren und Deserialisieren der `TokenCache` in der Sitzung.</span><span class="sxs-lookup"><span data-stu-id="1b05f-153">Most of the code here involves serializing and deserializing the `TokenCache` to the session.</span></span> <span data-ttu-id="1b05f-154">Außerdem werden eine Klasse und Methoden zum Serialisieren und Deserialisieren der Details des Benutzers in die Sitzung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1b05f-154">It also provides a class and methods to serialize and deserialize the user's details to the session.</span></span>

<span data-ttu-id="1b05f-155">Fügen Sie nun die folgende `using` Anweisung am Anfang der `App_Start/Startup.Auth.cs` Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-155">Now, add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

<span data-ttu-id="1b05f-156">Aktualisieren Sie nun `OnAuthorizationCodeReceivedAsync` die-Funktion, um eine Instanz `SessionTokenStore` der Klasse zu erstellen und diese dem Konstruktor für `ConfidentialClientApplication` das Objekt bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-156">Now update the `OnAuthorizationCodeReceivedAsync` function to create an instance of the `SessionTokenStore` class and provide that to the constructor for the `ConfidentialClientApplication` object.</span></span> <span data-ttu-id="1b05f-157">Dies führt dazu, dass MSAL ihre Cache Implementierung zum Speichern von Token verwendet.</span><span class="sxs-lookup"><span data-stu-id="1b05f-157">That will cause MSAL to use your cache implementation for storing tokens.</span></span> <span data-ttu-id="1b05f-158">Ersetzen Sie die vorhandene `OnAuthorizationCodeReceivedAsync`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="1b05f-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

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

<span data-ttu-id="1b05f-159">Zusammenfassung der Änderungen:</span><span class="sxs-lookup"><span data-stu-id="1b05f-159">To summarize the changes:</span></span>

- <span data-ttu-id="1b05f-160">Der Code übergibt jetzt ein `TokenCache` Objekt an den Konstruktor für `ConfidentialClientApplication`.</span><span class="sxs-lookup"><span data-stu-id="1b05f-160">The code now passes a `TokenCache` object to the constructor for `ConfidentialClientApplication`.</span></span> <span data-ttu-id="1b05f-161">Die MSAL-Bibliothek behandelt die Logik zum Speichern der Token und zum Aktualisieren bei Bedarf.</span><span class="sxs-lookup"><span data-stu-id="1b05f-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
- <span data-ttu-id="1b05f-162">Der Code übergibt nun die von Microsoft Graph abgerufenen Benutzer Details an `SessionTokenStore` das Objekt, das in der Sitzung gespeichert werden soll.</span><span class="sxs-lookup"><span data-stu-id="1b05f-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
- <span data-ttu-id="1b05f-163">Bei Erfolg wird der Code nicht mehr umgeleitet, sondern nur zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1b05f-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="1b05f-164">Dadurch kann die OWIN-Middleware den Authentifizierungsprozess abschließen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-164">This allows the OWIN middleware to complete the authentication process.</span></span>

<span data-ttu-id="1b05f-165">Da der Token-Cache in der Sitzung gespeichert ist, aktualisieren `SignOut` Sie die `Controllers/AccountController.cs` Aktion in, um den Tokenspeicher zu löschen, bevor Sie sich abmelden. Fügen Sie zunächst die folgende `using` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-165">Since the token cache is stored in the session, update the `SignOut` action in `Controllers/AccountController.cs` to clear the token store before signing out. First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
```

<span data-ttu-id="1b05f-166">Ersetzen Sie dann die vorhandene `SignOut` Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="1b05f-166">Then, replace the existing `SignOut` function with the following.</span></span>

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

<span data-ttu-id="1b05f-167">Die zwischengespeicherten Benutzer Details sind etwas, das jede Ansicht in der Anwendung benötigt, und aktualisieren `BaseController` Sie daher die Klasse, um diese Informationen aus der Sitzung zu laden.</span><span class="sxs-lookup"><span data-stu-id="1b05f-167">The cached user details are something that every view in the application will need, so update the `BaseController` class to load this information from the session.</span></span> <span data-ttu-id="1b05f-168">Öffnen `Controllers/BaseController.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei.</span><span class="sxs-lookup"><span data-stu-id="1b05f-168">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

<span data-ttu-id="1b05f-169">Fügen Sie dann die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="1b05f-169">Then add the following function.</span></span>

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

<span data-ttu-id="1b05f-170">Starten Sie den Server, und gehen Sie durch den Anmeldeprozess.</span><span class="sxs-lookup"><span data-stu-id="1b05f-170">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="1b05f-171">Sie sollten auf der Startseite zurückkehren, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="1b05f-171">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

<span data-ttu-id="1b05f-173">Klicken Sie auf den Benutzer Avatar in der oberen rechten Ecke, \*\*\*\* um auf den Link abmelden zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="1b05f-173">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="1b05f-174">Wenn \*\*\*\* Sie auf Abmelden klicken, wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite.</span><span class="sxs-lookup"><span data-stu-id="1b05f-174">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Screenshot des Dropdownmenüs mit dem Link "abMelden"](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="1b05f-176">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="1b05f-176">Refreshing tokens</span></span>

<span data-ttu-id="1b05f-177">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="1b05f-177">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="1b05f-178">Dies ist das Token, mit dem die APP auf Microsoft Graph im Namen des Benutzers zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="1b05f-178">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="1b05f-179">Dieses Token ist jedoch kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="1b05f-179">However, this token is short-lived.</span></span> <span data-ttu-id="1b05f-180">Das Token läuft eine Stunde nach der Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="1b05f-180">The token expires an hour after it is issued.</span></span> <span data-ttu-id="1b05f-181">An dieser Stelle wird das Aktualisierungstoken nützlich.</span><span class="sxs-lookup"><span data-stu-id="1b05f-181">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="1b05f-182">Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="1b05f-182">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="1b05f-183">Da die APP die MSAL-Bibliothek und ein `TokenCache` Objekt verwendet, müssen Sie keine Token-Aktualisierungslogik implementieren.</span><span class="sxs-lookup"><span data-stu-id="1b05f-183">Because the app is using the MSAL library and a `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="1b05f-184">Die `ConfidentialClientApplication.AcquireTokenSilentAsync` -Methode erledigt die gesamte Logik für Sie.</span><span class="sxs-lookup"><span data-stu-id="1b05f-184">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="1b05f-185">Zuerst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1b05f-185">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="1b05f-186">Wenn es abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um eine neue zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="1b05f-186">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="1b05f-187">Diese Methode wird im folgenden Modul verwendet.</span><span class="sxs-lookup"><span data-stu-id="1b05f-187">You'll use this method in the following module.</span></span>