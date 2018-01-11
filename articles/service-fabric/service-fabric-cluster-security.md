---
title: "Sécuriser un cluster Azure Service Fabric | Microsoft Docs"
description: "Découvrez des scénarios de sécurité relatifs aux clusters Azure Service Fabric, ainsi que les différentes technologies que vous pouvez utiliser pour les implémenter."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: f0fdbd7fc4ec48037371ffa296cf668897e45b70
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scénarios de sécurité d’un cluster Service Fabric
Un cluster Azure Service Fabric est une ressource que vous possédez. Il vous incombe la responsabilité de sécuriser vos clusters pour empêcher les utilisateurs non autorisés de s’y connecter. La sécurisation des clusters est particulièrement importante lorsque vous exécutez des charges de travail de production sur le cluster. Même s’il est possible d’en créer, les clusters non sécurisés permettent aux utilisateurs anonymes de s’y connecter si les points de terminaison de gestion sont exposés sur l’Internet public. Les clusters non sécurisés ne sont pas gérés pour les charges de travail de production. 

Cet article présente des scénarios de sécurité relatifs aux clusters Azure et aux clusters autonomes, ainsi que les différentes technologies que vous pouvez utiliser pour implémenter ces scénarios :

* Sécurité nœud à nœud
* Sécurité client à nœud
* Contrôle d’accès en fonction du rôle

## <a name="node-to-node-security"></a>Sécurité nœud à nœud
La sécurité nœud à nœud aide à sécuriser la communication entre les machines virtuelles ou les ordinateurs d’un cluster. Ce scénario de sécurité garantit que seuls les ordinateurs qui sont autorisés à rejoindre le cluster peuvent participer à l’hébergement des applications et des services du cluster.

![Diagramme de communication nœud à nœud][Node-to-Node]

Les clusters exécutés dans Azure et les clusters autonomes exécutés sur Windows peuvent utiliser la [sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx) pour les ordinateurs Windows Server.

### <a name="node-to-node-certificate-security"></a>Sécurité de certificat de nœud à nœud
Service Fabric utilise des certificats de serveur X.509 que vous spécifiez dans le cadre de la configuration du type de nœud, lorsque vous créez un cluster. La fin de cet article propose un rapide aperçu de ce que sont ces certificats et de la façon dont vous pouvez les acquérir ou les créer.

Pour configurer la sécurité par certificat lors de la création du cluster, vous pouvez utiliser un modèle Azure Resource Manager dans le portail Azure, ou un modèle JSON autonome. Vous pouvez configurer un certificat principal et un certificat secondaire facultatif, qui est utilisé pour les renouvellements de certificats. Les certificats principaux et secondaires que vous spécifiez doivent être différents de ceux du client d’administration et du client en lecture seule que vous spécifiez pour la [sécurité client à nœud](#client-to-node-security).

Pour plus d’informations sur la configuration de la sécurité par certificat dans un cluster Azure, consultez [Configurer un cluster à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Pour plus d’informations sur la configuration de la sécurité par certificat dans un cluster autonome Windows Server, consultez [Sécuriser un cluster autonome sur Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Sécurité Windows de nœud à nœud
Pour plus d’informations sur la configuration Windows dans un cluster autonome Windows Server, consultez [Sécuriser un cluster autonome sous Windows avec la sécurité Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Sécurité client à nœud
La sécurité client à nœud authentifie les clients et sécurise la communication entre un client et les nœuds du cluster. Ce type de sécurité aide à garantir que seuls les utilisateurs autorisés peuvent accéder au cluster et aux applications déployées sur le cluster. Les clients sont identifiés de manière unique grâce à leurs informations d’identification de sécurité Windows ou à leurs informations d’identification de sécurité de certificat.

![Diagramme de communication client à nœud][Client-to-Node]

Les clusters exécutés dans Azure et les clusters autonomes exécutés sur Windows peuvent utiliser la [sécurité par certificat](https://msdn.microsoft.com/library/ff649801.aspx) ou la [sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Sécurité par certificat de client à nœud
Pour configurer la sécurité par certificat de type « client à nœud » lors de la création du cluster, vous pouvez utiliser un modèle Azure Resource Manager dans le portail Azure, ou un modèle JSON autonome. Pour créer le certificat, spécifiez un certificat client d’administration ou un certificat client utilisateur. Selon les bonnes pratiques, les certificats client d’administration et client utilisateur que vous spécifiez doivent être différents des certificats principaux et secondaires que vous spécifiez pour la [sécurité de nœud à nœud](#node-to-node-security). Par défaut, les certificats de cluster pour la sécurité de nœud à nœud sont ajoutés à la liste de certificats client d’administration autorisés.

Les clients se connectant au cluster avec le certificat d’administration ont un accès complet aux fonctions de gestion. Les clients se connectant au cluster avec le certificat client utilisateur en lecture seule ont uniquement un accès en lecture aux fonctions de gestion. Ces certificats sont utilisés pour le contrôle d’accès en fonction du rôle (RBAC), qui est abordé plus loin dans cet article.

Pour plus d’informations sur la configuration de la sécurité par certificat dans un cluster Azure, consultez [Configurer un cluster à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Pour plus d’informations sur la configuration de la sécurité par certificat dans un cluster autonome Windows Server, consultez [Sécuriser un cluster autonome sur Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Sécurité Azure Active Directory de client à nœud sur Azure
Pour les clusters exécutés dans Azure, vous pouvez également sécuriser l’accès aux points de terminaison de gestion à l’aide d’Azure Active Directory (Azure AD). Pour plus d’informations sur la création des artefacts Azure AD nécessaires, sur leur remplissage lors de la création du cluster, et sur la connexion aux clusters par la suite, consultez [Configurer un cluster en utilisant un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Recommandations de sécurité
Si vous utilisez la sécurité de nœud à nœud pour un cluster Azure, nous vous recommandons d’utiliser la sécurité Azure AD pour authentifier les clients et les certificats.

Pour les clusters Windows Server autonomes, nous vous recommandons d’utiliser la sécurité Windows avec des comptes de service gérés de groupe si vous disposez de Windows Server 2012 R2 et d’Active Directory. Sinon, continuez à utiliser la sécurité Windows avec les comptes Windows.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle
Vous pouvez utiliser le contrôle d’accès pour limiter l’accès à certaines opérations de cluster pour différents groupes d’utilisateurs. Ainsi, vous rendez le cluster plus sécurisé. Deux types de contrôle d’accès sont pris en charge pour les clients qui se connectent à un cluster : le rôle Administrateur et le rôle Utilisateur.

Les utilisateurs qui reçoivent le rôle Administrateur ont un accès complet aux fonctionnalités de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs qui reçoivent le rôle Utilisateur ne disposent, par défaut, que d’un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête). Ils peuvent également résoudre des applications et des services.

Configurez les rôles clients Administrateur et Utilisateur lorsque vous créez le cluster. Attribuez des rôles en fournissant des identités distinctes (par exemple, en utilisant des certificats ou Azure AD) pour chaque type de rôle. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et sur la modification des paramètres par défaut, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificats X.509 et Service Fabric
Les certificats numériques X.509 sont couramment utilisés pour authentifier les clients et les serveurs. Ils sont également utilisés pour chiffrer et signer numériquement les messages. Pour plus d’informations sur les certificats numériques X.509, consultez [Utilisation des certificats](http://msdn.microsoft.com/library/ms731899.aspx).

Quelques éléments importants à prendre en compte :

* Si vous voulez créer des certificats pour des clusters qui exécutent des charges de travail de production, utilisez un service de certificats Windows Server correctement configuré ou obtenu auprès d’une [autorité de certification](https://en.wikipedia.org/wiki/Certificate_authority) approuvée.
* N’utilisez jamais de certificats temporaires ou de certificats de test créés à l’aide d’outils tels que MakeCert.exe dans un environnement de production.
* Vous pouvez utiliser un certificat auto-signé, mais uniquement dans un cluster de test. N’utilisez pas un certificat auto-signé dans un environnement de production.

### <a name="server-x509-certificates"></a>Certificats X.509 de serveur
La tâche principale des certificats de serveur est d’authentifier un serveur (nœud) pour les clients ou d’authentifier un serveur (nœud) pour un serveur (nœud). Lorsqu’un nœud ou un client authentifie un nœud, l’une des premières choses à faire est de vérifier la valeur du nom commun du champ **Objet**. Ce nom commun ou l’un des autres noms d’objet (SAN) du certificat doit figurer dans la liste des noms communs autorisés.

Pour savoir comment générer des certificats ayant des noms SAN, consultez [Comment ajouter un autre nom d’objet à un certificat LDAP sécurisé](http://support.microsoft.com/kb/931351).

Le champ **Objet** peut avoir plusieurs valeurs. Chaque valeur est préfixée par une initialisation pour indiquer le type valeur. En règle générale, l’initialisation est **CN** (pour *common name* (nom commun)), par exemple, **CN = www.contoso.com**. Le champ **Objet** peut être vide. Si le champ facultatif **Autre nom de l’objet** est renseigné, il doit contenir à la fois le nom commun du certificat et une entrée par autre nom d’objet. Ces noms sont entrés sous forme de valeurs de **Nom DNS**.

La valeur du champ **Rôles prévus** du certificat doit inclure une valeur appropriée, comme **Authentification serveur** ou **Authentification client**.

### <a name="client-x509-certificates"></a>Certificats X.509 de client
Les certificats clients ne sont généralement pas émis par une autorité de certification tierce. En effet, le magasin personnel de l’emplacement de l’utilisateur actuel contient généralement des certificats clients placés là par une autorité racine, avec la valeur **Authentification client** dans le champ **Rôles prévus**. Le client peut utiliser ce certificat lorsque l’authentification mutuelle est nécessaire.

> [!NOTE]
> Toutes les opérations de gestion sur un cluster Service Fabric requièrent des certificats de serveur. Les certificats clients ne peuvent pas être utilisés pour la gestion.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un cluster dans Azure à l’aide d’un modèle Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Créer un cluster à l’aide du portail Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
