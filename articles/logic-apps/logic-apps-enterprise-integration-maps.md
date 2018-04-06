---
title: Convertir des données XML avec des mappages XSLT - Azure Logic Apps | Microsoft Docs
description: Ajouter des mappages XSLT pour convertir des données XML avec Azure Logic Apps et Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4b4d626028eed09e9ce6a45fa8fa69859c082da7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="add-maps-for-xml-data-transform"></a>Ajouter des mappages de conversion des données XML

Enterprise Integration utilise des mappages pour convertir les données XML d’un format vers un autre. Un mappage est un document XML qui définit les données d’un document qui doivent être converties dans un autre format. 

## <a name="why-use-maps"></a>Pourquoi utiliser des mappages ?

Imaginons que vous recevez régulièrement des commandes ou des factures B2B de la part d’un client qui utilise le format AAAMMJJ pour les dates. Mais dans votre entreprise, les dates sont enregistrées au format MMJJAAA. Vous pouvez utiliser un mappage pour *convertir* le format de date AAAMMJJ vers MMJJAAA avant d'enregistrer les détails de la commande ou de la facture dans votre base de données clients.


## <a name="how-do-i-create-a-map"></a>Comment créer un mappage ?

Vous pouvez créer des projets d’intégration BizTalk grâce à [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack") pour Visual Studio 2015. Vous pouvez ensuite créer un fichier de mappage d’intégration qui vous permet de représenter graphiquement les éléments entre les deux fichiers de schéma XML. Après avoir créé ce projet, vous disposerez d’un document XSLT.

Si le mappage comporte une référence à un assembly externe, les deux doivent être chargés vers le compte d’intégration. Ils doivent être chargés dans un ordre spécifique, en premier l’assembly, puis le mappage qui fait référence à l’assembly.


## <a name="how-do-i-add-a-map"></a>Comment ajouter un mappage ?

1. Dans le portail Azure, sélectionnez **Parcourir**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Comptes d’intégration** dans la liste des résultats.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Sélectionnez le compte d’intégration auquel vous souhaitez ajouter le mappage.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Sélectionnez la mosaïque **Mappages**.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Une fois la page Mappages ouverte, choisissez **Ajouter**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Entrez un **nom** pour votre mappage. Pour télécharger le fichier de mappage, sélectionnez l’icône de dossier située à droite de la zone de texte **Mappage**. Une fois le processus de téléchargement terminé, sélectionnez **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Une fois qu’Azure ajoute le mappage à votre compte d’intégration, vous obtenez un message à l’écran qui indique si votre fichier de mappage a été ajouté ou non. Une fois que vous obtenez ce message, choisissez la mosaïque **Mappages** pour pouvoir afficher le mappage ajouté.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Comment ajouter un assembly ?
Ouvrez le compte d’intégration dans lequel vous souhaitez charger l’assembly.

1. Choisissez la mosaïque **Assemblys**.

    ![mosaïque-assembly-compteintegration](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Une fois la page Assemblys ouverte, choisissez **Ajouter**. Entrez un **Nom** pour votre assembly. Pour charger le fichier d’assembly, sélectionnez l’icône de dossier située à droite de la zone de texte **Assembly**. Une fois le processus de téléchargement terminé, sélectionnez **OK**.

    ![ajouter-assembly](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Comment modifier un mappage ?

Vous devez télécharger un nouveau fichier de mappage intégrant les modifications souhaitées. Vous pouvez d’abord télécharger le mappage pour modification.

Pour télécharger un nouveau mappage remplaçant un mappage existant, procédez comme suit :

1. Choisissez la mosaïque **Mappages**.

2. Une fois la page Mappages ouverte, sélectionnez le mappage que vous souhaitez modifier.

3. Dans la page **Mappages**, choisissez **Mettre à jour**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Dans le sélecteur de fichiers, sélectionnez le fichier de mappage que vous souhaitez télécharger, puis sélectionnez **Ouvrir**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Comment supprimer un mappage ?

1. Choisissez la mosaïque **Mappages**.

2. Une fois la page Mappages ouverte, sélectionnez le mappage que vous souhaitez supprimer.

3. Choisissez **Supprimer**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Confirmez que vous souhaitez supprimer le mappage.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  
* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  
* [En savoir plus sur les transformations](logic-apps-enterprise-integration-transform.md "Découvrez les transformations d’intégration d’entreprise")  

