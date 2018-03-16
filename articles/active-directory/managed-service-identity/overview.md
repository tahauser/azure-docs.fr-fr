---
title: "Identité du service administré (MSI) pour Azure Active Directory"
description: "Vue d’ensemble de l’identité du service administré (MSI) pour les ressources Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: 2d711d4fa48a1d10d4c37b9591a66e5b746f1ca7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identité du service administré (MSI) pour les ressources Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

La gestion des informations d’identification qui doivent se trouver dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement ou ne sont jamais archivées dans le contrôle de code source. Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. L’identité du service administré (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement gérée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lorsque vous activez l’identité du service administré sur un service Azure, Azure crée automatiquement une identité pour l’instance de service dans l’abonné Azure AD utilisé par votre abonnement Azure.  En arrière-plan, Azure approvisionne les informations d’identification pour l’identité dans l’instance de service.  Votre code peut ensuite faire une demande locale pour obtenir des jetons d’accès pour les services qui prennent en charge l’authentification Azure AD.  Azure prend en charge la restauration des informations d’identification utilisées par l’instance de service.  Si l’instance de service est supprimée, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.

Voici un exemple du fonctionnement de l’identité du service administré avec des machines virtuelles Azure.

![Exemple d’identité du service administré d’une machine virtuelle](../media/msi-vm-example.png)

1. Azure Resource Manager reçoit un message pour activer l’identité du service administré sur une machine virtuelle.
2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité de la machine virtuelle. Le principal de service est créé dans l’abonné Azure AD approuvé par cet abonnement.
3. Azure Resource Manager configure les détails du principal de service dans l’extension de l’identité de service administré de la machine virtuelle.  Cette étape inclut la configuration de l’ID de client et du certificat utilisés par l’extension pour obtenir des jetons d’accès d’Azure AD.
4. Maintenant que l’identité du principal de service de la machine virtuelle est connue, elle peut accéder aux ressources Azure.  Par exemple, si votre code doit appeler Azure Resource Manager, il vous faut ensuite attribuer le rôle approprié au principal de service de la machine virtuelle à l’aide du contrôle d’accès en fonction du rôle (RBAC) dans Azure AD.  Si votre code doit appeler Key Vault, cela signifie que vous devez accorder à votre code un accès au secret spécifique ou à la clé dans Key Vault.
5. Votre code en cours d’exécution sur la machine virtuelle demande un jeton à partir d’un point de terminaison local qui est hébergé par l’extension de l’identité de service administré de la machine virtuelle : http://localhost:50342/oauth2/token.  Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Par exemple, si vous souhaitez que votre code s’authentifie sur Azure Resource Manager, vous devez utiliser resource= https://management.azure.com/.
6. L’extension de l’identité de service administré de la machine virtuelle utilise son ID de client et son certificat configurés pour demander un jeton d’accès émanant d’Azure AD.  Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

Chaque service Azure qui prend en charge l’identité du service administré a sa propre méthode permettant à votre code d’obtenir un jeton d’accès. Consultez les didacticiels pour chaque service, afin de connaître la méthode spécifique pour obtenir un jeton.

## <a name="try-managed-service-identity"></a>Essayer l’identité du service administré

Essayez un didacticiel d’identité du service administré afin d’en savoir plus sur les scénarios de bout en bout pour l’accès à d’autres ressources Azure :
<br><br>
| À partir d’une ressource compatible avec l’identité du service administré | Découvrez comment |
| ------- | -------- |
| Machine virtuelle Azure (Windows) | [Accéder à Azure Data Lake Store avec une identité du service administré d’une machine virtuelle Windows](tutorial-windows-vm-access-datalake.md) |
|                    | [Accéder à Azure Resource Manager avec l’identité du service administré d’une machine virtuelle Windows](tutorial-windows-vm-access-arm.md) |
|                    | [Accéder à Azure SQL avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](tutorial-windows-vm-access-sql.md) |
|                    | [Accéder au stockage Azure via une clé d’accès avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](tutorial-windows-vm-access-storage.md) |
|                    | [Accéder au stockage Azure via la signature d’accès partagé (SAP) avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Accéder à une ressource non Azure AD avec l’identité du service administré d’une machine virtuelle Windows et Azure Key Vault](tutorial-windows-vm-access-nonaad.md) |
| Machine virtuelle Azure (Linux)   | [Accéder à Azure Data Lake Store avec une identité du service administré de la machine virtuelle Linux](tutorial-linux-vm-access-datalake.md) |
|                    | [Accéder à Azure Resource Manager avec l’identité du service administré d’une machine virtuelle Linux](tutorial-linux-vm-access-arm.md) |
|                    | [Accéder au stockage Azure via une clé d’accès avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux](tutorial-linux-vm-access-storage.md) |
|                    | [Accéder au stockage Azure via la signature d’accès partagé (SAP) avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Accéder à une ressource non Azure AD avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux et Azure Key Vault](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Utiliser l’identité du service administré avec Azure App Service ou Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Fonction Azure     | [Utiliser l’identité du service administré avec Azure App Service ou Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Utiliser Azure Service Bus avec une identité de service administré](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Hubs d'événements Azure   | [Utiliser une identité du service managé avec Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Quels services Azure prennent en charge l’identité du service administré ?

Les services Azure qui prennent en charge l’identité du service administré peuvent l’utiliser pour s’authentifier sur les services prenant en charge l’authentification Azure AD.  Nous sommes en train d’intégrer l’identité de service administré et l’authentification Azure AD sur Azure.  Vérifiez régulièrement cette page si des mises à jour sont disponibles.

### <a name="azure-services-that-support-managed-service-identity"></a>Services Azure qui prennent en charge l’identité du service administré

Les services Azure suivants prennent en charge l’identité du service administré.

| de diffusion en continu | Statut | Date | Configuration | Obtention d’un jeton |
| ------- | ------ | ---- | --------- | ----------- |
| Machines virtuelles Azure | VERSION PRÉLIMINAIRE | Septembre 2017 | [Portail Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)<br>[Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | VERSION PRÉLIMINAIRE | Septembre 2017 | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | VERSION PRÉLIMINAIRE | Septembre 2017 | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | VERSION PRÉLIMINAIRE | Novembre 2017 | [Portail Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Foundation](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec les services clients qui utilisent l’identité du service administré.

| de diffusion en continu | ID de ressource | Statut | Date | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Disponible | Septembre 2017 | [Portail Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[interface de ligne de commande Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Disponible | Septembre 2017 | |
| Azure Data Lake | https://datalake.azure.net | Disponible | Septembre 2017 | |
| Azure SQL | https://database.windows.net | Disponible | Octobre 2017 | |
| Hubs d'événements Azure | https://eventhubs.azure.net | Disponible | Décembre 2017 | |
| Azure Service Bus | https://servicebus.azure.net | Disponible | Décembre 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Combien coûte l’identité du service administré ?

L’identité du service administré est fournie avec Azure Active Directory Free, qui est l’abonnement Azure par défaut.  L’identité du service administré n’engendre pas de coûts supplémentaires.

## <a name="support-and-feedback"></a>Support et commentaires

Nous sommes à votre écoute !

* Posez des questions sur Stack Overflow avec la balise [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Soumettez vos demandes de fonctionnalités ou vos commentaires sur le [forum de commentaires Azure AD pour les développeurs](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






