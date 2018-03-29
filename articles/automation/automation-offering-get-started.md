---
title: Prise en main d'Azure Automation
description: Cet article fournit une vue d’ensemble du service Azure Automation. Il passe en revue les détails de la conception et de l’implémentation en vue de l’intégration de l’offre à partir de la Place de marché Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 961b783b44b95a871c98f96d3783f3429636f295
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-automation"></a>Prise en main d'Azure Automation

Cet article présente les concepts fondamentaux relatifs au déploiement d’Azure Automation. Si vous débutez avec Automation dans Azure ou que vous avez déjà utilisé des logiciels de workflow d’automatisation tels que System Center Orchestrator, vous pouvez découvrir comment préparer et intégrer Automation. Après avoir lu cet article, vous pourrez commencer à développer des runbooks répondant à vos besoins d’automatisation de processus. 


## <a name="automation-architecture-overview"></a>Présentation de l’architecture Automation

![Vue d’ensemble d’Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation est une application SaaS (software as a service) qui fournit un environnement multilocataire scalable et fiable, dans lequel vous pouvez utiliser des runbooks pour automatiser des processus. Vous pouvez utiliser DSC (Configuration de l’état souhaité) dans Azure, dans d’autres services cloud ou dans un environnement local pour gérer les modifications apportées à la configuration des systèmes Windows et Linux. Les entités dans votre compte Automation, notamment les runbooks, les ressources et les comptes d’identification, sont isolées des autres comptes Automation de votre abonnement et d’autres abonnements.  

Les runbooks que vous exécutez dans Azure sont exécutés sur des bacs à sable Automation. Les bacs à sable sont hébergés dans des machines virtuelles PaaS (platform as a service). 

Les bacs à sable Automation permettent d’isoler les locataires pour tous les aspects de l’exécution du runbook, notamment les modules, le stockage, la mémoire, la communication réseau et les flux de travail. Ce rôle est géré par le service. Vous ne pouvez pas accéder au rôle, ni le gérer, à partir de votre compte Azure ou Automation.         

Pour automatiser le déploiement et la gestion des ressources dans votre centre de données local ou d’autres services cloud, une fois que vous avez créé un compte Automation, vous pouvez désigner une ou plusieurs machines virtuelles pour l’exécution du rôle [Runbook Worker hybride](automation-hybrid-runbook-worker.md). Chaque Runbook Worker hybride requiert que Microsoft Management Agent soit installé et un compte Automation. L’agent doit disposer d’une connexion à un espace de travail Azure Log Analytics. Vous pouvez utiliser Log Analytics pour démarrer l’installation, mettre à jour Microsoft Management Agent et surveiller la fonctionnalité du Runbook Worker hybride. Azure Automation effectue la remise de runbooks et l’instruction visant à les exécuter.

Vous pouvez déployer plusieurs Runbooks Workers hybrides. Utilisez des Runbooks Workers hybrides pour fournir la haute disponibilité pour vos runbooks et équilibrer la charge des travaux de runbook. Dans certains cas, vous pouvez dédier des travaux de runbook pour des charges de travail ou environnements spécifiques. Microsoft Monitoring Agent sur le Runbook Worker hybride lance la communication avec le service Automation via le port TCP 443. Les Runbooks Workers hybrides n’ont aucune exigence relative aux pare-feu entrants.  

Vous pouvez choisir un runbook qui s’exécute sur un Runbook Worker hybride afin d’effectuer des tâches de gestion par rapport à d’autres machines ou services dans votre environnement. Dans ce scénario, le runbook peut également avoir besoin d’accéder aux autres ports. Si vos stratégies de sécurité informatique n’autorisent pas les ordinateurs de votre réseau à se connecter à Internet, consultez [Passerelle OMS](../log-analytics/log-analytics-oms-gateway.md). La passerelle OMS (Operations Management Suite) agit comme un proxy pour le Runbook Worker hybride. Elle recueille l’état du travail et reçoit des informations de configuration à partir de votre compte Automation.

Les runbooks qui s’exécutent sur un Runbook Worker hybride s’exécutent dans le contexte du compte système local sur l’ordinateur. Nous vous recommandons d’effectuer des actions administratives sur l’ordinateur Windows local dans le cadre d’un contexte de sécurité. Si vous souhaitez que le runbook exécute des tâches sur des ressources qui sont situées en dehors de l’ordinateur local, vous pouvez être amené à définir des ressources d’informations d’identification sécurisées dans le compte Automation. Vous pouvez accéder aux ressources d’informations d’identification sécurisées à partir du runbook et les utiliser pour l’authentification auprès de la ressource externe. Vous pouvez utiliser des ressources [d’informations d’identification](automation-credentials.md), de [certificat](automation-certificates.md) et de [connexion](automation-connections.md) dans votre runbook. Utilisez les ressources avec des applets de commande qui permettent de spécifier des informations d’identification pour les authentifier.

Vous pouvez appliquer les configurations DSC qui sont stockées dans Azure Automation à des machines virtuelles. Les autres machines physiques et virtuelles peuvent demander des configurations du serveur d’extraction Automation DSC. Vous n’avez pas besoin de déployer d’infrastructure pour prendre en charge le serveur d’extraction Automation DSC afin de gérer les configurations de vos systèmes Windows et Linux physiques ou virtuels locaux. Seul est nécessaire un accès Internet sortant à partir de chaque système que vous gérez à l’aide d’Automation DSC. La communication en direction du service OMS emprunte le port TCP 443.   

## <a name="prerequisites"></a>Prérequis


### <a name="automation-dsc"></a>Automation DSC
Vous pouvez utiliser Automation DSC pour gérer les machines suivantes :

* Machines virtuelles Azure (classique) exécutant Windows ou Linux
* Machines virtuelles Azure exécutant Windows ou Linux
* Machines virtuelles Amazon Web Services (AWS) exécutant Windows ou Linux
* Ordinateurs physiques et virtuels Windows en local ou dans un cloud autre qu’Azure ou AWS
* Ordinateurs physiques et virtuels Linux en local ou dans un cloud autre qu’Azure ou AWS

Pour les machines Windows, la version la plus récente de Windows Management Framework (WMF) 5 doit être installée. Pour les machines Linux, la version la plus récente de [l’agent DSC PowerShell pour Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) doit être installée. L’agent DSC PowerShell utilise WMF 5 pour communiquer avec Automation. 

### <a name="hybrid-runbook-worker"></a>Runbook Worker hybride  
Quand vous désignez un ordinateur pour exécuter des tâches de runbook hybride, il doit satisfaire aux prérequis suivants :

* Windows Server 2012 ou version ultérieure.
* Windows PowerShell 4.0 ou version ultérieure. Pour une fiabilité accrue, nous vous recommandons Windows PowerShell 5.0. Vous pouvez [télécharger la nouvelle version](https://www.microsoft.com/download/details.aspx?id=50395) à partir du Centre de téléchargement Microsoft.
* .NET Framework 4.6.2 ou ultérieur.
* Deux cœurs minimum.
* Un minimum de 4 Go de RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Autorisations requises pour créer un compte Automation
Pour créer ou mettre à jour un compte Automation et effectuer les tâches décrites dans cet article, vous devez disposer des privilèges et autorisations suivants :   
 
* Pour créer un compte Automation, votre compte utilisateur Azure Active Directory (Azure AD) doit être ajouté à un rôle disposant d’autorisations équivalentes à celles du rôle Propriétaire pour les ressources **Microsoft.Automation**. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  
* Dans le portail Azure, sous **Azure Active Directory** > **GÉRER** > **Inscriptions des applications**, si **Inscriptions des applications** a la valeur **Oui**, les utilisateurs non-administrateurs dans votre locataire Azure AD peuvent [inscrire les applications Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Si **Inscriptions des applications** est défini sur **Non**, l’utilisateur qui effectue cette action doit être administrateur général dans Azure AD. 

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général/Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce scénario, la page **Ajouter un compte Automation** affiche le message « Vous n’avez pas les autorisations pour créer. » 

Si un utilisateur reçoit d’abord le rôle Administrateur général/Coadministrateur, vous pouvez le supprimer de l’instance Active Directory de l’abonnement, puis le rajouter au rôle Utilisateur complet dans Active Directory.

Pour vérifier les rôles d’utilisateur :
1. Dans le portail Azure, accédez au volet **Azure Active Directory**.
2. Sélectionnez **Utilisateurs et groupes**.
3. Sélectionnez **Tous les utilisateurs**. 
4. Après avoir sélectionné un utilisateur spécifique, sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="authentication-planning"></a>Planifier l’authentification
Dans Azure Automation, vous pouvez automatiser des tâches sur des ressources locales ou situées dans Azure ou d’autres services cloud. Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources. Il doit avoir les droits minimaux requis dans l’abonnement.  

### <a name="what-is-an-automation-account"></a>Qu’est-ce qu’un compte Automation ? 
Toutes les tâches d’automatisation que vous effectuez sur les ressources à l’aide des applets de commande dans Azure Automation s’authentifient sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure AD.  Un compte Automation est séparé du compte que vous utilisez pour vous connecter au portail afin de configurer et d’utiliser des ressources Azure. 

Les ressources Automation suivantes sont fournies avec un compte Automation :

* **Certificats** : Contient un certificat utilisé pour l’authentification à partir d’un runbook ou d’une configuration DSC. Vous pouvez également ajouter des certificats.
* **Connexions**. Contient les informations d’authentification et de configuration nécessaires pour se connecter à une application ou à un service externe à partir d’un runbook ou d’une configuration DSC.
* **Informations d’identification**. Contient un objet **PSCredential**, qui a des informations d’identification de sécurité comme un nom d’utilisateur et un mot de passe. Les informations d’identification sont requises pour l’authentification à partir d’un runbook ou d’une configuration DSC.
* **Modules d’intégration**. Modules PowerShell fournis avec un compte Automation. Utilisez les modules PowerShell pour exécuter des applets de commande dans les runbooks et les configurations DSC.
* **Planifications**. Contient des planifications qui démarrent ou arrêtent un runbook à une heure spécifiée, y compris des fréquences récurrentes.
* **Variables**. Contient les valeurs qui sont disponibles à partir d’un runbook ou d’une configuration DSC.
* **Configurations DSC**. Scripts PowerShell qui expliquent comment configurer une fonctionnalité ou un paramètre du système d’exploitation, ou comment installer une application sur un ordinateur Windows ou Linux.  
* **Runbooks**. Ensembles de tâches qui exécutent des processus automatisés dans Automation sur la base de Windows PowerShell.    

Les ressources Automation pour chaque compte Automation sont associées à une seule région Azure. Toutefois, vous pouvez utiliser des comptes Automation pour gérer toutes les ressources dans votre abonnement. Créez des comptes Automation dans différentes régions si vous avez des stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique.

Quand vous créez un compte Automation dans le portail Azure, deux entités d’authentification sont automatiquement créées :

* **Compte d’identification**. Ce compte effectue les tâches suivantes :
  - Crée un principal de service dans Azure AD.
  - Créer un certificat.
  - Affectation du rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui gère les ressources Azure Resource Manager à l’aide de runbooks.
* **Compte d’identification Classic**. Ce compte charge un certificat de gestion. Le certificat sert à gérer les ressources classiques à l’aide de runbooks.

Vous pouvez utiliser le contrôle RBAC avec Resource Manager pour accorder des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification. Vous pouvez également utiliser le contrôle RBAC pour authentifier ce principal de service. Pour plus d’informations et pour obtenir de l’aide sur le développement d’un modèle de gestion des autorisations Automation, consultez [l’article Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Méthodes d’authentification
Le tableau suivant résume les méthodes d’authentification que vous pouvez utiliser pour chaque environnement qu’Azure Automation prend en charge.

| Méthode | Environnement 
| --- | --- | 
| Compte d’identification Azure et compte d’identification Classic |Déploiement Azure Resource Manager et déploiement Classic |  
| Compte d’utilisateur Azure AD |Déploiement Azure Resource Manager et déploiement Classic |  
| Authentification Windows |Centre de données local ou autre fournisseur de services cloud en utilisant le rôle Runbook Worker hybride |  
| Informations d’identification Amazon Web Services |Amazon Web Services |  

Les articles suivants fournissent une vue d’ensemble et des procédures afin de configurer l’authentification pour ces environnements. Les articles décrivent l’utilisation d’un compte existant et l’utilisation d’un nouveau compte que vous affectez pour cet environnement. 
* [Création d’un compte Azure Automation autonome](automation-create-standalone-account.md)
* [Authentification des Runbooks avec le déploiement Azure Classic et Resource Manager](automation-create-aduser-account.md)
* [Authentification des Runbooks avec Amazon Web Services](automation-config-aws-account.md)
* [Mise à jour d’un compte d’identification Automation](automation-create-runas-account.md)

L’article [Mise à jour d’un compte d’identification Automation](automation-create-runas-account.md) consacré aux comptes d’identification standard et aux comptes d’identification Classic décrit comment mettre à jour votre compte Automation existant avec les comptes d’identification à l’aide du portail. Il décrit également comment utiliser PowerShell si le compte Automation n’a pas été initialement configuré avec un compte d’identification standard ou un compte d’identification Classic. Vous pouvez créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat émis par votre AC d’entreprise. Consultez [Mettre à jour un compte d’identification Automation](automation-create-runas-account.md) pour apprendre à créer les comptes à l’aide de cette configuration.     
 
## <a name="network-planning"></a>Planifier votre réseau
Pour que le Runbook Worker hybride se connecte à OMS et soit inscrit, il doit avoir accès au numéro de port et aux URL décrits dans cette section. Il s’agit d’un ajout aux [ports et URL requis pour que Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) se connecte à OMS. 

Si vous utilisez un serveur proxy pour la communication entre l’agent et le service OMS, assurez-vous que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès.

Les port et URL suivants sont requis pour que le rôle Runbook Worker hybride communique avec Automation :

* Port : seul TCP 443 est nécessaire pour l’accès Internet sortant.
* URL globale : *.azure-automation.net.

Si vous avez un compte Automation défini pour une région spécifique, vous pouvez limiter les communications à ce centre de données régional. Le tableau suivant indique l’enregistrement DNS pour chaque région.

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

Pour obtenir la liste des adresses IP régionales plutôt que celle des noms des régions, téléchargez le fichier XML [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft. 

> [!NOTE]
> Le fichier XML Azure Datacenter IP address répertorie les plages d’adresses IP qui sont utilisées dans les centres de données Microsoft Azure. Les plages Compute, SQL et de stockage sont incluses dans le fichier. 
>
>Un fichier mis à jour est publié chaque semaine. Le fichier reflète les plages déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne sont pas utilisées dans les centres de données avant une semaine minimum. 
>
> Pensez à télécharger le nouveau fichier XML chaque semaine. Ensuite, mettez à jour votre site afin qu’il identifie correctement les services en cours d’exécution dans Azure. Les utilisateurs d’Azure ExpressRoute devraient remarquer que ce fichier est utilisé pour mettre à jour la publication BGP (Border Gateway Protocol) de l’espace Azure la première semaine de chaque mois. 
> 

## <a name="creating-an-automation-account"></a>Créer un compte Automation

Le tableau suivant présente des méthodes pour créer un compte Automation dans le portail Azure. Le tableau décrit chaque type d’expérience de déploiement et leurs différences.  

|Méthode | Description |
|-------|-------------|
| Sélectionner **Automation & Control** dans la Place de marché Azure | Une offre de la Place de marché Azure crée un compte Automation et un espace de travail OMS qui sont liés et qui se trouvent dans le même groupe de ressources et la même région. L’intégration à OMS offre également l’avantage d’utiliser Log Analytics pour surveiller et analyser l’état d’un travail de runbook et les flux de travaux au fil du temps. Vous pouvez également utiliser les fonctionnalités avancées de Log Analytics pour remonter ou étudier des problèmes. Cette offre déploie les solutions **Change Tracking** et **Update Management**, qui sont activées par défaut. |
| Sélectionner **Automation** dans la Place de marché | Cette méthode crée un compte Automation dans un groupe de ressources nouveau ou existant qui n’est pas lié à un espace de travail OMS. Elle n’inclut aucune solution disponible dans l’offre **Automation & Control**. Cette méthode est une configuration de base qui vous présente Automation. Elle peut vous aider à apprendre à écrire des runbooks et des configurations DSC, ainsi qu’à utiliser les fonctionnalités du service. |
| Sélectionner des solutions **Management** | Si vous sélectionnez une solution **Management**, notamment [Update Management](../operations-management-suite/oms-solution-update-management.md), [Start/Stop VMs during off hours](automation-solution-vm-management.md) ou [Change Tracking](../log-analytics/log-analytics-change-tracking.md), la solution vous invite à sélectionner un compte Automation et un espace de travail OMS existants. La solution vous offre la possibilité de créer un compte Automation et un espace de travail OMS en vue de son déploiement dans votre abonnement. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Créer un compte Automation intégré à OMS
Pour intégrer Automation, nous vous recommandons de sélectionner l’offre **Automation & Control** dans la Place de marché. Cette méthode crée un compte Automation et établit l’intégration à un espace de travail OMS. Quand vous utilisez cette méthode, vous avez également la possibilité d’installer les solutions de gestion qui sont disponibles avec l’offre.  

[Création d’un compte Azure Automation autonome](automation-create-standalone-account.md) vous guide tout au long du processus de création d’un compte Automation et d’un espace de travail OMS en intégrant l’offre **Automation & Control**. Vous pouvez apprendre à créer un compte Automation autonome pour tester le service ou en avoir un aperçu.  

Pour créer un espace de travail OMS et un compte Automation à l’aide de l’offre **Automation & Control** de la Place de marché :

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Sélectionnez **Nouveau**.<br><br> ![Sélectionner Nouveau dans le Portail Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Recherchez **Automation**. Dans les résultats de la recherche, sélectionnez **Automation & Control**.<br><br> ![Rechercher et sélectionner Automation & Control dans la Place de marché Azure](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)<br>   
4. Passez en revue la description de l’offre, puis sélectionnez **Créer**.  
5. Sous **Automation & Control**, sélectionnez **Espace de travail OMS**. Sous **Espaces de travail OMS**, sélectionnez un espace de travail OMS lié à l’abonnement Azure auquel appartient le compte Automation. Si vous ne disposez pas d’espace de travail OMS, sélectionnez **Créer un espace de travail**. Sous **Espace de travail OMS** : 
  1. Pour **Espace de travail OMS**, entrez un nom pour le nouvel espace de travail.
  2. Pour **Abonnement**, sélectionnez l’abonnement avec lequel établir la liaison. Si l’abonnement sélectionné par défaut n’est pas celui que vous souhaitez utiliser, sélectionnez ce dernier dans la liste déroulante.
  3. Pour **Groupe de ressources**, vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant.  
  4. Pour **Emplacement**, sélectionnez une région. Pour plus d’informations, consultez les [régions dans lesquelles Azure Automation est disponible](https://azure.microsoft.com/regions/services/). Des solutions sont proposées dans deux niveaux : niveau libre et par nœud (OMS). La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau gratuit. Le niveau par nœud (OMS) permet de collecter une quantité illimitée de données quotidiennement.  
  5. Sélectionnez **Compte Automation**.  Si vous créez un espace de travail OMS, vous devez également créer un compte Automation qui est associé à ce nouvel espace de travail. Incluez votre abonnement Azure, le groupe de ressources et la région. 
    1. Sélectionnez **Créer un compte Automation**.
    2. Sous **Compte Automation**, dans le champ **Nom**, entrez le nom du compte Automation.
    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné Vous ne pouvez pas modifier ces options. 
    3. Un compte d’identification Azure est la méthode d’authentification par défaut pour l’offre. Dès que vous sélectionnez **OK**, les options de configuration sont validées et le compte Automation est créé. Pour suivre sa progression, dans le menu, sélectionnez **Notifications**. 
    4. Sinon, sélectionnez un compte d’identification Automation existant. Le compte sélectionné ne peut pas être déjà lié à un autre espace de travail OMS. Sinon, un message de notification s’affiche. Si le compte est déjà lié à un espace de travail OMS, sélectionnez un autre compte d’identification Automation ou créez-en un.
    5. Une fois que vous avez entré ou sélectionné les informations requises, sélectionnez **Créer**. Les informations sont vérifiées, puis les comptes d’identification et Automation sont créés. Vous êtes automatiquement redirigé vers le volet **Espace de travail OMS**.  
6. Une fois que vous avez entré ou sélectionné les informations requises dans le volet **Espace de travail OMS**, sélectionnez **Créer**.  Les informations sont vérifiées, puis l’espace de travail est créé. Pour suivre sa progression, dans le menu, sélectionnez **Notifications**. Vous êtes redirigé vers le volet **Ajouter une solution**.  
7. Sous les paramètres **Automation & Control**, confirmez que vous souhaitez installer les solutions recommandées présélectionnées. Si vous changez des options par défaut, vous pouvez installer les solutions individuellement ultérieurement.  
8. Pour poursuivre l’intégration d’Automation et d’un espace de travail OMS, sélectionnez **Créer**. Tous les paramètres sont validés, puis Azure tente de déployer l’offre dans votre abonnement. Ce processus peut prendre plusieurs secondes. Pour suivre sa progression, dans le menu, sélectionnez **Notifications**. 

Une fois l’offre intégrée, vous pouvez effectuer les tâches suivantes :
* Commencer à créer des runbooks
* Utiliser les solutions de gestion que vous avez activées
* Déployer un rôle [Runbook Worker hybride](automation-hybrid-runbook-worker.md)
* Commencer à utiliser [Log Analytics](https://docs.microsoft.com/azure/log-analytics) pour collecter des données qui sont générées par des ressources dans votre environnement cloud ou local   

## <a name="next-steps"></a>Étapes suivantes
* Vérifiez que votre nouveau compte Automation peut s’authentifier auprès des ressources Azure. Pour plus d’informations, consultez [Test d’authentification du compte d’identification Azure Automation](automation-verify-runas-authentication.md).
* Prenez connaissance des considérations liées à la création de runbooks, puis lancez-vous. Pour plus d’informations, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md).


