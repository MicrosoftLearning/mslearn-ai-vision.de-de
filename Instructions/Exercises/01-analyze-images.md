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
    dotnet add package Azure.AI.Vision.ImageAnalysis -v 1.0.0-beta.1
    ```

    > **Hinweis**: Wenn Sie aufgefordert werden, Dev Kit-Erweiterungen zu installieren, können Sie die Meldung sicher schließen.

    **Python**
    
    ```
    pip install azure-ai-vision-imageanalysis==1.0.0b1
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
    using Azure.AI.Vision.ImageAnalysis;
    ```
    
    **Python**
    
    ```Python
    # import namespaces
    from azure.ai.vision.imageanalysis import ImageAnalysisClient
    from azure.ai.vision.imageanalysis.models import VisualFeatures
    from azure.core.credentials import AzureKeyCredential
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

2. Beachten Sie in der Funktion **Main** unter dem soeben hinzugefügten Code, dass der Code den Pfad zu einer Bilddatei angibt und dann den Bildpfad an zwei andere Funktionen (**AnalyzeImage** und **BackgroundForeground**) weitergibt. Diese Funktionen sind noch nicht vollständig implementiert.

3. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get result with specify features to be retrieved** (Ergebnisse mit bestimmten abzurufenden Features abrufen) den folgenden Code ein:

**C#**

```C#
// Get result with specified features to be retrieved
ImageAnalysisResult result = client.Analyze(
    BinaryData.FromStream(stream),
    VisualFeatures.Caption | 
    VisualFeatures.DenseCaptions |
    VisualFeatures.Objects |
    VisualFeatures.Tags |
    VisualFeatures.People);
```

**Python**

```Python
# Get result with specified features to be retrieved
result = cv_client.analyze(
    image_data=image_data,
    visual_features=[
        VisualFeatures.CAPTION,
        VisualFeatures.DENSE_CAPTIONS,
        VisualFeatures.TAGS,
        VisualFeatures.OBJECTS,
        VisualFeatures.PEOPLE],
)
```
    
4. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Display analysis results** (Analyseregeln anzeigen) den folgenden Code ein (einschließlich der Kommentare, die darauf hinweisen, wo Sie später weiteren Code hinzufügen werden):

**C#**

```C#
// Display analysis results
// Get image captions
if (result.Caption.Text != null)
{
    Console.WriteLine(" Caption:");
    Console.WriteLine($"   \"{result.Caption.Text}\", Confidence {result.Caption.Confidence:0.00}\n");
}

// Get image dense captions
Console.WriteLine(" Dense Captions:");
foreach (DenseCaption denseCaption in result.DenseCaptions.Values)
{
    Console.WriteLine($"   Caption: '{denseCaption.Text}', Confidence: {denseCaption.Confidence:0.00}");
}

// Get image tags


// Get objects in the image


// Get people in the image
```

**Python**

```Python
# Display analysis results
# Get image captions
if result.caption is not None:
    print("\nCaption:")
    print(" Caption: '{}' (confidence: {:.2f}%)".format(result.caption.text, result.caption.confidence * 100))

# Get image dense captions
if result.dense_captions is not None:
    print("\nDense Captions:")
    for caption in result.dense_captions.list:
        print(" Caption: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get objects in the image


# Get people in the image

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
if (result.Tags.Values.Count > 0)
{
    Console.WriteLine($"\n Tags:");
    foreach (DetectedTag tag in result.Tags.Values)
    {
        Console.WriteLine($"   '{tag.Name}', Confidence: {tag.Confidence:F2}");
    }
}
```

**Python**

```Python
# Get image tags
if result.tags is not None:
    print("\nTags:")
    for tag in result.tags.list:
        print(" Tag: '{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Beachten Sie dabei, dass neben der Bildbeschriftung auch eine Liste mit vorgeschlagenen Tags angezeigt wird.

## Erkennen und Suchen von Objekten in einem Bild

Die *Objekterkennung* ist eine spezielle Form des maschinellen Sehens, bei der einzelne Objekte innerhalb eines Bilds identifiziert und ihre Position durch einen Begrenzungsrahmen angegeben werden.

1. Fügen Sie in der Funktion **AnalyzeImage** unter dem Kommentar **Get objects in the image** (Objekte im Bild abrufen) den folgenden Code hinzu:

**C#**

```C#
// Get objects in the image
if (result.Objects.Values.Count > 0)
{
    Console.WriteLine(" Objects:");

    // Prepare image for drawing
    stream.Close();
    System.Drawing.Image image = System.Drawing.Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.WhiteSmoke);

    foreach (DetectedObject detectedObject in result.Objects.Values)
    {
        Console.WriteLine($"   \"{detectedObject.Tags[0].Name}\"");

        // Draw object bounding box
        var r = detectedObject.BoundingBox;
        Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.Tags[0].Name,font,brush,(float)r.X, (float)r.Y);
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
    image = Image.open(image_filename)
    fig = plt.figure(figsize=(image.width/100, image.height/100))
    plt.axis('off')
    draw = ImageDraw.Draw(image)
    color = 'cyan'

    for detected_object in result.objects.list:
        # Print object name
        print(" {} (confidence: {:.2f}%)".format(detected_object.tags[0].name, detected_object.tags[0].confidence * 100))
        
        # Draw object bounding box
        r = detected_object.bounding_box
        bounding_box = ((r.x, r.y), (r.x + r.width, r.y + r.height)) 
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.tags[0].name,(r.x, r.y), backgroundcolor=color)

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
if (result.People.Values.Count > 0)
{
    Console.WriteLine($" People:");

    // Prepare image for drawing
    System.Drawing.Image image = System.Drawing.Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.WhiteSmoke);

    foreach (DetectedPerson person in result.People.Values)
    {
        // Draw object bounding box
        var r = person.BoundingBox;
        Rectangle rect = new Rectangle(r.X, r.Y, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);
        
        // Return the confidence of the person detected
        //Console.WriteLine($"   Bounding box {person.BoundingBox.ToString()}, Confidence: {person.Confidence:F2}");
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
    image = Image.open(image_filename)
    fig = plt.figure(figsize=(image.width/100, image.height/100))
    plt.axis('off')
    draw = ImageDraw.Draw(image)
    color = 'cyan'

    for detected_people in result.people.list:
        # Draw object bounding box
        r = detected_people.bounding_box
        bounding_box = ((r.x, r.y), (r.x + r.width, r.y + r.height))
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
Console.WriteLine($" Background removal:");
// Define the API version and mode
string apiVersion = "2023-02-01-preview";
string mode = "backgroundRemoval"; // Can be "foregroundMatting" or "backgroundRemoval"

string url = $"computervision/imageanalysis:segment?api-version={apiVersion}&mode={mode}";

// Make the REST call
using (var client = new HttpClient())
{
    var contentType = new MediaTypeWithQualityHeaderValue("application/json");
    client.BaseAddress = new Uri(endpoint);
    client.DefaultRequestHeaders.Accept.Add(contentType);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

    // You can change the url to use other images in the images folder,
    // such as "building.jpg" or "person.jpg" to see different results.
    var data = new
    {
        url="https://github.com/MicrosoftLearning/mslearn-ai-vision/blob/main/Labfiles/01-analyze-images/Python/image-analysis/images/street.jpg?raw=true"
    };

    var jsonData = JsonSerializer.Serialize(data);
    var contentData = new StringContent(jsonData, Encoding.UTF8, contentType);
    var response = await client.PostAsync(url, contentData);

    if (response.IsSuccessStatusCode) {
        File.WriteAllBytes("background.png", response.Content.ReadAsByteArrayAsync().Result);
        Console.WriteLine("  Results saved in background.png\n");
    }
    else
    {
        Console.WriteLine($"API error: {response.ReasonPhrase} - Check your body url, key, and endpoint.");
    }
}
```

**Python**

```Python
# Remove the background from the image or generate a foreground matte
print('\nRemoving background from image...')
    
url = "{}computervision/imageanalysis:segment?api-version={}&mode={}".format(endpoint, api_version, mode)

headers= {
    "Ocp-Apim-Subscription-Key": key, 
    "Content-Type": "application/json" 
}

image_url="https://github.com/MicrosoftLearning/mslearn-ai-vision/blob/main/Labfiles/01-analyze-images/Python/image-analysis/{}?raw=true".format(image_file)  

body = {
    "url": image_url,
}
    
response = requests.post(url, headers=headers, json=body)

image=response.content
with open("backgroundForeground.png", "wb") as file:
    file.write(image)
print('  Results saved in backgroundForeground.png \n')
```
    
2. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Öffnen Sie dabei die Datei **background.png**, die im selben Ordner wie Ihre Codedatei für jedes Bild generiert wird.  Beachten Sie, wie der Hintergrund aus den einzelnen Bildern entfernt wurde.

Wir generieren nun eine Vordergrundmattierung für unsere Bilder.

3. Suchen Sie in Ihrer Codedatei die **BackgroundForeground-**-Funktion und ändern Sie unter dem Kommentar **Define the API version and mode** (API-Version und -modus definieren) die Modusvariable in `foregroundMatting`.

4. Speichern Sie Ihre Änderungen, und führen Sie das Programm einmal für jede der Bilddateien im Ordner **images** aus. Öffnen Sie dabei die Datei **background.png**, die im selben Ordner wie Ihre Codedatei für jedes Bild generiert wird.  Beachten Sie, wie eine Vordergrundmattierung für Ihre Bilder erzeugt wurde.

## Bereinigen von Ressourcen

Wenn Sie die in diesem Lab erstellten Azure-Ressourcen nicht für andere Trainingmodule verwenden, können Sie sie löschen, um weitere Gebühren zu vermeiden. Gehen Sie dazu wie folgt vor:

1. Öffnen Sie das Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit dem Microsoft-Konto an, das Ihrem Azure-Abonnement zugeordnet ist.

2. Suchen Sie in der oberen Suchleiste nach einem *Azure KI Services Multi-Service-Konto*, und wählen Sie die Azure KI Services Multi-Service-Kontoressource aus, die Sie in diesem Lab erstellt haben.

3. Wählen Sie auf der Ressourcenseite **Löschen** aus, und folgen Sie den Anweisungen zum Löschen der Ressource.

## Weitere Informationen

In dieser Übung haben Sie sich mit einigen der Funktionen des Azure KI Vision-Diensts zur Analyse und Bearbeitung von Bildern vertraut gemacht. Der Dienst umfasst auch Funktionen für die Erkennung von Objekten und Personen und andere Aufgaben des maschinelles Sehens.

Weitere Informationen zur Verwendung des **Azure KI Vision**-Diensts finden Sie in der [Dokumentation zu Azure KI Vision](https://learn.microsoft.com/azure/ai-services/computer-vision/).
