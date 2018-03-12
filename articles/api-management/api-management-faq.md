---
title: "FAQ sur la gestion des API Azure | Microsoft Docs"
description: "Découvrez les réponses aux questions fréquemment posées, les modèles et les bonnes pratiques pour la gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 091cfaaf8fa1cccf2f8b819732946bbbd08fd426
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="azure-api-management-faqs"></a>FAQ sur la gestion des API Azure
Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure.

## <a name="contact-us"></a>Nous contacter
* [Comment dois-je procéder pour poser une question à l’équipe de gestion des API Microsoft Azure ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
* [Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Comment sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Comment copier une instance de service Gestion des API vers une nouvelle instance ?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Puis-je gérer mon instance de gestion des API par programme ?](#can-i-manage-my-api-management-instance-programmatically)
* [Comment ajouter un utilisateur au groupe d’administrateurs ?](#how-do-i-add-a-user-to-the-administrators-group)
* [Pourquoi la stratégie que je souhaite ajouter n’est-elle pas disponible dans l’éditeur de stratégie ?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Comment configurer plusieurs environnements dans une seule API ?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Puis-je utiliser SOAP avec le service Gestion des API ?](#can-i-use-soap-with-api-management)
* [L’adresse IP de la passerelle de gestion des API est-elle constante ? Puis-je l’utiliser dans les règles de pare-feu ?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Puis-je configurer un serveur d’autorisation OAuth 2.0 avec la sécurité AD FS ?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Quelle méthode de routage la gestion des API utilise-t-elle lors de déploiements sur plusieurs emplacements géographiques ?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Puis-je utiliser un modèle Azure Resource Manager pour créer une instance de service Gestion des API ?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Puis-je utiliser un certificat SSL auto-signé pour un service principal ?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Pourquoi l’authentification échoue-t-elle lors d’une tentative de clonage d’un référentiel GIT ?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Le service Gestion des API est-il compatible avec Azure ExpressRoute ?](#does-api-management-work-with-azure-expressroute)
* [Pourquoi exigeons-nous un sous-réseau dédié dans les réseaux virtuels de type Resource Manager lorsque le service Gestion des API est déployé dans ceux-ci ?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Quelle est la taille minimale de sous-réseau requise lors du déploiement du service Gestion des API dans un réseau virtuel ?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Puis-je déplacer un service Gestion des API d’un abonnement à un autre ?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existe-t-il des restrictions ou des problèmes connus liés à l’importation de mon API ?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Comment dois-je procéder pour poser une question à l’équipe de gestion des API Microsoft Azure ?
Vous pouvez nous contacter de l’une des façons suivantes :

* Publiez vos questions sur notre [forum MSDN de gestion des API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Envoyez-nous un e-mail à l’adresse <mailto:apimgmt@microsoft.com>.
* Envoyez-nous une demande de fonctionnalité sur le [forum de commentaires Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?
Lorsqu’une fonctionnalité est disponible en version préliminaire, cela signifie que nous souhaitons avoir votre avis sur le fonctionnement de la fonctionnalité. Une fonctionnalité en version préliminaire est complète et opérationnelle, mais peut être modifiée par nos soins en réponse aux commentaires client. Nous vous recommandons de ne pas intégrer de manière définitive une fonctionnalité en version préliminaire à votre environnement de production. Si vous avez des commentaires sur les fonctionnalités en version préliminaire, n’hésitez pas à nous le faire savoir en nous contactant via l’une des méthodes décrites dans la section [Comment dois-je procéder pour poser une question à l’équipe de gestion des API Microsoft Azure ?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Comment sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?
Il existe plusieurs manières de sécuriser la connexion entre la passerelle de gestion des API et vos services principaux. Vous pouvez :

* Utilisez l’authentification HTTP de base. Pour plus d’informations, consultez [Importer et publier votre première API](import-and-publish.md).
* Utiliser l’authentification mutuelle SSL telle que décrite dans [Comment sécuriser des services principaux à l’aide d’une authentification par certificat client dans la Gestion des API Azure](api-management-howto-mutual-certificates.md).
* Utiliser une liste blanche des adresses IP sur votre service principal. Dans tous les niveaux de gestion des API, l’adresse IP de la passerelle reste constante, avec quelques [mises en garde](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Vous pouvez configurer votre liste blanche pour autoriser cette adresse IP. Vous pouvez obtenir l’adresse IP de votre instance de gestion des API sur le tableau de bord du portail Azure.
* Connectez votre instance de gestion des API à un réseau virtuel Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Comment copier une instance de service Gestion des API vers une nouvelle instance ?
Il existe plusieurs manières de copier une instance de gestion des API vers une nouvelle instance. Vous pouvez :

* Utiliser la fonction de sauvegarde et de restauration du service Gestion des API. Pour plus d’informations, consultez [Comment implémenter une récupération d’urgence à l’aide de la fonctionnalité de sauvegarde et de restauration des services dans Gestion des API Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Créer votre propre fonctionnalité de sauvegarde et de restauration à l’aide de [l’API REST Gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilisez l’API REST pour enregistrer et restaurer les entités à partir de l’instance de service souhaitée.
* Télécharger la configuration du service à l’aide de Git et l’envoyer vers une nouvelle instance. Pour plus d’informations, consultez [Comment enregistrer et configurer votre configuration du service Gestion des API à l’aide de Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Puis-je gérer mon instance de gestion des API par programme ?
Oui, vous pouvez gérer le service Gestion des API par programme en utilisant :

* [L’API REST Gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* Le [Kit de développement logiciel (SDK) de la bibliothèque de gestion du service Gestion des API Microsoft Azure](http://aka.ms/apimsdk).
* Les applets de commande PowerShell de [déploiement du service](https://msdn.microsoft.com/library/mt619282.aspx) et de [gestion du service](https://msdn.microsoft.com/library/mt613507.aspx).

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Comment ajouter un utilisateur au groupe d’administrateurs ?
Pour ajouter un utilisateur au groupe d’administrateurs, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez au groupe de ressources qui contient l’instance de gestion des API que vous souhaitez mettre à jour.
3. Dans Gestion des API, attribuez le rôle **collaborateur de gestion des API** à l’utilisateur.

Le collaborateur nouvellement ajouté peut désormais utiliser les [applets de commande](https://msdn.microsoft.com/library/mt613507.aspx) Azure PowerShell. Voici comment se connecter en tant qu’administrateur :

1. Utilisez l’applet de commande `Login-AzureRmAccount` pour vous connecter.
2. Définissez le contexte sur l’abonnement qui contient le service à l’aide de `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtenez une URL d’authentification unique à l’aide de `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilisez l’URL pour accéder au portail d’administration.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Pourquoi la stratégie que je souhaite ajouter n’est-elle pas disponible dans l’éditeur de stratégie ?
Si la stratégie que vous souhaitez ajouter apparaît grisée dans l’éditeur de stratégie, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour être utilisée dans certaines étendues et sections de la stratégie. Pour consulter les sections de la stratégie et les étendues pour une stratégie, consultez la section Utilisation de cette stratégie dans [Stratégies Gestion des API](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Comment configurer plusieurs environnements dans une seule API ?
Il existe deux manières de configurer plusieurs environnements, par exemple un environnement de test et un environnement de production, dans une seule API. Vous pouvez :

* Héberger des API distinctes sur le même client.
* Héberger les mêmes API sur plusieurs clients.

### <a name="can-i-use-soap-with-api-management"></a>Puis-je utiliser SOAP avec le service Gestion des API ?
Les [requêtes SOAP directes](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) sont désormais prises en charge. Les administrateurs peuvent importer le WSDL de leur service SOAP afin que le service Gestion des API Azure puisse créer un composant frontal SOAP. Une documentation relative au portail des développeurs, une console de test, des stratégies et des outils d’analyse sont disponibles pour les services SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>L’adresse IP de la passerelle de gestion des API est-elle constante ? Puis-je l’utiliser dans les règles de pare-feu ?
Dans tous les niveaux de la gestion des API, l’adresse IP publique (VIP, adresse IP virtuelle) du client de gestion des API est statique pour la durée de vie du client, avec néanmoins quelques exceptions. L’adresse IP est modifiée dans les cas suivants :

* Le service est supprimé, puis recréé.
* L’abonnement au service est [suspendu](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [fait l’objet d’un avertissement ](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)(par exemple pour non-paiement), puis réactivé.
* Vous ajoutez ou supprimez le réseau virtuel Azure (vous pouvez utiliser le réseau virtuel uniquement au niveau Premium et Développeur).

Pour les déploiements multirégion, l’adresse régionale change si la région est libérée, puis réactivée (vous pouvez utiliser le déploiement multirégion uniquement au niveau Premium).

Les clients du niveau Premium configurés pour un déploiement multirégion se voient attribués une seule adresse IP publique par région.

Vous pouvez obtenir votre adresse IP (ou vos adresses, dans le cas d’un déploiement multirégion) sur la page client dans le portail Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Puis-je configurer un serveur d’autorisation OAuth 2.0 avec la sécurité AD FS ?
Pour savoir comment configurer un serveur d’autorisation OAuth 2.0 avec la sécurité Active Directory Federation Services (AD FS), consultez [Utilisation d’AD FS dans Gestion des API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Quelle méthode de routage la gestion des API utilise-t-elle lors de déploiements sur plusieurs emplacements géographiques ?
Le service Gestion des API utilise la [méthode de routage du trafic basé sur les performances](../traffic-manager/traffic-manager-routing-methods.md#performance) lors de déploiements sur plusieurs emplacements géographiques. Le trafic entrant est acheminé vers la passerelle API la plus proche. Si une région est déconnectée, le trafic entrant est automatiquement redirigé vers la passerelle suivante la plus proche. Pour en savoir plus sur les méthodes de routage, consultez [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Puis-je utiliser un modèle Azure Resource Manager pour créer une instance de service Gestion des API ?
Oui. Consultez les modèles de démarrage rapide [Service Gestion des API Azure](http://aka.ms/apimtemplate).

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Puis-je utiliser un certificat SSL auto-signé pour un service principal ?
Oui. Vous pouvez utiliser PowerShell, ou l’envoyer directement à l’API. Cela désactive la validation de chaîne de certificat et vous permet d’utiliser des certificats auto-signés ou signés de manière privée lors des communications entre la fonction Gestion des API et les services back end.

#### <a name="powershell-method"></a>Méthode PowerShell ####
Utilisez les cmdlets PowerShell [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pour un nouveau back end) ou [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pour un back end existant) et définissez le paramètre `-SkipCertificateChainValidation` sur `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Méthode de mise à jour directe de l’API ####
1. Créez une entité [Backend](https://msdn.microsoft.com/library/azure/dn935030.aspx) à l’aide du service Gestion des API.       
2. Définissez la propriété **skipCertificateChainValidation** sur **true**.     
3. Si vous ne souhaitez plus autoriser les certificats auto-signés, supprimez l’entité Backend ou définissez la propriété **skipCertificateChainValidation** sur **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Pourquoi l’authentification échoue-t-elle lors d’une tentative de clonage d’un référentiel GIT ?
Si vous utilisez le Gestionnaire d’informations d’identification GIT ou si vous essayez de cloner un référentiel Git via Visual Studio, il est possible que vous rencontriez un problème connu dans la boîte de dialogue d’informations d’identification Windows, qui limite la longueur du mot de passe à 127 caractères seulement et tronque, par conséquent, le mot de passe généré par Microsoft. Nous travaillons au raccourcissement du mot de passe. Pour l’instant, utilisez GIT Bash pour cloner votre référentiel Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Le service Gestion des API est-il compatible avec Azure ExpressRoute ?
Oui. Le service Gestion des API est compatible avec Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Pourquoi exigeons-nous un sous-réseau dédié dans les réseaux virtuels de type Resource Manager lorsque le service Gestion des API est déployé dans ceux-ci ?
Nous exigeons un sous-réseau dédié pour le service Gestion des API parce qu’il repose sur le modèle de déploiement Classic (couche PAAS V1). Alors que nous pouvons effectuer le déploiement dans un réseau virtuel Resource Manager (couche V2), il y a des conséquences à cela. Le modèle de déploiement Classic dans Azure n’est pas fortement couplé au modèle Resource Manager, donc si vous créez une ressource dans la couche V2, la couche V1 n’en a pas connaissance et des problèmes peuvent survenir, tels que la tentative d’utilisation d’une adresse IP par le service Gestion des API, alors que cette adresse IP est déjà allouée à une carte réseau (créée sur V2).
Pour en savoir plus sur la différence entre les modèles Classic et Resource Manager dans Azure, consultez [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Quelle est la taille minimale de sous-réseau requise lors du déploiement du service Gestion des API dans un réseau virtuel ?
La taille minimale de sous-réseau requise pour déployer le service Gestion des API est [/29](../virtual-network/virtual-networks-faq.md#configuration), qui est la taille minimale de sous-réseau qu’Azure prend en charge.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Puis-je déplacer un service Gestion des API d’un abonnement à un autre ?
Oui. Pour savoir comment procéder, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existe-t-il des restrictions ou des problèmes connus liés à l’importation de mon API ?
[Problèmes connus et restrictions](api-management-api-import-restrictions.md) pour les formats Open API(Swagger), WSDL et WADL.
