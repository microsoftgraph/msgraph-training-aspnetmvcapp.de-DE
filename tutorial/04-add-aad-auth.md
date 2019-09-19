<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d31e6-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="d31e6-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zu erhalten, um die Microsoft Graph-API aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="d31e6-103">In diesem Schritt werden Sie die OWIN-Middleware und die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://www.nuget.org/packages/Microsoft.Identity.Client/) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

1. <span data-ttu-id="d31e6-104">Klicken Sie mit der rechten Maustaste auf das **Graph-Tutorial-** Projekt im Projektmappen-Explorer, und wählen Sie **#a0 neues Element hinzufügen aus...**. Wählen Sie **Webkonfigurationsdatei**aus, benennen Sie `PrivateSettings.config` die Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-104">Right-click the **graph-tutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and select **Add**.</span></span> <span data-ttu-id="d31e6-105">Ersetzen sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-105">Replace its entire contents with the following code.</span></span>

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    <span data-ttu-id="d31e6-106">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie durch den von Ihnen generierten Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="d31e6-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="d31e6-107">Wenn Ihr geheimer Client Schlüssel kaufmännische`&`und-Zeichen enthält (), müssen `&amp;` Sie `PrivateSettings.config`Sie unbedingt durch in ersetzen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="d31e6-108">Stellen Sie außerdem sicher, dass `PORT` Sie den Wert `ida:RedirectUri` für die an die URL der Anwendung anpassen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d31e6-109">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `PrivateSettings.config` aus der Quellcodeverwaltung auszuschließen, damit versehentlich keine APP-ID und Ihr Kennwort verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="d31e6-110">Aktualisieren `Web.config` , um diese neue Datei zu laden.</span><span class="sxs-lookup"><span data-stu-id="d31e6-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="d31e6-111">Ersetzen Sie `<appSettings>` die (Reihe 7) durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d31e6-111">Replace the `<appSettings>` (line 7) with the following</span></span>

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="d31e6-112">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d31e6-112">Implement sign-in</span></span>

<span data-ttu-id="d31e6-113">Beginnen Sie mit der Initialisierung der OWIN-Middleware, um Azure AD-Authentifizierung für die APP zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d31e6-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span>

1. <span data-ttu-id="d31e6-114">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **App_Start** , und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `Startup.Auth.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-114">Right-click the **App_Start** folder in Solution Explorer and select **Add > Class...**. Name the file `Startup.Auth.cs` and select **Add**.</span></span> <span data-ttu-id="d31e6-115">Ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-115">Replace the entire contents with the following code.</span></span>

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
    > <span data-ttu-id="d31e6-116">Dieser Code konfiguriert die OWIN-Middleware mit den Werten aus `PrivateSettings.config` und definiert zwei Rückrufmethoden `OnAuthenticationFailedAsync` und. `OnAuthorizationCodeReceivedAsync`</span><span class="sxs-lookup"><span data-stu-id="d31e6-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="d31e6-117">Diese Rückrufmethoden werden aufgerufen, wenn der Anmeldeprozess von Azure zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d31e6-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

1. <span data-ttu-id="d31e6-118">Aktualisieren Sie nun `Startup.cs` die Datei, um `ConfigureAuth` die-Methode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="d31e6-119">Ersetzen Sie den gesamten Inhalt `Startup.cs` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

1. <span data-ttu-id="d31e6-120">Fügen Sie `Error` der `HomeController` -Klasse eine Aktion hinzu, `message` um `debug` die-und Abfrage `Alert` Parameter in ein-Objekt zu transformieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="d31e6-121">Öffnen `Controllers/HomeController.cs` Sie und fügen Sie die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. <span data-ttu-id="d31e6-122">Hinzufügen eines Controllers zur Behandlung der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d31e6-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="d31e6-123">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller** , und wählen Sie **#a0 Controller hinzufügen**aus.... Wählen Sie **MVC 5 Controller – leer** , und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-123">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="d31e6-124">Nennen Sie den `AccountController` Controller, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-124">Name the controller `AccountController` and select **Add**.</span></span> <span data-ttu-id="d31e6-125">Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-125">Replace the entire contents of the file with the following code.</span></span>

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

    <span data-ttu-id="d31e6-126">Dies definiert eine `SignIn` and `SignOut` -Aktion.</span><span class="sxs-lookup"><span data-stu-id="d31e6-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="d31e6-127">Die `SignIn` Aktion überprüft, ob die Anforderung bereits authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="d31e6-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="d31e6-128">Wenn dies nicht der Fall ist, wird die OWIN-Middleware aufgerufen, um den Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="d31e6-129">Die `SignOut` Aktion ruft die OWIN-Middleware auf, um sich abzumelden.</span><span class="sxs-lookup"><span data-stu-id="d31e6-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

1. <span data-ttu-id="d31e6-130">Speichern Sie Ihre Änderungen, und starten Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d31e6-130">Save your changes and start the project.</span></span> <span data-ttu-id="d31e6-131">Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="d31e6-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="d31e6-132">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="d31e6-133">Der Browser wird an die APP umgeleitet, wobei das Token angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d31e6-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="d31e6-134">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="d31e6-134">Get user details</span></span>

<span data-ttu-id="d31e6-135">Sobald der Benutzer angemeldet ist, können Sie seine Informationen aus Microsoft Graph abrufen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-135">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="d31e6-136">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **#a0 neuen Ordner hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-136">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="d31e6-137">Nennen Sie den `Helpers`Ordner.</span><span class="sxs-lookup"><span data-stu-id="d31e6-137">Name the folder `Helpers`.</span></span>

1. <span data-ttu-id="d31e6-138">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `GraphHelper.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-138">Right click this new folder and select **Add > Class...**. Name the file `GraphHelper.cs` and select **Add**.</span></span> <span data-ttu-id="d31e6-139">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-139">Replace the contents of this file with the following code.</span></span>

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

    <span data-ttu-id="d31e6-140">Dadurch wird die `GetUserDetails` Funktion implementiert, die das Microsoft Graph-SDK verwendet, `/me` um den Endpunkt aufzurufen und das Ergebnis zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="d31e6-141">Aktualisieren Sie `OnAuthorizationCodeReceivedAsync` die- `App_Start/Startup.Auth.cs` Methode in, um diese Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="d31e6-142">Fügen Sie die `using` folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-142">Add the following `using` statement to the top of the file.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    ```

1. <span data-ttu-id="d31e6-143">Ersetzen Sie den `try` vorhandenen Block `OnAuthorizationCodeReceivedAsync` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

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

1. <span data-ttu-id="d31e6-144">Speichern Sie Ihre Änderungen, und starten Sie die APP, nachdem Sie sich angemeldet haben, sollten Sie den Namen und die e-Mail-Adresse des Benutzers anstelle des Zugriffstokens sehen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-144">Save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="d31e6-145">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="d31e6-145">Storing the tokens</span></span>

<span data-ttu-id="d31e6-146">Nachdem Sie nun Token erhalten können, ist es an der Zeit, eine Möglichkeit zum Speichern in der APP zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="d31e6-147">Da es sich hierbei um eine Beispiel-App handelt, verwenden Sie die Sitzung zum Speichern der Token.</span><span class="sxs-lookup"><span data-stu-id="d31e6-147">Since this is a sample app, you will use the session to store the tokens.</span></span> <span data-ttu-id="d31e6-148">Eine reale APP würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="d31e6-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span> <span data-ttu-id="d31e6-149">In diesem Abschnitt erfahren Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d31e6-149">In this section, you will:</span></span>

- <span data-ttu-id="d31e6-150">Implementieren Sie eine Tokenspeicher-Klasse, um den MSAL-Token-Cache und die Details des Benutzers in der Benutzersitzung zu serialisieren und zu speichern.</span><span class="sxs-lookup"><span data-stu-id="d31e6-150">Implement a token store class to serialize and store the MSAL token cache and the user's details in the user session.</span></span>
- <span data-ttu-id="d31e6-151">Aktualisieren Sie den Authentifizierungscode für die Verwendung der Token-Speicherklasse.</span><span class="sxs-lookup"><span data-stu-id="d31e6-151">Update the authentication code to use the token store class.</span></span>
- <span data-ttu-id="d31e6-152">Aktualisieren Sie die Basis Controllerklasse, um die gespeicherten Benutzer Details für alle Ansichten in der Anwendung verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-152">Update the base controller class to expose the stored user details to all views in the application.</span></span>

1. <span data-ttu-id="d31e6-153">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Graph-Tutorial** , und wählen Sie **#a0 neuen Ordner hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-153">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="d31e6-154">Nennen Sie den `TokenStorage`Ordner.</span><span class="sxs-lookup"><span data-stu-id="d31e6-154">Name the folder `TokenStorage`.</span></span>

1. <span data-ttu-id="d31e6-155">Klicken Sie mit der rechten Maustaste auf diesen neuen Ordner, und wählen Sie **#a0 Klasse hinzufügen**aus. Nennen Sie die `SessionTokenStore.cs` Datei, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-155">Right click this new folder and select **Add > Class...**. Name the file `SessionTokenStore.cs` and select **Add**.</span></span> <span data-ttu-id="d31e6-156">Ersetzen Sie den Inhalt dieser Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d31e6-156">Replace the contents of this file with the following code.</span></span>

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

1. <span data-ttu-id="d31e6-157">Fügen Sie am `using` Anfang der `App_Start/Startup.Auth.cs` Datei die folgenden Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-157">Add the following `using` statements to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. <span data-ttu-id="d31e6-158">Ersetzen Sie die vorhandene `OnAuthorizationCodeReceivedAsync`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d31e6-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

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
    > <span data-ttu-id="d31e6-159">Die Änderungen in dieser neuen Version von `OnAuthorizationCodeReceivedAsync` gehen wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="d31e6-159">The changes in this new version of `OnAuthorizationCodeReceivedAsync` do the following:</span></span>
    >
    > - <span data-ttu-id="d31e6-160">Der Code umschließt nun den `ConfidentialClientApplication`standardmäßigen Benutzertoken-Cache `SessionTokenStore` mit der-Klasse.</span><span class="sxs-lookup"><span data-stu-id="d31e6-160">The code now wraps the `ConfidentialClientApplication`'s default user token cache with the `SessionTokenStore` class.</span></span> <span data-ttu-id="d31e6-161">Die MSAL-Bibliothek verarbeitet die Logik, die Token zu speichern und bei Bedarf zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
    > - <span data-ttu-id="d31e6-162">Der Code übergibt nun die von Microsoft Graph erhaltenen Benutzer Details an `SessionTokenStore` das Objekt, das in der Sitzung gespeichert werden soll.</span><span class="sxs-lookup"><span data-stu-id="d31e6-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
    > - <span data-ttu-id="d31e6-163">Bei Erfolg wird der Code nicht mehr umgeleitet, sondern nur zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="d31e6-164">Auf diese Weise kann die OWIN-Middleware den Authentifizierungsprozess abschließen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-164">This allows the OWIN middleware to complete the authentication process.</span></span>

1. <span data-ttu-id="d31e6-165">Aktualisieren Sie `SignOut` die Aktion, um den Tokenspeicher vor dem Abmelden zu löschen. Fügen Sie die `using` folgende Anweisung oben in hinzu `Controllers/AccountController.cs`.</span><span class="sxs-lookup"><span data-stu-id="d31e6-165">Update the `SignOut` action to clear the token store before signing out. Add the following `using` statement to the top of `Controllers/AccountController.cs`.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="d31e6-166">Ersetzen Sie die vorhandene `SignOut`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d31e6-166">Replace the existing `SignOut` function with the following.</span></span>

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

1. <span data-ttu-id="d31e6-167">Öffnen `Controllers/BaseController.cs` Sie und fügen Sie `using` die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-167">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. <span data-ttu-id="d31e6-168">Fügen Sie die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="d31e6-168">Add the following function.</span></span>

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

1. <span data-ttu-id="d31e6-169">Starten Sie den Server, und fahren Sie mit dem Anmeldevorgang fort.</span><span class="sxs-lookup"><span data-stu-id="d31e6-169">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="d31e6-170">Sie sollten wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="d31e6-170">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Ein Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

1. <span data-ttu-id="d31e6-172">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d31e6-172">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="d31e6-173">Durch Klicken auf **Abmelden** wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="d31e6-173">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Screenshot des Dropdownmenüs mit dem Link zum Abmelden](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="d31e6-175">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="d31e6-175">Refreshing tokens</span></span>

<span data-ttu-id="d31e6-176">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="d31e6-176">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="d31e6-177">Dies ist das Token, mit dem die APP im Namen des Benutzers auf Microsoft Graph zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="d31e6-177">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="d31e6-178">Dieses Token ist jedoch nur kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="d31e6-178">However, this token is short-lived.</span></span> <span data-ttu-id="d31e6-179">Das Token läuft eine Stunde nach seiner Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="d31e6-179">The token expires an hour after it is issued.</span></span> <span data-ttu-id="d31e6-180">Hier wird das Aktualisierungstoken nützlich.</span><span class="sxs-lookup"><span data-stu-id="d31e6-180">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="d31e6-181">Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass sich der Benutzer erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="d31e6-181">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="d31e6-182">Da die APP die MSAL-Bibliothek verwendet und das `TokenCache` Objekt serialisiert, müssen Sie keine Logik für die Token-Aktualisierung implementieren.</span><span class="sxs-lookup"><span data-stu-id="d31e6-182">Because the app is using the MSAL library and serializing the `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="d31e6-183">Die `ConfidentialClientApplication.AcquireTokenSilentAsync` -Methode führt die gesamte Logik für Sie aus.</span><span class="sxs-lookup"><span data-stu-id="d31e6-183">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="d31e6-184">Zunächst wird das zwischengespeicherte Token überprüft, und wenn es nicht abgelaufen ist, wird es zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d31e6-184">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="d31e6-185">Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d31e6-185">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="d31e6-186">Sie verwenden diese Methode im folgenden Modul.</span><span class="sxs-lookup"><span data-stu-id="d31e6-186">You'll use this method in the following module.</span></span>
