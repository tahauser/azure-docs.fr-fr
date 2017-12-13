---
title: "Contrôler le comportement de mise en cache d’Azure Content Delivery Network au moyen de règles de mise en cache | Microsoft Docs"
description: "Vous pouvez utiliser les règles de mise en cache CDN pour définir ou modifier le comportement d’expiration du cache par défaut, globalement et avec des conditions, telles qu’un chemin d’URL et des extensions de fichier."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 8f89ef5a1763d5fc4ad09a9aeae89ccf683138c7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>Contrôler le comportement de mise en cache d’Azure Content Delivery Network au moyen de règles de mise en cache

> [!NOTE] 
> Les règles de mise en cache sont disponibles uniquement pour **Azure CDN à partir de Verizon Standard** et **Azure CDN à partir d’Akamai Standard**. Pour **Azure CDN à partir de Verizon Premium**, vous pouvez utiliser le [moteur de règles d’Azure CDN](cdn-rules-engine.md) dans le portail de **gestion** pour une fonctionnalité similaire.
 
Azure Content Delivery Network offre deux moyens de contrôler la façon dont vos fichiers sont mis en cache : 

- Règles de mise en cache : cet article explique comment vous servir des règles de mise en cache du réseau de diffusion de contenu (CDN) pour définir ou modifier le comportement d’expiration du cache par défaut, globalement et avec des conditions personnalisées, telles qu’un chemin URL et une extension de fichier. Azure CDN fournit deux types de règles de mise en cache :
   - Règles de mise en cache globales : vous pouvez définir une règle de mise en cache globale pour chaque point de terminaison dans votre profil, ce qui affecte toutes les requêtes au point de terminaison. La règle de mise en cache globale se substitue à tous les en-têtes à directive de cache HTTP, s’ils sont définis.
   - Règles de mise en cache personnalisées : vous pouvez définir une ou plusieurs règles de mise en cache personnalisées pour chaque point de terminaison dans votre profil. Les règles de mise en cache personnalisées correspondent à des chemins et extensions de fichier spécifiques, elles sont traitées dans l’ordre et remplacent la règle de mise en cache globale, si elle est définie. 

- La mise en cache des chaînes de requête : vous pouvez ajuster la manière dont Azure CDN traite la mise en cache pour les requêtes dotées de chaînes de requête. Pour plus d’informations, consultez [Contrôler le comportement de mise en cache d’Azure CDN avec des chaînes de requête](cdn-query-string.md). Si le fichier ne peut pas être mis en cache, le paramètre de mise en cache des chaînes de requête n’a aucun effet, compte tenu des règles de mise en cache et des comportements CDN par défaut.

Pour plus d’informations sur le comportement de mise en cache par défaut et sur les en-têtes à directive de mise en cache, consultez [Fonctionnement de la mise en cache](cdn-how-caching-works.md).

## <a name="tutorial"></a>Didacticiel

Comment définir les règles de mise en cache CDN :

1. Ouvrez le portail Azure, sélectionnez un profil CDN, puis sélectionnez un point de terminaison.
2. Dans le volet gauche, sous Paramètres, cliquez sur **Cache**.
3. Créez une règle de mise en cache globale comme suit :
   1. Sous **Règles de mise en cache générales**, définissez **Comportement de mise en cache des chaînes de requête** sur **Ignorer les chaînes de requête**.
   2. Définissez **Comportement de mise en cache** sur **Définir en cas d’absence**.
   3. Pour **Durée d’expiration du cache**, entrez 10 dans le champ **Jours**.

       La règle de mise en cache globale affecte toutes les requêtes au point de terminaison. Cette règle respecte les en-têtes à directive de cache d’origine, s’ils existent (`Cache-Control` ou `Expires`) ; s’ils ne sont pas spécifiés, elle définit le cache sur 10 jours. 

4. Créez une règle de mise en cache personnalisée comme suit :
    1. Sous **Règles de mise en cache personnalisées**, définissez **Condition de correspondance** sur **Chemin** et **Valeur(s) de correspondance** sur `/images/*.jpg`.
    2. Définissez **Comportement de mise en cache** sur **Remplacer** et entrez 30 dans le champ **Jours**.
       
       Cette règle de mise en cache personnalisée définit une durée de cache de 30 jours sur tous les fichiers image `.jpg` présents dans le dossier `/images` de votre point de terminaison. Elle se substitue à tout en-tête HTTP `Cache-Control` ou `Expires` qui sont envoyés par le serveur d’origine.

  ![Boîte de dialogue Règles de mise en cache](./media/cdn-caching-rules/cdn-caching-rules-dialog.png)

> [!NOTE] 
> Les fichiers qui sont mis en cache avant une modification de règle conservent leur paramètre de durée de cache d’origine. Pour réinitialiser leur durée de cache, vous devez [vider le fichier](cdn-purge-endpoint.md). Pour les points de terminaison **Azure CDN à partir de Verizon**, la mise en application des règles de mise en cache peut demander jusqu’à 90 minutes.

## <a name="reference"></a>Référence

### <a name="caching-behavior-settings"></a>Paramètres du comportement de mise en cache
Pour les règles de mise en cache globales et personnalisées, vous pouvez spécifier les paramètres de **Comportement de mise en cache** suivants :

- **Ignorer le cache** : ne pas mettre en cache et ignorer les en-têtes à directive de cache fournis à l’origine.
- **Remplacer** : ignorer les en-têtes à directive de cache fournis à l’origine ; utilisez la durée du cache fourni à la place.
- **Définir en cas d’absence** : respecter les en-têtes à directive de cache fournis à l’origine, s’ils existent ; sinon, utilisez la durée de cache fournie.

### <a name="cache-expiration-duration"></a>Durée d’expiration du cache
Pour les règles de mise en cache globales et personnalisées, vous pouvez spécifier la durée d’expiration du cache en jours, heures, minutes et secondes :

- Pour les paramètres de **Comportement de mise en cache** **Remplacer** et **Définir en cas d’absence**, la plage des durées de cache valide est comprise entre 0 et 366 jours. Pour une valeur de 0 seconde, le CDN met en cache le contenu, mais doit revalider chaque requête avec le serveur d’origine.
- Pour le paramètre **Ignorer le cache**, la durée du cache est automatiquement définie sur 0 seconde et ne peut pas être modifiée.

### <a name="custom-caching-rules-match-conditions"></a>Conditions de correspondance des règles de mise en cache personnalisées

Pour les règles de cache personnalisées, deux conditions de correspondance sont disponibles :
 
- **Chemin** : cette condition correspond au chemin de l’URL, à l’exclusion du nom de domaine, et prend en charge le caractère générique (\*). Par exemple, `/myfile.html`, `/my/folder/*` ou `/my/images/*.jpg`. La longueur maximale est de 260 caractères.

- **Extension** : cette condition correspond à l’extension de fichier du fichier demandé. Vous pouvez fournir une liste d’extensions de fichier séparées par des virgules pour la correspondance. Par exemple, `.jpg`, `.mp3` ou `.png`. Le nombre maximal d’extensions est de 50 et le nombre maximal de caractères par extension est de 16. 

### <a name="global-and-custom-rule-processing-order"></a>Ordre de traitement des règles globales et personnalisées
Les règles de mise en cache globales et personnalisées sont traitées dans l’ordre suivant :

- Les règles de mise en cache globales sont prioritaires sur le comportement de mise en cache CDN par défaut (paramètres d’en-tête à directive de cache HTTP). 

- Les règles de mise en cache personnalisées sont prioritaires sur les règles de mise en cache globales, où elles s’appliquent. Les règles de mise en cache personnalisées sont traitées de haut en bas. Autrement dit, si une requête remplie ces deux conditions, les règles situées au bas de la liste sont prioritaires sur celles qui sont situées en haut. Par conséquent, vous devez placer les règles vraiment spécifiques plus bas dans la liste.

**Exemple**:
- Règles de mise en cache générales : 
   - Comportement de mise en cache : **Remplacer**
   - Durée d’expiration du cache : 1 jour

- Règle no1 de mise en cache personnalisée :
   - Condition de correspondance : **Chemin**
   - Valeur de correspondance : `/home/*`
   - Comportement de mise en cache : **Remplacer**
   - Durée d’expiration du cache : 2 jours

- Règle no2 de mise en cache personnalisée :
   - Condition de correspondance : **Extension**
   - Valeur de correspondance : `.html`
   - Comportement de mise en cache : **Définir en cas d’absence**
   - Durée d’expiration du cache : 3 jours

Lorsque ces règles sont définies, une requête pour `<endpoint>.azureedge.net/home/index.html` déclenche la règle no2 de mise en cache personnalisée, qui est définie sur : **Définir en cas d’absence** et 3 jours. Par conséquent, si le fichier `index.html` est doté des en-têtes HTTP `Cache-Control` ou `Expires`, ils sont respectés ; si ces en-têtes ne sont pas définis, le fichier est mis en cache pendant 3 jours.

