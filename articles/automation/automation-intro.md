---
title: "Présentation d’Azure Automation | Microsoft Docs"
description: "Découvrez les avantages d’Azure Automation et obtenez des réponses aux questions courantes afin de commencer à créer et à utiliser des Runbooks et Azure Automation DSC."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: "présentation d’automatisation, azure automation, exemples d’applications d’azure automation"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
ms.openlocfilehash: ae2b607be9a02e688a6b513d593f79eeb985ca93
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-overview"></a>Vue d’ensemble d’Azure Automation
Microsoft Azure Automation permet aux utilisateurs d'automatiser les tâches répétitives, manuelles, de longue durée et susceptibles d'engendrer des erreurs, qui sont communément exécutées dans un environnement cloud et d’entreprise. Il fait gagner du temps, accroît la fiabilité des tâches d'administration récurrentes et planifie même leur exécution automatique à intervalles réguliers. Vous pouvez automatiser les processus à l'aide de Runbooks ou automatiser la gestion de configuration avec la Configuration de l'état souhaité (DSC, Desired State Configuration). Cet article propose une brève présentation d’Azure Automation et apporte des réponses à certaines questions courantes. Vous pouvez vous référer à d'autres articles de cette bibliothèque pour des informations plus détaillées sur les différentes rubriques.

## <a name="automating-processes-with-runbooks"></a>Automatisation des processus avec des Runbooks
Un Runbook est un ensemble de tâches qui exécutent des processus automatisés dans Azure Automation. Il peut s’agir d’un processus simple, comme le démarrage d’une machine virtuelle et la création d’une entrée de journal, ou d’un Runbook complexe combinant d’autres Runbooks plus petits pour effectuer un processus complexe sur plusieurs ressources, voire plusieurs clouds et environnements locaux.  

Par exemple, vous pouvez avoir un processus manuel existant, pour tronquer une base de données SQL si elle approche de la taille maximale, qui comprend plusieurs étapes comme la connexion au serveur, la connexion à la base de données, la récupération de la taille actuelle de la base de données, la vérification de dépassement du seuil, puis la troncation et la notification de l'utilisateur. Au lieu d'exécuter manuellement chacune de ces étapes, vous pouvez créer un Runbook qui effectue toutes ces tâches comme un processus unique. Vous démarrez le Runbook, fournissez les informations requises comme le nom du serveur SQL, le nom de la base de données et l’adresse électronique du destinataire, puis le processus s’occupe du reste. 

## <a name="what-can-runbooks-automate"></a>Que peuvent automatiser les Runbooks ?
Comme les runbooks dans Azure Automation reposent sur PowerShell Workflow, ils peuvent accomplir tout ce que permet PowerShell. Un Runbook peut fonctionner avec toute application ou tout service disposant d'une API. Si vous avez un module PowerShell pour l’application, vous pouvez charger ce module dans Azure Automation et inclure ces applets de commande dans votre Runbook. Les Runbooks Azure Automation s’exécutent dans le cloud Azure et peuvent ainsi accéder à toutes les ressources du cloud ou à des ressources externes accessibles à partir du cloud. Grâce à un [Runbook Worker hybride](automation-hybrid-runbook-worker.md), les Runbooks peuvent s’exécuter sur votre centre de données local pour gérer des ressources locales. 

## <a name="getting-runbooks-from-the-community"></a>Obtention de Runbooks auprès de la communauté
La [galerie de runbooks](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contient des runbooks provenant de Microsoft et de la communauté, que vous pouvez utiliser tels quels dans votre environnement ou personnaliser selon vos besoins. Ils sont également utiles comme références pour apprendre à créer vos propres Runbooks. Vous pouvez même ajouter vos propres Runbooks à la galerie si vous pensez qu'ils peuvent être utiles à d'autres utilisateurs. 

## <a name="creating-runbooks-with-azure-automation"></a>Création de Runbooks avec Azure Automation
Vous pouvez intégralement [créer vos runbooks](automation-creating-importing-runbook.md) ou modifier les runbooks à partir de la [galerie de runbooks](http://msdn.microsoft.com/library/azure/dn781422.aspx) selon vos besoins. Vous avez le choix entre trois [types de Runbooks](automation-runbook-types.md), en fonction de vos besoins et de votre expérience concernant PowerShell. Si vous préférez utiliser directement le code PowerShell, vous pouvez utiliser un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) ou un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) que vous modifiez en mode hors connexion ou avec [l’éditeur de texte](http://msdn.microsoft.com/library/azure/dn879137.aspx) du portail Azure. Si vous préférez modifier un Runbook sans passer par le code sous-jacent, vous pouvez créer un [Runbook graphique](automation-runbook-types.md#graphical-runbooks) à l’aide de [l’éditeur graphique](automation-graphical-authoring-intro.md) du portail Azure. 

Lire de la documentation vous enchante moyennement ? Regardez la vidéo ci-dessous, tournée lors de la session Microsoft Ignite de mai 2015. Remarque : bien que les concepts et les fonctionnalités abordés dans cette vidéo soient corrects, Azure Automation a beaucoup évolué depuis l’enregistrement de cette vidéo. Son interface utilisateur sur le portail Azure s’est développée et l’outil prend en charge des fonctionnalités supplémentaires.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>Automatisation de la gestion de configuration avec la Configuration d'état souhaité
[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) est une plateforme de gestion qui permet de gérer, de déployer et d’appliquer la configuration d’hôtes physiques et de machines virtuelles avec une syntaxe PowerShell déclarative. Vous pouvez définir des configurations sur un serveur Pull DSC central que les machines cibles peuvent extraire et appliquer automatiquement. DSC fournit un ensemble d'applets de commande PowerShell que vous pouvez utiliser pour gérer les configurations et les ressources.  

[Azure Automation DSC](automation-dsc-overview.md) est une solution cloud pour DSC PowerShell qui fournit les services requis aux environnements d’entreprise.  Vous pouvez gérer vos ressources DSC dans Azure Automation et appliquer des configurations aux machines virtuelles ou physiques qui les récupèrent à partir d'un serveur Pull DSC dans le cloud Azure.  Il fournit également des services de création de rapports qui vous informent des événements importants, par exemple lorsque les nœuds s'écartent de leur configuration affectée et lorsqu'une nouvelle configuration a été appliquée. 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Création de vos propres configurations DSC avec Azure Automation
[configurations DSC](automation-dsc-overview.md) spécifient l’état souhaité d’un nœud.  Plusieurs nœuds peuvent appliquer la même configuration pour garantir qu'ils conservent un état identique.  Vous pouvez créer une configuration avec n'importe quel éditeur de texte sur votre machine locale, puis l'importer dans Azure Automation où vous pouvez la compiler et l'appliquer aux nœuds.

## <a name="getting-modules-and-configurations"></a>Récupération de modules et de configurations
Vous pouvez obtenir des [modules PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) contenant des applets de commande que vous pouvez utiliser dans vos Runbooks et configurations DSC à partir de [PowerShell Gallery](http://www.powershellgallery.com/). Vous pouvez lancer cette galerie à partir du portail Azure et importer les modules directement dans Azure Automation, ou bien les télécharger et les importer manuellement. Vous ne pouvez pas installer les modules directement depuis le portail Azure, mais vous pouvez les télécharger et les installer comme vous le feriez avec tout autre module. 

## <a name="example-practical-applications-of-azure-automation"></a>Exemples d'applications pratiques d'Azure Automation
Voici quelques exemples des types de scénarios d’automatisation que permet Azure Automation. 

* Créer et copier des machines virtuelles dans différents abonnements Azure. 
* Programmer des copies de fichiers d'une machine locale vers un conteneur Azure Blob Storage. 
* Automatiser les fonctions de sécurité, comme refuser les requêtes d'un client lors de la détection d'une attaque par déni de service. 
* Vérifier que les machines s'alignent en permanence sur la stratégie de sécurité configurée.
* Gérer le déploiement continu du code d'application sur le cloud et sur l'infrastructure locale. 
* Créer une forêt Active Directory dans Azure pour votre environnement lab. 
* Tronquer une table dans une base de données SQL si la base de données approche de la taille maximale. 
* Mettre à jour à distance les paramètres de l’environnement d’un site web Azure. 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Quels sont les liens entre Azure Automation et d'autres outils d'automatisation ?
[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) vise à automatiser les tâches de gestion dans le cloud privé. Il est installé localement dans votre centre de données en tant que composant de [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA et Azure Automation utilisent le même format de Runbook basé sur Windows PowerShell et sur le workflow Windows PowerShell, mais SMA ne prend pas en charge les [Runbooks graphiques](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) est conçu pour l’automatisation de ressources locales. Il utilise un autre format de Runbooks qu'Azure Automation et Service Management Automation, et dispose d'une interface graphique permettant de créer des Runbooks sans aucun script. Ses Runbooks sont constitués d'activités provenant de packs d'intégration spécifiquement écrits pour Orchestrator. 

## <a name="where-can-i-get-more-information"></a>Où puis-je obtenir plus d'informations ?
De nombreuses ressources sont disponibles pour vous permettre d'en savoir plus sur Azure Automation et de créer vos propres Runbooks. 

* **bibliothèque Azure Automation** . Les articles de cette bibliothèque fournissent une documentation complète sur la configuration et l'administration d'Azure Automation et pour la création de vos propres Runbooks. 
* [applets de commande Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fournissent des informations sur les opérations d’automatisation Azure à l’aide de Windows PowerShell. Les Runbooks recourent à ces applets de commande pour utiliser les ressources Azure. 
* [blog de gestion](https://azure.microsoft.com/blog/tag/azure-automation/) fournit les informations les plus récentes sur Azure Automation et d’autres technologies de gestion de Microsoft. Vous devriez vous abonner à ce blog pour rester à jour avec la dernière version de l'équipe Azure Automation. 
* [forum forum Automation Forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) vous permet de publier des questions sur Azure Automation à destination de Microsoft et de la communauté Automation. 
* [applets de commande Azure Automation](https://msdn.microsoft.com/library/mt244122.aspx) fournissent des informations pour l’automatisation des tâches d’administration. Il contient des applets de commande pour gérer les comptes Automation, les ressources, les Runbooks et DSC.

## <a name="can-i-provide-feedback"></a>Puis-je fournir des commentaires ?
**Faites-nous part de vos commentaires !** Si vous recherchez un module d'intégration ou une solution de Runbook Azure Automation, envoyez une demande de script au Centre de scripts. Le cas échéant, publiez vos commentaires ou demandes de fonctionnalités pour Azure Automation sur [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback). Merci ! 

