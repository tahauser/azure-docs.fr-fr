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
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
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
Répertoire du chemin d’URL | Identifie les requêtes par leur chemin relatif.
Extension du chemin d’URL | Identifie les requêtes par leur extension de nom de fichier.
Nom de fichier du chemin d’URL | Identifie les requêtes par leur nom de fichier.
Littéral du chemin d’URL | Compare le chemin relatif d’une requête à la valeur spécifiée.
Expression régulière du chemin d’URL | Compare le chemin relatif d’une requête à l’expression régulière spécifiée.
Caractère générique du chemin d’URL | Compare le chemin relatif d’une requête au modèle spécifié.
Littéral de requête d’URL | Compare la chaîne de requête d’une requête à la valeur spécifiée.
Paramètre de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond au modèle spécifié.
Expression régulière de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
Caractère générique de requête d’URL | Compare les valeurs spécifiées à la chaîne de requête de la requête.


## <a name="reference-for-rules-engine-match-conditions"></a>Informations de référence des conditions de correspondance du moteur de règles

---
### <a name="always"></a>Toujours

La condition de correspondance Toujours applique un ensemble de fonctionnalités par défaut à toutes les requêtes.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Numéro AS 
Le réseau Numéro AS est défini par son numéro de système autonome (ASN). Une option est fournie pour indiquer si cette condition est remplie quand le réseau d’un client « Correspond » ou « Ne correspond pas » à l’ASN spécifié.

Informations essentielles :
- Spécifiez plusieurs ASN en les séparant par un espace. Par exemple, 64514 64515 correspond aux requêtes qui proviennent de 64514 ou 64515.
- Certaines requêtes peuvent ne pas retourner d’ASN valide. Un point d’interrogation (?) correspond aux requêtes pour lesquelles un ASN valide ne peut pas être déterminé.
- Vous devez spécifier l’ASN entier du réseau souhaité. Les valeurs partielles ne sont pas prises en compte.
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
- Le contenu du stockage Content Delivery Network est demandé.
- L’URI de la requête utilise le point d’accès de contenu (par exemple, /000001) qui est défini dans cette condition de correspondance.
  - URL Content Delivery Network : L’URI de la requête doit contenir le point d’accès de contenu sélectionné.
  - URL du CNAME de périmètre : La configuration du CNAME de périmètre correspondant doit pointer vers le point d’accès de contenu sélectionné.
  
Informations essentielles :
 - Le point d’accès de contenu identifie le service qui doit traiter le contenu demandé.
 - N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine CDN avec une condition de correspondance Origine client crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine CDN ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Adresse IP du client
Une option est fournie pour indiquer si la condition Adresse IP du client est remplie quand l’adresse IP d’un client « Correspond » ou « Ne correspond pas » aux adresses IP spécifiées.

Informations essentielles :
- Utilisez la notation CIDR.
- Spécifiez plusieurs adresses IP et/ou blocs d’adresses IP en les séparant par un espace.
  - **Exemple IPv4** : 1.2.3.4 10.20.30.40 correspond aux requêtes qui proviennent de 1.2.3.4 ou 10.20.30.40.
  - **Exemple IPv6** : 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 correspond aux requêtes qui proviennent de 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- La syntaxe d’un bloc d’adresses IP est l’adresse IP de base suivie d’une barre oblique et de la taille de préfixe.
  - **Exemple IPv4** : 5.5.5.64/26 correspond aux requêtes qui proviennent de 5.5.5.64 à 5.5.5.127.
  - **Exemple IPv6** : 1:2:3:/48 correspond aux requêtes qui proviennent de 1:2:3:0:0:0:0:0 à 1:2:3:ffff:ffff:ffff:ffff:ffff.
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
- **Ne correspond pas** : Nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :
- Nom du cookie : 
  - Les caractères spéciaux, y compris l’astérisque, ne sont pas pris en charge dans la spécification d’un nom de cookie. Cela signifie que seules les correspondances de nom de cookie exactes sont utilisées dans la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie : 
  - Spécifiez plusieurs valeurs de cookie en les séparant par un espace.
  - Une valeur de cookie peut contenir des caractères spéciaux. 
  - Si aucun caractère générique n’a été spécifié, seule une correspondance exacte satisfait cette condition de correspondance. Par exemple, « Valeur » correspond à « Valeur », mais pas à « Valeur1 » ni « Valeur2 ».
  - L’option **Ignorer la casse** détermine si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
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
La condition de correspondance Expression régulière de paramètre de cookie définit une valeur et un nom de cookie. Vous pouvez utiliser des expressions régulières pour définir la valeur de cookie souhaitée. 

L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles cette condition de correspondance est remplie.
- **Correspond** : Nécessite que la requête contienne le cookie spécifié avec une valeur qui correspond à l’expression régulière spécifiée.
- **Ne correspond pas** : Nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas le cookie spécifié.
  - Elle contient le cookie spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.
  
Informations essentielles :
- Nom du cookie : 
  - Les expressions régulières et les caractères spéciaux, y compris l’astérisque, ne sont pas pris en charge dans la spécification d’un nom de cookie. Cela signifie que seules les correspondances de nom de cookie exactes sont utilisées dans la comparaison.
  - Vous pouvez spécifier un seul nom de cookie par instance de cette condition de correspondance.
  - Les comparaisons de noms de cookie respectent la casse.
- Valeur du cookie : 
  - Une valeur de cookie peut contenir des expressions régulières.
  - L’option **Ignorer la casse** détermine si une comparaison respectant la casse est effectuée avec la valeur du cookie de la requête.
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
Vous pouvez spécifier un pays à l’aide de son code de pays. Une option est fournie pour indiquer si cette condition est remplie quand le pays émetteur de la requête « Correspond » ou « Ne correspond pas » aux valeurs spécifiées.

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

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origine client

Informations essentielles : 
- La condition de correspondance Origine client est satisfaite que le contenu soit demandé à l’aide d’une URL Content Delivery Network ou d’une URL de CNAME de périmètre qui pointe vers l’origine client sélectionnée.
- Une configuration d’origine client référencée par une règle ne peut pas être supprimée à partir de la page Origine client. Avant de supprimer une configuration d’origine client, vérifiez que les configurations suivantes ne la référencent pas :
  - Condition de correspondance Origine client
  - Configuration de CNAME de périmètre
- N’utilisez pas d’instruction AND IF pour combiner certaines conditions de correspondance. Par exemple, la combinaison d’une condition de correspondance Origine client avec une condition de correspondance Origine CDN crée un modèle de correspondance introuvable. Pour cette raison, deux conditions de correspondance Origine client ne peuvent pas être combinées à l’aide d’une instruction AND IF.

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés. La détection de périphérique mobile est obtenue par le biais de [WURFL](http://wurfl.sourceforge.net/). Le tableau suivant répertorie les fonctionnalités WURFL et leurs variables pour le moteur de règles Content Delivery Network.
<br>
> [!NOTE] 
> Les variables suivantes sont prises en charge dans les fonctionnalités **Modifier l’en-tête de requête client** et **Modifier l’en-tête de réponse client**.

Fonctionnalité | Variable | DESCRIPTION | Exemples de valeurs
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
Est une tablette | %{wurfl_cap_is_tablet} | Valeur booléenne qui indique si l’appareil est une tablette. Il s’agit d’une description indépendante du système d’exploitation. | true
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
- La liste des enregistrements CNAME de périmètre disponibles est limitée à ceux qui ont été configurés dans la page d’enregistrements CNAME de périmètre et qui correspondent à la plateforme sur laquelle le moteur de règles HTTP est configuré.
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
Le nom d’hôte associé au point d’accès par lequel le contenu a été demandé détermine si la condition Domaine de référence est remplie. Une option est fournie pour indiquer si cette condition est remplie quand le nom d’hôte de référence « Correspond » ou « Ne correspond pas » aux valeurs spécifiées.

Informations essentielles :
- Spécifiez plusieurs noms d’hôte en les séparant par un espace.
- Cette condition de correspondance prend en charge les caractères spéciaux.
- Si la valeur spécifiée ne contient pas d’astérisque, elle doit correspondre à la valeur exacte du nom d’hôte du point d’accès. Par exemple, « mydomain.com » ne correspond pas à « www.mydomain.com ».
- L’option **Ignorer la casse** détermine si une comparaison respectant la casse est effectuée.
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
L’option **Correspond**/**Ne correspond pas** détermine les conditions sous lesquelles cette condition de correspondance est remplie.
- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre à celle définie dans cette condition de correspondance.
- **Ne correspond pas** : Nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur définie dans cette condition de correspondance.
  
Informations essentielles :
- Les comparaisons de noms d’en-tête respectent toujours la casse. L’option **Ignorer la casse** détermine le respect de la casse dans les comparaisons de valeurs d’en-tête.
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
- **Correspond** : Nécessite que la requête contienne l’en-tête spécifié. Sa valeur doit correspondre au modèle défini dans l’expression régulière spécifiée.
- **Ne correspond pas** : Nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond pas à l’expression régulière spécifiée.

Informations essentielles :
- Nom de l’en-tête : 
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Les espaces dans le nom d’en-tête doivent être remplacés par « %20 ». 
- Valeur de l’en-tête : 
  - Une valeur d’en-tête peut contenir des expressions régulières.
  - L’option **Ignorer la casse** détermine le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - Seule une valeur d’en-tête exacte correspondant à au moins un des modèles spécifiés satisfait cette condition.
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
- **Ne correspond pas** : Nécessite que la requête réponde à l’un des critères suivants :
  - Elle ne contient pas l’en-tête spécifié.
  - Elle contient l’en-tête spécifié, mais sa valeur ne correspond à aucune valeur spécifiée.
  
Informations essentielles :
- Nom de l’en-tête : 
  - Les comparaisons de noms d’en-tête respectent la casse.
  - Les espaces dans le nom d’en-tête doivent être remplacés par « %20 ». Vous pouvez aussi utiliser cette valeur pour spécifier des espaces dans une valeur d’en-tête.
- Valeur de l’en-tête : 
  - Une valeur d’en-tête peut contenir des caractères spéciaux.
  - L’option **Ignorer la casse** détermine le respect de la casse dans les comparaisons de valeurs d’en-tête.
  - Seule une valeur d’en-tête exacte correspondant à au moins un des modèles spécifiés satisfait cette condition.
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
Seules les ressources qui sont demandées par la méthode de requête sélectionnée satisfont la condition Méthode de requête. Les méthodes de requête disponibles sont :
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
Seules les ressources qui sont demandées par le protocole sélectionné satisfont la condition Modèle de requête. Les protocoles disponibles sont HTTP et HTTPS.

Informations essentielles :
- En raison du type de suivi des paramètres de cache, cette condition de correspondance est incompatible avec les fonctionnalités suivantes :
  - Remplissage de cache complet
  - Âge maximal interne par défaut
  - Forcer l’âge maximal interne
  - Ignorer la requête non-cache d’origine
  - Obsolescence maximale interne

[Revenir en haut](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>étapes suivantes
* [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md)
* [Informations de référence du moteur de règles](cdn-rules-engine-reference.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)

