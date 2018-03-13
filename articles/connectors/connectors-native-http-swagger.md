---
title: Appeler des points de terminaison REST avec le connecteur HTTP + Swagger pour Azure Logic Apps | Microsoft Docs
description: "Se connecter aux points de terminaison REST à partir d’applications logiques via Swagger avec le connecteur HTTP + Swagger"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 0487dbedddee684c75420bd66effe2c963a18624
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-http--swagger-action"></a>Prise en main de l’action HTTP + Swagger

Vous pouvez créer un connecteur de première classe pour n’importe quel point de terminaison REST avec un [document Swagger](https://swagger.io) lorsque vous utilisez l’action HTTP + Swagger dans votre workflow d’application logique. Vous pouvez également étendre une application logique pour appeler n’importe quel point de terminaison REST avec une expérience de conception Logic App de première classe.

Pour savoir comment créer des applications logiques avec des connecteurs, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utiliser HTTP + Swagger comme un déclencheur ou d’une action

Le déclencheur et l’action HTTP + Swagger fonctionnent comme une [action HTTP](connectors-native-http.md) mais en fournissant une meilleure expérience dans le concepteur Logic App en exposant la structure d’API et les sorties à partir des [métadonnées Swagger](https://swagger.io). Vous pouvez également utiliser le connecteur HTTP + Swagger en tant que déclencheur. Si vous souhaitez implémenter un déclencheur d’interrogation, suivez le modèle d’interrogation décrit dans [Créer des API personnalisées pour appeler d’autres APO, services et systèmes à partir de Logic Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

En savoir plus sur les [déclencheurs et actions de l’application logique](connectors-overview.md).

Voici un exemple d’utilisation de l’opération HTTP + Swagger en tant qu’action dans un flux de travail dans une application logique.

1. Sélectionnez le bouton **Nouvelle étape** .
2. Sélectionnez **Ajouter une action**.
3. Dans la zone de recherche Action , tapez **Swagger** pour répertorier l’action HTTP + Swagger.
   
    ![Sélectionnez une action HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)
4. Entrez l’URL d'un document Swagger :
   
   * Pour fonctionner à partir du concepteur d'applications logiques, l’URL doit être un point de terminaison HTTPS et avoir CORS activé.
   * Si le document Swagger ne répond pas à ce critère, vous pouvez utiliser [Azure Storage avec CORS activé](#hosting-swagger-from-storage) pour stocker le document.
5. Cliquez sur **Suivant** pour lire et effectuer le rendu du document Swagger.
6. Ajoutez tout paramètre nécessaire à l’appel HTTP.
   
    ![Exécuter l’action HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Pour enregistrer et publier votre application logique, cliquez sur **Enregistrer** sur la barre d’outils du concepteur.

### <a name="host-swagger-from-azure-storage"></a>Héberger un document Swagger à partir d'Azure Storage
Vous pouvez faire référence à un document Swagger qui n’est pas hébergé ou qui ne répond pas aux exigences de sécurité et cross-origin pour le concepteur. Pour résoudre ce problème, vous pouvez stocker le document swagger dans Azure Storage et activer CORS pour référencer le document.  

Voici les étapes pour créer, configurer et stocker des documents swagger dans Azure Storage :

1. [Créez un compte de stockage Azure avec un stockage d'objets blob Azure](../storage/common/storage-create-storage-account.md). Pour cela, définissez les autorisations sur **Accès public**.

2. Activez CORS sur l’objet blob. 

   Pour configurer ce paramètre automatiquement, vous pouvez utiliser [ce script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Téléchargez le fichier Swagger dans l’objet blob. 

   Vous pouvez le faire par le biais du [portail Azure](https://portal.azure.com) ou à partir d’un outil tel que [Azure Storage Explorer](http://storageexplorer.com/).

4. Référencez un lien HTTPS vers le document dans le stockage d'objets blob Azure. 

   La liaison utilise ce format :

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Détails techniques
Vous trouverez ci-dessous les détails des déclencheurs et des actions que ce connecteur HTTP + Swagger prend en charge.

## <a name="http--swagger-triggers"></a>Déclencheurs HTTP + Swagger
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs.](connectors-overview.md) Le connecteur HTTP + Swagger a un déclencheur.

| Déclencheur | DESCRIPTION |
| --- | --- |
| HTTP + Swagger |Exécuter un appel HTTP et obtenir le contenu de la réponse |

## <a name="http--swagger-actions"></a>Actions HTTP + Swagger
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions.](connectors-overview.md) Le connecteur HTTP + Swagger a une action possible.

| Action | DESCRIPTION |
| --- | --- |
| HTTP + Swagger |Exécuter un appel HTTP et obtenir le contenu de la réponse |

### <a name="action-details"></a>Détails de l’action
Le connecteur HTTP + Swagger est créé avec une action possible. Vous trouverez ci-dessous plus d’informations sur chacune des actions, leurs champs obligatoires et facultatifs, et les détails des résultats correspondants associés à leur utilisation.

#### <a name="http--swagger"></a>HTTP + Swagger
Faites une demande HTTP sortante avec assistance des métadonnées Swagger.
Un astérisque (*) signifie un champ obligatoire.

| Nom complet | Nom de la propriété | DESCRIPTION |
| --- | --- | --- |
| Method (Méthode)* |method |Verbe HTTP à utiliser. |
| URI* |URI |URI de la requête HTTP. |
| headers |headers |Un objet JSON d’en-têtes HTTP à inclure. |
| body |body |Le texte de la requête HTTP. |
| Authentification |Authentification |Authentification à utiliser pour la requête. Pour plus d’informations, consultez la page [Connecteur HTTP](connectors-native-http.md#authentication). |

**Détails des résultats**

Réponse HTTP

| Nom de la propriété | Type de données | DESCRIPTION |
| --- | --- | --- |
| headers |objet |En-têtes de réponse |
| body |objet |Objet Réponse |
| Code d’état |int |Code d'état HTTP |

### <a name="http-responses"></a>Réponses HTTP
Lorsque vous exécutez des appels de diverses actions, vous pouvez obtenir certaines réponses. La table ci-dessous indique les réponses correspondantes et leurs descriptions.

| NOM | DESCRIPTION |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |

- - -
## <a name="next-steps"></a>Étapes suivantes

* [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Rechercher d’autres connecteurs](apis-list.md)