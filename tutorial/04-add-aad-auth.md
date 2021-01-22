<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fa632-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="fa632-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="fa632-102">Dies ist notwendig, um das erforderliche OAuth-Zugriffstoken zum Aufruf der Microsoft Graph-API abzurufen.</span><span class="sxs-lookup"><span data-stu-id="fa632-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="fa632-103">In diesem Schritt integrieren Sie die OWIN-Middleware und die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="fa632-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

1. <span data-ttu-id="fa632-104">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Graph-Lernprogramm-Projekt, und wählen **Sie > Neues Element... aus.** Wählen **Sie "Webkonfigurationsdatei"** aus, benennen Sie die `PrivateSettings.config` Datei, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="fa632-104">Right-click the **graph-tutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and select **Add**.</span></span> <span data-ttu-id="fa632-105">Ersetzen sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-105">Replace its entire contents with the following code.</span></span>

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    <span data-ttu-id="fa632-106">Ersetzen Sie diese durch die Anwendungs-ID aus dem Anwendungsregistrierungsportal, und ersetzen Sie sie durch den von `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` Ihnen generierten geheimen Client.</span><span class="sxs-lookup"><span data-stu-id="fa632-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="fa632-107">Wenn Ihr geheimer Kundenschlüsse ein Und-Zeichen (`&`) enthält, stellen Sie sicher, dass Sie diese in `PrivateSettings.config` durch `&amp;` ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fa632-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="fa632-108">Vergewissern Sie sich außerdem, dass Sie den `PORT`-Wert für den `ida:RedirectUri` so ändern, dass er der URL der Anwendung entspricht.</span><span class="sxs-lookup"><span data-stu-id="fa632-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="fa632-109">Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass versehentlich Ihre App-ID und Ihr Kennwort durch lecks `PrivateSettings.config` geht.</span><span class="sxs-lookup"><span data-stu-id="fa632-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="fa632-110">Aktualisieren `Web.config` Sie, um diese neue Datei zu laden.</span><span class="sxs-lookup"><span data-stu-id="fa632-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="fa632-111">Ersetzen Sie den `<appSettings>`-Wert (Zeile 7) durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fa632-111">Replace the `<appSettings>` (line 7) with the following</span></span>

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="fa632-112">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="fa632-112">Implement sign-in</span></span>

<span data-ttu-id="fa632-113">Beginnen Sie mit der Initialisierung der OWIN-Middleware, um die Azure AD-Authentifizierung für die App zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa632-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span>

1. <span data-ttu-id="fa632-114">Klicken Sie im App_Start mit **der rechten** Maustaste auf den Ordner ">", und wählen Sie **"Klasse >" aus.** Benennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="fa632-114">Right-click the **App_Start** folder in Solution Explorer and select **Add > Class...**. Name the file `Startup.Auth.cs` and select **Add**.</span></span> <span data-ttu-id="fa632-115">Ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-115">Replace the entire contents with the following code.</span></span>

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
    > <span data-ttu-id="fa632-116">Dieser Code konfiguriert die OWIN Middleware mit den Werten aus und definiert zwei `PrivateSettings.config` Rückrufmethoden `OnAuthenticationFailedAsync` und `OnAuthorizationCodeReceivedAsync` .</span><span class="sxs-lookup"><span data-stu-id="fa632-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="fa632-117">Diese Rückrufmethoden werden bei einer Umleitung über Azure im Rahmen des Anmeldevorgangs aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fa632-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

1. <span data-ttu-id="fa632-118">Aktualisieren Sie nun die `Startup.cs` Datei so, dass die Methode `ConfigureAuth` aufruft.</span><span class="sxs-lookup"><span data-stu-id="fa632-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="fa632-119">Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

1. <span data-ttu-id="fa632-120">Fügen Sie der `HomeController`-Klasse eine `Error`-Aktion hinzu, um die Abfrageparameter `message` und `debug` in ein `Alert`-Objekt umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="fa632-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="fa632-121">Öffnen `Controllers/HomeController.cs` Sie die folgende Funktion, und fügen Sie sie hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. <span data-ttu-id="fa632-122">Fügen Sie einen Controller zur Abwicklung der Anmeldung hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="fa632-123">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie **Hinzufügen > Controller...** aus. Wählen Sie **MVC 5-Controller – leer** aus, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="fa632-123">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="fa632-124">Benennen Sie den Controller `AccountController` und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="fa632-124">Name the controller `AccountController` and select **Add**.</span></span> <span data-ttu-id="fa632-125">Ersetzen Sie den gesamten Inhalt der Datei  durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="fa632-125">Replace the entire contents of the file with the following code.</span></span>

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

    <span data-ttu-id="fa632-126">Damit wird eine `SignIn`- und eine `SignOut`-Aktion definiert.</span><span class="sxs-lookup"><span data-stu-id="fa632-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="fa632-127">Die `SignIn`-Aktion überprüft, ob die Anforderung bereits authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="fa632-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="fa632-128">Ist das nicht der Fall, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="fa632-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="fa632-129">Die `SignOut`-Aktion ruft die OWIN-Middleware zur Abmeldung auf.</span><span class="sxs-lookup"><span data-stu-id="fa632-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

1. <span data-ttu-id="fa632-130">Speichern Sie Ihre Änderungen und starten Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="fa632-130">Save your changes and start the project.</span></span> <span data-ttu-id="fa632-131">Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden.</span><span class="sxs-lookup"><span data-stu-id="fa632-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="fa632-132">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="fa632-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="fa632-133">Der Browser leitet zur App um, in der Sie das Token sehen.</span><span class="sxs-lookup"><span data-stu-id="fa632-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="fa632-134">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="fa632-134">Get user details</span></span>

<span data-ttu-id="fa632-135">Sobald sich der Benutzer angemeldet hat, können Sie dessen Informationen über Microsoft Graph abrufen.</span><span class="sxs-lookup"><span data-stu-id="fa632-135">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="fa632-136">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **graph-tutorial** und wählen Sie **Hinzufügen > Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="fa632-136">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="fa632-137">Benennen Sie den Ordner `Helpers`.</span><span class="sxs-lookup"><span data-stu-id="fa632-137">Name the folder `Helpers`.</span></span>

1. <span data-ttu-id="fa632-138">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen **Sie > Klasse hinzufügen...** aus. Benennen Sie die `GraphHelper.cs` Datei, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="fa632-138">Right click this new folder and select **Add > Class...**. Name the file `GraphHelper.cs` and select **Add**.</span></span> <span data-ttu-id="fa632-139">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-139">Replace the contents of this file with the following code.</span></span>

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

    <span data-ttu-id="fa632-140">Dadurch wird die `GetUserDetails`-Funktion implementiert, die das Microsoft Graph-SDK verwendet, um den `/me`-Endpunkt aufzurufen und das Ergebnis zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="fa632-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="fa632-141">Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die Methode `App_Start/Startup.Auth.cs` in, um diese Funktion auf aufruft.</span><span class="sxs-lookup"><span data-stu-id="fa632-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="fa632-142">Fügen Sie die folgende `using`-Anweisung zum Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-142">Add the following `using` statement to the top of the file.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    ```

1. <span data-ttu-id="fa632-143">Ersetzen Sie den vorhandenen `try`-Block in `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

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

1. <span data-ttu-id="fa632-144">Speichern Sie Ihre Änderungen und starten Sie die App. Nach der Anmeldung sollten anstelle des Zugriffstokens der Name des Benutzers und dessen E-Mail-Adresse angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="fa632-144">Save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="fa632-145">Speichern des Tokens</span><span class="sxs-lookup"><span data-stu-id="fa632-145">Storing the tokens</span></span>

<span data-ttu-id="fa632-146">Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="fa632-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="fa632-147">Da es sich um eine Beispiel-App handelt, verwenden Sie die Sitzung zum Speichern der Token.</span><span class="sxs-lookup"><span data-stu-id="fa632-147">Since this is a sample app, you will use the session to store the tokens.</span></span> <span data-ttu-id="fa632-148">Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa632-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span> <span data-ttu-id="fa632-149">In diesem Abschnitt werden Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="fa632-149">In this section, you will:</span></span>

- <span data-ttu-id="fa632-150">Implementieren Sie eine Tokenspeicher-Klasse, um den Cache für MSAL-Token und die Details des Benutzers in der Benutzersitzung zu serialisieren und zu speichern.</span><span class="sxs-lookup"><span data-stu-id="fa632-150">Implement a token store class to serialize and store the MSAL token cache and the user's details in the user session.</span></span>
- <span data-ttu-id="fa632-151">Aktualisieren Sie den Authentifizierungscode so, dass die Tokenspeicher-Klasse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fa632-151">Update the authentication code to use the token store class.</span></span>
- <span data-ttu-id="fa632-152">Aktualisieren Sie die Base Controller-Klasse, um die gespeicherten Benutzerdetails für alle Ansichten in der Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fa632-152">Update the base controller class to expose the stored user details to all views in the application.</span></span>

1. <span data-ttu-id="fa632-153">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **graph-tutorial** und wählen Sie **Hinzufügen > Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="fa632-153">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="fa632-154">Benennen Sie den Ordner `TokenStorage`.</span><span class="sxs-lookup"><span data-stu-id="fa632-154">Name the folder `TokenStorage`.</span></span>

1. <span data-ttu-id="fa632-155">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen **Sie > Klasse hinzufügen...** aus. Benennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **"Hinzufügen" aus.**</span><span class="sxs-lookup"><span data-stu-id="fa632-155">Right click this new folder and select **Add > Class...**. Name the file `SessionTokenStore.cs` and select **Add**.</span></span> <span data-ttu-id="fa632-156">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa632-156">Replace the contents of this file with the following code.</span></span>

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

1. <span data-ttu-id="fa632-157">Fügen Sie die `using` folgenden Anweisungen am Oberen Rand der Datei `App_Start/Startup.Auth.cs` hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-157">Add the following `using` statements to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. <span data-ttu-id="fa632-158">Ersetzen Sie die vorhandene `OnAuthorizationCodeReceivedAsync`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="fa632-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

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
    > <span data-ttu-id="fa632-159">Die Änderungen in dieser neuen Version von `OnAuthorizationCodeReceivedAsync` haben folgenden Zweck:</span><span class="sxs-lookup"><span data-stu-id="fa632-159">The changes in this new version of `OnAuthorizationCodeReceivedAsync` do the following:</span></span>
    >
    > - <span data-ttu-id="fa632-160">Der Code umschließt nun den standardmäßigen Benutzertoken-Cache von `ConfidentialClientApplication` mit der `SessionTokenStore`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fa632-160">The code now wraps the `ConfidentialClientApplication`'s default user token cache with the `SessionTokenStore` class.</span></span> <span data-ttu-id="fa632-161">Die MSAL-Bibliothek führt bei Bedarf die Logik zum Speichern und Aktualisieren des Tokens aus.</span><span class="sxs-lookup"><span data-stu-id="fa632-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
    > - <span data-ttu-id="fa632-162">Der Code übermittelt nun die Benutzerdetails, die von Microsoft Graph abgerufen wurden, an das `SessionTokenStore`-Objekt, um sie in der Sitzung zu speichern.</span><span class="sxs-lookup"><span data-stu-id="fa632-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
    > - <span data-ttu-id="fa632-163">Nach erfolgreicher Durchführung des Vorgangs löst der Code keine Weiterleitung aus, sondern wird lediglich zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fa632-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="fa632-164">Auf diese Weise kann die OWIN-Middleware den Authentifizierungsprozess abschließen.</span><span class="sxs-lookup"><span data-stu-id="fa632-164">This allows the OWIN middleware to complete the authentication process.</span></span>

1. <span data-ttu-id="fa632-165">Aktualisieren Sie `SignOut` die Aktion, um den Tokenspeicher vor dem Abschreiben zu löschen. Fügen Sie die `using` folgende Anweisung am oberen Rand von `Controllers/AccountController.cs` hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-165">Update the `SignOut` action to clear the token store before signing out. Add the following `using` statement to the top of `Controllers/AccountController.cs`.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="fa632-166">Ersetzen Sie die vorhandene `SignOut`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="fa632-166">Replace the existing `SignOut` function with the following.</span></span>

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

1. <span data-ttu-id="fa632-167">Öffnen `Controllers/BaseController.cs` Sie die folgenden `using` Anweisungen, und fügen Sie sie am oberen Rand der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-167">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. <span data-ttu-id="fa632-168">Fügen Sie die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa632-168">Add the following function.</span></span>

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

1. <span data-ttu-id="fa632-169">Starten Sie den Server, und durchlaufen Sie den Anmeldevorgang.</span><span class="sxs-lookup"><span data-stu-id="fa632-169">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="fa632-170">Sie sollten wieder auf der Startseite landen, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="fa632-170">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. <span data-ttu-id="fa632-172">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den Link **"Abmelden" zu** klicken.</span><span class="sxs-lookup"><span data-stu-id="fa632-172">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="fa632-173">Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="fa632-173">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="fa632-175">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="fa632-175">Refreshing tokens</span></span>

<span data-ttu-id="fa632-176">An diesem Punkt verfügt Ihre Anwendung über ein Zugriffstoken, das im `Authorization` Header von API-Aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="fa632-176">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="fa632-177">Dies ist das Token, durch das die App im Namen des Benutzers auf Microsoft Graph zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="fa632-177">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="fa632-178">Dieses Token ist jedoch nur kurzzeitig verfügbar.</span><span class="sxs-lookup"><span data-stu-id="fa632-178">However, this token is short-lived.</span></span> <span data-ttu-id="fa632-179">Das Token läuft eine Stunde nach seiner Entsprechung ab.</span><span class="sxs-lookup"><span data-stu-id="fa632-179">The token expires an hour after it is issued.</span></span> <span data-ttu-id="fa632-180">An dieser Stelle kommt das Aktualisierungstoken ins Spiel.</span><span class="sxs-lookup"><span data-stu-id="fa632-180">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="fa632-181">Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="fa632-181">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="fa632-182">Da die App die MSAL-Bibliothek verwendet und das Objekt serialisiert, müssen Sie keine `TokenCache` Tokenaktualisierungslogik implementieren.</span><span class="sxs-lookup"><span data-stu-id="fa632-182">Because the app is using the MSAL library and serializing the `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="fa632-183">Die `ConfidentialClientApplication.AcquireTokenSilentAsync`-Methode übernimmt die Logik-Aufgaben für Sie.</span><span class="sxs-lookup"><span data-stu-id="fa632-183">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="fa632-184">Zuerst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fa632-184">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="fa632-185">Wenn es abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="fa632-185">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="fa632-186">Sie verwenden diese Methode im folgenden Modul.</span><span class="sxs-lookup"><span data-stu-id="fa632-186">You'll use this method in the following module.</span></span>
