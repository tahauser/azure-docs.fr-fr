---
title: "Sécuriser les services principaux à l’aide d’une authentification de certificat client dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment sécuriser des services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: c8573c73b0a6dc1b2644f78276d2f3e4fd442cea
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Comment sécuriser les services principaux à l'aide d'une authentification par certificat client dans la Gestion des API Azure
La Gestion des API permet de sécuriser l'accès au service principal d'une API en utilisant des certificats client. Ce guide explique comment gérer les certificats dans le portail des éditeurs de l’API et comment configurer une API pour utiliser un certificat et accéder à son service principal.

Pour en savoir plus sur la gestion des certificats à l’aide de l’API REST de gestion des API, consultez [Entité de certificat API REST de gestion des API Azure][Azure API Management REST API Certificate entity].

## <a name="prerequisites"></a>Configuration requise
Ce guide explique comment configurer votre instance de service de gestion des API afin d'utiliser l'authentification par certificat pour accéder au service principal d'une API. Avant de suivre la procédure présentée dans cette rubrique, vous devez configurer votre service principal pour l’authentification avec certificat client ([pour configurer l’authentification avec certificat dans Sites Web Azure, consultez cet article ][to configure certificate authentication in Azure WebSites refer to this article]) et avoir accès au certificat et au mot de passe associés afin de les charger dans le portail de publication de la gestion des API.

## <a name="step1"></a>Chargement d’un certificat client
Pour commencer, cliquez sur **Portail des éditeurs** dans le portail Azure de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs d’API][api-management-management-console]

> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].
> 
> 

Cliquez sur **Sécurité** dans le menu **Gestion des API** de gauche, puis sur **Certificats clients**.

![Certificats clients][api-management-security-client-certificates]

Pour charger un nouveau certificat, cliquez sur **Charger un certificat**.

![Charger un certificat][api-management-upload-certificate]

Accédez à votre certificat, puis entrez le mot de passe pour le certificat.

> Le certificat doit être au format **.pfx** . Les certificats auto-signés sont autorisés.
> 
> 

![Charger un certificat][api-management-upload-certificate-form]

Cliquez sur **Charger** pour charger le certificat.

> Le mot de passe du certificat est validé à ce moment. S'il est incorrect, un message d'erreur s'affiche.
> 
> 

![Certificat chargé][api-management-certificate-uploaded]

Lorsque le certificat est chargé, il s'affiche dans l'onglet **Certificats clients** . Si vous avez plusieurs certificats, faites une note indiquant les quatre derniers caractères de l’empreinte numérique, qui sont utilisés pour sélectionner le certificat lors de la configuration d’une API pour l’utilisation de certificats, tel qu’indiqué dans la section suivante [Configuration d’une API afin d’utiliser un certificat pour l’authentification de passerelle][Configure an API to use a client certificate for gateway authentication].

> Pour désactiver la validation des chaînes de certificat lorsque vous utilisez, par exemple, un certificat auto-signé, suivez les étapes décrites dans cet [élément](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) de FAQ.
> 
> 

## <a name="step1a"></a>Suppression d’un certificat client
Pour supprimer un certificat, cliquez sur **Supprimer** à côté du certificat en question.

![Suppression d'un certificat][api-management-certificate-delete]

Cliquez sur **Oui, le supprimer** pour confirmer la suppression.

![Confirmation de suppression][api-management-confirm-delete]

Si le certificat est en cours d'utilisation par une API, un écran d'avertissement s'affiche. Pour supprimer le certificat, vous devez d'abord le supprimer de toutes les API configurées pour l'utiliser.

![Confirmation de suppression][api-management-confirm-delete-policy]

## <a name="step2"></a>Configuration d'une API afin d'utiliser un certificat pour l'authentification de passerelle
Cliquez sur **API** dans le menu **Gestion des API** de gauche, cliquez sur le nom de l’API désirée, puis cliquez sur l’onglet **Sécurité**.

![Sécurité API][api-management-api-security]

Sélectionnez **Certificats client** dans la liste déroulante **Avec informations d’identification**.

![Certificats clients][api-management-mutual-certificates]

Sélectionnez le certificat désiré dans la liste déroulante **Certificat client** . Si plusieurs certificats s'affichent, vous pouvez consulter le sujet ou les quatre derniers caractères de l'empreinte numérique (notés dans la section précédente) afin d'identifier le certificat correct.

![Sélection de certificat][api-management-select-certificate]

Cliquez sur **Enregistrer** pour enregistrer la modification de configuration de l'API.

> Cette modification s'applique immédiatement, et les appels d'opérations de cette API utiliseront désormais ce certificat pour s'authentifier sur le serveur principal.
> 
> 

![Enregistrement des modifications d'API][api-management-save-api]

> Lorsqu’un certificat est spécifié pour l’authentification passerelle d’un service principal d’une API, il est intégré à la stratégie de cette API et peut être affiché dans l’éditeur de stratégies.
> 
> 

![Stratégie de certificat][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Certificats auto-signés

Si vous utilisez des certificats auto-signés, vous devrez désactiver la validation de chaîne de certificats afin que Gestion des API puisse communiquer avec le système principal. Dans le cas contraire, un code d’erreur 500 est généré. Pour configurer ce paramètre, vous pouvez utiliser les applets de commande PowerShell [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pour un nouveau serveur principal) ou [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pour un serveur principal existant) et configurez le paramètre `-SkipCertificateChainValidation` sur `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur les autres méthodes de sécurisation de votre service principal, telles que l’authentification HTTP de base ou partagée, regardez la vidéo suivante

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



