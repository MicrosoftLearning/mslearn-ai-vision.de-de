---
lab:
  title: Klassifizieren von Bildern mit einem benutzerdefinierten Azure KI Vision-Modell
  module: Module 2 - Develop computer vision solutions with Azure AI Vision
---

# Klassifizieren von Bildern mit einem benutzerdefinierten Azure KI Vision-Modell

Mit Azure KI Vision können Sie benutzerdefinierte Modelle trainieren, um Objekte mit von Ihnen angegebenen Kennzeichnungen zu klassifizieren und zu erkennen. In diesem Lab erstellen wir ein benutzerdefiniertes Bildklassifizierungsmodell, um Bilder von Obst zu klassifizieren.

## Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **Azre KI Vision** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-vision` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus. Wenn die Meldung *Azure-Funktionsprojekt im Ordner erkannt* angezeigt wird, können Sie diese Nachricht sicher schließen.

## Bereitstellen von Azure-Ressourcen

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine **Azure KI Services**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Suchen Sie in der oberen Suchleiste nach *Azure KI Services*, wählen Sie **Azure KI Services** aus und erstellen Sie eine Azure KI Services Multi-Service-Kontoressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie aus „USA, Osten“, „Frankreich, Mitte“, „Südkorea, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Europa, Westen“, „USA, Westen“ oder „Asien, Osten“\**
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0.

    \*Azure KI Vision 4.0-Features sind derzeit nur in diesen Regionen verfügbar.

3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. In einem zukünftigen Schritt werden Sie den Endpunkt und einen der Schlüssel von dieser Seite benötigen. Speichern Sie sie aus, oder lassen Sie diese Browserregisterkarte geöffnet.

Außerdem benötigen wir ein Speicherkonto, um die Trainingsbilder zu speichern.

1. Zurück im Azure-Portal suchen Sie **Speicherkonten** und wählen Sie es aus, und erstellen Sie ein neues Speicherkonto mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie die gleiche Ressourcengruppe aus, in der Sie Ihre Azure KI Service-Ressource erstellt haben.*
    - **Speicherkontoname**: customclassifySUFFIX 
        - *Hinweis: Ersetzen Sie das `SUFFIX`-Token durch Ihre Initialen oder einen anderen Wert, um sicherzustellen, dass der Ressourcenname global eindeutig ist.*
    - **Region**: *Wählen Sie dieselbe Region aus, die Sie für Ihre Azure KI Service-Ressource verwendet haben.*
    - **Leistung**: Standard
    - **Redundanz**: Lokal redundanter Speicher (LRS)
1. Wählen Sie nach dem Erstellen des Speicherkontos „Zu Ressource wechseln“ aus.
1. Aktivieren Sie den öffentlichen Zugriff für das Speicherkonto. Navigieren Sie zur **Konfiguration** in der Gruppe „Einstellungen“, und aktivieren Sie *Anonymen Blob-Zugriff zulassen*. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Fensterbereich **Container** aus, und erstellen Sie einen neuen Container mit dem Namen `fruit`, und legen Sie die **Stufe für anonymen Zugriff** auf *Container (anonymer Lesezugriff für Container und Blobs)* fest.
1. Navigieren Sie zu `fruit`, und laden Sie die Bilder in **02-image-classification\training-images** in diesen Container hoch.


## Erstellen eines Trainingsprojekts für ein benutzerdefiniertes Modell

Als Nächstes erstellen Sie ein neues Trainingsprojekt für die benutzerdefinierte Bildklassifizierung in Vision Studio.

1. Navigieren Sie im Webbrowser zu `https://portal.vision.cognitive.azure.com/` und melden Sie sich mit dem Microsoft-Konto an, in dem Sie Ihre Azure KI-Ressource erstellt haben.
1. Wählen Sie die Kachel **Modelle mit Bildern anpassen** aus (zu finden auf der Registerkarte **Bildanalyse**, wenn sie nicht in der Standardansicht angezeigt wird), und wählen Sie, wenn Sie dazu aufgefordert werden, die Azure KI-Ressource aus, die Sie erstellt haben.
1. Wählen Sie in Ihrem Projekt oben **Neues Dataset hinzufügen** aus. Konfigurieren Sie  mit den folgenden Einstellungen:
    - **Datasetname**: training_images
    - **Modelltyp**: Bildklassifizierung
    - **Wählen Sie den Azure Blob Storage Container**: Wählen Sie **Container auswählen** aus
        - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
        - **Speicherkonto**: *Das Speicherkonto, das Sie erstellt haben.*
        - **BLOB-Container**: Obst
    - Aktivieren Sie das Kontrollkästchen „Vision Studio das Lesen und Schreiben im Blobspeicher erlauben“.
1. Wählen Sie das Dataset **training_images** aus.

An diesem Punkt der Projekterstellung würden Sie in der Regel **Azure ML Datenbeschriftungsprojekt erstellen** auswählen und Ihre Bilder beschriften. Wenn Sie Zeit haben, können Sie das gerne versuchen, aber für die Zwecke dieses Labs haben wir die Bilder bereits für Sie beschriftet und die resultierende COCO-Datei bereitgestellt.

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