<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5929f-101">In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Azure Active Directory Admin Center.</span><span class="sxs-lookup"><span data-stu-id="5929f-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="5929f-102">Bestimmen Sie die URL Ihrer ASP.NET-app.</span><span class="sxs-lookup"><span data-stu-id="5929f-102">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="5929f-103">Wählen Sie im projektMappen-Explorer von Visual Studio das Projekt **Graph-Tutorial** aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="5929f-104">Suchen Sie im Fenster **Eigenschaften** den Wert der **URL**.</span><span class="sxs-lookup"><span data-stu-id="5929f-104">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="5929f-105">Kopieren Sie diesen Wert.</span><span class="sxs-lookup"><span data-stu-id="5929f-105">Copy this value.</span></span>

    ![Screenshot des Eigenschaftenfensters von Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="5929f-107">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="5929f-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="5929f-108">Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.</span><span class="sxs-lookup"><span data-stu-id="5929f-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="5929f-109">Wählen Sie **Azure Active Directory** in der linken Navigationsleiste aus, und wählen Sie dann **App-Registrierungen (Vorschau)** unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="5929f-110">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="5929f-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="5929f-111">Wählen Sie **neue Registrierung**aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-111">Select **New registration**.</span></span> <span data-ttu-id="5929f-112">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="5929f-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="5929f-113">Legen \*\*\*\* Sie Name `ASP.NET Graph Tutorial`auf fest.</span><span class="sxs-lookup"><span data-stu-id="5929f-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="5929f-114">Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis und persönlichen Microsoft-Konten**fest.</span><span class="sxs-lookup"><span data-stu-id="5929f-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="5929f-115">Legen Sie unter umLeitungs- **URI**die erste Dropdown `Web` Liste auf fest, und legen Sie den Wert auf die ASP.net-App-URL fest, die Sie in Schritt 1 kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="5929f-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Screenshot der Seite "Registrieren einer Anwendung"](./images/aad-register-an-app.png)

1. <span data-ttu-id="5929f-117">Wählen Sie **registrieren**aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-117">Choose **Register**.</span></span> <span data-ttu-id="5929f-118">Kopieren Sie auf der Seite **ASP.NET Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="5929f-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. <span data-ttu-id="5929f-120">Wählen Sie unter **Manage**die Option **Authentication** aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="5929f-121">Suchen Sie den **impliziten Grant** -Abschnitt, und aktivieren Sie **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="5929f-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="5929f-122">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-122">Choose **Save**.</span></span>

    ![Screenshot des impliziten Grant-Abschnitts](./images/aad-implicit-grant.png)

1. <span data-ttu-id="5929f-124">Wählen Sie unter **Manage**die Option **Certificates & Secrets** aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="5929f-125">Klicken Sie auf die Schaltfläche **neuen geheimen Client Schlüssel** .</span><span class="sxs-lookup"><span data-stu-id="5929f-125">Select the **New client secret** button.</span></span> <span data-ttu-id="5929f-126">Geben Sie einen Wert in **Description** ein, und wählen Sie eine der Optionen für **Expires** und wählen Sie **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="5929f-126">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Clients](./images/aad-new-client-secret.png)

1. <span data-ttu-id="5929f-128">Kopieren Sie den Client geheimen Wert, bevor Sie diese Seite verlassen.</span><span class="sxs-lookup"><span data-stu-id="5929f-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="5929f-129">Sie benötigen Sie im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="5929f-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="5929f-130">Dieser geheime Client Schlüssel wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="5929f-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des neu hinzugefügten geheimen Clients](./images/aad-copy-client-secret.png)