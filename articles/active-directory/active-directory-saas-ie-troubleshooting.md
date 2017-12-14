---
title: "Résolution des problèmes liés à l’extension Volet d’accès Azure pour IE| Microsoft Docs"
description: "Comment utiliser la stratégie de groupe pour déployer le module complémentaire Internet Explorer du portail Mes applications."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff6d0b299c45d89ee8d3a79fa98ce4a542174a5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Résolution des problèmes liés à l’extension Volet d’accès pour Internet Explorer
Cet article vous aide à résoudre les problèmes suivants :

* Vous ne parvenez pas à accéder à vos applications par le biais du portail Mes applications quand vous utilisez Internet Explorer.
* Le message « Installer le logiciel » s’affiche même si vous avez déjà installé le logiciel.

Si vous êtes administrateur, consultez aussi : [Déploiement de l’extension Volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Exécution de l’outil de diagnostic
Vous pouvez diagnostiquer les problèmes d’installation de l’extension Volet d’accès en téléchargeant et en exécutant l’outil de diagnostic du volet d’accès :

1. [Cliquez ici pour télécharger l’outil de diagnostic.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Ouvrez le fichier, puis appuyez sur le bouton **Extraire tout** .
   
    ![Appuyer sur Extraire tout](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Appuyez sur le bouton **Extraire** pour continuer.
   
    ![Appuyer sur Extraire](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Pour exécuter l’outil, cliquez sur le fichier nommé **AccessPanelExtensionDiagnosticTool**, puis sélectionnez **Ouvrir avec > Microsoft Windows Based Script Host**.
   
    ![Ouvrir avec > Microsoft Windows Based Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. La fenêtre de diagnostic suivante s’ouvre, qui décrit les éventuels problèmes liés à votre installation.
   
    ![Exemple de fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Cliquez sur**Oui**pour autoriser le programme à résoudre les problèmes identifiés.
7. Pour enregistrer ces modifications, fermez toutes les fenêtres d’Internet Explorer, puis rouvrez Internet Explorer.<br />Si vous ne pouvez toujours pas accéder à vos applications, essayez les étapes ci-dessous.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Vérifiez que l’extension Volet d’accès est activée.
Pour vérifier que l’extension Volet d’accès est activée dans Internet Explorer :

1. Dans Internet Explorer, cliquez sur l’**icône d’engrenage** en haut à droite de la fenêtre. Puis sélectionnez **Options Internet**.<br />(Dans les versions antérieures d’Internet Explorer, ce menu se trouve dans **Outils > Options Internet**.
   
    ![Accéder à Outils > Options Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Cliquez sur l’onglet **Programmes**, puis sur le bouton **Gérer les modules complémentaires**.
   
    ![Cliquer sur Gérer les modules complémentaires](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. Dans cette boîte de dialogue, sélectionnez **Extension Volet d’accès**, puis cliquez sur le bouton **Activer**.
   
    ![Cliquer sur Activer](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Pour enregistrer ces modifications, fermez toutes les fenêtres d’Internet Explorer, puis rouvrez Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Activation des extensions pour la navigation InPrivate
Si vous utilisez le mode de navigation InPrivate :

1. Dans Internet Explorer, cliquez sur l’**icône d’engrenage** en haut à droite de la fenêtre. Puis sélectionnez **Options Internet**.<br />(Dans les versions antérieures d’Internet Explorer, ce menu se trouve dans **Outils > Options Internet**.
   
    ![Exemple de fenêtre de diagnostic](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Accédez à l’onglet **Confidentialité**, puis **décochez** la case **Désactiver les barres d’outils et les extensions lors du démarrage de la navigation InPrivate**</p>
   
    ![Décocher Désactiver les barres d’outils et les extensions lors du démarrage de la navigation InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Pour enregistrer ces modifications, fermez toutes les fenêtres d’Internet Explorer, puis rouvrez Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Désinstallation de l’extension Volet d’accès
Pour désinstaller l’extension Volet d’accès de votre ordinateur :

1. Sur votre clavier, appuyez sur la **touche Windows** pour ouvrir le menu Démarrer. Ce menu vous permet de rechercher une application. Tapez « Panneau de configuration », puis ouvrez le **Panneau de configuration** quand il apparaît dans les résultats de recherche.
   
    ![Rechercher le Panneau de configuration](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. En haut à droite du Panneau de configuration, choisissez **Grandes icônes** sous l’option **Afficher par**. Recherchez et sélectionnez le bouton **Programmes et fonctionnalités**.
   
    ![Modifier l’affichage pour afficher les Grandes icônes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Dans la liste, sélectionnez **Extension Volet d’accès**, puis cliquez sur le bouton **Désinstaller**.
   
    ![Cliquer sur Désinstaller](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Essayez ensuite de réinstaller l’extension pour vérifier que le problème a été résolu.

Si vous rencontrez des problèmes pour désinstaller l’extension, vous pouvez également la supprimer à l’aide de l’outil [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Accès aux applications et authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Déploiement de l'extension du volet d'accès pour Internet Explorer à l'aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)

