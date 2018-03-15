---
title: "Résolution des problèmes de configuration de délégation Kerberos contrainte pour le proxy d’application | Microsoft Docs"
description: "Résolvez les problèmes de configuration de délégation Kerberos contrainte pour le proxy d’application."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: a580b0afbd34623986ea8a3f60147a937c423e5e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Résolution des problèmes de configuration de délégation Kerberos contrainte pour le proxy d’application

Les méthodes permettant d’utiliser l’authentification SSO avec des applications publiées peuvent varier d’une application à une autre. L’une des options que le proxy d’application Azure offre par défaut est la délégation Kerberos contrainte (KCD). Vous pouvez configurer un connecteur pour effectuer une authentification Kerberos contrainte auprès des applications principales, pour le compte des utilisateurs.

La procédure d’activation de KCD est relativement simple. Il suffit généralement d’une compréhension générale des différents composants et du flux d’authentification permettant l’authentification SSO. Il peut être difficile de trouver de bonnes sources d’informations permettant de résoudre les scénarios où l’authentification SSO KCD ne fonctionne pas comme prévu.

Par conséquent, cet article a pour objet de fournir un point de référence unique qui peut aider à résoudre certains problèmes courants et permettre aux utilisateurs d’y remédier eux-mêmes. Il offre également des conseils supplémentaires expliquant comment diagnostiquer les implémentations les plus complexes et les plus problématiques.

Cet article repose sur les hypothèses suivantes :

-   Le déploiement du proxy d’application Azure est conforme à notre [documentation](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable), et l’accès général aux applications non-KCD fonctionne comme prévu.

-   L’application cible publiée est basée sur IIS et l’implémentation de Kerberos de Microsoft.

-   Les hôtes de serveur et d’application se trouvent dans un même domaine Active Directory. Vous trouverez des informations détaillées sur les scénarios inter-domaines et inter-forêts dans le [livre blanc sur KCD](https://aka.ms/KCDPaper).

-   L’application concernée est publiée dans un client Azure pour lequel la pré-authentification est activée. Les utilisateurs doivent s’authentifier sur Azure par le biais de l’authentification basée sur les formulaires. Les scénarios d’authentification cliente complète ne sont pas couverts par cet article. Ils seront ajoutés ultérieurement.

## <a name="prerequisites"></a>Prérequis

Le proxy d’application Azure peut être déployé sur de nombreux types d’environnement ou d’infrastructure. Les architectures varient naturellement d’une organisation à l’autre. Les causes les plus courantes de problèmes liés à KCD ne portent pas sur les environnements eux-mêmes, mais plutôt sur de simples erreurs de configuration ou des négligences d’ordre général.

Par conséquent, il est préférable de vérifier systématiquement que vous respectez toutes les conditions préalables figurant dans notre [article sur l’utilisation de l’authentification SSO KCD avec le proxy d’application](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) avant d’essayer de résoudre les problèmes.

Consultez notamment la section sur la configuration de KCD sur 2012 R2. En effet, il convient d’adopter une approche radicalement différente pour la configuration de KCD avec les versions précédentes de Windows, et ce, tout en tenant compte de plusieurs considérations :

-   Il n’est pas rare qu’un serveur membre de domaine ouvre une boîte de dialogue sur un canal sécurisé avec un contrôleur de domaine spécifique, puis passe à une autre boîte de dialogue sans prévenir. Ainsi, les hôtes de connecteur ne doivent pas être limités à une communication avec des contrôleurs de domaine spécifique du site local.

-   De même, les scénarios inter-domaines s’appuient sur des références qui dirigent un hôte de connecteur vers des contrôleurs de domaine pouvant résider en dehors du périmètre du réseau local. Dans ce scénario, il est tout aussi important de vous assurer que vous autorisez également le trafic vers les contrôleurs de domaine qui représentent d’autres domaines respectifs. Sinon, la délégation échouera.

-   Dans la mesure du possible, vous devez éviter de placer des appareils IPS/IDS actifs entre les hôtes de connecteur et les contrôleurs de domaine. En effet, ceux-ci sont parfois trop intrusifs et interfèrent avec le trafic RPC de base.

Vous devez tester la délégation dans les scénarios les plus simples. Plus vous introduisez de variables, plus augmentez le nombre d’éléments à prendre en compte. Par exemple, en limitant vos tests à un seul connecteur, vous gagnerez un temps précieux. Vous pourrez ajouter d’autres connecteurs une fois les problèmes résolus.

Certains facteurs environnementaux peuvent également contribuer à un problème. Dans la mesure du possible, essayez de réduire l’architecture au strict minimum pour les tests. Par exemple, les listes ACL de pare-feu internes ne sont pas toujours bien configurées. Ainsi, si cela est possible, autorisez tout le trafic issu d’un connecteur à transiter directement vers les contrôleurs de domaine et l’application principale. 

En fait, les connecteurs doivent être positionnés le plus près possible de leurs cibles. Il s’agit là de leur meilleur emplacement. La mise en place d’un pare-feu en ligne lors des tests ne fait qu’ajouter une complexité inutile et peut prolonger vos recherches.

Alors quels aspects caractérisent effectivement un problème avec KCD ? On peut observer plusieurs indications classiques d’échec de l’authentification SSO KCD. Les premiers signes d’un problème se manifestent généralement dans le navigateur.

   ![Erreur de configuration incorrecte de KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Échec de l’autorisation en raison d’autorisations manquantes](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Ces erreurs entraînent toutes le même symptôme, à savoir l’échec de l’authentification SSO et, par conséquent, le refus de l’accès de l’utilisateur à l’application.

## <a name="troubleshooting"></a>Résolution de problèmes

Le mode de résolution varie selon le problème et les symptômes observés. Avant d’aller plus loin, consultez les liens suivants, qui offrent des informations utiles que vous ne connaissez peut-être pas déjà :

-   [Résoudre les problèmes de proxy d’application et les messages d’erreur](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Erreurs Kerberos](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Utilisation de l’authentification unique quand des identités locales et sur le cloud ne sont pas identiques](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Si vous en arrivez là, vous avez certainement affaire à un problème majeur. Commencez par séparer le flux en trois étapes distinctes pour résoudre chaque aspect.

**Pré-authentification du client** : l’utilisateur externe s’authentifiant sur Azure par le biais d’un navigateur.

L’authentification SSO KDC ne peut fonctionner que si la pré-authentification sur Azure est possible. Vous devez vérifier ce point et résoudre les problèmes éventuels. L’étape de pré-authentification n’a aucun lien avec KCD ou l’application publiée. Il doit être relativement simple de corriger les éventuelles incohérences en effectuant un contrôle de validité pour vérifier que le compte concerné existe dans Azure et qu’il n’est pas désactivé ou bloqué. Le message d’erreur dans le navigateur est généralement suffisamment descriptif pour permettre l’identification de la cause. Vous pouvez également consulter nos autres documents sur la résolution des problèmes en cas de doute.

**Service de délégation** : le connecteur de proxy Azure qui obtient un ticket de service Kerberos à partir d’un centre de distribution Kerberos (KDC, Kerberos Distribution Center) pour le compte d’utilisateurs.

Les communications externes entre le client et le serveur frontal Azure ne devraient avoir aucune incidence sur KCD. Elles doivent uniquement permettre de vérifier qu’il fonctionne. Ainsi, un identifiant utilisateur valide, qui sera utilisé pour l’obtention d’un ticket Kerberos, peut être fourni au service Azure Proxy. Sans cela, la délégation KCD n’est pas possible et échouera.

Comme mentionné précédemment, les messages d’erreur du navigateur fournissent généralement de bonnes indications sur les raisons d’un échec. Veillez à noter l’ID d’activité et l’horodatage fournis dans la réponse, car ils vous permettent de mettre en corrélation le comportement et les événements réels figurant dans le journal des événements Azure Proxy.

   ![Erreur de configuration incorrecte de KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Les entrées correspondantes du journal des événements apparaîtront comme événement 13019 ou 12027. Vous trouverez les journaux des événements des connecteurs sous **Journaux des applications et des services** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connecteur**&gt;**Admin**.

   ![Événement 13019 du journal des événements du proxy d’application](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Événement 12027 du journal des événements du proxy d’application](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Utilisez un enregistrement A dans votre DNS interne pour l’adresse de l’application et non un enregistrement CNAME.

-   Vérifiez de nouveau que l’hôte de connecteur a obtenu les droits nécessaires pour déléguer au SPN du compte cible désigné et que l’option **Utiliser tout protocole d’authentification** est sélectionnée. Pour plus d’informations, consultez [l’article sur la configuration de l’authentification unique](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Vérifiez qu’il n’existe qu’une seule instance du SPN dans Active Directory en émettant une commande `setspn -x` à partir d’une invite de commandes sur n’importe quel hôte membre du domaine

-   Vérifiez si une stratégie de domaine est appliquée pour limiter la [taille maximale des jetons Kerberos émis](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/) car cela empêche le connecteur d’obtenir un jeton si la taille est excessive

Pour obtenir d’autres informations de base sur les problèmes rencontrés, l’étape suivante consisterait à assurer un suivi réseau capturant les échanges entre l’hôte de connecteur et un KDC du domaine. Pour plus d’informations, consultez le [document sur la résolution approfondie des problèmes](https://aka.ms/proxytshootpaper).

Si la création de tickets semble correcte, un événement doit figurer dans les journaux, indiquant que l’authentification a échoué en raison du renvoi d’une erreur 401 par l’application. Cela indique généralement que l’application cible rejette votre ticket. Dans ce cas, passez à l’étape suivante :

**Application cible** : le consommateur du ticket Kerberos fourni par le connecteur

À ce stade, le connecteur doit avoir envoyé un ticket de service Kerberos au serveur principal, en tant qu’en-tête de la première demande de l’application.

-   À l’aide de l’URL interne de l’application défini dans le portail, vérifiez que l’application est directement accessible à partir du navigateur sur l’hôte de connecteur. Vous pouvez alors vous connecter. Pour plus d’informations à ce sujet, reportez-vous à la page sur la résolution des problèmes de connecteur.

-   Toujours sur l’hôte de connecteur, vérifiez que l’authentification entre le navigateur et l’application se fait à l’aide de Kerberos de l’une des façons suivantes :

1.  Exécutez les outils de développement (**F12**) dans Internet Explorer, ou utilisez [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) à partir de l’hôte de connecteur. Accédez à l’application à l’aide de l’URL interne et examinez les en-têtes d’autorisation WWW retournés dans la réponse de l’application pour vous assurer que Negotiate ou Kerberos est mentionné. Un blob Kerberos retourné à la suite dans la réponse du navigateur à l’application commence généralement par **YII**. Il s’agit d’une bonne indication de l’intervention de Kerberos. En revanche, NTLM commence toujours par **TlRMTVNTUAAB**, soit NTLMSSP lors d’un décodage à partir de Base64. Si **TlRMTVNTUAAB** figure au début du blob, cela indique que Kerberos n’est **pas** disponible. Dans le cas contraire, Kerberos est probablement disponible.
    > [!NOTE]
    > Si vous utilisez Fiddler, cette méthode nécessite de désactiver temporairement la protection étendue dans la configuration de l’application dans IIS.

     ![Fenêtre de contrôle de réseau du navigateur](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Illustration :* dans cet exemple, comme le blob ne commence pas par TIRMTVNTUAAB, Kerberos est disponible. Cet exemple montre un blob Kerberos qui ne commence pas par YII.

2.  Supprimez temporairement NTLM de la liste de fournisseurs sur le site IIS, et accédez à l’application directement à partir d’Internet Explorer sur l’hôte de connecteur. NTLM ne figurant plus dans la liste des fournisseurs, vous devez être en mesure d’accéder à l’application uniquement à l’aide de Kerberos. Si l’accès échoue, cela indique un problème avec la configuration de l’application. L’authentification Kerberos ne fonctionne donc pas.

Si Kerberos n’est pas disponible, vérifiez les paramètres d’authentification de l’application dans IIS pour vous assurer que Negotiate apparaît tout en haut avec NTLM juste en dessous. (Non-Negotiate : Kerberos ou Negotiate : PKU2U). Continuez uniquement si Kerberos est fonctionnel.

   ![Fournisseurs d’authentification Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Kerberos et NTLM étant en place, nous allons désactiver temporairement la pré-authentification pour l’application dans le portail. Essayez d’y accéder sur Internet à l’aide de l’URL externe. Vous devez être invité à vous authentifier et devez être en mesure de le faire avec le même compte que celui utilisé à l’étape précédente. Si ce n’est pas le cas, cela indique un problème avec l’application principale et non avec KCD.

-   Maintenant, réactivez la pré-authentification dans le portail et authentifiez-vous sur Azure en essayant de vous connecter à l’application à l’aide de son URL externe. Si l’authentification SSO a échoué, vous devez voir un message d’interdiction dans le navigateur, ainsi que l’événement 13022 dans le journal :

    *Le connecteur de proxy d’application Microsoft AAD ne peut pas authentifier l’utilisateur, car le serveur principal répond aux tentatives d’authentification Kerberos avec une erreur HTTP 401.*

    ![Message d’interdiction HTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Vérifiez l’application IIS pour vous assurer que le pool d’applications est configuré pour utiliser le même compte que le celui configuré pour le SPN dans Active Directory, comme dans l’illustration suivante

    ![Fenêtre de configuration d’application IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Une fois que vous connaissez l’identité, émettez la commande suivante à partir d’une invite de commandes pour vous assurer que ce compte est bien configuré avec le SPN en question. Par exemple, `setspn –q http/spn.wacketywack.com`

    ![Fenêtre de commandes SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Vérifiez que le SPN défini par rapport aux paramètres de l’application dans le portail est le même que celui configuré pour le compte AD cible utilisé par le pool d’applications de l’application

   ![Configuration du SPN dans le portail Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Accédez à IIS et sélectionnez l’option **Éditeur de configuration** pour l’application. Puis, accédez à **system.webServer/security/authentication/windowsAuthentication** pour vous assurer que **UseAppPoolCredentials** a la valeur **True**

   ![Option de configuration IIS - informations d’identification des pools d’applications](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Après la redéfinition de cette valeur sur **Vrai**, tous les tickets Kerberos mis en cache doivent être supprimés du serveur principal. Vous pouvez effectuer cette opération en exécutant la commande suivante :

```powershell
Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
``` 

Pour plus d’informations, consultez l’article [Purge the Kerberos client ticket cache for all sessions (Purger le cache des tickets de client Kerberos pour toutes les sessions)](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Garder le mode noyau activé s’avère utile pour améliorer les performances des opérations Kerberos. Cependant, dans ce cas, le ticket pour le service demandé sera déchiffré à l’aide du compte d’ordinateur. On parle également de système local. Ainsi, en cas de définition sur true, KCD est arrêté lorsque l’application est hébergée sur plusieurs serveurs au sein d’une batterie de serveurs.

-   Pour effectuer une vérification supplémentaire, vous pouvez également désactiver la **protection étendue**. Dans certains scénarios où elle est activée dans des configurations spécifiques, on a pu observer l’arrêt de KCD lorsqu’une application est publiée en tant que sous-dossier du site web par défaut. Celui-ci est configuré pour une authentification anonyme uniquement. Ainsi, les boîtes de dialogue sont totalement grisées, suggérant que les objets enfants n’hériteront d’aucun paramètre actif. Cependant, dans la mesure du possible, nous recommandons de l’activer systématiquement. Donc, quels que soient vos modes de test, n’oubliez pas de la réactiver.

Ces vérifications supplémentaires devraient vous mettre sur la bonne voie pour commencer à utiliser votre application publiée. Vous pouvez continuer et ajouter d’autres connecteurs également configurés pour la délégation, mais si les choses n’avancent pas, nous vous suggérons la lecture de notre [guide complet pour résoudre les problèmes de proxy d’application Azure AD](https://aka.ms/proxytshootpaper), qui vous offrira une présentation technique plus approfondie.

Si vous ne progressez toujours pas dans la résolution de votre problème, adressez-vous au support. L’équipe sera ravie de vous assister et de vous accompagner. Créez un ticket de support directement dans le portail. (Un ingénieur prendra contact avec vous.)

## <a name="other-scenarios"></a>Autres scénarios

-   Le proxy d’application Azure demande un ticket Kerberos avant d’envoyer sa demande à une application. Certaines applications tierces comme Tableau Server ne privilégient pas cette méthode d’authentification et attendent des négociations plus classiques. La première demande est anonyme, permettant à l’application de répondre avec les types d’authentification qu’elle prend en charge avec une erreur 401.

-   Authentification à deux tronçons : couramment utilisée dans les scénarios où une application est hiérarchisée, avec un serveur principal et un serveur frontal requérant tous deux une authentification comme les services SQL Reporting.

## <a name="next-steps"></a>Étapes suivantes
[Configurer la délégation Kerberos contrainte (KCD) sur un domaine géré](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
