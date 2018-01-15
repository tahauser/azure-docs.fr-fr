---
title: "Prise en main d’Azure Automation | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du service Azure Automation en examinant les détails de conception et d’implémentation dans l’optique d’intégrer l’offre à partir de la plateforme Place de marché Microsoft Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 69670d789d75a99d69538821d88427bd8ac397be
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="getting-started-with-azure-automation"></a>Prise en main d'Azure Automation

Ce guide de prise en main présente les concepts fondamentaux relatifs au déploiement d’Azure Automation. Si vous débutez avec Automation dans Azure ou que vous avez déjà utilisé des logiciels de workflow d’automatisation tels que System Center Orchestrator, ce guide vous explique comment préparer et intégrer Automation.  Ensuite, vous serez en mesure de commencer à développer des runbooks pour prendre en charge vos besoins d’automatisation de processus. 


## <a name="automation-architecture-overview"></a>Présentation de l’architecture Automation

![Vue d’ensemble d’Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation est une application SaaS qui propose un environnement mutualisé fiable et évolutif pour automatiser les processus avec des Runbooks et gérer les changements de configuration des systèmes Windows et Linux à l’aide de la configuration de l’état souhaité dans Azure, les autres services cloud ou en local. Les entités contenues dans votre compte Automation, telles que les Runbooks, les ressources et les comptes d’identification, sont isolées des autres comptes Automation de votre abonnement et d’autres abonnements.  

Les Runbooks que vous exécutez dans Azure sont exécutés dans des bacs à sable Automation, qui sont hébergés sur les machines virtuelles PaaS Azure.  Les bacs à sable Automation permettent d’isoler les clients pour tous les aspects de l’exécution du Runbook : modules, stockage, mémoire, communication réseau, flux de travail, etc. Ce rôle est géré par le service et n’est pas accessible à partir de votre compte Azure ou Azure Automation pour que vous puissiez le contrôler.         

Pour automatiser le déploiement et la gestion des ressources dans votre centre de données local ou d’autres services cloud, après avoir créé un compte Automation, vous pouvez désigner une ou plusieurs machines pour l’exécution du rôle [Runbook Worker hybride](automation-hybrid-runbook-worker.md).  Chaque Runbook Worker hybride a besoin de l’agent Microsoft Management Agent avec une connexion à un espace de travail Log Analytics et un compte Automation.  Log Analytics est utilisé pour démarrer l’installation, mettre à jour l’agent Microsoft Management Agent et surveiller la fonctionnalité du Runbook Worker hybride.  La remise de Runbooks et l'instruction visant à les exécuter sont effectuées par Azure Automation.

Vous pouvez déployer plusieurs Runbooks Worker hybrides pour fournir une haute disponibilité à vos runbooks, équilibrer la charge des travaux des runbooks, et dans certains cas, les allouer à des charges de travail ou environnements particuliers.  L’agent Microsoft Monitoring Agent sur le Runbook Worker hybride lance la communication avec le service Automation via le port TCP 443. Il n’existe aucune exigence de pare-feu entrante.  Dès que le runbook s’exécute sur un Runbook Worker hybride au sein de l’environnement et que vous souhaitez que le runbook réalise des tâches de gestion pour d’autres machines et d’autres services dans cet environnement, il est possible que le runbook ait besoin d’accéder à d’autres ports.  Si vos stratégies de sécurité informatiques n’autorisent pas les ordinateurs de votre réseau à se connecter à Internet, consultez l’article sur la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md), qui joue le rôle de proxy pour que le Runbook Worker hybride collecte le statut du travail et reçoive les informations de configuration de votre compte Automation.

Les Runbooks s’exécutant sur un Runbook Worker hybride s’exécutent dans le contexte du compte système local sur l’ordinateur, qui est le contexte de sécurité recommandé lorsque des actions administratives sont effectuées sur la machine Windows locale. Si vous souhaitez que le Runbook exécute des tâches sur des ressources en dehors de la machine locale, vous devrez peut-être définir des ressources d’informations d’identification sécurisées dans le compte Automation auquel vous pouvez accéder à partir du Runbook et les utiliser pour l’authentification auprès de la ressource externe. Vous pouvez utiliser les ressources [Informations d’identification](automation-credentials.md), [Certificat](automation-certificates.md) et [Connexion](automation-connections.md) dans votre Runbook avec des cmdlets qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de ces ressources.

Les configurations de l’état souhaité stockées dans Azure Automation peuvent être directement appliquées aux machines virtuelles Azure. Les autres machines physiques et virtuelles peuvent demander des configurations du serveur d’extraction Azure Automation DSC.  Pour la gestion des configurations de vos systèmes Windows et Linux virtuels ou physiques, vous n’avez pas besoin de déployer d’infrastructure pour prendre en charge le serveur Pull Automation DSC. Vous avez uniquement besoin de l’accès Internet sortant de chaque système à gérer par Automation DSC, communiquant sur le port TCP 443 avec le service OMS.   

## <a name="prerequisites"></a>Conditions préalables

### <a name="automation-dsc"></a>Automation DSC
Azure Automation DSC peut servir à gérer diverses machines :

* Machines virtuelles Azure (classique) exécutant Windows ou Linux
* Machines virtuelles Azure exécutant Windows ou Linux
* Machines virtuelles Amazon Web Services (AWS) exécutant Windows ou Linux
* Ordinateurs physiques/virtuels Windows en local ou dans un cloud autre qu’Azure ou AWS
* Ordinateurs physiques/virtuels Linux en local ou dans un cloud autre qu’Azure ou AWS

La dernière version de WMF 5 doit être installée pour que l'agent PowerShell DSC pour Windows puisse communiquer avec Azure Automation. La dernière version de [l’agent PowerShell DSC pour Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) doit être installée pour que Linux puisse communiquer avec Azure Automation.

### <a name="hybrid-runbook-worker"></a>Runbook Worker hybride  
Lorsque vous configurez un ordinateur pour qu’il exécute les travaux de runbook hybride, cet ordinateur doit disposer des éléments suivants :

* Windows Server 2012 ou version ultérieure
* Windows PowerShell 4.0 ou version ultérieure.  Nous vous recommandons d’installer Windows PowerShell 5.0 sur l’ordinateur pour renforcer la fiabilité. Vous pouvez la télécharger la nouvelle version à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=50395).
* .NET Framework 4.6.2 ou version ultérieure
* Deux cœurs minimum
* De 4 Go de RAM minimum

### <a name="permissions-required-to-create-automation-account"></a>Autorisations requises pour créer le compte Automation
Pour créer ou mettre à jour un compte Automation conformément à cette rubrique, vous devez disposer des privilèges spécifiques suivants et des autorisations requises.   
 
* Pour créer un compte Automation, votre compte utilisateur AD doit être ajouté à un rôle disposant d’autorisations équivalentes à celles du rôle Propriétaire pour les ressources Microsoft.Automation, comme indiqué dans l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  
* Si le paramètre Inscriptions des applications possède la valeur **Oui**, les utilisateurs non administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Si le paramètre Inscriptions d’applications est défini sur **Non**, l’utilisateur qui effectue cette action doit être un administrateur général dans Azure AD. 

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général/Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevez le message d’avertissement « Vous n’avez pas les autorisations pour créer… » dans le panneau **Ajouter un compte Automation**. Les utilisateurs qui ont d’abord reçu le rôle Administrateur général/Coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour devenir des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="authentication-planning"></a>Planification de l’authentification
Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs cloud.  Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement.  

### <a name="what-is-an-automation-account"></a>Qu’est-ce qu’un compte Automation ? 
Toutes les tâches d’automatisation que vous effectuez sur les ressources à l’aide des cmdlets Azure dans Azure Automation s’authentifient sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory.  Un compte Automation est séparé du compte que vous utilisez pour vous connecter au portail afin de configurer et d’utiliser des ressources Azure.  Les ressources Automation fournies avec un compte sont les suivantes :

* **Certificats** : contient un certificat utilisé pour l’authentification à partir d’un runbook ou d’une configuration DSC ou permet d’en ajouter.
* **Connexions** : contient les informations d’authentification et de configuration nécessaires pour se connecter à une application ou à un service externe à partir d’un runbook ou d’une configuration DSC.
* **Informations d’identification** : objet PSCredential qui contient des informations d’identification de sécurité, telles qu’un nom d’utilisateur et un mot de passe, requises pour l’authentification à partir d’un runbook ou d’une configuration DSC.
* **Modules d’intégration** : modules PowerShell fournis avec un compte Azure Automation pour l’utilisation d’applets de commande dans des runbooks et des configurations DSC.
* **Planifications** : contient des planifications qui démarrent ou arrêtent un runbook à une heure spécifiée, y compris des fréquences récurrentes.
* **Variables** : contient les valeurs qui sont disponibles à partir d’un runbook ou d’une configuration DSC.
* **Configurations DSC** : scripts PowerShell qui expliquent comment configurer une fonctionnalité ou un paramètre du système d’exploitation ou comment installer une application sur un ordinateur Windows ou Linux.  
* **Runbooks** : ensembles de tâches qui exécutent des processus automatisés dans Azure Automation sur la base de Windows PowerShell.    

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer toutes les ressources dans votre abonnement. Créez des comptes Automation dans différentes régions si vous avez des stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique.

Lorsque vous créez un compte Automation dans le portail Azure, deux entités d’authentification sont automatiquement créées :

* Compte d’identification Azure. Ce compte crée un principal de service dans Azure Active Directory (Azure AD) et un certificat. Il affecte également le rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui gère les ressources Resource Manager à l’aide de Runbooks.
* Compte d’identification Classic. Ce compte charge un certificat de gestion, qui est utilisé pour gérer les ressources classiques à l’aide de runbooks.

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier ce principal du service.  Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Méthodes d’authentification
Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Azure Automation.

| Méthode | Environnement 
| --- | --- | 
| Compte d’identification Azure et compte d’identification Classic |Déploiement Azure Resource Manager et déploiement Classic |  
| Compte d’utilisateur Azure AD |Déploiement Azure Resource Manager et déploiement Classic |  
| Authentification Windows |Centre de données local ou autre fournisseur cloud utilisant le Runbook Worker hybride |  
| Informations d’identification AWS |Amazon Web Services |  

La section **Procédures\Authentication et sécurité** comprend des articles proposant une vue d’ensemble et des étapes d’implémentation, afin de configurer l’authentification pour ces environnements avec un compte existant ou un nouveau compte dédié à l’environnement.  L’article [Mise à jour d’un compte d’identification Automation](automation-create-runas-account.md) consacré aux comptes d’identification Azure et aux comptes d’identification Classic décrit comment mettre à jour votre compte Automation existant avec les comptes d’identification à l’aide du portail ou de PowerShell si ce compte n’a pas été initialement configuré avec un compte d’identification Azure ou un compte d’identification Classic. Si vous souhaitez créer un compte d’identification Azure ou un compte d’identification Classic avec un certificat émis par votre autorité de certification (CA) d’entreprise, consultez cet article pour découvrir comment créer les comptes à l’aide de cette configuration.     
 
## <a name="network-planning"></a>Planification réseau
Pour que le Runbook Worker hybride se connecte à Microsoft Operations Management Suite (OMS) et soit inscrit, il doit avoir accès au numéro de port et aux URL décrits ci-dessous.  Il s’agit d’un ajout aux [ports et URL requis pour que Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) se connecte à OMS. Si vous utilisez un serveur proxy pour la communication entre l’agent et le service OMS, vous devez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès.

Les informations ci-dessous répertorient le port et les URL requis pour que le Runbook Worker hybride communique avec Automation.

* Port : seul TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : *.azure-automation.net

Si vous avez un compte Automation défini pour une région spécifique et que vous souhaitez limiter la communication avec ce centre de données régional, le tableau suivant fournit l’enregistrement DNS pour chaque région.

| **Région** | **Enregistrement DNS** |
| --- | --- |
| États-Unis - partie centrale méridionale |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centre-Ouest des États-Unis | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Est du Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us |

Pour obtenir la liste des adresses IP plutôt que celle des noms, téléchargez et consultez le fichier xml [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft. 

> [!NOTE]
> Ce fichier contient les plages d’adresses IP (y compris les plages de calcul, SQL et de stockage) utilisées dans les centres de données Microsoft Azure. Un fichier mis à jour est publié chaque semaine et reflète les plages actuellement déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne seront pas utilisées dans les centres de données avant une semaine minimum. Téléchargez le nouveau fichier xml chaque semaine et effectuez les modifications nécessaires sur votre site pour identifier correctement les services qui s’exécutent dans Azure. Les utilisateurs d’Express Route peuvent remarquer ce fichier utilisé pour mettre à jour la publication BGP de l’espace Azure la première semaine de chaque mois. 
> 

## <a name="creating-an-automation-account"></a>Création d’un compte Automation

Différentes options s’offrent à vous pour créer un compte Automation dans le portail Azure.  Le tableau suivant présente chaque type d’expérience de déploiement et leurs différences.  

|Méthode | DESCRIPTION |
|-------|-------------|
| Sélectionner Automation & Control dans la Place de marché | Une offre qui crée un compte Automation et un espace de travail OMS liés entre eux dans le même groupe de ressources et la même région.  L’intégration à OMS offre également l’avantage d’utiliser Log Analytics pour surveiller et analyser l’état d’un travail de runbook et les flux de travaux au fil du temps et pour utiliser des fonctionnalités avancées d’escalade ou d’examen des problèmes. En outre, cette offre déploie les solutions Change Tracking et Update Management, qui sont activées par défaut. |
| Sélectionner Automation dans la Place de marché | Crée un compte Automation dans un groupe de ressources nouveau ou existant qui n’est pas lié à un espace de travail OMS et qui n’inclut pas les solutions disponibles à partir de l’offre Automation & Control. Il s’agit d’une configuration de base qui vous présente Automation et peut vous aider à apprendre à écrire des runbooks, des configurations DSC et à utiliser les fonctionnalités du service. |
| Solutions de gestion sélectionnées | Si vous sélectionnez une solution (**[Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)**, **[Démarrer/arrêter les machines virtuelles durant les heures creuses](automation-solution-vm-management.md)**, ou **[Suivi des modifications](../log-analytics/log-analytics-change-tracking.md)**, vous êtes invité à choisir un compte Automation et un espace de travail OMS existant ou à les créer comme exigé pour que la solution soit déployée dans votre abonnement. |

Cette rubrique vous guide lors de la création d’un compte Automation et d’un espace de travail OMS en intégrant l’offre Automation & Control.  Pour créer un compte Automation à des fins de test ou pour avoir un aperçu du service, consultez l’article suivant [Create a standalone Azure Automation account](automation-create-standalone-account.md) (Créer un compte Azure Automation autonome).  

### <a name="create-automation-account-integrated-with-oms"></a>Créer un compte Automation intégré à OMS
La méthode recommandée pour intégrer Automation est de sélectionner l’offre Automation & Control dans la Place de marché.  Cela crée un compte Automation et intègre un espace de travail OMS, y compris l’option permettant d’installer les solutions de gestion qui sont disponibles avec l’offre.  

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

2. Cliquez sur **Nouveau**.<br><br> ![Sélection de l’option Nouveau dans le portail Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Recherchez **Automation**, puis dans les résultats de recherche, sélectionnez **Automation & Control***.<br><br> ![Recherche et sélection d’Automation & Control dans la Place de marché](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Après avoir lu la description de l’offre, cliquez sur **Créer**.  

5. Dans le panneau des paramètres **Automation & Control**, sélectionnez **Espace de travail OMS**.  Dans le panneau **Espaces de travail OMS**, sélectionnez un espace de travail OMS lié au même abonnement Azure que celui dans lequel le compte Automation se trouve, ou créez un espace de travail OMS.  Si vous ne disposez pas d’espace de travail OMS, vous pouvez sélectionner **Créer un espace de travail**, puis dans le panneau **Espace de travail OMS**, procédez comme suit : 
   - Spécifiez un nom pour le nouvel **espace de travail OMS**.
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Pour **Groupe de ressources**, vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant.  
   - Sélectionnez un **emplacement**.  Pour en savoir plus, consultez les [régions dans lesquelles Azure Automation est disponible](https://azure.microsoft.com/regions/services/).  Des solutions sont proposées dans deux niveaux : niveau libre et par nœud (OMS).  La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau gratuit.  Le niveau Par nœud (OMS) permet de collecter une quantité illimitée de données quotidiennement.  
   - Sélectionnez **Compte Automation**.  Si vous créez un espace de travail OMS, vous devez également créer un compte Automation associé au nouvel espace de travail OMS spécifié précédemment, y compris votre abonnement, votre groupe de ressources et votre région Azure.  Vous pouvez sélectionner **Créer un compte Automation** et dans le panneau **Compte Automation**, fournir les informations suivantes : 
  - Dans le champ **Nom**, saisissez le nom du compte Automation.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné et ne peuvent pas être modifiées.  Un compte d’identification Azure est la méthode d’authentification par défaut pour l’offre.  Lorsque vous cliquez sur **OK**, les options de configuration sont validées et le compte Automation est créé.  Vous pouvez suivre la progression sous **Notifications** dans le menu. 

    Sinon, sélectionnez un compte d’identification Automation existant.  Le compte que vous sélectionnez ne peut pas déjà être lié à un autre espace de travail OMS, ou une notification s’affichera dans le panneau.  Si le compte est déjà lié, vous devez sélectionner un autre compte d’identification Automation ou en créer un.

    Après avoir renseigné les informations requises, cliquez sur **Créer**.  Les informations sont vérifiées et les comptes d’identification et Automation sont créés.  Vous êtes automatiquement redirigé vers le panneau **Espace de travail OMS**.  

6. Après avoir entré les informations requises dans le panneau **Espace de travail OMS**, cliquez sur **Créer**.  Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous êtes redirigé vers le panneau **Ajouter une solution**.  

7. Dans le panneau des paramètres **Automation & Control**, confirmez que vous souhaitez installer les solutions recommandées présélectionnées. Si vous les désélectionnez, vous pourrez les installer séparément plus tard.  

8. Cliquez sur **Créer** pour poursuivre l’intégration d’Automation et d’un espace de travail OMS. Tous les paramètres sont validés et une tentative de déploiement de l’offre dans votre abonnement est effectuée.  Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

Une fois l’offre intégrée, vous pouvez commencer à créer des runbooks, à utiliser les solutions de gestion que vous avez activées, à déployer un rôle [Runbook Worker hybride](automation-hybrid-runbook-worker.md) ou à utiliser [Log Analytics](https://docs.microsoft.com/azure/log-analytics) pour collecter les données générées par les ressources de votre environnement cloud ou local.   

## <a name="next-steps"></a>étapes suivantes
* Vous pouvez confirmer que votre nouveau compte Automation s’authentifie auprès de ressources Azure en consultant [Test Azure Automation Run As account authentication](automation-verify-runas-authentication.md) (Test d’authentification du compte d’identification Azure Automation).
* Avant de procéder à la création de runbooks, commencez par examiner les [types de runbooks Automation](automation-runbook-types.md) pris en charge et les considérations associées.


