---
title: "Identité du service administré (MSI) pour Azure Active Directory"
description: "Vue d’ensemble de l’identité du service administré (MSI) pour les ressources Azure."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 5444e9d54bd9a2f7250ce590c3b6ced6b8b7bc51
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identité du service administré (MSI) pour les ressources Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

La gestion des informations d’identification qui doivent se trouver dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement ou ne sont jamais archivées dans le contrôle de code source. Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. Managed Service Identity (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement managée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lorsque vous activez l’identité du service administré sur un service Azure, Azure crée automatiquement une identité pour l’instance de service dans l’abonné Azure AD utilisé par votre abonnement Azure.  En arrière-plan, Azure approvisionne les informations d’identification pour l’identité dans l’instance de service.  Votre code peut ensuite faire une demande locale pour obtenir des jetons d’accès pour les services qui prennent en charge l’authentification Azure AD.  Azure prend en charge la restauration des informations d’identification utilisées par l’instance de service.  Si l’instance de service est supprimée, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.

Voici un exemple du fonctionnement de l’identité du service administré avec des machines virtuelles Azure.

![Exemple d’identité du service administré d’une machine virtuelle](./media/msi-vm-example.png)

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
| Machine virtuelle Azure (Windows) | [Accéder à Azure Data Lake Store avec une identité du service administré d’une machine virtuelle Windows](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Accéder à Azure Resource Manager avec l’identité du service administré d’une machine virtuelle Windows](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Accéder à Azure SQL avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Accéder au stockage Azure via une clé d’accès avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Accéder au stockage Azure via la signature d’accès partagé (SAP) avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Windows](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Accéder à une ressource non Azure AD avec l’identité du service administré d’une machine virtuelle Windows et Azure Key Vault](msi-tutorial-windows-vm-access-nonaad.md) |
| Machine virtuelle Azure (Linux)   | [Accéder à Azure Data Lake Store avec une identité du service administré de la machine virtuelle Linux](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Accéder à Azure Resource Manager avec l’identité du service administré d’une machine virtuelle Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Accéder au stockage Azure via une clé d’accès avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Accéder au stockage Azure via la signature d’accès partagé (SAP) avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Accéder à une ressource non Azure AD avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux et Azure Key Vault](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Utiliser l’identité du service administré avec Azure App Service ou Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Fonction Azure     | [Utiliser l’identité du service administré avec Azure App Service ou Azure Functions](/azure/app-service/app-service-managed-service-identity) |

## <a name="which-azure-services-support-managed-service-identity"></a>Quels services Azure prennent en charge l’identité du service administré ?

Les services Azure qui prennent en charge l’identité du service administré peuvent l’utiliser pour s’authentifier sur les services prenant en charge l’authentification Azure AD.  Nous sommes en train d’intégrer l’identité de service administré et l’authentification Azure AD sur Azure.  Vérifiez régulièrement cette page si des mises à jour sont disponibles.

### <a name="azure-services-that-support-managed-service-identity"></a>Services Azure qui prennent en charge l’identité du service administré

Les services Azure suivants prennent en charge l’identité du service administré.

| Service | État | Date | Configuration | Obtention d’un jeton |
| ------- | ------ | ---- | --------- | ----------- |
| Machines virtuelles Azure | VERSION PRÉLIMINAIRE | Septembre 2017 | [Portail Azure](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Interface de ligne de commande Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modèles Microsoft Azure Resource Manager](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Go](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | PRÉVERSION | Septembre 2017 | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | PRÉVERSION | Septembre 2017 | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec les services clients qui utilisent l’identité du service administré.

| Service | ID de ressource | État | Date | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Disponible | Septembre 2017 | [Portail Azure](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Interface de ligne de commande Azure](msi-howto-assign-access-CLI.md) |
| coffre de clés Azure | https://vault.azure.net/ | Disponible | Septembre 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponible | Septembre 2017 | |
| Azure SQL | https://database.windows.net/ | Disponible | Octobre 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Combien coûte l’identité du service administré ?

L’identité du service administré est fournie avec Azure Active Directory Free, qui est l’abonnement Azure par défaut.  L’identité du service administré n’engendre pas de coûts supplémentaires.

## <a name="support-and-feedback"></a>Support et commentaires

Nous sommes à votre écoute !

* Posez des questions sur Stack Overflow avec la balise [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Soumettez vos demandes de fonctionnalités ou vos commentaires sur le [forum de commentaires Azure AD pour les développeurs](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






