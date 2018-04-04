---
title: Vue d’ensemble d’Azure Automation
description: Découvrez comment utiliser Azure Automation permet pour automatiser le cycle de vie de l’infrastructure et des applications.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
keywords: azure automation, DSC, powershell, configuration de l’état souhaité, gestion des mises à jour, suivi des modifications, inventaire, runbooks, python, graphique
ms.date: 03/15/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: c44968dbceee2fdd29818a65e14f5b64ffcccade
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="an-introduction-to-azure-automation"></a>Présentation d’Azure Automation

Azure Automation offre un service d’automatisation et de configuration cloud permettant une gestion cohérente de vos environnements Azure et non-Azure. Il comporte des fonctionnalités d’automatisation des processus, de gestion des mises à jour et de configuration. Azure Automation permet de contrôler totalement le déploiement, les opérations et la désaffectation des charges de travail et des ressources.
Cet article propose une brève présentation d’Azure Automation et apporte des réponses à certaines questions courantes. Pour plus d’informations sur les différentes fonctionnalités, consultez les liens que vous trouverez au fil de cette présentation.

## <a name="azure-automation-capabilities"></a>Fonctionnalités d’Azure Automation

![Fonctionnalités de présentation d’Automation](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Automatisation de processus

Azure Automation permet d’automatiser les tâches de gestion de cloud fréquentes, longues et sujettes aux erreurs. Cette automatisation permet de vous concentrer sur des activités porteuses de valeur ajoutée. En diminuant le nombre d’erreurs et en augmentant l’efficacité, ce service vous permet également de réduire vos coûts d’exploitation. Vous pouvez intégrer des services Azure et d’autres systèmes publics requis pour le déploiement, la configuration et la gestion de vos processus de bout en bout. Le service vous permet de [créer des runbooks](automation-runbook-types.md) sous forme graphique dans PowerShell ou Python. L’utilisation d’un Runbook worker hybride permet d’unifier la gestion en assurant la coordination sur les environnements locaux. Les [Webhooks](automation-webhooks.md) permettent de répondre aux demandes et de garantir la continuité de la livraison et des opérations en déclenchant l’automatisation à partir des systèmes de gestion ITSM, DevOps et de surveillance.

### <a name="configuration-management"></a>Gestion des configurations

La [configuration de l’état souhaité](automation-dsc-overview.md) Azure Automation est une solution cloud pour DSC PowerShell qui fournit les services requis aux environnements d’entreprise. Gérez vos ressources DSC dans Azure Automation et appliquez des configurations aux machines virtuelles ou physiques à partir d’un serveur Pull DSC dans le cloud Azure. Il fournit également des rapports enrichis qui vous informent des événements importants, par exemple lorsque les nœuds s’écartent de leur configuration affectée. Vous pouvez surveiller et mettre à jour automatiquement la configuration de la machine sur divers ordinateurs physiques et machines virtuelles Windows ou Linux, dans le cloud ou localement.

Vous pouvez obtenir l’inventaire relatif aux ressources intégrées pour voir les applications installées et d’autres éléments de configuration. Les fonctionnalités de recherche et de rapports vous permettent de trouver rapidement des informations détaillées permettant de comprendre ce qui est configuré dans le système d’exploitation. Vous pouvez suivre les modifications apportées aux services, démons, logiciels, fichiers et au registre de manière à identifier rapidement les fichiers pouvant causer des problèmes. DSC peut également vous aider à générer des diagnostics et des alertes lorsque des modifications indésirables se produisent dans votre environnement.

### <a name="update-management"></a>Gestion des mises à jour

Mettez à jour les systèmes Windows et Linux dans des environnements hybrides avec Azure Automation. Vous pouvez voir la conformité des mises à jour sur les clouds Azure, locaux ou sur d’autres clouds. Vous pouvez créer des déploiements de calendrier pour coordonner l’installation de mises à jour dans une fenêtre de maintenance définie. Si une mise à jour ne doit pas être installée sur une machine, vous pouvez exclure ces mises à jour d’un déploiement.

### <a name="shared-capabilities"></a>Fonctionnalités partagées

Azure Automation se compose d’un ensemble de ressources partagées qui facilitent l’automatisation et la configuration de vos environnements à grande échelle.

* **[Contrôle d’accès basé sur le rôle](automation-role-based-access-control.md)** - Permet de contrôler l’accès au compte avec un rôle d’opérateur Automation qui permet l’exécution de tâches sans en permettre la création.
* **[Variables](automation-variables.md)** - Permettent de stocker du contenu pouvant être utilisé dans des runbooks et des configurations. Vous pouvez modifier les valeurs sans avoir à modifier les runbooks et les configurations qui y font référence.
* **[Informations d’identification](automation-credentials.md)** - Permettent de stocker en toute sécurité des informations sensibles pouvant être utilisées par des runbooks et des configurations lors de l’exécution.
* **[Certificats](automation-certificates.md)** - Peuvent être stockés et mis à disposition lors de l’exécution pour qu’ils puissent être utilisés pour l’authentification et la sécurisation des ressources déployées.
* **[Connexions](automation-connections.md)** - Permettent de stocker des paires nom/valeur d’informations contenant des informations courantes lors de la connexion à des systèmes dans des ressources de connexion. Les connexions sont définies par l’auteur du module en vue d’une utilisation lors de l’exécution dans des runbooks et des configurations.
* **[Calendriers](automation-schedules.md)** - Utilisés dans le service pour déclencher l’automatisation à des heures prédéfinies.
* **[Intégration au contrôle de code source](automation-source-control-integration.md)** -Permet de promouvoir la configuration en tant que code où les runbooks et les configurations peuvent être examinées dans un système de contrôle de code source.
* **[Modules PowerShell](automation-integration-modules.md)** - Les modules permettent de gérer Azure et d’autres systèmes. Importez-les dans le compte d’automatisation de ressources DSC et d’applets de commande définis personnalisés de Microsoft, d’un tiers ou d’une communauté.

### <a name="windows-and-linux"></a>Windows et Linux

Azure Automation est conçu pour fonctionner dans votre environnement de cloud hybride et également pour Windows et Linux. Il offre un moyen cohérent d’automatiser et de configurer les charges de travail déployées et le système d’exploitation sur lequel ils s’exécutent.

### <a name="community-gallery"></a>Galerie de la communauté

Parcourez la [galerie Automation](automation-runbook-gallery.md) pour les runbooks et les modules afin de commencer rapidement à intégrer et à créer vos processus à partir de la galerie PowerShell et du Centre de scripts Microsoft.

## <a name="common-scenarios-for-automation"></a>Scénarios courants pour Automation

Azure Automation effectue la gestion tout au long du cycle de vie de votre infrastructure et des applications. Transfert des connaissances relatives à la manière dont l’organisation fournit et gère les charges de travail vers le système. Création d’applications dans des langages courants tels que PowerShell, la configuration de l’état souhaité, Python et les runbooks graphiques. Obtention d’un inventaire complet des ressources déployées à des fins de ciblage, de création de rapports et de conformité. Identification des modifications pouvant entraîner une configuration incorrecte et amélioration de la conformité opérationnelle.

* **Générer/déployer des ressources** - Permet de déployer des machines virtuelles dans un environnement hybride à l’aide de modèles de Runbooks et Azure Resource Manager. Intégration à des outils de développement, tels que Jenkins et Visual Studio Team services.
* **Configurer des machines virtuelles** - Permet d’évaluer et de configurer des machines Windows et Linux avec la configuration souhaitée pour l’infrastructure et l’application.
* **Surveiller** - Permet d’identifier les modifications apportées aux machines à l’origine des problèmes et de corriger ou de faire remonter les informations aux systèmes de gestion.
* **Protéger** - Permet de mettre une machine virtuelle en quarantaine en cas de déclenchement de l’alerte de sécurité. Définition d’exigences intégrées.
* **Gouverner** - Permet de configurer un contrôle d’accès en fonction du rôle pour les équipes. Récupération de ressources qui ne sont pas utilisées.

## <a name="pricing-for-automation"></a>Tarif d’Automation

Vous pouvez consulter le prix d’Azure Automation sur la page relative à la [tarification](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un compte Automation](automation-quickstart-create-account.md)
