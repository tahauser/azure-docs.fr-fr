---
title: "Gérer l’accès aux applications cloud par la restriction des clients - Azure | Microsoft Docs"
description: "Utilisation des Restrictions du client pour gérer les utilisateurs qui peuvent accéder aux applications en fonction de leur client Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: kgremban
ms.openlocfilehash: 63e0fa54433a60fe7384d21cf7d215cc8283afca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Utiliser les Restrictions du client pour gérer l’accès aux applications cloud SaaS

Les organisations de grande taille qui se préoccupent de la sécurité veulent adopter les services cloud, comme Office 365, tout en s’assurant que leurs utilisateurs ne pourront accéder qu’à des ressources approuvées. En général, pour gérer l’accès, les entreprises limitent les noms de domaine ou les adresses IP. Cette approche échoue dans un monde où les applications SaaS sont hébergées dans un cloud public et s’exécutent sur des noms de domaine partagés comme outlook.office.com et login.microsoftonline.com. Le fait de bloquer ces adresses empêche les utilisateurs d’accéder à Outlook sur le web entièrement, au lieu de restreindre simplement leur accès à des identités et des ressources approuvées.

La solution d’Azure Active Directory consiste en une fonctionnalité appelée Restrictions du client. Les Restrictions du client permettent aux organisations de contrôler l’accès aux applications cloud SaaS, en fonction du client Azure AD utilisé par les applications pour l’authentification unique. Par exemple, vous souhaitez peut-être autoriser l’accès aux applications Office 365 de votre organisation, tout en empêchant l’accès aux instances d’autres organisations de ces mêmes applications.  

Les Restrictions du client permettent aux organisations de spécifier la liste des clients auxquels leurs utilisateurs sont autorisés à accéder. Dans ce cas, Azure AD accorde uniquement l’accès à ces clients autorisés.

Cet article se concentre sur les Restrictions du client pour Office 365, mais cette fonctionnalité doit fonctionner avec n’importe quelle application cloud SaaS qui utilise des protocoles d’authentification moderne avec Azure AD pour l’authentification unique. Si vous utilisez des applications SaaS avec un client Azure AD différent du client utilisé par Office 365, assurez-vous que tous les clients nécessaires sont autorisés. Pour plus d’informations sur les applications cloud SaaS, consultez [Active Directory Marketplace](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Fonctionnement

La solution comprend les composants suivants : 

1. **Azure AD** : si le paramètre `Restrict-Access-To-Tenants: <permitted tenant list>` est présent, Azure AD émet uniquement des jetons de sécurité pour les clients autorisés. 

2. **Infrastructure du serveur proxy local** : un appareil proxy capable d’inspection SSL, configuré pour insérer l’en-tête contenant la liste des clients autorisés dans le trafic destiné à Azure AD. 

3. **Logiciel client** : pour prendre en charge les Restrictions du client, le logiciel client doit demander des jetons directement à Azure AD, afin que le trafic puisse être intercepté par l’infrastructure du proxy. Actuellement, les Restrictions du client sont prises en charge par les applications Office 365 basées sur un navigateur et par les clients Office en cas d’utilisation de l’authentification moderne (comme OAuth 2.0). 

4. **Authentification moderne** : les services cloud doivent utiliser l’authentification moderne pour utiliser les Restrictions du client et bloquer l’accès à tous les clients non autorisés. Les services cloud Office 365 doivent être configurés pour utiliser les protocoles d’authentification moderne par défaut. Pour plus d’informations sur la prise en charge de l’authentification moderne par Office 365, consultez [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Authentification moderne Office 365 mise à jour).

Le schéma suivant illustre le flux de trafic de niveau supérieur. L’inspection SSL est requise uniquement pour le trafic vers Azure AD, pas pour le trafic vers les services cloud Office 365. Cette distinction est importante car le volume de trafic pour l’authentification auprès d’Azure AD est généralement beaucoup plus faible que le volume de trafic vers les applications SaaS comme Exchange Online et SharePoint Online.

![Flux de trafic des Restrictions du client - Schéma](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurer les Restrictions du client

Deux étapes sont nécessaires pour bien démarrer avec les Restrictions du client. La première étape consiste à vous assurer que vos clients peuvent se connecter aux adresses correctes. La seconde étape consiste à configurer l’infrastructure de votre proxy.

### <a name="urls-and-ip-addresses"></a>URL et adresses IP

Pour utiliser les Restrictions du client, vos clients doivent être en mesure de se connecter aux URL AD Azure suivantes pour s’authentifier : login.microsoftonline.com, login.microsoft.com et login.windows.net. En outre, pour accéder à Office 365, vos clients doivent également être en mesure de se connecter aux noms de domaine complets/URL et adresses IP définis dans [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuration du proxy et conditions requises

La configuration suivante est nécessaire pour activer les Restrictions du client dans l’infrastructure de votre proxy. Ce guide est générique, donc consultez la documentation du fabricant de votre proxy pour obtenir les étapes d’implémentation spécifiques.

#### <a name="prerequisites"></a>Conditions préalables

- Le proxy doit être en mesure d’effectuer l’interception SSL, l’insertion d’en-tête HTTP et de filtrer les destinations à l’aide des noms de domaine complets/URL. 

- Les clients doivent approuver la chaîne de certificat présentée par le proxy pour les communications SSL. Par exemple, si des certificats d’une infrastructure à clé publique interne sont utilisés, le certificat d’autorité de certificat racine émetteur interne doit être approuvé.

- Cette fonctionnalité est incluse dans les abonnements Office 365, mais si vous souhaitez utiliser les Restrictions du client pour contrôler l’accès à d’autres applications SaaS, alors vous aurez besoin de licences Premium 1 Azure AD.

#### <a name="configuration"></a>Configuration

Pour chaque demande entrante sur login.microsoftonline.com, login.microsoft.com et login.windows.net, insérez deux en-têtes HTTP : *Restrict-Access-To-Tenants* et *Restrict-Access-Context*.

Les éléments suivants doivent être inclus dans les en-têtes : 
- Pour *Restrict-Access-To-Tenants*, une valeur de \<permitted tenant list\> (liste de clients autorisés), qui est une liste séparée par des virgules des clients auxquels vous souhaitez que les utilisateurs puissent accéder. N’importe quel domaine qui est inscrit auprès d’un client peut être utilisé pour identifier le client dans cette liste. Par exemple, pour autoriser l’accès aux clients Contoso et Fabrikam, la paire nom/valeur ressemble à :  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Pour *Restrict-Access-Context*, une valeur d’ID de répertoire unique, déclarant quel client définit les Restrictions du client. Par exemple, pour déclarer Contoso en tant que client qui définit la stratégie Restrictions du client, la paire nom/valeur ressemble à :  `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Vous trouverez votre ID de répertoire dans le [portail Azure](https://portal.azure.com). Connectez-vous en tant qu’administrateur, sélectionnez **Azure Active Directory**, puis sélectionnez **Propriétés**.

Pour empêcher les utilisateurs d’insérer leur propre en-tête HTTP avec des clients non approuvés, le proxy doit remplacer l’en-tête Restrict-Access-To-Tenants si celui-ci est déjà présent dans la demande entrante. 

Les clients doivent être forcés à utiliser le proxy pour toutes les demandes à login.microsoftonline.com, login.microsoft.com et login.windows.net. Par exemple, si des fichiers PAC sont utilisés pour indiquer aux clients d’utiliser le proxy, les utilisateurs finaux ne doivent pas être en mesure de modifier ou de désactiver les fichiers PAC.

## <a name="the-user-experience"></a>Expérience utilisateur

Cette section présente l’expérience des utilisateurs finaux et des administrateurs.

### <a name="end-user-experience"></a>Expérience de l’utilisateur final

Par exemple, un utilisateur sur le réseau Contoso tente d’accéder à l’instance Fabrikam d’une application SaaS partagée comme Outlook Online. Si Contoso est un client non autorisé pour cette instance, la page suivante s’affiche pour l’utilisateur :

![Page Accès refusé pour les utilisateurs dans des clients non autorisés](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Expérience administrateur

Bien que la configuration des Restrictions du client est effectuée sur l’infrastructure du proxy d’entreprise, les administrateurs peuvent accéder aux rapports sur les Restrictions du client directement dans le portail Azure. Pour afficher les rapports, accédez à la page de présentation Azure Active Directory, puis à « Autres fonctionnalités ».

L’administrateur du client spécifié en tant que client Restricted-Access-Context peut utiliser ce rapport pour afficher toutes les connexions bloquées en raison de la stratégie Restrictions du client, notamment l’identité utilisée et l’ID du répertoire cible.

![Utiliser le portail Azure pour afficher les tentatives de connexion limitées](./media/active-directory-tenant-restrictions/portal-report.png)

Comme pour les autres rapports dans le portail Azure, vous pouvez utiliser des filtres pour spécifier l’étendue de votre rapport. Vous pouvez filtrer par utilisateur, application, client ou intervalle de temps spécifique.

## <a name="office-365-support"></a>Prise en charge d’Office 365

Les applications Office 365 doivent répondre à deux critères pour prendre pleinement en charge les Restrictions du client :

1. Le client utilisé prend en charge l’authentification moderne
2. L’authentification moderne est activée comme protocole d’authentification par défaut pour le service cloud.

Consultez [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Authentification moderne Office 365 mise à jour) pour plus d’informations sur les clients Office qui prennent actuellement en charge l’authentification moderne. Cette page inclut également des liens vers des instructions relatives à l’activation de l’authentification moderne sur des clients Exchange Online et Skype Entreprise Online spécifiques. L’authentification moderne est déjà activée par défaut dans SharePoint Online.

Les Restrictions du client sont actuellement prises en charge par les applications Office 365 basées sur un navigateur (Portail Office, Yammer, sites SharePoint, Outlook sur le web, etc.). Pour les clients lourds (Outlook, Skype Entreprise, Word, Excel, PowerPoint, etc.) Les Restrictions du client peuvent uniquement être appliquées si l’authentification moderne est utilisée.  

Les clients Outlook et Skype Entreprise qui prennent en charge l’authentification moderne peuvent continuer à utiliser les protocoles hérités sur les clients où l’authentification moderne n’est pas activée, contournant ainsi les Restrictions du client. Pour Outlook sur Windows, les clients peuvent choisir d’implémenter des restrictions qui empêchent les utilisateurs finaux d’ajouter des comptes de messagerie non approuvés à leurs profils. Par exemple, consultez le paramètre de stratégie de groupe [Empêcher l’ajout de comptes Exchange personnalisés](http://gpsearch.azurewebsites.net/default.aspx?ref=1). Pour Outlook sur les plateformes non Windows et pour Skype Entreprise sur toutes les plateformes, la prise en charge complète des Restrictions du client n’est actuellement pas disponible.

## <a name="testing"></a>Test

Si vous souhaitez tester la fonctionnalité Restrictions du client avant de l’implémenter pour l’ensemble de votre organisation, il existe deux options : une approche basée sur hôte à l’aide d’un outil comme Fiddler ou un déploiement par étapes des paramètres du proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pour une approche basée sur hôte

Fiddler est un proxy de débogage web gratuit qui peut être utilisé pour capturer et modifier le trafic HTTP/HTTPS, notamment l’insertion d’en-têtes HTTP. Pour configurer Fiddler afin de tester les Restrictions du client, procédez comme suit :

1.  [Téléchargez et installez Fiddler](http://www.telerik.com/fiddler).
2.  Configurez Fiddler pour déchiffrer le trafic HTTPS, conformément à [la documentation d’aide de Fiddler](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Configurez Fiddler pour insérer les en-têtes *Restrict-Access-To-Tenants* et *Restrict-Access-Context* à l’aide de règles personnalisées :
  1. Dans l’outil débogueur web Fiddler, sélectionnez le menu **Règles** et sélectionnez **Personnaliser les règles...** pour ouvrir le fichier CustomRules.
  2. Ajoutez les lignes suivantes au début de la fonction *OnBeforeRequest*. Remplacez le \<domaine du client\> par un domaine enregistré auprès de votre client, par exemple, contoso.onmicrosoft.com. Remplacez \<l’ID de répertoire\> par l’identificateur GUID Azure AD de votre client.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Si vous avez besoin d’autoriser plusieurs clients, utilisez une virgule pour séparer les noms des clients. Par exemple :

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Enregistrez et fermez le fichier CustomRules.

Après avoir configuré Fiddler, vous pouvez capturer le trafic en accédant au menu **Fichier** et en sélectionnant **Capturer le trafic**.

### <a name="staged-rollout-of-proxy-settings"></a>Déploiement par étapes des paramètres du proxy

En fonction des capacités de votre infrastructure de proxy, vous pourriez être en mesure d’étalonner le déploiement des paramètres pour vos utilisateurs. Voici deux options principales à prendre en compte :

1.  Utilisez des fichiers PAC pour pointer les utilisateurs test vers une infrastructure de proxy test, tandis que les utilisateurs normaux continuent à utiliser l’infrastructure du proxy de production.
2.  Certains serveurs proxy peuvent prendre en charge des configurations différentes à l’aide de groupes.

Consultez la documentation de votre serveur proxy pour obtenir des informations spécifiques.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’authentification moderne Office 365 mise à jour](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Consultez les [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
