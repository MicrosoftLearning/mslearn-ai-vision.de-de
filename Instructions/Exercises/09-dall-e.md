---
lab:
  title: Generieren von Bildern mit KI
  description: 'Erfahren Sie, wie Sie ein DALL-E OpenAI-Modell zum Generieren von Bildern verwenden.'
---

# Generieren von Bildern mit KI

In dieser Übung verwenden Sie das generative KI-Modell OpenAI DALL-E zum Generieren von Bildern. Sie entwickeln Ihre App mithilfe von Azure AI Foundry und dem Azure OpenAI Service.

Diese Übung dauert ca. **30** Minuten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](../media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option, einen neuen zu erstellen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **dalle** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*.
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden durch regionale Kontingente eingeschränkt. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](../media/ai-foundry-project.png)

## Bereitstellen eines DALL-E-Modells

Jetzt können Sie ein DALL-E-Modell bereitstellen, um die Bildgenerierung zu unterstützen.

1. Verwenden Sie in der Symbolleiste oben rechts auf Ihrer Azure AI Foundry-Projektseite das Symbol **Funktionen anzeigen**, um das Feature **Modelle zum Azure KI-Modellinferenz-Service bereitstellen** zu aktivieren. Mit diesem Feature wird sichergestellt, dass Ihre Modellbereitstellung für den Azure KI-Rückschlussdienst verfügbar ist, den Sie im Anwendungscode verwenden.
1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie in der Liste das Modell **dalle-e-3**, wählen Sie es aus und bestätigen Sie es.
1. Stimmen Sie der Lizenzvereinbarung zu, wenn Sie dazu aufgefordert werden, und stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein gültiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Bereitstellungsdetails**: *Verwenden der Standardeinstellungen.*
1. Warten Sie, bis der Bereitstellungsstatus **Abgeschlossen** ist.

## Testen des Modells im Playground

Vor der Erstellung einer Clientanwendung testen wir das DALL-E-Modell im Playground.

1. Wählen Sie auf der Seite für das bereitgestellte DALL-E-Modell die Option **Im Playground öffnen** aus (oder öffnen Sie auf der Seite **Playgrounds** den **Bilder-Playground**).
1. Stellen Sie sicher, dass ihre DALL-E-Modellbereitstellung ausgewählt ist. Geben Sie im Feld **Eingabeaufforderung** eine Eingabeaufforderung ein, z. B.`Create an image of an robot eating spaghetti`.
1. Überprüfen Sie das sich ergebende Bild im Playground:

    ![Screenshot des Bild-Playgrounds mit einem generierten Bild.](../media/images-playground.png)

1. Geben Sie eine Folgeäußerung ein, z. B. `Show the robot in a restaurant` und überprüfen Sie das sich ergebende Bild.
1. Fahren Sie mit dem Testen mit neuen Eingabeaufforderungen fort, um das Bild zu optimieren, bis sie mit den Ergebnissen zufrieden sind.

## Das Erstellen einer Clientanwendung

Das Modell scheint im Playground zu funktionieren. Jetzt können Sie das Azure OpenAI SDK verwenden, um es in einer Clientanwendung zu verwenden.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.
1. Verwenden Sie die Taste **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung aus. Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

5. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Codedateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, um ihn als reinen Text einzufügen):


    ```
    rm -r mslearn-ai-vision -f
    git clone https://github.com/MicrosoftLearning/mslearn-ai-vision
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers einnehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem sprachspezifischen Ordner, der die Anwendungscodedateien enthält, basierend auf der Programmiersprache Ihrer Wahl (Python oder C#):  

    **Python**

    ```
   cd mslearn-ai-vision/Labfiles/09-dalle-client/python
    ```

    **C#**

    ```
   cd mslearn-ai-vision/Labfiles/09-dalle-client/c-sharp
    ```

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Codedatei den Platzhalter **your_project_endpoint** durch die Zeichenfolge für die Verbindung zu Ihrem Projekt (kopiert von der **Übersichtsseite** des Azure AI Foundry-Portals) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer dall-e-3-Modellbereitstellung zugewiesen haben.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und mit Ihrem Modell zu chatten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Fügen Sie dann unter dem Kommentar **Add references** den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
1. Fügen Sie unter dem Kommentar **Initialize the project client** den folgenden Code hinzu, um sich mit Ihrem Azure AI Foundry-Projekt zu verbinden, und verwenden Sie dabei die Azure-Anmeldedaten, mit denen Sie derzeit angemeldet sind:

    **Python**

    ```python
   # Initialize the project client
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Fügen Sie unter dem Kommentar **Get an OpenAI client** den folgenden Code ein, um ein Client-Objekt zum Chatten mit einem Modell zu erstellen:

    **Python**

    ```python
   # Get an OpenAI client
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```csharp
   // Get an OpenAI client
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Fügen Sie dann im Schleifenabschnitt unter dem Kommentar **Generate an image** den folgenden Code hinzu, um den Prompt zu übermitteln und die URL für das generierte Bild aus Ihrem Modell abzurufen:

    **Python**

    ```python
   # Generate an image
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```csharp
   // Generate an image
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. Beachten Sie, dass der Code im restlichen Teil der **Hauptfunktion** die Bild-URL und einen Dateinamen an eine bereitgestellte Funktion übergibt, die das generierte Bild herunterlädt und als .png Datei speichert.

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen an der Codedatei zu speichern und verwenden Sie dann den Befehl **STRG+Q**, um den Code-Editor zu schließen, während die Befehlszeile der Cloud Shell geöffnet bleibt.

### Ausführen der Clientanwendung

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
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
