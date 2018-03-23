---
title: " Gérer des serveurs VMware vCenter dans Azure Site Recovery | Microsoft Docs"
description: Cet article explique comment ajouter et gérer VMware vCenter dans Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: be415340da09043eccd361b0168bb304d8904bef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="manage-vmware-vcenter-servers"></a>Gérer des serveurs VMware vCenter 

Cet article présente les différentes opérations Site Recovery qui peuvent être effectuées sur un serveur VMware vCenter. Vérifiez les [conditions préalables](vmware-physical-azure-support-matrix.md#replicated-machines) avant de commencer.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurer un compte pour la détection automatique

Site Recovery a besoin d’accéder à VMware pour que le serveur de processus découvre automatiquement des machines virtuelles ainsi que pour le basculement et la restauration automatique des machines virtuelles. Créez un compte d’accès comme suit :

1. Connectez-vous à la machine serveur de configuration.
2. Ouvrez l’exécutable cspsconfigtool.exe à l’aide du raccourci Bureau.
3. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Fournissez les détails du compte, puis cliquez sur **OK** pour l’ajouter.  Le compte doit disposer des privilèges résumés dans le tableau suivant. 

La synchronisation des informations du compte avec le service Site Recovery dure environ 15 minutes.

### <a name="account-permissions"></a>Autorisations du compte

|**Tâche** | **Compte** | **autorisations** | **Détails**|
|--- | --- | --- | ---|
|**Détection automatique/migrer (sans restauration automatique)** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|
|**Réplication/basculement** | Vous devez disposer d’au moins un utilisateur en lecture seule| Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).<br/><br/> Utile à des fins de migration, mais pas pour la réplication complète, le basculement et la restauration automatique.|
|**Réplication/basculement/restauration automatique** | Nous vous suggérons de créer un rôle (AzureSiteRecoveryRole) avec les autorisations nécessaires, puis d’attribuer le rôle à un utilisateur ou à un groupe d’utilisateurs VMware | Objet de centre de données -> Propager vers l’objet enfant, rôle = AzureSiteRecoveryRole<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).|


## <a name="add-vmware-server-to-the-vault"></a>Ajouter un serveur VMware dans le coffre

1. Sur le portail Azure, ouvrez votre coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
2. Sur la page **Détails**, cliquez sur **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modifier les informations d’identification

Modifiez les informations d’identification utilisées pour se connecter au serveur vCenter ou à l’hôte vSphere ESXi comme suit :

1. Connectez-vous au serveur de configuration et exécutez le fichier cspconfigtool.exe depuis le Bureau.
2. Cliquez sur **Ajouter un compte** dans l’onglet **Gérer les comptes**.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Fournissez les détails du nouveau compte, puis cliquez sur **OK** pour l’ajouter. Le compte doit disposer des privilèges répertoriés [ci-dessus](#account-permissions).
4. Sur le portail Azure, ouvrez le coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
5. Sur la page **Détails**, cliquez sur **Actualiser le serveur**.
6. Une fois l’actualisation du serveur terminée, sélectionnez le serveur vCenter pour ouvrir la page **Résumé** vCenter.
7. Sélectionnez le compte qui vient d’être ajouté dans le champ **vCenter server/vSphere host account** (Compte d’hôte vSphere/de serveur vCenter), puis cliquez sur le bouton **Enregistrer**.

    ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Supprimer un serveur vCenter

1. Sur le portail Azure, ouvrez votre coffre > **Site Recovery Infrastructure** (Infrastructure Site Recovery) > **Configuration Severs** (Serveurs de configuration) et ouvrez le serveur de configuration.
2. Sur la page **Détails**, sélectionnez le serveur vCenter.
3. Cliquez sur le bouton **Supprimer**.

  ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Si vous devez modifier l’adresse IP, le nom de domaine complet et le port du serveur vCenter, vous devez supprimer le serveur vCenter et l’ajouter à nouveau au portail.
