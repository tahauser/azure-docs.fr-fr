---
title: "Démarrage rapide pour l'API Graph Azure AD | Microsoft Docs"
description: "L’API Graph Azure Active Directory fournit un accès par programme à Azure AD via les points de terminaison de l’API REST OData. Les applications peuvent utiliser l'API Graph pour effectuer des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les données et objets d'annuaire."
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mtillman
editor: 
tags: 
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 2dc65598dd9a73e24a2f961027554bb10b961397
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Démarrage rapide pour l'API Graph Azure AD
L’API Graph Azure Active Directory (AD) donne accès par programme à Azure AD via les points de terminaison de l’API REST OData. Les applications peuvent utiliser l'API Graph pour effectuer des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les données et objets d'annuaire. Par exemple, vous pouvez utiliser l’API Graph pour créer un utilisateur, afficher ou mettre à jour ses propriétés, modifier son mot de passe, vérifier que l’appartenance à un groupe offre un accès en fonction du rôle, et désactiver ou supprimer l’utilisateur. Pour plus d’informations sur les fonctionnalités de l’API Graph et les scénarios d’application, consultez [Azure AD Graph API reference (Référence sur l’API Graph Azure AD)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) et [Prerequisites for Using the Graph API in an App (Conditions préalables à l’utilisation de l’API Graph dans une application)](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Nous vous recommandons fortement d’utiliser [Microsoft Graph](https://developer.microsoft.com/graph) au lieu de l’API Azure AD Graph pour accéder aux ressources Azure Active Directory. Nos efforts de développement sont maintenant axés sur Microsoft Graph et aucune autre amélioration n’est prévue pour l’API Azure AD Graph. Il existe un nombre très limité de scénarios pour lesquels l’API Azure AD Graph peut être appropriée. Pour plus d’informations, consultez le billet de blog [Microsoft Graph ou Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) dans le centre de développement Office.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Construction d'une URL d’API Graph
Dans l'API Graph, pour accéder aux données et objets d'annuaire (en d'autres termes, les ressources ou les entités) sur lesquels vous souhaitez effectuer des opérations CRUD, vous pouvez utiliser des URL basées sur le protocole OData (Open Data). Les URL utilisées dans l'API Graph se composent de quatre parties principales : racine de service, identificateur de locataire, chemin d'accès de ressource et options de chaîne de requête : `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Prenons l'exemple de l'URL suivante : `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Racine de service** : dans l’API Graph Azure AD, la racine du service est toujours https://graph.windows.net.
* **Identificateur de locataire** : cette section peut être un nom de domaine (inscrit) vérifié, dans l’exemple précédent, contoso.com. Cela peut également être un ID d’objet locataire ou l’alias « myorganization » ou « me ». Pour plus d'informations, consultez [Traitement des entités et opérations dans l'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)(en anglais).
* **Chemin d’accès de ressource** : cette section d’une URL identifie la ressource avec laquelle interagir (utilisateurs, groupes, utilisateur ou groupe spécifique, etc.) Dans l’exemple ci-dessus, il s’agit de la ressource de niveau supérieur « groups » à laquelle ce jeu de ressources est adressé. Vous pouvez également adresser une entité spécifique, par exemple « users/{objectId} » ou « users/userPrincipalName ».
* **Paramètres de requête** : un point d’interrogation (?) sépare la section du chemin de ressource de la section des paramètres de requête. Le paramètre de requête « api-version » est requis sur toutes les demandes dans l'API Graph. L’API Graph prend également en charge les options de requête OData suivantes : **$filter**, **$orderby**, **$expand**, **$top** et **$format**. Les options de requête suivantes ne sont pas prises en charge actuellement : **$count**, **$inlinecount** et **$skip**. Pour plus d'informations, consultez [Options de requêtes, de filtres et de pagination prises en charge dans l'API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versions d'API Graph
Vous spécifiez la version pour une demande d'API Graph dans le paramètre de requête « api-version ». Pour les versions 1.5 et ultérieures, utilisez une valeur numérique de version ; api-version=1.6. Pour les versions précédentes, utilisez une chaîne de date au format AAAA-MM-JJ ; par exemple, api-version=2013-11-08. Pour les fonctionnalités en version préliminaire, utilisez la chaîne « beta » ; par exemple, api-version=beta. Pour plus d'informations sur les différences entre les versions de l'API Graph, consultez [Contrôle de version de l'API graphique Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Métadonnées d'API Graph
Pour retourner le fichier de métadonnées de l’API Graph, ajoutez le segment « $metadata » après l’identificateur de locataire dans l’URL. Par exemple, l’URL suivante retourne des métadonnées pour une société de démonstration : `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Vous pouvez entrer cette URL dans la barre d'adresses d'un navigateur Web pour afficher les métadonnées. Le document de métadonnées CSDL renvoyé décrit les entités et les types complexes, leurs propriétés et les fonctions et actions exposées par la version de l'API Graph demandée. L'omission du paramètre api-version a pour effet de renvoyer des métadonnées pour la version la plus récente.

## <a name="common-queries"></a>Requêtes courantes
[Requêtes courantes de l'API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) répertorie des requêtes courantes qui peuvent être utilisées avec Azure AD Graph, notamment des requêtes permettant d’accéder aux ressources de niveau supérieur dans votre annuaire, et d’y effectuer des opérations.

Par exemple, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` renvoie des informations sur la société pour l'annuaire contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` répertorie tous les objets utilisateur dans l'annuaire contoso.com.

## <a name="using-the-graph-explorer"></a>Utilisation de l'explorateur graphique
Vous pouvez utiliser l'explorateur graphique pour l'API Graph Azure AD pour interroger les données d'annuaire lorsque vous générez votre application.

Voici la sortie que vous pouvez obtenir si vous accédez à l’explorateur graphique, que vous vous connectez, puis que vous entrez `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` pour afficher tous les utilisateurs figurant dans l’annuaire de l’utilisateur connecté :

![explorateur api graph Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Charger l’explorateur graphique** : pour charger l’outil, accédez à [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Cliquez sur **Connexion** et connectez-vous avec vos informations d’identification de compte Azure AD pour exécuter l’explorateur graphique sur votre locataire. Si vous exécutez l'explorateur graphique sur votre propre client, vous ou votre administrateur devrez donner votre consentement lors de la connexion. Si vous avez un abonnement à Office 365, vous disposez automatiquement d’un client Azure AD. Les informations d'identification que vous utilisez pour vous connecter à Office 365 sont, en fait, des comptes Azure AD. Vous pouvez les utiliser avec l'explorateur graphique.

**Exécuter une requête** : pour exécuter une requête, entrez votre requête dans la zone de texte de requête, puis cliquez sur **GET** ou sur la touche **Entrée**. Les résultats s’affichent dans la zone de réponse. Par exemple, `https://graph.windows.net/myorganization/groups?api-version=1.6` affiche la liste de tous les objets de groupe figurant dans l’annuaire de l’utilisateur connecté.

Notez les fonctionnalités et les limitations de l'Explorateur de graphique suivantes :

* Fonctionnalité de saisie semi-automatique sur des jeux de ressources. Pour voir cette fonctionnalité en action, cliquez sur la zone de texte de requête (où figure l’URL de la société). Vous pouvez sélectionner un jeu de ressources dans la liste déroulante.
* Prend en charge les alias d’adressage « me » et « myorganization ». Par exemple, vous pouvez utiliser `https://graph.windows.net/me?api-version=1.6` pour renvoyer l'objet utilisateur de l'utilisateur connecté, ou `https://graph.windows.net/myorganization/users?api-version=1.6` pour renvoyer tous les utilisateurs dans l'annuaire actif.
* Section d’en-tête de réponse. Cette section peut vous aider à résoudre les problèmes qui se produisent pendant l’exécution des requêtes.
* Visionneuse JSON pour la réponse, avec des capacités de développement et de réduction.
* Aucune prise en charge de l'affichage d'une photo miniature.

## <a name="using-fiddler-to-write-to-the-directory"></a>Utilisation de Fiddler pour écrire dans l’annuaire
Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser le débogueur web Fiddler pour les opérations « d’écriture » dans votre annuaire Azure AD. Pour plus d'informations et pour installer Fiddler, consultez [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Dans l'exemple ci-dessous, vous utilisez le débogueur web Fiddler pour créer un nouveau groupe de sécurité « MyTestGroup » dans votre annuaire Azure AD.

**Obtenir un jeton d'accès**: pour accéder à Azure AD Graph, les clients doivent tout d'abord s'authentifier auprès d'Azure AD. Pour plus d’informations, consultez la page [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

**Composer et exécuter une requête**: procédez comme suit :

1. Ouvrez le débogueur Web Fiddler et basculez vers l’onglet **Composer** .
2. Dans la mesure où vous souhaitez créer un groupe de sécurité, sélectionnez **Publier** comme méthode HTTP dans le menu déroulant. Pour plus d’informations sur les opérations et les autorisations relatives à un objet de groupe, consultez [Groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dans la [référence de l’API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Dans le champ en regard de **Publier**, entrez ce qui suit comme URL de demande : `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.
   
   > [!NOTE]
   > Vous devez remplacer mytenantdomain par le nom de domaine de votre propre annuaire Azure AD.
   > 
   > 
4. Dans le champ situé sous le menu déroulant Publier, entrez ce qui suit :
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Remplacez &lt;votre jeton d’accès&gt; par le jeton d’accès de votre répertoire Azure AD.
   > 
   > 
5. Dans le champ **Corps de la demande** , entrez ce qui suit :
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Pour plus d'informations sur la création de groupes, consultez [Création d’un groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Pour plus d'informations sur les entités et les types Azure AD exposés par Graph, ainsi que des informations sur les opérations exécutables pouvant y être exécutées avec Graph, consultez [Référence de l'API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’ [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* En savoir plus sur les [Étendues d’autorisation de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

