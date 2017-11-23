---
title: "Sécurisation des ressources CDN Azure avec l’authentification du jeton| Microsoft Docs"
description: "Découvrez comment utiliser l’authentification du jeton pour sécuriser l’accès à vos ressources Azure CDN."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 2f62c0c6783c3cdaf1ffda3299673071b8e4a6f2
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Sécurisation des ressources Azure Content Delivery Network avec l’authentification du jeton

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Vue d'ensemble

L’authentification du jeton est un mécanisme qui vous permet d’empêcher Azure Content Delivery Network (CDN) de fournir des ressources à des clients non autorisés. L’authentification du jeton vise généralement à empêcher le « hotlinking » de contenu, au cours duquel un autre site web (souvent un forum de discussion) utilise vos ressources sans autorisation. Le « hotlinking » peut avoir un impact sur vos coûts de distribution de contenu. En activant l’authentification du jeton sur CDN, les requêtes sont authentifiées par les points de présence de périmètre CDN avant que CDN ne remette le contenu. 

## <a name="how-it-works"></a>Fonctionnement

L’authentification du jeton s’assure que les requêtes sont générées par un site de confiance en vérifiant qu’elles comportent une valeur de jeton contenant des informations codées sur le demandeur. Le contenu est fourni à un demandeur uniquement si les informations codées respectent les exigences définies. Dans le cas contraire, les requêtes sont refusées. Vous pouvez procéder à la configuration à l’aide d’un ou de plusieurs des paramètres suivants :

- Pays : autorisez ou refusez les requêtes provenant des pays spécifiés par leur [code du pays](https://msdn.microsoft.com/library/mt761717.aspx).
- URL : autorisez uniquement les requêtes qui correspondent à la ressource ou au chemin d’accès spécifiés.
- Hôte : autorisez ou refusez les requêtes utilisant les hôtes spécifiés dans l’en-tête de requête.
- Référent : autorisez ou refusez une requête provenant du référent spécifié.
- Adresse IP : autorisez uniquement les requêtes provenant d’une adresse ou d’un sous-réseau IP spécifique.
- Protocole : autorisez ou refusez des requêtes en fonction du protocole utilisé pour demander le contenu.
- Date/heure d’expiration : affectez une période de date et d’heure pour vous assurer qu’un lien reste valide uniquement pendant une période limitée.

Pour plus d’informations, consultez les exemples de configuration détaillés pour chaque paramètre dans [Configuration de l’authentification du jeton](#setting-up-token-authentication).

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme des flux de travail suivant décrit comment Azure CDN applique l’authentification du jeton pour utiliser votre application web.

![Flux de travail de l’authentification du jeton dans CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logique de validation de jeton sur le point de terminaison CDN
    
L’organigramme suivant décrit comment Azure CDN valide la requête du client lorsque l’authentification du jeton est configurée sur le point de terminaison CDN.

![Logique de validation du jeton dans CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuration de l’authentification du jeton

1. À partir du [portail Azure](https://portal.azure.com), accédez à votre profil CDN, puis cliquez sur **Gérer** pour ouvrir le portail supplémentaire.

    ![Bouton Gérer du profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Placez le pointeur sur **HTTP Large**, puis cliquez sur **Token Auth** (Authentification du jeton) dans le menu volant. Vous pouvez ensuite configurer la clé de chiffrement et les paramètres de chiffrement comme suit :

    1. Créez une ou plusieurs clés de chiffrement. Une clé de chiffrement respecte la casse et peut contenir n’importe quelle combinaison de caractères alphanumériques. D’autres types de caractères, y compris les espaces, ne sont pas autorisés. La longueur maximale est de 250 caractères. Pour vous assurer que vos clés de chiffrement sont aléatoires, il est recommandé de les créer à l’aide de l’outil OpenSSL. L’outil OpenSSL présente la syntaxe suivante : `rand -hex <key length>`. Par exemple, `OpenSSL> rand -hex 32`. Pour éviter les temps d’arrêt, créez une clé primaire et une clé de sauvegarde. Une clé de sauvegarde fournit un accès ininterrompu à votre contenu pendant que la mise à jour de votre clé primaire est en cours.
    
    2. Entrez une clé de chiffrement unique dans la zone **Clé primaire**, puis tapez éventuellement une clé de sauvegarde dans la zone **Clé de sauvegarde**.

    3. Sélectionnez la version minimale de chiffrement pour chaque clé dans la liste déroulante **Minimum Encryption Version (Version minimale de chiffrement)**, puis cliquez sur **Update (Mettre à jour)** :
       - **V2** : indique que la clé peut être utilisée pour générer des jetons de version 2.0 et version 3.0. Utilisez cette option uniquement si vous effectuez la transition depuis une clé de chiffrement version 2.0 héritée vers une clé de version 3.0.
       - **V3** : (recommandé) indique que la clé peut être uniquement utilisée pour générer des jetons de version 3.0.

    ![Clé de configuration de l’authentification du jeton CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. Utilisez l’outil de chiffrement pour définir les paramètres de chiffrement et générer un jeton. Avec l’outil de chiffrement, vous pouvez autoriser ou refuser des requêtes en fonction de la date/heure d’expiration, du pays, du référent, du protocole et de l’IP du client (dans toute combinaison). Bien qu’il n’existe aucune restriction sur le nombre et la combinaison de paramètres pouvant être associés pour former un jeton, la longueur totale d’un jeton est limitée à 512 caractères. 

       ![Outil de chiffrement CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Entrez les valeurs pour un ou plusieurs des paramètres de chiffrement suivants dans la zone **Encrypt Tool (Outil de chiffrement)** :  

       - **ec_expire** : affecte une date/heure d’expiration à un jeton au-delà de laquelle le jeton est arrivé à expiration. Les requêtes soumises après la date/heure d’expiration sont refusées. Ce paramètre utilise un timestamp Unix, qui est basé sur le nombre de secondes écoulées depuis l’époque standard `1/1/1970 00:00:00 GMT`. (Vous pouvez utiliser des outils en ligne pour convertir l’heure standard en heure Unix, et inversement.) Par exemple, si vous souhaitez que le jeton arrive à expiration le `12/31/2016 12:00:00 GMT`, utilisez la valeur de timestamp Unix `1483185600`, comme suit. 
    
         ![Exemple de paramètre ec_expire dans CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec_url_allow** : permet d’adapter les jetons à une ressource ou à un chemin d’accès particulier. Ce paramètre restreint l’accès aux demandes dont l’URL commence par un chemin d’accès relatif spécifique. Les URL sont sensibles à la casse. Entrez plusieurs chemins d’accès en les séparant par une virgule. Selon vos exigences, vous pouvez définir des valeurs différentes pour fournir différents niveaux d’accès. 
        
         Par exemple, pour l’URL `http://www.mydomain.com/pictures/city/strasbourg.png`, ces requêtes sont autorisées pour les valeurs d’entrée suivantes :

         - Valeur d’entrée `/` : toutes les requêtes sont autorisées.
         - Valeur d’entrée `/pictures` : les requêtes suivantes sont autorisées :
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - Valeur d’entrée `/pictures/` : seules les requêtes contenant le chemin d’accès `/pictures/` sont autorisées. Par exemple, `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - Valeur d’entrée `/pictures/city/strasbourg.png` : seules les requêtes concernant ce chemin d’accès et cette ressource spécifiques sont autorisées.
    
       - **ec_country_allow** : autorise uniquement les requêtes provenant d’un ou de plusieurs pays spécifiés. Les requêtes provenant de tous les autres pays sont refusées. Utilisez des codes de pays et séparez-les par des virgules. Par exemple, pour autoriser l’accès aux requêtes provenant uniquement des États-Unis et de France, entrez US, FR dans la zone comme indiqué ci-dessous.  
        
           ![Exemple de paramètre ec_country_allow dans CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec_country_deny** : refuse les requêtes provenant d’un ou de plusieurs pays spécifiés. Les requêtes provenant de tous les autres pays sont autorisées. Utilisez des codes de pays et séparez-les par des virgules. Par exemple, pour refuser l’accès aux requêtes provenant des États-Unis et de France, entrez US, FR dans la zone.
    
       - **ec_ref_allow** : autorise uniquement les requêtes provenant du référent spécifié. Un référent identifie l’URL de la page web qui est liée à la ressource demandée. N’incluez pas le protocole dans la valeur du paramètre de référent. Les types d’entrée suivants sont autorisés pour la valeur du paramètre :
           - Un nom d’hôte ou un nom d’hôte et un chemin d’accès.
           - Plusieurs référents. Pour ajouter plusieurs référents, séparez chacun d’eux par une virgule. Si vous spécifiez une valeur de référent, mais que la configuration du navigateur ne permet pas l’envoi des informations sur le référent dans la requête, cette requête est refusée par défaut. 
           - Requêtes manquant d’informations sur le référent. Pour autoriser ces types de requête, entrez le texte « missing » ou une valeur vide. 
           - Sous-domaines. Pour autoriser des sous-domaines, entrez un astérisque (\*). Par exemple, pour autoriser tous les sous-domaines de `consoto.com`, entrez `*.consoto.com`. 
           
          L’exemple suivant présente l’entrée permettant d’autoriser l’accès aux requêtes provenant de `www.consoto.com`, à l’ensemble des sous-domaines sous `consoto2.com` et aux requêtes manquant d’informations ou disposant de valeurs vides pour les référents.
        
          ![Exemple de paramètre ec_ref_allow dans CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec_ref_deny** : refuse les requêtes provenant du référent spécifié. L’implémentation est identique à celle du paramètre ec_ref_allow.
         
       - **ec_proto_allow** : autorise uniquement les requêtes correspondant au protocole spécifié, par exemple, HTTP ou HTTPS.
            
       - **ec_proto_deny** : refuse les protocoles correspondant au protocole spécifié, par exemple, HTTP ou HTTPS.
    
       - **ec_clientip** : limite l’accès à l’adresse IP de demandeur spécifiée. Les adresses IPV4 et IPV6 sont prises en charge. Vous pouvez spécifier un sous-réseau IP ou une adresse IP de requête unique.
            
         ![Exemple de paramètre ec_clientip dans CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    5. Après avoir entré les valeurs des paramètres de chiffrement, sélectionnez une clé à chiffrer (si vous avez créé à la fois une clé primaire et une clé de sauvegarde) dans la liste **Key To Encrypt (Clé à chiffrer)**.
    
    6. Sélectionnez une version de chiffrement dans la liste **Encryption Version (Version de chiffrement)** : **V2** pour la version 2 ou **V3** pour la version 3 (recommandé). Ensuite, cliquez sur **Encrypt (Chiffrer)**  pour générer le jeton.

    Lorsque le jeton est généré, il est affiché dans la zone **Generated Token (Jeton généré)**. Pour utiliser le jeton, ajoutez-le en tant que chaîne de requête à la fin du fichier dans le chemin de l’URL. Par exemple, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    7. Testez éventuellement votre jeton avec l’outil de déchiffrement. Collez la valeur du jeton dans la zone **Token to Decrypt (Jeton à déchiffrer)**. Sélectionnez la clé de chiffrement à utiliser dans la liste déroulante **Key To Decrypt (Clé à déchiffrer)**, puis cliquez sur **Decrypt (Déchiffrer)**.

    Une fois que le jeton est déchiffré, ses paramètres sont affichés dans la zone **Original Parameters (Paramètres d’origine)**.

    8. Personnalisez éventuellement le type de code de réponse qui est retourné lorsqu’une requête est refusée. Sélectionnez le code dans la liste déroulante **Code de réponse**, puis cliquez sur **Enregistrer**. Le code de réponse **403** (Interdit) est sélectionné par défaut. Pour certains codes de réponse, vous pouvez également entrer l’URL de votre page d’erreur dans la zone **Valeur de l’en-tête**. 

3. Cliquez sur **Moteur de règles** sous **HTTP Large**. Le moteur de règles permet de définir les chemins d’accès pour appliquer la fonctionnalité, d’activer la fonctionnalité d’authentification du jeton et d’activer d’autres fonctionnalités associées à l’authentification du jeton. Pour plus d’informations, consultez [Moteur des règles Azure CDN](cdn-rules-engine-reference.md).

    1. Sélectionnez une règle existante ou créez-en une pour définir la ressource ou le chemin d’accès pour lesquels vous souhaitez appliquer l’authentification du jeton. 
    2. Pour activer l’authentification du jeton sur une règle, sélectionnez **[Token Auth (Authentification du jeton)](cdn-rules-engine-reference-features.md#token-auth)** dans la liste déroulante **Fonctionnalités**, puis sélectionnez **Activé**. Cliquez sur **Mettre à jour** si vous mettez à jour une règle ou sur **Ajouter** si vous en créez une.
        
    ![Exemple d’activation de l’authentification du jeton via le moteur de règles dans CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Dans le moteur de règles, vous pouvez également activer d’autres fonctionnalités associées à l’authentification. Pour activer l’une des fonctionnalités suivantes, sélectionnez-la dans la liste déroulante **Fonctionnalités**, puis sélectionnez **Activé**.
    
    - **[Token auth denial code (Code de refus d’authentification du jeton)](cdn-rules-engine-reference-features.md#token-auth-denial-code)** : détermine le type de réponse à retourner à un utilisateur quand une requête est refusée. Les règles définies ici remplacent les codes de réponse de la section **Custom Denial Handling (Gestion personnalisée des refus)** de la page d’authentification basée sur le jeton.
    - **[Token Auth Ignore URL Case (Ignorer la casse de l’URL pour l’authentification du jeton)](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)** : détermine si la casse de l’URL utilisée pour valider le jeton est prise en compte.
    - **[Token auth parameter (Paramètre d’authentification du jeton)](cdn-rules-engine-reference-features.md#token-auth-parameter)** : renomme le paramètre de chaîne de requête d’authentification du jeton affiché dans l’URL demandée. 
        
    ![Exemple de paramètres d’authentification du jeton via le moteur de règles dans CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Vous pouvez personnaliser votre jeton en accédant au code source dans [GitHub](https://github.com/VerizonDigital/ectoken).
Les langages disponibles sont notamment :
    
- C
- C#
- PHP
- Perl
- Java
- Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Tarification du fournisseur et des fonctionnalités du CDN Azure

Pour des informations sur les fonctionnalités, consultez [Vue d’ensemble du CDN](cdn-overview.md). Pour des informations sur les tarifs, consultez [Tarifs Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
