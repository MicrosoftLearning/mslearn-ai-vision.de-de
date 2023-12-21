---
lab:
  title: Klassifizieren von Bildern mit einem benutzerdefinierten Azure KI Vision-Modell
  module: Module 2 - Develop computer vision solutions with Azure AI Vision
---

# Klassifizieren von Bildern mit einem benutzerdefinierten Azure KI Vision-Modell

Mit Azure KI Vision können Sie benutzerdefinierte Modelle trainieren, um Objekte mit von Ihnen angegebenen Bezeichnungen zu klassifizieren und zu erkennen. In diesem Lab erstellen wir ein benutzerdefiniertes Bildklassifizierungsmodell, um Bilder von Früchten zu klassifizieren.

## Klonen des Repositorys für diesen Kurs

Wenn Sie das **Azure KI Vision**-Coderepository noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie hierfür die folgenden Schritte aus. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-vision` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus. Wenn die Meldung *Azure Functions-Projekt im Ordner erkannt* angezeigt wird, können Sie diese einfach schließen.

## Bereitstellen von Azure-Ressourcen

Wenn Sie noch nicht über eine solche Ressource in Ihrem Abonnement verfügen, müssen Sie eine **Azure KI Services**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Suchen Sie oben in der Suchleiste nach *Azure KI Services*, wählen Sie **Azure KI Services** und erstellen Sie eine Azure KI Services Multi-Service-Kontoressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie aus „USA, Osten“, „Europa, Westen“ und „USA, Westen 2“ aus.\**
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0.

    \*Benutzerdefinierte Azure KI Vision 4.0-Modelltags sind derzeit nur in diesen Regionen verfügbar.

3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
<!--4. When the resource has been deployed, go to it and view its **Keys and Endpoint** page. You will need the endpoint and one of the keys from this page in a future step. Save them off or leave this browser tab open.-->

Außerdem benötigen wir ein Speicherkonto, um die Schulungsbilder zu speichern.

1. Suchen Sie im Azure-Portal nach **Speicherkonten**, wählen Sie diese Option aus, und erstellen Sie ein neues Speicherkonto mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie die Ressourcengruppe aus, in der Sie Ihre Azure KI Service-Ressource erstellt haben.*
    - **Speicherkontoname**: Legen Sie hier „customclassifySUFFIX“ fest. 
        - *Hinweis: Ersetzen Sie das `SUFFIX`-Token durch Ihre Initialen oder einen anderen Wert, um sicherzustellen, dass der Ressourcenname global eindeutig ist.*
    - **Region**: *Wählen Sie die gleiche Region wie für Ihre Azure KI Service-Ressource aus.*
    - **Leistung**: Standard
    - **Redundanz**: Lokal redundanter Speicher (LRS)
1. Wechseln Sie während der Erstellung Ihres Speicherkontos zu Visual Studio Code, und erweitern Sie den Ordner **Labfiles/02-image-classification**.
1. Wählen Sie in diesem Ordner **replace.ps1** aus, und überprüfen Sie den Code. Sie sehen, dass der Name Ihres Speicherkontos durch den Platzhalter in einer JSON-Datei (der COCO-Datei) ersetzt wird, die wir in einem späteren Schritt verwenden. Ersetzen Sie den Platzhalter *in der ersten Zeile nur*für die Datei mit dem Namen Ihres Speicherkontos. Speichern Sie die Datei .
1. Klicken Sie mit der rechten Maustaste auf den Ordner **02-image-classification**, und öffnen Sie ein integriertes Terminal. Führen Sie den folgenden Befehl aus.

    ```powershell
    ./replace.ps1
    ```

1. Sie können die COCO-Datei überprüfen, um sicherzustellen, dass Ihr Speicherkontoname vorhanden ist. Wählen Sie **training-images/training_labels.json** aus, und sehen Sie sich die ersten Einträge an. Im Feld *absolute_url* sollte ein ähnlicher Eintrag wie *"https://myStorage.blob.core.windows.net/fruit/...* zu sehen sein. Wenn die erwartete Änderung nicht angezeigt wird, vergewissern Sie sich, dass Sie nur den ersten Platzhalter im PowerShell-Skript aktualisiert haben.
1. Schließen Sie sowohl die JSON- als auch die PowerShell-Datei, und wechseln Sie zurück in Ihr Browserfenster.
1. Ihr Speicherkonto sollte vollständig erstellt sein. Wechseln Sie zum Speicherkonto.
1. Aktivieren Sie den öffentlichen Zugriff für das Speicherkonto. Navigieren Sie im linken Bereich zu **Konfiguration** in der Gruppe **Einstellungen**, und aktivieren Sie *Anonymen Blob-Zugriff zulassen*. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Bereich die Option **Container** aus, erstellen Sie einen neuen Container mit dem Namen `fruit`, und stellen Sie **Anonyme Zugriffsebene** auf *Container (anonymer Lesezugriff für Container und Blobs)* ein.

    > **Hinweis**: Wenn **Anonyme Zugriffsebene** deaktiviert ist, aktualisieren Sie die Browserseite.

1. Navigieren Sie zu `fruit`, und laden Sie die Bilder (und die eine JSON-Datei) nach **Labfiles/02-image-classification/training-images** in diesem Container hoch.

## Erstellen eines Trainingsprojekts mit benutzerdefiniertem Modell

Als Nächstes erstellen Sie ein neues Trainingsprojekt für die benutzerdefinierte Bildklassifizierung in Vision Studio.

1. Navigieren Sie im Webbrowser zu `https://portal.vision.cognitive.azure.com/` und melden Sie sich mit dem Microsoft-Konto, unter dem Sie Ihre Azure AI-Ressource erstellt haben.
1. Wählen Sie die Kachel **Modelle mit Bildern anpassen** (auf der Registerkarte **Bildanalyse**, falls sie nicht in der Standardansicht angezeigt wird) und dann, wenn Sie dazu aufgefordert werden, die von Ihnen erstellte Azure KI-Ressource aus.
1. Wählen Sie oben in Ihrem Projekt die Option **Neues Dataset hinzufügen** aus. Konfigurieren Sie  mit den folgenden Einstellungen:
    - **Datasetname**: Legen Sie hier „training_images“ fest.
    - **Modelltyp**: Wählen Sie „Bildklassifizierung“ aus.
    - **Azure Blob Storage-Container auswählen**: Wählen Sie **Container auswählen** aus.
        - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
        - **Speicherkonto**: *Dies ist das Speicherkonto, das Sie erstellt haben.*
        - **BLOB-Container**: Legen Sie hier „fruit“ fest.
    - Aktivieren Sie das entsprechende Kontrollkästchen, damit Vision Studio den Blobspeicher lesen und darin schreiben kann.
1. Wählen Sie das Dataset **training_images** aus.

An diesem Punkt bei der Projekterstellung wählen Sie in der Regel die Option **Azure ML-Datenbeschriftungsprojekt erstellen** und die Beschriftung für Ihre Bilder aus, wodurch eine COCO-Datei generiert wird. Probieren Sie dies einmal aus, wenn Sie Zeit haben, aber für dieses Lab haben wir die Bilder bereits für Sie beschriftet und die entsprechende COCO-Datei bereitgestellt.

1. Wählen Sie **COCO-Datei hinzufügen** aus.
1. Wählen Sie im Dropdown die Option **COCO-Datei aus einem BLOB-Container importieren** aus.
1. Da Sie Ihren Container mit dem Namen `fruit` bereits verbunden haben, sucht Vision Studio nach einer entsprechenden COCO-Datei. Wählen Sie im Dropdown die Datei **training_labels.json** aus, und fügen Sie die COCO-Datei hinzu.
1. Navigieren Sie auf der linken Seite zu **Benutzerdefinierte Modelle**, und wählen Sie **Neues Modell trainieren** aus. Verwenden Sie folgende Einstellungen:
    - **Modellname**: Legen Sie hier „classifyfruit“ fest.
    - **Modelltyp**: Wählen Sie „Bildklassifizierung“ aus.
    - **Trainingsdataset wählen**: Wählen Sise „training_images“ aus.
    - Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Modell trainieren** aus.

Das Training kann einige Zeit in Anspruch nehmen – standardmäßig bis zu einer Stunde, aber für ein solch kleines Dataset geht es in der Regel deutlich schneller. Wählen Sie alle paar Minuten die Schaltfläche **Aktualisieren** aus, bis der Auftragsstatus *erfolgreich* lautet. Wählen Sie das Modell aus.

Hier können Sie die Leistung des Trainingsauftrags sehen. Überprüfen Sie die Präzision und Genauigkeit des trainierten Modells.

## Testen Ihres benutzerdefinierten Modells

Ihr Modell wurde trainiert und kann nun getestet werden.

1. Wählen Sie oben auf der Seite für Ihr benutzerdefiniertes Modell die Option **Jetzt ausprobieren** aus.
1. Wählen Sie im Dropdown das Modell **classifyfruit** aus, das verwendet werden soll, und navigieren Sie zum Ordner **02-image-classification\test-images**.
1. Wählen Sie die einzelnen Bilder aus, und sehen Sie sich die Ergebnisse an. Wählen Sie im Ergebnisfeld die Registerkarte **JSON** aus, um die vollständige JSON-Antwort zu untersuchen.

<!-- Option coding example to run-->
## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingsmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und suchen Sie in der oberen Suchleiste nach den Ressourcen, die Sie in diesem Lab erstellt haben.

2. Wählen Sie auf der Ressourcenseite die Option **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource. Sie können auch die gesamte Ressourcengruppe löschen, um alle Ressourcen gleichzeitig zu bereinigen.