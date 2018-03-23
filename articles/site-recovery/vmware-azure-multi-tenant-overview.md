---
title: Vue d’ensemble de la prise en charge de l’architecture multilocataire pour la réplication de machines virtuelles VMware sur Azure (CSP) à l’aide d’Azure Site Recovery | Microsoft Docs
description: Fournit une vue d’ensemble de la prise en charge d’Azure Site Recovery pour les abonnements de locataire dans un environnement multilocataires, via le programme CSP.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 9b4fbb34686a12f992b344ac61420c9ba99ee405
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Prise en charge de l’architecture multilocataire pour la réplication de VMware vers Azure avec CSP

[Azure Site Recovery](site-recovery-overview.md) prend en charge les environnements multilocataires pour les abonnements de locataire. Il prend également en charge une architecture multilocataire pour les abonnements de locataire qui sont créés et gérés via le programme Fournisseur de solutions cloud (CSP) Microsoft. 

Cet article offre une vue d’ensemble de l’implémentation et de la gestion de la réplication de l’architecture multilocataire VMware vers Azure. 

## <a name="multi-tenant-environments"></a>Environnements multilocataires

Il existe trois modèles multilocataires principaux :

* **Fournisseur de services d’hébergement partagé (HSP)** : le partenaire possède l’infrastructure physique et utilise des ressources partagées (vCenter, centres de données, stockage physique, etc.) pour héberger les machines virtuelles de plusieurs locataires sur la même infrastructure. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut disposer de la récupération d’urgence en tant que solution en libre-service.

* **Fournisseur de services d’hébergement dédié** : le partenaire possède l’infrastructure physique mais utilise des ressources dédiées (plusieurs vCenter, magasins de données physiques, etc.) pour héberger les machines virtuelles de chaque locataire sur une infrastructure distincte. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut en disposer en tant que solution en libre-service.

* **Fournisseur de services gérés (MSP)** : le client possède l’infrastructure physique qui héberge les machines virtuelles, et le partenaire réalise l’activation et la gestion de la récupération d’urgence.

## <a name="shared-hosting-services-provider-hsp"></a>Fournisseur de services d’hébergement partagé (HSP)

 Les deux autres scénarios sont des sous-ensembles du scénario d’hébergement partagé et ils reposent sur les mêmes principes. Les différences sont décrites à la fin des conseils d’hébergement partagé.

L’exigence de base dans un scénario de multilocation est que les locataires soient isolés. Un locataire ne doit pas être capable de voir ce qu’un autre locataire a hébergé. Dans un environnement géré par un partenaire, cette exigence n’est pas aussi importante que dans un environnement en libre-service où elle peut être critique. Cet article part du principe que l’isolation des locataires est requise.

L’architecture est illustrée dans le diagramme suivant.

![HSP partagé avec un vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Hébergement partagé avec un serveur vCenter**

Dans le diagramme, chaque client possède un serveur d’administration distinct. Cette configuration limite l’accès du locataire aux machines virtuelles propres au locataire et active l’isolation des locataires. Les machines virtuelles VMware utilisent le serveur de configuration pour découvrir des machines virtuelles et installer des agents. Les mêmes principes s’appliquent aux environnements multilocataires, en limitant en plus la détection de machines virtuelles à l’aide du contrôle d’accès vCenter.

L’exigence d’isolement des données signifie qu’aucune information sensible sur l’infrastructure (par exemple, les informations d’identification d’accès) ne soit divulguée aux locataires. Pour cette raison, nous recommandons que tous les composants du serveur d’administration restent sous le contrôle exclusif du partenaire. Les composants de serveur d’administration sont les suivants :

* Serveur de configuration
* Serveur de traitement
* Serveur cible maître

Un serveur de traitement non à l’échelle distinct est également contrôlé par le partenaire.

## <a name="configuration-server-accounts"></a>Comptes serveur de configuration

Dans le scénario multilocataire, chaque serveur de configuration utilise deux comptes :

- **Compte d’accès vCenter** : ce compte est utilisé pour découvrir les machines virtuelles du locataire. Des autorisations d’accès vCenter lui sont associées. Afin d’éviter les fuites d’accès, nous recommandons aux partenaires d’entrer eux-mêmes ces informations d’identification dans l’outil de configuration.

- **Compte d’accès de machine virtuelle** : ce compte est utilisé pour installer l’agent du service Mobilité sur les machines virtuelles du locataire, à l’aide d’une transmission automatique. Il s’agit généralement d’un compte de domaine qu’un locataire peut fournir à un partenaire ou un compte que le partenaire peut gérer directement. Si un locataire ne souhaite pas partager directement ses informations avec le partenaire, il peut entrer ses informations d’identification grâce à un accès limité au serveur de configuration. Avec l’aide du partenaire, il peut également l’agent du service Mobilité manuellement.

## <a name="vcenter-account-requirements"></a>Conditions requises pour le compte vCenter

Vous devez configurer le serveur de configuration avec un compte auquel un rôle spécial a été affecté. 

- L’attribution de rôle doit être appliquée au compte d’accès vCenter pour chaque objet vCenter et elle ne doit pas être propagée sur les objets enfants. Cette configuration garantit l’isolation des locataires, car la propagation d’accès peut entraîner un accès accidentel à d’autres objets.

    ![L’option de propagation sur les objets enfants](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- L’approche alternative consiste à affecter le compte d’utilisateur et le rôle à l’objet de centre de données et à les propager sur les objets enfants. Affectez ensuite au compte un rôle **Aucun accès** pour chaque objet (comme les machines virtuelles appartenant à d’autres locataires) qui doit être inaccessible à un locataire particulier. Cette configuration est fastidieuse. Elle entraîne des contrôles d’accès accidentels, car chaque nouvel objet enfant créé hérite automatiquement de l’accès de son parent. Nous vous recommandons donc d’utiliser la première approche.

### <a name="create-a-vcenter-account"></a>Créer un compte vCenter

1. Créez un nouveau rôle en clonant le rôle prédéfini *Lecture seule*, puis donnez-lui un nom pratique (tel que Azure_Site_Recovery comme indiqué dans cet exemple).
2. Attribuez les autorisations suivantes à ce rôle :

    * **Banque de données** : Allouer de l’espace, Parcourir la banque de données, Opérations de fichier de bas niveau, Supprimer le fichier, Mettre à jour les fichiers de machine virtuelle
    * **Réseau** : Attribution de réseau
    * **Ressource** : Affecter les machines virtuelles au pool de ressources, Migrer des machines virtuelles hors tension, Migrer des machines virtuelles sous tension
    * **Tâches** : Créer une tâche, Mettre à jour une tâche
    * **Machine virtuelle - Configuration** : Tout
    - **Machine virtuelle - Interagir** > Répondre à la question, Connexion d’appareil, Configurer un support de CD, Configurer une disquette, Mettre hors tension, Mettre sous tension, Installation des outils VMware
    - **Machine virtuelle - Inventaire** > Créer à partir d’un existant, Créer, S’inscrire, Annuler l’inscription
    - **Machine virtuelle - Approvisionnement** > Autoriser le téléchargement de machines virtuelles, Autoriser le chargement de fichiers de machine virtuelle
    - **Machine virtuelle Gestion des instantanés** > Supprimer les instantanés

        ![La boîte de dialogue Modifier le rôle](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Attribuez un niveau d’accès au compte vCenter (utilisé dans le serveur de configuration du locataire) pour divers objets comme suit :

>| Object | Rôle | Remarques |
>| --- | --- | --- |
>| vCenter | Lecture seule | Nécessaire uniquement pour autoriser l’accès vCenter pour la gestion de différents objets. Vous pouvez supprimer cette autorisation si le compte ne sera jamais fourni à un locataire ou utilisé pour des opérations de gestion sur le vCenter. |
>| Centre de données | Azure_Site_Recovery |  |
>| Hôte et cluster hôte | Azure_Site_Recovery | Revérifiez que l’accès se trouve au niveau de l’objet de sorte que seuls les hôtes accessibles comprennent des machines virtuelles de locataire avant le basculement et après la restauration automatique. |
>| Banque de données et cluster de banque de données | Azure_Site_Recovery | Identique à ce qui précède. |
>| Réseau | Azure_Site_Recovery |  |
>| Serveur d’administration | Azure_Site_Recovery | Inclut l’accès à tous les composants (CS, PS et MT) qui ne se trouvent pas sur l’ordinateur du CS. |
>| Machines virtuelles de locataire | Azure_Site_Recovery | Garantit que les nouvelles machines virtuelles d’un locataire disposent également de cet accès. Sinon, elles ne pourront pas être découvertes via le portail Azure. |

L’accès au compte vCenter est maintenant terminé. Cette étape répond aux conditions minimales requises des autorisations pour effectuer des opérations de restauration. Vous pouvez également utiliser ces autorisations d’accès avec vos stratégies existantes. Modifiez simplement vos autorisations existantes pour inclure des autorisations de rôle de l’étape 2 détaillée précédemment.

### <a name="failover-only"></a>Basculement uniquement
Pour limiter les opérations de récupération d’urgence au basculement uniquement (à savoir sans les fonctionnalités de restauration automatique), utilisez la procédure précédente avec les exceptions suivantes :

- Au lieu d’affecter le rôle *Azure_Site_Recovery* au compte d’accès vCenter, affectez uniquement un rôle *Lecture seule* à ce compte. Ce jeu d’autorisations permet la réplication et le basculement de la machine virtuelle, et il n’autorise pas la restauration automatique.
- Tout le reste de la procédure précédente ne change pas. Pour garantir l’isolation des locataires et limiter la découverte de la machine virtuelle, chaque autorisation est toujours affectée au niveau de l’objet uniquement et n’est pas propagée sur les objets enfants.


## <a name="dedicated-hosting-solution"></a>Solution d’hébergement dédié

Comme indiqué dans le diagramme suivant, la différence architecturale dans une solution d’hébergement dédié réside dans le fait que l’infrastructure de chaque locataire est configurée pour ce locataire uniquement. Étant donné que les locataires sont isolés à l’aide de vCenter distincts, le fournisseur d’hébergement doit toujours suivre les étapes CSP indiquées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires. Le CSP reste inchangé.

![architecture-hsp-partagé](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Scénario d’hébergement dédié avec plusieurs vCenter**

## <a name="managed-service-solution"></a>Solution de service géré

Comme indiqué dans le diagramme suivant, la différence architecturale dans une solution de service géré réside dans le fait que l’infrastructure de chaque locataire est également séparée physiquement de l’infrastructure d’autres locataires. Ce scénario s’applique généralement lorsque le locataire possède l’infrastructure et souhaite disposer d’un fournisseur de solutions pour gérer la récupération d’urgence. Là encore, étant donné que les locataires sont isolés physiquement via des infrastructures différentes, le partenaire doit suivre les étapes CSP indiquées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires. L’approvisionnement CSP reste inchangé.

![architecture-hsp-partagé](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scénario de service géré avec plusieurs vCenter**

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-role-based-linked-access-control.md) sur le contrôle d’accès en fonction du rôle dans Site Recovery.
Découvrez comment [configurer la récupération d’urgence des machines virtuelles VMware vers Azure](vmware-azure-tutorial.md)
[Set up disaster recovery for VMWare VMs with multi-tenancy with CSP](vmware-azure-multi-tenant-csp-disaster-recovery.md) (Configurer la récupération d’urgence pour des machines virtuelles VMware avec une architecture multilocataire avec CSP)
