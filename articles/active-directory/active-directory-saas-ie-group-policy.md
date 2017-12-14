---
title: "Déployer l’extension Volet d’accès Azure pour IE à l’aide d’un GPO | Microsoft Docs"
description: "Comment utiliser la stratégie de groupe pour déployer le module complémentaire Internet Explorer du portail Mes applications."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a203548575eacb2d0eb0d09a4aaf239b11caad3c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Déploiement de l’extension Volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe
Ce didacticiel montre comment utiliser la stratégie de groupe pour installer à distance l’extension Volet d’accès pour Internet Explorer sur les ordinateurs de vos utilisateurs. Cette extension est requise pour les utilisateurs d’Internet Explorer qui ont besoin de se connecter à des applications configurées à l’aide de l’ [authentification unique par mot de passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Il est recommandé que les administrateurs automatisent le déploiement de cette extension. Dans le cas contraire, les utilisateurs doivent télécharger et installer l’extension eux-mêmes, ce qui peut entraîner des erreurs des utilisateurs et nécessite des autorisations d’administrateur. Ce didacticiel présente une méthode d’automatisation des déploiements de logiciels à l’aide d’une stratégie de groupe. [En savoir plus sur la stratégie de groupe.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

L’extension Volet d’accès est également disponible pour [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) et [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) qui ne requièrent pas d’autorisations d’administrateur pour l’installation.

## <a name="prerequisites"></a>Conditions préalables
* Vous avez configuré les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)et vous avez joint les ordinateurs de vos utilisateurs à votre domaine.
* Vous devez disposer de l’autorisation « Modifier les paramètres » pour modifier l’objet de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants jouissent de cette autorisation : administrateurs de domaine, administrateurs d’entreprise et propriétaires créateurs de la stratégie de groupe. [En savoir plus.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Étape 1 : Créer le point de distribution
Tout d’abord, vous devez placer le package d’installation sur un emplacement réseau accessible à tous les ordinateurs sur lesquels vous souhaitez installer l’extension à distance. Pour ce faire, procédez comme suit :

1. Connectez-vous au serveur en tant qu’administrateur.
2. Dans la fenêtre **Gestionnaire de serveur**, accédez à **Services de fichiers et de stockage**.
   
    ![Ouvrir Services de fichiers et de stockage](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Accédez à l’onglet **Partages** . Ensuite, cliquez sur **Tâches** > **Nouveau partage...**
   
    ![Ouvrir Services de fichiers et de stockage](./media/active-directory-saas-ie-group-policy/shares.png)
4. Terminez l’ **Assistant Nouveau partage** et définissez des autorisations pour garantir l’accès à partir des ordinateurs de vos utilisateurs. [En savoir plus sur les partages.](https://technet.microsoft.com/library/cc753175.aspx)
5. Téléchargez le package Microsoft Windows Installer (fichier .msi) suivant : [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi) (Panel Extension.msi)
6. Copiez le package d’installation vers l’emplacement souhaité sur le partage.
   
    ![Copiez le fichier .msi dans le partage.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Vérifiez que vos ordinateurs client sont en mesure d’accéder au package d’installation à partir du partage. 

## <a name="step-2-create-the-group-policy-object"></a>Étape 2 : Créer l’objet de stratégie de groupe
1. Ouvrez une session sur le serveur qui héberge votre installation AD DS (services de domaine Active Directory).
2. Dans le Gestionnaire de serveur, accédez à **Outils** > **Gestion des stratégies de groupe**.
   
    ![Accéder à Outils > Gestion des stratégies de groupe](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. Dans le volet gauche de la fenêtre **Gestion des stratégies de groupe**, affichez votre hiérarchie des unités d’organisation (UO) et déterminez l’étendue selon laquelle vous aimeriez appliquer la stratégie de groupe. Par exemple, vous pouvez décider de sélectionner une petite UO à déployer pour quelques utilisateurs à des fins de test. Vous pouvez également choisir une UO de niveau supérieur à déployer dans toute votre organisation.
   
   > [!NOTE]
   > Si vous souhaitez créer ou modifier vos unités d’organisation (UO), revenez au Gestionnaire de serveur et accédez à **Outils** > **Utilisateurs et ordinateurs Active Directory**.
   > 
   > 
4. Une fois que vous avez sélectionné une UO, cliquez dessus avec le bouton droit et sélectionnez **Créer un objet GPO dans ce domaine, et le lier ici...**
   
    ![Créer un objet GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. Dans l’invite **Nouvel objet GPO** , tapez un nom pour le nouvel objet de stratégie de groupe.
   
    ![Nommer le nouvel objet GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Cliquez avec le bouton droit sur l’objet de stratégie de groupe que vous avez créé, puis sélectionnez **Modifier**.
   
    ![Modifier le nouvel objet GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Étape 3 : Assigner le package d’installation
1. Déterminez si vous souhaitez déployer l’extension selon la **Configuration ordinateur** ou la **Configuration utilisateur**. Lorsque vous utilisez la [Configuration ordinateur](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), l’extension est installée sur l’ordinateur, quels que soient les utilisateurs qui s’y connectent. Avec la [Configuration utilisateur](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), l’extension est installée pour les utilisateurs, quels que soient les ordinateurs auxquels ils se connectent.
2. Dans le volet gauche de la fenêtre **Éditeur de gestion des stratégies de groupe** , accédez à l’un des chemins de dossier suivants, selon le type de configuration que vous avez choisi :
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Cliquez avec le bouton droit sur **Installation de logiciel**, puis sélectionnez **Nouveau** > **Package...**
   
    ![Créer un package d’installation de logiciel](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Accédez au dossier partagé qui contient le package d’installation dans [Étape 1 : Créer le point de distribution](#step-1-create-the-distribution-point), sélectionnez le fichier .msi, puis cliquez sur **Ouvrir**.
   
   > [!IMPORTANT]
   > Si le partage se trouve sur le même serveur, vérifiez que vous accédez au fichier .msi via le chemin d’accès du fichier réseau, et non via le chemin d’accès du fichier local.
   > 
   > 
   
    ![Sélectionnez le package d’installation à partir du dossier partagé.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. Dans l’invite **Déploiement du logiciel**, sélectionnez **Affecté** pour votre méthode de déploiement. Cliquez ensuite sur **OK**.
   
    ![Sélectionnez Affecté, puis cliquez sur OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

L’extension est désormais déployée sur l’UO que vous avez sélectionnée. [En savoir plus sur l’installation des logiciels de la stratégie de groupe.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Étape 4 : Activer automatiquement l’extension pour Internet Explorer
Outre l’exécution du programme d’installation, toutes les extensions pour Internet Explorer doivent être explicitement activées avant de pouvoir être utilisées. Suivez les étapes ci-dessous pour activer l’extension Volet d’accès à l’aide de la stratégie de groupe :

1. Dans la fenêtre **Éditeur de gestion des stratégies de groupe** , accédez à l’un des chemins suivants, selon le type de configuration que vous avez choisi dans [Étape 3 : Assigner le package d’installation](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Cliquez avec le bouton droit sur **Liste des modules complémentaires**, puis sélectionnez **Modifier**.
    ![Modifiez la liste des modules complémentaires.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. Dans la fenêtre **Liste des modules complémentaires**, sélectionnez **Activé**. Ensuite, sous la section **Options** cliquez sur **Afficher...**.
   
    ![Cliquez sur Activer, puis sur Afficher...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. Dans la fenêtre **Afficher le contenu** , procédez comme suit :
   
   1. Pour la première colonne (champ **Nom de la valeur**), copiez et collez l’ID de classe suivant : `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Pour la deuxième colonne (champ **Valeur**), tapez la valeur suivante : `1`
   3. Cliquez sur **OK** pour fermer la fenêtre **Afficher le contenu**.
      
      ![Remplissez les valeurs comme indiqué ci-dessus.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Cliquez sur **OK** pour appliquer vos modifications et fermer la fenêtre **Liste des modules complémentaires**.

L’extension doit désormais être activée pour les ordinateurs dans l’UO sélectionnée. [En savoir plus sur l’utilisation de la stratégie de groupe pour activer ou désactiver les modules complémentaires d’Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Étape 5 (Facultatif) : Désactiver l’invite « Mémoriser le mot de passe »
Lorsque les utilisateurs se connectent à des sites Web à l'aide de l'Extension Volet d'accès, Internet Explorer peut afficher le message suivant : « Souhaitez-vous enregistrer votre mot de passe ? »

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Si vous ne souhaitez pas que les utilisateurs puissent accéder à ce message, suivez les étapes mentionnées ci-dessous pour que la saisie semi-automatique ne procède pas à la mémorisation de mots de passe :

1. Dans la fenêtre **Éditeur de gestion des stratégies de groupe** , accédez au chemin d'accès ci-dessous. Ce paramètre de configuration n’est disponible que sous **Configuration utilisateur**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Recherchez le paramètre nommé **Activer la saisie semi-automatique des noms d'utilisateur et des mots de passe dans les formulaires**.
   
   > [!NOTE]
   > Les versions précédentes d'Active Directory peuvent répertorier ce paramètre via le nom **Ne pas autoriser la saisie semi-automatique à enregistrer des mots de passe**. La configuration de ce paramètre diffère du paramètre décrit dans ce didacticiel.
   > 
   > 
   
    ![N'oubliez pas de le rechercher sous Paramètres utilisateur.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Cliquez avec le bouton droit sur le paramètre ci-dessus, puis sélectionnez **Modifier**.
4. Dans la fenêtre intitulée **Activer la saisie semi-automatique des noms d'utilisateur et des mots de passe dans les formulaires**, sélectionnez **Désactivé**.
   
    ![Sélectionner Désactiver](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Cliquez sur **OK** pour appliquer ces modifications et fermer la fenêtre.

Les utilisateurs ne seront plus en mesure d’enregistrer leurs informations d'identification ou d’utiliser la saisie semi-automatique pour accéder aux informations d'identification enregistrées précédemment. Toutefois, cette stratégie permet aux utilisateurs de continuer à utiliser la saisie semi-automatique pour les autres types de champs de formulaire, tels que les champs de recherche.

> [!WARNING]
> Si cette stratégie est activée après que les utilisateurs aient choisi de stocker certaines informations d'identification, cette stratégie n’effacera *pas* les informations d'identification qui ont déjà été enregistrées.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Étape 6 : tester le déploiement
Suivez les étapes ci-dessous pour vérifier si le déploiement de l’extension a réussi :

1. Si vous avez effectué le déploiement à l’aide de la **Configuration ordinateur**, connectez-vous à un ordinateur client appartenant à l’UO que vous avez sélectionnée dans [Étape 2 : Créer l’objet de stratégie de groupe](#step-2-create-the-group-policy-object). Si vous avez effectué le déploiement à l’aide de la **Configuration utilisateur**, veillez à vous connecter en tant qu’utilisateur appartenant à cette UO.
2. Les modifications de la stratégie de groupe peuvent nécessiter plusieurs connexions pour s’appliquer sur cet ordinateur. Pour forcer la mise à jour, ouvrez une fenêtre **d’invite de commande** et exécutez la commande suivante : `gpupdate /force`
3. Vous devez redémarrer l’ordinateur pour lancer l’installation. Le démarrage peut prendre beaucoup plus de temps que d’habitude lors de l’installation de l’extension.
4. Après avoir redémarré, ouvrez **Internet Explorer**. Dans le coin supérieur droit de la fenêtre, cliquez sur **Outils** (l’icône d’engrenage), puis sélectionnez **Gérer les modules complémentaires**.
   
    ![Accéder à Outils > Gérer les modules complémentaires](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. Dans la fenêtre **Gérer les modules complémentaires**, vérifiez que l’**extension Volet d’accès** a été installée et que son **État** a été défini sur **Activé**.
   
    ![Vérifiez que l’extension Volet d’accès est installée et activée.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Accès aux applications et authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Résolution des problèmes liés à l'extension du volet d'accès pour Internet Explorer](active-directory-saas-ie-troubleshooting.md)

