---
lab:
  title: Erkennen und Analysieren von Gesichtern
  module: Module 4 - Detecting and Analyze Faces
---

# Erkennen und Analysieren von Gesichtern

Die Fähigkeit, menschliche Gesichter zu erkennen und zu analysieren ist eine zentrale KI-Funktion. In dieser Übung lernen Sie zwei Azure KI Services kennen, die Sie verwenden können, um mit Gesichtern in Bildern zu arbeiten: den Dienst **Azure KI Vision** und den Dienst **Gesichtserkennung**.

> **Wichtig:** Dieses Lab kann abgeschlossen werden, ohne einen zusätzlichen Zugriff auf eingeschränkte Features anzufordern.

> **Hinweis**: Ab dem 21. Juni 2022 sind die Funktionen der Azure KI Services, die personenbezogene Informationen zurückgeben, auf Kunden beschränkt, denen [eingeschränkter Zugriff](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access) gewährt wurde. Darüber hinaus sind Funktionen, die Rückschlüsse auf den emotionalen Zustand zulassen, nicht mehr verfügbar. Weitere Informationen zu den von Microsoft vorgenommenen Änderungen und den Gründen hierfür finden Sie unter [Responsible AI investments and safeguards for facial recognition](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/) (Verantwortungsvolle KI-Investitionen und Vorsichtsmaßnahmen für die Gesichtserkennung).

## Klonen des Repositorys für diesen Kurs

Wenn Sie das noch nicht erledigt haben, müssen Sie das Coderepository für diesen Kurs klonen:

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-vision` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus.

## Bereitstellen einer Azure KI Services-Ressource

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine **Azure KI Services**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Suchen Sie in der oberen Suchleiste nach *Azure KI Services*, wählen Sie **Azure KI Services** aus und erstellen Sie eine Azure KI Services Multi-Service-Kontoressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe (wenn Sie eine gehostete Lab-Umgebung verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen, verwenden Sie dann die bereitgestellte Ressourcengruppe).*
    - **Region**: *Wählen Sie eine beliebige verfügbare Region aus*.
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0.
3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## Vorbereiten der Verwendung des Azure KI Vision SDK

In dieser Übung erstellen Sie eine teilweise implementierte Clientanwendung, die das Azure KI Vision SDK nutzt, um Gesichter in einem Bild zu analysieren.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im Fensterbereich **Explorer** zum Ordner **04-face**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **computer-vision**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Vision SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 0.15.1-beta.1
    ```

    **Python**

    ```
    pip install azure-ai-vision==0.15.1b1
    ```
    
3. Zeigen Sie den Inhalt des Ordners **computer-vision** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

4. Öffnen Sie die Konfigurationsdatei und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um den **Endpunkt** und einen **Schlüssel** für die Authentifizierung Ihrer Azure KI Services-Ressource hinzuzufügen. Speichern Sie die Änderungen.

5. Beachten Sie, dass der Ordner **computer-vision** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: detect-people.py

6. Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Vision SDK benötigen:

    **C#**

    ```C#
    // import namespaces
    using Azure.AI.Vision.Common;
    using Azure.AI.Vision.ImageAnalysis;
    ```

    **Python**

    ```Python
    # import namespaces
    import azure.ai.vision as sdk
    ```

## Anzeigen des zu analysierenden Bilds

In dieser Übung analysieren Sie ein Bild von Personen mithilfe des Azure KI Vision-Diensts.

1. Erweitern Sie in Visual Studio Code den Ordner **computer-vision** und den darin enthaltenen Ordner **images**.
2. Wählen Sie das Bild **people.jpg** aus, um es anzuzeigen.

## Gesichtserkennung in einem Bild

Jetzt können Sie das SDK verwenden, um den Vision-Dienst aufzurufen und Gesichter in einem Bild zu erkennen.

1. Beachten Sie in der Codedatei für Ihre Clientanwendung (**Program.cs** oder **detect-faces.py**) in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Azure KI Vision-Client authentifizieren**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Azure KI Vision-Clientobjekt zu erstellen und zu authentifizieren:

    **C#**

    ```C#
    // Authenticate Azure AI Vision client
    var cvClient = new VisionServiceOptions(
        aiSvcEndpoint,
        new AzureKeyCredential(aiSvcKey));
    ```

    **Python**

    ```Python
    # Authenticate Azure AI Vision client
    cv_client = sdk.VisionServiceOptions(ai_endpoint, ai_key)
    ```

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und den Bildpfad dann an eine Funktion namens **AnalyzeImage** weitergibt. Diese Funktion ist noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Specify features to be retrieved (PEOPLE)** (Abzurufende Features angeben (PERSONEN) den folgenden Code ein:

    **C#**

    ```C#
    // Specify features to be retrieved (PEOPLE)
    Features =
        ImageAnalysisFeature.People
    ```

    **Python**

    ```Python
    # Specify features to be retrieved (PEOPLE)
    analysis_options = sdk.ImageAnalysisOptions()
    
    features = analysis_options.features = (
        sdk.ImageAnalysisFeature.PEOPLE
    )    
    ```

4. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image analysis** (Bildanalyse abrufen) den folgenden Code hinzu:

    **C#**

    ```C
    // Get image analysis
    using var imageSource = VisionSource.FromFile(imageFile);
    
    using var analyzer = new ImageAnalyzer(serviceOptions, imageSource, analysisOptions);
    
    var result = analyzer.Analyze();
    
    if (result.Reason == ImageAnalysisResultReason.Analyzed)
    {
        // Get people in the image
        if (result.People != null)
        {
            Console.WriteLine($" People:");
        
            // Prepare image for drawing
            System.Drawing.Image image = System.Drawing.Image.FromFile(imageFile);
            Graphics graphics = Graphics.FromImage(image);
            Pen pen = new Pen(Color.Cyan, 3);
            Font font = new Font("Arial", 16);
            SolidBrush brush = new SolidBrush(Color.WhiteSmoke);
        
            foreach (var person in result.People)
            {
                // Draw object bounding box if confidence > 50%
                if (person.Confidence > 0.5)
                {
                    // Draw object bounding box
                    var r = person.BoundingBox;
                    Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
                    graphics.DrawRectangle(pen, rect);
        
                    // Return the confidence of the person detected
                    Console.WriteLine($"   Bounding box {person.BoundingBox}, Confidence {person.Confidence:0.0000}");
                }
            }
        
            // Save annotated image
            String output_file = "detected_people.jpg";
            image.Save(output_file);
            Console.WriteLine("  Results saved in " + output_file + "\n");
        }
    }
    else
    {
        var errorDetails = ImageAnalysisErrorDetails.FromResult(result);
        Console.WriteLine(" Analysis failed.");
        Console.WriteLine($"   Error reason : {errorDetails.Reason}");
        Console.WriteLine($"   Error code : {errorDetails.ErrorCode}");
        Console.WriteLine($"   Error message: {errorDetails.Message}\n");
    }
    
    ```

    **Python**
    
    ```Python
    # Get image analysis
    image = sdk.VisionSource(image_file)
    
    image_analyzer = sdk.ImageAnalyzer(cv_client, image, analysis_options)
    
    result = image_analyzer.analyze()
    
    if result.reason == sdk.ImageAnalysisResultReason.ANALYZED:
        # Get people in the image
        if result.people is not None:
            print("\nPeople in image:")
        
            # Prepare image for drawing
            image = Image.open(image_file)
            fig = plt.figure(figsize=(image.width/100, image.height/100))
            plt.axis('off')
            draw = ImageDraw.Draw(image)
            color = 'cyan'
        
            for detected_people in result.people:
                # Draw object bounding box if confidence > 50%
                if detected_people.confidence > 0.5:
                    # Draw object bounding box
                    r = detected_people.bounding_box
                    bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
                    draw.rectangle(bounding_box, outline=color, width=3)
            
                    # Return the confidence of the person detected
                    print(" {} (confidence: {:.2f}%)".format(detected_people.bounding_box, detected_people.confidence * 100))
                    
            # Save annotated image
            plt.imshow(image)
            plt.tight_layout(pad=0)
            outputfile = 'detected_people.jpg'
            fig.savefig(outputfile)
            print('  Results saved in', outputfile)
    
    else:
        error_details = sdk.ImageAnalysisErrorDetails.from_result(result)
        print(" Analysis failed.")
        print("   Error reason: {}".format(error_details.reason))
        print("   Error code: {}".format(error_details.error_code))
        print("   Error message: {}".format(error_details.message))
    ```

5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **computer-vision** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python detect-people.py
    ```

6. Beobachten Sie die Ausgabe, die die Anzahl der erkannten Gesichter anzeigen sollte.
7. Zeigen Sie die Datei **detected_people.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen. In diesem Fall hat Ihr Code die Attribute des Gesichts verwendet, um die Position der linken oberen Ecke des Rahmens zu kennzeichnen, und die Koordinaten des Begrenzungsrahmens, um ein Rechteck um jedes Gesicht zu zeichnen.

## Vorbereiten der Verwendung des SDK für die Gesichtserkennung

Während der **Azure KI Vision**-Dienst eine grundlegende Gesichtserkennung bietet (zusammen mit vielen anderen Bildanalysefunktionen), bietet der **Gesichtserkennungs**-Dienst umfassendere Funktionen zur Gesichtsanalyse und -erkennung.

1. Wechseln Sie in Visual Studio Code im Fensterbereich **Explorer** zum Ordner **04-face**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **face-api**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für die Gesichtserkennung, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.8.0-preview.3
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-face==0.6.0
    ```
    
3. Zeigen Sie den Inhalt des Ordners **face-api** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

4. Öffnen Sie die Konfigurationsdatei und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um den **Endpunkt** und einen **Schlüssel** für die Authentifizierung Ihrer Azure KI Services-Ressource hinzuzufügen. Speichern Sie die Änderungen.

5. Beachten Sie, dass der Ordner **face-api** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: analyze-faces.py

6. Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um die Namespaces zu importieren, die Sie für die Verwendung des Vision SDK benötigen:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.Azure.CognitiveServices.Vision.Face;
    using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.cognitiveservices.vision.face import FaceClient
    from azure.cognitiveservices.vision.face.models import FaceAttributeType
    from msrest.authentication import CognitiveServicesCredentials
    ```

7. Beachten Sie in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Face client** (Gesichtserkennungsclient authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein **FaceClient**-Objekt zu erstellen und zu authentifizieren:

    **C#**

    ```C#
    // Authenticate Face client
    ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
    faceClient = new FaceClient(credentials)
    {
        Endpoint = cogSvcEndpoint
    };
    ```

    **Python**

    ```Python
    # Authenticate Face client
    credentials = CognitiveServicesCredentials(cog_key)
    face_client = FaceClient(cog_endpoint, credentials)
    ```

8. Beachten Sie in der Funktion **Main** unter dem Code, den Sie soeben hinzugefügt haben, dass der Code ein Menü anzeigt, mit dem Sie Funktionen in Ihrem Code aufrufen können, um die Möglichkeiten des Diensts für die Gesichtserkennung zu erkunden. Sie werden diese Funktionen im weiteren Verlauf dieser Übung implementieren.

## Erkennen und Analysieren von Gesichtern

Eine der grundlegendsten Fähigkeiten des Gesichtsdiensts ist die Erkennung von Gesichtern in einem Bild und die Bestimmung ihrer Attribute, wie Kopfhaltung, Verschwommenheitsgrad, Vorhandensein einer Brille usw.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **1** auswählt. Dieser Code ruft die Funktion **DetectFaces** auf und übergibt den Pfad zu einer Bilddatei.
2. Suchen Sie die Funktion **DetectFaces** in der Codedatei, und fügen Sie unter dem Kommentar **Specify facial features to be retrieved** (Abzurufende Gesichtsmerkmale angeben) den folgenden Code ein:

    **C#**

    ```C#
    // Specify facial features to be retrieved
    IList<FaceAttributeType> features = new FaceAttributeType[]
    {
        FaceAttributeType.Occlusion,
        FaceAttributeType.Blur,
        FaceAttributeType.Glasses
    };
    ```

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeType.occlusion,
                FaceAttributeType.blur,
                FaceAttributeType.glasses]
    ```

3. Suchen Sie in der Funktion **DetectFaces** unter dem soeben hinzugefügten Code den Kommentar **Get faces** (Gesichter abrufen), und fügen Sie den folgenden Code hinzu:

**C#**

```C
// Get faces
using (var imageData = File.OpenRead(imageFile))
{    
    var detected_faces = await faceClient.Face.DetectWithStreamAsync(imageData, returnFaceAttributes: features, returnFaceId: false);

    if (detected_faces.Count() > 0)
    {
        Console.WriteLine($"{detected_faces.Count()} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.White);
        int faceCount=0;

        // Draw and annotate each face
        foreach (var face in detected_faces)
        {
            faceCount++;
            Console.WriteLine($"\nFace number {faceCount}");
            
            // Get face properties
            Console.WriteLine($" - Mouth Occluded: {face.FaceAttributes.Occlusion.MouthOccluded}");
            Console.WriteLine($" - Eye Occluded: {face.FaceAttributes.Occlusion.EyeOccluded}");
            Console.WriteLine($" - Blur: {face.FaceAttributes.Blur.BlurLevel}");
            Console.WriteLine($" - Glasses: {face.FaceAttributes.Glasses}");

            // Draw and annotate face
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Face number {faceCount}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Get faces
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data,
                                                            return_face_attributes=features,                     return_face_id=False)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'
        face_count = 0

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            face_count += 1
            print('\nFace number {}'.format(face_count))

            detected_attributes = face.face_attributes.as_dict()
            if 'blur' in detected_attributes:
                print(' - Blur:')
                for blur_name in detected_attributes['blur']:
                    print('   - {}: {}'.format(blur_name, detected_attributes['blur'][blur_name]))
                    
            if 'occlusion' in detected_attributes:
                print(' - Occlusion:')
                for occlusion_name in detected_attributes['occlusion']:
                    print('   - {}: {}'.format(occlusion_name, detected_attributes['occlusion'][occlusion_name]))

            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face number {}'.format(face_count)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```

4. Untersuchen Sie den Code, den Sie der **DetectFaces**-Funktion hinzugefügt haben. Sie analysiert eine Bilddatei und erkennt alle darin enthaltenen Gesichter, einschließlich der Attribute für Verdeckung, Unschärfe und das Vorhandensein von Brillen. Die Details der einzelnen Gesichter werden angezeigt, einschließlich eines eindeutigen Gesichtsbezeichners, der jedem Gesicht zugewiesen wird, und die Position der Gesichter wird auf dem Bild mithilfe eines Begrenzungsrahmens angezeigt.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die jetzt den Operator **await** verwenden. Sie können diese ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die die ID und die Attribute jedes erkannten Gesichts enthalten sollte.
7. Zeigen Sie die Datei **detected_faces.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen.

## Weitere Informationen

Es gibt mehrere zusätzliche Features im Dienst **Gesichtserkennung**, aber nach dem [Standard für verantwortungsvolle KI](https://aka.ms/aah91ff) gilt hier eine Richtlinie für eingeschränkten Zugriff. Diese Features umfassen die Identifizierung, Überprüfung und Erstellung von Gesichtserkennungsmodellen. Weitere Informationen und die Möglichkeit, den Zugriff zu beantragen, finden Sie unter [Eingeschränkter Zugriff für Azure KI Services](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access).

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts für die Gesichtserkennung finden Sie in der [Dokumentation zu Azure KI Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

Weitere Informationen zum Dienst für die **Gesichtserkennung** finden Sie in der [Dokumentation zur Gesichtserkennung](https://docs.microsoft.com/azure/cognitive-services/face/).
