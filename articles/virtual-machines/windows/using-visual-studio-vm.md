---
title: Utilisation de Visual Studio sur une machine virtuelle Azure | Microsoft Docs
description: Utilisation de Visual Studio sur une machine virtuelle Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 599a890be4d014d22bae899be4cf6e281c4109d4
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a id="top"> </a> Images de Visual Studio sur Azure
Exécuter Visual Studio sur une machine virtuelle Azure préconfigurée est le moyen le plus simple et le plus rapide de créer un environnement de développement opérationnel à partir de rien.  Des images système avec différentes configurations de Visual Studio sont disponibles sur la [Place de Marché Azure](https://portal.azure.com/). Il suffit de démarrer une machine virtuelle, et voilà !

Vous êtes un nouvel utilisateur d’Azure ? [Créer un compte Azure gratuit](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Quelles sont les configurations et les versions disponibles ?
Sur la Place de Marché Azure sont disponibles des images pour les dernières versions principales : Visual Studio 2017 et Visual Studio 2015.  Pour chaque version principale figurent la version initialement publiée (également appelée « RTW ») et les versions mises à jour les plus récentes.  Pour chacune de ces différentes versions, il existe des éditions Visual Studio Enterprise et Visual Studio Community.  Nous mettre à jour ces images au moins chaque mois pour inclure les dernières mises à jour Visual Studio et Windows.  Bien que les noms des images restent identiques, la description de chaque image inclut la version du produit installée et la date de création de l’image.

|               Version commerciale              |          Éditions            |     Version du produit     |
|:------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017 - Dernière version (version 15.5) |    Enterprise, Community     |      Version 15.5.3     |
|         Visual Studio 2017 - RTW           |    Enterprise, Community     |      Version 15.0.7     |
|   Visual Studio 2015 - Dernière version (Update 3)   |    Enterprise, Community     |  Version 14.0.25431.01  |
|         Visual Studio 2015 - RTW           |              Aucun            | (Expiration pour maintenance) |

> [!NOTE]
> Conformément à la politique de maintenance de Microsoft, la version initialement publiée (version « RTW ») de Visual Studio 2015 a expiré pour la maintenance.  Par conséquent, Visual Studio 2015 Update 3 est la seule version restante proposée pour la ligne de produits Visual Studio 2015.

Pour plus d’informations, consultez la [politique de maintenance de Visual Studio](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Quelles sont les fonctionnalités installées ?
Chaque image contient le jeu de fonctionnalités recommandé pour cette édition de Visual Studio.  En règle générale, l’installation comprend :

* Toutes les charges de travail disponibles, notamment les composants facultatifs recommandés de cette charge de travail.
* SDK .NET 4.6.2 et .NET 4.7, packs de ciblage et outils de développement
* Visual F#
* Extension GitHub pour Visual Studio
* Outils LINQ to SQL

Voici la ligne de commande que nous utilisons pour installer Visual Studio lors de la création des images :

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Si les images ne comprennent pas une fonctionnalité de Visual Studio dont vous avez besoin, faites-nous part de vos commentaires par le biais de l’outil de commentaires (en haut à droite de la page).

## <a name="what-size-vm-should-i-choose"></a>Quelle taille de machine virtuelle dois-je choisir ?
Il est facile de provisionner une machine virtuelle, et Azure propose une gamme complète de tailles de machine virtuelle.  Comme avec n’importe quelle acquisition de matériel, vous devez trouver un équilibre entre les performances et les coûts.  Visual Studio étant une application puissante et multithread, il faut une taille de machine virtuelle qui comprend au moins 2 processeurs et 7 Go de mémoire.  Voici les tailles de machines virtuelles recommandées pour les images Visual Studio :

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Pour plus d’informations sur les tailles de machine les plus récentes, consultez [Tailles des machines virtuelles Windows dans Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Avec Azure, vous n’êtes pas obligé de conserver votre premier choix : vous pouvez rééquilibrer votre choix initial en redimensionnant la machine virtuelle.  Vous pouvez soit provisionner une nouvelle machine virtuelle avec une taille plus appropriée, soit redimensionner votre machine virtuelle existante sur un autre matériel sous-jacent.  Pour plus d’informations, consultez [Redimensionner une machine virtuelle Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>Que faire une fois la machine virtuelle opérationnelle ?
Visual Studio suit le modèle « BYOL (apportez votre propre licence) » dans Azure.  Ainsi, comme pour une installation sur du matériel propriétaire, l’une des premières étapes est l’attribution d’une licence à votre installation Visual Studio.  Vous pouvez déverrouiller Visual Studio en vous connectant avec un compte Microsoft associé à un abonnement Visual Studio, ou avec la clé de produit fournie avec votre achat initial.  Pour plus d’informations, consultez [Se connecter à Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) et [Guide pratique pour déverrouiller Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>Après avoir généré la machine virtuelle de développement, comment faire pour l’enregistrer (capturer) en vue d’une utilisation ultérieure ou en équipe ?

Le spectre des environnements de développement est énorme, et la création d’environnements plus complexes a un coût réel.  Toutefois, quelle que soit la configuration de votre environnement, Azure simplifie la préservation de cet investissement en enregistrant/capturant votre machine virtuelle configurée parfaitement en tant qu’image de base en vue d’une utilisation ultérieure, pour vous-même ou pour d’autres membres de votre équipe.  Ensuite, quand vous démarrez une nouvelle machine virtuelle, vous pouvez la provisionner à partir de l’image de base au lieu de l’image de la Place de Marché.

En bref, vous devrez préparer (avec sysprep) et arrêter la machine virtuelle en cours d’exécution, puis *capturer (Figure 1)* la machine virtuelle en tant qu’image par le biais de l’interface utilisateur du portail Azure.  Azure enregistre le fichier `.vhd` qui contient l’image dans le compte de stockage de votre choix.  Ensuite, la nouvelle image apparaîtra en tant que ressource d’image dans la liste des ressources de votre abonnement.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figure 1) Capturer une image par le biais de l’interface utilisateur du portail Azure.*</center>

Pour plus d’informations, consultez [Capture d’une machine virtuelle vers une image](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Rappel :** N’oubliez pas d’exécuter sysprep sur la machine virtuelle.  Si vous omettez cette étape, Azure ne peut pas provisionner une machine virtuelle à partir de l’image.

> [!NOTE]
> Des frais vous sont quand même facturés pour le stockage des images, mais ces frais incrémentiels seront probablement négligeables par rapport aux frais de personnel nécessaires pour recréer la machine virtuelle à partir de rien, pour chaque personne de votre équipe ayant besoin d’une machine virtuelle.  Par exemple, cela coûte quelques dollars de créer et de stocker pendant un mois une image de 127 Go réutilisable par tous les membres de votre équipe.  Toutefois, ces coûts sont négligeables par rapport à toutes les heures investies par chaque employé pour créer et valider une zone de développement configurée correctement pour son usage individuel.

En outre, vos tâches de développement ou technologies peuvent nécessiter une mise à l’échelle (par exemple différentes variétés de configurations de développement et plusieurs configurations d’ordinateurs).  Vous pouvez utiliser Azure DevTest Labs pour créer des _recettes_ qui automatisent la construction de votre « image en or », et pour gérer les stratégies des machines virtuelles de votre équipe.  [Utiliser Azure DevTest Labs pour développeurs](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) est la meilleure source pour obtenir plus d’informations sur DevTest Labs.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur les images Visual Studio préconfigurées, l’étape suivante consiste à créer une machine virtuelle :

* [Créer une machine virtuelle avec le portail Azure](quick-create-portal.md)
* [Vue d’ensemble des machines virtuelles Windows](overview.md)
