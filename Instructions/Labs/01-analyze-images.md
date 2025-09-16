---
lab:
  title: Analysieren von Bildern
  description: 'Verwenden Sie die Bildanalyse von Azure KI Vision, um Bilder zu analysieren, Beschriftungen und Tags vorzuschlagen und Objekte und Personen zu erkennen.'
---

# Analysieren von Bildern

Azure KI Vision ist eine Funktion der künstlichen Intelligenz, die es Softwaresystemen ermöglicht, visuelle Eingaben durch die Analyse von Bildern zu interpretieren. In Microsoft Azure bietet der Azure KI-Dienst **Vision** vorgefertigte Modelle für gängige Aufgaben für maschinelles Sehen, darunter die Analyse von Bildern, um Beschriftungen und Tags vorzuschlagen, die Erkennung von gängigen Objekten und von Menschen. Sie können den Azure KI Vision-Dienst auch verwenden, um den Hintergrund zu entfernen oder eine Vordergrundmattierung von Bildern zu erstellen.

> **Hinweis:** Diese Übung basiert auf Vorabversionen von SDK-Software, die sich möglicherweise Änderungen unterliegen. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

Obwohl diese Übung auf dem Azure Vision-Python-SDK basiert, können Sie Vision-Anwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

* [Azure KI Vision-Analyse für JavaScript](https://www.npmjs.com/package/@azure-rest/ai-vision-image-analysis)
* [Azure KI Vision-Analyse für Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Vision.ImageAnalysis)
* [Azure KI Vision-Analyse für Java](https://mvnrepository.com/artifact/com.azure/azure-ai-vision-imageanalysis)

Diese Übung dauert ca. **30** Minuten.

## Bereitstellen einer Azure KI Vision-Ressource

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine Azure KI Vision-Ressource bereitstellen.

> **Hinweis:** In dieser Übung verwenden Sie eine eigenständige Ressource **Maschinelles Sehen**. Sie können Azure KI Vision-Dienste auch in einer Ressource mit mehreren Diensten von *Azure KI Services* verwenden, entweder direkt oder in einem *Azure AI Foundry*-Projekt.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`, und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Willkommensnachrichten oder Tipps, die angezeigt werden.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie in der Suchleiste nach `Computer Vision`, wählen Sie **Maschinelles Sehen** aus, und erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region:** *Wählen Sie aus **USA, Osten**, **USA, Westen**, **Frankreich, Mitte**, **Südkorea, Mitte**, **Europa, Norden**, **Asien, Südosten**, **Europa, Westen** oder **Asien, Osten**\**
    - **Name**: *Ein gültiger Name für Ihre Ressource „Maschinelles Sehen“*
    - **Tarif**: Free F0

    \*Azure AI Vision 4.0 mit vollem Funktionsumfang ist derzeit nur in diesen Regionen verfügbar.

1. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
1. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu der Ressource und zeigen Sie im Navigationsbereich unter dem Knoten **Ressourcenverwaltung** die Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## Entwickeln einer Bildanalyse-App mit dem Azure KI Vision-SDK

In dieser Übung stellen Sie eine teilweise implementierte Clientanwendung fertig, die das Azure KI Vision SDK nutzt, um Bilder zu analysieren.

### Vorbereiten der Anwendungskonfiguration

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen. Wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement aus.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

    > **Hinweis:** Wenn Sie vom Portal aufgefordert werden, einen Speicher auszuwählen, um Ihre Dateien beizubehalten, wählen Sie **Kein Speicherkonto erforderlich** aus. Wählen Sie das verwendete Abonnement aus und drücken Sie auf **Anwenden**.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Ändern Sie die Größe des Cloud Shell-Bereichs, damit die Seite **Schlüssel und Endpunkt** für Ihre Ressource „Maschinelles Sehen“ weiterhin angezeigt wird.

    > **Tipp** Sie können die Größe des Bereichs ändern, indem Sie den oberen Rahmen ziehen. Sie können außerdem die Schaltflächen zum Minimieren und Maximieren verwenden, um zwischen Cloud Shell und der Hauptschnittstelle des Portals zu wechseln.

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
    ```

    > **TIPP**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Nachdem das Repository geklont wurde, verwenden Sie den folgenden Befehl, um zu dem Ordner mit den Anwendungscodedateien zu navigieren und ihn anzuzeigen:   

    ```
   cd mslearn-ai-vision/Labfiles/analyze-images/python/image-analysis
   ls -a -l
    ```

    Der Ordner enthält Anwendungskonfigurations- und Codedateien für Ihre App. Außerdem enthält er den Unterordner **/images**, der einige Bilddateien enthält, die ihre App analysieren kann.
    
1. Installieren Sie das Azure KI Vision-SDK-Paket und andere erforderliche Pakete, indem Sie die folgenden Befehle ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-vision-imageanalysis==1.0.0
    ```

1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei für Ihre App zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie in der Codedatei die Konfigurationswerte, die sie enthält, um den **Endpunkt** und einen **Authentifizierungsschlüssel** für Ihre Ressource „Maschinelles Sehen“ widerzuspiegeln (von der Seite **Schlüssel und Endpunkt** im Azure-Portal kopiert).
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Hinzufügen von Code zum Vorschlagen einer Beschriftung

1. Geben Sie in der Befehlszeile der Cloud Shell den folgenden Befehl ein, um die Codedatei für die Clientanwendung zu öffnen:

    ```
   code image-analysis.py
    ```

    > **Tipp**: Möglicherweise möchten Sie den Bereich der Cloud Shell vergrößern und die geteilte Leiste zwischen der Befehlszeilenkonsole und dem Code-Editor verschieben, damit Sie den Code einfacher anzeigen können.

1. Suchen Sie in der Codedatei den Kommentar **Import namespaces** und fügen Sie den folgenden Code hinzu, um die Namespaces zu importieren, die Sie zum Verwenden des Azure KI Vision-SDK benötigen:

    ```python
   # import namespaces
   from azure.ai.vision.imageanalysis import ImageAnalysisClient
   from azure.ai.vision.imageanalysis.models import VisualFeatures
   from azure.core.credentials import AzureKeyCredential
    ```

1. Beachten Sie, dass in der **Main**-Funktion bereits Code zum Laden der Konfigurationseinstellungen und zum Ermitteln der zu analysierenden Bilddatei bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Azure AI Vision client** und fügen Sie den folgenden Code hinzu, um ein Azure KI Vision-Clientobjekt zu erstellen und zu authentifizieren (achten Sie auf die richtigen Einzugsebene):

    ```python
   # Authenticate Azure AI Vision client
   cv_client = ImageAnalysisClient(
        endpoint=ai_endpoint,
        credential=AzureKeyCredential(ai_key))
    ```

1. Suchen Sie in der **Main**-Funktion unter dem soeben hinzugefügten Code den Kommentar **Analyze image** und fügen Sie den folgenden Code hinzu:

    ```python
   # Analyze image
   with open(image_file, "rb") as f:
        image_data = f.read()
   print(f'\nAnalyzing {image_file}\n')

   result = cv_client.analyze(
        image_data=image_data,
        visual_features=[
            VisualFeatures.CAPTION,
            VisualFeatures.DENSE_CAPTIONS,
            VisualFeatures.TAGS,
            VisualFeatures.OBJECTS,
            VisualFeatures.PEOPLE],
   )
    ```

1. Suchen Sie den Kommentar **Get image captions** und fügen Sie den folgenden Code hinzu, um Bildbeschriftungen und dichte Beschriftungen anzuzeigen:

    ```python
   # Get image captions
   if result.caption is not None:
        print("\nCaption:")
        print(" Caption: '{}' (confidence: {:.2f}%)".format(result.caption.text, result.caption.confidence * 100))
    
   if result.dense_captions is not None:
        print("\nDense Captions:")
        for caption in result.dense_captions.list:
            print(" Caption: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*) und ändern Sie die Größe der Bereiche, damit die Befehlszeilenkonsole deutlich angezeigt werden kann, während der Code-Editor geöffnet ist. Geben Sie dann den folgenden Befehl ein, um das Programm mit den Argument **images/street.jpg**auszuführen:

    ```
   python image-analysis.py images/street.jpg
    ```

1. Schauen Sie sich die Ausgabe an, die eine vorgeschlagene Beschriftung für das Bild **street.jpg** enthalten sollte, das so aussieht:

    ![Ein Bild einer belebten Straße.](../media/street.jpg)

1. Führen Sie das Programm erneut aus, diesmal mit dem Argument **images/building.jpg**, um die Bildunterschrift anzuzeigen, die für das Bild **building.jpg** generiert wird, das so aussieht:

    ![Ein Bild eines Gebäudes.](../media/building.jpg)

1. Wiederholen Sie den vorherigen Schritt, um eine Beschriftung für die Datei **images/person.jpg** zu erstellen, die so aussieht:

    ![Ein Bild einer Person.](../media/person.jpg)

### Hinzufügen von Code zum Generieren vorgeschlagener Tags

Es kann manchmal hilfreich sein, relevante *Tags* zu identifizieren, die Hinweise auf den Inhalt eines Bilds geben.

1. Suchen Sie im Code-Editor in der **AnalyzeImage**-Funktion den Kommentar **Get image tags** und fügen Sie den folgenden Code hinzu:

    ```python
   # Get image tags
   if result.tags is not None:
        print("\nTags:")
        for tag in result.tags.list:
            print(" Tag: '{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), und führen Sie das Programm mit dem Argument **images/street.jpg**aus. Dabei wird neben der Bildbeschriftung eine Liste der vorgeschlagenen Tags angezeigt.
1. Führen Sie das Programm für die Dateien **images/building.jpg** und **images/person.jpg** erneut aus.

### Hinzufügen von Code zum Erkennen und Suchen von Objekten

1. Suchen Sie im Code-Editor in der **AnalyzeImage**-Funktion den Kommentar **"Get objects in the image** und fügen Sie den folgenden Code hinzu, um die im Bild erkannten Objekte aufzulisten. Rufen Sie die bereitgestellte Funktion auf, um ein Bild mit den erkannten Objekten zu kommentieren:

    ```python
   # Get objects in the image
   if result.objects is not None:
        print("\nObjects in image:")
        for detected_object in result.objects.list:
            # Print object tag and confidence
            print(" {} (confidence: {:.2f}%)".format(detected_object.tags[0].name, detected_object.tags[0].confidence * 100))
        # Annotate objects in the image
        show_objects(image_file, result.objects.list)
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), und führen Sie das Programm mit dem Argument **images/street.jpg**aus. Dabei wird neben der Bildbeschriftung und den vorgeschlagenen Tags, eine Datei mit dem Namen **objects.jpg** generiert.
1. Verwenden Sie den Befehl **download** (speziell für Azure Cloud Shell), um die Datei **objects.jpg** herunterzuladen:

    ```
   download objects.jpg
    ```

    Der Downloadbefehl erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen. Das Bild sollte ähnlich wie dies aussehen:

    ![Ein Bild mit Objektbegrenzungsfeldern.](../media/objects.jpg)

1. Führen Sie das Programm für die Dateien **images/building.jpg** und **images/person.jpg** erneut aus und laden Sie die generierte Datei „objects.jpg“ nach jeder Ausführung herunter.

### Hinzufügen von Code zum Erkennen und Suchen von Personen

1. Suchen Sie im Code-Editor in der **AnalyzeImage**-Funktion den Kommentar **Get people in the image** und fügen Sie den folgenden Code hinzu, um alle erkannten Personen mit einem Konfidenzniveau von 20 % oder mehr aufzulisten. Rufen Sie eine bereitgestellte Funktion auf, um sie in einem Bild zu kommentieren:

    ```Python
   # Get people in the image
   if result.people is not None:
        print("\nPeople in image:")

        for detected_person in result.people.list:
            if detected_person.confidence > 0.2:
                # Print location and confidence of each person detected
                print(" {} (confidence: {:.2f}%)".format(detected_person.bounding_box, detected_person.confidence * 100))
        # Annotate people in the image
        show_people(image_file, result.people.list)
    ```

1. Speichern Sie Ihre Änderungen (*STRG+S*), und führen Sie das Programm mit dem Argument **images/street.jpg**aus. Dabei wird neben der Bildbeschriftung, den vorgeschlagenen Tags und der Datei „objects.jpg“, eine Liste der Personenstandorte und eine Datei mit dem Namen **people.jpg** generiert.

1. Verwenden Sie den Befehl **download** (speziell für Azure Cloud Shell), um die Datei **objects.jpg** herunterzuladen:

    ```
   download people.jpg
    ```

    Der Downloadbefehl erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen. Das Bild sollte ähnlich wie dies aussehen:

    ![Ein Bild mit Begrenzungsfeldern für erkannte Personen.](../media/people.jpg)

1. Führen Sie das Programm für die Dateien **images/building.jpg** und **images/person.jpg** erneut aus und laden Sie die generierte Datei „people.jpg“ nach jeder Ausführung herunter.

   > **Tipp:** Wenn Begrenzungsrahmen aus dem Modell zurückgegeben werden, die nicht sinnvoll sind, überprüfen Sie die JSON-Konfidenzbewertung und versuchen Sie, die Filterung der Konfidenzbewertung in Ihrer App zu erhöhen.

## Bereinigen von Ressourcen

Wenn Sie mit der Erkundung von Azure KI Vision fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

1. Suchen Sie in der oberen Suchleiste nach *Maschinelles Sehen* und wählen Sie die Ressource „Maschinelles Sehen“ aus, die Sie in dieser Übung erstellt haben.

1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

