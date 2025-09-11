---
lab:
  title: Klassifizieren von Bildern
  description: 'Verwenden Sie den Azure KI Custom Vision-Dienst, um ein Bildklassifizierungsmodell zu trainieren.'
---

# Klassifizieren von Bildern

Mit dem Dienst **Azure KI Custom Vision** können Sie Modelle für maschinelles Sehen erstellen, die mit Ihren eigenen Bildern trainiert werden. Sie können ihn verwenden, um *Bildklassifizierungs*- und *Objekterkennungs*modelle zu trainieren, die Sie dann veröffentlichen und aus Anwendungen nutzen können.

In dieser Übung verwenden Sie den Custom Vision-Dienst, um ein Bildklassifizierungsmodell zu trainieren, das drei Klassen von Obst identifizieren kann (Apfel, Banane und Orange).

Obwohl diese Übung auf dem Azure Custom Vision-Python-SDK basiert, können Sie KI-Chatanwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

* [Azure Custom Vision für JavaScript (Training)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training)
* [Azure Custom Vision für JavaScript (Vorhersage)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)
* [Azure Custom Vision für Microsoft .NET (Training)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Azure Custom Vision für Microsoft .NET (Vorhersage)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)
* [Azure Custom Vision für Java (Training)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar)
* [Azure Custom Vision für Java (Vorhersage)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)

Diese Übung dauert ca. **45** Minuten.

## Erstellen von Custom Vision-Ressourcen

Bevor Sie ein Modell trainieren können, benötigen Sie Azure-Ressourcen für *Training* und *Vorhersage*. Sie können **Custom Vision**-Ressourcen für jede dieser Aufgaben erstellen, oder Sie können eine einzelne Ressource erstellen und sie für beide verwenden. In dieser Übung erstellen Sie **Custom Vision**-Ressourcen für Trainings und Vorhersagen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`, und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Willkommensnachrichten oder Tipps, die angezeigt werden.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie in der Suchleiste nach `Custom Vision`, wählen Sie **Custom Vision** aus und erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Erstellungsoptionen**: Beide
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Ein gültiger Name für Ihre Custom Vision-Ressource*
    - **Tarif für Training**: F0
    - **Tarif für Vorhersage**: F0

1. Erstellen Sie die Ressource und warten Sie, bis die Bereitstellung abgeschlossen ist. Zeigen Sie dann die Bereitstellungsdetails an. Beachten Sie, dass zwei Custom Vision-Ressourcen bereitgestellt werden; eine für Trainings und eine andere für die Vorhersage.

    > **Hinweis:** Jede Ressource hat ihren eigenen *Endpunkt* und *Schlüssel*, die verwendet werden, um den Zugriff aus Ihrem Code zu verwalten. Um ein Bildklassifizierungsmodell zu trainieren, muss Ihr Code die *Trainings*ressource (mit ihrem Endpunkt und Schlüssel) verwenden, und um das trainierte Modell zum Vorhersagen von Bildklassen zu verwenden, muss Ihr Code die *Vorhersage*ressource (mit ihrem Endpunkt und Schlüssel) verwenden.

1. Wenn die Ressourcen bereitgestellt wurden, wechseln Sie zur Ressourcengruppe, um sie anzuzeigen. Es sollten zwei Custom Vision-Ressourcen angezeigt werden, eine mit dem Suffix ***-Prediction***.

## Erstellen eines Custom Vision-Projekts im Custom Vision-Portal

Um ein Bildklassifizierungsmodell zu trainieren, müssen Sie ein Custom Vision-Projekt auf Grundlage Ihrer Trainingsressource erstellen. Dazu verwenden Sie das Custom Vision-Portal.

1. Öffnen Sie eine neue Browserregisterkarte (schließen Sie die Registerkarte mit dem Azure-Portal nicht, Sie kehren später dorthin zurück).
1. Öffnen Sie auf der neuen Browserregisterkarte das [Custom Vision-Portal](https://customvision.ai) unter `https://customvision.ai`. Wenn Sie dazu aufgefordert werden, melden Sie sich mit Ihren Azure-Anmeldeinformationen an und stimmen Sie den Vertragsbedingungen zu.
1. Erstellen Sie im Custom Vision-Portal ein neues Projekt mit den folgenden Einstellungen:
    - **Name**: `Classify Fruit`
    - **Beschreibung:** `Image classification for fruit`
    - **Ressource:** *Ihre Custom Vision-Ressource*
    - **Projekttypen:** Klassifizierung
    - **Klassifizierungstypen**: Multiklassen-Klassifizierung (einzelnes Tag pro Bild)
    - **Domänen**: Lebensmittel

### Hochladen und Kennzeichnen von Bildern

1. Laden Sie auf einer neuen Browserregisterkarte die [Trainingsbilder](https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/main/Labfiles/image-classification/training-images.zip) herunter `https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/main/Labfiles/image-classification/training-images.zip` und extrahieren Sie den ZIP-Ordner, um dessen Inhalt anzuzeigen. Dieser Ordner enthält Unterordner mit Bildern von Äpfeln, Bananen und Organen.
1. Klicken Sie im Custom Vision-Portal in Ihrem Bildklassifizierungsprojekt auf **Bilder hinzufügen** und wählen Sie alle Dateien im Ordner **training-images/apple** aus, den Sie zuvor heruntergeladen und extrahiert haben. Laden Sie anschließend die Bilddateien mit dem Tag `apple` hoch:

    ![Screenshot des Hochladens des Apfels mit dem Tag „Apfel“.](../media/upload_apples.jpg)

1. Verwenden Sie das Symbol **Bilder hinzufügen** (**[+]**) in der Symbolleiste, um den vorherigen Schritt zu wiederholen und die Bilder im Ordner **banana** mit dem Tag `banana` und die Bilder im Ordner **orange** mit dem Tag `orange` hochzuladen.
1. Sehen Sie sich die Bilder an, die Sie in Ihrem Custom Vision-Projekt hochgeladen haben. Sie sollten 15 Bilder pro Klasse haben, wie hier gezeigt:

    ![Markierte Bilder von Obst: 15 Äpfel, 15 Bananen und 15 Orangen](../media/fruit.jpg)

### Trainieren eines Modells

1. Klicken Sie im Custom Vision-Projekt über den Bildern auf **Trainieren** (&#9881;<sub>&#9881;</sub>), um ein Klassifizierungsmodell mit den mit Tags versehenen Bildern zu trainieren. Wählen Sie die Option **Schnelltraining** aus, und warten Sie, bis die Trainingsiteration abgeschlossen wurde (dieser Vorgang kann etwa eine Minute dauern).
1. Warten Sie, bis die Modelliteration trainiert wurde, und überprüfen Sie die Leistungsmetriken *Genauigkeit*, *Abruf* und *AP*. Diese Metriken messen die Vorhersagegenauigkeit des Klassifizierungsmodells und sollten jeweils den Wert „Hoch“ haben.

    ![Screenshot der Modellmetriken.](../media/custom-vision-metrics.png)

> **Hinweis**: Die Leistungsmetriken basieren auf einem Wahrscheinlichkeitsschwellenwert von 50 % für jede Vorhersage (d. h., wenn das Modell eine Wahrscheinlichkeit von 50 % oder höher berechnet, dass ein Bild einer bestimmten Klasse entspricht, wird diese Klasse vorhergesagt). Sie können dies oben links auf der Seite anpassen.

### Testen des Modells

1. Klicken Sie über den Leistungsmetriken auf **Schnelltest**.
1. Geben Sie im Feld **Bild-URL** die URL `https://aka.ms/test-apple` ein, und klicken Sie auf die Schaltfläche *Schnelltestbild (&#10132;)* .
1. Sehen Sie sich die von Ihrem Modell zurückgegebenen Vorhersagen an. *Apfel* sollte den höchsten Wahrscheinlichkeitswert haben, wie hier gezeigt:

    ![Screenshot eines Bilds mit einer Klassenvorhersage von Apfel.](../media/test-apple.jpg)

1. Testen Sie die folgenden Bilder:
    - `https://aka.ms/test-banana`
    - `https://aka.ms/test-orange`

1. Schließen Sie das Fenster **Schelltest**.

### Anzeigen der Projekteinstellungen

Dem von Ihnen erstellten Projekt wurde ein eindeutiger Bezeichner zugewiesen, den Sie in jedem Code angeben müssen, der damit interagiert.

1. Klicken Sie auf das Symbol *Einstellungen* (&#9881;) oben rechts auf der Seite **Leistung**, um die Projekteinstellungen anzuzeigen.
1. Notieren Sie unter **Allgemein** (auf der linken Seite) die **Projekt-ID**, die dieses Projekt eindeutig identifiziert.
1. Beachten Sie, dass auf der rechten Seite unter **Ressourcen** der Schlüssel und der Endpunkt angezeigt werden. Dies sind die Details für die *Trainingsressource*. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

## Verwenden der *Trainings*-API

Das Custom Vision-Portal bietet eine praktische Benutzeroberfläche, mit der Sie Bilder hochladen und markieren sowie Modelle trainieren können. In einigen Szenarien kann es jedoch sinnvoll sein, das Modelltraining mithilfe der Custom Vision-Trainings-API zu automatisieren.

### Vorbereiten der Anwendungskonfiguration

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (schließen Sie die Registerkarte mit dem Custom Vision-Portal nicht, Sie kehren später dorthin zurück).
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

1. Nachdem das Repository geklont wurde, verwenden Sie den folgenden Befehl, um zu den Anwendungscodedateien zu navigieren:

    ```
   cd mslearn-ai-vision/Labfiles/image-classification/python/train-classifier
   ls -a -l
    ```

    Der Ordner enthält Anwendungskonfigurations- und Codedateien für Ihre App. Er enthält auch einen Unterordner **/more-training-images**, der einige Bilddateien zum Durchführen zusätzlicher Trainings Ihres Modells enthält.

1. Installieren Sie das Azure KI Custom Vision-SDK-Paket für Trainings und alle anderen erforderlichen Pakete, indem Sie die folgenden Befehle ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-cognitiveservices-vision-customvision
    ```

1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei für Ihre App zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie in der Codedatei die darin enthaltenen Konfigurationswerte so, dass der **Endpunkt** und **Authentifizierungsschlüssel** für Ihre Custom Vision-*Trainingsressource* und die **Projekt-ID** für das zuvor erstellte Custom Vision-Projekt verwendet werden.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S**, um Ihre Änderungen zu speichern und dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Schreiben von Code zum Durchführen von Modelltrainings

1. Geben Sie in der Befehlszeile der Cloud Shell den folgenden Befehl ein, um die Codedatei für die Clientanwendung zu öffnen:

    ```
   code train-classifier.py
    ```

1. Achten Sie auf folgende Details in der Codedatei:
    - Die Namespaces für das Azure KI Custom Vision-SDK werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt zum Erstellen eines authentifizierten Clients.
    - **CustomVisionTrainingClient**, der dann mit der Projekt-ID verwendet wird, um einen **Projektverweis** auf Ihr Projekt zu erstellen.
    - Die **Upload_Images**-Funktion ruft die im Custom Vision-Projekt definierten Tags ab und lädt dann Bilddateien aus entsprechend benannten Ordnern in das Projekt hoch, wobei die jeweils entsprechende Tag-ID zugewiesen wird.
    - Die **Train_Model**-Funktion erstellt eine neue Trainingsiteration für das Projekt und wartet auf den Abschluss des Trainings.

1. Schließen Sie den Code-Editor (*STRG+Q*) und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    ```
   python train-classifier.py
    ```

1. Warten Sie, bis das Programm beendet wird. Kehren Sie dann zu Ihrer Browserregisterkarte mit dem Custom Vision-Portal zurück und zeigen Sie die Seite **Trainingsbilder** für Ihr Projekt an (aktualisieren Sie ggf. den Browser).
1. Vergewissern Sie sich, dass dem Projekt einige neue markierte Bilder hinzugefügt wurden. Zeigen Sie dann die Seite **Leistung** an, und überprüfen Sie, ob eine neue Iteration erstellt wurde.

## Verwenden der Bildklassifizierung in einer Clientanwendung

Jetzt können Sie Ihr trainiertes Modell veröffentlichen und in einer Clientanwendung verwenden.

### Veröffentlichen des Bildklassifizierungsmodells

1. Klicken Sie im Custom Vision-Portal auf der Seite **Leistung** auf **&#128504; Veröffentlichen**, um das trainierte Modell mit den folgenden Einstellungen zu veröffentlichen:
    - **Modellname**: `fruit-classifier`
    - **Vorhersageressource:** *Dies ist die zuvor erstellte **Vorhersageressource**, die mit „-Prediction“ endet (<u>nicht</u> die Trainingsressource).*
1. Klicken Sie links oben auf der Seite **Projekteinstellungen** auf das Symbol für den *Projektkatalog* (&#128065;), um zur Startseite des Custom Vision-Portals zu gelangen, auf der Ihr Projekt jetzt aufgelistet wird.
1. Klicken Sie auf der Startseite des Custom Vision-Portals oben rechts auf das Symbol *Einstellungen* (&#9881;), um die Einstellungen für Ihren Custom Vision-Dienst anzuzeigen. Suchen Sie dann unter **Ressourcen** Ihre auf „-Prediction“ endende *Vorhersageressource* (<u>nicht</u> die Trainingsressource), um ihre Werte für **Schlüssel** und **Endpunkt** zu ermitteln. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

### Verwenden der Bildklassifizierung aus einer Clientanwendung

1. Kehren Sie zur Browserregisterkarte zurück, in der sich das Azure-Portal und der Cloud Shell-Bereich befinden.
1. Führen Sie in Cloud Shell die folgenden Befehle aus, um zum Ordner für Ihre Clientanwendung zu wechseln und die darin enthaltenen Dateien anzuzeigen:

    ```
   cd ../test-classifier
   ls -a -l
    ```

    Der Ordner enthält Anwendungskonfigurations- und Codedateien für Ihre App. Außerdem enthält er den Unterordner **/test-images**, der einige Bilddateien zum Testen Ihres Modells enthält.

1. Installieren Sie das Azure KI Custom Vision-SDK-Paket für Vorhersagen und alle anderen erforderlichen Pakete, indem Sie die folgenden Befehle ausführen:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-cognitiveservices-vision-customvision
    ```

1. Geben Sie den folgenden Befehl ein, um die Konfigurationsdatei für Ihre App zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie die Konfigurationswerte so, dass sie den **Endpunkt** und den **Schlüssel** für Ihre Custom Vision-Ressource für die *<u>Vorhersage</u>*, die **Projekt-ID** für das Klassifizierungsprojekt und den Namen Ihres veröffentlichten Modells widerspiegeln (dies sollte *fruit-classifier* sein). Speichern Sie Ihre Änderungen (*STRG+S*), und schließen Sie den Code-Editor (*STRG+Q*).
1. Geben Sie in der Befehlszeile der Cloud Shell den folgenden Befehl ein, um die Codedatei für die Clientanwendung zu öffnen:

    ```
   code test-classifier.py
    ```

1. Überprüfen Sie den Code und notieren Sie sich die folgenden Details:
    - Die Namespaces für das Azure KI Custom Vision-SDK werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt zum Erstellen eines authentifizierten **CustomVisionPredictionClient**.
    - Das Vorhersageclientobjekt wird verwendet, um eine Klasse für jedes Bild im Ordner **test-images** vorherzusagen, wobei die Projekt-ID und der Modellname für jede Anforderung angegeben werden. Jede Vorhersage umfasst eine Wahrscheinlichkeit für jede mögliche Klasse, und es werden nur vorhergesagte Tags mit einer Wahrscheinlichkeit von mehr als 50 % angezeigt.

1. Schließen Sie den Code-Editor und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    ```
   python test-classifier.py
    ```

    Das Programm übermittelt jedes der folgenden Bilder an das Modell zur Klassifizierung:

    ![Bild eines Apfels](../media/IMG_TEST_1.jpg)

    **IMG_TEST_1.jpg**

    <br/><br/>

    ![Bild einer Banane](../media/IMG_TEST_2.jpg)

    **IMG_TEST_2.jpg**

    <br/><br/>

    ![Bild einer Orange](../media/IMG_TEST_3.jpg)

    **IMG_TEST_3.jpg**

1. Zeigen Sie die Bezeichnung (das Tag) und Wahrscheinlichkeitsbewertungen für jede Vorhersage an.

## Bereinigen von Ressourcen

Wenn Sie mit der Erkundung von Azure KI Custom Vision fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und suchen Sie in der oberen Suchleiste nach den Ressourcen, die Sie in diesem Lab erstellt haben.

1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource. Alternativ können Sie die gesamte Ressourcengruppe löschen, um alle Ressourcen gleichzeitig zu bereinigen.
