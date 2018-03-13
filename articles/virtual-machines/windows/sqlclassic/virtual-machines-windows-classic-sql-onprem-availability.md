---
title: "Étendre des groupes de disponibilité Always On locaux à Azure | Microsoft Docs"
description: "Ce didacticiel utilise des ressources créées avec le modèle de déploiement Classic, et explique comment utiliser l’Assistant Ajout d’un réplica dans SQL Server Management Studio (SSMS) pour ajouter un réplica de groupe de disponibilité Always On dans Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Étendre des groupes de disponibilité Always On locaux à Azure
Les groupes de disponibilité Always On fournissent une haute disponibilité pour les groupes de bases de données en ajoutant des réplicas secondaires. Ces réplicas autorisent le basculement des bases de données en cas de défaillance. Ils permettent en outre de décharger les charges de travail de lecture ou les tâches de sauvegarde.

Pour étendre les groupes de disponibilité locaux à Microsoft Azure, vous pouvez approvisionner une ou plusieurs machines virtuelles Azure avec SQL Server et les ajouter en tant que réplicas à vos groupes de disponibilité locaux.

Ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure actif. Vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un groupe de disponibilité Always On local existant. Pour plus d’informations sur les groupes de disponibilité, voir [Groupes de disponibilité Always On](https://msdn.microsoft.com/library/hh510230.aspx).
* Connectivité entre le réseau local et votre réseau virtuel Azure. Pour plus d’informations sur la création de ce réseau virtuel, consultez [Création d’une connexion de site à site à l’aide du portail Azure (Classic)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## <a name="add-azure-replica-wizard"></a>assistant Add Azure Replica
Cette section vous indique comment utiliser l’ **Assistant Ajout d’un réplica Azure** pour étendre votre solution de groupe de disponibilité Always On de manière à inclure des réplicas Azure.

> [!IMPORTANT]
> **L’Assistant Ajout d’un réplica Azure** prend en charge seulement les machines virtuelles créées avec le modèle de déploiement classique. Les nouveaux déploiements de machines virtuelles doivent utiliser le modèle Resource Manager plus récent. Si vous utilisez des machines virtuelles avec Resource Manager, vous devez ajouter manuellement le réplica Azure secondaire à l’aide de commandes Transact-SQL (non montrées ici). Cet Assistant ne fonctionne pas dans le scénario Resource Manager.

1. Dans SQL Server Management Studio, développez **Haute disponibilité Always On** > **Groupes de disponibilité** > **[Nom de votre groupe de disponibilité]**.
2. Cliquez avec le bouton droit sur **Réplicas de disponibilité**, puis sélectionnez **Ajouter un réplica**.
3. Par défaut, l’ **Assistant Ajout d’un réplica à un groupe de disponibilité** s’affiche. Cliquez sur **Suivant**.  Si vous avez sélectionné l’option **Ne plus afficher cette page** en bas de la page au précédent démarrage de l’Assistant, cet écran ne s’affiche pas.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Vous devrez vous connecter à tous les réplicas secondaires existants. Cliquez sur **Connecter...** en regard de chaque réplica, ou vous pouvez cliquer sur **Connecter tout...** en bas de l’écran. Une fois authentifié, cliquez sur **Suivant** pour accéder à l’écran suivant.
5. Sur la page **Spécifier les réplicas**, plusieurs onglets apparaissent dans la partie supérieure : **Réplicas**, **Points de terminaison**, **Préférences de sauvegarde** et **Écouteur**. À partir de l’onglet **Réplicas**, cliquez sur **Ajouter un réplica Azure...** pour démarrer l’Assistant Ajout d’un réplica Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Sélectionnez un certificat de gestion Azure existant dans le magasin de certificats Windows local si vous en avez déjà installé un. Sélectionnez ou entrez l’ID d’un abonnement Azure si vous en avez utilisé un précédemment. Vous pouvez cliquer sur Télécharger pour télécharger et installer un certificat de gestion Azure et télécharger la liste des abonnements à l’aide d’un compte Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Renseignez les champs de la page avec les valeurs qui seront utilisées pour créer la machine virtuelle Azure qui hébergera le réplica.
   
   | Paramètre | Description |
   | --- | --- |
   | **Image** |Sélectionnez la combinaison système d’exploitation- SQL Server souhaitée |
   | **Taille de la machine virtuelle** |Sélectionnez la taille de machine virtuelle qui correspond le mieux à vos besoins |
   | **Nom de la machine virtuelle** |Spécifiez un nom unique pour la nouvelle machine virtuelle. Le nom doit contenir entre 3 et 15 caractères, peut contenir uniquement des lettres, des chiffres et des traits d’union, et doit commencer par une lettre et se terminer par une lettre ou un chiffre. |
   | **Nom d’utilisateur de la machine virtuelle** |Spécifiez un nom d’utilisateur qui deviendra le compte d’administrateur sur la machine virtuelle |
   | **Mot de passe administrateur de la machine virtuelle** |Indiquez un mot de passe pour le nouveau compte |
   | **Confirmer le mot de passe** |Confirmez le mot de passe du nouveau compte |
   | **Réseau virtuel** |Indiquez le réseau virtuel Azure que la nouvelle machine virtuelle doit utiliser. Pour plus d’informations sur les réseaux virtuels, voir [Présentation du réseau virtuel](../../../virtual-network/virtual-networks-overview.md). |
   | **Sous-réseau de réseau virtuel** |Indiquez le sous-réseau de réseau virtuel que la nouvelle machine virtuelle doit utiliser |
   | **Domaine** |Confirmez que la valeur par défaut indiquée pour le domaine est correcte |
   | **Nom d’utilisateur de domaine** |Spécifiez un compte qui se trouve dans le groupe d’administrateurs local sur les nœuds du cluster local |
   | **Mot de passe** |Spécifiez le mot de passe du nom d’utilisateur du domaine |
8. Cliquez sur **OK** pour valider les paramètres de déploiement.
9. Les mentions légales s’affichent. Lisez-les et cliquez sur **OK** si vous les acceptez.
10. La page **Spécifier les réplicas** apparaît de nouveau. Vérifiez les paramètres du nouveau réplica Azure sous les onglets **Réplicas**, **Points de terminaison** et **Préférences de sauvegarde**. Modifiez les paramètres pour les adapter aux besoins de votre entreprise.  Pour plus d’informations sur les paramètres figurant sous ces onglets, voir [Page Spécifier les réplicas (Assistant Nouveau groupe de disponibilité/Assistant Ajout d’un réplica)](https://msdn.microsoft.com/library/hh213088.aspx).Notez que les écouteurs ne peuvent pas être créés à l’aide de l’onglet Écouteur pour les groupes de disponibilité contenant des réplicas Azure. Par ailleurs, si un écouteur a déjà été créé avant le démarrage de l’Assistant, vous recevrez un message indiquant qu’il n’est pas pris en charge dans Azure. Nous allons voir comment créer des écouteurs dans la section **Créer un écouteur de groupe de disponibilité** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Cliquez sur **Suivant**.
12. Sélectionnez la méthode de synchronisation de données à utiliser sur la page **Sélectionner la synchronisation de données initiale** et cliquez sur **Suivant**. Pour la plupart des scénarios, sélectionnez **Synchronisation complète des données**. Pour plus d’informations sur les méthodes de synchronisation de données, voir [Page Sélectionner la synchronisation de données initiale (assistants de groupe de disponibilité AlwaysOn)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Passez en revue les résultats sur la page **Validation** . Corrigez les problèmes et réexécutez la validation si nécessaire. Cliquez sur **Suivant**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Vérifiez les paramètres sur la page **Synthèse**, puis cliquez sur **Terminer**.
15. Le processus d’approvisionnement commence. Lorsque l’Assistant a terminé, cliquez sur **Fermer** pour quitter l’Assistant.

> [!NOTE]
> L’Assistant Ajout d’un réplica Azure crée un fichier journal dans Users\nom_utilisateur\AppData\Local\SQL Server\AddReplicaWizard. Ce fichier journal peut servir à résoudre les déploiements de réplica Azure ayant échoué. Si l’Assistant ne parvient pas à exécuter une action, toutes les opérations précédentes sont annulées, y compris la suppression de la machine virtuelle approvisionnée.
> 
> 

## <a name="create-an-availability-group-listener"></a>Créer un écouteur de groupe de disponibilité
Après avoir créé le groupe de disponibilité, vous devez créer un écouteur pour que les clients puissent se connecter aux réplicas. Les écouteurs dirigent les connexions entrantes vers le réplica principal ou un réplica secondaire en lecture seule. Pour plus d’informations sur les écouteurs, voir [Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>étapes suivantes
En plus d’utiliser l’ **Assistant Ajout d’un réplica Azure** pour étendre votre groupe de disponibilité Always On dans Azure, vous pouvez également déplacer entièrement des charges de travail SQL Server vers Azure. Pour commencer, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

