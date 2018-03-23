---
title: Ajouter un principal du service au rôle d’administrateur du serveur Azure Analysis Services | Microsoft Docs
description: Découvrez comment ajouter un principal du service d’automatisation au rôle d’administrateur du serveur
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
ms.assetid: ''
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Ajouter un principal du service au rôle d’administrateur du serveur 

 Pour automatiser les tâches PowerShell sans assistance, un principal du service doit disposer des privilèges **Administrateur du serveur** sur le serveur Analysis Services géré. Cet article décrit comment ajouter un principal du service au rôle d’administrateurs du serveur sur un serveur Azure AS.

## <a name="before-you-begin"></a>Avant de commencer
Avant d’exécuter cette tâche, vous devez disposer d’un principal du service inscrit dans Azure Active Directory.

[Créer le principal du service - Portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Créer le principal du service - PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Autorisations requises
Pour effectuer cette tâche, vous devez disposer d’autorisations [Administrateur du serveur](analysis-services-server-admins.md) sur le serveur Azure AS. 

## <a name="add-service-principle-to-server-administrators-role"></a>Ajouter un principal du service au rôle d’administrateurs du serveur

1. Dans SSMS, connectez-vous à votre serveur Azure AS.
2. Dans **Propriétés du serveur** > **Sécurité**, cliquez sur **Ajouter**.
3. Dans **Sélectionnez un utilisateur ou un groupe**, recherchez votre application inscrite par nom, sélectionnez, puis cliquez sur **Ajouter**.

    ![Rechercher le compte de principal du service](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Vérifiez l’ID de compte de principal du service, puis cliquez sur **OK**.
    
    ![Rechercher le compte de principal du service](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Pour les opérations de serveur utilisant des applets de commande AzureRm, le planificateur exécutant le principal du service doit également appartenir au rôle **Propriétaire** associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="related-information"></a>Informations connexes

* [Télécharger le module SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Télécharger SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


