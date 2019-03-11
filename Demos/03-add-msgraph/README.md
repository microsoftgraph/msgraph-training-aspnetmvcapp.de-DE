# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="bd09c-101">Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="bd09c-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd09c-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="bd09c-102">Prerequisites</span></span>

<span data-ttu-id="bd09c-103">Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bd09c-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="bd09c-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="bd09c-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="bd09c-105">Wenn Sie nicht über Visual Studio verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="bd09c-105">If you do not have Visual Studio, visit the previous link for download options.</span></span> <span data-ttu-id="bd09c-106">(**Hinweis:** dieses Lernprogramm wurde mit der Version 15,81 von Visual Studio 2017 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="bd09c-106">(**Note:** This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="bd09c-107">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, die jedoch nicht getestet wurden.)</span><span class="sxs-lookup"><span data-stu-id="bd09c-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="bd09c-108">Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Geschäfts-oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="bd09c-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="bd09c-109">Wenn Sie nicht über ein Microsoft-Konto verfügen, gibt es eine Reihe von Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="bd09c-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="bd09c-110">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="bd09c-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="bd09c-111">Sie können [sich für das office 365-Entwicklerprogramm anmelden](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="bd09c-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="bd09c-112">Registrieren einer Webanwendung mit dem Anwendungs Registrierungs Portal</span><span class="sxs-lookup"><span data-stu-id="bd09c-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="bd09c-113">Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="bd09c-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="bd09c-114">Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.</span><span class="sxs-lookup"><span data-stu-id="bd09c-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="bd09c-115">Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="bd09c-116">**Hinweis:** Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.</span><span class="sxs-lookup"><span data-stu-id="bd09c-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="bd09c-117">Legen Sie auf der Seite **Anwendung registrieren** den **anwendungsnamen** auf **ASP.NET Graph-Lernprogramm** fest, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-117">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="bd09c-119">Kopieren Sie auf der Seite **ASP.NET Graph Tutorial-Registrierung** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.</span><span class="sxs-lookup"><span data-stu-id="bd09c-119">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Screenshot der neu erstellten Anwendungs-ID](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="bd09c-121">Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .</span><span class="sxs-lookup"><span data-stu-id="bd09c-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="bd09c-122">Wählen Sie **Neues Kennwort generieren**aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="bd09c-123">Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.</span><span class="sxs-lookup"><span data-stu-id="bd09c-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="bd09c-124">**Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.</span><span class="sxs-lookup"><span data-stu-id="bd09c-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Screenshot des Kennworts der neu erstellten Anwendung](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="bd09c-126">Scrollen Sie nach unten zum Abschnitt **Plattformen** .</span><span class="sxs-lookup"><span data-stu-id="bd09c-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="bd09c-127">Wählen Sie **Plattform hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="bd09c-128">Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Screenshot Erstellen einer Plattform für die APP](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="bd09c-130">Geben Sie \*\*\*\* im Feld Webplattform die URL `http://localhost:64107/` für die Umleitungs- **URLs**ein.</span><span class="sxs-lookup"><span data-stu-id="bd09c-130">In the **Web** platform box, enter the URL `http://localhost:64107/` for the **Redirect URLs**.</span></span>

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="bd09c-132">Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.</span><span class="sxs-lookup"><span data-stu-id="bd09c-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="bd09c-133">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="bd09c-133">Configure the sample</span></span>

1. <span data-ttu-id="bd09c-134">Benennen Sie `PrivateSettings.config.example` die Datei `PrivateSettings.config`in um.</span><span class="sxs-lookup"><span data-stu-id="bd09c-134">Rename the `PrivateSettings.config.example` file to `PrivateSettings.config`.</span></span>
1. <span data-ttu-id="bd09c-135">Bearbeiten Sie `PrivateSettings.config` die Datei, und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="bd09c-135">Edit the `PrivateSettings.config` file and make the following changes.</span></span>
    1. <span data-ttu-id="bd09c-136">Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="bd09c-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="bd09c-137">Ersetzen `YOUR_APP_PASSWORD_HERE` Sie durch das Kennwort, das Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="bd09c-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="bd09c-138">Öffnen `graph-tutorial.sln` Sie in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bd09c-138">Open `graph-tutorial.sln` in Visual Studio.</span></span> <span data-ttu-id="bd09c-139">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die **Graph-Tutorial-** Lösung, und wählen Sie **NuGet-Pakete wiederherstellen**.</span><span class="sxs-lookup"><span data-stu-id="bd09c-139">In Solution Explorer, right-click the **graph-tutorial** solution and choose **Restore NuGet Packages**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="bd09c-140">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="bd09c-140">Run the sample</span></span>

<span data-ttu-id="bd09c-141">Drücken Sie in Visual Studio **F5** , oder klicken Sie auf **Debuggen > Start Debugging**.</span><span class="sxs-lookup"><span data-stu-id="bd09c-141">In Visual Studio, press **F5** or choose **Debug > Start Debugging**.</span></span>