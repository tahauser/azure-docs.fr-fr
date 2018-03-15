---
title: "Créer des artefacts personnalisés pour votre machine virtuelle DevTest Labs | Microsoft Docs"
description: "Découvrez comment créer vos propres artefacts pour les utiliser avec Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: v-craic
ms.openlocfilehash: 7766227d66df94eca72072f52ff02928f8ee277b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Créer des artefacts personnalisés pour votre machine virtuelle DevTest Labs

Regardez la vidéo suivante pour avoir une vue d’ensemble de la procédure décrite dans cet article :

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Vue d’ensemble
Vous pouvez utiliser des *artefacts* pour déployer et configurer votre application après avoir approvisionné une machine virtuelle. Un artefact se compose d’un fichier de définition d’artefact et autres fichiers de script qui sont stockés dans un dossier de dépôt Git. Les fichiers de définition d'artefact se composent de JSON et d'expressions que vous pouvez utiliser pour spécifier ce que vous voulez installer sur une machine virtuelle. Par exemple, vous pouvez définir le nom d’un artefact, une commande à exécuter et des paramètres disponibles quand la commande est exécutée. Vous pouvez faire référence à d'autres fichiers de script dans le fichier de définition d'artefact par nom.

## <a name="artifact-definition-file-format"></a>Format de fichier de définition d'artefact
L'exemple suivant indique les sections qui composent la structure de base d'un fichier de définition :

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nom de l'élément | Requis ? | Description |
| --- | --- | --- |
| $schema |Non  |Emplacement du fichier de schéma JSON. Le fichier de schéma JSON peut vous aider à tester la validité du fichier de définition. |
| title |OUI |Nom de l'artefact affiché dans le laboratoire. |
| description |OUI |Description de l'artefact affiché dans le laboratoire. |
| iconUri |Non  |URI de l’icône affichée dans le laboratoire. |
| targetOsType |OUI |Système d’exploitation de la machine virtuelle où l’artefact est installé. Les options prises en charge sont Windows et Linux. |
| parameters |Non  |Valeurs fournies quand la commande d’installation d’artefact est exécutée sur une machine. Cela vous permet de personnaliser votre artefact. |
| runCommand |OUI |Commande d'installation d'artefact qui est exécutée sur une machine virtuelle. |

### <a name="artifact-parameters"></a>Paramètres d'artefact
Dans la section des paramètres du fichier de définition, spécifiez les valeurs qu’un utilisateur peut entrer quand il installe un artefact. Vous pouvez faire référence à ces valeurs dans la commande d'installation d'artefact.

Pour définir des paramètres, utilisez la structure suivante :

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nom de l'élément | Requis ? | Description |
| --- | --- | --- |
| Type |OUI |Type de la valeur du paramètre. Consultez la liste suivante des types autorisés. |
| displayName |OUI |Nom du paramètre qui est affiché à un utilisateur dans le laboratoire. | |
| description |OUI |Description du paramètre qui est affiché dans le laboratoire. |

Les types autorisés sont :

* string (n’importe quelle chaîne JSON valide)
* int (n’importe quel entier JSON valide)
* bool (n’importe quel booléen JSON valide)
* array (n’importe quel tableau JSON valide)

## <a name="artifact-expressions-and-functions"></a>Expressions et fonctions d'artefact
Vous pouvez utiliser des expressions et des fonctions pour construire la commande d’installation d’artefact.
Les expressions sont placées entre crochets ([ et ]) et sont évaluées au moment où l'artefact est installé. Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON. Les expressions retournent toujours une autre valeur JSON. Si vous avez besoin d’utiliser une chaîne littérale qui commence par un crochet ([), vous devez utiliser deux crochets ([[).
En général, vous utilisez des expressions avec des fonctions pour construire une valeur. Comme en JavaScript, les appels de fonctions sont formatés ainsi : **functionName(arg1,arg2,arg3)**.

La liste suivante indique les fonctions courantes :

* **parameters(parameterName)** : retourne une valeur de paramètre qui est fournie à l’exécution de la commande de l’artefact.
* **concat(arg1,arg2,arg3, …..)** : combine plusieurs valeurs de chaîne. Cette fonction peut prendre de nombreux arguments.

L’exemple suivant indique comment utiliser les fonctions et les expressions pour construire une valeur :

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Création d'un artefact personnalisé

1. Installer un éditeur JSON. Vous avez besoin d’un éditeur JSON pour utiliser les fichiers de définition d’artefact. Nous vous recommandons d’utiliser [Visual Studio Code](https://code.visualstudio.com/), qui est disponible pour Windows, Linux et OS X.
2. Obtenir un exemple de fichier de définition artifactfile.json. Découvrez les artefacts créés par l’équipe DevTest Labs dans notre [dépôt GitHub](https://github.com/Azure/azure-devtestlab). Nous avons créé une bibliothèque d’artefacts riche qui vous permet de créer vos propres artefacts. Téléchargez un fichier de définition d'artefact et modifiez-le pour créer vos propres artefacts.
3. Utiliser IntelliSense. Utilisez IntelliSense pour voir des éléments valides que vous pouvez utiliser pour construire un fichier de définition d’artefact. Vous pouvez également voir les différentes options pour les valeurs d’un élément. Par exemple, quand vous modifiez l’élément **targetOsType**, IntelliSense vous montre deux options, pour Windows ou Linux.
4. Stocker l’artefact dans un [dépôt Git](devtest-lab-add-artifact-repo.md).
   
   1. Créez un répertoire distinct pour chaque artefact. Le nom du répertoire doit être le même que le nom de l’artefact.
   2. Stockez le fichier de définition d’artefact (artifactfile.json) dans le répertoire que vous avez créé.
   3. Stockez les scripts qui sont référencés à partir de la commande d'installation d'artefact.
      
      Voici un exemple de dossier d'artefact :
      
      ![Exemple de dossier d’artefact](./media/devtest-lab-artifact-author/git-repo.png)
5. Ajouter le dépôt d’artefacts au laboratoire. Consultez [Ajouter un dépôt Git des artefacts et des modèles](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Articles connexes
* [Guide pratique pour diagnostiquer les échecs d’artefact dans DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Joindre une machine virtuelle à un domaine Active Directory existant à l’aide d’un modèle Resource Manager dans DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter un dépôt d’artefacts Git à un laboratoire](devtest-lab-add-artifact-repo.md).

