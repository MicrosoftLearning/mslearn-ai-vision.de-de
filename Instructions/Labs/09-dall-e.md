---
lab:
  title: Generieren von Bildern mit KI
  description: Verwenden Sie ein OpenAI-Modell als DALL-E-Modell in Azure AI Foundry zum Generieren von Bildern.
---

# Generieren von Bildern mit KI

In dieser Übung verwenden Sie das OpenAI DALL-E generative KI-Modell zum Generieren von Bildern. Verwenden Sie auch das OpenAI-Python-SDK, um eine einfache App zum Generieren von Bildern basierend auf Ihren Prompts zu erstellen.

> **Hinweis:** Diese Übung basiert auf Vorabversionen von SDK-Software, die sich möglicherweise Änderungen unterliegen. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

Obwohl diese Übung auf dem OpenAI-Python-SDK basiert, können Sie KI-Chatanwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

* [OpenAI-Projekte für Microsoft .NET](https://www.nuget.org/packages/OpenAI)
* [OpenAI-Projekte für JavaScript](https://www.npmjs.com/package/openai)

Diese Übung dauert ca. **30** Minuten.

## Öffnen des Azure KI Foundry-Portals

Beginnen wir mit der Anmeldung im Azure AI Foundry-Portal.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Prüfen Sie die Informationen auf der Startseite.

## Auswählen eines Modells zum Starten eines Projekts

Ein *Azure KI-Projekt* bietet einen Arbeitsbereich für die KI-Entwicklung. Beginnen wir damit, ein Modell auszuwählen, mit dem wir arbeiten möchten, und ein Projekt zu erstellen, in dem wir es verwenden möchten.

> **Hinweis**: KI Foundry-Projekte können auf einer *Azure AI Foundry-Ressource* basieren, die Zugriff auf KI-Modelle (einschließlich Azure OpenAI), Azure AI-Dienste und andere Ressourcen für die Entwicklung von KI-Agents und Chatlösungen bietet. Alternativ können Projekte auf *KI-Hub*-Ressourcen basieren, die Verbindungen zu Azure-Ressourcen für sichere Speicherung, Rechenleistung und spezialisierte Tools umfassen. Azure AI Foundry-basierte Projekte eignen sich hervorragend für Fachkräfte in der Entwicklung, die Ressourcen für die Entwicklung von KI-Agents oder Chat-Apps verwalten möchten. KI-Hub-basierte Projekte eignen sich besser für Enterprise-Entwicklungsteams, die an komplexen KI-Lösungen arbeiten.

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `dall-e-3`, das wir in unserem Projekt verwenden werden.

1. Wählen Sie in den Suchergebnissen das Modell **dall-e-3** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.

1. Wenn Sie zur Erstellung eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und entfalten Sie die Option **Erweiterte Optionen**.

1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: Wählen Sie die **empfohlene AI Foundry-Instanz aus***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Wählen Sie **Erstellen** aus und warten Sie, bis Ihr Projekt einschließlich der von Ihnen ausgewählten Dall-e-3-Modellimplementierung erstellt wurde.

    > Hinweis: Je nach Modellauswahl erhalten Sie möglicherweise während des Projekterstellungsprozesses zusätzliche Eingabeaufforderungen. Stimmen Sie den Bedingungen zu und schließen Sie die Bereitstellung ab.

1. Wenn Ihr Projekt erstellt wurde, wird Ihr Modell auf der Seite **Modelle + Endpunkte** angezeigt.

## Testen des Modells im Playground

Vor der Erstellung einer Clientanwendung testen wir das DALL-E-Modell im Playground.

1. Wählen Sie **Playgrounds** und dann**Playground für Bilder** aus.

1. Stellen Sie sicher, dass ihre DALL-E-Modellbereitstellung ausgewählt ist. Geben Sie dann im Feld unten auf der Seite einen Prompt ein, z. B. `Create an image of an robot eating spaghetti`, und wählen Sie **Generieren** aus.

1. Überprüfen Sie das sich ergebende Bild im Playground:

    ![Screenshot des Bild-Playgrounds mit einem generierten Bild.](../media/images-playground.png)

1. Geben Sie eine Folgeäußerung ein, z. B. `Show the robot in a restaurant` und überprüfen Sie das sich ergebende Bild.

1. Fahren Sie mit dem Testen mit neuen Eingabeaufforderungen fort, um das Bild zu optimieren, bis sie mit den Ergebnissen zufrieden sind. 

1. Wählen Sie die Schaltfläche **\</\>Code anzeigen** aus und stellen Sie sicher, dass Sie sich auf der Registerkarte **Entra-ID-Authentifizierung** befinden. Notieren Sie dann die folgenden Informationen für die spätere Verwendung in der Übung. Beachten Sie, dass es sich um Beispielwerte handelt und stellen Sie sicher, dass sie die Informationen aus Ihrer Bereitstellung notieren.

    * OpenAI-Endpunkt: *https://dall-e-aus-resource.cognitiveservices.azure.com/*
    * OpenAI-API-Version: *2024-04-01-preview*
    * Bereitstellungsname (Modellname): *dall-e-3*

## Das Erstellen einer Clientanwendung

Das Modell scheint im Playground zu funktionieren. Jetzt können Sie das OpenAI-SDK in einer Clientanwendung verwenden.

### Vorbereiten der Anwendungskonfiguration

1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

1. Verwenden Sie die Taste **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

    > **Hinweis:** Wenn Sie vom Portal aufgefordert werden, einen Speicher auszuwählen, um Ihre Dateien beizubehalten, wählen Sie **Kein Speicherkonto erforderlich** aus. Wählen Sie das verwendete Abonnement aus und drücken Sie auf **Anwenden**.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):

    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    ```
   cd mslearn-ai-vision/Labfiles/dalle-client/python
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity openai requests
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie die Platzhalter **your_endpoint**, **your_model_deployment** und **your_api_version** durch die Werte, die Sie im **Playground für Bilder** aufgezeichnet haben.

1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und mit Ihrem Modell zu chatten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    ```
   code dalle-client.py
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Fügen Sie dann unter dem Kommentar **Add references** den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential, get_bearer_token_provider
   from openai import AzureOpenAI
   import requests
    ```

1. Beachten Sie, dass der Code in der **Main**-Funktion unter dem Kommentar **Get configuration settings** von Ihnen in der Konfigurationsdatei definierte Werte für Endpunkt, API-Version und Modellimplementierungsnamen lädt.

1. Fügen Sie unter dem Kommentar **Initialize the client** den folgenden Code hinzu, um sich mit Ihrem Modell zu verbinden, und verwenden Sie dabei die Azure-Anmeldeinformationen, mit denen Sie derzeit angemeldet sind:

    ```python
   # Initialize the client
   token_provider = get_bearer_token_provider(
       DefaultAzureCredential(exclude_environment_credential=True,
           exclude_managed_identity_credential=True), 
       "https://cognitiveservices.azure.com/.default"
   )
    
   client = AzureOpenAI(
       api_version=api_version,
       azure_endpoint=endpoint,
       azure_ad_token_provider=token_provider
   )
    ```

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Fügen Sie dann im Schleifenabschnitt unter dem Kommentar **Generate an image** den folgenden Code hinzu, um den Prompt zu übermitteln und die URL für das generierte Bild aus Ihrem Modell abzurufen:

    **Python**

    ```python
   # Generate an image
   result = client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

   json_response = json.loads(result.model_dump_json())
   image_url = json_response["data"][0]["url"] 
    ```

1. Beachten Sie, dass der Code im restlichen Teil der **Hauptfunktion** die Bild-URL und einen Dateinamen an eine bereitgestellte Funktion übergibt, die das generierte Bild herunterlädt und als .png Datei speichert.

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen an der Codedatei zu speichern und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Ausführen der Clientanwendung

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
   az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Szenarien ist nur die Verwendung von *az login* ausreichend. Wenn Sie jedoch Abonnements in mehreren Mandqanten haben, müssen Sie möglicherweise den Mandanten mit dem Parameter *--tenant* angeben. Weitere Informationen finden Sie unter [Interaktive Anmeldung bei Azure mit der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).

1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den angegebenen Authentifizierungscode und Ihre Azure-Anmeldeinformationen ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab, und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry Hub enthält, wenn Sie dazu aufgefordert werden.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die App auszuführen:

    ```
   python dalle-client.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Anforderung für ein Bild ein, z. B. `Create an image of a robot eating pizza`. Nach kurzer Zeit sollte die App bestätigen, dass das Bild gespeichert wurde.

1. Probieren Sie einige weitere Prompts aus. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

    > **Hinweis**: In dieser einfachen App wurde keine Logik implementiert, um den Unterhaltungsverlauf beizubehalten. Daher behandelt das Modell jeden Prompt als neue Anforderung ohne Kontext des vorherigen Prompts.

1. Um die Bilder, die von Ihrer App generiert wurden, herunterzuladen und anzuzeigen, verwenden Sie den Cloud Shell-Befehl **download** und geben Sie die PNG-Datei an, die generiert wurde:

    ```
   download ./images/image_1.png
    ```

    Der Befehl „download“ erstellt unten rechts im Browser einen Popuplink, den Sie auswählen können, um die Datei herunterzuladen und zu öffnen.

## Zusammenfassung

In dieser Übung haben Sie Azure AI Foundry und das Azure OpenAI SDK zum Erstellen einer Clientanwendung verwendet, die ein DALL-E-Modell zum Generieren von Bildern zu verwendet.

## Bereinigen

Wenn Sie mit der Erkundung von DALLE-E fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
