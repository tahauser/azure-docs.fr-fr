---
title: "Chaîne de connexion MongoDB pour un compte Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment connecter votre application MongoDB à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion MongoDB."
keywords: "chaîne de connexion mongodb"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: anhoh
ms.openlocfilehash: 41c0b360b54f9a9dcd2458ce5bdc05911c4f73a5
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connecter une application MongoDB à Azure Cosmos DB
Découvrez comment connecter votre application MongoDB à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion MongoDB. Vous pouvez ensuite utiliser une base de données Azure Cosmos DB en tant que magasin de données pour votre application MongoDB. 

Ce didacticiel fournit deux façons de récupérer les informations de la chaîne de connexion :

- [Méthode du démarrage rapide](#QuickstartConnection), à utiliser avec les pilotes .NET, Node.js, MongoDB Shell, Java et Python.
- [Méthode de la chaîne de connexion personnalisée](#GetCustomConnection), à utiliser avec d’autres pilotes.

## <a name="prerequisites"></a>configuration requise

- Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant. 
- Un compte Azure Cosmos DB. Pour des instructions, voir [Développer une application web API MongoDB avec .NET et le portail Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obtenir la chaîne de connexion MongoDB à l’aide du démarrage rapide
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez l’API pour le compte MongoDB. 
3. Dans le volet gauche du panneau Compte, cliquez sur **Démarrage rapide**. 
4. Choisissez votre plateforme (**.NET**, **Node.js**, **MongoDB Shell**, **Java** ou **Python**). Si votre pilote ou outil n’est pas répertorié, ne vous inquiétez pas, nous développons en permanence de nouveaux extraits de code de connexion. Entrez ci-dessous un commentaire sur ce que vous voudriez voir. Pour savoir comment créer votre propre connexion, lisez [Obtenir les informations de chaîne de connexion du compte](#GetCustomConnection).
5. Copiez et collez l’extrait de code dans votre application MongoDB.

    ![Panneau Démarrage rapide](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtenir la chaîne de connexion MongoDB à personnaliser
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez l’API pour le compte MongoDB. 
3. Dans le volet gauche du panneau Compte, cliquez sur **Chaîne de connexion**. 
4. Le panneau **Chaîne de connexion** s’ouvre. Il affiche toutes les informations nécessaires pour la connexion au compte à l’aide d’un pilote pour MongoDB, dont une chaîne de connexion prédéfinie.

    ![Panneau Chaîne de connexion](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Exigences relatives à la chaîne de connexion
> [!Important]
> Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via *SSL*. 
>
>

Azure Cosmos DB prend en charge le format d’URI de chaîne de connexion MongoDB standard, sous réserve que quelques exigences spécifiques soient satisfaites : les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via SSL. Le format de chaîne de connexion est donc :

    mongodb://username:password@host:port/[database]?ssl=true

Les valeurs de cette chaîne sont disponibles dans le panneau **Chaîne de connexion** illustré précédemment :

* Nom d’utilisateur (obligatoire) : nom du compte Azure Cosmos DB.
* Mot de passe (obligatoire) : mot de passe du compte Azure Cosmos DB.
* Hôte (obligatoire) : nom de domaine complet (FQDN) du compte Azure Cosmos DB.
* Port (obligatoire) : 10255.
* Base de données (facultatif) : base de données utilisée par la connexion. Si aucune base de données n’est fournie, la base de données par défaut est « test ».
* ssl = true (obligatoire)

Par exemple, observez le compte figurant dans le panneau **Chaîne de connexion**. Exemple de chaîne de connexion valide :

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser Studio 3T](mongodb-mongochef.md) avec une API Azure Cosmos DB pour le compte MongoDB.
* Découvrez l’API Azure Cosmos DB pour le compte MongoDB en consultant les [exemples](mongodb-samples.md).
