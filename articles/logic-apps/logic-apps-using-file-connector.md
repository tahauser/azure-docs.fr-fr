---
title: "Se connecter aux systèmes de fichiers locaux - Azure Logic Apps | Microsoft Docs"
description: "Se connecter aux systèmes de fichiers locaux à partir de workflows d’application logique par le biais de la passerelle de données locale et du connecteur de système de fichiers"
keywords: "systèmes de fichiers, locaux"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 32ab5be41a8dee3b1f2c0b1bde076c0d1a844bdd
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Se connecter aux systèmes de fichiers local à partir d’applications logiques avec le connecteur du système de fichiers

Pour gérer les données et accéder en toute sécurité aux ressources locales, vos applications logiques peuvent utiliser la passerelle de données locale. Cet article vous explique comment vous connecter à un système de fichiers local par l’intermédiaire de cet exemple de scénario simple : copie d’un fichier chargé dans Dropbox vers un partage de fichiers, puis envoi d’un e-mail.

## <a name="prerequisites"></a>configuration requise

* Téléchargez la toute dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127).

* Installez et configurez la dernière passerelle de données locale, version 1.15.6150.1 ou ultérieure. Pour connaître la procédure à suivre, consultez l’article [Connexion à des sources de données locales](http://aka.ms/logicapps-gateway). Vous devez installer la passerelle sur une machine locale avant de pouvoir effectuer le reste de la procédure.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Ajouter un déclencheur et des actions pour se connecter au système de fichiers

1. Créez une application logique vide. Ajoutez ce déclencheur comme première étape : **Dropbox - Quand un fichier est créé**. 

2. Sous le déclencheur, choisissez **+ Étape suivante** > **Ajouter une action**. 

3. Dans la zone de recherche, entrez « système de fichiers » en tant que filtre. Lorsque vous voyez apparaître toutes les actions pour le connecteur de système de fichiers, choisissez l’action **Système de fichiers - Créer un fichier**. 

   ![Rechercher le connecteur de fichiers](media/logic-apps-using-file-connector/search-file-connector.png)

4. Si vous ne disposez pas encore d’une connexion à votre système de fichiers, vous êtes invité à créer une connexion. 

5. Sélectionnez l’option **Se connecter via la passerelle de données locale**. Lorsque les propriétés de connexion apparaissent, configurez votre connexion comme spécifié dans le tableau.

   ![Configurer la connexion](media/logic-apps-using-file-connector/create-file.png)

   | Paramètre | DESCRIPTION |
   | ------- | ----------- |
   | **Dossier racine** | Spécifiez le dossier racine de votre système de fichiers. Vous pouvez spécifier un dossier local situé sur la machine où est installée la passerelle de données locale ; il peut également s’agir d’un partage réseau auquel la machine a accès. <p>**Conseil :** Le dossier racine est le dossier parent principal, qui est utilisé pour les chemins d’accès relatifs de toutes les actions liées aux fichiers. | 
   | **Type d’authentification** | Type d’authentification utilisé par votre système de fichiers. | 
   | **Nom d’utilisateur** | Fournissez votre nom d’utilisateur {*domaine*\\*nom d’utilisateur*} pour la passerelle que vous avez installée précédemment. | 
   | **Mot de passe** | Fournissez votre mot de passe pour la passerelle que vous avez installée précédemment. | 
   | **Passerelle** | Sélectionnez la passerelle que vous avez installée précédemment. | 
   ||| 

6. Après avoir fourni tous les détails de connexion, choisissez **Créer**. 

   Logic Apps configure et teste votre connexion pour vérifier son bon fonctionnement. 
   Si la connexion est configurée correctement, des options apparaissent pour l’action que vous avez sélectionnée précédemment. 
   Le connecteur Système de fichiers est maintenant prêt à être utilisé.

7. Configurez l’action **Créer un fichier** pour la copie de fichiers de Dropbox vers le dossier racine de votre partage de fichiers local.

   ![Créer une action de fichier](media/logic-apps-using-file-connector/create-file-filled.png)

8. Après cette action de copie du fichier, ajoutez une action Outlook qui envoie un e-mail afin que les utilisateurs concernés soient informés de la présence du nouveau fichier. Saisissez les destinataires, l’objet et le corps de l’e-mail. 

   Dans la liste **Contenu dynamique**, vous pouvez choisir les sorties de données du connecteur de fichier pour être en mesure d’ajouter plus de détails à l’e-mail.

   ![Envoyer l’action de messagerie](media/logic-apps-using-file-connector/send-email.png)

9. Enregistrez votre application logique. Testez votre application en chargeant un fichier sur Dropbox. Le fichier se copie sur le partage de fichiers local et vous recevrez un e-mail concernant l’opération.

Félicitations, vous avez maintenant une application logique en état de fonctionnement qui peut se connecter à votre système de fichiers local. 

Essayez de découvrir les autres fonctionnalités offertes par le connecteur, par exemple :

- Créer un fichier
- Répertorier les fichiers dans un dossier
- Ajouter un fichier
- Supprimer un fichier
- Obtenir le contenu d’un fichier
- Obtenir le contenu d’un fichier à l’aide du chemin
- Obtenir les métadonnées d’un fichier
- Obtenir les métadonnées d’un fichier à l’aide du chemin
- Répertorier les fichiers dans le dossier racine
- Mettre à jour un fichier

## <a name="view-the-swagger"></a>Afficher le swagger

Consultez les [détails sur Swagger](/connectors/fileconnector/). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site UserVoice concernant Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Se connecter à des données locales](../logic-apps/logic-apps-gateway-connection.md) 
* [Analyser vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Intégration Entreprise pour les scénarios B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
