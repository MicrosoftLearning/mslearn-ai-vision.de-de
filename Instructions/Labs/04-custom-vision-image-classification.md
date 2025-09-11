---
lab:
  title: Klassifizieren von Bildern mit Azure KI Custom Vision
---

# Klassifizieren von Bildern mit Azure KI Custom Vision

Mit dem Dienst **Azure KI Custom Vision** können Sie Modelle für maschinelles Sehen erstellen, die mit Ihren eigenen Bildern trainiert werden. Sie können ihn verwenden, um *Bildklassifizierungs*- und *Objekterkennungs*modelle zu trainieren, die Sie dann veröffentlichen und aus Anwendungen nutzen können.

In dieser Übung verwenden Sie den Custom Vision-Dienst, um ein Bildklassifizierungsmodell zu trainieren, das drei Klassen von Obst identifizieren kann (Apfel, Banane und Orange).

## Erstellen von Custom Vision-Ressourcen

Bevor Sie ein Modell trainieren können, benötigen Sie Azure-Ressourcen für *Training* und *Vorhersage*. Sie können **Custom Vision**-Ressourcen für jede dieser Aufgaben erstellen, oder Sie können eine einzelne **Azure KI Services**-Ressource erstellen und diese für eine der beiden Aufgaben (oder beide) verwenden.

In dieser Übung erstellen Sie **Custom Vision**-Ressourcen für Training und Vorhersage, damit Sie den Zugriff und die Kosten für diese Workloads separat verwalten können.

1. Öffnen Sie auf einer neuen Browserregisterkarte das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie die Schaltfläche **&#65291;Ressource erstellen** aus, suchen Sie nach *Custom Vision*, und erstellen Sie eine **Custom Vision**-Ressource mit den folgenden Einstellungen:
    - **Erstellungsoptionen**: Beide
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif für Training**: F0
    - **Tarif für Vorhersage**: F0

    > **Hinweis**: Falls Ihr Abonnement bereits einen Custom Vision-Dienst im Tarif F0 enthält, wählen Sie für diesen **S0** aus.

1. Warten Sie, bis die Ressourcen erstellt wurden, zeigen Sie dann die Bereitstellung an, und beachten Sie, dass zwei Custom Vision-Ressourcen bereitgestellt wurden, eine für das Training und eine für die Vorhersage (erkennbar am Suffix **-Prediction**). Sie können diese Ressourcen anzeigen, indem Sie zur Ressourcengruppe navigieren, in der Sie sie erstellt haben.

> **Wichtig**: Jede Ressource hat ihren eigenen *Endpunkt* und *Schlüssel*, die verwendet werden, um den Zugriff aus Ihrem Code zu verwalten. Um ein Bildklassifizierungsmodell zu trainieren, muss Ihr Code die *Trainings*ressource (mit ihrem Endpunkt und Schlüssel) verwenden, und um das trainierte Modell zum Vorhersagen von Bildklassen zu verwenden, muss Ihr Code die *Vorhersage*ressource (mit ihrem Endpunkt und Schlüssel) verwenden.

## Klonen des Repositorys für diesen Kurs

Sie entwickeln Ihren Code mithilfe von Cloud Shell aus dem Azure-Portal. Die Codedateien für Ihre App wurden in einem GitHub-Repository bereitgestellt.

> **Tipp**: Wenn Sie das Repository **mslearn-ai-vision** erst kürzlich geklont haben, können Sie diese Aufgabe überspringen. Führen Sie andernfalls die folgenden Schritte aus, um es in Ihrer Entwicklungsumgebung zu klonen.

1. Verwenden Sie im Azure-Portal die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Geben Sie im PowerShell-Bereich die folgenden Befehle ein, um das GitHub-Repository für diese Übung zu klonen:

    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/microsoftlearning/mslearn-ai-vision mslearn-ai-vision
    ```

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
   cd mslearn-ai-vision/Labfiles/07-custom-vision-image-classification
    ```
    
## Erstellen eines Custom Vision-Projekts

Um ein Bildklassifizierungsmodell zu trainieren, müssen Sie ein Custom Vision-Projekt auf Grundlage Ihrer Trainingsressource erstellen. Dazu verwenden Sie das Custom Vision-Portal.

1. Laden Sie die Trainingsbilder von `https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/main/Labfiles/07-custom-vision-image-classification/training-images.zip` herunter und extrahieren Sie den ZIP-Ordner, um dessen Inhalte anzuzeigen. Dieser Ordner enthält Unterordner mit Bildern von Äpfeln, Bananen und Organen.
1. Öffnen Sie auf einer neuen Browserregisterkarte das Custom Vision-Portal unter `https://customvision.ai`. Falls Sie dazu aufgefordert werden, melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist, und stimmen Sie den Nutzungsbedingungen zu.
1. Erstellen Sie im Custom Vision-Portal ein neues Projekt mit den folgenden Einstellungen:
    - **Name**: Classify Fruit (Obst klassifizieren)
    - **Beschreibung**: Bildklassifizierung für Obst
    - **Ressource**: *Die Custom Vision-Ressource, die Sie zuvor erstellt haben*
    - **Projekttypen:** Klassifizierung
    - **Klassifizierungstypen**: Multiklassen-Klassifizierung (einzelnes Tag pro Bild)
    - **Domänen**: Lebensmittel
1. Klicken Sie in dem neuen Projekt auf **\[+\]Bilder hinzufügen**, und wählen Sie alle Dateien in dem Ordner **training-images/apple** aus, den Sie zuvor angezeigt haben. Laden Sie anschließend die Bilddateien mit dem Tag *Apfel* hoch:

![Hochladen eines Apfels mit dem Tag „Apfel“](../media/upload_apples.jpg)
   
1. Wiederholen Sie den vorherigen Schritt, um die Bilder im Ordner **banana** mit dem Tag *Banane* und die Bilder im Ordner **orange** mit dem Tag *Orange* hochzuladen.
1. Sehen Sie sich die Bilder an, die Sie in Ihrem Custom Vision-Projekt hochgeladen haben. Sie sollten 15 Bilder pro Klasse haben, wie hier gezeigt:

![Markierte Bilder von Obst: 15 Äpfel, 15 Bananen und 15 Orangen](../media/fruit.jpg)
    
1. Klicken Sie im Custom Vision-Projekt über den Bildern auf **Trainieren**, um ein Klassifizierungsmodell mit den markierten Bildern zu trainieren. Wählen Sie die Option **Schnelltraining** aus, und warten Sie, bis die Trainingsiteration abgeschlossen wurde (dieser Vorgang kann etwa eine Minute dauern).
1. Warten Sie, bis die Modelliteration trainiert wurde, und überprüfen Sie die Leistungsmetriken *Genauigkeit*, *Abruf* und *AP*. Diese Metriken messen die Vorhersagegenauigkeit des Klassifizierungsmodells und sollten jeweils den Wert „Hoch“ haben.

> **Hinweis**: Die Leistungsmetriken basieren auf einem Wahrscheinlichkeitsschwellenwert von 50 % für jede Vorhersage (d. h., wenn das Modell eine Wahrscheinlichkeit von 50 % oder höher berechnet, dass ein Bild einer bestimmten Klasse entspricht, wird diese Klasse vorhergesagt). Sie können dies oben links auf der Seite anpassen.

## Testen des Modells

Nachdem Sie das Modell nun trainiert haben, können Sie es testen.

1. Klicken Sie über den Leistungsmetriken auf **Schnelltest**.
1. Geben Sie `https://aka.ms/apple-image` im Feld **Bild-URL** ein, und klicken Sie auf „&#10132;“.
1. Sehen Sie sich die von Ihrem Modell zurückgegebenen Vorhersagen an. *Apfel* sollte den höchsten Wahrscheinlichkeitswert haben, wie hier gezeigt:

![Ein Bild mit einer Klassenvorhersage für Apfel](../media/test-apple.jpg)

1. Schließen Sie das Fenster **Schelltest**.

## Anzeigen der Projekteinstellungen

Dem von Ihnen erstellten Projekt wurde ein eindeutiger Bezeichner zugewiesen, den Sie in jedem Code angeben müssen, der damit interagiert.

1. Klicken Sie auf das Symbol *Einstellungen* (&#9881;) oben rechts auf der Seite **Leistung**, um die Projekteinstellungen anzuzeigen.
1. Notieren Sie unter **Allgemein** (auf der linken Seite) die **Projekt-ID**, die dieses Projekt eindeutig identifiziert.
1. Beachten Sie, dass auf der rechten Seite unter **Ressourcen** der Schlüssel und der Endpunkt angezeigt werden. Dies sind die Details für die *Trainingsressource*. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

## Verwenden der *Trainings*-API

Das Custom Vision-Portal bietet eine praktische Benutzeroberfläche, mit der Sie Bilder hochladen und markieren sowie Modelle trainieren können. In einigen Szenarien kann es jedoch sinnvoll sein, das Modelltraining mithilfe der Custom Vision-Trainings-API zu automatisieren.

> **Hinweis**: In dieser Übung können Sie wahlweise die API aus dem **C#** oder **Python** SDK verwenden. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Führen Sie im Azure-Portal den Befehl `cd C-Sharp/train-classifier` oder `cd Python/train-classifier` abhängig von Ihrer bevorzugten Sprache aus.
1. Installieren Sie das Custom Vision-Trainingspaket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-customvision==3.1.0
    ```

1. Sie können mithilfe des Befehls `ls` den Inhalt des Ordners **train-classifier** anzeigen. Beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#**: appsettings.json
    - **Python**: .env

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **C#**

    ```
   code appsettings.json
    ```

    **Python**

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Aktualisieren Sie in der Codedatei die darin enthaltenen Konfigurationswerte so, dass der **Endpunkt** und **Authentifizierungsschlüssel** für Ihre Custom Vision-*Trainingsressource* und die Projekt-ID für das zuvor erstellte Objekterkennungsprojekt verwendet werden.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.
1. Beachten Sie, dass der Ordner **train-classifier** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: train-classifier.py

    Öffnen Sie die Codedatei, und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Namespaces aus dem von Ihnen installierten Paket werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt, um einen authentifizierten **CustomVisionTrainingClient** zu erstellen, der dann mit der Projekt-ID verwendet wird, um einen **Projekt**verweis auf Ihr Projekt zu erstellen.
    - Die **Upload_Images**-Funktion ruft die im Custom Vision-Projekt definierten Tags ab und lädt dann Bilddateien aus entsprechend benannten Ordnern in das Projekt hoch, wobei die jeweils entsprechende Tag-ID zugewiesen wird.
    - Die **Train_Model**-Funktion erstellt eine neue Trainingsiteration für das Projekt und wartet auf den Abschluss des Trainings.
1. Schließen Sie den Code-Editor und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python train-classifier.py
    ```
    
1. Warten Sie, bis das Programm beendet wird. Kehren Sie dann zu Ihrem Browser zurück, und zeigen Sie die Seite **Trainingsbilder** für Ihr Projekt im Custom Vision-Portal an (aktualisieren Sie ggf. den Browser).
1. Vergewissern Sie sich, dass dem Projekt einige neue markierte Bilder hinzugefügt wurden. Zeigen Sie dann die Seite **Leistung** an, und überprüfen Sie, ob eine neue Iteration erstellt wurde.

## Veröffentlichen des Bildklassifizierungsmodells

Jetzt können Sie Ihr trainiertes Modell veröffentlichen, damit es in einer Clientanwendung verwendet werden kann.

1. Klicken Sie im Custom Vision-Portal auf der Seite **Leistung** auf **&#128504; Veröffentlichen**, um das trainierte Modell mit den folgenden Einstellungen zu veröffentlichen:
    - **Modellname**: fruit-classifier
    - **Vorhersageressource:** *Dies ist die zuvor erstellte **Vorhersageressource**, die mit „-Prediction“ endet (<u>nicht</u> die Trainingsressource).*
1. Klicken Sie links oben auf der Seite **Projekteinstellungen** auf das Symbol für den *Projektkatalog* (&#128065;), um zur Startseite des Custom Vision-Portals zu gelangen, auf der Ihr Projekt jetzt aufgelistet wird.
1. Klicken Sie auf der Startseite des Custom Vision-Portals oben rechts auf das Symbol *Einstellungen* (&#9881;), um die Einstellungen für Ihren Custom Vision-Dienst anzuzeigen. Suchen Sie dann unter **Ressourcen** Ihre auf „-Prediction“ endende *Vorhersageressource* (<u>nicht</u> die Trainingsressource), um ihre Werte für **Schlüssel** und **Endpunkt** zu ermitteln. (Sie können diese Informationen auch abrufen, indem Sie die Ressource im Azure-Portal anzeigen.)

## Verwenden der Bildklassifizierung aus einer Clientanwendung

Nachdem Sie nun das Bildklassifizierungsmodell veröffentlicht haben, können Sie es aus einer Clientanwendung heraus verwenden. Auch hier können Sie wieder wahlweise **C#** oder **Python** verwenden.

1. Führen Sie in der Cloud Shell den Befehl `cd ../test-classifier` aus und geben Sie dann den folgenden SDK-spezifischen Befehl ein, um das Custom Vision-Vorhersagepaket zu installieren:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-customvision==3.1.0
    ```

> **Hinweis**: Das Python SDK-Paket enthält sowohl Trainings- als auch Vorhersagepakete und ist möglicherweise bereits installiert.

1. Mithilfe des Befehls `ls` können Sie den Inhalt des Ordners **test-classifier** anzeigen, der zum Implementieren einer Testclientanwendung für Ihr Bildklassifizierungsmodell verwendet wird.
1. Öffnen Sie die Konfigurationsdatei für Ihre Clientanwendung (*appsettings.json* für C# oder *.env* für Python), und aktualisieren Sie die darin enthaltenen Konfigurationswerte so, dass der Endpunkt und Schlüssel für Ihre Custom Vision-*Vorhersage*ressource, die Projekt-ID für das Klassifizierungsprojekt und der Name Ihres veröffentlichten Modells (dieses sollte *fruit-classifier* sein) verwendet werden. Speichern Sie Ihre Änderungen und schließen Sie den Code-Editor.
1. Öffnen Sie die Codedatei für Ihre Clientanwendung (*Program.cs* für C#, *test-classification.py* für Python), und überprüfen Sie den darin enthaltenen Code, wobei Sie sich die folgenden Details notieren:
    - Namespaces aus dem von Ihnen installierten Paket werden importiert.
    - Die **Main**-Funktion ruft die Konfigurationseinstellungen ab und verwendet den Schlüssel und Endpunkt zum Erstellen eines authentifizierten **CustomVisionPredictionClient**.
    - Das Vorhersageclientobjekt wird verwendet, um eine Klasse für jedes Bild im Ordner **test-images** vorherzusagen, wobei die Projekt-ID und der Modellname für jede Anforderung angegeben werden. Jede Vorhersage umfasst eine Wahrscheinlichkeit für jede mögliche Klasse, und es werden nur vorhergesagte Tags mit einer Wahrscheinlichkeit von mehr als 50 % angezeigt.
1. Schließen Sie den Code-Editor und geben Sie den folgenden SDK-spezifischen Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python test-classifier.py
    ```

1. Zeigen Sie die Bezeichnung (das Tag) und Wahrscheinlichkeitsbewertungen für jede Vorhersage an. Sie können die Bilder im Ordner **test-images** herunterladen, um zu überprüfen, ob das Modell sie ordnungsgemäß klassifiziert hat.

1. Wählen Sie auf der Symbolleiste der Cloud Shell **Dateien hochladen/herunterladen** und dann **Herunterladen** aus. Geben Sie im neuen Dialogfeld den folgenden Dateipfad ein und wählen Sie **Herunterladen** aus:

    **C#**
   
    ```
   mslearn-ai-vision/Labfiles/07-custom-vision-image-classification/C-Sharp/test-classifier/test-images/IMG_TEST_1.jpg
    ```

    **Python**
   
    ```
   mslearn-ai-vision/Labfiles/07-custom-vision-image-classification/Python/test-classifier/test-images/IMG_TEST_1.jpg
    ```

   Sie können über denselben Pfad auch `IMG_TEST_2.jpg` und `IMG_TEST_3.jpg` herunterladen.

## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und suchen Sie in der oberen Suchleiste nach den Ressourcen, die Sie in diesem Lab erstellt haben.

1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource. Alternativ können Sie die gesamte Ressourcengruppe löschen, um alle Ressourcen gleichzeitig zu bereinigen.

## Weitere Informationen

Weitere Informationen zur Bildklassifizierung mit dem Custom Vision-Dienst finden Sie in der [Custom Vision Dokumentation](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/).
