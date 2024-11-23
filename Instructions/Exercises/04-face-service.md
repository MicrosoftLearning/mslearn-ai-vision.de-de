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
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 1.0.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-vision-imageanalysis==1.0.0b3
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
    // Import namespaces
    using Azure.AI.Vision.ImageAnalysis;
    ```

    **Python**

    ```Python
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
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
    ImageAnalysisClient cvClient = new ImageAnalysisClient(
        new Uri(aiSvcEndpoint),
        new AzureKeyCredential(aiSvcKey));
    ```

    **Python**

    ```Python
    # Authenticate Azure AI Vision client
    cv_client = ImageAnalysisClient(
        endpoint=ai_endpoint,
        credential=AzureKeyCredential(ai_key)
    )
    ```

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und den Bildpfad dann an eine Funktion namens **AnalyzeImage** weitergibt. Diese Funktion ist noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion zur **AnalyzeImage** unter dem Kommentar **Get result with specified features to be retrieved (PEOPLE)** abrufen den folgenden Code hinzu:

    **C#**

    ```C#
    // Get result with specified features to be retrieved (PEOPLE)
    ImageAnalysisResult result = client.Analyze(
        BinaryData.FromStream(stream),
        VisualFeatures.People);
    ```

    **Python**

    ```Python
    # Get result with specified features to be retrieved (PEOPLE)
    result = cv_client.analyze(
        image_data=image_data,
        visual_features=[
            VisualFeatures.PEOPLE],
    )
    ```

4. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Draw bounding box around detected people** den folgenden Code ein:

    **C#**

    ```C
    // Draw bounding box around detected people
    foreach (DetectedPerson person in result.People.Values)
    {
        if (person.Confidence > 0.5) 
        {
            // Draw object bounding box
            var r = person.BoundingBox;
            Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
        }

        // Return the confidence of the person detected
        //Console.WriteLine($"   Bounding box {person.BoundingBox.ToString()}, Confidence: {person.Confidence:F2}");
    }
    ```

    **Python**
    
    ```Python
    # Draw bounding box around detected people
    for detected_people in result.people.list:
        if(detected_people.confidence > 0.5):
            # Draw object bounding box
            r = detected_people.bounding_box
            bounding_box = ((r.x, r.y), (r.x + r.width, r.y + r.height))
            draw.rectangle(bounding_box, outline=color, width=3)

        # Return the confidence of the person detected
        #print(" {} (confidence: {:.2f}%)".format(detected_people.bounding_box, detected_people.confidence * 100))
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
7. Zeigen Sie die Datei **people.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen. In diesem Fall hat Ihr Code die Attribute des Gesichts verwendet, um die Position der linken oberen Ecke des Rahmens zu kennzeichnen, und die Koordinaten des Begrenzungsrahmens, um ein Rechteck um jedes Gesicht zu zeichnen.

Wenn Sie die Konfidenzbewertung aller Personen sehen möchten, die der Dienst erkannt hat, können Sie die Auskommentierung unter dem Kommentar `Return the confidence of the person detected` aufheben und den Code erneut ausführen.

## Vorbereiten der Verwendung des SDK für die Gesichtserkennung

Während der **Azure KI Vision**-Dienst eine grundlegende Gesichtserkennung bietet (zusammen mit vielen anderen Bildanalysefunktionen), bietet der **Gesichtserkennungs**-Dienst umfassendere Funktionen zur Gesichtsanalyse und -erkennung.

1. Wechseln Sie in Visual Studio Code im Fensterbereich **Explorer** zum Ordner **04-face**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **face-api**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das SDK-Paket für die Gesichtserkennung, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**

    ```
    dotnet add package Azure.AI.Vision.Face -v 1.0.0-beta.2
    ```

    **Python**

    ```
    pip install azure-ai-vision-face==1.0.0b2
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
    using Azure;
    using Azure.AI.Vision.Face;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.ai.vision.face import FaceClient
    from azure.ai.vision.face.models import FaceDetectionModel, FaceRecognitionModel, FaceAttributeTypeDetection03
    from azure.core.credentials import AzureKeyCredential
    ```

7. Beachten Sie in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Authenticate Face client** (Gesichtserkennungsclient authentifizieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein **FaceClient**-Objekt zu erstellen und zu authentifizieren:

    **C#**

    ```C#
    // Authenticate Face client
    faceClient = new FaceClient(
        new Uri(cogSvcEndpoint),
        new AzureKeyCredential(cogSvcKey));
    ```

    **Python**

    ```Python
    # Authenticate Face client
    face_client = FaceClient(
        endpoint=cog_endpoint,
        credential=AzureKeyCredential(cog_key)
    )
    ```

8. Beachten Sie in der Funktion **Main** unter dem Code, den Sie soeben hinzugefügt haben, dass der Code ein Menü anzeigt, mit dem Sie Funktionen in Ihrem Code aufrufen können, um die Möglichkeiten des Diensts für die Gesichtserkennung zu erkunden. Sie werden diese Funktionen im weiteren Verlauf dieser Übung implementieren.

## Erkennen und Analysieren von Gesichtern

Eine der grundlegendsten Fähigkeiten des Face Service ist die Erkennung von Gesichtern in einem Bild und die Bestimmung ihrer Attribute, wie Kopfhaltung, Verschwommenheitsgrad, Vorhandensein einer Maske usw.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **1** auswählt. Dieser Code ruft die Funktion **DetectFaces** auf und übergibt den Pfad zu einer Bilddatei.
2. Suchen Sie die Funktion **DetectFaces** in der Codedatei, und fügen Sie unter dem Kommentar **Specify facial features to be retrieved** (Abzurufende Gesichtsmerkmale angeben) den folgenden Code ein:

    **C#**

    ```C#
    // Specify facial features to be retrieved
    FaceAttributeType[] features = new FaceAttributeType[]
    {
        FaceAttributeType.Detection03.HeadPose,
        FaceAttributeType.Detection03.Blur,
        FaceAttributeType.Detection03.Mask
    };
    ```

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeTypeDetection03.HEAD_POSE,
                FaceAttributeTypeDetection03.BLUR,
                FaceAttributeTypeDetection03.MASK]
    ```

3. Suchen Sie in der Funktion **DetectFaces** unter dem soeben hinzugefügten Code den Kommentar **Get faces** (Gesichter abrufen), und fügen Sie den folgenden Code hinzu:

**C#**

```C#
// Get faces
using (var imageData = File.OpenRead(imageFile))
{    
    var response = await faceClient.DetectAsync(
        BinaryData.FromStream(imageData),
        FaceDetectionModel.Detection03,
        FaceRecognitionModel.Recognition04,
        returnFaceId: false,
        returnFaceAttributes: features);
    IReadOnlyList<FaceDetectionResult> detected_faces = response.Value;

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
            Console.WriteLine($" - Head Pose (Yaw): {face.FaceAttributes.HeadPose.Yaw}");
            Console.WriteLine($" - Head Pose (Pitch): {face.FaceAttributes.HeadPose.Pitch}");
            Console.WriteLine($" - Head Pose (Roll): {face.FaceAttributes.HeadPose.Roll}");
            Console.WriteLine($" - Blur: {face.FaceAttributes.Blur.BlurLevel}");
            Console.WriteLine($" - Mask: {face.FaceAttributes.Mask.Type}");

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
    detected_faces = face_client.detect(
        image_content=image_data.read(),
        detection_model=FaceDetectionModel.DETECTION03,
        recognition_model=FaceRecognitionModel.RECOGNITION04,
        return_face_id=False,
        return_face_attributes=features,
    )

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

            print(' - Head Pose (Yaw): {}'.format(face.face_attributes.head_pose.yaw))
            print(' - Head Pose (Pitch): {}'.format(face.face_attributes.head_pose.pitch))
            print(' - Head Pose (Roll): {}'.format(face.face_attributes.head_pose.roll))
            print(' - Blur: {}'.format(face.face_attributes.blur.blur_level))
            print(' - Mask: {}'.format(face.face_attributes.mask.type))

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

4. Untersuchen Sie den Code, den Sie der **DetectFaces**-Funktion hinzugefügt haben. Sie analysiert eine Bilddatei und erkennt alle darin enthaltenen Gesichter, einschließlich der Attribute für Kopfhaltung, Unschärfe und das Vorhandensein einer Maske. Die Details der einzelnen Gesichter werden angezeigt, einschließlich eines eindeutigen Gesichtsbezeichners, der jedem Gesicht zugewiesen wird, und die Position der Gesichter wird auf dem Bild mithilfe eines Begrenzungsrahmens angezeigt.
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **face-api** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**

    ```
    dotnet run
    ```

    *Die C#-Ausgabe kann Warnungen zu asynchronen Funktionen anzeigen, die den Operator **await** nicht verwenden. Diese können Sie ignorieren.*

    **Python**

    ```
    python analyze-faces.py
    ```

6. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die die ID und die Attribute jedes erkannten Gesichts enthalten sollte.
7. Zeigen Sie die Datei **detected_faces.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Gesichter zu sehen.

## Weitere Informationen

Es gibt mehrere zusätzliche Features im Dienst **Gesichtserkennung**, aber nach dem [Standard für verantwortungsvolle KI](https://aka.ms/aah91ff) gilt hier eine Richtlinie für eingeschränkten Zugriff. Diese Features umfassen die Identifizierung, Überprüfung und Erstellung von Gesichtserkennungsmodellen. Weitere Informationen und die Möglichkeit, den Zugriff zu beantragen, finden Sie unter [Eingeschränkter Zugriff für Azure KI Services](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access).

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts für die Gesichtserkennung finden Sie in der [Dokumentation zu Azure KI Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

Weitere Informationen zum Dienst für die **Gesichtserkennung** finden Sie in der [Dokumentation zur Gesichtserkennung](https://learn.microsoft.com/azure/ai-services/computer-vision/overview-identity).
