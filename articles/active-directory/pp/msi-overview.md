---
title: "Identité du service administré (MSI) pour Azure Active Directory"
description: "Vue d’ensemble de l’identité du service administré (MSI) pour les ressources Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 53577c8da5f82235284d1cb9e48f2d47254aa6bd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identité du service administré (MSI) pour les ressources Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La gestion des informations d’identification qui doivent se trouver dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement ou ne sont jamais archivées dans le contrôle de code source. Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. L’identité du service administré (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement gérée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Lorsque vous utilisez Managed Service Identity sur une instance de service Azure, Azure crée une identité dans l’abonné Azure AD utilisé par votre abonnement Azure. En outre, Azure approvisionne les informations d’identification pour l’identité dans l’instance de service. Par conséquent, votre code peut ensuite effectuer une requête locale pour obtenir des jetons d’accès pour les services qui prennent en charge l’authentification Azure AD. Azure prend en charge la restauration des informations d’identification utilisées par l’instance de service.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Comment faire pour que mes ressources utilisent une identité de service administré ?

Il existe deux types d’identités de service administré : *celles affectées par le système* et *celles affectées par l’utilisateur*.

- Une identité de service administré **affectée par le système** est activée directement sur une instance de service Azure. Via le processus d’activation, Azure crée une identité pour l’instance de service dans l’abonné Azure AD et approvisionne les informations d’identification pour l’identité sur l’instance de service. Le cycle de vie d’une identité de service administré affectée par le système est directement lié à l’instance de service Azure sur laquelle elle est activée. Si l’instance de service est supprimée, Azure efface automatiquement les informations d’identification et l’identité dans Azure AD.

- Une identité de service administré **affectée par l’utilisateur** *(préversion privée)* est créée en tant que ressource Azure autonome. Via le processus de création, Azure crée une identité dans l’abonné Azure AD. Une fois l’identité créée, elle peut être affectée à une ou plusieurs instances de service Azure. Dans la mesure où une identité de service administré affectée par l’utilisateur peut être utilisée par plusieurs instances du service Azure, son cycle de vie est géré séparément.

Voici un exemple du fonctionnement d’une identité de service administré affectée par le système avec des machines virtuelles Azure.

![Exemple d’identité du service administré d’une machine virtuelle](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager reçoit un message pour activer l’identité de service administré affectée par le système sur une machine virtuelle.
2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité de la machine virtuelle. Le principal de service est créé dans l’abonné Azure AD approuvé par cet abonnement.
3. Azure Resource Manager configure les détails du principal de service dans l’extension de l’identité de service administré de la machine virtuelle. Cette étape inclut la configuration de l’ID de client et du certificat utilisés par l’extension pour obtenir des jetons d’accès d’Azure AD.
4. Maintenant que l’identité du principal de service de la machine virtuelle est connue, elle peut accéder aux ressources Azure. Par exemple, si votre code doit appeler Azure Resource Manager, il vous faut ensuite attribuer le rôle approprié au principal de service de la machine virtuelle à l’aide du contrôle d’accès en fonction du rôle (RBAC) dans Azure AD.  Si votre code doit appeler Key Vault, cela signifie que vous devez accorder à votre code un accès au secret spécifique ou à la clé dans Key Vault.
5. Votre code en cours d’exécution sur la machine virtuelle demande un jeton à partir d’un point de terminaison local qui est hébergé par l’extension de l’identité de service administré de la machine virtuelle : http://localhost:50342/oauth2/token. Le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Par exemple, si vous souhaitez que votre code s’authentifie sur Azure Resource Manager, vous devez utiliser resource= https://management.azure.com/.
6. L’extension de l’identité de service administré de la machine virtuelle utilise son ID de client et son certificat configurés pour demander un jeton d’accès émanant d’Azure AD.  Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

Dans le même diagramme, voici un exemple du fonctionnement d’une identité de service administré affectée par l’utilisateur avec des machines virtuelles Azure.

![Exemple d’identité du service administré d’une machine virtuelle](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager reçoit un message pour créer l’identité de service administré affectée par l’utilisateur.
2. Azure Resource Manager crée un principal de service dans Azure AD pour représenter l’identité de service administré. Le principal de service est créé dans l’abonné Azure AD approuvé par cet abonnement.
3. Azure Resource Manager reçoit un message pour configurer les détails du principal de service dans l’extension de l’identité de service administré de la machine virtuelle. Cette étape inclut la configuration de l’ID de client et du certificat utilisés par l’extension pour obtenir des jetons d’accès d’Azure AD.
4. Maintenant que l’identité du principal de service de l’identité de service administré est connue, elle peut accéder aux ressources Azure. Par exemple, si votre code doit appeler Azure Resource Manager, il vous faut ensuite attribuer le rôle approprié au principal de service de l’identité de service administré à l’aide du contrôle d’accès en fonction du rôle (RBAC) dans Azure AD. Si votre code doit appeler Key Vault, cela signifie que vous devez accorder à votre code un accès au secret spécifique ou à la clé dans Key Vault. Remarque : l’étape 3 n’est pas requise avant l’étape 4. Une fois qu’une identité de service administré existe, elle peut recevoir un accès aux ressources, qu’elle soit configurée comme une machine virtuelle ou non.
5. Votre code en cours d’exécution sur la machine virtuelle demande un jeton à partir d’un point de terminaison local qui est hébergé par l’extension de l’identité de service administré de la machine virtuelle : http://localhost:50342/oauth2/token. Le paramètre d’ID de client spécifie le nom de l’identité de service administré à utiliser. De plus, le paramètre de ressource spécifie le service vers lequel le jeton est envoyé. Par exemple, si vous souhaitez que votre code s’authentifie sur Azure Resource Manager, vous devez utiliser resource= https://management.azure.com/.
6. L’extension de machine virtuelle d’identité de service administré vérifie si le certificat associé à l’ID de client demandé est configuré et demande un jeton d’accès à Azure AD. Azure AD renvoie un jeton d’accès JSON Web Token (JWT).
7. Votre code envoie le jeton d’accès sur un appel à un service qui prend en charge l’authentification Azure AD.

Chaque service Azure qui prend en charge l’identité du service administré a sa propre méthode permettant à votre code d’obtenir un jeton d’accès. Consultez les didacticiels pour chaque service, afin de connaître la méthode spécifique pour obtenir un jeton.

## <a name="try-managed-service-identity"></a>Essayer l’identité du service administré

Essayez un didacticiel d’identité du service administré afin d’en savoir plus sur les scénarios de bout en bout pour l’accès à d’autres ressources Azure :
<br><br>
| À partir d’une ressource compatible avec l’identité du service administré | Découvrez comment |
| ------- | -------- |
| Machine virtuelle Azure (Linux)   | [Accéder à Azure Resource Manager avec l’identité du service administré d’une machine virtuelle Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Accéder au stockage Azure via une clé d’accès avec l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Quels services Azure prennent en charge l’identité du service administré ?

Les services Azure qui prennent en charge l’identité du service administré peuvent l’utiliser pour s’authentifier sur les services prenant en charge l’authentification Azure AD.  Nous sommes en train d’intégrer l’identité de service administré et l’authentification Azure AD sur Azure.  Vérifiez régulièrement cette page si des mises à jour sont disponibles.

### <a name="azure-services-that-support-managed-service-identity"></a>Services Azure qui prennent en charge l’identité du service administré

Les services Azure suivants prennent en charge l’identité du service administré.

| de diffusion en continu | Statut | Date | Configuration | Obtention d’un jeton |
| ------- | ------ | ---- | --------- | ----------- |
| Machines virtuelles Azure | VERSION PRÉLIMINAIRE | Septembre 2017 | [interface de ligne de commande Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modèles Microsoft Azure Resource Manager](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec les services clients qui utilisent l’identité du service administré.

| de diffusion en continu | ID de ressource | Statut | Date | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Disponible | Septembre 2017 | [interface de ligne de commande Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Disponible | Septembre 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponible | Septembre 2017 | |
| Azure SQL | https://database.windows.net/ | Disponible | Octobre 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Combien coûte l’identité du service administré ?

L’identité du service administré est fournie avec Azure Active Directory Free, qui est l’abonnement Azure par défaut.  L’identité du service administré n’engendre pas de coûts supplémentaires.

## <a name="support-and-feedback"></a>Support et commentaires

Nous sommes à votre écoute !

* Posez des questions sur Stack Overflow avec la balise [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Soumettez vos demandes de fonctionnalités ou vos commentaires sur le [forum de commentaires Azure AD pour les développeurs](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






