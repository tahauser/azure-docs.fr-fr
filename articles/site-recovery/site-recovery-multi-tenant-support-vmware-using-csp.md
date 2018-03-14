---
title: "Prise en charge de l’architecture multilocataire pour la réplication de machines virtuelles VMware sur Azure (programme CSP) | Microsoft Docs"
description: "Décrit comment déployer Azure Site Recovery dans un environnement multilocataire pour orchestrer la réplication, le basculement et la récupération de machines virtuelles (VM) VMware locales sur Azure via le programme CSP à l’aide du portail Azure"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Prise en charge de l’architecture multilocataire dans Azure Site Recovery pour répliquer des machines virtuelles VMware sur Azure via CSP

Azure Site Recovery prend en charge les environnements multilocataires pour les abonnements de locataire. Il prend également en charge une architecture multilocataire pour les abonnements de locataire qui sont créés et gérés via le programme Fournisseur de solutions cloud (CSP) Microsoft. Cet article détaille les conseils d’implémentation et de gestion de scénarios VMware vers Azure multilocataires. Pour plus d’informations sur la création et la gestion des abonnements locataires, consultez la page [Gestion d’une architecture mutualisée avec CSP](site-recovery-manage-multi-tenancy-with-csp.md).

Ces conseils proviennent principalement de la documentation existante sur la réplication des machines virtuelles VMware sur Azure. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware sur Azure avec Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Environnements multilocataires
Il existe trois modèles multilocataires principaux :

* **Fournisseur de services d’hébergement partagé (HSP)** : le partenaire possède l’infrastructure physique et utilise des ressources partagées (vCenter, centres de données, stockage physique, etc.) pour héberger les machines virtuelles de plusieurs locataires sur la même infrastructure. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut disposer de la récupération d’urgence en tant que solution en libre-service.

* **Fournisseur de services d’hébergement dédié** : le partenaire possède l’infrastructure physique mais utilise des ressources dédiées (plusieurs vCenter, magasins de données physiques, etc.) pour héberger les machines virtuelles de chaque locataire sur une infrastructure distincte. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut en disposer en tant que solution en libre-service.

* **Fournisseur de services gérés (MSP)** : le client possède l’infrastructure physique qui héberge les machines virtuelles, et le partenaire réalise l’activation et la gestion de la récupération d’urgence.

## <a name="shared-hosting-multi-tenant-guidance"></a>Conseils de multilocation de l’hébergement partagé
Cette section détaille le scénario d’hébergement partagé. Les deux autres scénarios sont des sous-ensembles du scénario d’hébergement partagé et ils reposent sur les mêmes principes. Les différences sont décrites à la fin des conseils d’hébergement partagé.

L’exigence de base dans un scénario de multilocation consiste à isoler les différents locataires. Un locataire ne doit pas être capable de voir ce qu’un autre locataire a hébergé. Dans un environnement géré par un partenaire, cette exigence n’est pas aussi importante que dans un environnement en libre-service où elle peut être critique. Ces conseils partent du principe que l’isolation des locataires est requise.

L’architecture est représentée dans le diagramme suivant :

![HSP partagé avec un vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Scénario d’hébergement partagé avec un vCenter**

Comme le montre le diagramme précédent, chaque client possède un serveur d’administration distinct. Cette configuration limite l’accès du locataire aux machines virtuelles propres au locataire et active l’isolation des locataires. Un scénario de réplication de machine virtuelle VMware utilise le serveur de configuration pour gérer les comptes afin de découvrir des machines virtuelles et d’installer des agents. Les mêmes principes s’appliquent aux environnements multi-locataires, en limitant en plus la détection de machines virtuelles par le biais du contrôle d’accès vCenter.

L’exigence d’isolement des données implique qu’aucune information sensible sur l’infrastructure (par exemple, les informations d’identification d’accès) ne soit divulguée aux locataires. Pour cette raison, nous recommandons que tous les composants du serveur d’administration restent sous le contrôle exclusif du partenaire. Les composants de serveur d’administration sont les suivants :
* Serveur de configuration (CS)
* Serveur de traitement (PS)
* Serveur cible maître (MT)

Un PS d’évolution est également sous le contrôle du partenaire.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Dans le scénario multilocataire, chaque CS utilise deux comptes

- **Compte d’accès vCenter**: utilisez ce compte pour découvrir les machines virtuelles du locataire. Des autorisations d’accès vCenter lui sont affectées (comme décrit dans la section suivante). Afin d’éviter les fuites d’accès accidentelles, nous recommandons aux partenaires d’entrer eux-mêmes ces informations d’identification dans l’outil de configuration.

- **Compte d’accès de machine virtuelle** : utilisez ce compte pour installer l’agent de mobilité sur les machines virtuelles du locataire via une transmission automatique. Il s’agit généralement d’un compte de domaine qu’un locataire peut fournir à un partenaire ou bien que le partenaire peut gérer directement. Si un locataire ne souhaite pas partager directement ses informations avec le partenaire, il peut être autorisé à entrer ses informations d’identification grâce à un accès limité dans le temps au CS ou, avec l’aide du partenaire, à installer des agents de mobilité manuellement.

### <a name="requirements-for-a-vcenter-access-account"></a>Conditions requises pour un compte d’accès vCenter

Comme mentionné dans la section précédente, vous devez configurer le CS avec un compte auquel un rôle spécial a été affecté. L’affectation de rôle doit être appliquée au compte d’accès vCenter pour chaque objet vCenter et elle ne doit pas être propagée sur les objets enfants. Cette configuration garantit l’isolation des locataires, car la propagation d’accès peut entraîner un accès accidentel à d’autres objets.

![L’option de propagation sur les objets enfants](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

L’alternative consiste à affecter le compte d’utilisateur et le rôle à l’objet de centre de données et à les propager sur les objets enfants. Affectez ensuite au compte un rôle *Aucun accès* pour chaque objet (comme les machines virtuelles d’autres locataires) qui doit être inaccessible à un locataire particulier. Cette configuration est fastidieuse et elle entraîne des contrôles d’accès accidentels, car chaque nouvel objet enfant créé hérite automatiquement de l’accès de son parent. Nous vous recommandons donc d’utiliser la première approche.

La procédure d’accès de compte vCenter est la suivante :

1. Créez un nouveau rôle en clonant le rôle prédéfini *Lecture seule*, puis donnez-lui un nom pratique (tel que Azure_Site_Recovery comme indiqué dans cet exemple).

2. Attribuez les autorisations suivantes à ce rôle :

    * **Banque de données** : Allouer de l’espace, Parcourir la banque de données, Opérations de fichier de bas niveau, Supprimer le fichier, Mettre à jour les fichiers de machine virtuelle

    * **Réseau** : Attribution de réseau

    * **Ressource** : Affecter les machines virtuelles au pool de ressources, Migrer des machines virtuelles hors tension, Migrer des machines virtuelles sous tension

    * **Tâches** : Créer une tâche, Mettre à jour une tâche

    * **Machine virtuelle** :
        * Configuration > tout
        * Interagir > Répondre à la question, Connexion d’appareil, Configurer un support de CD, Configurer une disquette, Mettre hors tension, Mettre sous tension, Installation des outils VMware
        * Inventaire > Créer à partir d’un existant, Créer, S’inscrire, Annuler l’inscription
        * Approvisionnement > Autoriser le téléchargement de machines virtuelles, Autoriser le chargement de fichiers de machine virtuelle
        * Gestion des instantanés > Supprimer les instantanés

    ![La boîte de dialogue Modifier le rôle](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Attribuez un niveau d’accès au compte vCenter (utilisé dans le CS du locataire) pour divers objets comme suit :

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

Pour limiter les opérations de récupération d’urgence jusqu’à l’état de basculement (à savoir sans les fonctionnalités de restauration automatique), suivez la procédure précédente avec une exception : au lieu d’affecter le rôle *Azure_Site_Recovery* au compte d’accès vCenter, affectez uniquement un rôle *Lecture seule* à ce compte. Ce jeu d’autorisations permet la réplication et le basculement de la machine virtuelle, et il n’autorise pas la restauration automatique. Tout le reste de la procédure précédente ne change pas. Pour garantir l’isolation des locataires et limiter la découverte de la machine virtuelle, chaque autorisation est toujours affectée au niveau de l’objet uniquement et n’est pas propagée sur les objets enfants.

## <a name="other-multi-tenant-environments"></a>Autres environnements multilocataires

La section précédente décrit la configuration d’un environnement multilocataire pour une solution d’hébergement partagé. Les deux autres solutions principales sont dédiées à l’hébergement et au service géré. L’architecture de ces solutions est décrite dans les sections suivantes.

### <a name="dedicated-hosting-solution"></a>Solution d’hébergement dédié

Comme indiqué dans le diagramme suivant, la différence architecturale dans une solution d’hébergement dédié réside dans le fait que l’infrastructure de chaque locataire est configurée pour ce locataire uniquement. Étant donné que les locataires sont isolés à l’aide de vCenter distincts, le fournisseur d’hébergement doit toujours suivre les étapes CSP indiquées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires. Le CSP reste inchangé.

![architecture-hsp-partagé](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Scénario d’hébergement dédié avec plusieurs vCenter**

### <a name="managed-service-solution"></a>Solution de service géré

Comme indiqué dans le diagramme suivant, la différence architecturale dans une solution de service géré réside dans le fait que l’infrastructure de chaque locataire est également séparée physiquement de l’infrastructure d’autres locataires. Ce scénario s’applique généralement lorsque le locataire possède l’infrastructure et souhaite disposer d’un fournisseur de solutions pour gérer la récupération d’urgence. Là encore, étant donné que les locataires sont isolés physiquement via des infrastructures différentes, le partenaire doit suivre les étapes CSP indiquées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires. L’approvisionnement CSP reste inchangé.

![architecture-hsp-partagé](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Scénario de service géré avec plusieurs vCenter**

## <a name="next-steps"></a>étapes suivantes
[Découvrez plus en détails](site-recovery-role-based-linked-access-control.md) le contrôle d’accès en fonction du rôle pour gérer les déploiements Azure Site Recovery.

[Gérer une architecture mutualisée avec CSP](site-recovery-manage-multi-tenancy-with-csp.md)
