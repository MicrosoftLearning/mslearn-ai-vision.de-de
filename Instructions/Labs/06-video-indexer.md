---
lab:
  title: Analysieren von Videos
  description: 'Verwenden Sie Azure KI Video Indexer, um ein Video zu analysieren.'
---

# Analysieren von Videos

Ein großer Teil der heute erstellten und verbrauchten Daten liegt im Videoformat vor. **Azure KI Video Indexer** ist ein KI-gestützter Dienst, mit dem Sie Videos indizieren und daraus Erkenntnisse gewinnen können.

> **Hinweis**: Ab dem 21. Juni 2022 sind die Funktionen der Azure KI Services, die personenbezogene Informationen zurückgeben, auf Kunden beschränkt, denen [eingeschränkter Zugriff](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access) gewährt wurde. Ohne Genehmigung für eingeschränkten Zugriff ist die Erkennung von Personen und Prominenten mit Video Indexer für dieses Lab nicht verfügbar. Weitere Informationen zu den von Microsoft vorgenommenen Änderungen und den Gründen hierfür finden Sie unter [Responsible AI investments and safeguards for facial recognition](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/) (Verantwortungsvolle KI-Investitionen und Vorsichtsmaßnahmen für die Gesichtserkennung).

## Hochladen eines Videos in Video Indexer

Zunächst müssen Sie sich beim Video Indexer-Portal anmelden und ein Video hochladen.

1. Öffnen Sie in Ihrem Browser das [Video Indexer-Portal](https://www.videoindexer.ai) unter `https://www.videoindexer.ai`.
1. Wenn Sie bereits über ein Video Indexer-Konto verfügen, melden Sie sich an. Andernfalls registrieren Sie sich für ein kostenloses Konto und melden sich mit Ihrem Microsoft-Konto (oder einer anderen gültigen Kontoart) an. Wenn Sie Probleme beim Anmelden haben, versuchen Sie, eine private Browsersitzung zu öffnen.

    > Hinweis: Wenn Sie sich zum ersten Mal anmelden, wird möglicherweise ein Popupformular angezeigt, in dem Sie gefragt werden, wie Sie den Dienst verwenden werden. 

1. Laden Sie auf einer neuen Registerkarte das Video „Verantwortungsvolle KI“ von `https://aka.ms/responsible-ai-video` herunter. Speichern Sie die Datei .
1. Wählen Sie im Video Indexer die Option **Hochladen** aus. Wählen Sie anschließend die Option **Dateien suchen** und dann das heruntergeladene Video aus. Wählen Sie **Hinzufügen** aus. Ändern Sie den Text im Feld **Dateiname** in **Verantwortungsvolle KI**. Wählen Sie **Überprüfen+ Hochladen** aus, überprüfen Sie die Zusammenfassungsübersicht, aktivieren Sie das Kontrollkästchen zum Bestätigen der Einhaltung der Microsoft-Richtlinien für die Gesichtserkennung und laden Sie die Datei hoch.
1. Warten Sie nach dem Hochladen der Datei einige Minuten, während Video Indexer sie automatisch indiziert.

> **Hinweis:** In dieser Übung verwenden wir dieses Video, um die Funktionalität von Video Indexer zu erkunden. Sie sollten sich jedoch die Zeit nehmen, es vollständig anzusehen, wenn Sie die Übung beendet haben, da es nützliche Informationen und Anleitungen für die verantwortungsvolle Entwicklung von KI-gestützten Anwendungen enthält! 

## Überprüfen von Erkenntnissen aus Videos

Der Indizierungsprozess extrahiert Erkenntnisse aus dem Video, das Sie im Portal ansehen können.

1. Wenn das Video indiziert ist, wählen Sie es im Video Indexer-Portal aus, um es anzuzeigen. Der Videoplayer wird neben einem Bereich angezeigt, in dem die aus dem Video extrahierten Erkenntnisse angezeigt werden.

    > **Hinweis**: Aufgrund der Richtlinie für Eingeschränkten Zugriff zum Schutz von Einzelpersonenidentitäten werden beim Indizieren des Videos möglicherweise keine Namen angezeigt.

    ![Video Indexer mit Videoplayer und Erkenntnisbereich](../media/video-indexer-insights.png)

1. Wählen Sie während der Wiedergabe des Videos die Registerkarte **Zeitachse** aus, um das Transkript der Audiodaten des Videos anzuzeigen.

    ![Video Indexer mit Videoplayer und Zeitachsenbereich, der das Videotranskript anzeigt.](../media/video-indexer-transcript.png)

1. Wählen Sie oben rechts im Portal das Symbol **Ansicht** (das ähnlich aussieht wie &#128455;) aus, und wählen Sie dann in der Liste der Erkenntnisse neben **Transkript** auch **OCR** und **Sprecher** aus.

    ![Video Indexer-Ansichtsmenü mit ausgewählter Option „Transkript“, „OCR“ und „Sprecher“](../media/video-indexer-view-menu.png)

1. Beachten Sie, dass das Fenster **Zeitachse** jetzt Folgendes enthält:
    - Transkript der Audioausgabe.
    - Im Video sichtbarer Text.
    - Angaben zu den Sprechern, die in dem Video auftreten. Einige bekannte Personen werden automatisch anhand ihres Namens erkannt, andere werden durch eine Nummer angezeigt (z. B. *Speaker #1* (Sprecher Nr. 1)).
1. Wechseln Sie zurück zum Fenster **Insights** (Erkenntnisse), und sehen Sie sich die dort gezeigten Erkenntnisse an. Dazu gehören:
    - Einzelne Personen, die in dem Video vorkommen.
    - Themen, die in dem Video besprochen werden.
    - Bezeichnungen für Objekte, die in dem Video vorkommen.
    - Benannte Entitäten, z. B. Personen und Marken, die in dem Video vorkommen.
    - Wichtige Szenen.
1. Wenn der Bereich **Insights** (Erkenntnisse) angezeigt wird, wählen Sie erneut das Symbol **Ansicht** aus, und fügen Sie in der Liste der Erkenntnisse die **Schlüsselwörter** und **Stimmungen** zu dem Bereich hinzu.

    Die gefundenen Erkenntnisse können Ihnen helfen, die Hauptthemen im Video zu bestimmen. Die **Themen** für dieses Video zeigen beispielsweise, dass es eindeutig um Technologie, soziale Verantwortung und Ethik geht.

## Suchen nach Erkenntnissen

Sie können Video Indexer verwenden, um das Video nach Erkenntnissen zu durchsuchen.

1. Geben Sie im Fenster **Insights** (Erkenntnisse) in das Feld **Suchen** den Eintrag *Bee* (Biene) ein. Möglicherweise müssen Sie im Bereich „Erkenntnisse“ nach unten scrollen, um die Ergebnisse für alle Arten von Erkenntnissen anzuzeigen.
1. Beachten Sie, dass eine übereinstimmende *Bezeichnung* gefunden wird, dessen Position im Video unten angegeben ist.
1. Wählen Sie den Anfang des Abschnitts aus, in dem eine Biene angezeigt wird, und sehen Sie sich das Video an dieser Stelle an (möglicherweise müssen Sie das Video anhalten und sorgfältig auswählen – die Biene erscheint nur kurz!)

    ![Suchergebnisse für die Video Indexer-Suche für Bee](../media/video-indexer-search.png)

1. Deaktivieren Sie das Feld **Suchen**, um alle Erkenntnisse für das Video anzuzeigen.

## Verwenden der REST-API für den Video Indexer

Video Indexer stellt eine REST-API zur Verfügung, mit der Sie Videos in Ihr Konto hochladen und verwalten können.

1. Öffnen Sie in einer neuen Browserregisterkarte das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie die vorhandene Registerkarte nicht, während Sie das Video Indexer-Portal öffnen.
1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen. Wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement aus.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

    > **Hinweis:** Wenn Sie vom Portal aufgefordert werden, einen Speicher auszuwählen, um Ihre Dateien beizubehalten, wählen Sie **Kein Speicherkonto erforderlich** aus. Wählen Sie das verwendete Abonnement aus und drücken Sie auf **Anwenden**.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Ändern Sie die Größe des Cloud Shell-Bereichs, damit Sie mehr davon anzeigen zu können.

    > **Tipp** Sie können die Größe des Bereichs ändern, indem Sie den oberen Rahmen ziehen. Sie können außerdem die Schaltflächen zum Minimieren und Maximieren verwenden, um zwischen Cloud Shell und der Hauptschnittstelle des Portals zu wechseln.

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung für diese Übung enthält:  

    ```
   cd mslearn-ai-vision/Labfiles/video-indexer
    ```

### Abrufen Ihrer API-Details

Um die Video Indexer-API verwenden zu können, benötigen Sie einige Informationen zum Authentifizieren von Anforderungen:

1. Erweitern Sie im Video Indexer-Portal den linken Bereich, und wählen Sie die Seite **Kontoeinstellungen** aus.
1. Notieren Sie sich die **Konto-ID** auf dieser Seite, da Sie sie später benötigen werden.
1. Öffnen Sie eine neue Browserregisterkarte und wechseln Sie zum [Video Indexer-Entwicklerportal](https://api-portal.videoindexer.ai) unter https://api-portal.videoindexer.ai, indem Sie sich mit Ihren Azure-Anmeldeinformationen anmelden.
1. Zeigen Sie auf der Seite **Profil** die **Abonnements** an, die Ihrem Profil zugeordnet sind.
1. Auf der Seite mit Ihren Abonnements sehen Sie, dass Ihnen für jedes Abonnement zwei Schlüssel (primär und sekundär) zugewiesen wurden. Wählen Sie dann **Show** (Anzeigen) für einen der Schlüssel aus, um ihn anzuzeigen. Sie werden diesen Schlüssel in Kürze benötigen.

### Verwenden der REST-API

Nachdem Sie nun über die Konto-ID und einen API-Schlüssel verfügen, können Sie die REST-API verwenden, um mit Videos in Ihrem Konto zu arbeiten. In diesem Verfahren verwenden Sie ein PowerShell-Skript, um REST-Aufrufe zu tätigen. Die gleichen Prinzipien gelten jedoch auch für HTTP-Dienstprogramme wie cURL oder Postman oder jede Programmiersprache, die JSON über HTTP senden und empfangen kann.

Alle Interaktionen mit der Video Indexer-REST-API folgen dem gleichen Muster:

- Eine erste Anforderung an die Methode **AccessToken** mit dem API-Schlüssel in der Kopfzeile wird verwendet, um ein Zugriffstoken zu erhalten.
- Nachfolgende Anforderungen verwenden das Zugriffstoken zur Authentifizierung, wenn Sie REST-Methoden zur Arbeit mit Videos aufrufen.

1. Verwenden Sie in der Cloud Shell den folgenden Befehl, um das PowerShell-Skript zu öffnen:

    ```
   code get-videos.ps1
    ```
    
1. Ersetzen Sie im PowerShell-Skript die Platzhalter **YOUR_ACCOUNT_ID** und **YOUR_API_KEY** durch die Werte für die Konto-ID und den API-Schlüssel, die Sie zuvor ermittelt haben.
1. Beachten Sie, dass der *Standort* für ein kostenloses Konto „trial“ ist. Wenn Sie ein uneingeschränktes Video Indexer-Konto (mit einer zugehörigen Azure-Ressource) erstellt haben, können Sie dies in den Standort ändern, an dem Ihre Azure-Ressource bereitgestellt wird (z. B. „eastus“).
1. Prüfen Sie den Code im Skript und stellen Sie fest, dass zwei REST-Methoden aufgerufen werden: eine zum Abrufen eines Zugriffstokens und eine weitere zum Auflisten der Videos in Ihrem Konto.
1. Speichern Sie Ihre Änderungen (drücken Sie *STRG+S*), schließen Sie den Code-Editor (drücken Sie *STRG+Q*) und führen Sie dann den folgenden Befehl aus, um das Skript auszuführen:

    ```
   ./get-videos.ps1
    ```
    
1. Zeigen Sie die JSON-Antwort des REST-Diensts an, die Details zu dem **Responsible AI**-Video (Verantwortungsbewusste künstliche Intelligenz) enthalten sollte, das Sie zuvor indiziert haben.

## Video Indexer-Widgets nutzen

Das Video Indexer-Portal ist eine nützliche Schnittstelle zum Verwalten von Projekten zur Videoindizierung. Es kann jedoch vorkommen, dass Sie das Video und seine Erkenntnisse auch Personen zugänglich machen möchten, die keinen Zugriff auf Ihr Video Indexer-Konto haben. Video Indexer bietet Widgets, die Sie zu diesem Zweck in eine Webseite einbetten können.

1. Verwenden Sie den Befehl `ls`, um den Inhalt des Ordners **video-indexer** anzuzeigen. Achten Sie darauf, dass er die Datei **analyze-video.html** enthält. Dies ist eine einfache HTML-Seite, zu der Sie die Widgets **Player** und **Insights** (Erkenntnisse) von Video Indexer hinzufügen werden.
1. Geben Sie zum Bearbeiten der Datei den folgenden Befehl ein:

    ```
   code analyze-video.html
    ```

    Die Datei wird in einem Code-Editor geöffnet.
   
1. Beachten Sie den Verweis auf das Skript **vb.widgets.mediator.js** in der Kopfzeile – dieses Skript ermöglicht es mehreren Video Indexer-Widgets auf der Seite, miteinander zu interagieren.
1. Kehren Sie im Video Indexer-Portal zur Seite **Mediendateien** zurück und öffnen Sie Ihr **Verantwortungsbewusste KI**-Video.
1. Wählen Sie unter dem Videoplayer **&lt;/&gt; Embed** (Einbetten) aus, um den HTML-iframe-Code zum Einbetten der Widgets anzuzeigen.
1. Wählen Sie im Dialogfeld **Share and Embed** (Freigeben und Einbetten) das Widget **Player** aus, stellen Sie die Videogröße auf 560 x 315, und kopieren Sie dann den Einbettungscode in die Zwischenablage.
1. Fügen Sie in der Azure-Portal-Cloud Shell im Code-Editor für die Datei **analyze-video.html** den kopierten Code unter dem Kommentar **&lt;-- Player widget goes here -- &gt;** ein.
1. Wählen Sie im Video Indexer-Portal im Dialogfeld **Freigeben und einbetten** das Widget **Erkenntnisse** aus und kopieren Sie dann den Einbettungscode in die Zwischenablage. Schließen Sie dann das Dialogfeld **Freigeben und einbetten**, wechseln Sie zurück zum Azure-Portal und fügen Sie den kopierten Code unter dem Kommentar **&lt;-- Insights widget goes here -- &gt;** ein.
1. Speichern Sie nach dem Bearbeiten der Datei im Code-Editor Ihre Änderungen (*STRG+S*) und schließen Sie dann den Code-Editor (*STRG+Q*). Schließen Sie die Cloud Shell-Befehlszeile nicht.
1. Geben Sie in der Symbolleiste der Cloud Shell den folgenden Befehl ein (speziell für Cloud Shell), um die von Ihnen bearbeitete HTML-Datei herunterzuladen:

    ```
    download analyze-video.html
    ```

    Der Downloadbefehl erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen. Dieser sollte wie folgt aussehen:

    ![Video Indexer-Widgets auf einer Webseite](../media/video-indexer-widgets.png)

1. Experimentieren Sie mit den Widgets und verwenden Sie das Widget **Insights** (Erkenntnisse), um nach Erkenntnissen zu suchen und im Video zu ihnen zu springen.

