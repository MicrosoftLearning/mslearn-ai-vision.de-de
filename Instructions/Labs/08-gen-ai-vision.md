---
lab:
  title: Entwickeln einer Vision-fähigen Chat-App
  description: 'Verwenden Sie Azure AI Foundry, um eine generative KI-App zu erstellen, die Text-, Bild- und Audioeingaben unterstützt.'
---

# Entwickeln einer Vision-fähigen Chat-App

In dieser Übung verwenden Sie das generative KI-Modell *Phi-4-multimodal-instruct*, um Antworten auf Prompts zu generieren, die Bilder enthalten. Sie entwickeln eine App, die KI-Unterstützung bei frischen Produkten in einem Lebensmittelgeschäft bietet, indem Sie Azure AI Foundry und den Azure KI-Modellinferenz-Dienst verwenden.

> **Hinweis:** Diese Übung basiert auf Vorabversionen von SDK-Software, die sich möglicherweise Änderungen unterliegen. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

Obwohl diese Übung auf dem Azure AI Foundry Python SDK basiert, können Sie KI-Chatanwendungen mit mehreren sprachspezifischen SDKs entwickeln, einschließlich:

- [Azure KI-Projekte für Python](https://pypi.org/project/azure-ai-projects)
- [Azure KI-Projekte für Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Azure KI-Projekte für JavaScript](https://www.npmjs.com/package/@azure/ai-projects)

Diese Übung dauert ca. **30** Minuten.

## Öffnen des Azure KI Foundry-Portals

Beginnen wir mit der Anmeldung im Azure AI Foundry-Portal.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Prüfen Sie die Informationen auf der Startseite.

## Auswählen eines Modells zum Starten eines Projekts

Ein *Azure KI-Projekt* bietet einen Arbeitsbereich für die KI-Entwicklung. Beginnen wir damit, ein Modell auszuwählen, mit dem wir arbeiten möchten, und ein Projekt zu erstellen, in dem wir es verwenden möchten.

> **Hinweis**: KI Foundry-Projekte können auf einer *Azure AI Foundry-Ressource* basieren, die Zugriff auf KI-Modelle (einschließlich Azure OpenAI), Azure AI-Dienste und andere Ressourcen für die Entwicklung von KI-Agents und Chatlösungen bietet. Alternativ können Projekte auf *KI-Hub*-Ressourcen basieren, die Verbindungen zu Azure-Ressourcen für sichere Speicherung, Rechenleistung und spezialisierte Tools umfassen. Azure AI Foundry-basierte Projekte eignen sich hervorragend für Fachkräfte in der Entwicklung, die Ressourcen für die Entwicklung von KI-Agents oder Chat-Apps verwalten möchten. KI-Hub-basierte Projekte eignen sich besser für Enterprise-Entwicklungsteams, die an komplexen KI-Lösungen arbeiten.

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `Phi-4-multimodal-instruct`, das wir in unserem Projekt verwenden werden.

1. Wählen Sie in den Suchergebnissen das Modell **Phi-4-multimodal-instruct** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.

1. Wenn Sie zur Erstellung eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und entfalten Sie die Option **Erweiterte Optionen**.

1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: Wählen Sie die **empfohlene AI Foundry-Instanz aus***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Wählen Sie **Erstellen** und warten Sie, bis Ihr Projekt einschließlich der von Ihnen ausgewählten Phi-4-multimodal-instruct-Modellimplementierung erstellt wurde.

    > Hinweis: Je nach Modellauswahl erhalten Sie möglicherweise während des Projekterstellungsprozesses zusätzliche Eingabeaufforderungen. Stimmen Sie den Bedingungen zu und schließen Sie die Bereitstellung ab.

1. Wenn Ihr Projekt erstellt wurde, wird Ihr Modell auf der Seite **Modelle + Endpunkte** angezeigt:

    ![Screenshot der Modellimplementierungsseite.](./media/ai-foundry-model-deployment.png)

## Testen des Modells im Playground

Nun können Sie Ihre multimodale Modellimplementierung mit einem bildbasierten Prompt im Chat-Playground testen.

1. Wählen Sie auf der Modellimplementierungsseite **Im Playground öffnen** aus.

1. Laden Sie in einer neuen Browserregisterkarte [mango.jpeg](https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/refs/heads/main/Labfiles/gen-ai-vision/mango.jpeg) von `https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/refs/heads/main/Labfiles/gen-ai-vision/mango.jpeg` herunter und speichern Sie es in einem Ordner im lokalen Dateisystem.

1. Stellen Sie auf der Seite „Chat-Playground“ im Bereich **Setup** sicher, dass Ihre **Phi-4-multimodal-instruct**-Modelimplementierung ausgewählt ist.

1. Verwenden Sie im Bereich der Hauptchatsitzung unter dem Chateingabefeld die Schaltfläche „Anfügen“ (**&#128206;**), um die Bilddatei *mango.jpeg* hochzuladen, und fügen Sie dann den Text `What desserts could I make with this fruit?` hinzu und senden Sie den Prompt ab.

    ![Screenshot der Seite „Chat-Playground“.](../media/chat-playground-image.png)

1. Lesen Sie die Antwort, die hoffentlich relevante Anleitungen für Desserts bereitstellen sollte, die Sie mit einer Mango zubereiten können.

## Das Erstellen einer Clientanwendung

Nachdem Sie das Modell bereitgestellt haben, können Sie die Bereitstellung in einer Clientanwendung verwenden.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.

1. Stellen Sie im Bereich **Endpunkte und Schlüssel** sicher, dass die **Azure AI Foundry**-Bibliothek ausgewählt ist und notieren Sie den **Azure AI Foundry-Projektendpunkt**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.

1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

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
   cd mslearn-ai-vision/Labfiles/gen-ai-vision/python
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    ```
   code .env
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei den Platzhalter **your_project_endpoint** durch den Foundry-Projektendpunkt (von der **Übersichtsseite** des Azure AI Foundry-Portals kopiert) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer Phi-4-multimodal-instruct-Modellimplementierung zugewiesen haben.

1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und einen Chat-Client für Ihr Modell zu erhalten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    ```
   code chat-app.py
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Suchen Sie dann den Kommentar **Verweise hinzufügen** und fügen Sie den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
1. Suchen Sie den Kommentar **Initialize the project client**, und fügen Sie den folgenden Code hinzu, um eine Verbindung mit Ihrem Azure AI Foundry-Projekt herzustellen:

    > **Tipp**: Achten Sie darauf, die richtige Einzugsebene für den Code beizubehalten.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_endpoint,
        )
    ```

1. Finden Sie den Kommentar **Get a chat client** und fügen Sie den folgenden Code hinzu, um ein Client-Objekt für den Chat mit einem Modell zu erstellen:

    ```python
   # Get a chat client
   openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```

### Schreiben von Code zur Übermittlung eines URL-basierten Prompts

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Suchen Sie dann im Schleifenabschnitt den Kommentar **Antwort auf Bildeingabe erhalten** und fügen Sie den folgenden Code ein, um einen Prompt zu übermitteln, der das folgende Bild enthält:

    ![Ein Foto einer Mango.](../media/orange.jpeg)

    ```python
   # Get a response to image input
   image_url = "https://github.com/MicrosoftLearning/mslearn-ai-vision/raw/refs/heads/main/Labfiles/gen-ai-vision/orange.jpeg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = openai_client.chat.completions.create(
        model=model_deployment,
        messages=[
            {"role": "system", "content": system_message},
            { "role": "user", "content": [  
                { "type": "text", "text": prompt},
                { "type": "image_url", "image_url": {"url": data_url}}
            ] } 
        ]
   )
   print(response.choices[0].message.content)
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Schließen Sie sie jedoch noch nicht.

## Bei Azure anmelden und die App ausführen

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
   az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Szenarien ist nur die Verwendung von *az login* ausreichend. Wenn Sie jedoch Abonnements in mehreren Mandqanten haben, müssen Sie möglicherweise den Mandanten mit dem Parameter *--tenant* angeben. Weitere Informationen finden Sie unter [Interaktive Anmeldung bei Azure mit der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den angegebenen Authentifizierungscode und Ihre Azure-Anmeldeinformationen ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab, und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry Hub enthält, wenn Sie dazu aufgefordert werden.

1. Geben Sie nach der Anmeldung den folgenden Befehl ein, um die Anwendung auszuführen:

    ```
   python chat-app.py
    ```

1. Geben Sie den folgenden Prompt ein, wenn Sie dazu aufgefordert werden:

    ```
   Suggest some recipes that include this fruit
    ```

1. Überprüfen Sie die Antwort. Geben Sie dann `quit` zum Beenden des Programms ein.

### Ändern Sie den Code, um eine lokale Bilddatei hochzuladen.

1. Suchen Sie im Code-Editor für Ihren App-Code im Schleifenabschnitt den Code, den Sie zuvor unter dem Kommentar **Antwort auf Bildeingabe erhalten** hinzugefügt haben. Ändern Sie dann den Code wie folgt, um diese lokale Bilddatei hochzuladen:

    ![Ein Foto von einer Drachenfrucht.](../media/mystery-fruit.jpeg)

    ```python
   # Get a response to image input
   script_dir = Path(__file__).parent  # Get the directory of the script
   image_path = script_dir / 'mystery-fruit.jpeg'
   mime_type = "image/jpeg"

   # Read and encode the image file
   with open(image_path, "rb") as image_file:
        base64_encoded_data = base64.b64encode(image_file.read()).decode('utf-8')

   # Include the image file data in the prompt
   data_url = f"data:{mime_type};base64,{base64_encoded_data}"
   response = openai_client.chat.completions.create(
            model=model_deployment,
            messages=[
                {"role": "system", "content": system_message},
                { "role": "user", "content": [  
                    { "type": "text", "text": prompt},
                    { "type": "image_url", "image_url": {"url": data_url}}
                ] } 
            ]
   )
   print(response.choices[0].message.content)
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    ```
   python chat-app.py
    ```

1. Geben Sie den folgenden Prompt ein, wenn Sie dazu aufgefordert werden:

    ```
   What is this fruit? What recipes could I use it in?
    ```

15. Überprüfen Sie die Antwort. Geben Sie dann `quit` zum Beenden des Programms ein.

    > **Hinweis**: In dieser einfachen App wurde keine Logik implementiert, um die aufgezeichneten Unterhaltungen beizubehalten. Daher behandelt das Modell jeden Prompt als neue Anforderung ohne Kontext des vorherigen Prompts.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
