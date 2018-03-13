---
title: "Authentification basée sur un en-tête avec PingAccess pour le proxy d’application Azure AD | Microsoft Docs"
description: "Publiez des applications avec PingAccess et Application Proxy pour prendre en charge l’authentification basée sur un en-tête."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bfff8ebff87b6c3c501202e95c463a0f4e235ffc
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Authentification basée sur l’en-tête pour une authentification unique avec le proxy d’application et PingAccess

Azure Active Directory Application Proxy et PingAccess se sont associés pour permettre aux clients Azure Active Directory d’accéder à davantage d’applications. PingAccess développe les [offres de proxy d’application existantes](active-directory-application-proxy-get-started.md) en intégrant l’accès par authentification unique aux applications qui utilisent des en-têtes d’authentification.

## <a name="what-is-pingaccess-for-azure-ad"></a>Présentation de PingAccess pour Azure AD

PingAccess pour Azure Active Directory est une offre de PingAccess qui vous permet d’octroyer aux utilisateurs un accès et une authentification unique aux applications qui utilisent des en-têtes pour l’authentification. Application Proxy traite ces applications comme n’importe quelle autre, en utilisant Azure AD pour authentifier l’accès et faire transiter le trafic via le service de connecteur. PingAccess se trouve devant les applications et convertit le jeton d’accès Azure AD en en-tête afin que l’application reçoive l’authentification dans un format qu’elle puisse lire.

Vos utilisateurs ne remarqueront rien lorsqu’ils se connecteront pour utiliser vos applications d’entreprise. Ils pourront toujours travailler depuis n’importe où et sur n’importe quel appareil. 

Par ailleurs, comme les connecteurs Application Proxy redirigent le trafic distant vers l’ensemble des applications, quel que soit leur type d’authentification, ils continuent à équilibrer la charge automatiquement.

## <a name="how-do-i-get-access"></a>Comment puis-je avoir accès ?

Comme ce scénario est possible grâce à un partenariat entre Azure Active Directory et PingAccess, vous avez besoin de licences pour les deux services. Toutefois, les abonnements Azure Active Directory Premium incluent une licence PingAccess de base qui couvre jusqu’à 20 applications. Si vous devez publier plus de 20 applications basées sur un en-tête, vous pouvez acheter une licence supplémentaire auprès de PingAccess. 

Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Publier votre application dans Azure

Cet article s’adresse aux personnes qui publient une application pour la première fois dans ce cas de figure. Il explique comment mettre en œuvre Application Proxy et PingAccess, en plus de la procédure de publication. Si vous avez déjà configuré les deux services mais souhaitez bénéficier d’un rappel sur la procédure de publication, vous pouvez ignorer l’installation du connecteur et passer à [Ajouter votre application dans Azure AD avec Application Proxy](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Comme ce cas de figure repose sur un partenariat entre Azure AD et PingAccess, certaines de ces instructions figurent sur le site Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installer un connecteur Application Proxy

Si le proxy d’application est activé et si le connecteur est installé, vous pouvez ignorer cette section et passer à [Ajouter votre application dans Azure AD avec Application Proxy](#add-your-app-to-azure-ad-with-application-proxy).

Le connecteur Application Proxy est un service Windows Server qui dirige le trafic de vos employés distants vers vos applications publiées. Pour plus d’informations sur l’installation, consultez [Activer Application Proxy dans le portail Azure](active-directory-application-proxy-enable.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Application Proxy**.
3. Sélectionnez **Download Connector** (Télécharger le connecteur) pour lancer le téléchargement du connecteur Application Proxy. Suivez les instructions d’installation.

   ![Activer Application Proxy et télécharger le connecteur](./media/application-proxy-ping-access/install-connector.png)

4. Le téléchargement du connecteur doit activer automatiquement Application Proxy pour votre annuaire, mais pas si vous pouvez sélectionner **Enable Application Proxy** (Activer Application Proxy).


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Ajouter votre application dans Azure AD avec Application Proxy

Vous devez effectuer deux actions dans le portail Azure. Tout d’abord, vous devez publier votre application avec le proxy d’application. Puis vous devez collecter certaines informations sur l’application que vous pouvez utiliser pendant la procédure PingAccess.

Suivez ces étapes pour publier votre application. Pour obtenir plus de détails sur les étapes 1 à 8, consultez [Publier des applications avec le Proxy d’application Azure AD](application-proxy-publish-azure-portal.md).

1. Si cela n’a pas été fait dans la partie précédente, connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur global.
2. Sélectionnez **Azure Active Directory (Azure Active Directory)** > **Enterprise applications (Applications d’entreprise)**.
3. Sélectionnez **Ajouter** en haut du panneau.
4. Sélectionnez **On-premises application (Application locale)**.
5. Saisissez les informations concernant votre nouvelle application dans les champs requis. Suivez les conseils ci-dessous pour les paramètres :
   - **URL interne** : logiquement, vous devez indiquer l’URL de la page de connexion de l’application lorsque vous êtes sur le réseau d’entreprise. Pour les besoins de ce partenariat, le connecteur doit traiter le proxy PingAccess en tant que première page de l’application. Utilisez le format suivant : `https://<host name of your PA server>:<port>`. Le port par défaut est 3000, mais vous pouvez le configurer dans PingAccess.

    > [!WARNING]
    > Pour ce type d’authentification unique, l’URL interne doit utiliser le protocole https et ne peut pas utiliser le protocole http.

   - **Méthode de pré-authentification** : Azure Active Directory
   - **Traduire des URL dans les en-têtes** : Non

   >[!NOTE]
   >S’il s’agit de votre première application, utilisez le port 3000 pour démarrer et revenir afin de mettre à jour ce paramètre si vous modifiez votre configuration PingAccess. S’il s’agit de votre deuxième application ou d’une application ultérieure, le paramètre doit correspondre à l’écouteur que vous avez configuré dans PingAccess. Découvrez plus en détail les [écouteurs dans PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Sélectionnez **Ajouter** en bas du panneau. Votre application est ajoutée et le menu de démarrage rapide s’ouvre.
7. Dans le menu de démarrage rapide, sélectionnez **Assign a user for testing (Attribuer un utilisateur à des fins de test)**, et ajoutez au moins un utilisateur à l’application. Vérifiez que ce compte de test a accès à l’application locale.
8. Sélectionnez **Affecter** pour enregistrer l’affectation de l’utilisateur de test.
9. Dans le panneau de gestion de l’application, sélectionnez **Authentification unique**.
10. Choisissez **Authentification basée sur un en-tête** dans le menu déroulant. Sélectionnez **Enregistrer**.

   >[!TIP]
   >S’il s’agit votre première authentification unique basée sur un en-tête, vous devez installer PingAccess. Pour vous assurer que votre abonnement Azure est automatiquement associé à votre installation PingAccess, utilisez le lien de cette page d’authentification unique afin de télécharger PingAccess. Vous pouvez ouvrir le site de téléchargement maintenant ou revenir sur cette page ultérieurement. 

   ![Sélectionner l’authentification basée sur un en-tête](./media/application-proxy-ping-access/sso-header.PNG)

11. Fermez le panneau des applications d’entreprise ou faites défiler vers la gauche pour revenir au menu Azure Active Directory.
12. Sélectionnez **Inscriptions d’applications**.

   ![Sélectionner Inscriptions d’applications](./media/application-proxy-ping-access/app-registrations.png)

13. Sélectionnez l’application que vous venez d’ajouter, puis sélectionnez **Reply URLs (URL de réponse)**.

   ![Sélectionner URL de réponse](./media/application-proxy-ping-access/reply-urls.png)

14. Vérifiez si l’URL externe que vous avez affectée à votre application à l’étape 5 figure dans la liste des URL de réponse. Si tel n’est pas le cas, ajoutez-la maintenant.
15. Dans le panneau Paramètres d’application, sélectionnez **Required permissions (Autorisations requises)**.

  ![Sélectionner Autorisations requises](./media/application-proxy-ping-access/required-permissions.png)

16. Sélectionnez **Ajouter**. Pour l’API, choisissez **Windows Azure Active Directory**, puis **Sélectionner**. Pour les autorisations, choisissez **Lire et écrire toutes les applications** et **Se connecter et lire le profil utilisateur**, puis **Sélectionner** et **Terminé**.  

  ![Sélectionner les autorisations](./media/application-proxy-ping-access/select-permissions.png)

17. Attribuez les autorisations avant de fermer la fenêtre des autorisations. 
![Accorder des autorisations](media/application-proxy-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Collecter les informations pour la procédure PingAccess

1. Dans le panneau des paramètres de l’application, sélectionnez **Propriétés**. 

  ![Sélectionner Propriétés](./media/application-proxy-ping-access/properties.png)

2. Enregistrez la valeur figurant dans **ID de l’Application**. Cette information est utilisée comme ID de client lors de la configuration de PingAccess.
3. Dans le panneau des paramètres de l’application, sélectionnez **Clés**.

  ![Sélectionner Clés](./media/application-proxy-ping-access/Keys.png)

4. Créez une clé en entrant une description et en choisissant une date d’expiration dans le menu déroulant.
5. Sélectionnez **Enregistrer**. Un GUID s’affiche dans le champ **Valeur**.

  Enregistrez cette valeur maintenant, car vous ne la reverrez plus, une fois cette fenêtre fermée.

  ![Créer une clé](./media/application-proxy-ping-access/create-keys.png)

6. Fermez le panneau des inscriptions d’application ou faites défiler vers la gauche pour revenir au menu Azure Active Directory.
7. Sélectionner **Propriétés**.
8. Enregistrez le GUID **ID de répertoire**.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Facultatif - Mise à jour de GraphAPI pour envoyer des champs personnalisés

Pour obtenir la liste des jetons de sécurité qu’envoie Azure AD pour l’authentification, consultez [Référence sur les jetons Azure AD](./develop/active-directory-token-and-claims.md). Si vous avez besoin d’une revendication personnalisée qui envoie d’autres jetons, utilisez Graph Explorer ou le manifeste de l’application dans le portail Azure pour définir le champ d’application *acceptMappedClaims* sur **True**.    

Cet exemple utilise l’Explorateur Graph :

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Cet exemple utilise le [portail Azure](https://portal.azure.com) pour mettre à jour le champ *acceptedMappedClaims* :
1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Sélectionnez **Azure Active Directory** > **Inscriptions des applications**.
3. Sélectionnez votre application > **Manifeste**.
4. Sélectionnez **Modifier**, recherchez le champ *acceptedMappedClaims* et remplacez la valeur par **true**.
![Manifeste de l’application](media/application-proxy-ping-access/application-proxy-ping-access-manifest.PNG)
1. Sélectionnez **Enregistrer**.

>[!NOTE]
>Pour utiliser une revendication personnalisée, vous devez également disposer d’une stratégie personnalisée définie et affectée à l’application.  Cette stratégie doit inclure tous les attributs personnalisés nécessaires.
>
>La définition et l’attribution de la stratégie peuvent être effectuées dans MS Graph, l’Explorateur Azure AD Graph et PowerShell.  Si vous utilisez PowerShell, vous devez d’abord utiliser `New-AzureADPolicy `, puis l’attribuer à l’application avec `Set-AzureADServicePrincipalPolicy`.  Pour plus d’informations, consultez la [documentation relative aux stratégies Azure AD](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Facultatif : Utiliser une revendication personnalisée
Pour que votre application utilise une revendication personnalisée et comprenne des champs supplémentaires, [créez une stratégie de mappage de revendications personnalisées et attribuez-la à l’application](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Télécharger PingAccess et configurer votre application

Maintenant que vous avez terminé toutes les étapes de configuration d’Azure Active Directory, vous pouvez passer à la configuration de PingAccess. 

La procédure concernant PingAccess dans ce scénario continue dans la documentation de Ping Identity sur la [configuration de PingAccess pour Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Ces étapes expliquent comment obtenir un compte PingAccess (si vous n’en avez pas), installer le serveur PingAccess et créer une connexion Azure AD OIDC Provider avec l’ID de répertoire que vous avez copié à partir du portail Azure. Ensuite, utilisez les valeurs ID d’application et Clé pour créer une session Web sur PingAccess. Enfin, vous pouvez configurer le mappage d’identité et créer un hôte virtuel, le site et l’application.

### <a name="test-your-app"></a>Test de l'application

Une fois toutes ces étapes effectuées, votre application doit être opérationnelle. Pour la tester, ouvrez un navigateur et accédez à l’URL externe que vous avez créée lors de la publication de l’application dans Azure. Connectez-vous au compte de test que vous avez attribué à l’application.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer PingAccess pour Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Comment le proxy d’application Azure AD fournit-il une authentification unique ?](application-proxy-sso-overview.md)
- [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md)
