---
lab:
  title: Lesen von Text in Bildern
  module: Module 11 - Reading Text in Images and Documents
---

# Lesen von Text in Bildern

Die optische Zeichenerkennung (OCR) ist eine Teilmenge des maschinellen Sehens, die sich mit dem Lesen von Text in Bildern und Dokumenten beschäftigt. Der Dienst **Azure KI Vision** bietet eine API zum Lesen von Text, die Sie in dieser Übung kennenlernen werden.

## Klonen des Repositorys für diesen Kurs

Wenn Sie das noch nicht erledigt haben, müssen Sie das Coderepository für diesen Kurs klonen:

1. Starten Sie Visual Studio Code.
2. Öffnen Sie die Palette (UMSCHALT+STRG+P), und führen Sie einen **Git: Clone**-Befehl aus, um das Repository `https://github.com/MicrosoftLearning/mslearn-ai-vision` in einen lokalen Ordner zu klonen (der Ordner ist beliebig).
3. Nachdem das Repository geklont wurde, öffnen Sie den Ordner in Visual Studio Code.
4. Warten Sie, während zusätzliche Dateien zur Unterstützung der C#-Codeprojekte im Repository installiert werden.

    > **Hinweis**: Wenn Sie aufgefordert werden, erforderliche Ressourcen zum Erstellen und Debuggen hinzuzufügen, wählen Sie **Not now** (Jetzt nicht) aus. Wenn die Meldung *Azure-Funktionsprojekt im Ordner erkannt* angezeigt wird, können Sie diese Nachricht sicher schließen.

## Bereitstellen einer Azure KI Services-Ressource

Wenn Sie noch keine in Ihrem Abonnement haben, müssen Sie eine **Azure KI Services**-Ressource bereitstellen.

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.
2. Suchen Sie in der oberen Suchleiste nach *Azure KI Services*, wählen Sie **Azure KI Services** aus und erstellen Sie eine Azure KI Services Multi-Service-Kontoressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine (wenn Sie ein eingeschränktes Abonnement verwenden, sind Sie möglicherweise nicht berechtigt, eine neue Ressourcengruppe zu erstellen. Verwenden Sie dann die bereitgestellte Gruppe.)*
    - **Region**: *Wählen Sie aus „USA, Osten“, „Frankreich, Mitte“, „Südkorea, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Europa, Westen“, „USA, Westen“ oder „Asien, Osten“\**
    - **Name**: *Geben Sie einen eindeutigen Namen ein.*
    - **Tarif**: Standard S0.

    \*Azure KI Vision 4.0-Features sind derzeit nur in diesen Regionen verfügbar.

3. Aktivieren Sie die erforderlichen Kontrollkästchen, und erstellen Sie die Ressource.
4. Warten Sie, bis die Bereitstellung abgeschlossen ist, und zeigen Sie dann die Bereitstellungsdetails an.
5. Wenn die Ressource bereitgestellt wurde, wechseln Sie zu ihr, und zeigen Sie ihre Seite **Schlüssel und Endpunkt** an. Sie benötigen den Endpunkt und einen der Schlüssel von dieser Seite im nächsten Verfahren.

## Vorbereiten der Verwendung des Azure KI Vision SDK

In dieser Übung schließen Sie eine teilweise implementierte Clientanwendung ab, die das Azure KI Vision SDK nutzt, um Text zu lesen.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die entsprechenden Aktionen für Ihre bevorzugte Sprache aus.

1. Wechseln Sie in Visual Studio Code im Fensterbereich **Explorer** zum Ordner **Labfiles\05-ocr**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **read-text**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Vision SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**
    
    ```
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 1.0.0-beta.3
    ```

    > **Hinweis**: Wenn Sie aufgefordert werden, Dev Kit-Erweiterungen zu installieren, können Sie die Meldung sicher schließen.

    **Python**
    
    ```
    pip install azure-ai-vision-imageanalysis==1.0.0b3
    ```

3. Zeigen Sie den Inhalt des Ordners **read-text** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:

    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um den **Endpunkt** und einen **Schlüssel** für die Authentifizierung Ihrer Azure KI Services-Ressource hinzuzufügen. Speichern Sie die Änderungen.


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
    ```
    
    **Python**
    
    ```Python
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
    ```

2. Beachten Sie in der Codedatei für Ihre Clientanwendung in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Azure KI Vision-Client authentifizieren**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Azure KI Vision-Clientobjekt zu erstellen und zu authentifizieren:

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

3. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und den Bildpfad dann an eine Funktion namens **GetTextRead** weitergibt. Diese Funktion ist noch nicht vollständig implementiert.

4. Wir fügen Code zum Textkörper der Funktion **GetTextRead** hinzu. Suchen Sie den Kommentar **Verwenden der Funktion „Bild analysieren“ zum Lesen von Text im Bild**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu. Die visuellen Features werden beim Aufrufen der `Analyze`-Funktion angegeben:

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
    
        // Prepare image for drawing
        System.Drawing.Image image = System.Drawing.Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.Cyan, 3);
        
        foreach (var line in result.Read.Blocks.SelectMany(block => block.Lines))
        {
            // Return the text detected in the image
    
    
        }
            
        // Save image
        String output_file = "text.jpg";
        image.Save(output_file);
        Console.WriteLine("\nResults saved in " + output_file + "\n");   
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

5. Fügen Sie im Code, den Sie soeben zur Funktion **GetTextRead** hinzugefügt haben, unter dem Kommentar **Return the text detected in the image** den folgenden Code hinzu (dieser Code gibt den Bildtext in der Konsole aus und generiert das Bild **text.jpg**, das den Bildtext hervorhebt):

    **C#**
    
    ```C#
    // Return the text detected in the image
    Console.WriteLine($"   '{line.Text}'");
    
    // Draw bounding box around line
    var drawLinePolygon = true;
    
    // Return the position bounding box around each line
    
    
    
    // Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    
    
    
    // Draw line bounding polygon
    if (drawLinePolygon)
    {
        var r = line.BoundingPolygon;
    
        Point[] polygonPoints = {
            new Point(r[0].X, r[0].Y),
            new Point(r[1].X, r[1].Y),
            new Point(r[2].X, r[2].Y),
            new Point(r[3].X, r[3].Y)
        };
    
        graphics.DrawPolygon(pen, polygonPoints);
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

6. Wählen Sie im Ordner **read-text/images** das Bild **Lincoln.jpg** aus, um die Datei anzuzeigen, die Ihr Code verarbeiten wird.

7. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **1** auswählt. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zur Bilddatei *Lincoln.jpg*.

8. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **read-text** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

9. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die der aus dem Bild extrahierte Text ist.

10. Wählen Sie im Ordner **read-text** das Bild **text.jpg** aus, und beachten Sie, wie sich um jede *Textzeile* herum ein Polygon befindet.

11. Kehren Sie zur Codedatei in Visual Studio Code zurück, und suchen Sie den Kommentar **Return the position bounding box around each line**. Fügen Sie dann unter diesem Kommentar den folgenden Code ein:

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

12. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **read-text** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

13. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die jede Textzeile im Bild mit ihrer jeweiligen Position im Bild umfassen sollte.


14. Kehren Sie zur Codedatei in Visual Studio Code zurück, und suchen Sie den Kommentar **Rückgabe jedes im Bild erkannten Wortes und des Positionsrahmens um jedes Wort mit der Konfidenzstufe für jedes Wort**. Fügen Sie dann unter diesem Kommentar den folgenden Code ein:

    **C#**
    
    ```C#
    // Return each word detected in the image and the position bounding box around each word with the confidence level of each word
    foreach (DetectedTextWord word in line.Words)
    {
        Console.WriteLine($"     Word: '{word.Text}', Confidence {word.Confidence:F4}, Bounding Polygon: [{string.Join(" ", word.BoundingPolygon)}]");
        
        // Draw word bounding polygon
        drawLinePolygon = false;
        var r = word.BoundingPolygon;
    
        Point[] polygonPoints = {
            new Point(r[0].X, r[0].Y),
            new Point(r[1].X, r[1].Y),
            new Point(r[2].X, r[2].Y),
            new Point(r[3].X, r[3].Y)
        };
    
        graphics.DrawPolygon(pen, polygonPoints);
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

15. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **read-text** zurück, und geben Sie den folgenden Befehl ein, um das Programm auszuführen:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

16. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die jedes Wort des Texts im Bild mit ihrer jeweiligen Position im Bild umfassen sollte. Beachten Sie, dass auch die Konfidenzstufe für jedes Wort zurückgegeben wird.

17. Wählen Sie im Ordner **read-text** das Bild **text.jpg** aus, und beachten Sie, dass sich um jedes *Wort* herum ein Polygon befindet.

## Verwenden des Azure KI Vision SDK zum Lesen handschriftlicher Texte in einem Bild

In der vorherigen Übung haben Sie klar definierten Text in einem Bild gelesen. Aber manchmal möchten Sie auch Text aus handschriftlichen Notizen oder Papieren lesen. Die gute Nachricht ist, dass das **Azure KI Vision SDK** auch handschriftlichen Text mit genau demselben Code lesen kann, den Sie zum Lesen von klar definiertem Text verwendet haben. Wir verwenden den gleichen Code aus der vorherigen Übung, aber diesmal verwenden wir ein anderes Bild.

1. Wählen Sie im Ordner **read-text/images** das Bild **Note.jpg**, um die Datei anzuzeigen, die Ihr Code verarbeiten wird.

2. Untersuchen Sie in der Codedatei Ihrer Anwendung in der Funktion **Main** den Code, der ausgeführt wird, wenn der Benutzer die Menüoption **2** auswählt. Dieser Code ruft die Funktion **GetTextRead** auf und übergibt den Pfad zur Bilddatei *Note.jpg*.

3. Geben Sie im integrierten Terminal für den Ordner **read-text** folgenden Befehl zur Ausführung des Programms ein:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python read-text.py
    ```

4. Wenn Sie dazu aufgefordert werden, geben Sie **1** ein, und beobachten Sie die Ausgabe, die der aus dem Bild extrahierte Text ist.

5. Wählen Sie im Ordner **read-text** das Bild **text.jpg** aus, und beachten Sie, dass sich um jedes *Wort* der Notiz ein Polygon befindet.

## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Suchen Sie in der oberen Suchleiste nach *Azure KI Services Multi-Service-Konto*, und wählen Sie die Azure KI Services Multi-Service-Kontoressource aus, die Sie in diesem Lab erstellt haben.

3. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts zum Lesen von Text finden Sie in der [Dokumentation zu Azure KI Vision](https://learn.microsoft.com/azure/ai-services/computer-vision/concept-ocr).
