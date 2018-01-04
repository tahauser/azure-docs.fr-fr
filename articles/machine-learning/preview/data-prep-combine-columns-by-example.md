---
title: "Transformation Combiner des colonnes par exemple à l’aide d’Azure Machine Learning Workbench"
description: "Document de référence pour la transformation « Combiner des colonnes par exemple »"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: dc3870e81edd47a9932db86d347290451c4f1eb8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="combine-columns-by-example-transformation"></a>Transformation Combiner des colonnes par exemple
Cette transformation permet à l’utilisateur d’ajouter une nouvelle colonne en combinant les valeurs de plusieurs colonnes. L’utilisateur peut spécifier un séparateur ou fournir des exemples de valeurs combinées pour effectuer cette transformation. Quand l’utilisateur fournit des exemples de combinaison, la transformation est gérée par le même moteur **Par exemple** que celui utilisé avec la transformation **Dériver des colonnes par exemple**.

## <a name="how-to-perform-this-transformation"></a>Comment effectuer cette transformation

Pour effectuer cette transformation, exécutez les étapes suivantes :
1. Sélectionnez plusieurs colonnes que vous souhaitez combiner. 
2. Sélectionnez **Combiner des colonnes par exemple** dans le menu **Transformations**. Vous pouvez aussi cliquer avec le bouton droit sur l’en-tête de la colonne sélectionnée et sélectionner **Combiner des colonnes par exemple** dans le menu contextuel. L’Éditeur de transformation s’ouvre et une nouvelle colonne est ajoutée en regard de la colonne sélectionnée la plus à droite. La nouvelle colonne contient les valeurs combinées, séparées par un séparateur par défaut. Les colonnes sélectionnées peuvent être identifiées par les cases à cocher dans les en-têtes de colonnes. Vous pouvez ajouter des colonnes à la sélection (et en supprimer) à l’aide des cases à cocher.
3. Vous pouvez mettre à jour la valeur combinée dans la colonne nouvellement créée. La valeur mise à jour est utilisée comme exemple pour apprendre la transformation.
4. Cliquez sur **OK** pour accepter la transformation.

### <a name="transform-editor-advanced-mode"></a>Éditeur de transformation : mode avancé

Le Mode avancé fournit une expérience de combinaison de colonnes enrichie. 

La sélection de **Separator** (Séparateur) sous **Combine Columns by** (Combiner les colonnes par) permet à l’utilisateur de spécifier des chaînes dans la zone de texte **Separator**. Appuyez sur la touche Tab pour sortir de la zone de texte **Separator** et afficher un aperçu des résultats dans la grille de données. Appuyez sur **OK** pour valider la transformation.

La sélection de **Examples** sous **Combine Columns by** (Combiner les colonnes par) permet à l’utilisateur de fournir des exemples de valeurs combinées. Pour promouvoir une ligne en exemple, double-cliquez dessus dans la grille. Tapez la sortie attendue dans la zone de texte associée à la ligne promue. Appuyez sur la touche Tab pour sortir de la zone de texte **Separator** et afficher un aperçu des résultats dans la grille de données. Appuyez sur **OK** pour valider la transformation. 

L’utilisateur peut basculer entre le **Mode de base** et le **Mode avancé** en cliquant sur les liens dans l’Éditeur de transformation.

### <a name="editing-existing-transformation"></a>Modification d’une transformation existante

Un utilisateur peut modifier une transformation **Combiner des colonnes par exemple** existante en sélectionnant l’option **Edit** (Modifier) à l’étape Transformation. Un clic sur **Edit** (Modifier) ouvre l’Éditeur de transformation en **Mode de base**. L’utilisateur peut basculer en **Mode avancé** en cliquant sur le lien dans l’en-tête. Tous les exemples fournis lors de la création de la transformation sont affichés.

## <a name="example-using-separators"></a>Exemple d’utilisation de séparateurs

Une virgule suivie d’un espace est utilisée comme séparateur dans cet exemple pour combiner les colonnes *Rue*, *Ville*, *État* et *Code postal*.

|Rue|City|État|ZIP|Colonne|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Exemple utilisant Par exemple

La valeur en **gras** a été fournie comme exemple.

|Date|Mois|Year|Hour|Minute|Seconde|Colonne combinée|
|:----|:----|:----|:----|:----|:----|:----|
|13.|Oct|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|Oct|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|Oct|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mars|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Avr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

