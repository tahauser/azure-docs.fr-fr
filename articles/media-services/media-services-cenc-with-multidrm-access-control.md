---
title: "CENC avec Multi-DRM et contrôle d’accès : une conception de référence et l’application sur Windows Azure et Azure Media Services | Microsoft Docs"
description: "En savoir plus sur la licence du kit de portage Smooth Streaming client Microsoft®."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 50bcb71cd4f52386e9ea428fc124ac30ae9a862b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC avec Multi-DRM et contrôle d’accès : une conception de référence et l’application sur Windows Azure et Azure Media Services
 
## <a name="introduction"></a>Introduction
Il est bien connu que la conception et la mise en place d’un sous-système DRM pour une OTT ou une solution de diffusion en continu en ligne est une tâche complexe. Et il est très courant que les fournisseurs/opérateurs de vidéo en ligne externalisent cette partie aux fournisseurs de services spécialisés DRM. L’objectif de ce document est de présenter la conception et l’implémentation d’un sous-système DRM de bout en bout dans OTT ou une solution de diffusion en ligne en continu de référence.

Les lecteurs ciblés de ce document sont des ingénieurs travaillant dans un sous-système DRM d’OTT ou des solutions de diffusion en ligne/à plusieurs écrans, ou tous les lecteurs intéressés par les sous-systèmes de gestion des droits numériques. L’hypothèse est que les lecteurs sont familiers avec au moins une des technologies DRM sur le marché, notamment PlayReady, Widevine, FairPlay ou Adobe Access.

Par DRM, nous entendons également CENC (chiffrement commun) avec multi-DRM. Une tendance majeure de l’industrie de la diffusion en continu en ligne et OTT consiste à utiliser CENC avec multi-DRM natif sur plusieurs plateformes clientes, ce qui représente une évolution par rapport à la tendance précédente, qui consistait à utiliser un seul DRM et son kit de développement logiciel client pour différentes plateformes clientes. Lors de l’utilisation de CENC avec DRM multi natif, PlayReady et Windevine sont chiffrés conformément à la spécification [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Les avantages de CENC avec multi-DRM sont les suivants :

1. réduction des frais de chiffrement dans la mesure où un seul traitement de chiffrement est utilisé, ciblant différentes plateformes avec ses DRM natifs ;
2. réduction du coût de la gestion des ressources chiffrées, car une seule copie de ces dernières est nécessaire ;
3. élimination les coûts de licence de client DRM natif, car ce dernier est généralement proposé gratuitement sur sa plate-forme d’origine.

Microsoft a joué le rôle de promoteur actif de DASH et de CENC, tout comme d’autres acteurs majeurs du secteur. Microsoft Azure Media Services offre la prise en charge de DASH et CENC. Pour les dernières annonces, consultez les blogs de Mingfei : [Annonce d’une distribution de licence Google Widevine dans Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) et [Azure Media Services ajoute un package Gooble Windevine pour la distribution de flux multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Présentation de cet article
L’objectif de cet article inclut les éléments suivants :

1. fourniture d’une conception de référence du sous-système de gestion des droits numériques avec CENC multi-DRM ;
2. fourniture d’une implémentation de référence sur la plateforme Microsoft Azure/Azure Media Services ;
3. Commentaires de certaines rubriques relatives à la conception et l’implémentation.

Dans cet article, « multi DRM » aborde les thèmes suivants :

1. Microsoft PlayReady
2. Google Widevine
3. Apple FairPlay 

Le tableau suivant résume l’application de la plateforme/application native et les navigateurs pris en charge pour chaque DRM.

| **Plateforme cliente** | **Prise en charge native de DRM** | **Navigateur/App** | **Formats de diffusion en continu** |
| --- | --- | --- | --- |
| **Téléviseurs intelligents, récepteurs d’opérateur, récepteurs OTT** |PlayReady principalement, et/ou Widevine, et/ou autres |Linux, Opera, WebKit, autre |Divers formats |
| **Appareils Windows 10 (PC Windows, tablettes Windows, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |DASH (pour HLS, PlayReady n’est pas pris en charge)<br/><br/>DASH, Smooth Streaming (pour HLS, PlayReady n’est pas pris en charge) |
| **Appareils Android (téléphone, tablette, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), clients OS X et Apple TV** |FairPlay |Safari 8+/EME |HLS |


Compte tenu de l'état actuel du déploiement de chaque DRM, un service mettra généralement en œuvre 2 ou 3 DRM pour s’assurer que vous gérez tous les types de points de terminaison de façon optimale.

Il existe un compromis entre la complexité de la logique du service et la complexité côté client pour atteindre un certain niveau d'expérience utilisateur sur les différents clients.

Pour effectuer votre sélection, prenez en compte les faits suivants :

* PlayReady est implémenté en mode natif dans tous les appareils Windows, sur certains appareils Android, et est disponible via les kits de développement logiciel (SDK) sur pratiquement n'importe quelle plate-forme
* Widevine est implémenté en mode natif dans chaque appareil Android, dans Chrome, et dans certains autres appareils
* FairPlay est disponible uniquement sur iOS et les clients Mac OS ou via iTunes.

Par conséquent, une configuration multi-DRM standard serait :

* Option 1 : PlayReady et Widevine
* Option 2 : PlayReady, Widevine et FairPlay

## <a name="a-reference-design"></a>Une conception de référence
Dans cette section, nous présenterons une conception de référence indépendante des technologies utilisées pour leur mise en œuvre.

Un sous-système DRM peut contenir les éléments suivants :

1. Gestion des clés
2. Packaging DRM
3. Remise de licence DRM
4. Vérification des droits
5. Authentification/autorisation
6. Lecteur
7. Origine/CDN

Le diagramme suivant illustre l’interaction entre les composants d’un sous-système DRM.

![Sous-système de gestion des droits numériques avec CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

La conception compte trois couches de base :

1. Couche back-office (en noir) qui n’est pas présentée à l’extérieur ;
2. Couche « DMZ » (en bleu) contenant tous les points de terminaison exposés au public ;
3. Couche Internet Public (en bleu clair) contenant les réseaux de diffusion en continu et des lecteurs avec le trafic transitant par l’Internet public.

Il doit exister un outil de gestion de contenu pour le contrôle de la protection DRM, que le chiffrement soit statique ou dynamique. Les entrées de chiffrement de la gestion des droits numériques doivent inclure :

1. le contenu vidéo MBR ;
2. la clé de contenu ;
3. URL d’acquisition de licence.

Pendant la lecture, le flux de haut niveau suit le déroulement suivant :

1. l’utilisateur est authentifié ;
2. un jeton d’autorisation est créé pour l’utilisateur ;
3. le contenu protégé par DRM (manifeste) est téléchargé sur le lecteur ;
4. le joueur soumet la demande d’acquisition de licence pour les serveurs de licence avec l’ID de clé et le jeton d’autorisation.

Avant de passer à la rubrique suivante, quelques mots sur la conception de la gestion de clés.

| **ContentKey vers ressources** | **Scénario** |
| --- | --- |
| 1 à 1 |Le cas le plus simple. Il offre le meilleur contrôle. Il génère en général le coût de licence le plus élevé. Chaque ressource protégée nécessite au moins une licence. |
| 1 à plusieurs |Vous pouvez utiliser la même clé de contenu pour plusieurs éléments multimédias. Par exemple, pour tous les éléments multimédias d’un groupe logique comme un genre ou un sous-ensemble de genre (ou séquence génétique), vous pouvez utiliser une clé de contenu unique. |
| Plusieurs à 1 |Plusieurs clés de contenu sont nécessaires pour chaque élément multimédia. <br/><br/>Par exemple, si vous devez appliquer la protection dynamique CENC avec multi-DRM à MPEG-DASH et le chiffrement AES-128 pour TLS, vous avez besoin de deux clés de contenu distinctes, chacune avec son propre ContentKeyType. (Concernant la clé de contenu utilisée pour la protection CENC dynamique, ContentKeyType.CommonEncryption doit être utilisée. Pour la clé de contenu servant au chiffrement dynamique AES-128, c’est la clé ContentKeyType.EnvelopeEncryption qui doit être utilisée.)<br/><br/>Autre exemple, pour la protection CENC du contenu DASH, en théorie, une clé de contenu peut être utilisée pour protéger les flux vidéo et une autre clé de contenu pour protéger des flux audio. |
| Plusieurs à plusieurs |Combinaison des deux scénarios ci-dessus : un jeu de clés de contenu est utilisé pour chacun des éléments multimédia du même « groupe ». |

Autre facteur important à prendre en compte : l’utilisation des licences persistantes et non persistantes.

Pourquoi ces considérations sont-elles importantes ?

Elles ont un impact direct sur le coût de distribution de la licence si vous utilisez le cloud public pour la distribution de licence. Prenons deux cas différents pour illustrer le cas :

1. Abonnement mensuel : utilisation d’une licence persistante et d’un mappage clé à ressource 1 à plusieurs. Par exemple, pour tous les films pour enfant, nous utilisons une clé de contenu unique pour le chiffrement. Dans ce cas :

    Nombre total de licences requis pour tous les films/appareils = 1
2. Abonnement mensuel : utilisation d’une licence non persistante et du mappage 1 à 1 entre la clé de contenu et la ressource de contenu. Dans ce cas :

    Nombre total de licences requis pour tous les films/appareils = [nombre de films # regardés] x [# de séances]

Comme vous pouvez le voir, ces deux conceptions différentes débouchent sur des modèles de demande de licence et, donc des frais de distribution de licence très différents si cette dernière est fournie par le cloud public (par exemple, Azure Media Services).

## <a name="mapping-design-to-technology-for-implementation"></a>Correspondance entre conception et technologie pour la mise en œuvre
Nous allons ensuite associer notre conception générique aux technologies de plate-forme Microsoft Azure/Azure Media Services, en spécifiant la technologie à utiliser pour chaque bloc de construction.

La table qui suit affiche le mappage :

| **Bloc de construction** | **Technology** |
| --- | --- |
| **Lecteur** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fournisseur d’identité (IDP)** |Azure Active Directory |
| **Secure Token Service (Service d’émission de jeton de sécurité - STS)** |Azure Active Directory |
| **Flux de travail de Protection DRM** |Protection dynamique Azure Media Services |
| **Distribution de licence DRM** |1. Distribution de licence Azure Media Services (PlayReady, Widevine, FairPlay) ou <br/>2. Serveur de licences de Axinom, <br/>3. Serveur de licences PlayReady personnalisé |
| **Origine** |Point de terminaison des Services de diffusion multimédia en continu Azure |
| **Gestion des clés** |Inutile pour l’implémentation de référence |
| **Gestion de contenu** |Une application de console C# |

En d’autres termes, le fournisseur d’identité (IDP) et du STS (Service d’émission de jeton de sécurité - Secure Token Services) sera AD Azure. Nous utiliserons l’ [API de lecteur Azure Media](http://amp.azure.net/libs/amp/latest/docs/)comme lecteur. Azure Media Services et le lecteur Azure Media prend en charge DASH et CENC avec multi-DRM.

Le diagramme suivant affiche la structure et le flux d’ensemble avec le mappage technologique.

![CENC sur AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Pour configurer le chiffrement dynamique de CENC, l’outil de gestion de contenu utilise les entrées suivantes :

1. contenu ouvert ;
2. clé de contenu issue de la génération/la gestion de clé ;
3. URL d’acquisition de licence ;
4. liste d’informations fournie par Azure AD.

Le résultat de l’outil de gestion de contenu sera :

1. ContentKeyAuthorizationPolicy, contenant la spécification de la distribution de licence vérifie un jeton JWT et les spécifications de licence DRM ;
2. AssetDeliveryPolicy contient des spécifications au format streaming, la protection DRM et les URL d’acquisition de licence.

Lors de l’exécution, le flux se présente comme suit :

1. au moment de l’authentification de l’utilisateur, un jeton JWT est généré ;
2. L’une des revendications contenues dans le jeton JWT est une revendication de type « groupes » qui contient l’ID d’objet de groupe de « EntitledUserGroup ». Cette revendication sera utilisée pour le réussir la « vérification des droits ».
3. Le lecteur charge le manifeste client d’un contenu protégé CENC et « voit » le texte suivant :

   1. ID de clé,
   2. le contenu est protégé par CENC
   3. URL d’acquisition de licence.
4. Le lecteur effectue une demande d’acquisition de licence basée sur le navigateur/DRM pris en charge. Dans la demande d’acquisition de clé, l’ID de clé et le jeton JWT sont eux aussi envoyés. Le service de distribution de licence vérifie le jeton JWT et les revendications contenues avant la délivrance de la licence requise.

## <a name="implementation"></a>Implémentation
### <a name="implementation-procedures"></a>Procédures de mise en œuvre
La mise en œuvre comprend les étapes suivantes :

1. Préparer des ressources de test : coder/regrouper une vidéo de test dans un MP4 fragmenté à plusieurs vitesses dans Azure Media Services. Cet actif n’est pas protégé par DRM. La protection DRM sera assurée par le biais d’une protection dynamique ultérieurement.
2. Créez l’ID de clé et une clé de contenu (éventuellement à partir d’une amorce de clé). Dans le cas présent, le système de gestion de clés n’est pas nécessaire dans la mesure où nous avons affaire à un seul ensemble d’ID de clés et de clé contenu pour plusieurs ressources de test ;
3. utilisez l’API AMS pour configurer les services de distribution de licence multi-DRM pour l’élément de test. Si vous utilisez des serveurs de licence personnalisés par le biais de votre société ou les fournisseurs de votre société et non les services de licence d’Azure Media Services, vous pouvez ignorer cette étape et spécifier les URL d’acquisition de licence lors de l’étape de configuration de la remise de la licence. L’API AMS sert à spécifier certaines configurations détaillées, comme la restriction de la stratégie d’autorisation, les modèles de réponse de licence des différents services de licence DRM, etc. À ce stade, le portail Azure ne fournit pas encore l’interface utilisateur nécessaire pour cette configuration. Vous trouverez des informations sur le niveau API et un exemple de code dans l’article suivant : [Utilisation du chiffrement commun dynamique PlayReady et/ou Widevine](media-services-protect-with-playready-widevine.md).
4. Utilisez l’API AMS pour configurer une stratégie de distribution des éléments multimédia pour le test des éléments multimédia. Vous trouverez des informations sur le niveau API et un exemple de code dans l’article suivant : [Utilisation du chiffrement commun dynamique PlayReady et/ou Widevine](media-services-protect-with-playready-widevine.md).
5. créer et modifier un client Azure Active Directory dans Azure ;
6. créer plusieurs comptes d’utilisateurs et groupes dans votre client Azure Active Directory : vous devez créer au moins un groupe « EntitledUser » et ajouter un utilisateur à ce groupe. Les utilisateurs de ce groupe qui réussiront la vérification des droits lors de l’acquisition de licence et les utilisateurs n’appartenant pas à ce groupe échoueront au contrôle d’authentification et ne pourront pas acquérir de licence. L’appartenance à un groupe « EntitledUser » est une revendication de « groupes » du jeton JWT délivré par AD Azure. Cette exigence de revendication doit être spécifiée pendant l’opération de configuration de services de distribution de licence multi-DRM.
7. Créez une application ASP.NET MVC qui hébergera votre lecteur vidéo. Cette application ASP.NET sera protégée par le biais de l’authentification des utilisateurs par rapport au client Azure Active Directory. Des revendications adéquates seront incluses dans les jetons d’accès obtenus après authentification de l’utilisateur. L’API OpenID Connect est recommandée pour cette étape. Vous devez installer les packages NuGet suivants :
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. Créer un lecteur à l’aide d’ [API Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [API ProtectionInfo d’Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) vous permet de spécifier la technologie DRM à utiliser sur une autre plate-forme DRM.
9. Matrice de test :

| **DRM** | **Browser** | **Résultat pour les utilisateurs autorisés** | **Résultat pour les utilisateurs non autorisés** |
| --- | --- | --- | --- |
| **PlayReady** |MS Edge ou IE11 sous Windows 10 |Réussite |Échec |
| **Widevine** |Chrome sous Windows 10 |Réussite |Échec |
| **FairPlay** |TBD | | |

George Trifonov, de l’équipe Azure Media Services, a écrit un blog fournissant la procédure détaillée de la configuration d’Azure Active Directory pour une application de lecteur MVC ASP.NET : [intégrer l’application Azure Media Services OWIN basée sur MVC avec Azure Active Directory et restreindre la distribution de clé de contenu en se fondant sur les revendications JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George a également écrit un blog concernant la page [Authentification des jetons JWT dans Azure Media Services et chiffrement dynamique](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Pour plus d’informations, sur Azure Active Directory :

* Vous pouvez trouver des informations pour les développeurs dans [Guide du développeur Azure Active Directory](../active-directory/active-directory-developers-guide.md).
* Vous pouvez trouver des informations sur l’administrateur dans [Administrer votre annuaire Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Des problèmes de mise en œuvre
La mise en œuvre peut présenter certains « pièges ». Nous espérons que la liste des « pièges » qui suit vous aidera à résoudre d’éventuels problèmes.

1. **Issuer** : l’URL doit se terminer par **« / »**.  

    **Audience** : doit correspondre à l’ID client de l’application de lecteur. Vous devez également ajouter **« / »** à la fin de l’URL de l’émetteur.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Dans le [décodeur JWT](http://jwt.calebb.net/), **aud** et **iss** doivent apparaître comme suit dans le jeton JWT :

    ![Première astuce](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. Ajouter des autorisations à l’application dans AAD (sur l’onglet de configuration de l’application). Cette opération est obligatoire pour chaque application (versions locales et déployées).

    ![Deuxième astuce](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. Utilisez le bon émetteur pour établir une protection CENC dynamique :

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Ce qui suit ne fonctionnera pas :

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Le GUID est l’ID du client AAD. Il se trouve dans le menu contextuel des points de terminaison dans le portail Azure.
4. Octroi des autorisations de revendications de membre de groupe. Assurez-vous que vous avez bien ce qui suit dans le fichier de manifeste de l’application AAD

    « groupMembershipClaims » : « All » (la valeur par défaut est Null.)
5. Définition d’un TokenType approprié lors de la création de conditions de restriction.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Depuis l’ajout de la prise en charge de JWT (AAD) en plus des SWT (ACS), la valeur par défaut de TokenType est TokenType.JWT. Si vous utilisez SWT/ACS, vous devez la définir à TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Rubriques supplémentaires pour l’implémentation
Ensuite, nous aborderons certaines rubriques supplémentaires de notre conception et de l’implémentation.

### <a name="http-or-https"></a>HTTP ou HTTPS ?
L’application de lecteur MVC ASP.NET que nous avons créée doit prendre en charge les éléments suivants :

1. authentification des utilisateurs via Azure AD qui doit être sous HTTPS ;
2. échange de jeton JWT entre le client et Azure AD qui doit être sous HTTPS ;
3. Acquisition de licence DRM par le client qui doit être sous HTTPS si la distribution de licences est fournie par Azure Media Services. Bien entendu, la suite de produits PlayReady n'impose pas le format HTTPS pour la distribution de licences. Si votre serveur de licences PlayReady se trouve en dehors d'Azure Media Services, vous pouvez utiliser HTTP ou HTTPS.

C’est pour cette raison que l’application de lecteur d’ASP.NET utilise HTTPS comme une meilleure pratique. Cela signifie que le lecteur Azure Media se trouvera sur une page HTTPS. Toutefois, pour la diffusion nous préférons HTTP, par conséquent, nous devons tenir compte des problèmes liés au contenu mixte.

1. Le navigateur ne permet pas un contenu mixte. Cependant, les plug-ins tels que Silverlight et OSMF pour Smooth et DASH le permettent. Le contenu mixte est un problème de sécurité, en raison de la menace que constitue la possibilité d’injecter un logiciel JS malveillant, et risque de mettre en danger les données clientes.  Les navigateurs bloquent ces dernières par défaut et jusqu’à présent, le seul moyen de contourner ce problème est côté serveur (origine), et autoriser tous les domaines (quel que soit le protocole https ou http). Il ne s’agit probablement pas d’une idée judicieuse.
2. Il est préférable d’éviter les contenus mixtes : les deux doivent utiliser soit HTTP, soit utiliser HTTPS  Si du contenu mixte est lu, silverlightSS tech nécessite la suppression d’un avertissement de contenu mixte. flashSS tech traite le contenu mixte sans aucun avertissement.
3. Si votre point de terminaison de diffusion a été créé avant août 2014, il ne prend pas en charge HTTPS. Dans ce cas, créez et utilisez un nouveau point de terminaison pour le protocole HTTPS.

Dans l’implémentation de référence, dans le cas de contenu protégé DRM application et la diffusion en continu sont toutes les deux sous HTTPS. Pour les contenus ouverts, le lecteur n’a pas besoin d’authentification ou de licence. Vous pouvez au choix utiliser soit HTTP, soit HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Substitution de la clé de signature Azure Active Directory
Il s’agit d’un point important de votre implémentation à prendre en compte. Si vous ne prenez pas cet élément pour l’implémentation, le système terminé risque de cesser de fonctionner complètement au bout de 6 semaines au maximum.

Azure AD utilise une norme standard pour établir une relation de confiance entre lui-même et les applications à l’aide d’Azure AD. Azure AD utilise plus particulièrement une clé de signature se composant d’une paire clé publique-clé privée. Lorsqu’Azure AD crée un jeton de sécurité contenant des informations sur l’utilisateur, ce jeton est signé par Azure AD à l’aide de sa clé privée avant d’être renvoyé à l’application. Pour vérifier que le jeton est valide et provient bien d’Azure AD, l’application doit valider la signature du jeton à l’aide de la clé publique exposée par Azure AD contenue dans le document de métadonnées de fédération du client. Cette clé publique (et la clé de signature d’où elle dérive) est la même que celle qui est utilisée pour tous les clients dans Azure AD.

Vous trouverez des informations détaillées sur la substitution de la clé Azure AD dans le document intitulé [Informations importantes sur la substitution des clés de signature dans Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Dans la [paire de clés publique-privée](https://login.microsoftonline.com/common/discovery/keys/),

* la clé privée est utilisée par Azure Active Directory pour créer un jeton JWT ;
* la clé publique est utilisée par une application telle que le service de distribution de licences DRM dans AMS pour vérifier le jeton JWT ;

à des fins de sécurité, Azure Active Directory renouvelle ce certificat périodiquement (toutes les 6 semaines). La substitution de clé peut survenir à tout moment en cas de violation de la sécurité. Par conséquent, les services de distribution de licence dans AMS doivent mettre à jour la clé publique utilisée lorsqu’Azure AD renouvelle la paire de clés. S’il ne le fait pas, l’authentification des jetons dans AMS échouera et aucune licence ne sera émise.

Pour ce faire, il faut définir TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument au moment de configurer les services de distribution de licence DRM.

Le flux de jeton JWT se présente comme suit :

1. Azure AD émet le jeton JWT avec la clé privée en cours pour un utilisateur authentifié ;
2. Lorsqu’un joueur voit un CENC avec un contenu protégé par multi-DRM, il commence par localiser le jeton JWT émis par Azure AD.
3. Le lecteur envoie la demande d’acquisition de licence avec le jeton JWT pour émettre des licences de services de distribution dans AMS ;
4. les services de distribution de licence dans AMS utiliseront la clé publique de licence actuelle/valide depuis Azure AD pour vérifier le jeton JWT, avant d’émettre des licences.

Les services de distribution de licence DRM vérifieront systématiquement la clé publique actuelle/valide dans Azure AD. La clé publique présentée par Azure AD sera la clé utilisée pour vérifier un jeton JWT émis par Azure AD.

Que se passe-t-il si la substitution de la clé a lieu après qu’AAD ait généré un jeton JWT, mais avant que le jeton JSON soit envoyé par les lecteurs aux services de distribution de licence DRM dans AMS pour vérification ?

Une clé pouvant être substituée à tout moment, il y a toujours plusieurs clés publiques valides disponibles dans le document de métadonnées de la fédération. La distribution de licence Azure Media Services peut utiliser une des clés spécifiées dans le document, car une clé peut être substituée rapidement et une autre prise en remplacement, et ainsi de suite.

### <a name="where-is-the-access-token"></a>Où se trouve le jeton d’accès ?
Si vous regardez comment une application web appelle une application API sous [Identité d’application avec octroi d’informations d’identification client OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), le flux d’authentification est comme ci-dessous :

1. Un utilisateur est connecté à Azure AD dans l’application web (voir la section [Navigateur web vers application web](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)ci-dessus).
2. Le point de terminaison d’autorisation Azure AD redirige l’agent utilisateur vers l’application cliente avec un code d’autorisation. L’agent utilisateur renvoie le code d’autorisation à l’URI de l’application cliente.
3. L’application web doit obtenir un jeton d’accès pour pouvoir s’authentifier auprès de l’API web et extraire la ressource souhaitée. Elle envoie une demande au point de terminaison de jeton d’Azure AD, avec les informations d’identification, l’ID client et  l’URI ID d’application de l’API web. Il présente le code d’autorisation permettra de prouver que l’utilisateur a donné son consentement.
4. Azure AD authentifie l’application et renvoie un jeton d’accès JWT, qui est utilisé pour appeler l’API web.
5. Sur HTTPS, l’application web utilise le jeton d’accès JWT renvoyé pour ajouter la chaîne JWT avec la mention « porteur » dans l’en-tête d’autorisation de la demande adressée à l’API web. L’API web valide ensuite le jeton JWT et, si la validation réussit, renvoie la ressource souhaitée.

Dans ce flux « Identité de l’application », l’API web suppose que l’application web a authentifié l’utilisateur. C’est pour cette raison que ce modèle est appelé « sous-système approuvé ». Le [diagramme sur cette page](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) explique comment le flux relatif au code d’autorisation fonctionne.

Dans l’acquisition de licence avec restriction de jeton, nous suivons le même modèle de sous-système approuvé. Et le service de distribution de licences dans Azure Media Services est une ressource API web, la « Ressource backend » a besoin d’un accès. Où se trouve le jeton d’accès ?

Nous devons obtenir un jeton d’accès de la part d’Azure AD. Une fois l’authentification utilisateur réussie, le code d’autorisation est renvoyé. Il est ensuite utilisé avec l’ID client et la clé d’application pour l’échange d’un jeton d’accès. Le jeton d’accès sert à accéder à une application de « pointeur » qui pointe sur les services de distribution de licence Azure Media Services ou les représente.

Nous devons inscrire et configurer l’application de « pointeur » dans Azure AD en suivant les étapes ci-dessous :

1. Dans le client Azure AD

   * ajouter une application (ressource) avec l’URL de connexion :

   https://[nom_ressource].azurewebsites.net/ et

   * URL d’ID d’application :

   https://[nom_client_aad].onmicrosoft.com/[nom_ressource]
2. Ajoutez une clé à l’application de ressource ;
3. Mettez à jour le fichier manifeste d’application afin que la propriété groupMembershipClaims se voie attribuer la valeur suivante : groupMembershipClaims » : « All »  
4. Dans l’application Azure AD qui pointe vers l’application web du lecteur, dans la section « autorisations pour d’autres applications », ajoutez l’application de ressource ajoutée à l’étape 1 ci-dessus. Sous « autorisations déléguées », cliquez sur la coche « Accès [nom_ressource] ». Cette opération donne à l’application web l’autorisation de créer des jetons d’accès pour accéder l’application de ressource. Vous devez procéder ainsi à la fois pour la version locale et la version déployée de l’application web si vous procédez à un développement avec une application web Visual Studio et Azure.

Le jeton JWT émis par Azure AD est donc le jeton d’accès servant à accéder à cette ressource de type « pointeur ».

### <a name="what-about-live-streaming"></a>Qu’en est-il de la diffusion en continu ?
Dans l’exemple ci-dessus, notre propos était axé sur les éléments multimédias à la demande. Qu’en est-il de la diffusion en continu ?

La bonne nouvelle est que vous pouvez utiliser exactement les mêmes présentation et mise en œuvre de la protection de la diffusion en continu dans Azure Media Services, en traitant la ressource associée à un programme de type « élément multimédia VOD ».

Il est bien connu que pour diffuser en continu dans les Azure Media Services, vous devez créer un canal, puis un programme sous ce canal. Pour créer le programme, vous devez créer un élément contenant l’archive en direct pour le programme. Pour pouvoir assurer la protection multi-DRM CENC, tout ce que vous avez à faire est d’appliquer le même programme d’installation/de traitement à l’élément multimédia, comme s’il s’agissait d’une « ressource VOD » avant de démarrer le programme.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Qu’en est-il des serveurs de licences hors Azure Media Services ?
Souvent, les clients investissent dans une batterie de serveurs qu’ils hébergent dans leur propre centre de données ou chez des fournisseurs de service DRM. Heureusement, la protection de contenu Azure Media Services vous permet de fonctionner en mode hybride : le contenu est hébergé et dynamiquement protégé dans Azure Media Services, tandis que des licences DRM sont fournies par des serveurs en dehors d’Azure Media Services. Dans ce cas, il faut envisager les modifications suivantes :

1. Le service STS (Service d’émission de jeton de sécurité - Secure Token Service) doit émettre des jetons acceptables et pouvant être vérifiés par la batterie de serveurs de licence. Par exemple, les serveurs de licences Widevine fournis par Axinom exigent un jeton JWT spécifique contenant le message « message d’octroi de droit ». Par conséquent, vous devez disposer d’un STS pour émettre ce jeton JWT. Les auteurs ont effectué cette implémentation. Vous pouvez trouver les détails dans le document suivant dans le [Centre de documentation Azure](https://azure.microsoft.com/documentation/) : [Utilisation d’Axinom pour fournir des licences Widevine à Azure Media Services](media-services-axinom-integration.md).
2. Vous n’avez plus besoin de configurer le service de distribution de licence (ContentKeyAuthorizationPolicy) dans Azure Media Services. Vous devez alors fournir les URL d’acquisition de la licence (pour PlayReady, Widevine et FairPlay) au moment où vous configurez AssetDeliveryPolicy dans Configuration CENC avec multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Que se passe-t-il si je souhaite utiliser un STS personnalisé ?
Il existe plusieurs raisons pour qu’un client choisisse d’utiliser un STS personnalisé (Service d’émission de jeton de sécurité - Secure Token Service) pour fournir des jetons JWT, parmi lesquelles :

1. Le fournisseur d’identité (IDP) utilisé par le client ne prend pas en charge STS. Dans ce cas, un service STS personnalisé pourrait être une option.
2. Le client peut avoir besoin d’un contrôle plus souple ou plus étroit pour l’intégration de STS avec le système de facturation client. Par exemple, un opérateur MVPD peut proposer plusieurs packages d’abonné OTT, par exemple Premium, de base, sports, etc. L’opérateur peut appliquer les revendications d’un jeton avec le package d’un abonné afin que seul le contenu du package adéquat soit mis à disposition. Dans ce cas, un STS personnalisé offre la souplesse et la maîtrise nécessaires.

Deux modifications doivent être apportées lors de l’utilisation d’un STS personnalisé :

1. Lorsque vous configurez le service de distribution de licence pour un élément multimédia, vous devez spécifier la clé de sécurité utilisée par le STS personnalisé pour la vérification (plus de détails ci-dessous) et non la clé actuelle d’Azure Active Directory.
2. Une fois le jeton JTW généré, une clé de sécurité est spécifiée à la place de la clé privée du certificat X509 courant dans Azure Active Directory.

Il existe deux types de clés de sécurité :

1. Clé symétrique : la même clé est utilisée pour générer et vérifier un jeton JWT ;
2. Clé asymétrique : une paire de clés publique-privée dans un certificat X 509 est utilisée avec une clé privée pour chiffrer/générer un jeton JWT et la clé publique pour vérifier le jeton.

#### <a name="tech-note"></a>Note technique
Si vous utilisez .NET Framework / C# en tant que plate-forme de développement, le certificat X509 utilisé pour la clé de sécurité asymétrique doit avoir une clé d’une longueur d’au moins 2048 bits. Il s’agit d’une exigence de la classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey dans .NET Framework. Dans le cas contraire, l’exception suivante est générée :

IDX10630 : la longueur de la signature « System.IdentityModel.Tokens.X509AsymmetricSecurityKey » ne peut pas être inférieure à « 2048 » bits.

## <a name="the-completed-system-and-test"></a>Le système et le test terminé
Examinons quelques scénarios dans le système achevé de bout en bout afin que les lecteurs puissent avoir une « image » générale du comportement avant d’obtenir un compte de connexion.

Vous trouverez l’application web de lecteur et ses informations de connexion [ici](https://openidconnectweb.azurewebsites.net/).

Si vous avez besoin d’un scénario « non intégré », de ressources vidéo hébergées dans Azure Media Services non protégées ou protégées par DRM, mais sans authentification des jetons (avec émission d’une licence pour toute personne qui la demande), vous pouvez mener les tests hors connexion (en activant le HTTP si votre vidéo en temps réel fonctionne sous HTTP).

Si vous avez besoin d’un scénario intégré de bout en bout : les ressources vidéo sont sous protection DRM dynamique dans Azure Media Services, avec authentification du jeton généré par Azure AD, vous devez vous connecter.

### <a name="user-login"></a>Connexion utilisateur
Pour tester le système DRM intégré de bout en bout, vous devez disposer d’un « compte » créé ou ajouté.

Quel compte ?

Même si Azure avait initialement autorisé l’accès aux utilisateurs de compte Microsoft uniquement, il autorise désormais l’accès aux utilisateurs des deux systèmes. Ceci a été possible via l’approbation des propriétés Azure pour l’authentification Azure AD, l’authentification Azure AD des utilisateurs professionnels et la création d’une relation de fédération dans laquelle Azure AD approuve le système d’identité Microsoft de compte consommateur pour authentifier les utilisateurs consommateurs. Par conséquent, Azure AD est en mesure d'authentifier les comptes « invités » de Microsoft, ainsi que les comptes Azure AD « natifs ».

Dans la mesure où Azure AD approuve le domaine de compte Microsoft (MSA), vous pouvez ajouter tous les comptes dans les domaines clients suivants d’Azure AD et utiliser le compte de connexion :

| **Nom de domaine** | **Domaine** |
| --- | --- |
| **Domaine client Azure AD personnalisé** |somename.onmicrosoft.com |
| **Domaine d’entreprise** |microsoft.com |
| **Domaine de compte Microsoft (MSA)** |outlook.com, live.com, hotmail.com |

Vous pouvez contacter l’un des auteurs pour qu’un compte soit créé ou ajouté pour vous.

Vous trouverez ci-dessous les captures d’écran de pages de connexion différentes utilisées par les différents comptes de domaine.

Le **compte de domaine client Azure personnalisé** : dans ce cas, vous pouvez voir la page de connexion personnalisée du domaine client Azure AD.

![compte de domaine client Azure personnalisé](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Compte de domaine Microsoft avec carte à puce** : dans ce cas, vous voyez la page de connexion personnalisée par l’informatique d’entreprise Microsoft avec authentification à deux facteurs.

![compte de domaine client Azure personnalisé](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Compte Microsoft (MSA)**: dans ce cas, vous allez voir la page de connexion de Microsoft Account pour les consommateurs.

![compte de domaine client Azure personnalisé](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Utilisation de Encrypted Media Extensions pour PlayReady
Sur un navigateur moderne prenant en charge Encrypted Media Extensions (EME) pour PlayReady tel qu’IE 11 sous Windows 8.1 et version ultérieure et le navigateur Microsoft Edge sous Windows 10, PlayReady sera la DRM sous-jacente d’EME.

![Utilisation d’EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

La zone de lecteur foncée est due au fait que la protection PlayReady empêche d’effectuer une capture d’écran de la vidéo protégée.

L’écran suivant illustre les plug-ins du lecteur et la prise en charge MSE/EME.

![Utilisation d’EME pour PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME dans Microsoft Edge et IE 11 sur Windows 10 permet d’appeler [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) sur les appareils Windows 10 compatibles. PlayReady SL3000 déverrouille le flux de contenu premium améliorées (4K, HDR, etc.) et les nouveaux modèles de distribution de contenu (première fenêtre de contenu amélioré).

Concentrez-vous sur les appareils Windows : PlayReady est le seul DRM dans le matériel disponible sur les appareils Windows (PlayReady SL3000). Un service de diffusion en continu peut utiliser PlayReady via EME ou via une application UWP, et offrir ainsi une meilleure qualité vidéo à l'aide de PlayReady SL3000 par rapport à un autre DRM. En règle générale, le contenu 2K transite via Chrome ou Firefox et le contenu 4K via Microsoft Edge/IE11 ou une application UWP sur le même appareil (selon les paramètres de service et l'implémentation).

#### <a name="using-eme-for-widevine"></a>Utilisation d’EME pour Widevine
Sur un navigateur moderne doté de la prise en charge d’EME/Widevine, telle que Chrome 41 + sous Windows 10, Windows 8.1, Mac OSX Yosemite et Chrome sur Android 4.4.4, la gestion des droits numériques derrière EME est assurée par Google Widevine.

![Utilisation d’EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Notez que Widevine n’empêche pas d’effectuer une capture d’écran de la vidéo protégée.

![Utilisation d’EME pour Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Utilisateurs sans intitulé
Si un utilisateur n’est pas membre du groupe « Utilisateurs autorisés », il n’est pas en mesure de réussir le « contrôle des droits » et le service de licence multi-DRM refusera d’émettre la licence requise comme indiqué ci-dessous. La description détaillée est « L’acquisition de licence a échoué », ce qui correspond à la conception.

![Utilisateurs non habilités](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Exécution d’un Service d’émission de jeton sécurisé personnalisé
Pour le scénario d’exécution de Secure Token Service (STS) personnalisé, le jeton JWT est émis par le STS personnalisé à l’aide d’une clé symétrique ou asymétrique.

Cas d’utilisation de clé symétrique (avec Chrome) :

![Exécution de STS personnalisé](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Cas d’utilisation d’une clé asymétrique via un certificat X509 (à l’aide d’un navigateur moderne Microsoft).

![Exécution de STS personnalisé](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Dans les deux cas cités, l’authentification utilisateur reste la même via Azure AD. La seule différence est que les jetons JWT sont émis par le STS personnalisé et non par Azure AD. Lorsque vous configurez la protection CENC dynamique, la restriction du service de distribution de licence spécifie le type de jeton JWT, avec clé symétrique ou asymétrique.

## <a name="summary"></a>Résumé
Dans ce document, nous avons abordé les sujets des DRM natives multiples et un contrôle d’authentification des jetons : sa conception et son implémentation à l’aide d’Azure, d’Azure Media Services et d’Azure Media Player.

* Une conception de référence est présentée. Elle contient tous les composants nécessaires dans un sous-système DRM/CENC ;
* Une implémentation de référence sur Azure, Azure Media Services et Azure Media Player.
* Certaines rubriques directement impliquées dans la conception et l’implémentation sont également abordées.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
