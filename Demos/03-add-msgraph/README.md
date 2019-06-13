# <a name="how-to-run-the-completed-project"></a>Vorgehensweise Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert. Wenn Sie nicht über Visual Studio verfügen, besuchen Sie den vorherigen Link für Downloadoptionen. (**Hinweis:** dieses Lernprogramm wurde mit Visual Studio 2017 Version 15,81 geschrieben. Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)
- Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto.

Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Registrieren einer Webanwendung im Azure-Active Directory Admin Center

1. Bestimmen Sie die URL Ihrer ASP.NET-Anwendung. Wählen Sie im Projektmappen-Explorer von Visual Studio das **Graph-Tutorial-** Projekt aus. Suchen Sie im Fenster **Eigenschaften** den Wert von **URL**. Kopieren Sie diesen Wert.

    ![Screenshot des Visual Studio Fensters "Eigenschaften"](/tutorial/images/vs-project-url.png)

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App** -Registrierungen unter **Manage**aus.

    ![Ein Screenshot der APP-Registrierungen ](/tutorial/images/aad-portal-app-registrations.png)

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `ASP.NET Graph Tutorial` fest.
    - Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.
    - Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Web` fest, und legen Sie den Wert der URL der ASP.NET-App fest, die Sie in Schritt 1 kopiert haben.

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite **ASP.NET Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, die Sie im nächsten Schritt benötigen.

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus. Suchen Sie den Abschnitt **Implizite Gewährung**, und aktivieren Sie **ID-Token**. Wählen Sie **Speichern** aus.

    ![Screenshot des impliziten Grant-Abschnitts](/tutorial/images/aad-implicit-grant.png)

1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheime Clientschlüssel** aus. Wählen Sie die Schaltfläche **Neuen geheimen Clientschlüssel** aus. Geben Sie einen Wert unter **Beschreibung** ein, wählen Sie eine der Optionen für **Gilt bis** aus, und wählen Sie dann **Hinzufügen**.

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Client Schlüssels](/tutorial/images/aad-new-client-secret.png)

1. Kopieren Sie den Wert des geheimen Clientschlüssels, bevor Sie diese Seite verlassen. Sie benötigen ihn im nächsten Schritt.

    > [!IMPORTANT]
    > Dieser geheime Clientschlüssel wird nicht noch einmal angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.

    ![Screenshot des neu hinzugefügten geheimen Client Schlüssels](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `PrivateSettings.config.example` die Datei `PrivateSettings.config`in.
1. Bearbeiten Sie `PrivateSettings.config` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.
    1. Ersetzen `YOUR_APP_PASSWORD_HERE` Sie durch das **Anwendungs Geheimnis** , das Sie aus dem App-Registrierungs Portal erhalten haben.
    1. Ersetzen `PORT` Sie durch die Portnummer, die als Teil der URL Ihrer ASP.NET-Anwendung enthalten ist.
1. Öffnen `graph-tutorial.sln` Sie in Visual Studio. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die **Grafik Lernprogramm** Lösung, und wählen Sie **NuGet-Pakete wiederherstellen**.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Drücken Sie in Visual Studio **F5** , oder wählen Sie **Debug #a0 Debuggen starten**aus.