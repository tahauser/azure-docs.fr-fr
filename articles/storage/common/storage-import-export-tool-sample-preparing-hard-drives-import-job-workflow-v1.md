---
title: "Exemple de workflow pour préparer des disques durs pour un travail d’importation Azure Import/Export - v1 | Microsoft Docs"
description: "Obtenez la procédure pas à pas relative au processus de préparation des disques à un travail d’importation dans le service Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 66e85bd3e9e43ae360d0507f5bdf3596abbeb7d1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Exemple de workflow pour préparer des disques durs à un travail d’importation
Cette rubrique vous guide tout au long du processus de préparation des disques pour un travail d’importation.  
  
Cet exemple importe les données suivantes dans un compte de stockage Azure Windows nommé `mystorageaccount` :  
  
|Lieu|Description|  
|--------------|-----------------|  
|H:\Video|Collection de vidéos, 5 To au total.|  
|H:\Photo|Collection de photos, 30 Go au total.|  
|K:\Temp\FavoriteMovie.ISO|Image de disque Blu-ray™, 25 Go.|  
|\\\bigshare\john\music|Collection de fichiers de musique sur un partage réseau, 10 Go au total.|  
  
Le travail d’importation importe ces données dans les destinations suivantes dans le compte de stockage :  
  
|Source|Répertoire virtuel ou objet blob de destination|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
Avec ce mappage, le fichier `H:\Video\Drama\GreatMovie.mov` est importé dans l’objet blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Ensuite, pour déterminer le nombre de disques durs nécessaires, calculez la taille des données :  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Pour cet exemple, deux disques durs de 3 To devraient suffire. Cependant, comme le répertoire source `H:\Video` contient 5 To de données et que la capacité de votre disque dur unique est de 3 To seulement, il est nécessaire de diviser `H:\Video` en deux répertoires plus petits (`H:\Video1` et `H:\Video2`) avant d’exécuter l’outil Microsoft Azure Import/Export. Cette étape génère les répertoires sources suivants :  
  
|Lieu|Taille|Répertoire virtuel ou objet blob de destination|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 To|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 To|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 Go|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 Go|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 Go|https://mystorageaccount.blob.core.windows.net/music|  
  
 Même si le répertoire `H:\Video` a été divisé en deux répertoires, ils pointent tous deux vers le même répertoire virtuel de destination dans le compte de stockage. De cette façon, tous les fichiers vidéo sont conservés dans un seul conteneur `video` au sein du compte de stockage.  
  
 Ensuite, les répertoires sources précédents sont répartis uniformément sur les deux disques durs :  
  
||||  
|-|-|-|  
|Disque dur|Répertoires sources|Taille totale|  
|Premier disque|H:\Video1|2,5 To + 30 Go|  
||H:\Photo||  
|Deuxième disque|H:\Video2|2,5 To + 35 Go|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
En outre, vous pouvez définir les métadonnées suivantes pour tous les fichiers :  
  
-   **UploadMethod :** Service Windows Azure Import/Export  
  
-   **DataSetName :** SampleData  
  
-   **CreationDate :** 10/1/2013  
  
Pour définir les métadonnées pour les fichiers importés, créez un fichier texte, `c:\WAImportExport\SampleMetadata.txt`, avec le contenu suivant :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Vous pouvez également définir des propriétés pour l’objet blob `FavoriteMovie.ISO` :  
  
-   **Content-Type :** application/octet-stream  
  
-   **Content-MD5 :** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control :** no-cache  
  
Pour définir ces propriétés, créez un fichier texte, `c:\WAImportExport\SampleProperties.txt` :  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Vous êtes maintenant prêt à exécuter l’outil Azure Import/Export pour préparer les deux disques durs. Notez les points suivants :  
  
-   Le premier disque est monté en tant que disque X.  
  
-   Le deuxième disque est monté en tant que disque Y.  
  
-   La clé du compte de stockage `mystorageaccount` est `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Préparation du disque à l’importation lorsque des données sont pré-chargées
 
 Si les données à importer sont déjà présentes sur le disque, utilisez l’indicateur /skipwrite. La valeur de /t et de /srcdir doit pointer vers le disque préparé pour l’importation. Si les données à importer ne vont pas toutes dans le même répertoire virtuel de destination ou la même racine du compte de stockage, exécutez la même commande pour chaque répertoire de destination séparément en conservant la même valeur /id pour toutes les exécutions.

>[!NOTE] 
>Ne spécifiez pas la valeur /format car elle efface les données sur le disque. Vous pouvez spécifier la valeur /encrypt ou /bk selon que le disque est déjà chiffré ou non. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Session de copie - premier lecteur

Pour le premier disque, exécutez l’outil Azure Import/Export deux fois pour copier les deux répertoires sources :  

**Première session de copie**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Deuxième session de copie**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Session de copie - deuxième lecteur
 
Pour le deuxième disque, exécutez l’outil Azure Import/Export trois fois (une fois pour chaque répertoire source et une fois pour le fichier d’image Blu-Ray™ autonome) :  
  
**Première session de copie** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Deuxième session de copie**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Troisième session de copie**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Fin de la session de copie

Une fois les sessions de copie terminées, vous pouvez déconnecter les deux disques de l’ordinateur de copie et les expédier au centre de données Windows Azure approprié. Téléchargez les deux fichiers journaux (`FirstDrive.jrn` et `SecondDrive.jrn`) au moment de la création du travail d’importation dans le [portail Azure](https://portal.azure.com).  
  
## <a name="next-steps"></a>Étapes suivantes

* [Préparation des disques durs pour un travail d’importation](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Référence rapide pour les commandes fréquemment utilisées](../storage-import-export-tool-quick-reference-v1.md) 
