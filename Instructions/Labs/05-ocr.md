---
lab:
  title: Lesen von Text in Bildern
  module: Module 11 - Reading Text in Images and Documents
---

# Lesen von Text in Bildern

Die optische Zeichenerkennung (OCR) ist eine Teilmenge des maschinellen Sehens, die sich mit dem Lesen von Text in Bildern und Dokumenten beschäftigt. Der Dienst **Azure KI Vision** bietet eine API zum Lesen von Text, die Sie in dieser Übung kennenlernen werden.

## Bereitstellen einer Ressource für maschinelles Sehen

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine Ressource **Maschinelles Sehen** bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
1. Wählen Sie **Ressource erstellen** aus.
1. Suchen Sie in der Suchleiste nach *Maschinelles Sehen*, wählen Sie **Maschinelles Sehen** aus und erstellen Sie die Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie aus „USA, Osten“, „USA, Westen“, „Frankreich, Mitte“, „Südkorea, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Europa, Westen“, oder „Asien, Osten“\**
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Free F0

    \*Azure AI Vision 4.0 mit vollem Funktionsumfang ist derzeit nur in diesen Regionen verfügbar.

1. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
1. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

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
   cd mslearn-ai-vision/Labfiles/05-ocr
    ```

## Vorbereiten der Verwendung des Azure KI Vision SDK

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Azure KI Vision SDK nutzt, um Text zu lesen.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Navigieren Sie zu dem Ordner, der die Anwendungscodedateien für Ihre bevorzugte Sprache enthält:  

    **C#**

    ```
   cd C-Sharp/read-text
    ```
    
    **Python**

    ```
   cd Python/read-text
    ```

1. Installieren Sie dann das Azure KI Vision SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**
    
    ```
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 1.0.0
    dotnet add package SkiaSharp --version 3.116.1
    dotnet add package SkiaSharp.NativeAssets.Linux --version 3.116.1
    ``` 

    **Python**
    
    ```
    pip install azure-ai-vision-imageanalysis==1.0.0
    pip install dotenv
    pip install matplotlib
    ```

1. Mit dem Befehl `ls` können Sie den Inhalt des Ordners **computer-vision** anzeigen. Beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:

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

1. Aktualisieren Sie in der Codedatei die darin enthaltenen Konfigurationswerte, um den **Endpunkt** und einen **Authentifizierungsschlüssel** für Ihre Ressource „Maschinelles Sehen“ widerzuspiegeln.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

## Verwenden des Azure KI Vision SDK zum Lesen von Text aus einem Bild

Eines der Features des **Azure KI Vision SDK** besteht darin, Text aus einem Bild zu lesen. In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Azure KI Vision SDK nutzt, um Text in einem Bild zu lesen.

1. Beachten Sie, dass der Ordner **read-text** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: read-text.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Vision SDK benötigen:

    **C#**
    
    ```C#
    // Import namespaces
    using Azure.AI.Vision.ImageAnalysis;
    using SkiaSharp;
    ```
    
    **Python**
    
    ```Python
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
    ```

1. Beachten Sie in der Codedatei für Ihre Clientanwendung in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Azure KI Vision-Client authentifizieren**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Azure KI Vision-Clientobjekt zu erstellen und zu authentifizieren:

    **C#**
    
    ```C#
    // Authenticate Azure AI Vision client
    ImageAnalysisClient client = new ImageAnalysisClient(
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

1. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und den Bildpfad dann an eine Funktion namens **GetTextRead** weitergibt. Diese Funktion ist noch nicht vollständig implementiert.

1. Wir fügen Code zum Textkörper der Funktion **GetTextRead** hinzu. Suchen Sie den Kommentar **Verwenden der Funktion „Bild analysieren“ zum Lesen von Text im Bild**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu. Die visuellen Features werden beim Aufrufen der `Analyze`-Funktion angegeben:

    **C#**

    ```C#
    // Use Analyze image function to read text in image
    ImageAnalysisResult result = client.Analyze(
        BinaryData.FromStream(stream),
        // Specify the features to be retrieved
        VisualFeatures.Read);
    
    stream.Close();
    
    // Display analysis results
    if (result.Read != null)
    {
        Console.WriteLine($"Text:");
    
        // Load the image using SkiaSharp
        using SKBitmap bitmap = SKBitmap.Decode(imageFile);
        // Create canvas to draw on the bitmap
        using SKCanvas canvas = new SKCanvas(bitmap);

        // Create paint for drawing polygons (bounding boxes)
        SKPaint paint = new SKPaint
        {
            Color = SKColors.Cyan,
            StrokeWidth = 3,
            Style = SKPaintStyle.Stroke,
            IsAntialias = true
        };

        foreach (var line in result.Read.Blocks.SelectMany(block => block.Lines))
        {
            // Return the text detected in the image
    
    
        }
            
        // Save the annotated image using SkiaSharp
        using (SKFileWStream output = new SKFileWStream("text.jpg"))
        {
            // Encode the bitmap into JPEG format with full quality (100)
            bitmap.Encode(output, SKEncodedImageFormat.Jpeg, 100);
        }

        Console.WriteLine("\nResults saved in text.jpg\n");
    }
    ```
    
    **Python**
    
    ```Python
    # Use Analyze image function to read text in image
    result = cv_client.analyze(
        image_data=image_data,
        visual_features=[VisualFeatures.READ]
    )

    # Display the image and overlay it with the extracted text
    if result.read is not None:
        print("\nText:")

        # Prepare image for drawing
        image = Image.open(image_file)
        fig = plt.figure(figsize=(image.width/100, image.height/100))
        plt.axis('off')
        draw = ImageDraw.Draw(image)
        color = 'cyan'

        for line in result.read.blocks[0].lines:
            # Return the text detected in the image

            
        # Save image
        plt.imshow(image)
        plt.tight_layout(pad=0)
        outputfile = 'text.jpg'
        fig.savefig(outputfile)
        print('\n  Results saved in', outputfile)
    ```

1. Fügen Sie im Code, den Sie soeben zur Funktion **GetTextRead** hinzugefügt haben, unter dem Kommentar **Return the text detected in the image** den folgenden Code hinzu (dieser Code gibt den Bildtext in der Konsole aus und generiert das Bild **text.jpg**, das den Bildtext hervorhebt):

    **C#**
    
    ```C#
    // Return the text detected in the image
    Console.WriteLine($"   '{line.Text}'");
    
    // Draw bounding box around line
    bool drawLinePolygon = true;
    
    // Return the position bounding box around each line
    
    
    
    // Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    
    
    
    // Draw line bounding polygon
    if (drawLinePolygon)
    {
        var r = line.BoundingPolygon;
        SKPoint[] polygonPoints = new SKPoint[]
        {
            new SKPoint(r[0].X, r[0].Y),
            new SKPoint(r[1].X, r[1].Y),
            new SKPoint(r[2].X, r[2].Y),
            new SKPoint(r[3].X, r[3].Y)
        };

    DrawPolygon(canvas, polygonPoints, paint);
    }
    ```
    
    **Python**
    
    ```Python
    # Return the text detected in the image
    print(f"  {line.text}")    
    
    drawLinePolygon = True
    
    r = line.bounding_polygon
    bounding_polygon = ((r[0].x, r[0].y),(r[1].x, r[1].y),(r[2].x, r[2].y),(r[3].x, r[3].y))
    
    # Return the position bounding box around each line
    
    
    # Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    
    
    # Draw line bounding polygon
    if drawLinePolygon:
        draw.polygon(bounding_polygon, outline=color, width=3)
    ```

1. Nur für die C#-Programmdatei ist eine Hilfsfunktion erforderlich, um ein Polygon zu zeichnen. Fügen Sie unter dem Kommentar **Helper method to draw a polygon given an array of SKPoints** folgenden Code hinzu:

    **C#**
   
    ```C#
    // Helper method to draw a polygon given an array of SKPoints
    static void DrawPolygon(SKCanvas canvas, SKPoint[] points, SKPaint paint)
    {
        if (points == null || points.Length == 0)
            return;

        using (var path = new SKPath())
        {
            path.MoveTo(points[0]);
            for (int i = 1; i < points.Length; i++)
            {
                path.LineTo(points[i]);
            }
            path.Close();
            canvas.DrawPath(path, paint);
        }
    }
    ```

1. Überprüfen Sie in der **Main**-Funktion den Code, der ausgeführt wird, wenn Benutzende die Menüoption **1** auswählen. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zur Bilddatei *Lincoln.jpg*.

1. Speichern Sie Ihre Änderungen und schließen Sie den Code-Editor.

1. Wählen Sie auf der Symbolleiste der Cloud Shell **Dateien hochladen/herunterladen** und dann **Herunterladen** aus. Geben Sie im neuen Dialogfeld den folgenden Dateipfad ein und wählen Sie **Herunterladen** aus:

    **C#**
   
    ```
    mslearn-ai-vision/Labfiles/05-ocr/C-Sharp/read-text/images/Lincoln.jpg
    ```

    **Python**

    ```
    mslearn-ai-vision/Labfiles/05-ocr/Python/read-text/images/Lincoln.jpg
    ```
       
1. Öffnen Sie das Bild **Lincoln.jpg**, um es anzuzeigen.

1. Geben Sie nach dem Anzeigen des Bilds, das Ihr Code verarbeitet, den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die der aus dem Bild extrahierte Text ist.

1. Im Ordner **read-text** wurde das Bild **text.jpg** erstellt. Sie können es mithilfe des Dateipfads `mslearn-ai-vision/Labfiles/05-ocr/***C-Sharp or Python***/read-text/text.jpg` herunterladen und sehen, wie sich um jede *Textzeile* herum ein Polygon befindet.

1. Öffnen Sie die Codedatei erneut und suchen Sie den Kommentar **Return the position bounding box around each line**. Fügen Sie dann unter diesem Kommentar den folgenden Code ein:

    **C#**
    
    ```C#
    // Return the position bounding box around each line
    Console.WriteLine($"   Bounding Polygon: [{string.Join(" ", line.BoundingPolygon)}]");
    ```
    
    **Python**
    
    ```Python
    # Return the position bounding box around each line
    print("   Bounding Polygon: {}".format(bounding_polygon))
    ```

1. Speichern Sie Ihre Änderungen und schließen Sie den Code-Editor. Geben Sie dann den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die jede Textzeile im Bild mit ihrer jeweiligen Position im Bild umfassen sollte.

1. Öffnen Sie die Codedatei erneut und suchen Sie den Kommentar **Return each word detected in the image and the position bounding box around each word with the confidence level of each word**. Fügen Sie dann unter diesem Kommentar den folgenden Code ein:

    **C#**
    
    ```C#
    // Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    foreach (DetectedTextWord word in line.Words)
    {
        Console.WriteLine($"     Word: '{word.Text}', Confidence {word.Confidence:F4}, Bounding Polygon: [{string.Join(" ", word.BoundingPolygon)}]");
        
        // Draw word bounding polygon
        drawLinePolygon = false;
        var r = word.BoundingPolygon;
    
        // Convert the bounding polygon into an array of SKPoints    
        SKPoint[] polygonPoints = new SKPoint[]
        {
            new SKPoint(r[0].X, r[0].Y),
            new SKPoint(r[1].X, r[1].Y),
            new SKPoint(r[2].X, r[2].Y),
            new SKPoint(r[3].X, r[3].Y)
        };

        // Draw the word polygon on the canvas
        DrawPolygon(canvas, polygonPoints, paint);
    }
    ```
    
    **Python**
    
    ```Python
    # Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    for word in line.words:
        r = word.bounding_polygon
        bounding_polygon = ((r[0].x, r[0].y),(r[1].x, r[1].y),(r[2].x, r[2].y),(r[3].x, r[3].y))
        print(f"    Word: '{word.text}', Bounding Polygon: {bounding_polygon}, Confidence: {word.confidence:.4f}")
    
        # Draw word bounding polygon
        drawLinePolygon = False
        draw.polygon(bounding_polygon, outline=color, width=3)
    ```

1. Speichern Sie Ihre Änderungen und schließen Sie den Code-Editor. Geben Sie dann den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die jedes Wort des Texts im Bild mit ihrer jeweiligen Position im Bild umfassen sollte. Beachten Sie, dass auch die Konfidenzstufe für jedes Wort zurückgegeben wird.

1. Laden Sie das Bild **text.jpg** erneut herunter und sehen Sie, wie sich um jedes *Wort* herum ein Polygon befindet.

## Verwenden des Azure KI Vision SDK zum Lesen handschriftlicher Texte in einem Bild

In der vorherigen Übung haben Sie klar definierten Text in einem Bild gelesen. Aber manchmal möchten Sie auch Text aus handschriftlichen Notizen oder Papieren lesen. Die gute Nachricht ist, dass das **Azure KI Vision SDK** auch handschriftlichen Text mit genau demselben Code lesen kann, den Sie zum Lesen von klar definiertem Text verwendet haben. Wir verwenden den gleichen Code aus der vorherigen Übung, aber diesmal verwenden wir ein anderes Bild.

1. Laden Sie **Note.jpg** mithilfe des Dateipfads `mslearn-ai-vision/Labfiles/05-ocr/***C-Sharp or Python***/read-text/images/Note.jpg` herunter, um das nächste Bild anzuzeigen, das Ihr Code verarbeitet.

1. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **2** auswählt. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zur Bilddatei *Note.jpg*.

1. Geben Sie den folgenden Befehl im Terminal ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die der aus dem Bild extrahierte Text ist.

1. Laden Sie das Bild **text.jpg** erneut herunter und sehen Sie, wie sich um jedes *Wort* der Notiz herum ein Polygon befindet.

## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

1. Suchen Sie in der oberen Suchleiste nach *Maschinelles Sehen* und wählen Sie die Ressource „Maschinelles Sehen“ aus, die Sie in dieser Übung erstellt haben.

1. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts zum Lesen von Text finden Sie in der [Dokumentation zu Azure KI Vision](https://learn.microsoft.com/azure/ai-services/computer-vision/concept-ocr).
