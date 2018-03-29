---
title: Guide de sécurité Azure Storage | Microsoft Docs
description: Présente en détail les nombreuses méthodes de sécurisation d’Azure Storage, notamment (liste non exhaustive) RBAC, Storage Service Encryption, le chiffrement côté client, SMB 3.0 et Azure Disk Encryption.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: cshoe
ms.openlocfilehash: e0a398075b01b3c3750a33a9dd74b5ad1c0f71fd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-security-guide"></a>Guide de sécurité Azure Storage

## <a name="overview"></a>Vue d'ensemble

Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées :

- Toutes les données écrites dans le stockage Azure sont automatiquement chiffrées à l’aide du [Chiffrement de service de stockage (SSE)](storage-service-encryption.md). Pour plus d’informations, consultez [Annonce du chiffrement par défaut des objets blob, fichiers, tables et stockages de file d’attente Azure](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Le compte de stockage proprement dit peut être sécurisé à l’aide du contrôle d’accès en fonction du rôle et d’Azure Active Directory. 
- Les données peuvent être sécurisées en transit entre une application et Azure au moyen du [chiffrement côté client](../storage-client-side-encryption.md), de HTTPS ou de SMB 3.0.  
- Les disques de système d’exploitation et de données utilisés par les machines virtuelles Azure peuvent être chiffrés à l’aide [d’Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Il est possible d’accorder un accès délégué aux objets de données d’Azure Storage en utilisant des [signatures d’accès partagé](../storage-dotnet-shared-access-signature-part-1.md).

Cet article fournit une vue d’ensemble sur chacune de ces fonctionnalités de sécurité, qui peuvent être utilisées avec Azure Storage. Des liens vers des articles détaillés vous sont proposés pour vous permettre d’explorer de façon plus détaillée chaque fonctionnalité.

Voici les sujets qui sont abordés dans cet article :

* [Sécurité du plan de gestion](#management-plane-security) – Sécurisation de votre compte de stockage

  Le plan de gestion se compose des ressources utilisées pour gérer votre compte de stockage. Cette section, couvre modèle de déploiement Azure Resource Manager et la façon d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès à vos comptes de stockage. Il évoque aussi la gestion des clés de compte de stockage et vous expliquerons comment les régénérer.
* [Sécurité du plan de données](#data-plane-security) – Sécurisation de l’accès à vos données

  Dans cette section, nous verrons comment autoriser l’accès aux objets de données réels de votre compte de stockage (objets blob, fichiers, files d’attente et tables) en utilisant des signatures d’accès partagé et des stratégies d’accès stockées. Nous évoquerons à la fois les signatures d’accès partagé (SAP) au niveau des services et les signatures d’accès partagé au niveau des comptes. Nous verrons aussi comment limiter l’accès à une adresse IP spécifique (ou à une plage d’adresses IP), comment limiter le protocole utilisé pour HTTPS et comment révoquer une signature d’accès partagé sans attendre son expiration.
* [Chiffrement en transit](#encryption-in-transit)

  Cette section explique comment sécuriser les données pendant leur transfert vers et à partir d’Azure Storage. Nous vous livrerons des recommandations concernant l’utilisation de HTTPS et vous parlerons du chiffrement utilisé par SMB 3.0 pour les partages de fichiers Azure. Nous nous intéresserons aussi au chiffrement côté client, qui vous permet de chiffrer les données avant leur transfert vers Storage dans une application cliente et de déchiffrer les données après leur transfert à partir de Storage.
* [Chiffrement au repos](#encryption-at-rest)

  Nous parlerons du chiffrement de service de stockage (SSE), qui est maintenant activé automatiquement pour les comptes de stockage nouveaux et existants. Nous verrons aussi comment utiliser Azure Disk Encryption et explorerons les différences fondamentales entre Disk Encryption, SSE et le chiffrement côté client, ainsi que des cas d’utilisation. Nous nous pencherons brièvement sur la conformité aux normes FIPS des ordinateurs de l’administration américaine.
* Audit de l’accès d’Azure Storage à l’aide de [Storage Analytics](#storage-analytics)

  Cette section explique comment rechercher des informations dans les journaux d’analyse du stockage pour une demande donnée. Nous examinerons des données de journal d’analyse de stockage réelles et verrons comment déterminer si une demande est formulée avec la clé de compte de stockage, une signature d’accès partagé ou de façon anonyme, et si elle a abouti ou échoué.
* [Activation de clients basés sur le navigateur à l’aide de CORS](#Cross-Origin-Resource-Sharing-CORS)

  Cette section explique comment autoriser le partage des ressources cross-origin (CORS). Nous évoquerons l’accès intra-domaines, ainsi que sa gestion à l’aide des fonctionnalités CORS intégrées au stockage Azure.

## <a name="management-plane-security"></a>Sécurité du plan de gestion
Le plan de gestion se compose des opérations qui affectent le compte de stockage proprement dit. Par exemple, vous pouvez créer ou supprimer un compte de stockage, obtenir la liste des comptes de stockage d’un abonnement, récupérer les clés de compte de stockage ou régénérer les clés de compte de stockage.

Quand vous créez un compte de stockage, vous sélectionnez un modèle de déploiement Classique ou Resource Manager. Le modèle classique de création de ressources dans Azure autorise seulement un accès « tout ou rien » à l’abonnement et, à tour de rôle, au compte de stockage.

Ce guide porte essentiellement sur le modèle Resource Manager, qui est la méthode recommandée pour créer des comptes de stockage. Au lieu d’accorder l’accès à l’ensemble de l’abonnement, les comptes de stockage Resource Manager vous permettent de contrôler l’accès au plan de gestion à un niveau plus limité en utilisant le contrôle d’accès en fonction du rôle (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Comment sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle (RBAC)
Pour commencer, expliquons ce qu’est RBAC et voyons comment l’utiliser. À chaque abonnement Azure correspond un annuaire Azure Active Directory. Les utilisateurs, les groupes et les applications de cet annuaire peuvent être autorisés à gérer les ressources de l’abonnement Azure qui reposent sur le modèle de déploiement Gestionnaire de ressources. Ce type de sécurité porte le nom de contrôle d’accès en fonction du rôle (RBAC). Pour gérer cet accès, vous pouvez utiliser le [Portail Azure](https://portal.azure.com/), les [outils de l’interface de ligne de commande Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST du fournisseur de ressources de stockage Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Avec le modèle Gestionnaire de ressources, vous devez placer le compte de stockage dans un groupe de ressources et contrôler l’accès au plan de gestion de ce compte de stockage spécifique à l’aide d’Azure Active Directory. Par exemple, vous pouvez permettre à certains utilisateurs d’accéder aux clés de compte de stockage, pendant que d’autres pourront voir les informations relatives au compte de stockage, mais pas accéder aux clés de compte de stockage.

#### <a name="granting-access"></a>Octroi de l’accès
L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue appropriée. Pour octroyer l’accès à l’ensemble de l’abonnement, vous devez attribuer un rôle au niveau de l’abonnement. Vous pouvez accorder l’accès à toutes les ressources d’un groupe de ressources en octroyant des autorisations au groupe de ressources proprement dit. Vous pouvez aussi attribuer des rôles spécifiques à certaines ressources, telles que des comptes de stockage.

Voici les principaux points à prendre en compte pour accéder aux opérations de gestion d’un compte de stockage Azure à l’aide de RBAC :

* Quand vous attribuez un accès, vous attribuez en fait un rôle au compte auquel vous souhaitez accéder. Vous pouvez contrôler l’accès aux opérations servant à gérer ce compte de stockage, mais pas aux objets de données du compte. Par exemple, vous pouvez accorder une autorisation pour récupérer les propriétés du compte de stockage (par exemple, la redondance), mais pas pour accéder à un conteneur ou aux données d’un conteneur à l’intérieur de Blob Storage.
* Pour permettre à une personne d’accéder aux objets de données du compte de stockage, vous pouvez lui accorder l’autorisation de lire les clés de compte de stockage, ce qui lui permettra d’utiliser ces clés pour accéder aux objets blob, files d'attente, tables et autres fichiers.
* Il est possible d’attribuer des rôles à un compte d’utilisateur spécifique, à un groupe d’utilisateurs ou à une application déterminée.
* À chaque rôle correspond une liste d’actions et de non-actions. Par exemple, le rôle de contributeur de machine virtuelle dispose d’une action nommée « listKeys » qui autorise la lecture des clés de compte de stockage. Le contributeur dispose de « non-actions », telles que la mise à jour de l’accès des utilisateurs présents dans Active Directory.
* Les rôles relatifs au stockage sont (entre autres) les suivants :

  * Propriétaire : il peut tout gérer, y compris l’accès.
  * Contributeur : il a les mêmes prérogatives que le propriétaire, sauf qu’il ne peut pas attribuer l’accès. Une personne associée à ce rôle peut afficher et régénérer les clés de compte de stockage. Les clés de compte de stockage lui permettent d’accéder aux objets de données.
  * Lecteur : il peut afficher les informations relatives au compte de stockage, à l’exception des secrets. Par exemple, si vous attribuez un rôle avec des autorisations de lecture sur le compte de stockage, l’utilisateur qui en bénéficie peut afficher les propriétés du compte de stockage, mais il ne peut y apporter aucune modification ni afficher les clés de compte de stockage.
  * Contributeur de compte de stockage : il peut gérer le compte de stockage, à savoir lire les groupes de ressources et les ressources de l’abonnement, créer et gérer des déploiements de groupes de ressources d’abonnement. Il peut également accéder aux clés de compte de stockage, ce qui sous-entend qu’il peut accéder au plan de données.
  * Administrateur de l’accès utilisateur :  il peut gérer l’accès utilisateur au compte de stockage. Par exemple, il peut accorder un accès en lecture à un utilisateur spécifique.
  * Contributeur de machine virtuelle : il peut gérer les machines virtuelles, mais pas le compte de stockage auquel elles sont connectées. Ce rôle permet de dresser la liste des clés de compte de stockage, ce qui signifie que l’utilisateur qui en bénéficie peut mettre à jour le plan de données.

    Pour pouvoir créer une machine virtuelle, l’utilisateur doit pouvoir créer le fichier VHD correspondant dans un compte de stockage. Pour ce faire, il doit pouvoir récupérer la clé de compte de stockage et la passer à l’API chargée de créer la machine virtuelle. Autrement dit, il doit disposer de cette autorisation pour pouvoir dresser la liste des clés de compte de stockage.
* Cette possibilité de définir des rôles personnalisés est une spécificité qui vous permet de composer un ensemble d’actions à partir d’une liste d’actions applicables aux ressources Azure.
* Pour pouvoir affecter un rôle à l’utilisateur, celui-ci doit au préalable avoir été défini dans Azure Active Directory.
* Vous pouvez créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue à l’aide de PowerShell ou de l’interface de ligne de commande Azure.

#### <a name="resources"></a>Ressources
* [Contrôle d’accès en fonction du rôle Azure Active Directory](../../active-directory/role-based-access-control-configure.md)

  Cet article décrit le contrôle d’accès en fonction du rôle d’Active Directory Azure et en explique le fonctionnement.
* [RBAC : rôles intégrés](../../active-directory/role-based-access-built-in-roles.md)

  Cet article explique en détail tous les rôles intégrés disponibles dans RBAC.
* [Présentation du déploiement de Resource Manager et du déploiement classique](../../azure-resource-manager/resource-manager-deployment-model.md)

  Cet article décrit le modèle de déploiement Resource Manager et le modèle de déploiement classique et explique les avantages liés à l’utilisation de Resource Manager et des groupes de ressources. Il explique le fonctionnement des fournisseurs de solutions de calcul, de réseau et de stockage selon le modèle Resource Manager.
* [Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST](../../active-directory/role-based-access-control-manage-access-rest.md)

  Cet article montre comment utiliser l’API REST pour gérer RBAC.
* [Informations de référence sur l’API REST du fournisseur de ressources Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Cette référence d’API décrit les API que vous pouvez utiliser pour gérer votre compte de stockage par programmation.
* [Developer’s guide to auth with Azure Resource Manager API (Guide du développeur pour l’authentification avec l’API Azure Resource Manager)](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  Cet article explique l’authentification avec les API Resource Manager.
* [Role-Based Access Control for Microsoft Azure from Ignite (Contrôle d’accès en fonction du rôle pour Microsoft Azure)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Ce lien donne accès à une vidéo sur Channel 9 enregistrée à l’occasion de la conférence MS Ignite en 2015. Cette session traite des fonctionnalités de gestion d’accès et de création de rapports d’Azure et explore les bonnes pratiques en ce qui concerne la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Gestion des clés de compte de stockage
Les clés de compte de stockage sont des chaînes de 512 bits créés par Azure qui, combinées avec le nom de compte de stockage, permettent d’accéder aux objets de données présents dans le compte de stockage, notamment les blobs, les entités d’une table, les messages de file d’attente et les fichiers se trouvant sur un partage de fichiers Azure. Le contrôle d’accès aux clés de compte de stockage permet de contrôler l’accès au plan de données de ce compte de stockage.

Chaque compte de stockage a deux clés appelées « Clé 1 » et « Clé 2 » dans le [portail Azure](http://portal.azure.com/) et dans les applets de commande PowerShell. Il est possible de les régénérer manuellement au moyen de diverses méthodes, notamment avec le [Portail Azure](https://portal.azure.com/), PowerShell, l’interface de ligne de commande Azure ou par programmation en utilisant la bibliothèque cliente de stockage .NET ou l’API REST des services de Stockage Azure.

La décision de régénérer les clés de compte de stockage peut être motivée par différents facteurs.

* Vous pouvez souhaiter les régénérer à intervalles réguliers pour des raisons de sécurité.
* De même, vous régénérerez vos clés de compte de stockage si un individu a réussi à s’introduire dans une application et a pu récupérer la clé qui était codée en dur ou enregistrée dans un fichier de configuration. En pareil cas, il aura en effet un accès complet à votre compte de stockage.
* Autre cas où vos clés devront être régénérées : si votre équipe utilise une application Storage Explorer qui conserve la clé de compte de stockage et que l’un des membres de l’équipe est sur le départ. L’application continuera de fonctionner, et cette personne sera en mesure d’accéder à votre compte de stockage après être parti. Il s’agit de fait du premier motif de création de signatures d’accès partagé au niveau des comptes, qui se substituent au stockage des clés d’accès dans un fichier de configuration.

#### <a name="key-regeneration-plan"></a>Plan de régénération de clé
Vous ne pouvez pas vous permettre de régénérer la clé que vous utilisez sans aucune planification. Vous risqueriez en effet de couper tous les accès au compte de stockage, ce qui provoquerait une interruption majeure. C’est la raison pour laquelle il existe deux clés. Nous vous recommandons de régénérer une seule clé à la fois.

Avant de régénérer vos clés, veillez à dresser une liste qui recense toutes les applications qui dépendent du compte de stockage, ainsi que tous les autres services que vous utilisez dans Azure, le cas échéant. Par exemple, si vous utilisez Azure Media Services et que ce produit dépend de votre compte de stockage, vous devez resynchroniser les clés d’accès avec ce service après avoir régénéré la clé. Si vous utilisez des applications telles que Storage Explorer, vous devrez aussi fournir les nouvelles clés à ces applications. Si vous disposez de machines virtuelles dont les fichiers VHD sont stockés dans le compte de stockage, elles ne seront pas affectées par la régénération des clés de compte de stockage.

Vous pouvez régénérer vos clés dans le Portail Azure. Une fois les clés régénérées, leur synchronisation au niveau des services de stockage peut prendre jusqu’à 10 minutes.

Quand vous êtes prêt, voici la procédure générale à suivre pour modifier votre clé. Dans ce cas, vous êtes censé utiliser actuellement la clé 1 et vous apprêter à tout modifier pour utiliser la clé 2.

1. Régénérez la clé 2 de façon à la sécuriser. Vous pouvez faire cette opération dans le Portail Azure.
2. Dans toutes les applications où est stockée la clé de stockage, remplacez la valeur de clé de stockage par la nouvelle valeur de la clé 2. Testez et publiez l’application.
3. Dès lors que l’ensemble des applications et des services fonctionnent correctement, régénérez la clé 1. Cela garantit que toute personne à laquelle vous n’avez pas accordé expressément la nouvelle clé n’aura plus accès au compte de stockage.

Si vous utilisez actuellement la clé 2, vous pouvez utiliser le même processus, mais en inversant les noms des clés.

Vous pouvez effectuer la migration sur deux jours, en modifiant chaque application pour utiliser la nouvelle clé et en la publiant. Quand vous avez traité l’ensemble des services et applications, vous devez revenir en arrière et régénérer l’ancienne clé pour qu’elle ne fonctionne plus.

Une autre option consiste à placer la clé de compte de stockage dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) en tant que clé secrète et à faire en sorte que vos applications la récupèrent à cet endroit. Ensuite, quand vous régénérez la clé et mettez à jour Azure Key Vault, vous n’avez pas à redéployer les applications, car elles choisissent automatiquement la nouvelle clé dans Azure Key Vault. Notez que vous pouvez faire en sorte que l’application lise la clé chaque fois que vous en avez besoin, ou vous pouvez la mettre en cache en mémoire et, en cas d’échec lors de son utilisation, vous pouvez de nouveau la récupérer dans Azure Key Vault.

L’utilisation d’Azure Key Vault ajoute également un autre niveau de sécurité pour les clés de stockage. Si vous utilisez cette méthode, la clé de stockage n’est jamais codée en dur dans un fichier de configuration, ce qui rend impossible l’accès aux clés par une personne sans autorisation spécifique.

Un autre avantage de l’utilisation d’Azure Key Vault est que vous pouvez également contrôler l’accès à vos clés à l’aide d’Azure Active Directory. Cela signifie que vous pouvez accorder l’accès aux quelques applications qui doivent récupérer les clés dans Azure Key Vault, tout en sachant que d’autres applications ne seront pas en mesure d’accéder aux clés si l’autorisation ne leur est pas expressément accordée.

Remarque : Il est recommandé d’utiliser uniquement l’une des clés dans toutes vos applications en même temps. Si vous utilisez parfois la clé 1 et parfois la clé 2, vous ne pouvez effectuer aucune rotation de vos clés sans qu’une application ne perde l’accès.

#### <a name="resources"></a>Ressources
* [À propos des comptes Azure Storage](storage-create-storage-account.md#regenerate-storage-access-keys)

  Cet article présente une vue d’ensemble des comptes de stockage et décrit l’affichage, la copie et la régénération des clés d’accès de stockage.
* [Informations de référence sur l’API REST du fournisseur de ressources Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

  Cet article contient des liens vers des articles spécifiques sur la récupération des clés de compte de stockage et la régénération des clés de compte de stockage pour un compte Azure à l’aide de l’API REST. Remarque : ceci concerne les comptes de stockage Resource Manager.
* [Opérations sur les comptes de stockage](https://msdn.microsoft.com/library/ee460790.aspx)

  Cet article dans Informations de référence sur l’API REST du gestionnaire de service de stockage contient des liens vers des articles spécifiques sur la récupération et la régénération des clés de compte de stockage à l’aide de l’API REST. Remarque : Seuls les comptes de stockage classiques sont concernés.
* [Say goodbye to key management – manage access to Azure Storage data using Azure AD (Dites au revoir à la gestion des clés - gérer l’accès aux données Azure Storage avec Azure AD)](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  Cet article montre comment utiliser Active Directory pour contrôler l’accès à vos clés de stockage Azure dans Azure Key Vault. Il montre également comment utiliser un travail Azure Automation pour régénérer les clés toutes les heures.

## <a name="data-plane-security"></a>Sécurité du plan de données
La sécurité du plan de données fait référence aux méthodes permettant de sécuriser les objets de données stockés dans Azure Storage : les objets blob, files d’attente, tables et fichiers. Nous avons vu des méthodes qui permettent de chiffrer les données et la sécurité pendant le transit des données, mais comment faire pour contrôler l’accès aux objets ?

Deux méthodes permettent d’autoriser l’accès aux objets de données eux-mêmes. Il s’agit de contrôler l’accès aux clés de compte de stockage et d’utiliser des signatures d’accès partagé pour accorder l’accès à des objets de données spécifiques pour une durée déterminée.

Par ailleurs, pour le stockage Blob, vous pouvez autoriser un accès public à vos objets blob en définissant de manière appropriée le niveau d’accès du conteneur qui contient les objets blob. Si vous définissez l’accès pour un conteneur sur Objet blob ou Conteneur, vous autorisez l’accès en lecture public pour les objets blob de ce conteneur. Cela signifie que toute personne avec une URL pointant vers un objet blob dans ce conteneur peut l’ouvrir dans un navigateur sans utiliser de signature d’accès partagé ni disposer de clé de compte de stockage.

En plus de limiter l’accès par le biais de l’autorisation, vous pouvez également utiliser [Pare-feu et réseaux virtuels](storage-network-security.md) pour limiter l’accès au compte de stockage en fonction des règles de réseau.  Cette approche vous permet de refuser l’accès au trafic Internet public et d’accorder l’accès uniquement à certains réseaux virtuels Azure ou certaines plages d’adresses IP Internet publiques.


### <a name="storage-account-keys"></a>Clés de compte de stockage
Les clés de compte de stockage sont des chaînes de 512 bits créées par Azure qui, combinées avec le nom de compte, permettent d’accéder aux objets de données stockés dans le compte de stockage.

Par exemple, vous pouvez lire des objets blob, écrire dans des files d’attente, créer des tables et modifier des fichiers. La plupart de ces actions peuvent être effectuées avec le Portail Azure ou à l’aide de l’une des nombreuses applications de l’explorateur de stockage. Vous pouvez également écrire du code pour utiliser l’API REST ou l’une des bibliothèques clientes de stockage pour effectuer ces opérations.

Comme indiqué dans la section [Sécurité du plan de gestion](#management-plane-security), l’accès aux clés de stockage pour un compte de stockage classique peut être accordé en donnant un accès complet à l’abonnement Azure. L’accès aux clés de stockage pour un compte de stockage avec le modèle Azure Resource Manager peut être contrôlé via le contrôle d’accès en fonction du rôle (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Comment déléguer l’accès aux objets dans votre compte à l’aide de signatures d’accès partagé et stratégies d’accès stockées
Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité qui peut être associé à un URI et vous permet de déléguer l’accès aux objets de stockage et de spécifier des restrictions telles que les autorisations et la plage des dates/heures d’accès.

Vous pouvez accorder l’accès à des objets blob, conteneurs, messages de file d’attente, fichiers et tables. Avec les tables, vous pouvez en fait accorder l’autorisation d’accéder à un ensemble d’entités dans la table en spécifiant les plages de clés de partition et de ligne auxquelles vous souhaitez que l’utilisateur ait accès. Par exemple, si vous avez des données stockées avec une clé de partition d’état géographique, vous pouvez permettre à quelqu’un d’accéder uniquement aux données pour la Californie.

Dans un autre exemple, vous pouvez donner à une application web un jeton SAP qui lui permet d’écrire des entrées dans une file d’attente, et donner à une application de rôle de travail un jeton SAP pour obtenir des messages à partir de la file d’attente et les traiter. Vous pouvez également donner à un client un jeton SAP qu’il peut utiliser pour charger des images sur un conteneur de stockage d’objets blob et donner à une application web l’autorisation de lire ces images. Dans les deux cas, les problèmes sont séparés : chaque application peut recevoir uniquement l’accès dont elle a besoin pour effectuer sa tâche. Ceci est possible avec l’utilisation des signatures d’accès partagé.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Pourquoi utiliser des signatures d’accès partagé
Pourquoi utiliser une signature d’accès partagé au lieu de donner simplement votre clé de compte de stockage, ce qui est tellement plus facile ? La distribution de votre clé de compte de stockage revient à partager les clés de votre royaume de stockage. L’accès accordé est complet. Une personne peut utiliser vos clés et charger l’intégralité de son audiothèque sur votre compte de stockage. Elle peut également remplacer vos fichiers par des versions infectées par des virus, ou voler vos données. Le fait d’accorder un accès illimité à votre compte de stockage est une décision que vous ne devez pas prendre à la légère.

Avec les signatures d’accès partagé, vous pouvez donner à un client uniquement les autorisations requises pour une durée limitée. Par exemple, si un utilisateur charge un objet blob sur votre compte, vous pouvez lui accorder un accès en écriture juste le temps nécessaire de charger l’objet blob (selon la taille de l’objet blob, bien sûr). Si vous changez ensuite d’avis, vous pouvez révoquer cet accès.

En outre, vous pouvez spécifier que les demandes effectuées à l’aide d’une signature d’accès partagé doivent être limitées à une certaine adresse IP ou plage d’adresses IP externe à Azure. Vous pouvez également exiger que les demandes soient effectuées à l’aide d’un protocole spécifique (HTTPS ou HTTP/HTTPS). Cela signifie que, si vous souhaitez uniquement autoriser le trafic HTTPS, vous pouvez affecter la valeur HTTPS uniquement au protocole requis et le trafic HTTP sera bloqué.

#### <a name="definition-of-a-shared-access-signature"></a>Définition d’une signature d’accès partagé
Une signature d’accès partagé est un ensemble de paramètres de requête ajoutés à l’URL pointant vers la ressource

qui fournit des informations sur l’accès autorisé et la durée pendant laquelle l’accès est autorisé. Voici un exemple ; cet URI fournit un accès en lecture à un objet blob pendant cinq minutes. Notez que les paramètres de requête SAP doivent être encodés dans une URL, par exemple %3A pour le signe deux-points (:) ou %20 pour un espace.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Comment la signature d’accès partagé est authentifiée par le service Azure Storage
Quand le service de stockage reçoit la demande, il accepte les paramètres de requête d’entrée et crée une signature en utilisant la même méthode que le programme appelant. Il compare ensuite les deux signatures. Si elles correspondent, le service de stockage peut vérifier que la version du service de stockage est valide, que la date et l’heure sont dans la fenêtre spécifiée, que l’accès demandé correspond à la demande effectuée, etc.

Par exemple, avec notre URL ci-dessus, si l’URL pointe vers un fichier au lieu d’un objet blob, cette demande échoue, car elle spécifie que la signature d’accès partagé concerne un objet blob. Si la commande REST appelée doit mettre à jour un objet blob, elle échoue car la signature d’accès partagé spécifie que seul l’accès en lecture est autorisé.

#### <a name="types-of-shared-access-signatures"></a>Types de signatures d’accès partagé
* Une signature d’accès partagé de niveau service permet d’accéder à des ressources spécifiques dans un compte de stockage. À titre d’exemple, il peut s’agir de la récupération d’une liste d’objets blob dans un conteneur, du téléchargement d’un objet blob, de la mise à jour d’une entité dans une table, de l’ajout de messages à une file d’attente ou du chargement d’un fichier sur un partage de fichiers.
* Une signature d’accès partagé de niveau compte permet d’accéder à tout ce pour quoi une signature d’accès partagé de niveau service peut être utilisée. En outre, elle peut proposer des options pour des ressources qui ne sont pas autorisées avec une signature d’accès partagé de niveau service, telles que la capacité de créer des conteneurs, tables, files d’attente et partages de fichiers. Vous pouvez également spécifier l’accès à plusieurs services à la fois. Par exemple, vous pouvez accorder à un utilisateur l’accès à la fois aux objets blob et aux fichiers dans votre compte de stockage.

#### <a name="creating-a-sas-uri"></a>Création d’un URI SAS
1. Vous pouvez créer un URI à la demande, en définissant chaque fois l’ensemble des paramètres de requête.

   Cette approche est flexible, mais si vous avez un ensemble logique de paramètres qui sont chaque fois similaires, il est plus judicieux d’utiliser une stratégie d’accès stockée.
2. Vous pouvez créer une stratégie d’accès stockée pour un conteneur entier, un partage de fichiers, une table ou une file d’attente. Vous pouvez ensuite l’utiliser comme base pour les URI SAP que vous créez. Les autorisations basées sur les stratégies d’accès stockées peuvent être facilement révoquées. Jusqu’à cinq stratégies peuvent être définies sur chaque conteneur, file d’attente, table ou partage de fichiers.

   Par exemple, si un grand nombre de personnes doivent lire les objets blob dans un conteneur spécifique, vous pouvez créer une stratégie d’accès stockée qui indique « d’accorder l’accès en lecture » et tous les autres paramètres qui seront chaque fois les mêmes. Vous pouvez ensuite créer un URI SAP en utilisant les paramètres de la stratégie d’accès stockée et en spécifiant la date/l’heure d’expiration. L’avantage de cette opération est que vous n’avez pas à spécifier chaque fois tous les paramètres de requête.

#### <a name="revocation"></a>Révocation
Supposons que votre signature d’accès partagé a été compromise ou que vous voulez la modifier en raison des exigences de sécurité de l’entreprise ou de conformité aux réglementations. Comment révoquer l’accès à une ressource à l’aide de cette signature ? Cela dépend de la façon dont vous avez créé l’URI SAP.

Si vous utilisez des URI appropriés, vous avez trois possibilités. Vous pouvez émettre des jetons SAP avec des stratégies de délai d’expiration court et attendre que la signature d’accès partagé expire. Vous pouvez renommer ou supprimer la ressource (en supposant que le jeton a été étendu à un objet unique). Vous pouvez modifier les clés de compte de stockage. Cette dernière option peut avoir un impact important, en fonction du nombre de services qui utilisent ce compte de stockage, et n’est probablement pas souhaitable sans une certaine planification.

Si vous utilisez une signature d’accès partagé dérivée d’une stratégie d’accès stockée, vous pouvez supprimer l’accès en révoquant la stratégie d’accès stockée : vous pouvez simplement la modifier de sorte qu’elle ait déjà expiré, ou la supprimer complètement. Cette opération prend effet immédiatement et invalide toutes les signatures d’accès partagé créées à l’aide de cette stratégie d’accès stockée. La mise à jour ou la suppression de la stratégie d’accès stockée peut avoir un impact sur les personnes qui accèdent à ce conteneur, ce partage de fichiers, cette table ou cette file d’attente spécifique via une signature d’accès partagé, mais si les clients sont écrits pour demander une nouvelle signature quand l’ancienne n’est plus valide, cela fonctionne correctement.

Comme l’utilisation d’une signature d’accès partagé dérivée d’une stratégie d’accès stockée vous permet de révoquer immédiatement cette signature d’accès partagé, il est recommandé de toujours utiliser des stratégies d’accès stockées quand cela est possible.

#### <a name="resources"></a>Ressources
Pour plus d’informations sur l’utilisation des signatures d’accès partagé et des stratégies d’accès stockées, ainsi que pour obtenir des exemples, consultez les articles suivants :

* Ce sont les articles de référence.

  * [Exemples d’associations de sécurité de service](https://msdn.microsoft.com/library/dn140256.aspx)

    Cet article fournit des exemples d’utilisation d’une signature d’accès partagé de niveau service avec des objets blob, des messages de file d’attente, des plages de tables et des fichiers.
  * [Construction d’un service SAP](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Construction d’un compte SAP](https://msdn.microsoft.com/library/mt584140.aspx)
* Il s’agit de didacticiels pour l’utilisation de la bibliothèque cliente .NET pour créer des signatures d’accès partagé et des stratégies d’accès stockées.

  * [Utilisation des signatures d’accès partagé (SAP)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Signatures d’accès partagé, partie 2 : création et utilisation d’une signature d’accès partagé avec le service BLOB](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Cet article contient une description du modèle SAP, des exemples de signatures d’accès partagé et des recommandations pour une utilisation optimale de ces signatures. La révocation de l’autorisation accordée est également abordée.

* Authentification

  * [Authentification pour les services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Didacticiel de prise en main des signatures d’accès partagé

  * [SAS Getting Started Tutorial (Didacticiel de prise en main des signatures d’accès partagé)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Chiffrement en transit
### <a name="transport-level-encryption--using-https"></a>Chiffrement au niveau du transport – Utilisation de HTTPS
Une autre étape à suivre pour garantir la sécurité de vos données Azure Storage consiste à chiffrer les données entre le client et Azure Storage. La première recommandation est de toujours utiliser le protocole [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , qui garantit une communication sécurisée via l’Internet public.

Pour disposer d’un canal de communication sécurisé, vous devez toujours utiliser HTTPS lors de l’appel des API REST ou de l’accès aux objets dans le stockage. De plus, les **signatures d’accès partagé**, qui peuvent être utilisées pour déléguer l’accès aux objets de stockage Azure, incluent une option pour spécifier que seul le protocole HTTPS est autorisé avec les signatures d’accès partagé. Cette option garantit que le protocole approprié est utilisé par tous ceux qui envoient des liens avec des jetons SAP.

Vous pouvez appliquer l’utilisation du protocole HTTPS lorsque vous appelez les API REST pour accéder aux objets dans les comptes de stockage en activant l’option [Transfert sécurisé requis](../storage-require-secure-transfer.md) pour le compte de stockage. Les connexions utilisant le protocole HTTP seront refusées une fois cette option activée.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Utilisation du chiffrement pendant le transit avec des partages de fichiers Azure
Azure Files prend en charge HTTPS avec l’API REST, mais il est plus couramment utilisé comme partage de fichiers SMB attaché à une machine virtuelle. SMB 2.1 ne prend pas en charge le chiffrement. Les connexions sont donc autorisées uniquement dans la même région Azure. Toutefois, SMB 3.0 prend en charge le chiffrement et est disponible dans Windows Server 2012 R2, Windows 8, Windows 8.1 et Windows 10, ce qui rend possibles l’accès entre les régions et l’accès sur le bureau.

Si les partages de fichiers Azure peuvent être utilisés avec Unix, le client SMB de Linux ne prend pas encore en charge le chiffrement. L’accès est donc autorisé uniquement dans une région Azure. La prise en charge du chiffrement pour Linux est prévue par les développeurs Linux responsables de la fonctionnalité SMB. Quand ce chiffrement sera pris en charge, vous pourrez accéder à un partage de fichiers Azure sur Linux de la même manière que sur Windows.

Vous pouvez appliquer l’utilisation du chiffrement avec le service Azure Files en activant l’option [Transfert sécurisé requis](../storage-require-secure-transfer.md) pour le compte de stockage. Si vous utilisez les API REST, le protocole HTTPS est requis. Pour SMB, seules les connexions SMB qui prennent en charge le chiffrement seront établies avec succès.

#### <a name="resources"></a>Ressources
* [Présentation d’Azure Files](../files/storage-files-introduction.md)
* [Bien démarrer avec Azure Files sur Windows](../files/storage-how-to-use-files-windows.md)

  Cet article offre une vue d’ensemble des partages de fichiers Azure et décrit comment les monter et les utiliser sur Windows.

* [Comment utiliser Azure Files avec Linux](../files/storage-how-to-use-files-linux.md)

  Cet article montre comment monter un partage de fichiers Azure sur un système Linux et comment charger/télécharger des fichiers.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Utilisation du chiffrement côté client pour sécuriser les données envoyées dans le stockage
Le chiffrement côté client est une autre méthode possible pour garantir la sécurité de vos données pendant leur transfert entre une application cliente et Azure Storage. Les données sont chiffrées avant d’être transférées vers Azure Storage. Quand vous récupérez les données d’Azure Storage, les données sont déchiffrées seulement après leur réception côté client. Même si les données sont chiffrées quand elles sont en transit sur le réseau, nous vous recommandons d’utiliser également le protocole HTTPS. En effet, HTTPS inclut des vérifications de l’intégrité des données qui contribuent à réduire les erreurs réseau ayant un impact sur l’intégrité des données.

Le chiffrement côté client est aussi une méthode qui vous permet de chiffrer vos données au repos, car les données sont stockées dans leur forme chiffrée. Nous discuterons de ce point plus en détail dans la section sur le [chiffrement au repos](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Chiffrement au repos
Il existe trois fonctionnalités Azure qui fournissent un chiffrement au repos. La première, Azure Disk Encryption, s’utilise pour chiffrer les disques de données et de système d’exploitation utilisés par des machines virtuelles IaaS. Le chiffrement côté client et SSE, sont utilisés pour chiffrer les données dans Azure Storage. 

Vous pouvez utiliser le chiffrement côté client pour chiffrer les données en transit (ces données sont également stockées dans leur forme chiffrée dans Azure Storage). Si vous préférez, vous pouvez aussi utiliser le protocole HTTPS pendant le transfert et utiliser un autre moyen de votre choix pour chiffrer automatiquement les données stockées. Il existe deux méthodes possibles : Azure Disk Encryption et SSE. La première chiffre les données directement sur les disques de données et de système d’exploitation. La seconde chiffre les données qui sont écrites dans le stockage Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE est activé pour tous les comptes de stockage et ne peut pas être désactivé. SSE chiffre automatiquement vos données lors de leur écriture dans le stockage Azure. Lorsque vous lisez des données depuis le stockage Azure, elles déchiffrées par le stockage Azure avant d’être retournées. SSE vous permet de sécuriser vos données sans avoir à modifier le code existant ni à ajouter du code dans les applications.

Les clés utilisées pour SSE sont gérées par Microsoft. Microsoft crée initialement les clés, puis gère leur stockage sécurisé ainsi que leur rotation régulière, conformément à la politique interne de Microsoft en la matière. Les clés gérées par le client finiront par être disponibles, avec un chemin de migration des clés gérées par Microsoft en clés gérées par le client.

Le chiffrement du service de stockage chiffre automatiquement les données pour tous les niveaux de performance (Standard ou Premium), tous les modèles de déploiement (Azure Resource Manager et Classic) et tous les services de Stockage Azure (blob, file d’attente, table et fichier). 

### <a name="client-side-encryption"></a>chiffrement côté client
Nous avons déjà mentionné le chiffrement côté client quand nous avons parlé du chiffrement des données en transit. Cette fonctionnalité vous permet de chiffrer par programmation vos données dans une application cliente avant de les envoyer via le réseau vers Azure Storage, et de les déchiffrer par programmation une fois que vous les avez récupérées d’Azure Storage.

Elle fournit le chiffrement en transit, mais également le chiffrement au repos. Même si les données sont chiffrées quand elles sont en transit, nous vous recommandons encore d’utiliser le protocole HTTPS, car HTTPS inclut des vérifications de l’intégrité des données qui contribuent à réduire les erreurs réseau ayant un impact sur l’intégrité des données.

Par exemple, utilisez cette fonctionnalité si vous avez une application web qui stocke et récupère des objets blob, et si vous souhaitez sécuriser au maximum l’application et les données. Dans ce cas, le chiffrement côté client est la méthode appropriée. Le trafic entre le client et le service Azure Blob contient la ressource chiffrée, et personne ne peut interpréter les données en transit et les reconstituer dans vos objets blob privés.

Le chiffrement côté client est intégré aux bibliothèques clientes de stockage Java et .NET, qui utilisent à leur tour les API Azure Key Vault, ce qui rend son implémentation facile. Le processus de chiffrement et déchiffrement des données utilise la technique d’enveloppe, et stocke les métadonnées utilisées par le chiffrement dans chaque objet de stockage. Par exemple, pour les objets blob, il les stocke dans les métadonnées d’objet blob et, pour les files d’attente, il les ajoute à chaque message de file d’attente.

Pour le chiffrement proprement dit, vous pouvez créer et gérer vos propres clés de chiffrement. Vous pouvez également utiliser les clés générées par la bibliothèque cliente Azure Storage, ou demander à Azure Key Vault de générer les clés. Vous pouvez stocker vos clés de chiffrement dans votre stockage de clés local, ou les stocker dans un coffre Azure Key Vault. Azure Key Vault vous permet d’accorder l’accès à des clés secrètes dans Azure Key Vault à des utilisateurs spécifiques à l’aide d’Azure Active Directory. De cette manière, vous limitez les personnes autorisées à accéder au coffre de clés Azure Key Vault et à récupérer les clés que vous utilisez pour le chiffrement côté client.

#### <a name="resources"></a>Ressources
* [Chiffrement et déchiffrement d’objets blob dans Microsoft Azure Storage à l'aide d'Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Cet article montre comment utiliser le chiffrement côté client avec Azure Key Vault, notamment comment créer le certificat KEK et le stocker dans le coffre à l’aide de PowerShell.
* [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](../storage-client-side-encryption.md)

  Cet article explique le fonctionnement du chiffrement côté client. Il fournit des exemples d’utilisation de la bibliothèque cliente de stockage pour chiffrer et déchiffrer les ressources des quatre services de stockage. Il parle également d’Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Utilisation de la fonctionnalité Azure Disk Encryption pour chiffrer les disques utilisés par vos machines virtuelles
Azure Disk Encryption est une nouvelle fonctionnalité. Cette fonctionnalité vous permet de chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle IaaS. Sur Windows, les disques sont chiffrés à l’aide de la technologie de chiffrement BitLocker standard. Sur Linux, les disques sont chiffrés à l’aide de la technologie DM-Crypt. La fonctionnalité est intégrée à Azure Key Vault pour vous permettre de contrôler et gérer les clés de chiffrement de disque.

La solution prend en charge les scénarios de machines virtuelles IaaS suivants lorsqu’ils sont activés dans Microsoft Azure :

* Prise en main d’Azure Key Vault
* Machines virtuelles de niveau standard : [Machines virtuelles IaaS des séries A, D, DS, G, GS, etc.](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Activation du chiffrement sur les machines virtuelles IaaS Windows et Linux
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles IaaS Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux
* Activation du chiffrement sur les machines virtuelles IaaS exécutant le système d’exploitation client Windows
* Activation du chiffrement sur les volumes avec chemins d’accès de montage
* Activation du chiffrement sur les machines virtuelles Linux configurées avec entrelacement de disques (RAID) à l’aide de mdadm
* Activation du chiffrement sur les machines virtuelles Linux à l’aide de LVM pour les disques de données
* Activation du chiffrement sur les machines virtuelles Windows configurées à l’aide d’espaces de stockage
* Toutes les régions publiques Azure sont prises en charge

La solution ne prend pas en charge les scénarios, fonctionnalités et technologies suivants dans la version :

* Machines virtuelles IaaS de niveau de base
* Désactivation du chiffrement sur un lecteur de système d’exploitation pour les machines virtuelles IaaS Linux
* Machines virtuelles IaaS créées à l’aide de la méthode classique de création de machines virtuelles
* Intégration à votre service de gestion de clés local
* Azure Files (système de partage de fichiers), NFS (Network File System), volumes dynamiques et machines virtuelles Windows configurées avec des systèmes RAID logiciels


> [!NOTE]
> Le chiffrement de disque du système d’exploitation Linux est actuellement pris en charge sur les distributions Linux suivantes : RHEL 7.2, CentOS 7.2n et Ubuntu 16.04.
>
>

Cette fonctionnalité garantit que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans Azure Storage.

#### <a name="resources"></a>Ressources
* [Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparaison entre Azure Disk Encryption, SSE et le chiffrement côté client

#### <a name="iaas-vms-and-their-vhd-files"></a>Machines virtuelles IaaS et fichiers VHD associés

Pour les disques de données utilisés par des machines virtuelles IaaS, nous vous recommandons d’utiliser le chiffrement Azure Disk Encryption. Si vous créez une machine virtuelle à partir d’une image issue d’Azure Marketplace, Azure effectue une [copie superficielle](https://en.wikipedia.org/wiki/Object_copying) de l’image dans votre compte de stockage dans Azure Storage, mais elle ne chiffre pas les données, même si SSE est activé. SSE commence le chiffrement des données après avoir créé la machine virtuelle et démarré la mise à jour de l’image. Pour cette raison, il est préférable d’utiliser Azure Disk Encryption sur des machines virtuelles créées à partir d’images dans Place de marché Azure pour garantir le chiffrement de toutes les données.

Si vous ajoutez une machine virtuelle déjà chiffrée dans Azure à partir d’un emplacement local, vous pouvez charger les clés de chiffrement dans Azure Key Vault et continuer à utiliser le chiffrement qui était utilisé localement pour cette machine virtuelle. Dans ce scénario, le chiffrement Azure Disk Encryption est activé.

Si vous utilisez un fichier VHD local non chiffré, vous pouvez le charger dans la galerie comme une image personnalisée et approvisionner une machine virtuelle à partir de cette image. Si vous le faites à l’aide de modèles Resource Manager, vous pouvez demander l’activation de la fonctionnalité Azure Disk Encryption au démarrage de la machine virtuelle.

Quand vous ajoutez un disque de données et le montez ensuite sur la machine virtuelle, vous pouvez activer Azure Disk Encryption sur ce disque. Azure Disk Encryption chiffrera d’abord ce disque de données localement, puis la couche de modèle de déploiement classique effectuera une écriture différée sur le stockage pour en chiffrer le contenu.

#### <a name="client-side-encryption"></a>chiffrement côté client
Le chiffrement côté client est la méthode la plus sûre pour chiffrer vos données, car il chiffre les données avant leur transit.  Toutefois, cette méthode vous oblige à ajouter du code dans vos applications qui utilisent le stockage, ce qui peut ne pas vous convenir. Dans ce cas, vous pouvez utiliser le protocole HTTPS pour sécuriser vos données en transit. Une fois que les données atteignent le stockage Azure, elles sont chiffrées par SSE.

Le chiffrement côté client vous permet de chiffrer des entités de table, des messages de file d’attente et des objets blob. 

Le chiffrement côté client est entièrement géré par l’application. C’est l’approche la plus sûre, mais elle vous oblige à effectuer des modifications par programmation dans votre application et à mettre en place des processus de gestion des clés. Choisissez cette méthode pour bénéficier d’une sécurité maximale pendant le transit des données et si vous souhaitez que vos données stockées soient chiffrées.

Le chiffrement côté client entraîne une plus grande charge pour le client. Vous devez donc tenir compte de ce paramètre dans vos plans d’extensibilité, en particulier si vous chiffrez et transférez une grande quantité de données.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE est géré par le Stockage Azure. SSE ne sécurise pas les données en transit, mais chiffre les données quand elles sont écrites dans Azure Storage. Le chiffrement du service de stockage n’affecte pas les performances de Stockage Azure.

Il est possible de chiffrer tous types de données du compte de stockage avec SSE (objets blob de blocs, objets blob d’ajout, objets blob de pages, données de table, données de file d’attente et fichiers).

Si vous utilisez une archive ou une bibliothèque de fichiers VHD comme base pour créer des machines virtuelles, créez un compte de stockage, puis chargez les fichiers VHD dans le nouveau compte. Ces fichiers VHD seront chiffrés par Azure Storage.

Si Azure Disk Encryption est activé pour les disques dans une machine virtuelle, toutes les données qui viennent d’être écrites sont chiffrées par SSE et par le chiffrement de disque Azure.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Utilisation de Storage Analytics pour surveiller le type d’autorisation
Pour chaque compte de stockage, vous pouvez activer Azure Storage Analytics pour effectuer la journalisation et stocker les données de métriques. Il s’agit d’un excellent outil à utiliser quand vous voulez vérifier les métriques de performances d’un compte de stockage, ou que vous devez résoudre des problèmes liés à un compte de stockage car vos performances ne sont pas satisfaisantes.

Les journaux d’analyse du stockage vous permettent de voir un autre élément de données : la méthode d’authentification utilisée par un utilisateur lors de l’accès au stockage. Par exemple, avec Blob Storage, vous pouvez voir s’il a utilisé une signature d’accès partagé ou les clés de compte de stockage, ou si l’objet blob qui a fait l’objet d’un accès est public.

Cela peut être utile si vous surveillez étroitement l’accès au stockage. Par exemple, dans Blob Storage, vous pouvez définir tous les conteneurs comme privés et implémenter l’utilisation d’un service SAP dans toutes vos applications. Vous pouvez alors vérifier régulièrement les journaux pour voir si vos objets blob font l’objet d’un accès à l’aide de clés de compte de stockage, ce qui peut indiquer une violation de la sécurité, ou si les objets blob sont publics alors qu’ils ne devraient pas l’être.

#### <a name="what-do-the-logs-look-like"></a>Comment se présentent les journaux ?
Une fois que vous avez activé les métriques de compte de stockage et la journalisation par le biais du Portail Azure, les données d’analyse commencent à s’accumuler rapidement. La journalisation et les métriques de chaque service sont distinctes : la journalisation est écrite uniquement en cas d’activité dans ce compte de stockage, tandis que les métriques sont consignées chaque minute, chaque heure ou chaque jour, en fonction de leur configuration.

Les journaux sont stockés dans des objets blob de blocs se trouvant dans un conteneur nommé $logs dans le compte de stockage. Ce conteneur est automatiquement créé quand Storage Analytics est activé. Une fois ce conteneur créé, vous ne pouvez pas le supprimer, même si vous pouvez en supprimer le contenu.

Sous le conteneur $logs se trouve un dossier correspondant à chaque service, puis des sous-dossiers pour l’année/le mois/le jour/l’heure. Sous les heures, les journaux sont numérotés. La structure des répertoires se présente ainsi :

![Affichage des fichiers journaux](./media/storage-security-guide/image1.png)

Chaque requête à Azure Storage est consignée. Voici un instantané d’un fichier journal montrant les premiers champs.

![Instantané d’un fichier journal](./media/storage-security-guide/image2.png)

Vous voyez que vous pouvez utiliser les journaux pour suivre des appels de tous types à un compte de stockage.

#### <a name="what-are-all-of-those-fields-for"></a>À quoi servent tous ces champs ?
Un article répertorié dans les ressources ci-dessous fournit la liste des nombreux champs présents dans les journaux et indique ce pour quoi ils sont utilisés. Voici la liste de ces champs, dans l’ordre :

![Instantané des champs d’un fichier journal](./media/storage-security-guide/image3.png)

Nous nous intéressons aux entrées pour GetBlob, et comment elles sont authentifiées. Nous devons donc rechercher les entrées avec operation-type « Get-Blob » et vérifier l’état de la requête (quatrième</sup> colonne) et le type d’autorisation (huitième</sup> colonne).

Par exemple, dans les premières lignes de la liste ci-dessus, l’état de la requête est « Success » et le type d’autorisation est « authenticated ». Cela signifie que la requête a été validée à l’aide de la clé du compte de stockage.

#### <a name="how-are-my-blobs-being-authenticated"></a>Comment mes objets blob sont-ils authentifiés ?
Trois cas nous intéressent.

1. L’objet blob est public et les utilisateurs y accèdent à l’aide d’une URL sans signature d’accès partagé. Dans ce cas, l’état de la requête est « AnonymousSuccess » et authorization-type est « anonymous ».

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. L’objet blob est privé et a été utilisé avec une signature d’accès partagé. Dans ce cas, l’état de la requête est « SASSuccess » et authorization-type est « sas ».

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. L’objet blob est privé et la clé de stockage a été utilisée pour y accéder. Dans ce cas, l’état de la requête est « **Success** » et authorization-type est « **authenticated** ».

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Vous pouvez utiliser Microsoft Message Analyzer pour afficher et analyser ces journaux. Il inclue des fonctions de recherche et de filtre. Par exemple, vous voulez peut-être rechercher les instances de GetBlob pour voir si l’utilisation répond à vos attentes, par exemple, pour vérifier que personne n’accède à votre compte de stockage de façon inappropriée.

#### <a name="resources"></a>Ressources
* [Analyse du stockage](../storage-analytics.md)

  Cet article est une vue d’ensemble de l’analyse du stockage et de la façon de l’activer.
* [Format du journal de l’analyse de stockage](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Cet article illustre le format du journal de l’analyse de stockage et présente les champs disponibles, notamment authentication-type, qui indique le type d’authentification utilisé pour la requête.
* [Surveiller un compte de stockage dans le portail Azure](../storage-monitor-storage-account.md)

  Cet article explique comment configurer la surveillance des métriques et la journalisation pour un compte de stockage.
* [Résolution des problèmes de bout en bout avec les métriques et la journalisation Azure, AzCopy et Message Analyzer](../storage-e2e-troubleshooting.md)

  Cet article traite du dépannage à l’aide de l’analyse du stockage et montre comment utiliser Microsoft Message Analyzer.
* [Microsoft Message Analyzer Operating Guide (Guide des opérations Microsoft Message Analyzer)](https://technet.microsoft.com/library/jj649776.aspx)

  Cet article de référence porte sur Microsoft Message Analyzer et inclut des liens vers un didacticiel, un démarrage rapide et un résumé des fonctionnalités.

## <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)
### <a name="cross-domain-access-of-resources"></a>Accès interdomaines des ressources
Quand un navigateur web s’exécutant dans un domaine effectue une requête HTTP pour une ressource à partir d’un autre domaine, on parle de requête HTTP cross-origin. Par exemple, une page HTML traitée à partir de contoso.com effectue une requête pour une image jpeg hébergée sur fabrikam.blob.core.windows.net. Pour des raisons de sécurité, les navigateurs limitent les requêtes HTTP cross-origin lancées à partir de scripts, comme JavaScript. Ainsi, quand du code JavaScript sur une page web contoso.com demande cet élément jpeg sur fabrikam.blob.core.windows.net, le navigateur n’autorise pas la requête.

Quel est le rapport avec Azure Storage ? En fait, si vous stockez des ressources statiques telles que les fichiers de données JSON ou XML dans Blob Storage à l’aide d’un compte de stockage nommé Fabrikam, le domaine des ressources est fabrikam.blob.core.windows.net et l’application web contoso.com ne peut pas y accéder à l’aide de JavaScript, car les domaines sont différents. Il est en de même si vous essayez d’appeler l’un des services de stockage Azure, tels que Stockage Table, qui retournent les données JSON qui doivent être traitées par le client JavaScript.

#### <a name="possible-solutions"></a>Solutions possibles
Une façon de résoudre ce problème consiste à attribuer un domaine personnalisé comme « storage.contoso.com » à fabrikam.blob.core.windows.net. Le souci est que vous pouvez attribuer ce domaine personnalisé uniquement à un compte de stockage. Que se passe-t-il si les ressources sont stockées dans plusieurs comptes de stockage ?

Une autre façon de résoudre ce problème consiste à faire en sorte que l’application web joue le rôle de proxy pour les appels de stockage. Cela signifie que si vous chargez un fichier sur Blob Storage, soit l’application web l’écrit localement puis le copie dans Blob Storage, soit elle le lit en intégralité en mémoire puis l’écrit dans Blob Storage. Vous pouvez également écrire une application web dédiée (par exemple, une API web) qui charge les fichiers localement et les écrit dans Blob Storage. Dans les deux cas, vous devez prendre en compte cette fonction pour déterminer les besoins en termes d’extensibilité.

#### <a name="how-can-cors-help"></a>En quoi CORS peut constituer une aide ?
Azure Storage vous permet d’activer le Partage des ressources cross-origin (CORS, Cross Origin Resource Sharing). Pour chaque compte de stockage, vous pouvez spécifier les domaines qui peuvent accéder aux ressources de ce compte de stockage. Par exemple, dans le cas indiqué ci-dessus, nous pouvons activer CORS sur le compte de stockage fabrikam.blob.core.windows.net et le configurer pour autoriser l’accès à contoso.com. L’application web contoso.com peut alors accéder directement aux ressources dans fabrikam.blob.core.windows.net.

Concernant CORS, il convient de noter qu’il autorise l’accès, mais qu’il ne fournit pas d’authentification, ce qui est nécessaire pour l’accès non public aux ressources de stockage. Cela signifie que vous pouvez accéder uniquement aux objets blob s’ils sont publics ou que vous devez inclure une signature d’accès partagé vous donnant l’autorisation appropriée. Les tables, les files d’attente et les fichiers n’ont pas d’accès public et requièrent une SAP.

Par défaut, CORS est désactivé sur tous les services. Vous pouvez activer CORS à l’aide de l’API REST ou de la bibliothèque cliente de stockage afin d’appeler l’une des méthodes pour définir les stratégies de service. Quand vous procédez ainsi, vous incluez une règle CORS, qui est au format XML. Voici un exemple d’une règle CORS qui a été définie à l’aide de l’opération Set Service Properties pour le service Blob pour un compte de stockage. Vous pouvez effectuer cette opération à l’aide de la bibliothèque cliente de stockage ou des API REST pour Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Voici ce que signifie chaque ligne :

* **AllowedOrigins** Cette ligne indique quels domaines sans correspondance peuvent demander et recevoir des données du service de stockage. Cela indique que contoso.com et fabrikam.com peuvent demander des données à partir de Blob Storage pour un compte de stockage spécifique. Vous pouvez également définir ici un caractère générique (\*) pour autoriser tous les domaines à effectuer des demandes d’accès.
* **AllowedMethods** Cette ligne répertorie les méthodes (verbes de requête HTTP) qui peuvent être utilisées lors de la requête. Dans cet exemple, seuls PUT et GET sont autorisés. Vous pouvez définir ici un caractère générique (\*) pour autoriser l’utilisation de toutes les méthodes.
* **AllowedHeaders** Cette ligne représente les en-têtes de requête que le domaine d’origine peut spécifier lors de la requête. Dans cet exemple, tous les en-têtes de métadonnées commençant par x-ms-meta-data, x-ms-meta-target et x-ms-meta-abc sont autorisés. Le caractère générique (\*) indique que tout en-tête commençant par le préfixe spécifié est autorisé.
* **ExposedHeaders** Cette ligne indique quels en-têtes de réponse doivent être exposés par le navigateur à l’émetteur de la requête. Dans cet exemple, tout en-tête commençant par « x-ms - meta-» est exposé.
* **MaxAgeInSeconds** Cette ligne indique la durée maximale pendant laquelle un navigateur met en cache la requête OPTIONS préliminaire. (Pour plus d’informations sur la requête préliminaire, consultez le premier article ci-dessous).

#### <a name="resources"></a>Ressources
Pour plus d’informations sur CORS et sur la façon de l’activer, consultez les ressources suivantes.

* [Prise en charge du service Partage des ressources cross-origine (CORS) pour les services Azure Storage sur Azure.com](../storage-cors-support.md)

  Cet article fournit une vue d’ensemble de CORS et de la façon de définir les règles pour les différents services de stockage.
* [Prise en charge du service Partage des ressources cross-origine (CORS) pour les services Azure Storage sur MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Il s’agit de la documentation de référence pour la prise en charge de CORS pour les services Azure Storage. Elle propose des liens vers des articles concernant chaque service de stockage, et propose un exemple et une description de chaque élément du fichier CORS.
* [Microsoft Azure Storage: Introducing CORS (Microsoft Azure Storage : Présentation de CORS)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Il s’agit d’un lien vers l’article de blog initial qui présente CORS et montre comment l’utiliser.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Questions fréquemment posées (FAQ) sur la sécurité Azure Storage
1. **Comment puis-je vérifier l’intégrité des objets blob que je transfère vers ou à partir du stockage Azure si je ne peux pas utiliser le protocole HTTPS ?**

   Si, pour une raison quelconque, vous devez utiliser le protocole HTTP au lieu de HTTPS et que vous travaillez avec des objets blob de blocs, vous pouvez utiliser la vérification MD5 pour vérifier l’intégrité des objets blob transférés. Ceci contribuera à la protection contre les erreurs au niveau du réseau/transport, mais pas nécessairement contre les attaques intermédiaires.

   Si vous pouvez utiliser le protocole HTTPS, qui fournit une sécurité au niveau du transport, alors l’utilisation de la vérification MD5 est redondant et inutile.

   Pour plus d’informations, consultez [Présentation d’Azure Blob MD5](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Qu’en est-il de la conformité aux normes FIPS pour l’État fédéral américain ?**

   Les normes FIPS (Federal Information Processing Standard) des États-Unis définissent les algorithmes de chiffrement qui sont approuvés pour une utilisation sur les systèmes informatiques de l’État fédéral américain dans le but de protéger les données sensibles. L’activation du mode FIPS sur un serveur ou un bureau Windows indique au système d’exploitation que seuls les algorithmes de chiffrement conformes aux normes FIPS doivent être utilisés. Si une application utilise des algorithmes non conformes, les applications s’arrêtent. Avec .NET Framework versions 4.5.2 ou ultérieures, l’application bascule automatiquement les algorithmes de chiffrement pour utiliser des algorithmes conformes aux normes FIPS quand l’ordinateur est en mode FIPS.

   Microsoft laisse à chaque client le soin de décider si le mode FIPS doit être activé. Nous pensons qu’il n’existe aucune raison valable pour les clients qui ne sont pas soumis aux réglementations gouvernementales d’activer le mode FIPS par défaut.

   **Ressources**

* [Why We’re Not Recommending “FIPS Mode” Anymore (Pourquoi nous ne recommandons plus le « mode FIPS »)](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Cet article de blog donne une vue d’ensemble des normes FIPS et explique pourquoi le mode FIPS n’est plus activé par défaut.
* [FIPS 140 Validation (Validation de la norme FIPS 140)](https://technet.microsoft.com/library/cc750357.aspx)

  Cet article fournit des informations sur la façon dont les produits et les modules de chiffrement Microsoft sont conformes aux normes FIPS pour l’État fédéral américain.
* [Effets des paramètres de sécurité « Chiffrement système : utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature » dans Windows XP et les versions ultérieures de Windows](https://support.microsoft.com/kb/811833)

  Cet article traite de l’utilisation du mode FIPS sur des ordinateurs Windows anciens.
