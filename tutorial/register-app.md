<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="40e8b-101">In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Application Registry Portal (ARP).</span><span class="sxs-lookup"><span data-stu-id="40e8b-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="40e8b-102">Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="40e8b-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="40e8b-103">Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.</span><span class="sxs-lookup"><span data-stu-id="40e8b-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="40e8b-104">Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="40e8b-105">Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.</span><span class="sxs-lookup"><span data-stu-id="40e8b-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="40e8b-106">Legen Sie auf der Seite **Anwendung registrieren** den **anwendungsnamen** auf **ASP.NET Graph-Lernprogramm** fest, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-106">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](./images/arp-create-app-01.png)

1. <span data-ttu-id="40e8b-108">Kopieren Sie auf der Seite **ASP.NET Graph Tutorial-Registrierung** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.</span><span class="sxs-lookup"><span data-stu-id="40e8b-108">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Screenshot der neu erstellten Anwendungs-ID](./images/arp-create-app-02.png)

1. <span data-ttu-id="40e8b-110">Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .</span><span class="sxs-lookup"><span data-stu-id="40e8b-110">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="40e8b-111">Wählen Sie **Neues Kennwort generieren**aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-111">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="40e8b-112">Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.</span><span class="sxs-lookup"><span data-stu-id="40e8b-112">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="40e8b-113">**Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="40e8b-113">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des Kennworts der neu erstellten Anwendung](./images/arp-create-app-03.png)

1. <span data-ttu-id="40e8b-115">Bestimmen Sie die URL Ihrer ASP.NET-app.</span><span class="sxs-lookup"><span data-stu-id="40e8b-115">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="40e8b-116">Wählen Sie im projektMappen-Explorer von Visual Studio das Projekt **Graph-Tutorial** aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-116">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="40e8b-117">Suchen Sie im Fenster **Eigenschaften** den Wert der **URL**.</span><span class="sxs-lookup"><span data-stu-id="40e8b-117">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="40e8b-118">Kopieren Sie diesen Wert.</span><span class="sxs-lookup"><span data-stu-id="40e8b-118">Copy this value.</span></span>

    ![Screenshot des Eigenschaftenfensters von Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="40e8b-120">Scrollen Sie nach unten zum Abschnitt **Plattformen** .</span><span class="sxs-lookup"><span data-stu-id="40e8b-120">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="40e8b-121">Wählen Sie **Plattform hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-121">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="40e8b-122">Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-122">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Screenshot Erstellen einer Plattform für die APP](./images/arp-create-app-04.png)

    1. <span data-ttu-id="40e8b-124">Geben Sie \*\*\*\* im Feld Webplattform die URL ein, die Sie aus den Eigenschaften des Visual Studio-Projekts für die Umleitungs- **URLs**kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="40e8b-124">In the **Web** platform box, enter the URL you copied from the Visual Studio project's properties for the **Redirect URLs**.</span></span>

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](./images/arp-create-app-05.png)

1. <span data-ttu-id="40e8b-126">Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.</span><span class="sxs-lookup"><span data-stu-id="40e8b-126">Scroll to the bottom of the page and select **Save**.</span></span>