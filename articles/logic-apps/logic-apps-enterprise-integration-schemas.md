---
title: "Schémas de validation XML - Azure Logic Apps | Microsoft Docs"
description: "Valider des documents XML avec des schémas pour Azure Logic Apps et Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: db2d9313e443ebc9dd32fcb905b0ae62219e4bbf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Valider des documents XML avec des schémas pour Azure Logic Apps et Enterprise Integration Pack

Les schémas confirment que les documents XML que vous recevez sont valides et contiennent les données attendues dans un format prédéfini. Les schémas aident également à valider les messages échangés dans un scénario d’entreprise à entreprise (B2B).

## <a name="add-a-schema"></a>Ajout d’un schéma

1. Dans le portail Azure, sélectionnez **Tous les services**.

    ![Portail Azure, « Tous les services »](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats.

    ![Zone de recherche de filtre](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Sélectionnez le **compte d’intégration** auquel vous souhaitez ajouter le schéma.

    ![Liste des comptes d’intégration](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Choisissez la mosaïque **Schémas**.

    ![Exemple de compte d’intégration, « Schémas »](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Ajout d’un fichier de schéma d’une taille inférieure à 2 Mo

1. Dans le panneau **Schémas** qui s’affiche (après les étapes précédentes), sélectionnez **Ajouter**.

    ![Panneau Schémas, « Ajouter »](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Saisissez un nom pour votre schéma. Téléchargez le fichier de schéma en sélectionnant l’icône de dossier à côté de la zone **Schéma**. Une fois le processus de téléchargement terminé, sélectionnez **OK**.

    ![Capture d’écran d’« Ajouter un schéma » avec « Fichier de petite taille » mis en surbrillance](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Ajout d’un fichier de schéma d’une taille supérieure à 2 Mo (jusqu’à un maximum de 8 Mo)

Ces étapes varient selon le niveau d’accès du conteneur d’objets blob : **Public** ou **No anonymous access** (Aucun accès anonyme).

**Pour déterminer ce niveau d’accès**

1.  Ouvrez **l’Explorateur de stockage Azure**. 

2.  Sous **Conteneurs d’objets Blob**, sélectionnez le conteneur d’objets blob de votre choix. 

3.  Sélectionnez **Sécurité**, **Niveau d’accès**.

Si le niveau d’accès de sécurité d’objet blob est **Public**, procédez comme suit.

![Explorateur de stockage Azure, avec « Conteneurs d’objets Blob », « Sécurité » et « Public » mis en surbrillance](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Téléchargez le schéma vers votre compte de stockage, puis copiez l’URI.

    ![Compte de stockage avec l’URI mis en surbrillance](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. Sélectionnez **Fichier volumineux** dans **Ajouter un schéma** et indiquez l’URI dans la zone de texte **URI du contenu**.

    ![Schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Si le niveau de sécurité de l’accès aux objets blob est défini sur **Pas d’accès anonyme**.

![Explorateur de stockage Azure, avec « Conteneurs d’objets Blob », « Sécurité » et « No anonymous access » (Aucun accès anonyme) mis en surbrillance](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Téléchargez le schéma vers votre compte de stockage.

    ![Compte de stockage](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Générez un URI de signature d’accès partagé pour le schéma.

    ![Compte de stockage, avec l’onglet des signatures d’accès partagé mis en surbrillance](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. Sélectionnez **Fichier volumineux** dans **Ajouter un schéma** et indiquez l’URI de signature d’accès partagé dans la zone de texte **URI du contenu**.

    ![Schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. Dans le panneau **Schémas** de votre compte d’intégration, votre nouveau schéma doit maintenant s’afficher.

    ![Votre compte d’intégration, avec « Schémas » et le nouveau schéma mis en surbrillance](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Modification de schémas

1. Choisissez la mosaïque **Schémas**.

2. Dans le panneau **Schémas** qui s’affiche, sélectionnez le schéma que vous souhaitez modifier.

3. Dans le panneau **Schémas**, sélectionnez **Modifier**.

    ![Panneau Schémas](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Sélectionnez le fichier de schéma que vous souhaitez modifier, puis cliquez sur **Ouvrir**.

    ![Ouvrir le fichier de schéma à modifier](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure affiche un message indiquant que le schéma a été téléchargé avec succès.

## <a name="delete-schemas"></a>Suppression de schémas

1. Choisissez la mosaïque **Schémas**.

2. Dans le panneau **Schémas** qui s’affiche, sélectionnez le schéma que vous souhaitez supprimer.

3. Dans le panneau **Schémas**, sélectionnez **Supprimer**.

    ![Panneau Schémas](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Pour confirmer que vous souhaitez supprimer le schéma sélectionné, cliquez sur **Oui**.

    ![Message de confirmation « Supprimer le schéma »](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    Dans le panneau **Schémas**, la liste des schémas est actualisée et n’inclut plus le schéma que vous avez supprimé.

    ![Votre compte d’intégration, avec « Schémas » mis en surbrillance](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack").  

