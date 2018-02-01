---
title: "Créer des ressources pour une utilisation avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment préparer Azure pour la réplication de machines locales à l’aide du service Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 63290127b298efced14ad34e9223840f3229f046
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Préparer des ressources Azure pour la réplication de machines locales

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Ce didacticiel décrit comment préparer les composants Azure pour répliquer des machines virtuelles (Hyper-V ou VMware) ou des serveurs physiques Windows/Linux locaux sur Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifiez que votre compte dispose des autorisations de réplication.
> * Créez un compte de stockage Azure.
> * Définissez un réseau Azure. Quand les machines virtuelles Azure sont créées après le basculement, elles sont jointes à ce réseau Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="verify-account-permissions"></a>Vérifier les autorisations du compte

Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication pour une nouvelle machine virtuelle, vous devez avoir :

- L’autorisation de créer une machine virtuelle dans le groupe de ressources sélectionné
- L’autorisation de créer une machine virtuelle dans le réseau virtuel sélectionné
- L’autorisation d’écrire dans le compte de stockage sélectionné

Le rôle prédéfini « Contributeur de machines virtuelles » a ces autorisations. Vous devez également avoir l’autorisation de gérer les opérations Azure Site Recovery. Le rôle « Collaborateur Site Recovery » a toutes les autorisations nécessaires pour gérer les opérations Site Recovery dans un coffre Recovery Services.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Les images des machines répliquées sont conservées dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure.

1. Dans le menu [Portail Azure](https://portal.azure.com), cliquez sur **Nouveau** -> **Stockage** -> **Compte de stockage**.
2. Dans **Créer un compte de stockage** , entrez un nom correspondant au compte. Pour ces didacticiels, nous utilisons le nom **contosovmsacct1910171607**. Le nom doit être unique dans Azure, avoir entre 3 et 24 caractères, et contenir uniquement des nombres et des lettres minuscules.
3. Utilisez le modèle de déploiement **Resource Manager**.
4. Sélectionnez **Usage général** > **Standard**. Ne sélectionnez pas Stockage Blob.
5. Sélectionnez la valeur par défaut **RA-GRS** pour la redondance du stockage.
6. Sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.
7. Spécifiez un nouveau groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Pour ces didacticiels, nous utilisons le nom **ContosoRG**.
8. Sélectionnez l’emplacement géographique de votre compte de stockage. Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services. Pour ces didacticiels, nous utilisons la région **Europe de l’Ouest**.

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. Cliquez sur **Créer** pour créer le compte de stockage.

## <a name="create-a-vault"></a>création d'un coffre

1. Dans le menu Portail Azure, cliquez sur **Nouveau** > **Surveillance et gestion** >
   **Backup and Site Recovery**.
2. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Pour ce didacticiel, nous utilisons **ContosoVMVault**.
3. Sélectionnez le groupe de ressources existant nommé **contosoRG**.
4. Spécifiez la région Azure **Europe de l’Ouest** que nous utilisons dans cette série de didacticiels.
5. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord** > **Créer**.

   ![Nouveau coffre](./media/tutorial-prepare-azure/new-vault-settings.png)

   Le nouveau coffre apparaît sur le **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Quand les machines virtuelles Azure sont créées à partir du stockage après le basculement, elles sont jointes à ce réseau.

1. Dans le menu [Portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Mise en réseau** >
   **Réseau virtuel**
2. Laissez **Resource Manager** sélectionné comme modèle de déploiement. Resource Manager est le modèle de déploiement préféré.
   - Spécifiez un nom de réseau. Le nom doit être unique au sein du groupe de ressources Azure. Nous utilisons le nom **ContosoASRnet**.
   - Utilisez le groupe de ressources existant **contosoRG**.
   - Spécifiez la plage d’adresses réseau 10.0.0.0/24.
   - Pour ce didacticiel, nous n’avons pas besoin d’un sous-réseau.
   - Sélectionnez l’abonnement dans lequel créer le réseau.
   - Sélectionnez l’emplacement **Europe de l’Ouest**. Ce réseau doit se trouver dans la même région que le coffre Recovery Services.
3. Cliquez sur **Créer**.

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   La création du réseau virtuel prend quelques secondes. Une fois qu’il est créé, vous le voyez dans le tableau de bord du portail Azure.

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Préparer l’infrastructure VMware locale pour la récupération d’urgence dans Azure](tutorial-prepare-on-premises-vmware.md)
