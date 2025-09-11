---
lab:
  title: "Klassifizieren von Bildern mit einem benutzerdefinierten Azure\_KI\_Vision-Modell"
  module: Module 2 - Develop computer vision solutions with Azure AI Vision
---

# Klassifizieren von Bildern mit einem benutzerdefinierten Azure KI Vision-Modell

Mit Azure KI Vision können Sie benutzerdefinierte Modelle trainieren, um Objekte mit von Ihnen angegebenen Kennzeichnungen zu klassifizieren und zu erkennen. In diesem Lab erstellen wir ein benutzerdefiniertes Bildklassifizierungsmodell, um Bilder von Obst zu klassifizieren.

## Bereitstellen einer Ressource für maschinelles Sehen

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine Ressource **Maschinelles Sehen** bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie in der Suchleiste nach *Maschinelles Sehen*, wählen Sie **Maschinelles Sehen** aus und erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie aus „USA, Osten“, „USA, Westen“, „Frankreich, Mitte“, „Südkorea, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Europa, Westen“, oder „Asien, Osten“\**
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Free F0

    \*Azure AI Vision 4.0 mit vollem Funktionsumfang ist derzeit nur in diesen Regionen verfügbar.

1. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
<!--4. When the resource has been deployed, go to it and view its **Keys and Endpoint** page. You will need the endpoint and one of the keys from this page in a future step. Save them off or leave this browser tab open.-->

Außerdem benötigen wir ein Speicherkonto, um die Schulungsbilder zu speichern.

1. Suchen Sie im Azure-Portal nach **Speicherkonten**, wählen Sie diese Option aus, und erstellen Sie ein neues Speicherkonto mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie dieselbe Ressourcengruppe aus, in der Sie Ihre Custom Vision-Ressource erstellt haben*
    - **Speicherkontoname**: customclassifySUFFIX 
        - *Hinweis: Ersetzen Sie das Token `SUFFIX` durch Ihre Initialen oder einen anderen Wert, um sicherzustellen, dass der Ressourcenname global eindeutig ist.*
    - **Region**: *Wählen Sie dieselbe Region aus, die Sie für Ihre Azure KI Service-Ressource verwendet haben.*
    - **Primärer Dienst**: Azure Blob Storage oder Azure Data Lake Storage Gen 2
    - **Primäre Workload**: Andere
    - **Leistung**: Standard
    - **Redundanz**: Lokal redundanter Speicher (LRS)

1. Nachdem die Ressource bereitgestellt wurde, wählen Sie **Zu Ressource wechseln** aus.
1. Aktivieren Sie den öffentlichen Zugriff für das Speicherkonto. Navigieren Sie im linken Bereich zu **Konfiguration** in der Gruppe **Einstellungen**, und aktivieren Sie *Anonymen Blob-Zugriff zulassen*. Wählen Sie**Speichern**
1. Wählen Sie im linken Bereich unter **Datenspeicher** die Option **Container** und erstellen Sie einen neuen Container mit dem Namen `fruit`. Legen Sie **Anonyme Zugriffsebene** auf *Container (anonymer Lesezugriff für Container und Blobs)* fest.

    > **Hinweis**: Wenn **Anonyme Zugriffsebene** deaktiviert ist, aktualisieren Sie die Browserseite.
   
## Klonen des Repositorys für diesen Kurs

Die Bilddateien für das Training Ihres Modells wurden in einem GitHub-Repository bereitgestellt. Sie klonen das Repository und laden die Bilder mithilfe von Cloud Shell aus dem Azure-Portal in Ihr Speicherkonto hoch. 

> **Tipp**: Wenn Sie das Repository **mslearn-ai-vision** erst kürzlich geklont haben, können Sie diese Klonaufgabe überspringen. Führen Sie andernfalls die folgenden Schritte aus, um das Repository in Ihrer Entwicklungsumgebung zu klonen.

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/microsoftlearning/mslearn-ai-vision mslearn-ai-vision
    ```

1. Nachdem das Repository geklont wurde, navigieren Sie zu dem Ordner, der die Übungsdateien enthält:  

    ```
   cd mslearn-ai-vision/Labfiles/02-image-classification
    ```

1. Führen Sie den Befehl `code replace.ps1` aus und überprüfen Sie den Code. Sie sehen, dass der Name Ihres Speicherkontos durch den Platzhalter in einer JSON-Datei (der COCO-Datei) ersetzt wird, die wir in einem späteren Schritt verwenden.
1. Ersetzen Sie den Platzhalter *in der ersten Zeile nur*für die Datei mit dem Namen Ihres Speicherkontos.
1. Nachdem Sie den Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S**, oder **klicken Sie mit der rechten Maustaste und klicken dann auf „Speichern“**, um Ihre Änderungen zu speichern. Verwenden Sie dann den Befehl **STRG+Q**, oder **klicken Sie mit der rechten Maustaste und klicken dann auf „Beenden“**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.
1. Führen Sie das Skript mit dem folgenden Befehl aus:

    ```powershell
    ./replace.ps1
    ```

1. Sie können die COCO-Datei überprüfen, um sicherzustellen, dass Ihr Speicherkontoname vorhanden ist. Führen Sie die ersten Einträge `code training-images/training_labels.json` aus und zeigen Sie sie an. Im Feld *absolute_url* sollte ein ähnlicher Eintrag wie *"https://myStorage.blob.core.windows.net/fruit/...* zu sehen sein. Wenn die erwartete Änderung nicht angezeigt wird, vergewissern Sie sich, dass Sie nur den ersten Platzhalter im PowerShell-Skript aktualisiert haben.
1. Schließen Sie den Code-Editor.
1. Führen Sie den folgenden Befehl aus, um `<your-storage-account>` durch den Namen Ihres Speicherkontos zu ersetzen und den Inhalt des Ordners **training-images** in den zuvor erstellten Container `fruit` hochzuladen.

    ```powershell
    az storage blob upload-batch --account-name <your-storage-account> -d fruit -s ./training-images/
    ```

1. Öffnen Sie den Container `fruit` und stellen Sie sicher, dass die Dateien ordnungsgemäß hochgeladen wurden.

## Erstellen eines Trainingsprojekts mit benutzerdefiniertem Modell

Als Nächstes erstellen Sie ein neues Trainingsprojekt für die benutzerdefinierte Bildklassifizierung in Vision Studio.

1. Navigieren Sie im Webbrowser zu `https://portal.vision.cognitive.azure.com/` und melden Sie sich mit dem Microsoft-Konto an, in dem Sie Ihre Azure KI-Ressource erstellt haben.
1. Wählen Sie die Kachel **Modelle mit Bildern anpassen** aus (zu finden auf der Registerkarte **Bildanalyse**, wenn sie nicht in der Standardansicht angezeigt wird).
1. Wählen Sie das von Ihnen erstellte Azure KI Services-Konto aus.
1. Wählen Sie in Ihrem Projekt oben **Neues Dataset hinzufügen** aus. Konfigurieren Sie  mit den folgenden Einstellungen:
    - **Datasetname**: training_images
    - **Modelltyp**: Bildklassifizierung
    - **Wählen Sie den Azure Blob Storage Container**: Wählen Sie **Container auswählen** aus
        - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
        - **Speicherkonto**: *Das Speicherkonto, das Sie erstellt haben.*
        - **BLOB-Container**: Obst
    - Aktivieren Sie das Kontrollkästchen „Vision Studio das Lesen und Schreiben im Blobspeicher erlauben“.
1. Wählen Sie das Dataset **training_images** aus.

An diesem Punkt bei der Projekterstellung wählen Sie in der Regel die Option **Azure ML-Datenbeschriftungsprojekt erstellen** und die Beschriftung für Ihre Bilder aus, wodurch eine COCO-Datei generiert wird. Probieren Sie dies einmal aus, wenn Sie Zeit haben, aber für dieses Lab haben wir die Bilder bereits für Sie beschriftet und die entsprechende COCO-Datei bereitgestellt.

1. Wählen Sie **COCO-Datei hinzufügen** aus.
1. Wählen Sie in der Dropdownliste **COCO-Datei aus einem BLOB-Container importieren** aus.
1. Da Sie ihren Container namens `fruit` bereits verbunden haben, sucht Vision Studio darin nach einer COCO-Datei. Wählen Sie **training_labels.json** aus der Dropdownliste aus, und fügen Sie die COCO-Datei hinzu.
1. Navigieren Sie auf der linken Seite zu **Benutzerdefinierte Modelle** und wählen Sie **Neues Modell trainieren** aus. Verwenden Sie folgende Einstellungen:
    - **Name des Modells**: classifyfruit
    - **Modelltyp**: Bildklassifizierung
    - **Trainingsdataset auswählen**: training_images
    - Belassen Sie die übrigen bei den Standardeinstellungen, und wählen Sie **Modell trainieren** aus.

Das Training kann einige Zeit in Anspruch nehmen – das Standardbudget beträgt bis zu einer Stunde, aber für dieses kleine Dataset geht es in der Regel viel schneller. Klicken Sie alle paar Minuten auf die Schaltfläche **Aktualisieren**, bis der Status des Auftrags *Erfolgreich* ist. Wählen Sie das Modell aus.

Hier können Sie die Leistung des Trainingsauftrags anzeigen. Überprüfen Sie die Genauigkeit und Korrektheit des trainierten Modells.

## Testen Ihres benutzerdefinierten Modells

Ihr Modell wurde trainiert und kann getestet werden.

1. Wählen Sie oben auf der Seite für Ihr benutzerdefiniertes Modell die Option **Ausprobieren** aus.
1. Wählen Sie das Modell **classifyfruit** aus der Dropdownliste aus und geben Sie an, welches Modell Sie verwenden wollen, und navigieren Sie zum Ordner **02-image-classification\test-images**.
1. Wählen Sie jedes Bild aus, und zeigen Sie die Ergebnisse an. Wählen Sie die Registerkarte **JSON** im Ergebnisfeld aus, um die vollständige JSON-Antwort zu untersuchen.

<!-- Option coding example to run-->
## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und suchen Sie in der oberen Suchleiste nach den Ressourcen, die Sie in diesem Lab erstellt haben.

2. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource. Alternativ können Sie die gesamte Ressourcengruppe löschen, um alle Ressourcen gleichzeitig zu bereinigen.
