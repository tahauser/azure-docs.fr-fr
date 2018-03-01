---
title: Utilisation de certificats avec Enterprise Integration Pack | Microsoft Docs
description: "Découvrez comment utiliser les certificats avec Enterprise Integration Pack | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0357e67a8920a57b2ab8b79ebd8dd3a64d888478
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>En savoir plus sur les certificats et Enterprise Integration Pack
## <a name="overview"></a>Vue d'ensemble
Enterprise Integration utilise des certificats pour sécuriser les communications B2B. Vous pouvez utiliser deux types de certificats dans vos applications Enterprise Integration :

* Des certificats publics, qui doivent être achetés auprès d’une autorité de certification (CA).
* Des certificats privés, que vous pouvez créer vous-même. Ces certificats sont parfois appelés « certificats auto-signés ».

## <a name="what-are-certificates"></a>Que sont les certificats ?
Les certificats sont des documents numériques qui vérifient l’identité des participants dans des communications électroniques et qui sécurisent ces communications électroniques.

## <a name="why-use-certificates"></a>Pourquoi utiliser des certificats ?
Parfois, les communications B2B doivent rester confidentielles. Enterprise Integration utilise des certificats pour sécuriser ces communications de deux manières :

* En chiffrant le contenu des messages
* En signant numériquement les messages  

## <a name="upload-a-public-certificate"></a>Téléchargement d’un certificat public

Pour utiliser un *certificat public* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration.  

Après avoir téléchargé un certificat, vous pouvez l'utiliser pour sécuriser vos messages B2B lorsque vous définissez leurs propriétés dans les [contrats](logic-apps-enterprise-integration-agreements.md) que vous créez.  

Voici les étapes détaillées pour télécharger vos certificats publics sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :

1. Sélectionnez **Tous les services** et entrez **intégration** dans la zone de recherche de filtre. Sélectionnez **Comptes d’intégration** dans la liste des résultats.     
![Sélectionnez Parcourir](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Sélectionnez le compte d’intégration auquel vous ajouterez le certificat.  
![Sélectionnez le compte d’intégration auquel vous ajouterez le certificat](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Sélectionnez la mosaïque **Certificats** .  
![Sélectionnez la mosaïque Certificats](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. Dans le panneau **Certificats** qui s’affiche, sélectionnez le bouton **Ajouter**.   
![Cliquez sur le bouton Ajouter](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Entrez le **nom** de votre certificat, puis sélectionnez le type de certificat **public** dans la liste déroulante.  
6. Sélectionnez l’icône de dossier à droite de la zone de texte **Certificat**. Lorsque le sélecteur de fichiers apparaît, recherchez puis sélectionnez le fichier de certificat que vous souhaitez télécharger sur votre compte d’intégration.
7. Sélectionnez le certificat, puis choisissez **OK** dans le sélecteur de fichiers. Cette opération valide et télécharge le certificat sur votre compte d’intégration.
8. Enfin, de retour dans le panneau **Ajouter un certificat**, sélectionnez le bouton **OK**.  
![Cliquez sur le bouton OK](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Sélectionnez la mosaïque **Certificats** . Vous devez voir le certificat qui vient d’être ajouté.  
![Consultez le nouveau certificat](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Téléchargement d’un certificat privé

Pour utiliser un *certificat privé* dans vos applications logiques avec des capacités B2B, vous pouvez télécharger un certificat privé dans votre compte d’intégration en effectuant les étapes suivantes.

1. [Chargez votre clé privée dans Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur Key Vault") et indiquez un **nom de clé**. 
   
   > [!TIP]
   > Vous devez autoriser Logic Apps à effectuer des opérations sur Key Vault. Vous pouvez autoriser l’accès au principal du service Logic Apps à l’aide de cette commande PowerShell : `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Après avoir réalisé l’étape précédente, ajoutez un certificat privé au compte d’intégration.

Voici les étapes détaillées pour télécharger vos certificats privés sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :  
 
1. Sélectionnez le compte d’intégration auquel vous souhaitez ajouter le certificat, puis sélectionnez la mosaïque **Certificats**.  
![Sélectionnez la mosaïque Certificats](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. Dans le panneau **Certificats** qui s’affiche, sélectionnez le bouton **Ajouter**.   
![Cliquez sur le bouton Ajouter](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Entrez le **nom** de votre certificat, puis sélectionnez le type de certificat **privé** dans la liste déroulante.   
4. Sélectionnez l’icône de dossier à droite de la zone de texte **Certificat**. Lorsque le sélecteur de fichiers apparaît, recherchez le certificat public que vous souhaitez charger sur votre compte d’intégration.   
   
   > [!Note]
   > Lors de l’ajout d’un certificat privé, il est important d’ajouter le certificat public correspondant à afficher dans les paramètres d’envoi et de réception du [contrat AS2](logic-apps-enterprise-integration-as2.md) pour la signature et le chiffrement des messages.
   > 
   >   

5. Sélectionnez les valeurs appropriées dans les listes déroulantes **Groupe de ressources**, **Key Vault** et **Nom de la clé**, puis cliquez sur le bouton **OK**.  
![Ajouter le certificat](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Sélectionnez la mosaïque **Certificats** . Vous devez voir le certificat qui vient d’être ajouté.
![Consultez le nouveau certificat](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Créer un contrat B2B](logic-apps-enterprise-integration-agreements.md)  
* [En savoir plus sur Azure Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés")  

