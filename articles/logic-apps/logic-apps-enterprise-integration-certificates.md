---
title: "Sécurisation de messages B2B à l’aide de certificats dans Azure Logic Apps | Microsoft Docs"
description: "Ajouter des certificats pour sécuriser les messages B2B avec le pack Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 708bdcddede71186c48ae7d4034cc9df0bd61391
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="secure-b2b-messages-with-certificates"></a>Sécuriser les messages B2B à l’aide de certificats

Vous avez parfois besoin d’établir une communication B2B confidentielle. Afin de sécuriser la communication B2B pour les applications d’intégration de votre entreprise, les applications logiques en particulier, vous pouvez ajouter des certificats à votre compte d’intégration. Les certificats sont des documents numériques qui vérifient l’identité des participants dans des communications électroniques.
Ils vous aident à sécuriser la communication de plusieurs façons :

* Chiffrement du contenu du message
* Signature numérique des messages  

Vous pouvez utiliser ces certificats dans vos applications Enterprise Integration :

* Des certificats publics, qui doivent être achetés auprès d’une autorité de certification (CA).
* Des certificats privés, que vous pouvez créer vous-même. Ces certificats sont parfois appelés « certificats auto-signés ».

## <a name="upload-a-public-certificate"></a>Téléchargement d’un certificat public

Pour utiliser un *certificat public* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration. Une fois que vous avez défini les propriétés dans les [contrats](logic-apps-enterprise-integration-agreements.md) que vous créez, le certificat est disponible pour vous aider à sécuriser vos messages B2B.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Entrez « intégration » dans la zone de recherche, puis sélectionnez **Comptes d’intégration**.

   ![Chercher votre compte d’intégration](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter le certificat.

   ![Sélectionnez le compte d’intégration auquel vous ajouterez le certificat](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Choisissez la mosaïque **Certificats**.  

   ![Choisir « Certificats »](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. Sous **Certificats**, choisissez **Ajouter**.

   ![Choisir « Ajouter »](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. Sous **Ajouter un certificat**, fournissez les détails de votre certificat.
   
   1. Entrez le **nom** de votre certificat. Pour le type de certificat, sélectionnez **Public**.

   2. Sélectionnez l’icône en forme de dossier à droite de la zone de texte **Certificat**. 
   Cherchez et sélectionnez le fichier de certificat à charger. 
   Une fois que vous avez terminé, sélectionnez **OK**.

      ![Charger un certificat public](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure charge votre certificat après avoir validé votre sélection.

   ![Consulter le nouveau certificat](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Téléchargement d’un certificat privé

Pour utiliser un *certificat privé* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration. Vous devez également disposer d’une clé privée que vous ajoutez d’abord à [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Une fois que vous avez défini les propriétés dans les [contrats](logic-apps-enterprise-integration-agreements.md) que vous créez, le certificat est disponible pour vous aider à sécuriser vos messages B2B.

> [!NOTE]
> Pour les certificats privés, n’oubliez pas d’ajouter un certificat public correspondant qui apparaitra dans les paramètres Envoyer et Recevoir du [contrat AS2](logic-apps-enterprise-integration-as2.md) pour la signature et le chiffrement des messages.

1. [Ajoutez votre clé privée dans Azure Key Vault](../key-vault/key-vault-get-started.md#add) et indiquez un **nom de clé**.
   
2. Vous devez autoriser Azure Logic Apps à effectuer des opérations sur Azure Key Vault. Pour accorder l’accès au principal du service Logic Apps, utilisez la commande PowerShell, [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), par exemple :

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Connectez-vous au [Portail Azure](https://portal.azure.com).

4. Dans le menu principal Azure, sélectionnez **Tous les services**. Entrez « intégration » dans la zone de recherche, puis sélectionnez **Comptes d’intégration**.

   ![Chercher votre compte d’intégration](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter le certificat.

6. Choisissez la mosaïque **Certificats**.  

   ![Choisir la mosaïque Certificats](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. Sous **Certificats**, choisissez **Ajouter**.   

   ![Choisir le bouton Ajouter](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. Sous **Ajouter un certificat**, fournissez les détails de votre certificat.
   
   1. Entrez le **nom** de votre certificat. Pour le type de certificat, sélectionnez **Privé**.

   2. Sélectionnez l’icône en forme de dossier à droite de la zone de texte **Certificat**. 
   Cherchez et sélectionnez le fichier de certificat à charger. 
   En outre, choisissez le **groupe de ressources**, le **coffre de clés** et le **nom de la clé**. 
   Une fois que vous avez terminé, sélectionnez **OK**.

      ![Ajouter un certificat](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure charge votre certificat après avoir validé votre sélection.

   ![Consulter le nouveau certificat](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un contrat B2B](logic-apps-enterprise-integration-agreements.md)
