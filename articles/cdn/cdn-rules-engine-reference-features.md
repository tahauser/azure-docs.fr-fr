---
title: Fonctionnalités du moteur de règles Azure CDN | Microsoft Docs
description: Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 9f1a9343a657e076e94f6aa59fd03128ef488ac9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cdn-rules-engine-features"></a>Fonctionnalités du moteur de règles Azure CDN
Cet article comprend les descriptions détaillées des fonctionnalités du [moteur de règles](cdn-rules-engine.md) Azure Content Delivery Network (CDN).

La troisième partie d’une règle est la fonctionnalité. Une fonctionnalité définit le type d’action appliqué au type de requête identifié par un ensemble de conditions de correspondance.

## <a name="access-features"></a>Fonctionnalités d’accès

Ces fonctionnalités sont conçues pour contrôler l’accès au contenu.


NOM | Objectif
-----|--------
[Deny Access (403)](#deny-access-403) | Détermine si toutes les requêtes sont rejetées avec une réponse 403 Interdit.
[Token Auth](#token-auth) | Détermine si l’authentification basée sur les jetons est appliquée à une requête.
[Token Auth Denial Code](#token-auth-denial-code) | Détermine le type de réponse à retourner à un utilisateur quand une requête est refusée en raison de l’authentification basée sur les jetons.
[Token Auth Ignore URL Case](#token-auth-ignore-url-case) | Détermine si les comparaisons d’URL effectuées par l’authentification basée sur les jetons respectent la casse.
[Token Auth Parameter](#token-auth-parameter) | Détermine si le paramètre de chaîne de requête de l’authentification basée sur les jetons doit être renommé.


## <a name="caching-features"></a>Fonctionnalités de mise en cache

Ces fonctionnalités sont conçues pour personnaliser la mise en cache du contenu.

NOM | Objectif
-----|--------
[Bandwidth Parameters](#bandwidth-parameters) | Détermine si les paramètres de limitation de bande passante (par exemple, ec_rate et ec_prebuf) sont actifs.
[Bandwidth Throttling](#bandwidth-throttling) | Limite la bande passante pour la réponse fournie par le point de présence (POP).
[Bypass Cache](#bypass-cache) | Détermine si la requête doit contourner la mise en cache.
[Cache-Control Header Treatment](#cache-control-header-treatment) | Contrôle la génération des en-têtes `Cache-Control` par le point de présence quand la fonctionnalité Obsolescence maximale externe est active.
[Cache-Key Query String](#cache-key-query-string) | Détermine si la clé de cache inclut ou exclut les paramètres de chaîne de requête associés à une requête.
[Cache-Key Rewrite](#cache-key-rewrite) | Réécrit la clé de cache associée à une requête.
[Complete Cache Fill](#complete-cache-fill) | Détermine ce qui se passe quand une requête génère une absence de cache partielle sur un point de présence.
[Compress File Types](#compress-file-types) | Définit les formats de fichier des fichiers qui sont compressés sur le serveur.
[Default Internal Max-Age](#default-internal-max-age) | Détermine l’intervalle d’âge maximal par défaut pour la revalidation du cache entre le point de présence et le serveur d’origine.
[Expires Header Treatment](#expires-header-treatment) | Contrôle la génération des en-têtes `Expires` par un point de présence quand la fonctionnalité Obsolescence maximale externe est active.
[External Max-Age](#external-max-age) | Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le navigateur et le point de présence.
[Force Internal Max-Age](#force-internal-max-age) | Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le point de présence et le serveur d’origine.
[H.264 Support (HTTP Progressive Download)](#h264-support-http-progressive-download) | Détermine les types de formats de fichier H.264 qui peuvent être utilisés pour diffuser du contenu.
[Honor No-Cache Request](#honor-no-cache-request) | Détermine si les requêtes non-cache d’un client HTTP sont transmises au serveur d’origine.
[Ignore Origin No-Cache](#ignore-origin-no-cache) | Détermine si le CDN ignore certaines directives remises par un serveur d’origine.
[Ignore Unsatisfiable Ranges](#ignore-unsatisfiable-ranges) | Détermine la réponse à retourner aux clients quand une requête génère un code d’état 416 Plage demandée non satisfaisante.
[Internal Max-Stale](#internal-max-stale) | Contrôle la durée après l’expiration normale d’une ressource mise en cache pendant laquelle cette ressource peut être remise depuis un point de présence quand ce point de présence ne parvient pas à la revalider avec le serveur d’origine.
[Partial Cache Sharing](#partial-cache-sharing) | Détermine si une requête peut générer du contenu partiellement mis en cache.
[Prevalidate Cached Content](#prevalidate-cached-content) | Détermine si du contenu mis en cache peut faire l’objet d’une revalidation anticipée avant l’expiration de sa durée de vie.
[Refresh Zero-Byte Cache Files](#refresh-zero-byte-cache-files) | Détermine la façon dont les points de présence gèrent la requête d’un client HTTP liée à une ressource de cache de 0 octet.
[Set Cacheable Status Codes](#set-cacheable-status-codes) | Définit l’ensemble des codes d’état qui peuvent générer du contenu mis en cache.
[Stale Content Delivery on Error](#stale-content-delivery-on-error) | Détermine si du contenu mis en cache qui a expiré est remis quand une erreur se produit pendant la revalidation du cache ou l’extraction du contenu demandé à partir du serveur d’origine du client.
[Stale While Revalidate](#stale-while-revalidate) | Améliore les performances en permettant aux points de présence de rendre obsolète le client pour le demandeur pendant la revalidation.

## <a name="comment-feature"></a>Fonctionnalité de commentaire

Cette fonctionnalité est conçue pour fournir des informations supplémentaires à l’intérieur d’une règle.

NOM | Objectif
-----|--------
[Commentaire](#comment) | Permet d’ajouter une remarque dans une règle.
 
## <a name="header-features"></a>Fonctionnalités d’en-tête

Ces fonctionnalités sont conçues pour ajouter, modifier ou supprimer des en-têtes de la demande ou réponse.

NOM | Objectif
-----|--------
[Age Response Header](#age-response-header) | Détermine si un en-tête de réponse Age est inclus dans la réponse envoyée au demandeur.
[Debug Cache Response Headers](#debug-cache-response-headers) | Détermine si une réponse peut inclure l’en-tête de réponse X-EC-Debug qui fournit des informations sur la stratégie de cache de la ressource demandée.
[Modify Client Request Header](#modify-client-request-header) | Remplace, complète ou supprime un en-tête dans une requête.
[Modify Client Response Header](#modify-client-response-header) | Remplace, complète ou supprime un en-tête dans une réponse.
[Set Client IP Custom Header](#set-client-ip-custom-header) | Permet d’ajouter l’adresse IP du client demandeur à la requête sous la forme d’en-tête de requête personnalisé.


## <a name="logging-features"></a>Fonctionnalités de journalisation

Ces fonctionnalités sont conçues pour personnaliser les données stockées dans les fichiers journaux bruts.

NOM | Objectif
-----|--------
[Custom Log Field 1](#custom-log-field-1) | Détermine le format et le contenu à attribuer au champ de fichier journal personnalisé dans un fichier journal brut.
[Log Query String](#log-query-string) | Détermine si une chaîne de requête est stockée avec l’URL dans les journaux d’accès.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Fonctionnalités d’origine

Ces fonctionnalités sont conçues pour contrôler la manière dont le CDN communique avec un serveur d’origine.

NOM | Objectif
-----|--------
[Maximum Keep-Alive Requests](#maximum-keep-alive-requests) | Définit le nombre maximal de requêtes pour une connexion toujours active avant sa fermeture.
[Proxy Special Headers](#proxy-special-headers) | Définit l’ensemble des en-têtes de requête propres à CDN à transmettre depuis un point de présence vers un serveur d’origine.


## <a name="specialty-features"></a>Fonctionnalités spéciales

Il s’agit de fonctionnalités avancées destinées aux utilisateurs expérimentés.

NOM | Objectif
-----|--------
[Cacheable HTTP Methods](#cacheable-http-methods) | Détermine l’ensemble de méthodes HTTP supplémentaires pouvant être mises en cache sur le réseau.
[Cacheable Request Body Size](#cacheable-request-body-size) | Définit le seuil permettant de déterminer si une réponse POST peut être mise en cache.
[User Variable](#user-variable) | À usage interne uniquement.

 
## <a name="url-features"></a>Fonctionnalités d’URL

Ces fonctionnalités permettent de rediriger ou de réécrire une requête vers une URL différente.

NOM | Objectif
-----|--------
[Follow Redirects](#follow-redirects) | Détermine si les requêtes peuvent être redirigées vers le nom d’hôte défini dans l’en-tête Location retourné par un serveur d’origine du client.
[URL Redirect](#url-redirect) | Redirige les requêtes via l’en-tête Location.
[URL Rewrite](#url-rewrite)  | Réécrit l’URL de la requête.



## <a name="azure-cdn-rules-engine-features-reference"></a>Référence sur les fonctionnalités du moteur de règles Azure CDN

---
### <a name="age-response-header"></a>En-tête de réponse Age
**Objectif** : détermine si un en-tête de réponse Age est inclus dans la réponse envoyée au demandeur.
Valeur|Résultat
--|--
activé | L’en-tête de réponse Age est inclus dans la réponse envoyée au demandeur.
Désactivé | L’en-tête de réponse Age est exclu de la réponse envoyée au demandeur.

**Comportement par défaut :** Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Paramètres de bande passante
**Objectif :** détermine si les paramètres de limitation de bande passante (par exemple, ec_rate et ec_prebuf) sont actifs.

Les paramètres de limitation de bande passante déterminent si le taux de transfert de données pour une requête du client est limité à un taux personnalisé.

Valeur|Résultat
--|--
activé|Permet aux points de présence d’honorer les requêtes de limitation de bande passante.
Désactivé|Force les points de présence à ignorer les paramètres de limitation de bande passante. Le contenu demandé est pris en charge normalement (c’est-à-dire, sans limitation de bande passante).

**Comportement par défaut :** Activé.
 
[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Limitation de bande passante
**Objectif :** limite la bande passante pour la réponse fournie par les points de présence.

Les deux options suivantes doivent être définies pour configurer correctement la limitation de bande passante.

Option|Description
--|--
Koctets par seconde|Définissez cette option sur la bande passante maximale (en Ko par seconde) qui peut être utilisée pour fournir la réponse.
Secondes prebuf|Définissez cette option sur le nombre de secondes que doivent attendre les points de présence pour que soit effectuée la limitation de la bande passante. L’objectif de cette période de bande passante illimitée consiste à éviter les problèmes de coupure ou de mise en mémoire tampon pour un lecteur multimédia en raison d’une limitation de bande passante.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Ignorer le cache
**Objectif :** détermine si la requête doit contourner la mise en cache.

Valeur|Résultat
--|--
activé|Force toutes les requêtes à passer par le serveur d’origine, même si le contenu a été précédemment mis en cache sur les points de présence.
Désactivé|Force les points de présence à mettre les ressources en cache selon la stratégie de cache définie dans ses en-têtes de réponse.

**Comportement par défaut :**

- **HTTP Large :** Désactivé

<!---
- **ADN:** Enabled

--->

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Méthodes HTTP pouvant être mises en cache
**Objectif :** détermine l’ensemble de méthodes HTTP supplémentaires pouvant être mises en cache sur le réseau.

Informations essentielles :

- Cette fonction suppose que les réponses GET soient toujours mises en cache. Par conséquent, la méthode HTTP GET ne doit pas être incluse lors de la définition de cette fonctionnalité.
- Cette fonctionnalité prend uniquement en charge la méthode HTTP POST. Activez la mise en cache de réponse POST en définissant cette fonctionnalité sur `POST`.
- Par défaut, seules les requêtes dont le corps est inférieur à 14 Ko sont mises en cache. Utilisez la fonctionnalité Taille de corps de requête pouvant être mise en cache pour définir la taille de corps de requête maximale.

**Comportement par défaut :** seules les réponses GET sont mises en cache.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Taille de corps de requête pouvant être mise en cache
**Objectif :** définit le seuil permettant de déterminer si une réponse POST peut être mise en cache.

Ce seuil est déterminé par la définition d’une taille de corps de requête maximale. Les requêtes qui contiennent un corps de requête plus large ne sont pas mises en cache.

Informations essentielles :

- Cette fonctionnalité n’est applicable que lorsque les réponses POST peuvent prétendre à la mise en cache. Utilisez la fonctionnalité Méthodes HTTP pouvant être mises en cache pour activer la mise en cache de requête POST.
- Le corps de requête est pris en considération pour :
    - les valeurs x-www-form-urlencoded
    - Garantir une clé de cache unique
- La définition d’une taille de corps de requête maximale peut affecter les performances de diffusion de données.
    - **Valeur recommandée :** 14 Ko
    - **Valeur minimale :** 1 Ko

**Comportement par défaut :** 14 ko

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Traitement d’en-tête de contrôle de cache
**Objectif :** contrôle la génération des en-têtes `Cache-Control` par le point de présence quand la fonctionnalité Obsolescence maximale externe est active.

Pour obtenir ce type de configuration le plus simple consiste à placer les fonctionnalités Âge maximal externe et En-tête de contrôle de cache dans la même instruction.

Valeur|Résultat
--|--
Remplacer|Garantit que les actions suivantes se produisent :<br/> - Remplacement de l’en-tête `Cache-Control` généré par le serveur d’origine. <br/>- Ajout de l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age à la réponse.
Transmettre directement|Permet de garantir que l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age ne soit jamais ajouté à la réponse. <br/> Si le serveur d’origine génère un en-tête `Cache-Control`, celui-ci est transmis directement à l’utilisateur final. <br/> Si le serveur d’origine ne génère pas d’en-tête `Cache-Control`, l’en-tête de réponse risque de ne pas contenir d’en-tête `Cache-Control`.
Ajouter si manquant|Si aucun en-tête `Cache-Control` n’a été transmis par le serveur d’origine, cette option ajoute l’en-tête `Cache-Control` généré par la fonctionnalité External Max-Age. Cette option est utile pour garantir que toutes les ressources reçoivent un en-tête `Cache-Control`.
Supprimer| Cette option permet de garantir qu’aucun en-tête `Cache-Control` ne soit inclus dans la réponse de l’en-tête. Si un en-tête `Cache-Control` a déjà été attribué, il est supprimé de la réponse de l’en-tête.

**Comportement par défaut :** Remplacer.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Chaîne de requête de clé de cache
**Objectif :** détermine si la clé de cache inclut ou exclut les paramètres de chaîne de requête associés à une requête.

Informations essentielles :

- Spécifiez un ou plusieurs noms de paramètre de chaîne de requête. Délimite chaque nom de paramètre par un espace.
- Cette fonctionnalité détermine si les paramètres de chaîne de requête sont inclus ou exclus de la clé de cache. Vous trouverez des informations supplémentaires pour chaque option dans le tableau suivant.

type|Description
--|--
 Inclure|  Indique que chaque paramètre spécifié doit être inclus dans la clé de cache. Une clé de cache unique est générée pour chaque requête contenant une valeur unique pour un paramètre de chaîne de requête défini dans cette fonctionnalité. 
 Inclure tout  |Indique qu’une clé de cache unique est créée pour chaque requête dans une ressource qui inclut une chaîne de requête unique. Ce type de configuration n’est généralement pas recommandé, car il peut entraîner un petit pourcentage de correspondances dans le cache. Un faible nombre de correspondances dans le cache augmente la charge sur le serveur d’origine, car il doit traiter plus de requêtes. Cette configuration duplique le comportement de mise en cache appelé « unique-cache » sur la page de mise en cache de la chaîne de requête. 
 Exclure | Indique que seuls les paramètres spécifiés sont exclus de la clé de cache. Tous les autres paramètres de chaîne de requête figurent dans la clé de cache. 
 Exclure tous  |Indique que tous les paramètres de chaîne de requête sont exclus de la clé de cache. Cette configuration duplique le comportement de mise en cache par défaut « standard-cache » sur la page de mise en cache de la chaîne de requête.  

Le moteur de règles vous permet de personnaliser la manière dont la mise en cache de la chaîne de requête est implémentée. Par exemple, vous pouvez spécifier que la mise en cache de la chaîne de requête est effectuée uniquement sur certains emplacements ou types de fichiers.

Pour dupliquer le comportement de mise en cache de la chaîne de requête « no-cache » sur la page de mise en cache de la chaîne de requête, créez une règle qui contient une condition de correspondance de caractère générique de requête d’URL et une fonctionnalité Ignorer le cache. Définissez la condition de correspondance de caractère générique de requête d’URL sur un astérisque (*).

#### <a name="sample-scenarios"></a>Exemples de scénarios

L’exemple d’utilisation suivant inclut un exemple de requête et la clé de cache par défaut :

- **Exemple de requête :** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&amp;language=EN&amp;userid=01
- **Clé de cache par défaut :** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Inclure

Exemple de configuration :

- **Type :** Inclure
- **Paramètre(s) :** langue

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Inclure tout

Exemple de configuration :

- **Type :** Inclure tout

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclure

Exemple de configuration :

- **Type :** Exclure
- **Paramètre(s) :**sessionid userid

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Exclure tous

Exemple de configuration :

- **Type :** Exclure tous

Ce type de configuration génère la clé de cache de paramètre de chaîne de requête suivante :

    /800001/Origin/folder/asset.htm

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Réécriture de la clé de cache
**Objectif :** réécrit la clé de cache associée à une requête.

Une clé de cache est le chemin d’accès relatif qui identifie une ressource pour les besoins de la mise en cache. En d’autres termes, les serveurs recherchent une version mise en cache d’une ressource en fonction de son chemin, tel que défini par sa clé de cache.

Configurez cette fonctionnalité en définissant les deux options suivantes :

Option|Description
--|--
Chemin d’accès d’origine| Permet de définir le chemin d’accès relatif aux types de requêtes dont la clé de cache est réécrite. Un chemin d’accès relatif peut être défini en sélectionnant un chemin d’accès d’origine de base et en définissant un modèle d’expression régulière.
Nouveau chemin d’accès|Permet de définir le chemin d’accès relatif pour la nouvelle clé de cache. Un chemin d’accès relatif peut être défini en sélectionnant un chemin d’accès d’origine de base et en définissant un modèle d’expression régulière. Ce chemin d’accès relatif peut être construit de manière dynamique via l’utilisation de variables HTTP
**Comportement par défaut :** La clé de cache d’une requête est déterminée par l’URI de requête.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Commentaire
**Objectif :** permet d’ajouter une remarque dans une règle.

Une utilisation de cette fonctionnalité consiste à fournir des informations supplémentaires sur l’utilisation générale d’une règle ou sur la raison pour laquelle une condition de correspondance spécifique ou une fonctionnalité spécifique a été ajoutée à la règle.

Informations essentielles :

- Vous pouvez spécifier un maximum de 150 caractères.
- Utilisez uniquement des caractères alphanumériques.
- Cette fonctionnalité n’affecte pas le comportement de la règle. Il est simplement destiné à proposer une zone dans laquelle vous pouvez fournir des informations afin de pouvoir vous y référer ultérieurement, ou qui peuvent vous aider lors du dépannage de la règle.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Remplissage de cache complet
**Objectif :** détermine ce qui se passe quand une requête génère une absence de cache partielle sur un point de présence.

Une absence de cache partielle décrit l’état du cache pour une ressource qui n’a pas été complètement téléchargée sur un point de présence. Si une ressource n’est que partiellement mise en cache sur un point de présence, la requête suivante de cette ressource est transférée à nouveau sur le serveur d’origine.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Une absence de cache partielle se produit généralement après qu’un utilisateur a abandonné un téléchargement ou pour les ressources qui sont demandées uniquement à l’aide de requêtes de plage HTTP. Cette fonctionnalité est particulièrement utile pour les ressources volumineuses qui ne sont généralement pas téléchargées entièrement (par exemple, les vidéos). Par conséquent, cette fonctionnalité est activée par défaut sur la plateforme HTTP Large. Elle est désactivée sur toutes les autres plateformes.

Conservez la configuration par défaut pour la plateforme HTTP Large, car cela réduit la charge sur le serveur d’origine de votre client et augmente la vitesse à laquelle vos clients téléchargent votre contenu.

En raison de la manière dont les paramètres de cache sont suivis, cette fonctionnalité ne peut pas être associée aux conditions de correspondance suivantes : Edge Cname, Littéral d’en-tête de requête, Caractère générique d’en-tête de requête, Littéral de requête d’URL et Caractère générique de requête d’URL.

Valeur|Résultat
--|--
activé|Restaure le comportement par défaut. Le comportement par défaut consiste à forcer le point de présence à lancer une récupération en arrière-plan de la ressource à partir du serveur d’origine. Après quoi, la ressource se trouvera dans le cache local du point de présence.
Désactivé|Empêche un point de présence d’effectuer une récupération en arrière-plan pour la ressource. Le résultat est tel que la requête suivante pour cette ressource à partir de cette région force un point de présence à la demander à partir du serveur d’origine du client.

**Comportement par défaut :** Activé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Compresser les types de fichiers
**Objectif :** définit les formats de fichier des fichiers qui sont compressés sur le serveur.

Un format de fichier peut être spécifié à l’aide de son type de média Internet (par exemple, Content-Type). Le type de média Internet est constitué de métadonnées indépendantes de la plateforme, permettant aux serveurs d’identifier le format de fichier d’une ressource particulière. Vous trouverez ci-dessous une liste des types de média Internet courants.

Type de média Internet|Description
--|--
texte/brut|Fichiers de texte brut
texte/html| Fichiers HTML
texte/css|Cascading Style Sheets (CSS)
application/x-javascript|JavaScript
application/javascript|JavaScript
Informations essentielles :

- Spécifiez plusieurs types de média Internet en délimitant chacun d’entre eux à l’aide d’un seul espace. 
- Cette fonctionnalité compresse uniquement les ressources dont la taille est inférieure à 1 Mo. Les ressources plus volumineuses ne sont pas compressées par les serveurs.
- Certains types de contenu, tels que des images, des vidéos et des éléments multimédias audio (par exemple, JPG, MP3, MP4, etc.), sont déjà compressés. Étant donné que la compression supplémentaire sur ces types de ressources ne diminue pas considérablement la taille du fichier, il est recommandé de ne pas activer la compression sur ceux-ci.
- Les caractères génériques, tels que des astérisques, ne sont pas pris en charge.
- Avant d’ajouter cette fonctionnalité à une règle, veillez à définir l’option de désactivation de la compression sur la page Compression pour la plateforme à laquelle cette règle est appliquée.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Champ de fichier journal personnalisé 1
**Objectif :** détermine le format et le contenu à attribuer au champ de fichier journal personnalisé dans un fichier journal brut.

Ce champ personnalisé vous permet de déterminer quelles valeurs d’en-tête de réponse et de requête sont stockées dans vos fichiers journaux.

Par défaut, le champ du fichier journal personnalisé est appelé « x-ec_custom-1 ». Le nom de ce champ peut être personnalisé à partir de la page des paramètres de journalisation bruts.

Le format pour spécifier les en-têtes de requête et de réponse est défini comme suit :

Type d’en-tête|Format|Exemples
-|-|-
En-tête de requête|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
En-tête de réponse|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informations essentielles :

- Un champ de fichier journal personnalisé peut contenir n’importe quelle combinaison de champs d’en-tête et de texte brut.
- Les caractères valides pour ce champ sont les suivants : caractères alphanumériques (par exemple, 0 à 9, a à z et A à Z), tirets, deux-points, points-virgules, apostrophes, virgules, points, traits de soulignement, signes égal, parenthèses, crochets et espaces. Le symbole de pourcentage et les accolades sont autorisés uniquement lorsqu’ils sont utilisés pour spécifier un champ d’en-tête.
- L’orthographe de chaque champ d’en-tête spécifié doit correspondre au nom d’en-tête de requête/réponse souhaité.
- Si vous souhaitez spécifier plusieurs en-têtes, utilisez un séparateur pour indiquer chaque en-tête. Par exemple, vous pouvez utiliser une abréviation pour chaque en-tête :
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Valeur par défaut :** -

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>En-têtes de réponse de cache de débogage
**Objectif :** détermine si une réponse peut inclure l’en-tête de réponse X-EC-Debug qui fournit des informations sur la stratégie de cache de la ressource demandée.

Les en-têtes de réponse de cache de débogage seront inclus dans la réponse lorsque les conditions suivantes sont remplies :

- La fonctionnalité En-têtes de réponse de cache de débogage a été activée sur la requête souhaitée.
- La requête ci-dessus définit l’ensemble des en-têtes de réponse de cache de débogage qui seront inclus dans la réponse.

Des en-têtes de réponse de cache de débogage peuvent être demandés en incluant l’en-tête suivant et les directives voulues dans la requête :

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Exemple :**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valeur|Résultat
-|-
activé|Les requêtes des en-têtes de réponse de cache de débogage renvoient une réponse qui inclut l’en-tête X-EC-Debug.
Désactivé|L’en-tête de réponse X-EC-Debug est exclu de la réponse.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Âge maximal interne par défaut
**Objectif :** détermine l’intervalle d’âge maximal par défaut pour la revalidation du cache entre le point de présence et le serveur d’origine. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un point de présence ne vérifie si une ressource mise en cache correspond à la ressource stockée sur le serveur d’origine.

Informations essentielles :

- Cette action a lieu uniquement pour les réponses issues d’un serveur d’origine qui n’a pas attribué d’indication d’âge maximal (max-age) dans l’en-tête `Cache-Control` ou `Expires`.
- Cette action n’aura pas lieu pour les ressources qui ne sont pas considérées comme susceptibles d’être mises en cache.
- Cette action n’affecte pas les revalidations du cache entre le navigateur et le point de présence. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` ou `Expires` envoyés au navigateur, qui peuvent être personnalisés avec la fonctionnalité External Max-Age.
- Les résultats de cette action n’ont pas d’effet visible sur les en-têtes de réponse et le contenu retourné à partir des points de présence de votre contenu, mais cela peut avoir une incidence sur la quantité de trafic de revalidation envoyé à partir des points de présence vers votre serveur d’origine.
- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel un âge maximal interne par défaut peut être appliqué.
    - spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’intervalle d’âge maximal interne.

- Définissez l’unité de temps sur « Off » (Désactivé) pour affecter un intervalle d’âge maximal interne par défaut (default internal max-age interval) de 7 jours pour les requêtes auxquelles aucune indication d’âge maximal (max-age) n’a été affectée dans l’en-tête `Cache-Control` ou `Expires`.
- En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes : 
    - Edge 
    - Cname
    - Littéral d’en-tête de requête
    - Caractère générique d’en-tête de requête
    - Méthode de requête
    - Littéral de requête d’URL
    - Caractère générique de requête d’URL

**Valeur par défaut :** 7 jours

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Deny Access (403)
**Objectif** : détermine si toutes les requêtes sont rejetées avec une réponse 403 Interdit.

Valeur | Résultat
------|-------
activé| Entraîne le rejet de toutes les demandes qui répondent aux critères avec une réponse 403 Interdit.
Désactivé| Restaure le comportement par défaut. Le comportement par défaut consiste à autoriser le serveur d’origine à déterminer le type de réponse qui sera renvoyée.

**Comportement par défaut** : Désactivé

> [!TIP]
   > Une utilisation possible de cette fonctionnalité consiste à l’associer à une condition de correspondance d’en-tête de requête pour bloquer l’accès aux référents HTTP qui utilisent des liens incorporés vers votre contenu.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Expiration du traitement d’en-tête
**Objectif :** contrôle la génération des en-têtes `Expires` par un point de présence quand la fonctionnalité Obsolescence maximale externe est active.

La manière la plus simple d’atteindre ce type de configuration consiste à placer les fonctionnalités Âge maximal externe et Expiration du traitement d’en-tête dans la même instruction.

Valeur|Résultat
--|--
Remplacer|Garantit que les actions suivantes auront lieu :<br/>- Remplacement de l’en-tête `Expires` généré par le serveur d’origine.<br/>- Ajout de l’en-tête `Expires` généré par la fonctionnalité External Max-Age à la réponse.
Transmettre directement|Permet de garantir que l’en-tête `Expires` généré par la fonctionnalité External Max-Age ne soit jamais ajouté à la réponse. <br/> Si le serveur d’origine génère un en-tête `Expires`, celui-ci est transmis directement à l’utilisateur final. <br/>Si le serveur d’origine ne génère pas d’en-tête `Expires`, l’en-tête de réponse risque de ne pas contenir d’en-tête `Expires`.
Ajouter si manquant| Si aucun en-tête `Expires` n’a été transmis par le serveur d’origine, cette option ajoute l’en-tête `Expires` généré par la fonctionnalité External Max-Age. Cette option est utile pour garantir que toutes les ressources reçoivent un en-tête `Expires`.
Supprimer| Garantit qu’aucun en-tête `Expires` n’est inclus dans la réponse de l’en-tête. Si un en-tête `Expires` a déjà été attribué, il est supprimé de la réponse de l’en-tête.

**Comportement par défaut :** Remplacer

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Âge maximal externe
**Objectif :** détermine l’intervalle d’âge maximal pour la revalidation du cache entre le navigateur et le point de présence. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un navigateur puisse rechercher la nouvelle version d’une ressource à partir d’un point de présence.

L’activation de cette fonctionnalité génère des en-têtes `Cache-Control: max-age` et `Expires` à partir des points de présence, et les envoie au client HTTP. Par défaut, ces en-têtes remplacent ceux créés par le serveur d’origine. Toutefois, les fonctionnalités Traitement d’en-tête de contrôle de cache et Expiration du traitement d’en-tête peuvent servir à modifier ce comportement.

Informations essentielles :

- Cette action n’affecte pas les revalidations du cache entre le point de présence et le serveur d’origine. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` et `Expires` provenant du serveur d’origine, et peuvent être personnalisés avec les fonctionnalités Default Internal Max-Age et Force Internal Max-Age.
- Configurez cette fonctionnalité en spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.).
- Définissez cette fonctionnalité sur une valeur négative pour que les points de présence envoient un `Cache-Control: no-cache`, ainsi qu’une heure `Expires` passée avec chaque réponse envoyée au navigateur. Même si un client HTTP ne met pas en cache la réponse, ce paramètre n’affecte pas la capacité des points de présence à mettre en cache la réponse provenant du serveur d’origine.
- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Les en-têtes `Cache-Control` et `Expires` avec la réponse du serveur d’origine sont transmis directement au navigateur.

**Comportement par défaut :** Désactivé

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Suivre les redirections
**Objectif :** détermine si les requêtes peuvent être redirigées vers le nom d’hôte défini dans l’en-tête Location renvoyé par un serveur d’origine du client.

Informations essentielles :

- Les requêtes peuvent uniquement être redirigées vers les URL CNAME Edge qui correspondent à la même plateforme.

Valeur|Résultat
-|-
activé|Les requêtes peuvent être redirigées.
Désactivé|Les requêtes ne seront pas redirigées.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Forcer l’âge maximal interne
**Objectif :** détermine l’intervalle d’âge maximal pour la revalidation du cache entre le point de présence et le serveur d’origine. En d’autres termes, il s’agit de la durée s’écoulant avant qu’un point de présence ne vérifie si une ressource mise en cache correspond à la ressource stockée sur le serveur d’origine.

Informations essentielles :

- Cette fonctionnalité remplace l’intervalle d’âge maximal (max-age interval) défini dans les en-têtes `Cache-Control` ou `Expires` générés à partir d’un serveur d’origine.
- Cette fonctionnalité n’affecte pas les revalidations du cache entre le navigateur et le point de présence. Ces types de revalidations sont déterminés par les en-têtes `Cache-Control` ou `Expires` envoyés au navigateur.
- Cette fonctionnalité n’a pas d’effet visible sur la réponse fournie par un point de présence au demandeur. Toutefois, elle peut avoir une incidence sur la quantité de trafic de revalidation envoyée à partir des points de présence vers le serveur d’origine.
- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel un âge maximal interne sera appliqué.
    - spécifiant une valeur entière et en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’intervalle d’âge maximal de la requête.

- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Un intervalle d’âge maximal interne n’est pas attribué aux ressources demandées. Si l’en-tête d’origine ne contient pas d’instructions de mise en cache, la ressource est mise en cache en fonction du paramètre actif de la fonctionnalité Âge maximal interne par défaut.
- En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes : 
    - Edge 
    - Cname
    - Littéral d’en-tête de requête
    - Caractère générique d’en-tête de requête
    - Méthode de requête
    - Littéral de requête d’URL
    - Caractère générique de requête d’URL

**Comportement par défaut :** Désactivé

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>Prise en charge H.264 (téléchargement progressif HTTP)
**Objectif :** détermine les types de formats de fichier H.264 pouvant être utilisés pour diffuser du contenu.

Informations essentielles :

- Définissez un ensemble d’extensions de noms de fichier H.264 autorisées séparé par un espace dans l’option Extensions de fichier. Cette option remplacera le comportement par défaut. Maintenez la prise en charge de MP4 et F4V en incluant ces extensions de noms de fichier lors de la définition de cette option. 
- Veillez à inclure un point lorsque vous spécifiez une extension de nom de fichier (par exemple, .mp4 .f4v).

**Comportement par défaut :** Le téléchargement progressif HTTP prend en charge les formats multimédia MP4 et F4V par défaut.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Honorer la requête non-cache
**Objectif :** détermine si les requêtes non-cache d’un client HTTP sont transmises au serveur d’origine.

Une demande non-cache se produit lorsque le client HTTP envoie un en-tête `Cache-Control: no-cache` et/ou `Pragma: no-cache` dans la requête HTTP.

Valeur|Résultat
--|--
activé|Permet de transmettre une requête non cache d’un client HTTP au serveur d’origine. Le serveur d’origine retourne alors les en-têtes et le corps de la réponse via le point de présence au client HTTP.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à éviter la transmission des requêtes non cache au serveur d’origine.

Pour tout le trafic de production, il est fortement recommandé de laisser cette fonctionnalité désactivée par défaut. Dans le cas contraire, les serveurs d’origine ne sont pas protégés des utilisateurs finaux qui peuvent par inadvertance déclencher un grand nombre de requêtes non cache lors de l’actualisation des pages web ou des nombreux lecteurs multimédias populaires codés pour envoyer un en-tête non cache avec chaque requête vidéo. Néanmoins, il peut être utile d’appliquer cette fonctionnalité à certains répertoires de test ou intermédiaires hors production pour autoriser la collecte de contenu actualisé à partir du serveur d’origine à la demande.

L’état du cache qui sera signalé pour une requête, dont le transfert vers un serveur d’origine est autorisé en raison de cette fonctionnalité est TCP_Client_Refresh_Miss. Le rapport des états du cache, qui est disponible dans le module de création de rapports de base, fournit des informations statistiques par état de cache. Cela vous permet de suivre le nombre et le pourcentage de requêtes transférées vers un serveur d’origine en raison de cette fonctionnalité.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorer la requête non-cache d’origine
**Objectif :** détermine si le CDN ignore les directives suivantes remises par un serveur d’origine :

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informations essentielles :

- Configurez cette fonctionnalité en définissant une liste délimitée par un espace des codes d’état pour lequel les directives ci-dessus seront ignorées.
- L’ensemble des codes d’état valides pour cette fonctionnalité sont les suivants :200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, et 505.
- Désactivez cette fonctionnalité en lui attribuant une valeur vide.
- En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes : 
    - Edge 
    - Cname
    - Littéral d’en-tête de requête
    - Caractère générique d’en-tête de requête
    - Méthode de requête
    - Littéral de requête d’URL
    - Caractère générique de requête d’URL

**Comportement par défaut :** Le comportement par défaut consiste à respecter les directives ci-dessus.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorer les plages inacceptables 
**Objectif :** détermine la réponse à renvoyer aux clients quand une requête génère un code d’état 416 Plage demandée non satisfaisante.

Par défaut, ce code d’état est retourné lorsque la requête de plage d’octets spécifiée ne peut pas être satisfaite par un point de présence et qu’un champ d’en-tête de requête If-Range n’a pas été spécifié.

Valeur|Résultat
-|-
activé|Empêche les points de présence de répondre à une requête de plage d’octets non valide avec un code d’état 416 Plage demandée non satisfaisante. Au lieu de cela, les serveurs fournissent la ressource demandée et retournent le code 200 - OK au client.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à respecter le code d’état 416 Plage demandée non satisfaisante.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Obsolescence maximale interne
**Objectif :** contrôle la durée après l’expiration normale d’une ressource mise en cache pendant laquelle cette ressource peut être remise depuis un point de présence quand ce point de présence ne parvient pas à la revalider avec le serveur d’origine.

Normalement, lorsque l’élément max-age d’une ressource arrive à expiration, le point de présence envoie une requête de revalidation au serveur d’origine. Le serveur d’origine répond ensuite par un 304 Non modifié pour permettre au point de présence d’exploiter la ressource mise en cache, ou un 200 OK pour fournir au point de présence une version mise à jour de la ressource mise en cache.

Si le point de présence n’est pas en mesure d’établir une connexion avec le serveur d’origine lors de la tentative d’une telle revalidation, cette fonctionnalité Obsolescence maximale interne contrôle si et depuis combien de temps, le point de présence peut continuer à traiter la ressource now-stale.

Notez que cet intervalle de temps démarre à compter de l’expiration de l’âge maximal de la ressource et non lors de l’échec d’une revalidation. Par conséquent, la durée maximale pendant laquelle une ressource peut être traitée sans réussir la revalidation correspond à la durée spécifiée par la combinaison max-age/max-stale. Par exemple, si une ressource a été mise en cache à 9 h 00, avec un âge maximal (max-age) de 30 minutes et une obsolescence maximale de 15 minutes, l’échec d’une tentative de revalidation à 9 h 44 entraînerait la réception par l’utilisateur final de la ressource mise en cache obsolète, tandis que l’échec d’une tentative de revalidation à 9 h 46 entraînerait la réception par l’utilisateur final d’un 504 Expiration de la passerelle.

Les valeurs configurées pour cette fonctionnalité sont remplacées par les en-têtes `Cache-Control: must-revalidate` ou `Cache-Control: proxy-revalidate` reçus du serveur d’origine. Si l’un de ces en-têtes issus du serveur d’origine est reçu lors de la mise en cache initiale d’une ressource, le point de présence ne traite pas de ressource mise en cache obsolète. Dans ce cas, si le point de présence n’est pas en mesure de revalider avec le serveur d’origine après expiration de l’intervalle d’âge maximal de la ressource, le point de présence retourne une erreur 504 Expiration de la passerelle.

Informations essentielles :

- Configurez cette fonctionnalité en :
    - sélectionnant le code d’état pour lequel une obsolescence maximale sera appliquée.
    - spécifiant une valeur entière, puis en sélectionnant l’unité de temps souhaitée (par exemple, secondes, minutes, heures, etc.). Cette valeur définit l’obsolescence maximale qui sera appliquée.

- La définition de l’unité de temps sur « Désactivé » a pour effet de désactiver cette fonctionnalité. Une ressource mise en cache ne sera pas traitée au-delà de son délai d’expiration normal.
- En raison du type de suivi des paramètres de cache, cette fonctionnalité ne peut pas être associée avec les conditions de correspondance suivantes : 
    - Edge 
    - Cname
    - Littéral d’en-tête de requête
    - Caractère générique d’en-tête de requête
    - Méthode de requête
    - Littéral de requête d’URL
    - Caractère générique de requête d’URL

**Comportement par défaut :** deux minutes

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Journaliser la chaîne de requête
**objectif :** détermine si une chaîne de requête est stockée avec l’URL dans les journaux d’accès.

Valeur|Résultat
-|-
activé|Permet le stockage des chaînes de requête lors de l’enregistrement des URL dans un journal d’accès. Si une URL ne contient pas de chaîne de requête, cette option n’aura pas d’effet.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à ignorer les chaînes de requête lors de l’enregistrement des URL dans un journal d’accès.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Nombre maximal de requêtes toujours actives
**Objectif :** Définit le nombre maximal de requêtes pour une connexion toujours active avant sa fermeture.

Définir le nombre maximal de requêtes sur une valeur faible est fortement déconseillé et peut entraîner une dégradation des performances.

Informations essentielles :

- Spécifiez cette valeur sous forme d’entier.
- N’incluez pas de virgule ou de point dans la valeur spécifiée.

**Valeur par défaut :** 10 000 requêtes

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Modifier l’en-tête de requête client
**Objectif :** chaque requête contient un ensemble d’en-têtes de requête qui la décrivent. Cette fonctionnalité peut soit :

- Ajouter ou remplacer la valeur affectée à un en-tête de requête. Si l’en-tête de requête spécifié n’existe pas, cette fonctionnalité l’ajoute à la requête.
- Supprimez un en-tête de requête de la requête.

Les requêtes transmises à un serveur d’origine reflètent les modifications apportées par cette fonctionnalité.

Une des actions suivantes peut être effectuée sur un en-tête de requête :

Option|Description|Exemples
-|-|-
Append|La valeur spécifiée sera ajoutée à la fin de la valeur d’en-tête de requête existante.|**Valeur d’en-tête de requête (Client) :**Value1 <br/> **Valeur d’en-tête de requête (moteur de règles HTTP) :** Value2 <br/>**Nouvelle valeur d’en-tête de requête :** Value1Value2
Remplacer|La valeur d’en-tête de requête est définie sur la valeur spécifiée.|**Valeur d’en-tête de requête (Client) :**Value1 <br/>**Valeur d’en-tête de requête (moteur de règles HTTP) :** Value2 <br/>**Nouvelle valeur d’en-tête de requête :** Value2 <br/>
Supprimer|Supprime l’en-tête de requête spécifié.|**Valeur d’en-tête de requête (Client) :**Value1 <br/> **Modifier la configuration de l’en-tête de requête Client :** supprime l’en-tête de requête en question. <br/>**Résultat :** l’en-tête de requête spécifié ne sera pas transféré vers le serveur d’origine.

Informations essentielles :

- Veillez à ce que la valeur spécifiée dans l’option Nom corresponde exactement à l’en-tête de requête voulu.
- La casse n’est pas prise en compte pour l’identification d’un en-tête. Par exemple, l’une des variantes du nom de l’en-tête `Cache-Control` ci-dessous peut servir à l’identifier :
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Lorsque vous spécifiez un nom d’en-tête, utilisez uniquement des caractères alphanumériques, des tirets ou des traits de soulignement.
- La suppression d’un en-tête évite sa transmission à un serveur d’origine par les points de présence.
- Les en-têtes suivants sont réservés et ne peuvent pas être modifiés par cette fonctionnalité :
    - forwarded
    - host
    - via
    - Avertissement
    - x-forwarded-for
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Modifier l’en-tête de réponse client
Chaque réponse contient un ensemble d’en-têtes de réponse qui la décrivent. Cette fonctionnalité peut soit :

- Ajouter ou remplacer la valeur affectée à un en-tête de réponse. Si l’en-tête de requête spécifié n’existe pas, cette fonctionnalité l’ajoute à la réponse.
- Supprimez un en-tête de réponse de la réponse.

Par défaut, les valeurs d’en-tête de réponse sont définies par un serveur d’origine et par les points de présence.

Une des actions suivantes peut être effectuée sur un en-tête de réponse :

Option|Description|Exemples
-|-|-
Append|La valeur spécifiée sera ajoutée à la fin de la valeur d’en-tête de réponse existante.|**Valeur d’en-tête de réponse (Client) :**Value1 <br/> **Valeur d’en-tête de réponse (moteur de règles HTTP) :** Value2 <br/>**Nouvelle valeur d’en-tête de réponse :** Value1Value2
Remplacer|La valeur d’en-tête de réponse est définie sur la valeur spécifiée.|**Valeur d’en-tête de réponse (Client) :**Value1 <br/>**Valeur d’en-tête de réponse (moteur de règles HTTP) :** Value2 <br/>**Nouvelle valeur d’en-tête de réponse :** Value2 <br/>
Supprimer|Supprime l’en-tête de réponse spécifiée.|**Valeur d’en-tête de réponse (Client) :** Value1 <br/> **Modifier la configuration de l’en-tête de réponse client :** supprime l’en-tête de réponse en question. <br/>**Résultat :** l’en-tête de requête spécifié ne sera pas transféré vers le demandeur.

Informations essentielles :

- Veillez à ce que la valeur spécifiée dans l’option Nom corresponde exactement à l’en-tête de réponse voulu. 
- La casse n’est pas prise en compte pour l’identification d’un en-tête. Par exemple, l’une des variantes du nom de l’en-tête `Cache-Control` ci-dessous peut servir à l’identifier :
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- La suppression d’un en-tête évite son transfert au demandeur.
- Les en-têtes suivants sont réservés et ne peuvent pas être modifiés par cette fonctionnalité :
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - date
    - server
    - trailer
    - transfer-encoding
    - mettre à niveau
    - vary
    - via
    - Avertissement
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Partage de cache partiel
**Objectif :** détermine si une requête peut générer du contenu partiellement mis en cache.

Ce cache partiel peut ensuite être utilisé pour répondre aux nouvelles requêtes de ce contenu jusqu’à ce que le contenu demandé soit entièrement mis en cache.

Valeur|Résultat
-|-
activé|Les requêtes peuvent générer du contenu partiellement mis en cache.
Désactivé|Les requêtes peuvent uniquement générer une version entièrement mise en cache du contenu demandé.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prévalider le contenu mis en cache
**Objectif :** détermine si du contenu mis en cache peut faire l’objet d’une revalidation anticipée avant l’expiration de sa durée de vie.

Définissez la durée avant expiration de la durée de vie du contenu demandé au cours de laquelle il pourra faire l’objet d’une revalidation anticipée.

Informations essentielles :

- Sélectionnez « Désactivé », dans la mesure où l’unité de temps nécessite la revalidation une fois la durée de vie du contenu mis en cache expirée. Le temps ne doit pas être spécifié, et est ignoré.

**Comportement par défaut :** Désactivé. La revalidation peut uniquement avoir lieu après l’expiration de la durée de vie du contenu mis en cache.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>En-têtes spéciaux de proxy
**Objectif :** définit l’ensemble d’en-têtes de requête propres à CDN devant être transmis depuis un point de présence vers un serveur d’origine.

Informations essentielles :

- Chaque en-tête de requête CDN spécifique défini dans cette fonctionnalité sera transmis à un serveur d’origine.
- Empêchez la transmission d’un en-tête de requête propre à CDN vers un serveur d’origine en le supprimant de la liste.

**Comportement par défaut :** tous les en-têtes de requête propres à CDN sont transmis au serveur d’origine.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Actualiser les fichiers de cache de zéro octet
**Objectif :** détermine la façon dont les points de présence gèrent la requête d’un client HTTP liée à une ressource de cache de 0 octet.

Les valeurs autorisées sont :

Valeur|Résultat
--|--
activé|Force le point de présence à récupérer de nouveau la ressource à partir du serveur d’origine.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut consiste à fournir des ressources de cache valides sur requête.
Cette fonctionnalité n’est pas requise pour la mise en cache correcte et la distribution de contenu, mais peut être utile comme solution de contournement. Par exemple, des générateurs de contenu dynamique sur des serveurs d’origine peuvent entraîner par inadvertance l’envoi de réponses de 0 octet aux points de présence. Ces types de réponses sont généralement mis en cache par les points de réponse. Si vous savez qu’une réponse de 0 octet n’est jamais une réponse valide 

pour ce type de contenu, cette fonctionnalité peut empêcher le traitement de ces types de ressources sur vos clients.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Définir les codes d’état pouvant être mis en cache
**Objectif :** définit l’ensemble des codes d’état qui peuvent générer du contenu mis en cache.

Par défaut, la mise en cache est uniquement activée pour les réponses 200 OK.

Définissez un ensemble de codes d’état souhaités séparés par un espace.

Informations essentielles :

- Activez la fonctionnalité Ignorer la requête non-cache d’origine. Si cette fonctionnalité n’est pas activée, les réponses non 200 OK ne peuvent pas être mises en cache.
- L’ensemble des codes d’état valides pour cette fonctionnalité sont les suivants : 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, et 505.
- Cette fonctionnalité ne peut pas être utilisée pour désactiver la mise en cache des réponses qui génèrent un code d’état 200 OK.

**Comportement par défaut :** la mise en cache est activée uniquement pour les réponses qui génèrent un code d’état 200 OK.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Définir un en-tête personnalisé d’adresse IP client
**Objectif :** ajoute un en-tête personnalisé qui identifie le client demandeur par adresse IP à la requête.

L’option de nom d’en-tête définit le nom de l’en-tête de requête personnalisée où est stockée l’adresse IP du client.

Cette fonctionnalité permet à un serveur d’origine du client de trouver les adresses IP du client via un en-tête de requête personnalisé. Si la requête est traitée à partir du cache, le serveur d’origine n’est pas informé de l’adresse IP du client. Par conséquent, il est recommandé que cette fonctionnalité soit utilisée avec les ressources qui ne sont pas mises en cache.

Assurez-vous que le nom d’en-tête spécifié ne correspond pas à un des noms suivants :

- Noms d’en-tête de requête standard. Vous trouverez une liste de noms d’en-tête standard dans [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Noms d’en-tête réservés :
    - forwarded-for
    - host
    - vary
    - via
    - Avertissement
    - x-forwarded-for
    - Tous les noms d’en-tête commençant par « x-ec » sont réservés.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Rendre obsolète la remise de contenu en cas d’erreur
**Objectif :** détermine si du contenu mis en cache qui a expiré est remis quand une erreur se produit pendant la revalidation du cache ou la récupération du contenu demandé à partir du serveur d’origine du client.

Valeur|Résultat
-|-
activé|Le contenu est rendu obsolète pour le demandeur lorsqu’une erreur se produit lors d’une connexion à un serveur d’origine.
Désactivé|L’erreur du serveur d’origine est transférée au demandeur.

**Comportement par défaut :** Désactivé

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Rendre obsolète pendant la revalidation
**Objectif :** améliore les performances en permettant aux points de présence de rendre obsolète le contenu pour le demandeur pendant la revalidation.

Informations essentielles :

- Le comportement de cette fonctionnalité varie en fonction de l’unité de temps sélectionnée.
    - **Unité de temps :** spécifiez une durée, puis sélectionnez une unité de temps (par exemple, secondes, minutes, heures, etc.) pour permettre la distribution de contenu obsolète. Ce type d’installation permet au CDN d’étendre la durée pendant laquelle il peut fournir du contenu avant de demander une validation en fonction de la formule suivante : **TTL** + **Rendre obsolète pendant la revalidation** 
    - **Désactivé :** sélectionnez « Désactivé » pour demander la revalidation avant qu’une requête de contenu obsolète puisse être traitée.
        - Ne spécifiez pas de durée, car elle est inapplicable et sera ignorée.

**Comportement par défaut :** Désactivé. La revalidation doit intervenir avant que le contenu demandé puisse être traité.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Authentification de jeton
**Objectif :** détermine si l’authentification basée sur les jetons est appliquée à une requête.

Si l’authentification basée sur les jetons est activée, seules les requêtes qui fournissent un jeton chiffré et se conforment aux exigences définies par ce jeton seront respectées.

La clé de chiffrement qui est utilisée pour chiffrer et déchiffrer des valeurs de jeton est déterminée par les options de la clé primaire et de la clé de sauvegarde dans la page d’authentification du jeton. N’oubliez pas que les clés de chiffrement sont spécifiques de la plateforme.

Valeur | Résultat
------|---------
activé | Protège le contenu demandé avec l’authentification basée sur les jetons. Seules les demandes des clients qui fournissent un jeton valide et répondent aux exigences seront respectées. Les transactions FTP sont exclues de l’authentification basée sur les jetons.
Désactivé| Restaure le comportement par défaut. Le comportement par défaut consiste à permettre à votre configuration d’authentification basée sur les jetons de déterminer si une demande sera sécurisée.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Code de refus d’authentification de jeton
**Objectif :** détermine le type de réponse à retourner à un utilisateur quand une requête est refusée en raison de l’authentification basée sur les jetons.

Les codes de réponse disponibles sont répertoriés ci-dessous.

Code de la réponse|Nom de la réponse|Description
----------------|-----------|--------
301|Déplacé de façon permanente|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location.
302|Trouvé|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location. Ce code d’état est la méthode standard du secteur en termes d’exécution d’une redirection.
307|Redirection temporaire|Ce code d’état redirige les utilisateurs non autorisés vers l’URL spécifiée dans l’en-tête Location.
401|Non autorisé|L’association de ce code d’état avec l’en-tête de réponse WWW-Authenticate vous permet d’inviter un utilisateur à procéder à une authentification.
403|Interdit|Il s’agit du message d’état 403 Interdit standard qu’un utilisateur non autorisé voit lorsqu’il tente d’accéder à un contenu protégé.
404|Fichier introuvable|Ce code d’état indique que le client HTTP a été en mesure de communiquer avec le serveur, mais que le contenu demandé est introuvable.

#### <a name="url-redirection"></a>Redirection d’URL

Cette fonctionnalité prend en charge la redirection de l’URL vers une URL définie par l’utilisateur lorsqu’elle est configurée pour renvoyer un code d’état 3xx. Cette URL définie par l’utilisateur peut être spécifiée en effectuant les opérations suivantes :

1. Sélectionnez un code de réponse 3xx pour la fonctionnalité Code de refus d’authentification de jeton.
2. Sélectionnez « Location » à partir de l’option de nom de l’en-tête facultatif.
3. Définissez l’option Optional Header Value (Valeur d’en-tête facultatif) sur l’URL souhaitée.

Si une URL n’est pas définie pour un code d’état 3xx, la page de réponse standard pour un code d’état 3xx sera renvoyée à l’utilisateur.

La redirection des URL est uniquement applicable pour les codes de réponse 3xx.

L’option de valeur d’en-tête facultative prend en charge les caractères alphanumériques, les guillemets et les espaces.

#### <a name="authentication"></a>Authentification

Cette fonctionnalité prend en charge la capacité à inclure l’en-tête WWW-Authenticate lors de la réponse à une demande non autorisée pour un contenu protégé par l’authentification basée sur les jetons. Si l’en-tête WWW-Authenticate a été défini sur « de base » dans votre configuration, l’utilisateur non autorisé est invité à entrer des informations d’identification de compte.

La configuration ci-dessus peut être obtenue en effectuant les opérations suivantes :

1. Sélectionnez « 401 » comme code de réponse pour la fonctionnalité Code de refus d’authentification de jeton.
2. Sélectionnez « WWW-Authenticate » à partir de l’option de nom de l’en-tête facultatif.
3. Définissez l’option Optional Header Value (Valeur d’en-tête facultatif) sur « de base ».

L’en-tête WWW-Authenticate est uniquement applicable pour les codes de réponse 401.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Ignorer la casse de l’URL pour l’authentification de jeton
**Objectif :** détermine si les comparaisons d’URL effectuées par l’authentification basée sur les jetons respectent la casse.

Les paramètres affectés par cette fonction sont :

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Les valeurs autorisées sont :

Valeur|Résultat
---|----
activé|Force le point de présence à ignorer la casse lors de la comparaison des URL pour les paramètres d’authentification basée sur les jetons.
Désactivé|Restaure le comportement par défaut. Le comportement par défaut pour les comparaisons d’URL pour l’authentification du jeton consiste à respecter la casse.

**Comportement par défaut** : Désactivé.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Paramètre d’authentification de jeton
**Objectif :** détermine si le paramètre de chaîne de requête de l’authentification basée sur les jetons doit être renommé.

Informations essentielles :

- L’option Valeur définit le nom de paramètre de chaîne de requête par le biais duquel un jeton peut être spécifié.
- L’option Valeur ne peut pas être définie sur « ec_token ».
- Assurez-vous que le nom défini dans l’option Valeur contient uniquement des caractères d’URL valides.

Valeur|Résultat
----|----
activé|L’option Valeur définit le nom de paramètre de chaîne de requête par le biais duquel les jetons doivent être définis.
Désactivé|Un jeton peut être spécifié comme un paramètre de chaîne de requête non défini dans l’URL de requête.

**Comportement par défaut** : Désactivé. Un jeton peut être spécifié comme un paramètre de chaîne de requête non défini dans l’URL de requête.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Redirection d'URL
**Objectif :** redirige les requêtes via l’en-tête Location.

La configuration de cette fonctionnalité nécessite de définir les options suivantes :

Option|Description
-|-
Code|Sélectionnez le code de réponse qui sera renvoyé au demandeur.
Source et modèle| Ces paramètres définissent un modèle d’URI de requête qui identifie le type de requêtes pouvant être redirigées. Seules les requêtes dont l’URL satisfait aux deux critères suivants seront redirigées : <br/> <br/> **Source (ou point d’accès au contenu) :** sélectionnez un chemin d’accès relatif qui identifie un serveur d’origine. Il s’agit de la section « /XXXX/ » et de votre nom de point de terminaison. <br/> **Source (modèle) :** Un modèle qui identifie les requêtes via un chemin d’accès relatif doit être défini. Ce modèle d’expression régulière doit définir un chemin d’accès commençant directement après le point d’accès au contenu sélectionné précédemment (voir ci-dessus). <br/> - Vérifiez que les critères d’URI de requête (c’est-à-dire, Source et Modèle) définis précédemment ne sont pas en conflit avec les conditions de correspondance définies pour cette fonctionnalité. <br/> - Spécifiez un modèle. Si vous utilisez une valeur vide comme modèle, toutes les chaînes sont mises en correspondance.
Destination| Définissez l’URL vers laquelle les requêtes ci-dessus seront redirigées. <br/> Construisez dynamiquement cette URL à l’aide des éléments suivants : <br/> - Un modèle d’expression régulière <br/>- Des variables HTTP <br/> Remplacez les valeurs capturées dans le modèle source dans le modèle de destination à l’aide de $_n_ où _n_ identifie une valeur par l’ordre dans lequel elle a été capturée. Par exemple, $1 représente la première valeur capturée dans le modèle source, tandis que $2 représente la deuxième valeur. <br/> 
Il est fortement recommandé d’utiliser une URL absolue. L’utilisation d’une URL relative peut rediriger les URL CDN vers un chemin d’accès non valide.

**Exemple de scénario**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge qui correspond à l’URL CDN de base suivante : http://marketing.azureedge.net/brochures

Les requêtes éligibles sont redirigées vers l’URL CNAME Edge de base suivante : http://cdn.mydomain.com/resources

Cette redirection d’URL peut être obtenue via la configuration suivante :![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Points essentiels :**

- La fonctionnalité Redirection d’URL définit les URL de requête à rediriger. Par conséquent, aucune condition de correspondance supplémentaire n’est requise. Bien que la condition de correspondance ait été définie en tant que « Toujours », seules les requêtes pointant vers le dossier « brochures » du serveur d’origine du client « marketing » s’afficheront. 
- Toutes les requêtes correspondantes seront redirigées vers l’URL CNAME Edge définie dans l’option Destination. 
    - Exemple de scénario n°1 : 
        - Exemple de requête (URL CDN) : http://marketing.azureedge.net/brochures/widgets.pdf 
        - URL de la requête (après la redirection) : http://cdn.mydomain.com/resources/widgets.pdf  
    - Exemple de scénario n°2 : 
        - Exemple de requête (URL CNAME Edge) : http://marketing.mydomain.com/brochures/widgets.pdf 
        - URL de la requête (après la redirection) : http://cdn.mydomain.com/resources/widgets.pdf
    - Exemple de scénario n°3 : 
        - Exemple de requête (URL CNAME Edge) : http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL de la requête (après la redirection) : http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- La variable Modèle de requête (%{scheme}) a été utilisée dans l’option Destination. Cela garantit que le modèle de requête reste inchangé après la redirection.
- Les segments d’URL qui ont été capturés à partir de la requête sont ajoutés à la nouvelle URL via « $1 ».

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Réécriture d’URL
**Objectif :** réécrit l’URL de la requête.

Informations essentielles :

- La configuration de cette fonctionnalité nécessite de définir les options suivantes :

Option|Description
-|-
 Source et modèle | Ces paramètres définissent un modèle d’URI de requête qui identifie le type de requêtes pouvant être réécrites. Seules les requêtes dont l’URL satisfait aux deux critères suivants seront réécrites : <br/>     - **Source (ou point d’accès au contenu) :** sélectionnez un chemin d’accès relatif qui identifie un serveur d’origine. Il s’agit de la section « /XXXX/ » et de votre nom de point de terminaison. <br/> - **Source (modèle) :** un modèle qui identifie les requêtes via un chemin d’accès relatif doit être défini. Ce modèle d’expression régulière doit définir un chemin d’accès commençant directement après le point d’accès au contenu sélectionné précédemment (voir ci-dessus). <br/> Vérifiez que les critères d’URI de requête (c’est-à-dire, Source et Modèle) définis précédemment ne sont pas en conflit avec les conditions de correspondance définies pour cette fonctionnalité. Spécifiez un modèle. Si vous utilisez une valeur vide comme modèle, toutes les chaînes sont mises en correspondance. 
 Destination  |Définissez l’URL relative vers laquelle les requêtes ci-dessus seront réécrites en : <br/>    1. Sélectionnant un point d’accès au contenu qui identifie un serveur d’origine. <br/>    2. Définissant un chemin d’accès relatif à l’aide des éléments suivants : <br/>        - Un modèle d’expression régulière <br/>        - Des variables HTTP <br/> <br/> Remplacez les valeurs capturées dans le modèle source dans le modèle de destination à l’aide de $_n_ où _n_ identifie une valeur par l’ordre dans lequel elle a été capturée. Par exemple, $1 représente la première valeur capturée dans le modèle source, tandis que $2 représente la deuxième valeur. 
 Cette fonctionnalité permet aux points de présence de réécrire l’URL sans effectuer de redirection classique. Cela signifie que le demandeur reçoit un code de réponse identique à celui reçu si l’URL réécrite avait été demandée.

**Exemple de scénario nº 1**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge qui correspond à l’URL CDN de base suivante : http://marketing.azureedge.net/brochures/

Les requêtes éligibles sont redirigées vers l’URL CNAME Edge de base suivante : http://MyOrigin.azureedge.net/resources/

Cette redirection d’URL peut être obtenue via la configuration suivante :![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exemple de scénario nº 2**

Dans cet exemple, nous expliquons comment rediriger une URL CNAME Edge en majuscules vers son équivalent en minuscules à l’aide d’expressions régulières.

Cette redirection d’URL peut être obtenue via la configuration suivante :![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Points essentiels :**

- La fonctionnalité Réécriture d’URL définit les URL de demande à réécrire. Par conséquent, aucune condition de correspondance supplémentaire n’est requise. Bien que la condition de correspondance ait été définie sur « Toujours », seules les demandes pointant vers le dossier « brochures » du serveur d’origine du client « marketing » seront réécrites.

- Les segments d’URL qui ont été capturés à partir de la requête sont ajoutés à la nouvelle URL via « $1 ».

#### <a name="compatibility"></a>Compatibilité

Cette fonctionnalité inclut des critères de correspondance devant être remplis avant de pouvoir l’appliquer à une requête. Pour empêcher la définition de critères de correspondance conflictuels, cette fonctionnalité est incompatible avec les conditions de correspondance suivantes :

- Numéro AS
- Origine CDN
- Adresse IP du client
- Origine client
- Modèle de requête
- Répertoire du chemin d’URL
- Extension du chemin d’URL
- Nom de fichier du chemin d’URL
- Littéral du chemin d’URL
- Expression régulière du chemin d’URL
- Caractère générique du chemin d’URL
- Littéral de requête d’URL
- Paramètre de requête d’URL
- Expression régulière de requête d’URL
- Caractère générique de requête d’URL

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Variable utilisateur
**Objectif :** à usage interne uniquement.

[Revenir en haut](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Étapes suivantes
* [Informations de référence du moteur de règles](cdn-rules-engine-reference.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Conditions de correspondance du moteur de règles](cdn-rules-engine-reference-match-conditions.md)
* [Remplacement du comportement HTTP à l’aide du moteur de règles](cdn-rules-engine.md)
* [Vue d’ensemble d’Azure CDN](cdn-overview.md)
