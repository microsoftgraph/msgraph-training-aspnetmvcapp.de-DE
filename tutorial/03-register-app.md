<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="58469-101">In dieser Übung erstellen Sie mithilfe des Azure Active Directory Admin Center eine neue Azure AD-Webanwendungs Registrierung.</span><span class="sxs-lookup"><span data-stu-id="58469-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="58469-102">Bestimmen der URL der ASP.NET-App</span><span class="sxs-lookup"><span data-stu-id="58469-102">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="58469-103">Wählen Sie im Projektmappen-Explorer von Visual Studio das **Graph-Tutorial-** Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="58469-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="58469-104">Suchen Sie im Fenster **Eigenschaften** den Wert von **URL**.</span><span class="sxs-lookup"><span data-stu-id="58469-104">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="58469-105">Kopieren Sie diesen Wert.</span><span class="sxs-lookup"><span data-stu-id="58469-105">Copy this value.</span></span>

    ![Screenshot des Visual Studio Fensters "Eigenschaften"](./images/vs-project-url.png)

1. <span data-ttu-id="58469-107">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="58469-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="58469-108">Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="58469-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="58469-109">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App** -Registrierungen unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="58469-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="58469-110">Ein Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="58469-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="58469-111">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="58469-111">Select **New registration**.</span></span> <span data-ttu-id="58469-112">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="58469-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="58469-113">Legen Sie **Name** auf `ASP.NET Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="58469-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="58469-114">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="58469-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="58469-115">Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Web` fest, und legen Sie den Wert der URL der ASP.NET-App fest, die Sie in Schritt 1 kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="58469-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](./images/aad-register-an-app.png)

1. <span data-ttu-id="58469-117">Wählen Sie **registrieren**aus.</span><span class="sxs-lookup"><span data-stu-id="58469-117">Select **Register**.</span></span> <span data-ttu-id="58469-118">Kopieren Sie auf der Seite **ASP.NET Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, die Sie im nächsten Schritt benötigen.</span><span class="sxs-lookup"><span data-stu-id="58469-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. <span data-ttu-id="58469-120">Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.</span><span class="sxs-lookup"><span data-stu-id="58469-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="58469-121">Suchen Sie den Abschnitt **Implizite Gewährung**, und aktivieren Sie **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="58469-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="58469-122">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="58469-122">Select **Save**.</span></span>

    ![Screenshot des impliziten Grant-Abschnitts](./images/aad-implicit-grant.png)

1. <span data-ttu-id="58469-124">Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheime Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="58469-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="58469-125">Wählen Sie die Schaltfläche **Neuen geheimen Clientschlüssel** aus.</span><span class="sxs-lookup"><span data-stu-id="58469-125">Select the **New client secret** button.</span></span> <span data-ttu-id="58469-126">Geben Sie einen Wert in **Description** ein, und wählen Sie eine der Optionen für **Ablaufdatum** aus, und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="58469-126">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Client Schlüssels](./images/aad-new-client-secret.png)

1. <span data-ttu-id="58469-128">Kopieren Sie den Wert des geheimen Clientschlüssels, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="58469-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="58469-129">Sie benötigen ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="58469-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="58469-130">Dieser geheime Clientschlüssel wird nicht noch einmal angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="58469-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des neu hinzugefügten geheimen Client Schlüssels](./images/aad-copy-client-secret.png)
