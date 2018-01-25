---
title: "Plusieurs fichiers d’entrée et propriétés du composant avec Premium Encoder - Azure | Microsoft Docs"
description: "Cette rubrique explique comment utiliser setRuntimeProperties pour plusieurs fichiers d’entrée et transmettre des données personnalisées au processeur multimédia de flux de travail Media Encoder Premium."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: a55d6dc212da05d7c14679579258e28921fecdc8
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilisation de plusieurs fichiers d’entrée et propriétés du composant avec Premium Encoder
## <a name="overview"></a>Vue d’ensemble
Il existe des scénarios dans lesquels vous devrez peut-être personnaliser les propriétés du composant, spécifier le contenu du fichier XML de liste de séquences ou envoyer plusieurs fichiers d’entrée lorsque vous soumettez une tâche avec le processeur multimédia **Media Encoder Premium Workflow** . Voici quelques exemples :

* Superposition de texte sur la vidéo et définition de la valeur du texte (par exemple, la date actuelle) au moment de l’exécution pour chaque vidéo d’entrée.
* Personnalisation du fichier XML de liste de séquences (pour spécifier un ou plusieurs fichiers source, avec ou sans découpage, etc.).
* Superposition d’une image de logo sur l’entrée vidéo pendant l’encodage de la vidéo.
* Encodage de plusieurs langues audio.

Pour faire savoir à **Media Encoder Premium Workflow** que vous modifiez certaines propriétés dans le flux de travail quand vous créez la tâche ou envoyez plusieurs fichiers d’entrée, vous devez utiliser une chaîne de configuration qui contient **setRuntimeProperties** et/ou **transcodeSource**. Cette rubrique explique comment les utiliser.

## <a name="configuration-string-syntax"></a>Syntaxe de chaîne de configuration
La chaîne de configuration à définir dans la tâche d’encodage utilise un document XML qui ressemble au document suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Voici le code C# qui lit la configuration XML d’un fichier, la met à jour avec le nom de fichier vidéo approprié et la transmet à la tâche d’un travail :

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Personnalisation des propriétés du composant
### <a name="property-with-a-simple-value"></a>Propriété avec une valeur simple
Dans certains cas, il peut être utile de personnaliser une propriété du composant avec le fichier de flux de travail qui doit être exécuté par Media Encoder Premium Workflow.

Supposons que vous ayez conçu un flux de travail qui superpose du texte sur vos vidéos et que ce texte (par exemple, la date actuelle) doive être défini au moment de l’exécution. Pour cela, vous pouvez envoyer le texte à définir comme nouvelle valeur de la propriété de texte du composant Overlay à partir de la tâche d’encodage. Vous pouvez utiliser ce mécanisme pour modifier d’autres propriétés d’un composant du flux de travail (par exemple, la position ou la couleur de la superposition, la vitesse de transmission de l’encodeur AVC, etc.).

**setRuntimeProperties** est utilisé pour remplacer une propriété dans les composants du flux de travail.

Exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Propriété avec une valeur XML
Pour définir une propriété qui attend une valeur XML, encapsulez à l’aide de `<![CDATA[ and ]]>`.

Exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Assurez-vous de ne pas mettre de retour chariot juste après `<![CDATA[`.

### <a name="propertypath-value"></a>Valeur propertyPath
Dans les exemples précédents, la valeur propertyPath était « /Media File Input/filename », « /inactiveTimeout » ou « clipListXml ».
Il s’agit en général du nom du composant, puis du nom de la propriété. Le chemin d’accès peut avoir un nombre de niveaux différent, comme « / primarySourceFile » (étant donné que la propriété est à la racine du flux de travail) ou « /Video Processing/Graphic Overlay/Opacity » (car la propriété Overlay est dans un groupe).    

Pour vérifier le nom du chemin et de la propriété, utilisez le bouton d’action en regard de chaque propriété. Vous pouvez cliquer sur ce bouton d’action et sélectionner **Modifier**. Cette commande affiche le nom réel de la propriété et, juste au-dessus, l’espace de noms.

![Action/Modifier](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriété](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Fichiers d’entrée multiples
Chaque tâche que vous envoyez au **Media Encoder Premium Workflow** nécessite deux éléments :

* Une *Ressource de flux de travail* qui contient un fichier de flux de travail. pouvant être créé à l’aide du [Concepteur de flux de travail](media-services-workflow-designer.md).
* Un *Élément multimédia* qui contient le(s) fichier(s) multimédia(s) que vous souhaitez encoder.

Lorsque vous envoyez plusieurs fichiers multimédias à l’encodeur **Media Encoder Premium Workflow** , les contraintes suivantes s’appliquent :

* Tous les fichiers multimédias doivent être dans le même *Élément multimédia*. L’utilisation de plusieurs éléments multimédias n’est pas prise en charge.
* Vous devez définir le fichier principal dans cet élément multimédia (dans l’idéal, il s’agit du fichier vidéo principal que l’encodeur doit traiter).
* Il est nécessaire de passer les données de configuration qui incluent l’élément **setRuntimeProperties** et/ou **transcodeSource** au processeur.
  * **setRuntimeProperties** est utilisé pour remplacer la propriété Nom de fichier ou une autre propriété dans les composants du flux de travail.
  * **transcodeSource** est utilisé pour spécifier le contenu du fichier XML de liste de séquences.

Connexions dans le flux de travail :

* Si vous utilisez un ou plusieurs composants Media File Input et que vous envisagez d’utiliser **setRuntimeProperties** pour spécifier le nom de fichier, ne leur connectez pas la broche du composant de fichier principal. Vérifiez qu’il n’y a aucune connexion entre l’objet de fichier principal et le(s) composant(s) Media File Input.
* Si vous préférez utiliser le fichier XML de liste de séquences et un composant Media Source, vous pouvez les connecter entre eux.

![Aucune connexion entre la propriété Primary Source File et le composant Media File Input](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Il n’y a aucune connexion entre le fichier principal et le(s) composant(s) Media File Input si vous utilisez setRuntimeProperties pour définir la propriété Nom de fichier.*

![Connexion depuis le fichier XML de liste de séquences au composant Clip List Source](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Vous pouvez connecter le fichier XML de liste de séquences à Media Source et utiliser transcodeSource.*

### <a name="clip-list-xml-customization"></a>Personnalisation du fichier XML de liste de séquences
Vous pouvez spécifier le fichier XML de liste de séquences dans le flux de travail, lors de l’exécution, à l’aide de **transcodeSource** dans la chaîne de configuration XML. Cela nécessite que la broche du fichier XML de liste de séquences soit connectée au composant Media Source dans le flux de travail.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Si vous souhaitez spécifier /primarySourceFile pour utiliser cette propriété afin de nommer les fichiers de sortie à l’aide de « Expressions », nous vous recommandons de transmettre le fichier XML de liste de séquences en tant que propriété *après* la propriété /primarySourceFile, afin d’éviter que la liste de séquences ne soit remplacée par le paramètre /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Avec découpage précis de la trame supplémentaire :

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemple 1 : Superposition d’une image sur la vidéo

### <a name="presentation"></a>Présentation
Prenons un exemple dans lequel vous voulez superposer une image de logo sur la vidéo d’entrée pendant l’encodage de la vidéo. Dans cet exemple, la vidéo d’entrée est nommée « Microsoft_HoloLens_Possibilities_816p24.mp4 » et le logo est nommé « logo.png ». Vous devez effectuer les étapes suivantes :

* Créez une ressource de flux de travail avec le fichier de flux de travail (voir l’exemple suivant).
* Créez un élément multimédia contenant deux fichiers : MyInputVideo.mp4 en tant que fichier principal et MyLogo.png.
* Envoyez une tâche au processeur multimédia Media Encoder Premium Workflow avec les ressources d’entrée ci-dessus et spécifiez la chaîne de configuration suivante.

Configuration :

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Dans l’exemple ci-dessus, le nom du fichier vidéo est envoyé au composant Media File Input et à la propriété primarySourceFile. Le nom du fichier logo est envoyé à un autre Media File Input connecté au composant de superposition graphique.

> [!NOTE]
> Le nom du fichier vidéo est envoyé à la propriété primarySourceFile. L’objectif est d’utiliser cette propriété dans le flux de travail pour générer le nom de fichier de sortie correct à l’aide d’Expressions, par exemple.

### <a name="step-by-step-workflow-creation"></a>Procédure de création de workflow pas à pas
Voici les étapes pour créer un flux de travail prenant deux fichiers en entrée : une vidéo et une image. Ce flux de travail superpose l’image sur la vidéo.

Ouvrez **Concepteur de flux de travail** et sélectionnez **Fichier** > **Nouvel espace de travail** > **Plan de transcodage**.

Le nouveau flux de travail affiche trois éléments :

* Fichier source principal
* Fichier XML de liste de séquences
* Fichier/élément multimédia de sortie  

![Nouveau workflow d’encodage](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nouveau workflow d’encodage*

Pour accepter le fichier multimédia d’entrée, commencez par ajouter un composant Media File Input. Pour ajouter un composant au workflow, recherchez-le dans la zone de recherche du référentiel et faites glisser l’entrée souhaitée dans le volet du concepteur.

Ensuite, ajoutez le fichier vidéo à utiliser pour la conception de votre flux de travail. Pour ce faire, cliquez sur l’arrière-plan du volet du Concepteur de flux de travail et recherchez la propriété Primary Source File dans le volet des propriétés à droite. Cliquez sur l’icône de dossier et sélectionnez le fichier vidéo approprié.

![Primary Source File](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primary Source File*

Ensuite, spécifiez le fichier vidéo dans le composant Media File Input.   

![Source Media File Input](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Source Media File Input*

Une fois cette opération effectuée, le composant Media File Input inspectera le fichier et remplira ses broches de sortie afin de refléter le fichier inspecté.

L’étape suivante consiste à ajouter un composant «Video Data Type Updater » pour spécifier l’espace colorimétrique sur Rec.709. Ajoutez un composant « Video Format Converter » défini sur Disposition des données/Type de disposition = Planaire configurable. Cela convertit le flux vidéo en un format pouvant être considéré comme une source du composant de superposition.

![Video Data Type Updater et Format Converter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video Data Type Updater et Format Converter*

![Type de disposition = Planaire configurable](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Le type de disposition est Planaire configurable*

Ensuite, ajoutez un composant Video Overlay et connectez la broche de la vidéo (Uncompressed) à la broche de la vidéo (Uncompressed) du composant Media File Input.

Ajoutez un autre composant Media File Input (pour charger le fichier du logo), cliquez sur ce composant et renommez-le « Logo Media File Input », puis sélectionnez une image (un fichier .png par exemple) dans la propriété de fichier. Connectez la broche de l’image Uncompressed à la broche de l’image Uncompressed de la superposition.

![Composant Overlay et source du fichier d’image](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Composant Overlay et source du fichier d’image*

Si vous souhaitez modifier la position du logo sur la vidéo (vous voulez par exemple le positionner 10 % en retrait de l’angle supérieur gauche), décochez la case « Entrée manuelle ». Vous pouvez le faire car vous utilisez un composant Media File Input pour fournir le fichier de logo au composant de superposition.

![Position de superposition](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Position de superposition*

Pour encoder le flux vidéo en H.264, ajoutez les composants AVC Video Encoder et AAC encoder à l’aire du concepteur. Connectez les broches.
Configurez le composant AAC encoder et sélectionnez Conversion du format audio/Présélection : 2.0 (G, D).

![Encodeurs audio et vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Encodeurs audio et vidéo*

Ajoutez maintenant les composants **ISO Mpeg-4 Multiplexer** et **File Output**, et connectez les broches, comme illustré.

![MP4 Multiplexer et File Output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 Multiplexer et File Output*

Vous devez définir le nom du fichier de sortie. Cliquez sur le composant **File Output** et modifiez l’expression du fichier :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nom de sortie du fichier](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nom de sortie du fichier*

Vous pouvez exécuter le flux de travail localement pour vérifier qu’il fonctionne correctement.

Une fois terminé, vous pouvez l’exécuter dans Azure Media Services.

Préparez tout d’abord un élément multimédia dans Azure Media Services avec deux fichiers : le fichier vidéo et le logo. Vous pouvez pour cela utiliser l’API REST ou .NET. Vous pouvez également le faire avec le portail Azure ou [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ce didacticiel vous montre comment gérer des éléments multimédias avec AMSE. Il existe deux façons d’ajouter des fichiers à un élément multimédia :

* Créez un dossier local, copiez-y les deux fichiers et glissez-déplacez le dossier vers l’onglet **Élément multimédia** .
* Téléchargez le fichier vidéo en tant qu’élément multimédia, affichez les informations de l’élément, accédez à l’onglet Fichier, puis téléchargez un fichier supplémentaire (logo).

> [!NOTE]
> Veillez à définir un fichier principal dans l’élément multimédia (le fichier vidéo principal).

![Fichiers d’élément multimédia dans AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Fichiers d’élément multimédia dans AMSE*

Sélectionnez l’élément et choisissez de l’encoder avec Premium Encoder. Téléchargez le flux de travail et sélectionnez-le.

Cliquez sur le bouton pour transmettre les données au processeur, puis ajoutez le code XML suivant pour définir les propriétés d’exécution :

![Premium Encoder dans AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium Encoder dans AMSE*

Ensuite, collez les données XML suivantes. Vous devez spécifier le nom du fichier vidéo pour le composant Media File Input et pour la propriété primarySourceFile. Spécifiez le nom de fichier pour le logo.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Si vous utilisez le Kit de développement logiciel (SDK) .NET pour créer et exécuter la tâche, ces données XML doivent être transmises en tant que chaîne de configuration.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Une fois la tâche effectuée, le fichier MP4 dans l’élément de sortie affiche la superposition.

![Superposition sur la vidéo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Superposition sur la vidéo*

Vous pouvez télécharger l’exemple de flux de travail sur [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemple 2 : Encodage de plusieurs langues audio

Vous trouverez un exemple de workflow d’encodage de plusieurs langues audio dans [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ce dossier contient un exemple de workflow qui peut être utilisé pour encoder un fichier MXF en une ressource contenant plusieurs fichiers MP4 avec plusieurs pistes audio.

Ce workflow suppose que le fichier MXF contient une piste audio. Les pistes audio supplémentaires doivent être transmises en tant que fichiers audio distincts (WAV ou MP4...).

Pour effectuer l’encodage, procédez comme suit :

* Créez une ressource Media Services avec le fichier MXF et les fichiers audio (de 0 à 18 fichiers audio).
* Assurez-vous que le fichier MXF est défini comme fichier principal.
* Créez un travail et une tâche à l’aide du processeur de workflow d’encodeur premium. Utilisez le workflow fourni (MultiMP4-1080p-19audio-v1.workflow).
* Transmettez les données setruntime.xml à la tâche (si vous utilisez Azure Media Services Explorer, utilisez le bouton « Transmettre les données xml au workflow »).
  * Mettez à jour les données XML pour spécifier les noms de fichier et balises de langues corrects.
  * Le workflow comporte des éléments audio nommés de Audio 1 à Audio 18.
  * RFC5646 est pris en charge pour la balise de langue.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* La ressource encodée contiendra des pistes audio en plusieurs langues, qui doivent être sélectionnables dans Azure Media Player.

## <a name="see-also"></a>Voir aussi
* [Présentation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Utilisation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Codecs et formats de Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Exemples de fichiers de workflow](https://github.com/Azure/azure-media-services-samples)
* [Outil Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
