<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Application Registry Portal (ARP).

1. Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.

    > [!NOTE]
    > Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.

1. Legen Sie auf der Seite **Anwendung registrieren** den **anwendungsnamen** auf **ASP.NET Graph-Lernprogramm** fest, und wählen Sie **Erstellen**aus.

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](./images/arp-create-app-01.png)

1. Kopieren Sie auf der Seite **ASP.NET Graph Tutorial-Registrierung** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.

    ![Screenshot der neu erstellten Anwendungs-ID](./images/arp-create-app-02.png)

1. Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .

    1. Wählen Sie **Neues Kennwort generieren**aus.
    1. Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.

        > **Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.

    ![Screenshot des Kennworts der neu erstellten Anwendung](./images/arp-create-app-03.png)

1. Bestimmen Sie die URL Ihrer ASP.NET-app. Wählen Sie im projektMappen-Explorer von Visual Studio das Projekt **Graph-Tutorial** aus. Suchen Sie im Fenster **Eigenschaften** den Wert der **URL**. Kopieren Sie diesen Wert.

    ![Screenshot des Eigenschaftenfensters von Visual Studio](./images/vs-project-url.png)

1. Scrollen Sie nach unten zum Abschnitt **Plattformen** .

    1. Wählen Sie **Plattform hinzufügen**aus.
    1. Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.

        ![Screenshot Erstellen einer Plattform für die APP](./images/arp-create-app-04.png)

    1. Geben Sie **** im Feld Webplattform die URL ein, die Sie aus den Eigenschaften des Visual Studio-Projekts für die Umleitungs- **URLs**kopiert haben.

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](./images/arp-create-app-05.png)

1. Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.