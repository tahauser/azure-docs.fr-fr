---
title: " Gérer un serveur VMware vCenter dans Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment ajouter et gérer VMware vCenter dans Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 1b7a2d99cfa9f7d54d5303c21202fc34cf6d15ab
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Gérer un serveur VMware vCenter dans Azure Site Recovery
Cet article présente les différentes opérations Site Recovery qui peuvent être effectuées sur un serveur VMware vCenter.

## <a name="prerequisites"></a>Composants requis

**Prise en charge du serveur VMware vCenter et de l’hôte VMware vSphere ESX** | **Détails** |
|--- | --- |
|**Serveurs VMware locaux** | Un ou plusieurs serveurs VMware vSphere exécutant la version 6.0, 5.5, 5.1 avec les dernières mises à jour. Les serveurs doivent se trouver dans le même réseau que le serveur de configuration (ou un serveur de processus distinct).<br/><br/> Nous recommandons un serveur vCenter pour gérer les hôtes vSphere exécutant la version 6.0 ou 5.5 avec les dernières mises à jour. Seules les fonctionnalités disponibles dans la version 5.5 sont prises en charge lorsque du déploiement de la version 6.0.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique
Site Recovery a besoin d’accéder à VMware pour que le serveur de processus découvre automatiquement des machines virtuelles ainsi que pour le basculement et la restauration automatique des machines virtuelles.

* **Migrer** : si vous souhaitez uniquement migrer des machines virtuelles VMware vers Azure, sans jamais procéder à leur restauration automatique, vous pouvez utiliser un compte VMware avec un rôle en lecture seule. Ce type de rôle peut exécuter le basculement mais ne peut pas arrêter les machines source protégées. Cela n’est pas nécessaire pour la migration.
* **Réplication/restauration** : si vous souhaitez déployer la réplication complète (réplication, basculement et restauration automatique), le compte doit être en mesure d’exécuter des opérations telles que la création et la suppression de disques, l’alimentation de machines virtuelles, etc.
* **Découverte automatique** : au moins un compte en lecture seule est nécessaire.


|**Tâches :** | **Nécessite un compte/rôle** | **Autorisations** | **Détails**|
|--- | --- | --- | ---|
|**Le serveur de processus découvre automatiquement les machines virtuelles VMware** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|
|**Type de basculement** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).<br/><br/> Utile à des fins de migration, mais pas pour la réplication complète, le basculement et la restauration automatique.|
|**Basculement et restauration automatique** | Nous vous suggérons de créer un rôle (AzureSiteRecoveryRole) avec les autorisations nécessaires, puis d’attribuer le rôle à un utilisateur ou à un groupe d’utilisateurs VMware | Objet de centre de données -> Propager vers l’objet enfant, rôle = AzureSiteRecoveryRole<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Créer un compte pour se connecter au serveur VMware vCenter/à l’hôte VMware vSphere EXSi
1. Connectez-vous au serveur de configuration et exécutez le fichier cspsconfigtool.exe à l’aide du raccourci sur le bureau.
2. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Fournissez les détails du compte puis cliquez sur OK pour ajouter le compte. Le compte doit avoir les privilèges répertoriés dans la section [Préparer un compte pour la découverte automatique](#prepare-an-account-for-automatic-discovery).

  >[!NOTE]
  La synchronisation des informations du compte avec le service Site Recovery dure environ 15 minutes.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Associer un serveur VMware vCenter/hôte VMware vSphere ESX (ajouter vCenter)
* Sur le portail Azure, accédez à *YourRecoveryServicesVault* > **Site Recovery Infrastructure** > **Configuration Servers** > *ConfigurationServer*
* Dans la page des détails du serveur de configuration, cliquez sur le bouton +vCenter.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Modifier les informations d’identification utilisées pour se connecter au serveur vCenter/à l’hôte vSphere ESXi

1. Se connecter au serveur de configuration et exécuter le fichier cspconfigtool.exe
2. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Fournissez les nouveaux détails du compte puis cliquez sur OK pour ajouter le compte. Le compte doit avoir les privilèges répertoriés dans la section [Préparer un compte pour la découverte automatique](#prepare-an-account-for-automatic-discovery).
4. Sur le portail Azure, accédez à *YourRecoveryServicesVault* > **Site Recovery Infrastructure** > **Configuration Servers** > *ConfigurationServer*
5. Dans la page des détails du serveur de configuration, cliquez sur le bouton **Refresh Server**.
6. Une fois l’actualisation du serveur terminée, sélectionnez le serveur vCenter pour ouvrir la page du résumé vCenter.
7. Sélectionnez le compte qui vient d’être ajouté dans le champ **vCenter server/vSphere host account**, puis cliquez sur le bouton **Save**.

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Supprimer un serveur vCenter dans Azure Site Recovery
1. Sur le portail Azure, accédez à *YourRecoveryServicesVault* > **Site Recovery Infrastructure** > **Configuration Servers** > *ConfigurationServer*
2. Dans la page des détails du serveur de configuration, sélectionnez le serveur vCenter pour ouvrir la page de résumé vCenter.
3. Cliquez sur le bouton **Delete** pour supprimer le serveur vCenter

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Si vous devez modifier les détails concernant l’adresse IP, le nom de domaine complet et le port du serveur vCenter, vous devez supprimer le serveur vCenter et l’ajouter à nouveau.
