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
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 9db7e276fbbc064abe16cab2d2df668d2b1c8f7d
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Prise en charge de l’architecture multilocataire dans Azure Site Recovery pour répliquer des machines virtuelles VMware sur Azure via CSP

Azure Site Recovery prend en charge les environnements multilocataires pour les abonnements de locataire. Il prend également en charge une architecture multilocataire pour les abonnements de locataire qui sont créés et gérés via le programme Fournisseur de solutions cloud (CSP) Microsoft. Cet article détaille les conseils d’implémentation et de gestion de scénarios VMware vers Azure multilocataires. Il couvre également la création et la gestion des abonnements de locataire via CSP.

Ces conseils proviennent principalement de la documentation existante sur la réplication des machines virtuelles VMware sur Azure. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware sur Azure avec Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Environnements multilocataires
Il existe trois modèles multilocataires principaux :

* **Fournisseur de services d’hébergement partagé (HSP)** : le partenaire possède l’infrastructure physique et utilise des ressources partagées (vCenter, centres de données, stockage physique, etc.) pour héberger des machines virtuelles à plusieurs locataires sur la même infrastructure. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut disposer de la récupération d’urgence en tant que solution en libre-service.

* **Fournisseur de services d’hébergement dédié** : le partenaire possède l’infrastructure physique mais utilise des ressources dédiées (plusieurs vCenter, magasins de données physiques, etc.) pour héberger les machines virtuelles de chaque locataire sur une infrastructure distincte. Le partenaire peut fournir la gestion de la récupération d’urgence en tant que service géré, ou le locataire peut en disposer en tant que solution en libre-service.

* **Fournisseur de services gérés (MSP)** : le client possède l’infrastructure physique qui héberge les machines virtuelles, et le partenaire réalise l’activation et la gestion de la récupération d’urgence.

## <a name="shared-hosting-multi-tenant-guidance"></a>Conseils de multilocation de l’hébergement partagé
Cette section détaille le scénario d’hébergement partagé. Les deux autres scénarios sont des sous-ensembles du scénario d’hébergement partagé et ils reposent sur les mêmes principes. Les différences sont décrites à la fin des conseils d’hébergement partagé.

L’exigence de base dans un scénario de multilocation consiste à isoler les différents locataires. Un locataire ne doit pas être capable de voir ce qu’un autre locataire a hébergé. Dans un environnement géré par un partenaire, cette exigence n’est pas aussi importante que dans un environnement en libre-service où elle peut être critique. Ces conseils partent du principe que l’isolation des locataires est requise.

L’architecture est représentée dans le diagramme suivant :

![HSP partagé avec un vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Scénario d’hébergement partagé avec un vCenter**

Comme le montre le diagramme précédent, chaque client possède un serveur d’administration distinct. Cette configuration limite l’accès du locataire aux machines virtuelles propres au locataire et active l’isolation des locataires. Un scénario de réplication de machine virtuelle VMware utilise le serveur de configuration pour gérer les comptes afin de découvrir des machines virtuelles et d’installer des agents. Nous suivons les mêmes principes pour les environnements multilocataires, en limitant en plus la découverte de machines virtuelles via le contrôle d’accès vCenter.

L’exigence d’isolation des données nécessite que toutes les informations sensibles sur l’infrastructure (telles que les informations d’identification d’accès) ne soient pas divulguées aux locataires. Pour cette raison, nous recommandons que tous les composants du serveur d’administration restent sous le contrôle exclusif du partenaire. Les composants de serveur d’administration sont les suivants :
* Serveur de configuration (CS)
* Serveur de traitement (PS)
* Serveur cible maître (MT)

Un PS d’évolution est également sous le contrôle du partenaire.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Dans le scénario multilocataire, chaque CS utilise deux comptes

- **Compte d’accès vCenter**: utilisez ce compte pour découvrir les machines virtuelles du locataire. Des autorisations d’accès vCenter lui sont affectées (comme décrit dans la section suivante). Afin d’éviter les fuites d’accès accidentelles, nous recommandons aux partenaires d’entrer eux-mêmes ces informations d’identification dans l’outil de configuration.

- **Compte d’accès de machine virtuelle** : utilisez ce compte pour installer l’agent de mobilité sur les machines virtuelles du locataire via une transmission automatique. Il s’agit généralement d’un compte de domaine qu’un locataire peut fournir à un partenaire ou bien que le partenaire peut gérer directement. Si un locataire ne souhaite pas partager directement ses informations avec le partenaire, il peut être autorisé à entrer ses informations d’identification via un accès limité dans le temps au CS ou, avec l’aide du partenaire, à installer des agents de mobilité manuellement.

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
>| Serveur d’administration | Azure_Site_Recovery | Inclut l’accès à tous les composants (CS, PS et MT) si l’un de ceux-ci ne se trouve pas sur l’ordinateur du CS. |
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

## <a name="csp-program-overview"></a>Vue d’ensemble du programme CSP
Le [programme CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) met en avant des témoignages de collaboration qui offrent aux partenaires tous les services cloud de Microsoft, notamment Office 365, Enterprise Mobility Suite et Microsoft Azure. Grâce à CSP, nos partenaires établissent une relation de bout en bout avec les clients et deviennent le point de contact principal. Les partenaires peuvent déployer des abonnements Azure pour les clients et combiner les abonnements avec leurs propres offres personnalisées à valeur ajoutée.

Grâce à Azure Site Recovery, les partenaires peuvent gérer la solution de récupération d’urgence complète pour les clients directement via le CSP. Ils peuvent également utiliser le CSP pour configurer des environnements de récupération de site et permettre aux clients de gérer leurs propres besoins de récupération d’urgence en libre-service. Dans les deux cas, les partenaires jouent le rôle de liaison entre la récupération de site et leurs clients. Les partenaires assument la relation client et facturent les clients pour l’utilisation de la récupération de site.

## <a name="create-and-manage-tenant-accounts"></a>Créer et gérer des comptes locataires

### <a name="step-0-prerequisite-check"></a>Étape 0 : Vérifier les prérequis

Les prérequis de la machine virtuelle sont les mêmes que ceux décrits dans la [documentation d’Azure Site Recovery](site-recovery-vmware-to-azure.md). Outre ces prérequis, vous devez disposer des contrôles d’accès mentionnés précédemment avant de procéder à la gestion des locataires via CSP. Pour chaque locataire, créez un serveur d’administration distinct qui peut communiquer avec les machines virtuelles du locataire et le vCenter du partenaire. Seul le partenaire dispose de droits d’accès à ce serveur.

### <a name="step-1-create-a-tenant-account"></a>Étape 1 : Créer un compte locataire

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), connectez-vous à votre compte CSP.

2. Dans le menu **Tableau de bord**, sélectionnez **Clients**.

    ![Le lien Clients de Microsoft Partner Center](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. Sur la page qui s’affiche, cliquez sur le bouton **Ajouter un client**.

    ![Le bouton Ajouter un client](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. Sur la page **Nouveau client**, renseignez les informations sur le compte pour le locataire, puis cliquez sur **Suivant : Abonnements**.

    ![La page Informations du compte](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. Sur la page de sélection des abonnements, sélectionnez la case à cocher **Microsoft Azure**. Vous pouvez ajouter d’autres abonnements maintenant ou à tout moment.

    ![La case à cocher d’abonnement Microsoft Azure](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. Sur la page **Révision**, vérifiez les détails du locataire, puis cliquez sur **Envoyer**.

    ![La page Révision](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    Une fois que vous avez créé le compte locataire, une page de confirmation affichant les détails du compte par défaut et le mot de passe de cet abonnement apparaît.

7. Enregistrez les informations et modifiez le mot de passe ultérieurement si nécessaire via la page de connexion au portail Azure.  

    Vous pouvez partager ces informations avec le locataire en l’état, ou vous pouvez créer et partager un compte distinct si nécessaire.

### <a name="step-2-access-the-tenant-account"></a>Étape 2 : Accéder au compte locataire

Vous pouvez accéder à l’abonnement du locataire via le tableau de bord du Microsoft Partner Center, comme décrit dans « Étape 1 : Créer un compte locataire. »

1. Accédez à la page **Clients**, puis cliquez sur le nom du compte locataire.

2. Sur la page **Abonnements** du compte locataire, vous pouvez surveiller les abonnements de compte existants et ajouter d’autres abonnements si nécessaire. Pour gérer les opérations de récupération d’urgence du locataire, sélectionnez **Toutes les ressources (portail Azure)**.

    ![Le lien Toutes les ressources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  

    Cliquer sur **Toutes les ressources** vous permet d’accéder aux abonnements Azure du locataire. Vous pouvez vérifier l’accès en cliquant sur le lien Azure Active Directory en haut à droite du portail Azure.

    ![Lien Azure Active Directory](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Vous pouvez maintenant effectuer toutes les opérations de récupération de site pour le locataire via le portail Azure et gérer les opérations de récupération d’urgence. Pour accéder à l’abonnement locataire via CSP pour la récupération d’urgence gérée, suivez la procédure décrite précédemment.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>Étape 3 : Déployer des ressources sur l’abonnement locataire
1. Sur le portail Azure, créez un groupe de ressources, puis déployez un coffre Recovery Services en suivant la procédure habituelle.

2. Téléchargez la clé d’inscription du coffre.

3. Inscrivez le serveur de configuration du locataire à l’aide de la clé d’inscription du coffre.

4. Entrez les informations d’identification pour les deux comptes d’accès : compte d’accès vCenter et compte d’accès de machine virtuelle.

    ![Comptes serveur de configuration de gestionnaire](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Étape 4 : Inscrire l’infrastructure de récupération de site dans le coffre Recovery Services
1. Dans le portail Azure, dans le coffre que vous avez créé précédemment, inscrivez le serveur vCenter sur le CS que vous avez inscrit dans « Étape 3 : Déployer des ressources sur l’abonnement locataire. » Pour cela, utilisez le compte d’accès vCenter.
2. Terminez la « Préparation de l’infrastructure » pour la récupération de site en suivant la procédure habituelle.
3. Les machines virtuelles sont maintenant prêtes pour la réplication. Vérifiez que seules les machines virtuelles du locataire sont affichées dans le panneau **Sélectionner des machines virtuelles** sous l’option **Répliquer**.

    ![Liste des machines virtuelles de locataire dans le panneau Sélectionner des machines virtuelles](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>Étape 5 : Affecter un accès locataire à l’abonnement

Pour la récupération d’urgence en libre-service, fournissez au locataire les détails du compte, comme indiqué à l’étape 6 de la section « Étape 1 : Créer un compte locataire ». Effectuez cette action une fois que le partenaire a défini l’infrastructure de récupération d’urgence. Si le type de récupération d’urgence est géré ou en libre-service, les partenaires doivent accéder aux abonnements locataire via le portail CSP. Ils configurent le coffre appartenant à un partenaire et inscrivent l’infrastructure dans les abonnements locataire.

Les partenaires peuvent également ajouter un nouvel utilisateur à l’abonnement locataire via le portail CSP en procédant comme suit :

1. Accédez à la page d’abonnement CSP du locataire, puis sélectionnez l’option **Utilisateurs et licences**.

    ![La page d’abonnement CSP du locataire](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Vous pouvez maintenant créer un nouvel utilisateur en entrant les informations utiles et en sélectionnant des autorisations ou en téléchargeant la liste des utilisateurs dans un fichier CSV.

2. Lorsque vous avez créé un nouvel utilisateur, revenez au portail Azure puis, sur le panneau **Abonnement**, sélectionnez l’abonnement approprié.

3. Sur le panneau qui s’affiche, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **Ajouter** pour ajouter un utilisateur avec le niveau d’accès approprié.      
    Les utilisateurs qui ont été créés via le portail CSP apparaissent automatiquement dans le panneau qui s’affiche après avoir cliqué sur un niveau d’accès.

    ![Ajouter un utilisateur](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Le rôle *Contributeur* suffit pour la plupart des opérations de gestion. Les utilisateurs disposant de ce niveau d’accès peuvent effectuer toutes les opérations sur un abonnement, à l’exception du changement de niveau d’accès (pour cette opération, le niveau d’accès *Propriétaire* est nécessaire). Vous pouvez également régler les niveaux d’accès en fonction des besoins.
