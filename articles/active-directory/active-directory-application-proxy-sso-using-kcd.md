---
title: "Authentification unique avec le proxy d’application | Microsoft Docs"
description: "Explique comment fournir l’authentification unique à l’aide du proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: d6f6894d495170221ad8334bc32ef141fb4e62b4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Délégation contrainte Kerberos pour l’authentification unique à vos applications avec le proxy d’application

Vous pouvez fournir l’authentification unique pour les applications locales publiées via le proxy d’application et sécurisées avec l’authentification Windows intégrée. L’accès à ces applications nécessitent un ticket Kerberos. Le proxy d’application utilise la délégation Kerberos contrainte(KCD) pour prendre en charge ces applications. 

Vous pouvez activer l’authentification unique pour vos applications avec l’authentification Windows intégrée en attribuant aux connecteurs du proxy d’application dans Active Directory l’autorisation d’emprunter l’identité des utilisateurs. Les connecteurs utilisent cette autorisation pour envoyer et recevoir des jetons en leur nom.

## <a name="how-single-sign-on-with-kcd-works"></a>Fonctionnement de l’authentification unique avec KCD
Ce diagramme explique le flux quand un utilisateur tente d’accéder à une application locale qui utilise I’authentification Windows intégrée.

![Diagramme de flux de l’authentification Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L’utilisateur entre l’URL pour accéder à l’application locale via le proxy d’application.
2. Le proxy d’application redirige la demande vers les services d’authentification d’Azure AD pour effectuer la préauthentification. À ce stade, Azure AD applique les stratégies d’authentification et d’autorisation applicables, comme l’authentification multifacteur. Si l’utilisateur est validé, Azure AD crée un jeton et l’envoie à l’utilisateur.
3. L’utilisateur transmet le jeton au proxy d’application.
4. Le proxy d’application valide le jeton et y récupère le nom d’utilisateur principal (UPN), puis envoie la demande, l’UPN et le nom de principal du service (SPN) au connecteur via un canal sécurisé doublement authentifié.
5. Le connecteur effectue une négociation de délégation Kerberos contrainte avec Active Directory local, en empruntant l’identité de l’utilisateur pour obtenir un jeton Kerberos pour l’application.
6. Active Directory envoie le jeton Kerberos de l’application au connecteur.
7. Le connecteur envoie la demande d’origine au serveur d’applications, en utilisant le jeton Kerberos reçu d’Active Directory.
8. L’application envoie la réponse au connecteur, qui est ensuite retournée au service de proxy d’application et enfin à l’utilisateur.

## <a name="prerequisites"></a>Prérequis
Avant de commencer avec l’authentification unique pour les applications IWA, assurez-vous que votre environnement est prêt à l’aide des configurations et paramètres suivants :

* Vos applications, comme les applications web SharePoint, sont configurées pour utiliser l’authentification Windows intégrée. Pour plus d’informations, consultez [Activer la prise en charge de l’authentification Kerberos](https://technet.microsoft.com/library/dd759186.aspx) ou, pour SharePoint, consultez [Planifier l’authentification Kerberos dans SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Toutes vos applications disposent de [Noms de principal du service](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Le serveur exécutant le connecteur et le serveur exécutant l’application sont joints au domaine et font partie du même domaine ou de domaines sécurisés. Pour plus d’informations sur la jonction à un domaine, consultez [Joindre un ordinateur à un domaine](https://technet.microsoft.com/library/dd807102.aspx).
* Le serveur exécutant le connecteur est autorisé à lire l’attribut TokenGroupsGlobalAndUniversal pour les utilisateurs. Ce paramètre par défaut peut avoir été affecté par la sécurisation renforcée de l’environnement.

### <a name="configure-active-directory"></a>Configurer Active Directory
La configuration d’Active Directory varie selon que votre connecteur de proxy d’application et le serveur d’application sont dans le même domaine ou non.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Le connecteur et le serveur d’application sont dans le même domaine
1. Dans Active Directory, accédez à **Outils** > **Utilisateurs et ordinateurs**.
2. Sélectionnez le serveur exécutant le connecteur.
3. Cliquez avec le bouton droit, puis sélectionnez **Properties** > **Délégation**.
4. Sélectionnez **N’approuver cet ordinateur que pour la délégation aux services spécifiés**. 
5. Sous **Services auxquels ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de l’identité du nom de principal du service (SPN) du serveur d’applications. Ceci permet au connecteur de proxy d’application d’emprunter l’identité des utilisateurs dans Active Directory pour les applications définies dans la liste.

   ![Capture d’écran de la fenêtre Propriétés du connecteur-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Le connecteur et le serveur d’application sont dans des domaines différents
1. Pour obtenir la liste des conditions préalables à l’utilisation de la délégation Kerberos contrainte entre domaines, consultez [Délégation Kerberos contrainte entre domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilisez la propriété `principalsallowedtodelegateto` du serveur de connecteur pour activer le proxy d’application à déléguer pour le serveur de connecteur. Le serveur d’application est `sharepointserviceaccount` et le serveur qui délègue est `connectormachineaccount`. Pour Windows 2012 R2, utilisez ce code comme exemple :

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

Le Sharepointserviceaccount peut être le compte de la machine SPS ou un compte de service dans lequel s’exécute le pool d’applications SPS.

## <a name="configure-single-sign-on"></a>Configurer l’authentification unique 
1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](application-proxy-publish-azure-portal.md). Veillez à sélectionner **Azure Active Directory** comme **méthode de préauthentification**.
2. Dès que votre application apparaît dans la liste des applications d’entreprise, sélectionnez-la, puis cliquez sur **Authentification unique**.
3. Définissez le mode d’authentification unique sur **Authentification Windows intégrée**.  
4. Entrez le **SPN d’application interne** du serveur d’applications. Dans cet exemple, le nom de principal du service pour notre application publiée est http/www.contoso.com. Ce SPN doit se trouver dans la liste des services auxquels le connecteur peut présenter des informations d’identification déléguées. 
5. Choisissez l’**Identité de connexion déléguée** pour le connecteur à utiliser pour le compte de vos utilisateurs. Pour plus d’informations, consultez [Utilisation d’identités cloud et locales différentes](#Working-with-different-on-premises-and-cloud-identities)

   ![Configuration avancée des applications](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>Authentification unique pour les applications non Windows
Le flux de délégation Kerberos dans le proxy d’application Azure AD démarre quand Azure AD authentifie l’utilisateur dans le cloud. Une fois que la demande est disponible localement, le connecteur du proxy d’application AD Azure émet un ticket Kerberos pour le compte de l’utilisateur en interagissant avec le répertoire Active Directory local. Ce processus est appelé délégation Kerberos contrainte (KCD). Au cours de la phase suivante, une demande est envoyée à l’application principale avec ce ticket Kerberos. Plusieurs protocoles définissent la manière d’envoyer ces demandes. La plupart des serveurs non Windows supposent qu’il s’agit de Negotiate/SPNego, qui est maintenant pris en charge sur le proxy d’application Azure AD.

Pour plus d’informations sur Kerberos, consultez la page [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Tout ce que vous voulez savoir sur Kerberos Constrained Delegation (KCD)).

Les applications non Windows utilisent en général les noms d’utilisateur ou les noms de compte SAM au lieu des adresses e-mail de domaine. Si cette situation s’applique à vos applications, vous devez configurer le champ d’identité de connexion déléguée pour connecter vos identités cloud à vos identités d’application. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Utilisation d’identités cloud et locales différentes
Le proxy d’application suppose que les utilisateurs ont exactement la même identité dans le cloud et localement. Dans le cas contraire, vous pouvez toujours utiliser KCD pour l’authentification unique. Configurez une **Identité de connexion déléguée** pour chaque application afin de spécifier l’identité qui doit être utilisée pendant l’exécution d’une authentification unique.  

Grâce à cette fonctionnalité, de nombreuses organisations disposant d’identités locales et cloud différentes peuvent mettre en œuvre l’authentification unique des applications cloud aux applications locales sans contraindre les utilisateurs à entrer des noms d’utilisateur et des mots de passe différents. Cela inclut les organisations qui :

* disposent de plusieurs domaines en interne (joe@us.contoso.com, joe@eu.contoso.com) et d’un domaine unique dans le cloud (joe@contoso.com) ;
* disposent d’un nom de domaine non routable en interne (joe@contoso.usa) et d’un nom de domaine légal dans le cloud ;
* n’utilisent pas de noms de domaine en interne (joe) ;
* utilisent différents alias localement et dans le cloud, Par exemple, joe-johns@contoso.com et joej@contoso.com  

Avec le proxy d’application, vous pouvez sélectionner l’identité à utiliser pour obtenir le ticket Kerberos. Ce paramètre est à configurer application par application. Certaines de ces options sont adaptées pour les systèmes qui n’acceptent pas le format d’adresse de messagerie, tandis que d’autres sont conçues pour les connexions alternatives.

![Capture d’écran du paramètre Identité de connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si l’identité de connexion déléguée est utilisée, il se peut que la valeur ne soit pas la même pour l’ensemble des domaines ou forêts de votre organisation. Vous pouvez éviter ce problème en publiant ces applications deux fois à l’aide de deux groupes de connecteurs différents. Dans la mesure où chaque application possède sa propre audience utilisateur, vous pouvez joindre ses connecteurs à un autre domaine.

### <a name="configure-sso-for-different-identities"></a>Configuration de l’authentification unique pour différentes identités
1. Configurez les paramètres Azure AD Connect de manière à ce que l’identité principale soit l’adresse de messagerie (courrier). Cette opération est effectuée dans le cadre du processus de personnalisation, en modifiant le champ **Nom d’utilisateur principal** dans les paramètres de synchronisation. Ces paramètres déterminent également comment les utilisateurs se connectent à Office 365, aux appareils Windows 10 et autres applications qui utilisent Azure AD comme magasin d’identités.  
   ![Capture d’écran de l’identification des utilisateurs : liste déroulante Nom d’utilisateur principal](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Dans les paramètres de configuration de l’application à modifier, sélectionnez l’ **Identité de connexion déléguée** à utiliser :

   * Nom d’utilisateur principal (par exemple, joe@contoso.com)
   * Nom d’utilisateur principal alternatif (par exemple, joed@contoso.local)
   * Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal (par exemple, joe)
   * Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal alternatif (par exemple, joed)
   * Nom du compte SAM local (en fonction de la configuration du contrôleur de domaine)

### <a name="troubleshooting-sso-for-different-identities"></a>Résolution des problèmes liés à l’authentification unique pour différentes identités
Si une erreur se produit dans le processus d’authentification unique, elle apparaît dans le journal des événements d’ordinateur du connecteur, comme expliqué dans la section [Résolution des problèmes](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Toutefois, dans certains cas, la demande est correctement envoyée à l’application principale, mais celle-ci répond dans plusieurs réponses HTTP. Pour résoudre ces cas, il faut tout d’abord examiner le numéro d’événement 24029 sur l’ordinateur connecteur dans le journal des événements de session du proxy d’application. L’identité de l’utilisateur qui a été utilisée pour la délégation s’affiche dans le champ « utilisateur » dans les détails de l’événement. Pour activer le journal de session, sélectionnez **Afficher les journaux d’analyse et de débogage** dans le menu Affichage de l’Observateur d’événements.

## <a name="next-steps"></a>Étapes suivantes

* [Comment configurer une application de proxy d’application pour utiliser la délégation Kerberos contrainte ?](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)


Pour les dernières nouvelles et mises à jour, consultez le site [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

