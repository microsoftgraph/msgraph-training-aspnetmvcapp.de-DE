# <a name="how-to-run-the-completed-project"></a>Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:

- [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert. Wenn Sie nicht über Visual Studio verfügen, besuchen Sie den vorherigen Link für Downloadoptionen. (**Hinweis:** dieses Lernprogramm wurde mit der Version 15,81 von Visual Studio 2017 geschrieben. Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, die jedoch nicht getestet wurden.)
- Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Geschäfts-oder Schulkonto.

Wenn Sie nicht über ein Microsoft-Konto verfügen, gibt es eine Reihe von Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können [sich für das office 365-Entwicklerprogramm anmelden](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-a-web-application-with-the-application-registration-portal"></a>Registrieren einer Webanwendung mit dem Anwendungs Registrierungs Portal

1. Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.

    > **Hinweis:** Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.

1. Legen Sie auf der Seite **Anwendung registrieren** den **anwendungsnamen** auf **ASP.NET Graph-Lernprogramm** fest, und wählen Sie **Erstellen**aus.

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](/tutorial/images/arp-create-app-01.png)

1. Kopieren Sie auf der Seite **ASP.NET Graph Tutorial-Registrierung** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.

    ![Screenshot der neu erstellten Anwendungs-ID](/tutorial/images/arp-create-app-02.png)

1. Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .

    1. Wählen Sie **Neues Kennwort generieren**aus.
    1. Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.

        > **Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.

    ![Screenshot des Kennworts der neu erstellten Anwendung](/tutorial/images/arp-create-app-03.png)

1. Scrollen Sie nach unten zum Abschnitt **Plattformen** .

    1. Wählen Sie **Plattform hinzufügen**aus.
    1. Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.

        ![Screenshot Erstellen einer Plattform für die APP](/tutorial/images/arp-create-app-04.png)

    1. Geben Sie **** im Feld Webplattform die URL `http://localhost:64107/` für die Umleitungs- **URLs**ein.

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](/tutorial/images/arp-create-app-05.png)

1. Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `PrivateSettings.config.example` die Datei `PrivateSettings.config`in um.
1. Bearbeiten Sie `PrivateSettings.config` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.
    1. Ersetzen `YOUR_APP_PASSWORD_HERE` Sie durch das Kennwort, das Sie im App-Registrierungs Portal erhalten haben.
1. Öffnen `graph-tutorial.sln` Sie in Visual Studio. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die **Graph-Tutorial-** Lösung, und wählen Sie **NuGet-Pakete wiederherstellen**.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Drücken Sie in Visual Studio **F5** , oder klicken Sie auf **Debuggen > Start Debugging**.