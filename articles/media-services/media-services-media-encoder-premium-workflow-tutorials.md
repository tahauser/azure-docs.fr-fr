---
title: "Didacticiels de workflow avancés Media Encoder Premium"
description: "Ce document contient des procédures pas à pas décrivant la manière d’effectuer des tâches avancées avec Media Encoder Premium Workflow et de créer des workflows complexes avec Workflow Designer."
services: media-services
documentationcenter: 
author: xstof
manager: cfowler
editor: 
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 8b714fcf001a6830cffe4df8c152dab40834c7c4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Didacticiels de workflows avancés Media Encoder Premium
## <a name="overview"></a>Vue d'ensemble
Ce document contient des procédures pas à pas qui montrent comment personnaliser les workflows avec **Workflow Designer**. Les fichiers de workflow sont disponibles [ici](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Table des matières
Cet article contient les rubriques suivantes :

* [Encodage du fichier MXF au format MP4 à débit binaire unique](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Démarrage d’un nouveau workflow](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Utilisation du composant Media File Input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspection des flux multimédias](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Ajout d’un encodeur vidéo pour la génération de fichiers MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Encodage du flux audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexage des flux audio et vidéo dans un conteneur de fichiers MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Écriture du fichier MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Création d’un élément multimédia Media Services à partir du fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Test en local du workflow terminé](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Encodage du fichier MXF en fichiers MP4 multidébit avec l’empaquetage dynamique](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Ajout d’une ou plusieurs sorties MP4 supplémentaires](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configuration des noms de fichier de sortie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Ajout d’une piste audio distincte](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Ajout du fichier SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Encodage du fichier MXF en fichiers MP4 multidébit : plan optimisé](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Vue d’ensemble du workflow à améliorer](#workflow-overview-to-enhance)
  * [Conventions d’appellation de fichiers](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publication des propriétés du composant sur la racine du workflow](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Utilisation des valeurs de propriété publiées pour les noms de fichiers de sortie générés](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Ajout de miniatures au fichier de sortie MP4 multidébit](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Vue d’ensemble du workflow auquel ajouter des miniatures](#workflow-overview-to-add-thumbnails-to)
  * [Ajout d’un encodage JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Conversion de l’espace colorimétrique](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Écriture des miniatures](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Détection des erreurs dans un workflow](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Worflow terminé](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Découpage temporel du fichier de sortie MP4 multidébit](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Vue d’ensemble du workflow pour l’ajout du découpage](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Utilisation du composant Stream Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Worflow terminé](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Écriture de scripts dans un workflow : Hello World](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Découpage par images du fichier de sortie MP4 multidébit](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Vue d’ensemble du plan pour l’ajout du découpage](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Utilisation du fichier XML Clip List](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modification de la liste de séquences à partir d’un composant de script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Ajout d’une propriété de commodité ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Encodage du fichier MXF au format MP4 à débit binaire unique
Cette section explique comment créer un fichier .MP4 à débit binaire unique avec des données audio encodées en AAC-HE à partir d’un fichier d’entrée .MXF.

### <a id="MXF_to_MP4_start_new"></a>Démarrage d’un nouveau workflow
Ouvrez le concepteur de flux de travail et sélectionnez Fichier > Nouvel espace de travail > Plan de transcodage.

Le nouveau flux de travail affiche trois éléments :

* Fichier source principal
* Fichier XML de liste de séquences
* Fichier/élément multimédia de sortie  

![Nouveau workflow d’encodage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nouveau workflow d’encodage*

### <a id="MXF_to_MP4_with_file_input"></a>Utilisation du composant Media File Input
Pour accepter le fichier multimédia d’entrée, nous devons commencer par ajouter un composant Media File Input. Pour ajouter un composant au workflow, recherchez-le dans la zone de recherche du référentiel et faites glisser l’entrée souhaitée dans le volet du concepteur. Répétez cette opération pour le composant Media File Input et connectez le composant Primary Source File à la broche d’entrée FileName du composant Media File Input.

![Composant Media File Input connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Composant Media File Input connecté*

Au départ, identifiez un fichier d’exemple approprié à utiliser lors de la conception d’un workflow personnalisé. Pour ce faire, cliquez sur l’arrière-plan du volet du concepteur et recherchez la propriété Primary Source File dans le volet des propriétés à droite de l’écran. Cliquez sur l’icône de dossier et sélectionnez le fichier à utiliser pour tester le workflow. Le composant Media File Input inspecte le fichier et remplit ses broches de sortie afin de refléter les détails du fichier d’exemple inspecté.

![Composant Media File Input renseigné](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Composant Media File Input renseigné*

Maintenant que l’entrée est remplie, l’étape suivante consiste à configurer les paramètres d’encodage de sortie. En suivant la même procédure que la configuration du fichier source principal, vous devez à présent configurer la propriété Output Folder Variable, qui se trouve juste en dessous.

![Propriétés d’entrée et de sortie configurées](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriétés d’entrée et de sortie configurées*

### <a id="MXF_to_MP4_streams"></a>Inspection des flux multimédias
Il est souvent intéressant de savoir à quoi ressemble le flux qui transite via le workflow. Pour examiner à tout moment un flux de données dans le workflow, cliquez simplement sur une broche de sortie ou d’entrée sur l’un des composants. Dans ce cas, essayez de cliquer sur la broche de sortie Uncompressed Video à partir du composant Media File Input. Vous accédez à une boîte de dialogue dans laquelle vous pouvez examiner la sortie vidéo.

![Inspection de la broche de sortie Uncompressed Video](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspection de la broche de sortie Uncompressed Video*

Dans ce cas, nous avons affaire à une vidéo avec une entrée 1920 x 1080 à 24 images par seconde en échantillonnage 4:2:2 pour une vidéo d’un peu moins de 2 minutes.

### <a id="MXF_to_MP4_file_generation"></a>Ajout d’un encodeur vidéo pour la génération de fichiers MP4
Le composant Media File Input comporte maintenant une broche de sortie Uncompressed Video et plusieurs broches de sortie Uncompressed Audio. Pour encoder la vidéo d’entrée, il faut ajouter un composant d’encodage au workflow (pour, dans le cas présent, générer des fichiers .MP4).

Pour encoder le flux vidéo en H.264, ajoutez le composant AVC Video Encoder à la surface du concepteur. Ce composant utilise un flux vidéo non compressé comme entrée et génère un flux vidéo AVC compressé sur sa broche de sortie.

![Encodeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Encodeur AVC non connecté*

Ses propriétés déterminent le déroulement exact de la procédure d’encodage. Observons quelques-uns des principaux paramètres :

* Largeur de sortie et hauteur de sortie : ce paramètre détermine la résolution de la vidéo encodée. Dans ce cas, 640 x 360 est un paramètre approprié.
* Fréquence d’images : lorsque ce paramètre est défini sur un transfert direct, il adopte simplement la fréquence d’images source (il est cependant possible de remplacer cette valeur). Cette conversion de fréquence d’images n’est pas compensée en mouvement.
* Profil et niveau : ce paramètre détermine le profil et le niveau de l’encodeur AVC. Pour obtenir facilement des informations supplémentaires sur les différents niveaux et profils, cliquez sur l’icône de point d’interrogation au niveau du composant AVC Video Encoder pour accéder à une page d’aide qui décrit en détail chaque niveau. Pour cet exemple, utilisez le profil principal au niveau 3.2 (valeur par défaut).
* Mode de contrôle du débit et débit binaire (kbit/s) : dans ce scénario, optez pour un débit binaire constant (CBR) à 1 200 kbit/s.
* Format vidéo : cette option fournit des informations d’état qualitatif de la vidéo écrites dans le flux H.264 (informations annexes pouvant être utilisées par un décodeur afin d’améliorer l’affichage, mais n’ayant aucun caractère essentiel pour le déroulement du décodage) :
* NTSC (standard aux États-Unis ou au Japon, avec un débit de 30 images par seconde)
* PAL (standard en Europe, avec un débit de 25 images par seconde)
* Mode de taille de groupe d’images : configurez une taille de groupe d’images fixe avec un intervalle de clé de 2 secondes et des groupes d’images fermés. Ce délai de 2 secondes garantit la compatibilité avec l’empaquetage dynamique fourni par Azure Media Services.

Pour charger l’encodeur AVC, connectez la broche de sortie Uncompressed Video du composant Media File Input à la broche d’entrée Uncompressed Video de l’encodeur AVC.

![Encodeur AVC connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Encodeur AVC principal connecté*

### <a id="MXF_to_MP4_audio"></a>Encodage du flux audio
À ce stade, il reste encore à compresser le flux audio non compressé d’origine. Pour la compression du flux audio, ajoutez un composant d’encodeur AAC (Dolby) au workflow.

![Encodeur AVC non connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Encodeur AAC non connecté*

Nous sommes maintenant confrontés à une incompatibilité : l’encodeur AAC comporte une seule broche d’entrée Uncompressed Audio, alors que le composant Media File Input disposera très probablement de deux flux audio non compressés : un pour le canal audio de gauche, l’autre pour celui de droite (soit 6 canaux dans le cas d’un son surround). Il est donc impossible de connecter directement l’audio de la source Media File Input dans l’encodeur audio AAC. Le composant AAC attend un flux audio dit « entrelacé », c’est-à-dire un flux unique dans lequel les canaux de gauche et de droite sont entrelacés. Une fois que notre fichier multimédia source nous permet de connaître la position des pistes audio dans la source, nous pouvons générer ce flux audio entrelacé en affectant correctement les positions de haut-parleur pour les canaux de gauche et de droite.

Tout d’abord, nous allons générer un flux entrelacé à partir des canaux audio source requis. Le composant Audio Stream Interleaver s’en charge automatiquement. Ajoutez ce composant au workflow et connectez-y les sorties audio du composant Media File Input.

![Composant Audio Stream Interleaver connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Composant Audio Stream Interleaver connecté*

Nous disposons maintenant d’un flux audio entrelacé, mais nous n’avons pas encore indiqué où affecter les positions de haut-parleur gauche ou droite. Pour cela, nous pouvons utiliser le composant Speaker Position Assigner.

![Ajout d’un composant Speaker Position Assigner](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Ajout d’un composant Speaker Position Assigner*

Configurez le composant Speaker Position Assigner pour pouvoir l’utiliser avec un flux d’entrée stéréo via un filtre de présélection d’encodeur « personnalisé » et une présélection de canal nommée « 2.0 (L,R) ». (Ceci vous permet d’affecter la position de haut-parleur gauche au canal 1 et la position de haut-parleur droit au canal 2.)

Connectez la sortie du composant Speaker Position Assigner à l’entrée de l’encodeur AAC. Indiquez ensuite à l’encodeur AAC d’utiliser une présélection de canal « 2.0 (L,R) » afin qu’il puisse traiter le flux audio stéréo en tant qu’entrée.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexage des flux audio et vidéo dans un conteneur de fichiers MP4
Nous pouvons maintenant capturer notre flux vidéo encodé en AVC et notre flux audio encodé en AAC dans un conteneur de fichiers .MP4. On appelle « multiplexage » (ou « muxing ») le processus qui consiste à combiner différents flux en un seul. Dans ce cas, nous allons entrelacer les flux audio et flux vidéo en un seul package de fichiers .MP4 cohérent. Le composant qui coordonne cette opération pour un conteneur . MP4 est appelé « multiplexeur ISO MPEG-4 ». Ajoutez-en un à la surface du concepteur et connectez l’encodeur vidéo AVC et l’encodeur AAC à ses entrées.

![Multiplexeur MPEG4 connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Multiplexeur MPEG4 connecté*

### <a id="MXF_to_MP4_writing_mp4"></a>Écriture du fichier MP4
Le composant File Output est utilisé pour l’écriture d’un fichier de sortie. Ce composant peut être connecté à la sortie du multiplexeur ISO MPEG-4 de manière à pouvoir écrire ses sorties sur le disque. Pour ce faire, connectez la broche de sortie Container (MPEG-4) à la broche d’entrée Write du composant File Output.

![Composant File Output connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Composant File Output connecté*

Le nom de fichier utilisé est déterminé par la propriété de fichier. Si cette propriété peut être codée en dur sur une valeur donnée, il est sans doute préférable de la définir dans une expression.

Pour que le workflow détermine automatiquement la propriété de nom du fichier de sortie à partir d’une expression, cliquez sur le bouton en regard du nom de fichier (près de l’icône de dossier). Dans le menu déroulant, sélectionnez « Expression ». L’éditeur d’expressions s’affiche. Commencez par effacer le contenu de l’éditeur.

![Éditeur d’expressions vide](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Éditeur d’expressions vide*

L’éditeur d’expressions vous permet de saisir n’importe quelle valeur littérale, combinée avec une ou plusieurs variables. Les variables commencent par le symbole de dollar. Lorsque vous appuyez sur la touche $, l’éditeur affiche une liste déroulante contenant les variables disponibles. Dans notre cas, nous allons utiliser à la fois la variable de répertoire de sortie et la variable de nom du fichier d’entrée de base :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Éditeur d’expressions renseigné](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Éditeur d’expressions renseigné*

> [!NOTE]
> Pour afficher un fichier de sortie de votre tâche d’encodage dans Azure, vous devez renseigner une valeur dans l’éditeur d’expressions.
>
>

Lorsque vous cliquez sur OK pour confirmer l’expression, la fenêtre des propriétés affiche un aperçu de la valeur résolue à ce stade par la propriété de fichier.

![L’expression du fichier résout le répertoire de sortie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*L’expression du fichier résout le répertoire de sortie*

### <a id="MXF_to_MP4_asset_from_output"></a>Création d’un élément multimédia Media Services à partir du fichier de sortie
Bien que nous ayons écrit un fichier de sortie MP4, il nous reste encore à indiquer que ce fichier appartient à la ressource de sortie générée par Media Services suite à l’exécution de ce workflow. Le nœud Output File/Asset sur le canevas du workflow est utilisé à cette fin. Tous les fichiers entrants dans ce nœud font partie intégrante de l’élément multimédia Azure Media Services généré.

Connectez le composant File Output au composant Output File/Asset pour terminer le workflow.

![Worflow terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Worflow terminé*

### <a id="MXF_to_MP4_test"></a>Test en local du workflow terminé
Pour tester le workflow en local, appuyez sur le bouton de lecture situé dans la barre d’outils en haut de l’écran. Une fois l’exécution du workflow terminée, examinez la sortie générée dans le dossier de sortie configuré. Vous y verrez le fichier de sortie MP4 terminé qui a été encodé à partir du fichier d’entrée MXF source.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Encodage du fichier MXF en fichiers MP4 multidébit avec l’empaquetage dynamique
Cette procédure pas à pas explique comment créer un ensemble de fichiers MP4 multidébit avec un flux audio encodé en AAC à partir d’un seul fichier d’entrée  MXF.

Si vous souhaitez combiner une sortie d’élément multimédia multidébit aux fonctionnalités d’empaquetage dynamique offertes par Azure Media Services, vous devez générer plusieurs fichiers MP4 alignés sur le groupe d’images ayant chacun un débit binaire et une résolution différents. La procédure d’ [encodage d’un fichier MXF en fichier MP4 à débit binaire unique](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) offre un excellent point de départ pour y parvenir.

![Démarrage du workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Démarrage du workflow*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Ajout d’une ou plusieurs sorties MP4 supplémentaires
Chaque fichier MP4 contenu dans l’élément multimédia Azure Media Services que nous avons généré prend en charge des valeurs de débit binaire et de résolution différentes. Nous allons ajouter un ou plusieurs fichiers de sortie MP4 au workflow.

Pour avoir la certitude que tous nos encodeurs vidéo sont créés avec les mêmes paramètres, le mieux est de dupliquer l’encodeur vidéo AVC existant et de configurer une autre combinaison de résolution et de débit binaire (soit une résolution de 960 x 540 à 25 images par seconde pour un débit de 2,5 Mbit/s). Pour dupliquer l’encodeur existant, vous devez le copier et le coller sur la surface du concepteur.

Connectez la broche de sortie Uncompressed Video du composant Media File Input dans notre nouveau composant AVC.

![Deuxième encodeur AVC connecté](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Deuxième encodeur AVC connecté*

Nous devons maintenant adapter la configuration de notre nouvel encodeur AVC à une sortie de 960 x 540 à 2,5 Mbit/s. (Pour cela, utilisez les propriétés « largeur de sortie », « hauteur de sortie » et « débit binaire (Kbit/s) »).

Comme nous voulons utiliser l’élément multimédia obtenu avec les fonctionnalités d’empaquetage dynamique d’Azure Media Services, le point de terminaison de streaming doit être capable d’utiliser ces fichiers MP4 pour générer des fragments MP4/DASH HLS/fragmentés qui soient précisément alignés entre eux afin de permettre aux clients qui basculent d’un débit binaire à un autre de bénéficier d’une expérience audio et vidéo parfaitement fluide et homogène. Nous devons pour cela nous assurer que la taille du groupe d’images (GOP) pour les deux fichiers MP4 est bien définie sur 2 secondes dans les propriétés de ces deux encodeurs AVC, en procédant comme suit :

* définir le mode de taille du groupe d’images sur une taille de groupe d’images fixe et
* configurer un intervalle de trame-clé de deux secondes ;
* et définir le contrôle IDR du groupe d’images sur un groupe d’images fermé afin de s’assurer que tous les groupes d’images seront autonomes et sans aucune dépendance.

Pour simplifier la compréhension de ce workflow, renommez le premier encodeur AVC « Encodeur vidéo AVC 640x360 1 200 Kbit/s » et le second « Encodeur vidéo AVC 960x540 2 500 Kbit/s ».

Ajoutez maintenant un deuxième multiplexeur ISO MPEG-4 et un deuxième composant File Output. Connectez le multiplexeur au nouvel encodeur AVC et assurez-vous que sa sortie est dirigée vers le composant File Output. Connectez également la sortie de l’encodeur audio AAC à l’entrée du nouveau multiplexeur. Le composant File Output peut à son tour être connecté au nœud Output File/Asset pour être ajouté à l’élément multimédia Media Services qui sera créé.

![Deuxièmes composants Muxer et File Output connectés](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Deuxièmes composants Muxer et File Output connectés*

Pour garantir la compatibilité avec l’empaquetage dynamique Azure Media Services, configurez le mode bloc du multiplexeur sur le nombre ou la durée du groupe d’images et définissez une valeur de 1 groupe d’images par bloc (il doit s’agir de la valeur par défaut).

![Modes bloc du multiplexeur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modes bloc du multiplexeur*

Remarque : vous devrez peut-être répéter ce processus pour les autres combinaisons de débit binaire et de résolution que vous souhaitez ajouter à la sortie de l’élément multimédia.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configuration des noms de fichier de sortie
Plusieurs fichiers sont ajoutés à l’élément multimédia de sortie. Nous devons donc nous assurer que les noms de fichiers de chacun des fichiers de sortie sont différents les uns des autres, voire même appliquer une convention d’affectation de noms de fichier afin de déduire le contenu du fichier simplement à partir de son nom.

La dénomination des fichiers de sortie peut être contrôlée au moyen d’expressions dans le concepteur. Ouvrez le volet des propriétés d’un des composants File Output et ouvrez l’éditeur d’expressions correspondant à la propriété de fichier. Notre premier fichier de sortie a été configuré via l’expression suivante (consultez le didacticiel relatif à la conversion [d’un fichier MXF à une sortie MP4 à débit binaire unique](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)) :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Autrement dit, notre nom de fichier est déterminé par deux variables : le répertoire de sortie dans lequel écrire et le nom de base du fichier source. Le premier est exposé sous la forme d’une propriété sur la racine du workflow et le second est déterminé par le fichier entrant. Le répertoire de sortie sert au test en local ; cette propriété sera remplacée par le moteur de workflow lorsque le workflow est exécuté par le processeur multimédia cloud dans Azure Media Services.
Pour donner à nos deux fichiers de sortie des noms de sortie cohérents, modifiez la première expression d’affectation de noms de fichier comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

et la seconde comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Exécutez un test intermédiaire pour vous assurer que les deux fichiers de sortie MP4 sont correctement générés.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Ajout d’une piste audio distincte
Nous verrons plus tard que lorsque nous générons un fichier .ism en complément des fichiers de sortie MP4, nous avons également besoin d’un fichier MP4 audio uniquement qui servira de piste audio pour notre streaming adaptatif. Pour créer ce fichier, ajoutez un multiplexeur supplémentaire au workflow (multiplexeur ISO-MPEG-4) et connectez la broche de sortie de l’encodeur AAC à sa broche d’entrée pour la piste 1.

![Ajout du multiplexeur audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Ajout du multiplexeur audio*

Créez un troisième composant File Output pour générer le flux sortant à partir du multiplexeur et configurez l’expression d’affectation de noms de fichier comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Création du composant File Output par le multiplexeur audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Création du composant File Output par le multiplexeur audio*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Ajout du fichier SMIL .ISM
Pour garantir la compatibilité de l’empaquetage dynamique avec les deux fichiers MP4 (et le fichier MP4 audio uniquement) de notre élément multimédia Media Services, nous avons également besoin d’un fichier manifeste (également appelé fichier « SMIL », ou Synchronized Multimedia Integration Language). Ce fichier indique à Azure Media Services les fichiers MP4 disponibles pour l’empaquetage dynamique ainsi que ceux qui doivent être pris en compte pour le streaming audio. Le fichier manifeste associé à un ensemble de fichiers MP4 à un seul flux audio se présente généralement comme suit :

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Le fichier .ism contient, au sein d’une instruction switch, une référence à chaque fichier vidéo MP4 ainsi qu’une ou plusieurs références de fichier audio à un fichier MP4 contenant uniquement les données audio.

Un composant appelé « AMS Manifest Writer » permet de générer le fichier manifeste pour notre ensemble de fichiers MP4. Pour utiliser ce composant, faites-le glisser sur la surface et connectez les broches de sortie « Write Complete » des trois composants File Output dans l’entrée AMS Manifest Writer. Veillez ensuite à connecter la sortie du composant AMS Manifest Writer au composant Output File/Asset.

Comme pour nos autres composants de sortie de fichier, configurez le nom de sortie du fichier .ism à l’aide d’une expression, comme suit :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Notre workflow terminé prend l’aspect suivant :

![Workflow de la conversion du MXF en MP4 multidébit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Workflow de la conversion du MXF en MP4 multidébit*

## <a id="MXF_to__multibitrate_MP4"></a>Encodage du fichier MXF en fichiers MP4 multidébit : plan optimisé
Dans la [procédure ci-dessus](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous avons vu comment convertir un élément multimédia d’entrée MXF en un élément multimédia de sortie comprenant des fichiers MP4 multidébit, un fichier MP4 audio uniquement et un fichier manifeste pouvant être utilisés avec les fonctionnalités d’empaquetage dynamique Azure Media Services.

Cette procédure pas à pas explique comment améliorer et simplifier certains aspects.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Vue d’ensemble du workflow à améliorer
![Worflow de fichiers MP4 multidébit à améliorer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Worflow de fichiers MP4 multidébit à améliorer*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Conventions d’appellation de fichiers
Dans le workflow précédent, nous avons spécifié une expression simple permettant de générer les noms des fichiers de sortie. Mais cette approche implique des doublons dans la mesure où tous les composants de fichier de sortie individuels spécifient cette expression.

Par exemple, notre composant de sortie de fichier pour le premier fichier vidéo est configuré avec l’expression suivante :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Quant à la deuxième sortie vidéo, nous obtenons une expression du type :

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Pourquoi ne pas rendre tout cela plus clair, juste et pratique en évitant ces doublons et en renforçant les possibilités de configuration ? Heureusement, cela est possible : nous pouvons renforcer la convivialité en utilisant à la fois les fonctionnalités d’expression du concepteur et les fonctions permettant de créer des propriétés personnalisées à la racine de notre workflow.

Imaginons que la configuration des noms de fichiers soit dérivée des débits binaires de chaque fichier MP4. Nous cherchons à configurer ces débits binaires dans un emplacement centralisé (à la racine de notre graphique), d’où ils seront accessibles pour configurer et exécuter la génération des noms de fichiers de disque. Pour ce faire, nous allons commencer par publier la propriété de débit binaire à partir des deux encodeurs AVC à la racine de notre workflow, afin qu’elle devienne accessible aussi bien depuis la racine qu’à partir des encodeurs AVC (même si cette propriété s’affiche à deux endroits différents, il n’y a qu’une seule valeur sous-jacente).

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publication des propriétés du composant sur la racine du workflow
Ouvrez le premier encodeur AVC, accédez à la propriété de débit binaire (kbit/s) et, dans la liste déroulante, choisissez l’option Publier.

![Publication de la propriété de débit binaire](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publication de la propriété de débit binaire*

Configurez la boîte de dialogue Publier pour effectuer la publication à la racine du graphique de notre workflow. Choisissez « video1bitrate » comme nom publié et « Video 1 Bitrate » comme nom complet explicite. Configurez un nom de groupe personnalisé appelé « Streaming Bitrates » et appuyez sur Publier.

![Publication de la propriété de débit binaire](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Boîte de dialogue de publication pour la propriété de débit binaire*

Répétez cette opération pour la propriété de débit binaire du deuxième encodeur AVC et nommez-la « video2bitrate », en choisissant « Video 2 Bitrate » comme nom d’affichage et en la plaçant dans le même groupe personnalisé « Streaming Bitrates ».

En vérifiant maintenant les propriétés à la racine du workflow, nous pouvons voir notre groupe personnalisé contenant les deux propriétés publiées. Celles-ci reflètent la valeur du débit binaire de leur encodeur AVC respectif.

![Propriétés de débit binaire publiées sur la racine du workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Chaque fois que vous souhaitez accéder à ces propriétés à partir du code ou d’une expression, vous pouvez procéder de la manière suivante :

* à partir du code en ligne d’un composant, directement sous la racine : node.getPropertyAsString('../ video1bitrate', null)
* dans une expression : ${ROOT_video1bitrate}

Complétons le groupe « Streaming Bitrates » en y publiant également le débit binaire de notre piste audio. Dans les propriétés de l’encodeur AAC, recherchez le paramètre de débit binaire et sélectionnez Publier dans la liste déroulante en regard de celui-ci. Publiez la propriété à la racine du graphique en lui attribuant le nom « audio1bitrate » et le nom d’affichage « Audio 1 Bitrate » au sein de notre groupe personnalisé « Streaming Bitrates ».

![Boîte de dialogue de publication pour le débit audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Boîte de dialogue de publication pour le débit audio*

![Propriétés vidéo et audio générées à la racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriétés vidéo et audio générées à la racine*

La modification d’une de ces trois valeurs a également pour effet de reconfigurer et de modifier les valeurs sur les composants respectifs auxquels elles sont liées (et à partir desquels elles sont publiées).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Utilisation des valeurs de propriété publiées pour les noms de fichiers de sortie générés
Au lieu de coder les noms de fichier générés, nous pouvons maintenant modifier notre expression de nom de fichier sur chacun des composants File Output pour qu’elle utilise les propriétés de débit binaire que nous avons publiées sur la racine du graphique. En commençant par la première sortie de fichier, recherchez la propriété de fichier et modifiez l’expression comme suit :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Vous pouvez consulter et saisir les différents paramètres de cette expression en vous plaçant dans la fenêtre d’expression et en appuyant sur le signe dollar du clavier. La propriété video1bitrate que nous avons publiée précédemment apparaît parmi les paramètres disponibles.

![Accès aux paramètres dans une expression](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Accès aux paramètres dans une expression*

Procédez de la même manière pour la sortie de fichier de notre deuxième vidéo :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

ainsi que pour la sortie du fichier audio uniquement :

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Si nous modifions maintenant le débit binaire d’un des fichiers audio ou vidéo, l’encodeur concerné sera reconfiguré et la convention de nom de fichier basée sur le débit sera automatiquement appliquée.

## <a id="thumbnails_to__multibitrate_MP4"></a>Ajout de miniatures au fichier de sortie MP4 multidébit
Partons d’un workflow qui génère [une sortie MP4 multidébit à partir d’une entrée MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging). Nous allons maintenant voir comment ajouter des miniatures à la sortie.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Vue d’ensemble du workflow auquel ajouter des miniatures
![Flux de travail MP4 multidébit de départ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Flux de travail MP4 multidébit de départ*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Ajout d’un encodage JPG
La génération de nos miniatures repose sur le composant JPG Encoder, conçu pour générer des fichiers JPG.

![Encodeur JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Encodeur JPG*

Nous ne pouvons cependant pas connecter directement notre flux vidéo non compressé à l’encodeur JPG à partir du composant Media File Input. L’encodeur s’attend à recevoir des images individuelles, ce que l’on peut effectuer grâce au composant Video Frame Gate

![Connexion d’une porte de trame à l’encodeur JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Connexion d’une porte de trame à l’encodeur JPG*

L’ouverture de la porte de trame toutes les secondes ou à chaque image permet la transmission d’une image vidéo. L’intervalle et le temps de décalage de cette ouverture peuvent être configurés dans les propriétés.

![Propriétés du composant Video Frame Gate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriétés du composant Video Frame Gate*

Nous allons créer une miniature chaque minute en définissant le mode sur Temps (en secondes) avec un intervalle de 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Conversion de l’espace colorimétrique
S’il semble logiquement possible désormais de connecter à la fois les broches Uncompressed Video de la porte de trame et le composant Media File Input, une telle opération entraînerait l’envoi d’un avertissement.

![Erreur de l’espace colorimétrique d’entrée](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erreur de l’espace colorimétrique d’entrée*

Cette erreur est liée au fait que la représentation des informations colorimétriques dans notre flux vidéo non compressé d’origine (brut) en provenance de notre MXF est différente de la représentation attendue par l’encodeur JPG. L’encodeur attend plus précisément un « espace colorimétrique » de type « RVB » ou « Échelle de gris ». Autrement dit, vous devez d’abord appliquer une conversion au flux vidéo entrant du composant Video Frame Gate compte tenu de son espace colorimétrique.

Faites glisser le convertisseur d’espace colorimétrique sur le workflow et connectez-le à la porte de trame.

![Connexion d’un convertisseur d’espace colorimétrique](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Connexion d’un convertisseur d’espace colorimétrique*

Dans la fenêtre des propriétés, sélectionnez l’entrée RVB 24 dans la liste des présélections.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Écriture des miniatures
Contrairement aux vidéos MP4, le composant JPG Encoder génère plusieurs fichiers. Pour gérer cette opération, on peut utiliser un composant Scene Search JOG File Writer, qui capture les miniatures JPG entrantes et les écrit sur la sortie, chaque nom de fichier étant suivi d’un nombre différent. (Ce nombre indique généralement le nombre de secondes/d’unités contenues dans le flux à partir duquel la miniature a été extraite.)

![Présentation du composant Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Présentation du composant Scene Search JPG File Writer*

Configurez la propriété de chemin d’accès du dossier de sortie avec l’expression : ${ROOT_outputWriteDirectory}

et la propriété de préfixe de nom de fichier avec :

    ${ROOT_sourceFileBaseName}_thumb_

Le préfixe détermine la dénomination des fichiers de miniatures. Ces fichiers sont suivis d’un nombre indiquant la position du curseur de défilement dans le flux.

![Propriétés du composant Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriétés du composant Scene Search JPG File Writer*

Connectez le composant Scene Search JPG File Writer au nœud Output File/Asset.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Détection des erreurs dans un workflow
Connectez l’entrée du convertisseur d’espace colorimétrique à la sortie vidéo non compressée brute. Testez maintenant le workflow en local. Il est probable que le workflow interrompe subitement son exécution et indique dans un cadre rouge le composant qui a rencontré une erreur :

![Erreur du convertisseur d’espace colorimétrique](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erreur du convertisseur d’espace colorimétrique*

Cliquez sur la petite icône rouge « E » située en haut à droite du composant Color Space Converter pour identifier la cause de l’échec de la tentative d’encodage.

![Boîte de dialogue d’erreur du convertisseur d’espace colorimétrique](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Boîte de dialogue d’erreur du convertisseur d’espace colorimétrique*

Il s’avère, comme vous pouvez le voir, que l’espace colorimétrique entrant du convertisseur d’espace colorimétrique doit normalement être rec601 pour notre demande de conversion du format YUV au format RVB. Apparemment, notre flux n’indique pas qu’il s’agit de la norme rec601 (Rec 601 est une norme d’encodage de signaux vidéo analogiques entrelacés sous forme de vidéo numérique. Elle spécifie une région active couvrant 720 échantillons de luminance et 360 échantillons de chrominance par ligne. Le système d’encodage colorimétrique est appelé YCbCr 4:2:2).

Pour résoudre ce problème, nous allons indiquer dans les métadonnées de notre flux que nous avons affaire à un contenu rec601. Pour ce faire, nous allons utiliser un composant Video Data Type Updater, que nous allons insérer entre notre source brute et le composant de conversion d’espace colorimétrique. Ce composant de mise à jour du type de données permet de mettre à jour manuellement certaines propriétés du type de données vidéo. Configurez-le pour indiquer une norme d’espace colorimétrique de « Rec 601 ». Le composant Video Data Type Updater ajoute alors au flux un indicateur d’espace colorimétrique « Rec 601 » si aucun espace colorimétrique n’est encore défini (les métadonnées existantes ne seront pas écrasées, sauf si la case indiquant un écrasement est activée).

![Mise à jour de la norme d’espace colorimétrique sur le composant de mise à jour du type de données](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Mise à jour de la norme d’espace colorimétrique sur le composant de mise à jour du type de données*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Worflow terminé
Maintenant que notre workflow est terminé, effectuez un autre test pour vérifier s’il s’exécute avec succès.

![Flux de travail terminé pour la sortie MP4 multidébit avec miniatures](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Flux de travail terminé pour la sortie MP4 multidébit avec miniatures*

## <a id="time_based_trim"></a>Découpage temporel du fichier de sortie MP4 multidébit
À partir d’un workflow qui génère [une sortie MP4 multidébit à partir d’une entrée MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant examiner la question du découpage de la vidéo source selon un horodatage.

### <a id="time_based_trim_start"></a>Vue d’ensemble du workflow pour l’ajout du découpage
![Démarrage du workflow pour l’ajout d’un découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Démarrage du workflow pour l’ajout d’un découpage*

### <a id="time_based_trim_use_stream_trimmer"></a>Utilisation du composant Stream Trimmer
Le composant Stream Trimmer permet de découper le début et la fin d’un flux d’entrée en fonction d’informations temporelles (secondes, minutes, etc.). Il ne prend pas en charge le découpage par images.

![Composant Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Composant Stream Trimmer*

Au lieu de lier directement les encodeurs AVC et le composant d’affectation de position du haut-parleur au composant Media File Input, nous allons insérer entre eux le composant Stream Trimmer (un pour le signal vidéo, un autre pour le signal audio entrelacé).

![Insertion du composant Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Insertion du composant Stream Trimmer*

Nous allons maintenant configurer le composant de découpage afin de traiter uniquement les données vidéo et audio contenues dans l’intervalle de 15 à 60 secondes du flux vidéo.

Accédez aux propriétés du composant Video Stream Trimmer et configurez l’heure de début (15 s) et l’heure de fin (60 s). Pour avoir la certitude que nos composants de découpage audio et vidéo seront toujours configurés avec les mêmes valeurs de début et de fin, nous les publions à la racine du workflow.

![Publication de la propriété d’heure de début à partir du composant Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publication de la propriété d’heure de début à partir du composant Stream Trimmer*

![Boîte de dialogue des propriétés de publication pour l’heure de début](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Boîte de dialogue des propriétés de publication pour l’heure de début*

![Boîte de dialogue des propriétés de publication pour l’heure de fin](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Boîte de dialogue des propriétés de publication pour l’heure de fin*

Si nous observons à présent la racine de notre workflow, les deux propriétés y apparaissent clairement. Vous pouvez les configurer à partir de cet emplacement.

![Propriétés publiées disponibles sur la racine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriétés publiées disponibles sur la racine*

Ouvrez maintenant les propriétés de découpage à partir du composant de découpage audio et configurez les heures de début et de fin avec une expression faisant référence aux propriétés publiées sur la racine de notre workflow.

Pour l’heure de début du découpage audio :

    ${ROOT_TrimmingStartTime}

et pour son heure de fin :

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Worflow terminé
![Worflow terminé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Worflow terminé*

## <a id="scripting"></a>Présentation du composant de script
Les composants de script peuvent exécuter des scripts arbitraires au cours des phases d’exécution de notre workflow. Quatre différents scripts peuvent être exécutés, chacun présentant des caractéristiques spécifiques et occupant une place bien définie dans le cycle de vie du workflow :

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

La documentation du composant de script est détaillée pour chacun des éléments ci-dessus. Dans [la section suivante](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), le composant de script **realizeScript** est utilisé pour construire un fichier XML de liste de séquences à la volée lors du démarrage du workflow. Ce script est appelé lors de l’installation du composant, autrement dit une seule fois au cours de son cycle de vie.

### <a id="scripting_hello_world"></a>Écriture de scripts dans un workflow : Hello World
Faites glisser un composant de script sur la surface du concepteur et renommez-le (par exemple, « SetClipListXML »).

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant de script, les quatre types de scripts différents s’affichent, chacun pouvant être configuré pour un autre script.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*

Désactivez le composant processInputScript et ouvrez l’éditeur pour le composant realizeScript. Nous sommes maintenant prêts à commencer l’écriture de scripts.

Les scripts sont écrits en Groovy, un langage de script compilé de manière dynamique pour la plateforme Java qui conserve la compatibilité avec Java. En fait, la plupart des codes Java sont considérés comme du code Groovy valide.

Nous allons écrire un simple script Groovy Hello World dans le contexte de notre composant realizeScript. Entrez les paramètres suivants dans l’éditeur :

    node.log("hello world");

Exécutez maintenant une série de tests en local. Une fois les tests effectués, inspectez la propriété des journaux (via l’onglet Système sur le composant de script).

![Sortie du journal Hello World](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Sortie du journal Hello World*

L’objet de nœud sur lequel nous appelons la méthode de journalisation fait référence à notre « nœud » actif ou au composant dans lequel nous allons écrire le script. Chaque composant en tant que tel est en mesure de générer des données de journalisation, lesquelles sont disponibles via l’onglet Système. Dans ce cas, nous générons la chaîne littérale « hello world ». Il est important ici de comprendre ici que cette chaîne peut s’avérer très utile pour le débogage, car elle fournit des informations sur l’action réelle exécutée par le script.

À partir de notre environnement de script, nous avons également accès aux propriétés d’autres composants. Procédez comme suit :

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

La fenêtre du journal présente les éléments suivants :

![Sortie de journal pour l’accès aux chemins du nœud](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Sortie de journal pour l’accès aux chemins du nœud*

## <a id="frame_based_trim"></a>Découpage par images du fichier de sortie MP4 multidébit
À partir d’un workflow qui génère [une sortie MP4 multidébit à partir d’une entrée MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), nous allons maintenant examiner la question du découpage de la vidéo source selon le nombre d’images.

### <a id="frame_based_trim_start"></a>Vue d’ensemble du plan pour l’ajout du découpage
![Workflow pour l’ajout du découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Workflow pour l’ajout du découpage*

### <a id="frame_based_trim_clip_list"></a>Utilisation du fichier XML Clip List
Dans tous les didacticiels de workflow précédents, nous avons utilisé le composant Media File Input comme source d’entrée vidéo. Pour ce scénario, en revanche, nous allons utiliser le composant Clip List Source. Cette approche n’est pas recommandée ; utilisez uniquement le composant Clip List Source lorsque cela est véritablement justifié (comme dans le cas suivant, où nous utilisons les fonctionnalités de découpage d’une liste de séquences).

Pour passer du composant Media File Input au composant Clip List Source, faites glisser le composant Clip List Source sur la surface du concepteur et connectez la broche Clip List XML au nœud Clip List XML du concepteur de workflow. Le composant Clip List Source comporte maintenant des broches de sortie correspondant à notre vidéo d’entrée. Connectez à présent les broches Uncompressed Video et Uncompressed Audio du composant Clip List Source à leurs encodeurs AVC respectifs et à l’entrelaceur de flux audio. Supprimez maintenant le composant Media File Input.

![Remplacement du composant Media File Input par le composant Clip List Source](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Remplacement du composant Media File Input par le composant Clip List Source*

Le composant Clip List Source utilise un « fichier XML de liste de séquences » comme donnée d’entrée. Lorsque vous sélectionnez le fichier source pour le test en local, ce fichier xml de liste de séquences est automatiquement renseigné.

![Propriété du fichier XML de liste de séquences automatiquement renseigné](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriété du fichier XML de liste de séquences automatiquement renseigné*

En examinant de plus près le fichier XML, nous obtenons quelque chose de similaire à ce qui suit :

![Boîte de dialogue de modification de la liste de séquences](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Boîte de dialogue de modification de la liste de séquences*

Cela ne reflète toutefois pas les fonctionnalités du fichier XML de liste de séquences. L’une des options consiste à ajouter un élément « Découpage » sous la source vidéo et la source audio, comme suit :

![Ajout d’un élément de découpage à la liste de séquences](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Ajout d’un élément de découpage à la liste de séquences*

Si vous modifiez le fichier XML de liste de séquences comme indiqué ci-dessus et que vous effectuez une série de tests en local, la vidéo sera coupée dans l’intervalle 10 à 20 secondes du flux vidéo.

Mais contrairement au résultat obtenu lors d’une exécution en local, ce fichier XML de liste de séquences aurait le même effet s’il était appliqué dans un workflow exécuté sous Azure Media Services. Au démarrage d’Azure Premium Encoder, le fichier XML de liste de séquences est de nouveau généré à chaque fois, compte tenu du fichier d’entrée affecté à la tâche d’encodage. Cela signifie que toutes les modifications apportées au fichier XML seront malheureusement écrasées.

Pour éviter d’écraser le fichier XML de liste de séquences au démarrage d’une tâche d’encodage, nous pouvons le régénérer à la volée immédiatement après le démarrage de notre workflow. Ces actions personnalisées peuvent être exécutées via ce que l’on appelle un « composant de script ». Pour plus d’informations, consultez le paragraphe [Présentation du composant de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Faites glisser un composant de script sur la surface du concepteur et renommez-le « SetClipListXML ».

![Ajout d’un composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Ajout d’un composant de script*

Lorsque vous examinez les propriétés du composant de script, les quatre types de scripts différents s’affichent, chacun pouvant être configuré pour un autre script.

![Propriétés du composant de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriétés du composant de script*

### <a id="frame_based_trim_modify_clip_list"></a>Modification de la liste de séquences à partir d’un composant de script
Avant de pouvoir réécrire le fichier XML de liste de séquences généré au cours du démarrage du workflow, nous devons accéder à la propriété et au contenu du fichier XML de liste de séquences. Pour cela, nous pouvons procéder de la manière suivante :

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Liste de séquences entrantes consignée dans le journal](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Liste de séquences entrantes consignée dans le journal*

Nous devons tout d’abord trouver un moyen de déterminer les points de début et de fin à utiliser pour le découpage de la vidéo. Pour rendre cette procédure plus accessibles aux utilisateurs moins techniques, publiez deux propriétés à la racine du graphique. Pour ce faire, cliquez avec le bouton droit sur la surface du concepteur et sélectionnez « Ajouter une propriété » :

* Première propriété : « ClippingTimeStart » de type : « TIMECODE »
* Deuxième propriété : « ClippingTimeEND » de type : « TIMECODE »

![Boîte de dialogue Ajouter une propriété pour l’heure de début du découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Boîte de dialogue Ajouter une propriété pour l’heure de début du découpage*

![Propriétés de temps de découpage publiées sur la racine du workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Propriétés de temps de découpage publiées sur la racine du workflow*

Configurez les deux propriétés sur une valeur appropriée :

![Configuration des propriétés de démarrage et de fin du découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configuration des propriétés de démarrage et de fin du découpage*

À partir de dans notre script, nous pouvons désormais accéder aux deux propriétés, comme suit :

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Fenêtre de journal indiquant le début et la fin du découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Fenêtre de journal indiquant le début et la fin du découpage*

Nous allons analyser les chaînes de code temporel sous une forme plus conviviale, à l’aide d’une expression régulière simple :

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Fenêtre de journal avec la sortie du code temporel analysé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Fenêtre de journal avec la sortie du code temporel analysé*

Avec cette information en main, nous pouvons maintenant modifier le fichier XML de liste de séquences pour refléter les heures de début et de fin du découpage par images du film.

![Code de script permettant d’ajouter des éléments de découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Code de script permettant d’ajouter des éléments de découpage*

Nous avons utilisé pour cela des opérations normales de manipulation de chaîne. Le fichier XML de liste de séquences modifié que nous avons obtenu est réécrit pour la propriété clipListXML à la racine du workflow via la méthode « setProperty ». La fenêtre de journal après l’exécution d’un autre test nous indique les éléments suivants :

![Consignation de la liste de séquences obtenue](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Consignation de la liste de séquences obtenue*

Effectuez une série de tests pour voir la manière dont les flux vidéo et audio ont été découpés. Lorsque vous effectuerez plusieurs séries de tests avec des valeurs différentes pour les points de découpage, vous remarquerez que ceux ne seront pas pris en compte ! Ceci s’explique par le fait que le concepteur, contrairement à l’exécution Azure, n’écrase PAS le fichier XML de liste de séquences à chaque exécution. Autrement dit, le fichier XML ne sera transformé que la première fois où vous définissez les points d’entrée et de sortie. Toutes les autres fois, notre clause de garde (if(clipListXML.indexOf("<trim>") == -1)) empêchera le workflow d’ajouter un élément de découpage si un autre est déjà présent.

Pour faciliter le test en local de notre workflow, le mieux est d’ajouter du code d’entretien qui vérifie la présence préalable d’un élément de découpage. Si tel est le cas, nous pouvons supprimer cet élément avant de modifier le fichier XML avec les nouvelles valeurs. Au lieu d’utiliser de simples manipulations de chaînes, il est probablement plus prudent de procéder à une analyse du modèle d’objet XML réel.

Mais avant de pouvoir ajouter ce code, nous devons d’abord ajouter un certain nombre d’instructions d’importation au début de notre script :

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Après quoi, nous pouvons ajouter le code de nettoyage requis :

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Ce code doit être inséré juste au-dessus du point auquel nous ajoutons les éléments de découpage au fichier XML de liste de séquences.

À ce stade, nous pouvons exécuter et modifier notre workflow à loisir tout en ayant la garantie que les modifications seront appliquées à chaque fois.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Ajout d’une propriété de commodité ClippingEnabled
Il n’est pas toujours judicieux d’appliquer un découpage. Aussi, nous allons terminer notre workflow en ajoutant un indicateur booléen pratique qui indique si nous souhaitons ou non activer le découpage.

Comme auparavant, publiez à la racine de notre workflow une nouvelle propriété appelée « ClippingEnabled » de type « BOOLEAN ».

![Publication d’une propriété activant le découpage](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publication d’une propriété activant le découpage*

Avec la simple clause de garde ci-dessus, nous pouvons vérifier si le découpage est ou non requis et déterminer si notre liste de séquences doit ou non être modifiée.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Code complet

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Voir aussi
[Présentation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Utilisation de l’encodage Premium dans Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Encodage de contenu à la demande avec Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)

[Codecs et formats de Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Exemples de fichiers de workflow](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Outil Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
