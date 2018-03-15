---
title: "Conditions de correspondance du moteur de règles Azure CDN | Microsoft Docs"
description: "Documentation de référence des conditions de correspondance du moteur de règles Azure Content Delivery Network."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 08845355be0bfb7e7dde52d19949fee4a68ed54b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Conditions de correspondance du moteur de règles Azure CDN
Cet article fournit les descriptions détaillées des conditions de correspondance disponibles pour le [moteur de règles](cdn-rules-engine.md) Azure Content Delivery Network (CDN).

La deuxième partie d’une règle est la condition de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles un ensemble de fonctionnalités est exécuté.

Par exemple, vous pouvez utiliser une condition de correspondance pour les tâches suivantes :
- Filtrer les requêtes ciblant le contenu dans un emplacement particulier.
- Filtrer les requêtes générées à partir d’une adresse IP ou d’un pays en particulier.
- Filtrer les requêtes d’après les informations d’en-tête.

## <a name="always-match-condition"></a>Condition de correspondance Toujours

La condition de correspondance Toujours applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

NOM | Objectif
-----|--------
[Toujours](#always) | Applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

## <a name="device-match-condition"></a>Condition de correspondance Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.  

NOM | Objectif
-----|--------
[Appareil](#device) | Identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.

## <a name="location-match-conditions"></a>Conditions de correspondance Emplacement

Les conditions de correspondance Emplacement identifient les requêtes selon l’emplacement du demandeur.

NOM | Objectif
-----|--------
[Numéro AS](#as-number) | Identifie les requêtes issues d’un réseau particulier.
[Pays](#country) | Identifie les requêtes provenant des pays spécifiés.

## <a name="origin-match-conditions"></a>Conditions de correspondance Origine

Les conditions de correspondance Origine identifient les requêtes qui pointent vers le stockage Content Delivery Network ou le serveur d’origine d’un client.

NOM | Objectif
-----|--------
[Origine CDN](#cdn-origin) | Identifie les requêtes de contenu stocké dans le stockage Content Delivery Network.
[Origine client](#customer-origin) | Identifie les requêtes de contenu stocké sur le serveur d’origine d’un client spécifique.

## <a name="request-match-conditions"></a>Conditions de correspondance Requête

Les conditions de correspondance Requête identifient les requêtes selon leurs propriétés.

NOM | Objectif
-----|--------
[Adresse IP du client](#client-ip-address) | Identifie les requêtes issues d’une adresse IP particulière.
[Paramètre de cookie](#cookie-parameter) | Recherche la valeur spécifiée dans les cookies associés à chaque requête.
[Expression régulière de paramètre de cookie](#cookie-parameter-regex) | Recherche l’expression régulière spécifiée dans les cookies associés à chaque requête.
[CNAME de périmètre](#edge-cname) | Identifie les requêtes qui pointent vers un CNAME de périphérie spécifique.
[Domaine de référence](#referring-domain) | Identifie les requêtes qui ont été référencées à partir des noms d’hôte spécifiés.
[Littéral d’en-tête de requête](#request-header-literal) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur spécifiée.
[Expression régulière d’en-tête de requête](#request-header-regex) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
[Caractère générique d’en-tête de requête](#request-header-wildcard) | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond au modèle spécifié.
[Méthode de requête](#request-method) | Identifie les requêtes par leur méthode HTTP.
[Modèle de requête](#request-scheme) | Identifie les requêtes par leur protocole HTTP.

## <a name="url-match-conditions"></a>Conditions de correspondance URL

Les conditions de correspondance URL identifient les requêtes selon leurs URL.

NOM | Objectif
-----|--------
[Répertoire du chemin d’URL](#url-path-directory) | Identifie les requêtes par leur chemin relatif.
[Extension du chemin d’URL](#url-path-extension) | Identifie les requêtes par leur extension de nom de fichier.
[Nom de fichier du chemin d’URL](#url-path-filename) | Identifie les requêtes par leur nom de fichier.
[Littéral du chemin d’URL](#url-path-literal) | Compare le chemin relatif d’une requête à la valeur spécifiée.
[Expression régulière du chemin d’URL](#url-path-regex) | Compare le chemin relatif d’une requête à l’expression régulière spécifiée.
[Caractère générique du chemin d’URL](#url-path-wildcard) | Compare le chemin relatif d’une requête au modèle spécifié.
[Littéral de requête d’URL](#url-query-literal) | Compare la chaîne de requête d’une requête à la valeur spécifiée.
[Paramètre de requête d’URL](#url-query-parameter) | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond au modèle spécifié.
[Expression régulière de requête d’URL](#url-query-regex) | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
[Caractère générique de requête d’URL](#url-query-wildcard) | Compare les valeurs spécifiées à la chaîne de requête de la requête.


## <a name="reference-for-rules-engine-match-conditions"></a>Informations de référence des conditions de correspondance du moteur de règles

---
### <a name="always"></a>Toujours

La condition de correspondance Toujours applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Numéro AS 
Le réseau Numéro AS est défini par son numéro de système autonome (ASN). 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Numéro AS est remplie :
- **Correspond** : nécessite que l’ASN du réseau client corresponde à l’un des ASN spécifiés. 
- **Ne correspond pas** : nécessite que l’ASN du réseau client ne corresponde pas aux ASN spécifiés.

Informations essentielles :
- Spécifiez plusieurs ASN en les séparant par un espace. Par exemple, 64514 64515 correspond aux requêtes qui proviennent de 64514 ou 64515.
- Certaines requêtes peuvent ne pas retourner d’ASN valide. Un point d’interrogation (?) correspond aux requêtes pour lesquelles un ASN valide ne peut pas être déterminé.
- Spécifiez l’ASN entier du réseau souhaité. Les valeurs partielles ne sont pas prises en compte.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Origine CDN
La condition de correspondance Origine CDN est remplie quand les deux conditions suivantes sont remplies :
- Le contenu du stockage CDN a été demandé.
- L’URI de la requête utilise le type de point d’accès de contenu (par exemple, /000001) qui est défini dans cette condition de correspondance :
  - URL CDN : l’URI de la requête doit contenir le point d’accès de contenu sélectionné.
  - URL du CNAME de périmètre : La configuration du CNAME de périmètre correspondant doit pointer vers le point d’accès de contenu sélectionné.
  
Informations essentielles :
 - Le point d’accès de contenu identifie le service qui doit traiter le contenu demandé.
 - N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine CDN avec une condition de correspondance Origine client crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine CDN ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Adresse IP du client
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Adresse IP du client est remplie :
- **Correspond** : nécessite que l’adresse IP du client corresponde à l’une des adresses IP spécifiées. 
- **Ne correspond pas** : nécessite que l’adresse IP du client ne corresponde pas aux adresses IP spécifiées. 

Informations essentielles :
- Utilisez la notation CIDR.
- Spécifiez plusieurs adresses IP et/ou blocs d’adresses IP en les séparant par un espace. Par exemple : 
  - **Exemple IPv4** : 1.2.3.4 10.20.30.40 correspond aux requêtes qui proviennent de l’adresse 1.2.3.4 ou 10.20.30.40.
  - **Exemple IPv6** : 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 correspond aux requêtes qui proviennent de l’adresse 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- La syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe. Par exemple : 
  - **Exemple IPv4** : 5.5.5.64/26 correspond aux requêtes qui proviennent des adresses 5.5.5.64 à 5.5.5.127.
  - **Exemple IPv6** : 1:2:3:/48 correspond aux requêtes qui proviennent des adresses 1:2:3:0:0:0:0:0 à 1:2:3:ffff:ffff:ffff:ffff:ffff.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Paramètre de cookie
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Paramètre de cookie est remplie.
- **Correspond** : Nécessite qu’une requête contienne le cookie spécifié avec une valeur qui correspond à au moins une des valeurs définies dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :
- Nom du cookie : 
  - Étant donné que des valeurs de caractère générique, y compris les astérisques (*), ne sont pas prises en charge lorsque vous spécifiez un nom de cookie, seules les correspondances de nom de cookie exactes sont éligibles pour la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie : 
  - Spécifiez plusieurs valeurs de cookie en les séparant par un espace.
  - Une valeur de cookie peut utiliser des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values). 
  - Si aucune valeur de caractère générique n’a été spécifiée, seule une correspondance exacte satisfait cette condition de correspondance. Par exemple, « Valeur » correspond à « Valeur », mais pas à « Valeur1 » ni « Valeur2 ».
  - Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Expression régulière de paramètre de cookie
La condition de correspondance Expression régulière de paramètre de cookie définit une valeur et un nom de cookie. Vous pouvez utiliser des [expressions régulières](cdn-rules-engine-reference.md#regular-expressions) pour définir la valeur de cookie souhaitée. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière de paramètre de cookie est remplie.
- **Correspond** : Nécessite que la requête contienne le cookie spécifié avec une valeur qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.
  
Informations essentielles :
- Nom du cookie : 
  - Étant donné que des expressions régulières et des valeurs de caractère générique, y compris les astérisques (*), ne sont pas prises en charge lorsque vous spécifiez un nom de cookie, seules les correspondances de nom de cookie exactes sont éligibles pour la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie : 
  - Une valeur de cookie peut contenir des expressions régulières.
  - Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Pays
Vous pouvez spécifier un pays à l’aide de son code de pays. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Pays est remplie :
- **Correspond** : nécessite que la requête contienne les codets de pays spécifiés. 
- **Ne correspond pas** : nécessite que la requête ne contienne pas les codets de pays spécifiés.

Informations essentielles :
- Spécifiez plusieurs codes de pays en les séparant par un espace.
- Les caractères génériques ne sont pas pris en charge dans la spécification d’un code de pays.
- Les codes de pays « EU » et « AP » n’incluent pas toutes les adresses IP de ces régions.
- Certaines requêtes peuvent ne pas retourner de code de pays valide. Un point d’interrogation (?) correspond aux requêtes pour lesquelles un code de pays valide ne peut pas être déterminé.
- Les codes de pays respectent la casse.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implémentation du filtrage par pays à l’aide du moteur de règles
Cette condition de correspondance vous permet d’effectuer une multitude de personnalisations en fonction de l’emplacement d’origine d’une requête. Par exemple, le comportement de la fonctionnalité de filtrage par pays peut être répliqué via la configuration suivante :

- Correspondance de caractère générique du chemin d’URL : définissez la [condition de correspondance Caractère générique du chemin d’URL](#url-path-wildcard) sur le répertoire qui sera sécurisé. 
    Ajoutez un astérisque à la fin du chemin d’accès relatif pour vous assurer que l’accès à tous ses enfants sera limité par cette règle.

- Correspondance de pays : définissez la condition de correspondance Pays sur l’ensemble de pays souhaité.
   - Autoriser : définissez la condition de correspondance Pays sur **Ne correspond pas** pour autoriser uniquement l’accès des pays spécifiés au contenu stocké dans l’emplacement défini par la condition de correspondance Caractère générique du chemin d’URL.
   - Bloquer : définissez la condition de correspondance Pays sur **Correspond** pour bloquer l’accès des pays spécifiés au contenu stocké dans l’emplacement défini par la condition de correspondance Caractère générique du chemin d’URL.

- Deny Access (403) Feature (Fonctionnalité Refuser l’accès (403)) : activez [Deny Access (403) feature](cdn-rules-engine-reference-features.md#deny-access-403) (Fonctionnalité Refuser l’accès (403)) pour répliquer la partie autorisée ou bloquée de la fonctionnalité de filtrage par pays.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origine client

Informations essentielles : 
- La condition de correspondance Origine client est satisfaite que le contenu soit demandé à l’aide d’une URL CDN ou d’une URL CNAME de périmètre qui pointe vers l’origine client sélectionnée.
- Une configuration d’origine client référencée par une règle ne peut pas être supprimée à partir de la page Origine client. Avant de supprimer une configuration d’origine client, vérifiez que les configurations suivantes ne la référencent pas :
  - Condition de correspondance Origine client
  - Configuration de CNAME de périmètre
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine client avec une condition de correspondance Origine CDN crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine client ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés. La détection de périphérique mobile est obtenue par le biais de [WURFL](http://wurfl.sourceforge.net/). 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Appareil est remplie :
- **Correspond**: nécessite que l’appareil du demandeur corresponde à la valeur spécifiée. 
- **Ne correspond pas**: nécessite que l’appareil du demandeur ne corresponde pas à la valeur spécifiée.

Informations essentielles :

- Utilisez l’option **Ignorer la casse** pour spécifier si la valeur indiquée respecte la casse.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

#### <a name="string-type"></a>Type de chaîne
En général, une fonctionnalité WURFL accepte n’importe quelle combinaison de chiffres, de lettres et de symboles. En raison de la nature flexible de cette fonctionnalité, vous devez choisir la façon dont la valeur associée à cette condition de correspondance est interprétée. Le tableau suivant décrit l’ensemble d’options disponibles :

type     | Description
---------|------------
Littéral  | Sélectionnez cette option pour empêcher la plupart des caractères de prendre une signification particulière à l’aide de leur [valeur littérale](cdn-rules-engine-reference.md#literal-values).
Caractère générique | Sélectionnez cette option pour utiliser tous les [caractères génériques] ([valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values)).
Expression régulière    | Sélectionnez cette option pour utiliser des [expressions régulières](cdn-rules-engine-reference.md#regular-expressions). Les expressions régulières sont utiles pour la définition d’un modèle de caractères.

#### <a name="wurfl-capabilities"></a>Fonctionnalités WURFL
Une fonctionnalité WURFL fait référence à une catégorie qui décrit les appareils mobiles. La fonctionnalité sélectionnée détermine le type de description de l’appareil mobile qui est utilisé pour identifier les requêtes.

Le tableau suivant répertorie les fonctionnalités WURFL et leurs variables pour le moteur de règles.
<br>
> [!NOTE] 
> Les variables suivantes sont prises en charge dans les fonctionnalités **Modifier l’en-tête de requête client** et **Modifier l’en-tête de réponse client**.

Fonctionnalité | Variable | Description | Exemples de valeurs
-----------|----------|-------------|----------------
Nom de la marque | %{wurfl_cap_brand_name} | Chaîne qui indique le nom de la marque de l’appareil. | Samsung
Système d’exploitation de l’appareil | %{wurfl_cap_device_os} | Chaîne qui indique le système d’exploitation installé sur l’appareil. | IOS
Version du système d’exploitation de l’appareil | %{wurfl_cap_device_os_version} | Chaîne qui indique le numéro de version du système d’exploitation installé sur l’appareil. | 1.0.1
Orientation double | %{wurfl_cap_dual_orientation} | Valeur booléenne qui indique si l’appareil prend en charge l’orientation double. | true
DTD HTML par défaut | %{wurfl_cap_html_preferred_dtd} | Chaîne qui indique la définition de type de document (DTD) par défaut du périphérique mobile pour le contenu HTML. | Aucun<br/>xhtml_basic<br/>html5
Incorporation d’images | %{wurfl_cap_image_inlining} | Valeur booléenne qui indique si l’appareil prend en charge les images codées en Base64. | false
Est Android | %{wurfl_vcap_is_android} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation Android. | true
Est IOS | %{wurfl_vcap_is_ios} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation iOS. | false
Est une télévision intelligente | %{wurfl_cap_is_smarttv} | Valeur booléenne qui indique si l’appareil est une télévision intelligente. | false
Est un smartphone | %{wurfl_vcap_is_smartphone} | Valeur booléenne qui indique si l’appareil est un smartphone. | true
Est une tablette | %{wurfl_cap_is_tablet} | Valeur booléenne qui indique si l’appareil est une tablette. Cette description est indépendante du système d’exploitation. | true
Est un appareil sans fil | %{wurfl_cap_is_wireless_device} | Valeur booléenne qui indique si l’appareil est considéré comme un appareil sans fil. | true
Nom marketing | %{wurfl_cap_marketing_name} | Chaîne qui indique le nom marketing de l’appareil. | BlackBerry 8100 Pearl
Navigateur mobile | %{wurfl_cap_mobile_browser} | Chaîne qui indique le navigateur utilisé pour demander du contenu à partir de l’appareil. | Chrome
Version du navigateur mobile | %{wurfl_cap_mobile_browser_version} | Chaîne qui indique la version du navigateur utilisé pour demander du contenu à partir de l’appareil. | 31
Nom du modèle | %{wurfl_cap_model_name} | Chaîne qui indique le nom du modèle de l’appareil. | s3
Téléchargement progressif | %{wurfl_cap_progressive_download} | Valeur booléenne qui indique si l’appareil prend en charge la lecture d’un fichier audio/vidéo pendant son téléchargement. | true
Date de lancement | %{wurfl_cap_release_date} | Chaîne qui indique l’année et le mois de l’ajout de l’appareil à la base de données WURFL.<br/><br/>Format : `yyyy_mm` | 2013_december
Hauteur de résolution | %{wurfl_cap_resolution_height} | Entier qui indique la hauteur de l’appareil en pixels. | 768
Largeur de résolution | %{wurfl_cap_resolution_width} | Entier qui indique la largeur de l’appareil en pixels. | 1 024

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Cname Edge
Informations essentielles : 
- La liste des CNAME de périmètre disponibles est limitée à ceux qui ont été configurés sur la page des CNAME de périmètre pour la plateforme sur laquelle le moteur de règles est configuré.
- Avant de supprimer une configuration de CNAME de périmètre, vérifiez qu’une condition de correspondance CNAME de périmètre ne la référence pas. Les configurations de CNAME de périmètre qui ont été définies dans une règle ne peuvent pas être supprimées à partir de la page Enregistrements CNAME de périmètre. 
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance CNAME de périmètre avec une condition de correspondance Origine client crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance CNAME de périmètre ne peuvent pas être combinées à l’aide d’une instruction AND IF.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Domaine de référence
Le nom d’hôte associé au point d’accès par lequel le contenu a été demandé détermine si la condition Domaine de référence est remplie. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Domaine de référence est remplie :
- **Correspond**: nécessite que le nom d’hôte de référence corresponde aux valeurs spécifiées. 
- **Ne correspond pas**: nécessite que le nom d’hôte de référence ne corresponde pas à la valeur spécifiée.

Informations essentielles :
- Spécifiez plusieurs noms d’hôte en les séparant par un espace.
- Cette condition de correspondance prend en charge les [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values).
- Si la valeur spécifiée ne contient pas d’astérisque, elle doit correspondre à la valeur exacte du nom d’hôte du point d’accès. Par exemple, « mydomain.com » ne correspond pas à « www.mydomain.com ».
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Littéral d’en-tête de requête
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral d’en-tête de requête est remplie.
- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre à celle définie dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :
- Les comparaisons de noms d’en-tête respectent toujours la casse. Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Expression régulière d’en-tête de requête
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière d’en-tête de requête est remplie.
- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre au modèle défini dans [l’expression régulière](cdn-rules-engine-reference.md#regular-expressions) spécifiée.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :
- Nom de l’en-tête : 
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Remplacez les espaces dans le nom d’en-tête par « % 20 ». 
- Valeur de l’en-tête : 
  - Une valeur d’en-tête peut contenir des expressions régulières.
  - Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - La condition de correspondance est remplie uniquement quand une valeur d’en-tête correspond exactement à au moins un des modèles spécifiés.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne 

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Caractère générique d’en-tête de requête
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique d’en-tête de requête est remplie.
- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre à au moins une valeur définie dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur spécifiée.
  
Informations essentielles :
- Nom de l’en-tête : 
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Les espaces dans le nom d’en-tête doivent être remplacés par « %20 ». Vous pouvez aussi utiliser cette valeur pour spécifier des espaces dans une valeur d’en-tête.
- Valeur de l’en-tête : 
  - Une valeur d’en-tête peut utiliser des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values).
  - Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - Cette condition de correspondance est remplie quand une valeur d’en-tête correspond exactement à au moins un des modèles spécifiés.
  - Spécifiez plusieurs valeurs en les séparant par un espace.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Méthode de requête
La condition de correspondance Méthode de requête est respectée uniquement quand les ressources sont demandées par la méthode de requête sélectionnée. Les méthodes de requête disponibles sont :
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- SUPPRIMER 
- TRACE 
- CONNECT 

Informations essentielles :
- Par défaut, seule la méthode de requête GET peut générer le contenu mis en cache sur le réseau. Toutes les autres méthodes de requête sont traitées par proxy sur le réseau.
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Modèle de requête
La condition de correspondance Modèle de requête est respectée uniquement quand les ressources sont demandées par le protocole sélectionné. Les protocoles disponibles sont : 
- HTTP
- HTTPS

Informations essentielles :
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>Répertoire du chemin d’URL
Identifie une requête par son chemin d’accès relatif, qui exclut le nom de fichier de la ressource demandée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Répertoire du chemin d’URL est remplie.
- **Correspond** : nécessite que la requête contienne un chemin d’URL relatif, sans le nom de fichier, qui correspond au modèle d’URL spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL relatif, sans le nom de fichier, qui ne correspond pas au modèle d’URL spécifié.

Informations essentielles :
- Utilisez l’option **Relative to** (Relatif à) pour spécifier si la comparaison de l’URL commence avant ou après le point d’accès au contenu. Le point d’accès au contenu est la partie du chemin d’accès qui s’affiche entre le nom d’hôte CDN Verizon et le chemin d’accès relatif à la ressource demandée (par exemple, /800001/CustomerOrigin). Il identifie un emplacement par type de serveur (par exemple, CDN ou origine du client) et votre numéro de compte client.

   Les valeurs suivantes sont disponibles pour l’option **Relative to** (Relatif à) :
   - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN. 

     Par exemple : http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

   - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

     Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

     Cette URL pointe vers le nom d’hôte CDN Verizon suivant : http:\//wpc.0001.&lt;Domain&gt;/800001/myorigin/**myfolder**/index.htm

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL.

    Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL.
    - URL CDN : http:\//wpc.0001.&lt;Domain&gt;/800001/CustomerOrigin/path/asset.htm
    
    - URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Informations supplémentaires :
    - Domaine personnalisé : https:\//my.domain.com/path/asset.htm
    
    - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/
    
    - Chemin d’URL (relatif à l’origine) : /path/

- La partie de l’URL qui est utilisée pour la comparaison d’URL s’arrête juste avant le nom de fichier de la ressource demandée. Une barre oblique de fin est le dernier caractère de ce type de chemin.
    
- Remplacez les espaces dans le modèle de chemin d’URL par « %20 ».
    
- Chaque modèle de chemin d’URL peut contenir un ou plusieurs astérisques (*), où chaque astérisque correspond à une séquence d’un ou de plusieurs caractères.
    
- Spécifiez plusieurs chemins d’URL dans le modèle en les séparant par une espace.

    Par exemple : */sales/ */marketing/

- Une spécification de chemin d’URL peut utiliser des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values).

- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>Extension du chemin d’URL
Identifie les requêtes par l’extension de fichier de la ressource demandée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Extension du chemin d’URL est remplie.
- **Correspond** : nécessite que l’URL de la requête contienne une extension de fichier qui correspond exactement au modèle spécifié.

   Par exemple, si vous spécifiez « htm », les ressources « htm » correspondent, mais pas les ressources « html ».  

- **Ne correspond pas** : nécessite que l’URL de la requête contienne une extension de fichier qui ne correspond pas au modèle spécifié.

Informations essentielles :
- Spécifiez les extensions de fichier à mettre en correspondance dans le champ **Valeur**. N’incluez pas de point de début ; par exemple, utilisez htm au lieu de .htm.

- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

- Spécifiez plusieurs extensions de fichier en délimitant chaque extension avec une espace. 

    Par exemple : htm html

- Par exemple, spécifier « htm » correspond aux ressources « htm », mais pas aux ressources « html ».


#### <a name="sample-scenario"></a>Exemple de scénario

L’exemple de configuration suivant part du principe que cette condition de correspondance est remplie quand une requête correspond à l’une des extensions spécifiées.

Spécification de valeur : asp aspx php html

Cette condition de correspondance est remplie quand elle trouve des URL qui se terminent par les extensions suivantes :
- .asp
- .aspx
- .php
- .html

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>Nom de fichier du chemin d’URL
Identifie les requêtes par le nom de fichier de la ressource demandée. Dans le cadre de cette condition de correspondance, un nom de fichier se compose du nom de la ressource demandée, d’un point et de l’extension de fichier (par exemple, index.html).

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Nom de fichier du chemin d’URL est remplie.
- **Correspond** : nécessite que la requête contienne un nom de fichier dans son chemin d’URL qui correspond au schéma spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un nom de fichier dans son chemin d’URL qui ne correspond pas au schéma spécifié.

Informations essentielles :
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.

- Pour spécifier plusieurs extensions de fichier, séparez chaque extension avec une espace.

    Par exemple : index.htm index.html

- Remplacez les espaces dans une valeur de nom de fichier par « % 20 ».
    
- Une valeur de nom de fichier peut utiliser des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values). Par exemple, chaque modèle de nom de fichier peut contenir un ou plusieurs astérisques (*), où chaque astérisque correspond à une séquence d’un ou de plusieurs caractères.
    
- Si aucun caractère générique n’est spécifié, seule une correspondance exacte satisfait cette condition de correspondance.

    Par exemple, spécifier « presentation.ppt » correspond à une ressource nommée « presentation.ppt », mais pas à une ressource nommée « presentation.pptx ».

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>Littéral du chemin d’URL
Compare le chemin d’URL d’une requête, y compris le nom de fichier, à la valeur spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral du chemin d’URL est remplie.
- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond au schéma spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas au schéma spécifié.

Informations essentielles :
- Utilisez l’option **Relative to** (Relatif à) pour spécifier si le point de comparaison de l’URL commence avant ou après le point d’accès au contenu. 

    Les valeurs suivantes sont disponibles pour l’option **Relative to** (Relatif à) :
     - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN.

       Par exemple : http:\//wpc.0001.&lt;Domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

       Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Cette URL pointe vers le nom d’hôte CDN Verizon suivant: http:\//wpc.0001.&lt;Domain&gt;/800001/myorigin/**myfolder/index.htm**

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant une comparaison d’URL.

   Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL :
    - URL CDN : http:\//wpc.0001.&lt;Domain&gt;/800001/CustomerOrigin/path/asset.htm
    - URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Informations supplémentaires :
    
    - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/asset.htm
   
    - Chemin d’URL (relatif à l’origine) : /path/asset.htm

- Les chaînes de requête dans l’URL sont ignorées.
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
- La valeur spécifiée pour cette condition de correspondance sera comparée au chemin d’accès relatif de la requête exacte effectuée par le client.

- Pour faire correspondre toutes les demandes effectuées vers un répertoire particulier, utilisez la condition de correspondance [Répertoire du chemin d’URL](#url-path-directory) ou [Caractère générique du chemin d’URL](#url-path-wildcard).

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>Expression régulière du chemin d’URL
Compare le chemin d’URL d’une requête à [l’expression régulière](cdn-rules-engine-reference.md#regular-expressions) spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière du chemin d’URL est remplie.
- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :
- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL. 
 
   Par exemple, deux URL pointent vers la même ressource et ont donc le même chemin d’URL.

     - URL CDN : http:\//wpc.0001.&lt;Domain&gt;/800001/CustomerOrigin/path/asset.htm

     - URL CNAME de périmètre : http:\//my.domain.com/path/asset.htm

   Informations supplémentaires :
    
     - Chemin d’URL : /800001/CustomerOrigin/path/asset.htm

- Les chaînes de requête dans l’URL sont ignorées.
    
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
    
- Les espaces dans le chemin d’URL doivent être remplacées par « %20 ».

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>Caractère générique du chemin d’URL
Compare le chemin d’URL relatif d’une requête au modèle de caractère générique spécifié.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique du chemin d’URL est remplie.
- **Correspond** : nécessite que la requête contienne un chemin d’URL qui correspond au schéma de caractère générique spécifié.
- **Ne correspond pas** : nécessite que la requête contienne un chemin d’URL qui ne correspond pas au schéma de caractère générique spécifié.

Informations essentielles :
- Option **Relative to** (Relatif à) : cette option détermine si le point de comparaison de l’URL commence avant ou après le point d’accès au contenu.

   Cette option peut avoir les valeurs suivantes :
     - **Racine** : indique que le point de comparaison d’URL commence directement après le nom d’hôte CDN.

       Par exemple : http:\//wpc.0001.&lt;Domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origine** : indique que le point de comparaison d’URL commence après le point d’accès au contenu (par exemple, /000001 ou /800001/myorigin). Étant donné que le CNAME \*.azureedge.net est créé par rapport au répertoire d’origine sur le nom d’hôte CDN Verizon par défaut, les utilisateurs de CDN Azure doivent utiliser la valeur **Origine**. 

       Par exemple : https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     Cette URL pointe vers le nom d’hôte CDN Verizon suivant: http:\//wpc.0001.&lt;Domain&gt;/800001/myorigin/**myfolder/index.htm**

- Une URL CNAME de périmètre est réécrite sur une URL CDN avant la comparaison d’URL.

   Par exemple, les deux URL suivantes pointent vers la même ressource et ont donc le même chemin d’URL :
     - URL CDN : http://wpc.0001.&lt;Domain&gt;/800001/CustomerOrigin/path/asset.htm
     - URL CNAME de périmètre : http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

   Informations supplémentaires :
    
     - Chemin d’URL (relatif à la racine) : /800001/CustomerOrigin/path/asset.htm
    
     - Chemin d’URL (relatif à l’origine) : /path/asset.htm
    
- Spécifiez plusieurs chemins d’URL en les séparant par une espace.

   Par exemple : /marketing/asset.* /sales/*.htm

- Les chaînes de requête dans l’URL sont ignorées.
    
- Utilisez l’option **Ignorer la casse** pour contrôler si une comparaison respectant la casse est effectuée.
    
- Remplacez les espaces dans le chemin d’URL par « % 20 ».
    
- La valeur spécifiée pour un chemin d’URL peut utiliser des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values). Chaque modèle de chemin d’URL peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.

#### <a name="sample-scenarios"></a>Exemples de scénarios

Les exemples de configuration dans le tableau suivant supposent que cette condition de correspondance est remplie quand une requête correspond au modèle d’URL spécifié :

Valeur                   | Par rapport à    | Résultat 
------------------------|----------------|-------
*/test.html */test.php  | Racine ou Origine | Ce modèle correspond aux requêtes de ressources nommées « test.html » ou « test.php » dans les dossiers.
/80ABCD/origin/text/*   | Racine           | Ce modèle correspond lorsque la ressource demandée répond aux critères suivants : <br />- Il doit résider sur une origine client appelée « origin ». <br />- Le chemin d’accès relatif doit commencer par un dossier appelé « text ». Autrement dit, la ressource demandée peut résider dans le dossier « text » ou l’un de ses sous-dossiers récursifs.
*/css/* */js/*          | Racine ou Origine | Ce modèle correspond à toutes les URL CDN ou CNAME de périmètre qui contiennent un dossier css ou js.
*.jpg *.gif *.png       | Racine ou Origine | Ce modèle correspond à toutes les URL CDN ou CNAME de périmètre se terminant par .jpg, .gif ou .png. Une autre façon de spécifier ce modèle consiste à utiliser la [condition de correspondance Extension du chemin d’URL](#url-path-extension).
/images/* /media/*      | Origine         | Ce modèle est mis en correspondance par les URL CDN ou CNAME de périmètre dont le chemin d’accès relatif commence par un dossier « images » ou « media ». <br />- URL CDN : http:\//wpc.0001.&lt;Domain&gt;/800001/myorigin/images/sales/event1.png<br />- Exemple d’URL CNAME de périmètre : http:\//cdn.mydomain.com/images/sales/event1.png

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>Littéral de requête d’URL
Compare la chaîne de requête d’une requête à la valeur spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Littéral de requête d’URL est remplie.
- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à la chaîne spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à la chaîne spécifiée.

Informations essentielles :

- Seule une chaîne de requête exacte répond à cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- N’incluez pas de point d’interrogation (?) au début du texte de valeur de chaîne de requête.
    
- Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

   Caractère | Encodage par URL
   ----------|---------
   Espace     | %20
   &         | %25

- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>Paramètre de requête d’URL
Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié. Ce paramètre est défini sur une valeur qui correspond à un modèle spécifié. Les paramètres de chaîne de requête (par exemple, paramètre = valeur) dans l’URL de requête détermine si cette condition est remplie. Cette condition de correspondance identifie un paramètre de chaîne de requête par son nom et accepte une ou plusieurs valeurs pour la valeur du paramètre. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Paramètre de requête d’URL est remplie.
- **Correspond** : nécessite qu’une requête contienne le paramètre spécifié avec une valeur qui correspond à au moins une des valeurs définies dans cette condition de correspondance.
- **Ne correspond pas** : nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le paramètre spécifié.
  - Elle contient le paramètre spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.

Cette condition de correspondance fournit un moyen simple de spécifier des combinaisons nom/valeur de paramètre. Pour plus de souplesse, si vous faites correspondre un paramètre de chaîne de requête, envisagez d’utiliser la condition de correspondance [Caractère générique de requête d’URL](#url-query-wildcard).

Informations essentielles :
- Seul un nom de paramètre de requête d’URL peut être spécifié par instance de cette condition de correspondance.
    
- Étant donné que les valeurs de caractère générique ne sont pas prises en charge lorsqu’un nom de paramètre est spécifié, seules les correspondances de nom de paramètre exactes sont éligibles pour la comparaison.
- Les valeurs de paramètre peuvent inclure des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values).
   - Chaque modèle de valeur de paramètre peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.
   - Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

       Caractère | Encodage par URL
       ----------|---------
       Espace     | %20
       &         | %25

- Spécifiez plusieurs valeurs de paramètre de chaîne de requête en les séparant par une espace. Cette condition de correspondance est remplie quand une requête contient l’une des combinaisons nom/valeur spécifiées.

   - Exemple 1 :

     - Configuration :

       ValueA ValueB

     - Cette configuration correspond aux paramètres de chaîne de requête suivants :

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Exemple 2 :

     - Configuration : 

        Value%20A Value%20B

     - Cette configuration correspond aux paramètres de chaîne de requête suivants :

       Parameter1=Value%20A

       Parameter1=Value%20B

- Cette condition de correspondance est remplie uniquement s’il existe une correspondance exacte à au moins une des combinaisons nom/valeur de chaîne de requête spécifiées.

   Par exemple, si vous utilisez la configuration de l’exemple précédent, la combinaison nom/valeur de paramètre « Parameter1 = ValueAdd » ne serait pas considérée comme une correspondance. Toutefois, si vous spécifiez l’une des valeurs suivantes, elle correspondra à la combinaison nom/valeur :

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

#### <a name="sample-scenarios"></a>Exemples de scénarios
L’exemple suivant montre comment cette option fonctionne dans des situations spécifiques :

NOM      | Valeur |  Résultat
----------|-------|--------
Utilisateur      | Joe   | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée est « ?user=joe ».
Utilisateur      | *     | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée contient un paramètre Utilisateur.
Email Joe (E-mail Joe) | *     | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée contient un paramètre E-mail qui commence par « Joe ».

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>Expression régulière de requête d’URL
Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié. Ce paramètre est défini sur une valeur qui correspond à une [expression régulière](cdn-rules-engine-reference.md#regular-expressions) spécifiée.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Expression régulière de requête d’URL est remplie.
- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :
- Seules les correspondances exactes à l’expression régulière spécifiée satisfont cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- Pour cette option, une chaîne de requête commence par le premier caractère après le délimiteur de point d’interrogation (?) pour la chaîne de requête.
    
- Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

   Caractère | Encodage par URL | Valeur
   ----------|--------------|------
   Espace     | %20          | \%20
   &         | %25          | \%25

   Notez que les symboles de pourcentage doivent être placés dans une séquence d’échappement.

- Placez les caractères d’expression régulière spéciaux dans une double séquence d’échappement (par exemple, \^$. +) pour inclure une barre oblique inverse dans l’expression régulière.

   Par exemple : 

   Valeur | Interprété comme 
   ------|---------------
   \\+    | +
   \\\+   | \\+

- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne


[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>Caractère générique de requête d’URL
Compare les valeurs spécifiées à la chaîne de requête de la requête.

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles la condition de correspondance Caractère générique de requête d’URL est remplie.
- **Correspond** : nécessite que la requête contienne une chaîne de requête d’URL qui correspond à la valeur de caractère générique spécifiée.
- **Ne correspond pas** : nécessite que la requête contienne une chaîne de requête d’URL qui ne correspond pas à la valeur de caractère générique spécifiée.

Informations essentielles :
- Pour cette option, une chaîne de requête commence par le premier caractère après le délimiteur de point d’interrogation (?) pour la chaîne de requête.
- Les valeurs de paramètre peuvent inclure des [valeurs de caractère générique](cdn-rules-engine-reference.md#wildcard-values) :
   - Chaque modèle de valeur de paramètre peut contenir un ou plusieurs astérisques (*), où chaque astérisque peut correspondre à une séquence d’un ou de plusieurs caractères.
   - Certains caractères nécessitent un encodage par URL. Utilisez le symbole de pourcentage pour encoder par URL les caractères suivants :

     Caractère | Encodage par URL
     ----------|---------
     Espace     | %20
     &         | %25

- Spécifiez plusieurs valeurs en les séparant par un espace.

   Par exemple : *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Seules les correspondances exactes à au moins un des modèles de chaîne de requête spécifiés satisfont cette condition de correspondance.
    
- Utilisez l’option **Ignorer la casse** pour contrôler le respect de la casse dans les comparaisons de chaînes de requête.
    
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
   - Remplissage de cache complet
   - Âge maximal interne par défaut
   - Forcer l’âge maximal interne
   - Ignorer la requête non-cache d’origine
   - Obsolescence maximale interne

#### <a name="sample-scenarios"></a>Exemples de scénarios
L’exemple suivant montre comment cette option fonctionne dans des situations spécifiques :

 NOM                 | Description
 ---------------------|------------
user=joe              | Ce modèle correspond lorsque la chaîne de requête d’une URL demandée est « ?user=joe ».
\*user=\* \*optout=\* | Ce modèle correspond lorsque la requête d’URL CDN contient l’utilisateur ou le paramètre de désabonnement.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md)
* [Informations de référence du moteur de règles](cdn-rules-engine-reference.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)

