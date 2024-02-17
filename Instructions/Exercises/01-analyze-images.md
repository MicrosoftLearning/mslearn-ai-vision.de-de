---
lab:
  title: Analysieren von Bildern mit Azure KI Vision
  module: Module 2 - Develop computer vision solutions with Azure AI Vision
---

# Analysieren von Bildern mit Azure KI Vision

Azure KI Vision ist eine Funktion der künstlichen Intelligenz, die es Softwaresystemen ermöglicht, visuelle Eingaben durch die Analyse von Bildern zu interpretieren. In Microsoft Azure bietet der Azure KI-Dienst **Vision** vorgefertigte Modelle für gängige Aufgaben für maschinelles Sehen, darunter die Analyse von Bildern, um Beschriftungen und Tags vorzuschlagen, die Erkennung von gängigen Objekten und von Menschen. Sie können den Azure KI Vision-Dienst auch verwenden, um den Hintergrund zu entfernen oder eine Vordergrundmattierung von Bildern zu erstellen.

## Klonen des Repositorys für diesen Kurs

Wenn Sie das Coderepository **Azre KI Vision** noch nicht in die Umgebung geklont haben, in der Sie an diesem Lab arbeiten werden, führen Sie die folgenden Schritte aus, um dies zu tun. Öffnen Sie andernfalls den geklonten Ordner in Visual Studio Code.

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

In dieser Übung stellen Sie eine teilweise implementierte Clientanwendung fertig, die das Azure KI Vision SDK nutzt, um Bilder zu analysieren.

> **Hinweis**: Sie können auswählen, das SDK für **C#** oder **Python** zu verwenden. Führen Sie in den folgenden Schritten die Aktionen aus, die für Ihre bevorzugte Sprache geeignet sind.

1. Wechseln Sie in Visual Studio Code im Fensterbereich **Explorer** zum Ordner **Labfiles/01-analyze-images**, und erweitern Sie je nach Ihrer bevorzugten Sprache den Ordner **C-Sharp** oder **Python**.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **image-analysis**, und öffnen Sie ein integriertes Terminal. Installieren Sie dann das Azure KI Vision SDK-Paket, indem Sie den entsprechenden Befehl für Ihre bevorzugte Sprache ausführen:

    **C#**
    
    ```
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 0.15.1-beta.1
    ```

    > **Hinweis**: Wenn Sie aufgefordert werden, Dev Kit-Erweiterungen zu installieren, können Sie die Meldung sicher schließen.

    **Python**
    
    ```
    pip install azure-ai-vision==0.15.1b1
    ```
    
3. Zeigen Sie den Inhalt des Ordners **image-analysis** an, und beachten Sie, dass er eine Datei für Konfigurationseinstellungen enthält:
    - **C#** : appsettings.json
    - **Python**: .env

    Öffnen Sie die Konfigurationsdatei und aktualisieren Sie die darin enthaltenen Konfigurationswerte, um den **Endpunkt** und einen **Schlüssel** für die Authentifizierung Ihrer Azure KI Services-Ressource hinzuzufügen. Speichern Sie die Änderungen.
4. Beachten Sie, dass der Ordner **image-analysis** eine Codedatei für die Clientanwendung enthält:

    - **C#** : Program.cs
    - **Python**: image-analysis.py

    Öffnen Sie die Codedatei, und suchen Sie oben unter den vorhandenen Namespaceverweisen nach dem Kommentar **Import namespaces** (Namespaces importieren). Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code hinzu, um die Namespaces zu importieren, die Sie für die Verwendung des Azure KI Vision SDK benötigen:

**C#**

```C#
// Import namespaces
using Azure.AI.Vision.Common;
using Azure.AI.Vision.ImageAnalysis;
```

**Python**

```Python
# import namespaces
import azure.ai.vision as sdk
```
    
## Anzeigen der zu analysierenden Bilder

In dieser Übung verwenden Sie den Azure KI Vision-Dienst, um mehrere Bilder zu analysieren.

1. Erweitern Sie in Visual Studio Code den Ordner **image-analysis** und den darin enthaltenen Ordner **images**.
2. Wählen Sie nacheinander die einzelnen Bilddateien aus, um sie in Visual Studio Code anzuzeigen.

## Analysieren eines Bilds zum Vorschlagen einer Beschriftung

Nun können Sie das SDK verwenden, um den Vision-Dienst aufzurufen und ein Bild zu analysieren.

1. Beachten Sie in der Codedatei für Ihre Clientanwendung (**Program.cs** oder **image-analysis.py**) in der Funktion **Main**, dass der Code zum Laden der Konfigurationseinstellungen bereitgestellt wurde. Suchen Sie dann den Kommentar **Azure KI Vision-Client authentifizieren**. Fügen Sie dann unter diesem Kommentar den folgenden sprachspezifischen Code ein, um ein Azure KI Vision-Clientobjekt zu erstellen und zu authentifizieren:

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

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und dann den Bildpfad an zwei andere Funktionen (**AnalyzeImage** und **BackgroundForeground**) weitergibt. Diese Funktionen sind noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Specify features to be retrieved** (Abzurufende Features angeben) den folgenden Code ein:

**C#**

```C#
// Specify features to be retrieved
Features =
    ImageAnalysisFeature.Caption
    | ImageAnalysisFeature.DenseCaptions
    | ImageAnalysisFeature.Objects
    | ImageAnalysisFeature.People
    | ImageAnalysisFeature.Text
    | ImageAnalysisFeature.Tags
```

**Python**

```Python
# Specify features to be retrieved
analysis_options = sdk.ImageAnalysisOptions()

features = analysis_options.features = (
    sdk.ImageAnalysisFeature.CAPTION |
    sdk.ImageAnalysisFeature.DENSE_CAPTIONS |
    sdk.ImageAnalysisFeature.TAGS |
    sdk.ImageAnalysisFeature.OBJECTS |
    sdk.ImageAnalysisFeature.PEOPLE
)
```
    
4. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image analysis** (Bildanalyse abrufen) den folgenden Code ein (einschließlich der Kommentare, die darauf hinweisen, wo Sie später weiteren Code hinzufügen werden):

**C#**

```C#
// Get image analysis
using var imageSource = VisionSource.FromFile(imageFile);

using var analyzer = new ImageAnalyzer(serviceOptions, imageSource, analysisOptions);

var result = analyzer.Analyze();

if (result.Reason == ImageAnalysisResultReason.Analyzed)
{
    // get image captions
    if (result.Caption != null)
    {
        Console.WriteLine(" Caption:");
        Console.WriteLine($"   \"{result.Caption.Content}\", Confidence {result.Caption.Confidence:0.0000}");
    }

    //get image dense captions
    if (result.DenseCaptions != null)
    {
        Console.WriteLine(" Dense Captions:");
        foreach (var caption in result.DenseCaptions)
        {
            Console.WriteLine($"   \"{caption.Content}\", Confidence {caption.Confidence:0.0000}");
        }
        Console.WriteLine($"\n");
    }

    // Get image tags


    // Get objects in the image


    // Get people in the image

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
    # Get image captions
    if result.caption is not None:
        print("\nCaption:")
        print(" Caption: '{}' (confidence: {:.2f}%)".format(result.caption.content, result.caption.confidence * 100))

    # Get image dense captions
    if result.dense_captions is not None:
        print("\nDense Captions:")
        for caption in result.dense_captions:
            print(" Caption: '{}' (confidence: {:.2f}%)".format(caption.content, caption.confidence * 100))

    # Get image tags


    # Get objects in the image


    # Get people in the image


else:
    error_details = sdk.ImageAnalysisErrorDetails.from_result(result)
    print(" Analysis failed.")
    print("   Error reason: {}".format(error_details.reason))
    print("   Error code: {}".format(error_details.error_code))
    print("   Error message: {}".format(error_details.message))
```
    
5. Speichern Sie Ihre Änderungen, und kehren Sie zum integrierten Terminal in den Ordner **image-analysis** zurück, und geben Sie den folgenden Befehl ein, um das Programm mit dem Argument **images/street.jpg** auszuführen:

**C#**

```
dotnet run images/street.jpg
```

**Python**

```
python image-analysis.py images/street.jpg
```
    
6. Beobachten Sie die Ausgabe, die eine vorgeschlagene Beschriftung für das Bild **street.jpg** enthalten sollte.
7. Führen Sie das Programm erneut aus, diesmal mit dem Argument **images/building.jpg**, um die Bildunterschrift anzuzeigen, die für das Bild **building.jpg** generiert wird.
8. Wiederholen Sie den vorherigen Schritt, um eine Beschriftung für die Datei **images/person.jpg** zu erstellen.

## Abrufen vorgeschlagener Tags für ein Bild

Es kann manchmal hilfreich sein, relevante *Tags* zu identifizieren, die Hinweise auf den Inhalt eines Bilds geben.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get image tags** (Bildtags abrufen) den folgenden Code hinzu:

**C#**

```C#
// Get image tags
if (result.Tags != null)
{
    Console.WriteLine($" Tags:");
    foreach (var tag in result.Tags)
    {
        Console.WriteLine($"   \"{tag.Name}\", Confidence {tag.Confidence:0.0000}");
    }
    Console.WriteLine($"\n");
}
```

**Python**

```Python
# Get image tags
if result.tags is not None:
    print("\nTags:")
    for tag in result.tags:
        print(" Tag: '{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beachten Sie dabei, dass neben der Bildbeschriftung auch eine Liste mit vorgeschlagenen Tags angezeigt wird.

## Erkennen und Suchen von Objekten in einem Bild

Die *Objekterkennung* ist eine spezielle Form des maschinellen Sehens, bei der einzelne Objekte innerhalb eines Bilds identifiziert und ihre Position durch einen Begrenzungsrahmen angegeben werden.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get objects in the image** (Objekte im Bild abrufen) den folgenden Code hinzu:

**C#**

```C#
// Get objects in the image
if (result.Objects != null)
{
    Console.WriteLine(" Objects:");

    // Prepare image for drawing
    System.Drawing.Image image = System.Drawing.Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.WhiteSmoke);

    foreach (var detectedObject in result.Objects)
    {
        Console.WriteLine($"   \"{detectedObject.Name}\", Confidence {detectedObject.Confidence:0.0000}");

        // Draw object bounding box
        var r = detectedObject.BoundingBox;
        Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.Name,font,brush,r.X, r.Y);
    }
                    
    // Save annotated image
    String output_file = "objects.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file + "\n");   
}

```

**Python**

```Python
# Get objects in the image
if result.objects is not None:
    print("\nObjects in image:")

    # Prepare image for drawing
    image = Image.open(image_file)
    fig = plt.figure(figsize=(image.width/100, image.height/100))
    plt.axis('off')
    draw = ImageDraw.Draw(image)
    color = 'cyan'

    for detected_object in result.objects:
        # Print object name
        print(" {} (confidence: {:.2f}%)".format(detected_object.name, detected_object.confidence * 100))
        
        # Draw object bounding box
        r = detected_object.bounding_box
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.name,(r.x, r.y), backgroundcolor=color)

    # Save annotated image
    plt.imshow(image)
    plt.tight_layout(pad=0)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```

2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beobachten Sie dabei die erkannten Objekte. Zeigen Sie nach jedem Durchlauf die Datei **objects.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Objekte zu sehen.

## Erkennen und Suchen von Personen in einem Bild

Die *Personenerkennung* ist eine spezielle Form des maschinellen Sehens, bei der einzelne Personen innerhalb eines Bilds erkannt und ihre Positionen durch einen Begrenzungsrahmen angegeben werden.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Personen im Bild erfassen** den folgenden Code hinzu:

**C#**

```C#
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
        // Draw object bounding box
        var r = person.BoundingBox;
        Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);

        // Return the confidence of the person detected
        //Console.WriteLine($"   Bounding box {person.BoundingBox}, Confidence {person.Confidence:0.0000}");
    }

    // Save annotated image
    String output_file = "persons.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file + "\n");
}
```

**Python**

```Python
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
        # Draw object bounding box
        r = detected_people.bounding_box
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)

        # Return the confidence of the person detected
        #print(" {} (confidence: {:.2f}%)".format(detected_people.bounding_box, detected_people.confidence * 100))
        
    # Save annotated image
    plt.imshow(image)
    plt.tight_layout(pad=0)
    outputfile = 'people.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```

2. (Optional) Heben Sie die Auskommentierung des Befehls **Console.Writeline** unter dem Abschnitt **Rückgabe der Konfidenz der Personenerkennung** auf, um die zurückgegebene Konfidenzstufe, mit der eine Person an einer bestimmten Position des Bilds erkannt wurde, zu überprüfen.

3. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beobachten Sie dabei die erkannten Objekte. Zeigen Sie nach jedem Durchlauf die Datei **objects.jpg** an, die im selben Ordner wie Ihre Codedatei generiert wird, um die kommentierten Objekte zu sehen.

> **Hinweis**: In den vorangegangenen Aufgaben haben Sie eine einzelne Methode verwendet, um das Bild zu analysieren, und dann inkrementell Code hinzugefügt, um die Ergebnisse zu analysieren und anzuzeigen. Das SDK bietet auch individuelle Methoden für das Vorschlagen von Beschriftungen, das Identifizieren von Tags, das Erkennen von Objekten usw. – das bedeutet, dass Sie die am besten geeignete Methode verwenden können, um nur die benötigten Informationen zurückzugeben und so die Menge der zurückzugebenden Nutzdaten zu reduzieren. Weitere Informationen finden Sie in der [.NET SDK-Dokumentation](https://learn.microsoft.com/dotnet/api/overview/azure/cognitiveservices/computervision?view=azure-dotnet) oder in der [Python SDK-Dokumentation](https://learn.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision).

## Entfernen des Hintergrunds oder Generieren einer Vordergrundmattierung eines Bilds

In einigen Fällen müssen Sie möglicherweise den Hintergrund eines Bilds entfernen oder eine Vordergrundmattierung dieses Bilds erstellen. Beginnen wir mit dem Entfernen des Hintergrunds.

1. Suchen Sie in ihrer Codedatei die Funktion **BackgroundForeground** und fügen Sie unter dem Kommentar **Entfernen des Hintergrunds aus dem Bild oder Erzeugen eines matten Vordergrunds** den folgenden Code hinzu:

**C#**

```C#
// Remove the background from the image or generate a foreground matte
Console.WriteLine($"\nRemove the background from the image or generate a foreground matte");

using var imageSource = VisionSource.FromFile(imageFile);

var analysisOptions = new ImageAnalysisOptions()
{
    // Set the image analysis segmentation mode to background or foreground
    SegmentationMode = ImageSegmentationMode.BackgroundRemoval
};

using var analyzer = new ImageAnalyzer(serviceOptions, imageSource, analysisOptions);

var result = analyzer.Analyze();

// Remove the background or generate a foreground matte
if (result.Reason == ImageAnalysisResultReason.Analyzed)
{
    using var segmentationResult = result.SegmentationResult;

    var imageBuffer = segmentationResult.ImageBuffer;
    Console.WriteLine($"\n Segmentation result:");
    Console.WriteLine($"   Output image buffer size (bytes) = {imageBuffer.Length}");
    Console.WriteLine($"   Output image height = {segmentationResult.ImageHeight}");
    Console.WriteLine($"   Output image width = {segmentationResult.ImageWidth}");

    string outputImageFile = "newimage.jpg";
    using (var fs = new FileStream(outputImageFile, FileMode.Create))
    {
        fs.Write(imageBuffer.Span);
    }
    Console.WriteLine($"   File {outputImageFile} written to disk\n");
}
else
{
    var errorDetails = ImageAnalysisErrorDetails.FromResult(result);
    Console.WriteLine(" Analysis failed.");
    Console.WriteLine($"   Error reason : {errorDetails.Reason}");
    Console.WriteLine($"   Error code : {errorDetails.ErrorCode}");
    Console.WriteLine($"   Error message: {errorDetails.Message}");
    Console.WriteLine(" Did you set the computer vision endpoint and key?\n");
}
```

**Python**

```Python
# Remove the background from the image or generate a foreground matte
print('\nRemove the background from the image or generate a foreground matte')

image = sdk.VisionSource(image_file)

analysis_options = sdk.ImageAnalysisOptions()

# Set the image analysis segmentation mode to background or foreground
analysis_options.segmentation_mode = sdk.ImageSegmentationMode.BACKGROUND_REMOVAL
    
image_analyzer = sdk.ImageAnalyzer(cv_client, image, analysis_options)

result = image_analyzer.analyze()

if result.reason == sdk.ImageAnalysisResultReason.ANALYZED:

    image_buffer = result.segmentation_result.image_buffer
    print(" Segmentation result:")
    print("   Output image buffer size (bytes) = {}".format(len(image_buffer)))
    print("   Output image height = {}".format(result.segmentation_result.image_height))
    print("   Output image width = {}".format(result.segmentation_result.image_width))

    output_image_file = "newimage.jpg"
    with open(output_image_file, 'wb') as binary_file:
        binary_file.write(image_buffer)
    print("   File {} written to disk".format(output_image_file))

else:

    error_details = sdk.ImageAnalysisErrorDetails.from_result(result)
    print(" Analysis failed.")
    print("   Error reason: {}".format(error_details.reason))
    print("   Error code: {}".format(error_details.error_code))
    print("   Error message: {}".format(error_details.message))
    print(" Did you set the computer vision endpoint and key?")
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Öffnen Sie dabei die Datei **newimage.jpg**, die im selben Ordner wie Ihre Codedatei für jedes Bild erzeugt wird.  Beachten Sie, wie der Hintergrund aus den einzelnen Bildern entfernt wurde.

Wir generieren nun eine Vordergrundmattierung für unsere Bilder.

3. Suchen Sie in Ihrer Codedatei die Funktion **BackgroundForeground**. Ersetzen Sie unter dem Kommentar **Einstellen des Segmentierungsmodus für die Bildanalyse auf Hintergrund oder Vordergrund** den Code, den Sie zuvor hinzugefügt haben, durch den folgenden Code:

**C#**

```C#
// Set the image analysis segmentation mode to background or foreground
SegmentationMode = ImageSegmentationMode.ForegroundMatting
```

**Python**

```Python
# Set the image analysis segmentation mode to background or foreground
analysis_options.segmentation_mode = sdk.ImageSegmentationMode.FOREGROUND_MATTING
```

4. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Öffnen Sie dabei die Datei **newimage.jpg**, die im selben Ordner wie Ihre Codedatei für jedes Bild erzeugt wird.  Beachten Sie, wie eine Vordergrundmattierung für Ihre Bilder erzeugt wurde.

## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Suchen Sie in der oberen Suchleiste nach einem *Azure KI Services Multi-Service-Konto*, und wählen Sie die Azure KI Services Multi-Service-Kontoressource aus, die Sie in diesem Lab erstellt haben.

3. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

In dieser Übung haben Sie sich mit einigen der Funktionen des Azure KI Vision-Diensts zur Analyse und Bearbeitung von Bildern vertraut gemacht. Der Dienst umfasst auch Funktionen für die Erkennung von Objekten und Personen und andere Aufgaben des maschinelles Sehens.

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts finden Sie in der [Dokumentation zu Azure KI Vision](https://learn.microsoft.com/azure/ai-services/computer-vision/).
