---
title: "Concepts de développeur Data Catalog | Microsoft Docs"
description: "Présentation des concepts clés du modèle conceptuel d’Azure Data Catalog, tels qu’ils sont exposés via l’API REST Catalog."
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: spelluru
ms.openlocfilehash: 48d4a33f7667786f2eb8851ed69dedc206e777ae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-developer-concepts"></a>Concepts de développeur Azure Data Catalog
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui fournit des fonctionnalités de détection de source de données et de métadonnées de source de données de crowdsourcing. Les développeurs peuvent utiliser le service via son API REST. Il est important de comprendre les concepts du service pour l’intégrer efficacement à **Azure Data Catalog**.

## <a name="key-concepts"></a>Concepts clés
Le modèle conceptuel **Azure Data Catalog** repose sur quatre concepts clés : le **catalogue**, les **utilisateurs**, les **ressources** et les **annotations**.

![concept][1]

*Figure 1 : modèle conceptuel simplifié d’Azure Data Catalog*

### <a name="catalog"></a>Catalogue
Un **catalogue** est le conteneur de niveau supérieur pour toutes les métadonnées stockées par une organisation. Chaque compte Azure ne peut avoir qu’un **catalogue** . Les catalogues sont liés à un abonnement Azure, mais seul un **catalogue** peut être créé pour un compte Azure donné, même si un compte peut avoir plusieurs abonnements.

Un catalogue contient des **utilisateurs** et des **ressources**.

### <a name="users"></a>Utilisateurs
Les utilisateurs sont des principaux de sécurité qui ont des autorisations pour effectuer des actions (recherche dans le catalogue, ajout, modification ou suppression d’éléments, etc.) dans le catalogue.

Un utilisateur peut avoir plusieurs rôles différents. Pour plus d’informations sur les rôles, consultez la section Rôles et autorisations.

Des utilisateurs individuels et des groupes de sécurité peuvent être ajoutés.

Azure Data Catalog utilise Azure Active Directory pour la gestion des identités et des accès. Chaque utilisateur de catalogue doit être un membre de l’Active Directory du compte.

### <a name="assets"></a>Éléments multimédias
Un **catalogue** contient des ressources de données . **ressources** représentent l’unité de granularité gérée par le catalogue.

La granularité d'une ressource varie selon la source de données. Pour SQL Server ou une base de données Oracle, une ressource peut être une table ou un affichage. Pour SQL Server Analysis Services, une ressource peut être une mesure, une dimension ou un indicateur de performance clé (KPI). Pour SQL Server Reporting Services, une ressource est un rapport.

Une **ressource** est l’élément que vous ajoutez ou supprimez d’un catalogue. C’est l'unité de résultat que vous récupérez lors d’une **recherche**.

Une **ressource** est constituée de son nom, de son emplacement et de son type ainsi que d’annotations apportant une description supplémentaire.

### <a name="annotations"></a>annotations
Les annotations sont des éléments qui représentent des métadonnées sur les ressources.

Les annotations sont, par exemple, des descriptions, des balises, un schéma, une documentation, etc. La liste complète des types de ressources et d’annotations figure dans la section Modèle d’objet de ressource.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Annotations de crowdsourcing et perspective de l'utilisateur (multiplicité d'opinions)
Un aspect clé d’Azure Data Catalog est la manière dont il prend en charge le crowdsourcing des métadonnées dans le système. Par opposition à une approche wiki, où il n’y a qu’un seul avis et le dernier à écrire gagne, le modèle Azure Data Catalog autorise plusieurs avis parallèles dans le système.

Cette approche reflète la réalité des données d'entreprise où les utilisateurs peuvent avoir différentes perspectives sur une ressource donnée :

* un administrateur de base de données peut fournir plus d'informations sur les contrats de niveau de service ou la fenêtre de traitement disponible pour les opérations ETL en bloc ;
* un gestionnaire de données peut fournir des informations sur les processus d'entreprise auxquels s'applique la ressource ou les classifications appliquées par l'entreprise ;
* un analyste financier peut fournir des informations sur l’utilisation des données lors des tâches de création de rapports de fin de période.

Pour prendre en charge cet exemple, chaque utilisateur (l'administrateur de base de données, le gestionnaire de données et l'analyste) peut ajouter une description à une table enregistrée dans le catalogue. Toutes les descriptions sont conservées dans le système et affichées dans le portail Azure Data Catalog.

Ce modèle est appliqué à la plupart des éléments du modèle d’objet, ce qui signifie que les types d’objets dans la charge utile JSON sont souvent des tableaux, pour des propriétés où vous pouvez attendre un singleton.

Par exemple, un tableau d'objets de description se trouve sous la racine de ressources. La propriété du tableau s’appelle « descriptions ». Un objet description possède une propriété : description. Le modèle est tel que chaque utilisateur qui tape une description obtient un objet description créé pour la valeur fournie par l’utilisateur.

L'expérience utilisateur permet alors l’affichage de la combinaison. Il existe trois modèles d'affichage différents.

* Le plus simple est « Tout afficher ». Dans ce modèle, tous les objets sont affichés sous forme de liste. L’expérience utilisateur du portail Azure Data Catalog utilise ce modèle comme description.
* Un autre modèle est « Fusionner ». Dans ce modèle, toutes les valeurs des différents utilisateurs sont fusionnées et les doublons supprimés. Les propriétés des balises et des experts sont des exemples de ce modèle dans l’expérience utilisateur du portail Azure Data Catalog.
* Un troisième modèle est la « règle de Thomas ». Dans ce modèle, seule la dernière valeur saisie est indiquée. Le nom convivial est un exemple de ce modèle.

## <a name="asset-object-model"></a>Modèle d'objet de ressource
Comme décrit dans la section Concepts clés, le modèle d’objet **Azure Data Catalog** comprend des éléments pouvant être des ressources ou des annotations. Les éléments ont des propriétés, qui peuvent être facultatives ou obligatoires. Certaines propriétés s'appliquent à tous les éléments. Certaines propriétés s'appliquent à toutes les ressources. Certaines propriétés s'appliquent uniquement à des types de ressources spécifiques.

### <a name="system-properties"></a>Propriétés système
<table><tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>timestamp</td><td>Datetime</td><td>L’heure de la dernière modification de l’élément. Ce champ est généré par le serveur lors de l’insertion ou de la mise à jour d’un élément. La valeur de cette propriété est ignorée lors de l’entrée d’opérations de publication.</td></tr><tr><td>id</td><td>Uri</td><td>URL absolue de l’élément (en lecture seule). Il s’agit de l’URI adressable unique de l’élément.  La valeur de cette propriété est ignorée lors de l’entrée d’opérations de publication.</td></tr><tr><td>Type</td><td>Chaîne</td><td>Le type de ressource (lecture seule)</td></tr><tr><td>etag</td><td>Chaîne</td><td>Chaîne correspondant à la version de l’élément utilisable pour un contrôle d’accès concurrentiel optimiste lors de l’exécution d’opérations de mise à jour d’éléments dans le catalogue. « * » peut être utilisé pour correspondre à n’importe quelle valeur.</td></tr></table>

### <a name="common-properties"></a>Propriétés communes
Ces propriétés s'appliquent à tous les types de ressources racines et tous les types d'annotations.

<table>
<tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booléen</td><td>Indique si les données de l’élément sont dérivées d’un système source (tel que Base de données SQL Server, Base de données Oracle) ou créées par un utilisateur.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriétés de racine communes
<p>
Ces propriétés s'appliquent à tous les types de ressources racines.

<table><tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr><tr><td>Nom</td><td>Chaîne</td><td>Un nom dérivé des informations d’emplacement de source de données</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Décrit la source de données de manière unique et est un des identificateurs de la ressource. (Voir la section identité double).  La structure du dsl varie selon le type de protocole et de source.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Plus de détails sur le type de ressource.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Décrit l'utilisateur qui a enregistré cette ressource le plus récemment.  Contient l’ID unique de l’utilisateur (l’UPN) ainsi qu’un nom complet (nom et prénom).</td></tr><tr><td>containerId</td><td>Chaîne</td><td>ID de la ressource de conteneur pour la source de données. Cette propriété n'est pas prise en charge pour le type Conteneur.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriétés d’annotation non singleton courantes
Ces propriétés s’appliquent à tous les types d’annotations non singleton (c’est-à-dire des annotations pouvant être multiples par ressource).

<table>
<tr><td><b>Nom de la propriété</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr>
<tr><td>key</td><td>Chaîne</td><td>Clé spécifiée par l’utilisateur, qui identifie de façon unique l’annotation dans la collection actuelle. La longueur de la clé ne peut pas dépasser 256 caractères.</td></tr>
</table>

### <a name="root-asset-types"></a>Types de ressources racines
Les types de ressources racines sont des types qui représentent les différents types de ressources de données pouvant être enregistrés dans le catalogue. Pour chaque type de racine, une vue permet de décrire la ressource et les annotations contenues dans la vue. Le nom de la vue doit être utilisé dans le segment d’URL {nom_vue} correspondant au moment de publier une ressource à l’aide de l’API REST.

<table><tr><td><b>Type de ressource (nom de la vue)</b></td><td><b>Propriétés supplémentaires</b></td><td><b>Type de données</b></td><td><b>Annotations autorisées</b></td><td><b>Commentaires</b></td></tr><tr><td>Table (« tables »)</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Schéma<p>ColumnDescription<p>ColumnTag<p> Expert<p>VERSION PRÉLIMINAIRE<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentation<p></td><td>Une table représente des données tabulaires.  Par exemple : une table SQL, un affichage SQL, une table tabulaire Analysis Services, une dimension multidimensionnelle Analysis Services, une table Oracle, etc.   </td></tr><tr><td>Mesures (« measures »)</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentation<p></td><td>Ce type représente une mesure Analysis Services.</td></tr><tr><td></td><td>mesure</td><td>Colonne</td><td></td><td>Métadonnées décrivant la mesure</td></tr><tr><td></td><td>isCalculated </td><td>Booléen</td><td></td><td>Indique si la mesure est calculée ou non.</td></tr><tr><td></td><td>measureGroup</td><td>Chaîne</td><td></td><td>Conteneur physique de mesure</td></tr><td>KPI (« kpis »)</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentation</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Chaîne</td><td></td><td>Conteneur physique de mesure</td></tr><tr><td></td><td>goalExpression</td><td>Chaîne</td><td></td><td>Une expression numérique MDX ou un calcul qui retourne la valeur cible de l'indicateur de performance clé.</td></tr><tr><td></td><td>valueExpression</td><td>Chaîne</td><td></td><td>Une expression numérique MDX qui retourne la valeur réelle de l'indicateur de performance clé.</td></tr><tr><td></td><td>statusExpression</td><td>Chaîne</td><td></td><td>Une expression MDX qui représente l'état de l'indicateur de performance clé à un point spécifié dans le temps.</td></tr><tr><td></td><td>trendExpression</td><td>Chaîne</td><td></td><td>Une expression MDX qui évalue la valeur de l’indicateur de performance clé au fil du temps. La tendance peut être n'importe quel critère de temps utile dans un contexte d’entreprise spécifique.</td>
<tr><td>Report (« reports »)</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentation<p></td><td>Ce type représente un rapport SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Chaîne</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Chaîne</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Chaîne</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Chaîne</td><td></td><td></td></tr><tr><td>Container (« containers »)</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentation<p></td><td>Ce type représente un conteneur d'autres ressources telles qu'une base de données SQL, un conteneur d'objets Blob Azure ou un modèle Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Types d'annotation
Les types d'annotation représentent des types de métadonnées qui peuvent être attribuées à d'autres types dans le catalogue.

<table>
<tr><td><b>Type d’annotation (nom de la vue imbriqué)</b></td><td><b>Propriétés supplémentaires</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr>

<tr><td>Description (« descriptions »)</td><td></td><td></td><td>Cette propriété contient une description de la ressource. Chaque utilisateur du système peut ajouter se propre description.  Seul cet utilisateur peut modifier l'objet Description.  (Les administrateurs et les propriétaires de ressources peuvent supprimer l’objet Description, mais pas le modifier). Le système conserve séparément les descriptions des utilisateurs.  Il y a donc un tableau des descriptions de chaque ressource (un pour chaque utilisateur qui a contribué à la ressource, en plus d’un éventuel tableau qui contient des informations dérivées de la source de données).</td></tr>
<tr><td></td><td>description</td><td>chaîne</td><td>Brève description (2 ou 3 lignes) de la ressource.</td></tr>

<tr><td>Tag (« tags »)</td><td></td><td></td><td>Cette propriété définit une balise pour une ressource. Chaque utilisateur du système peut ajouter plusieurs balises pour une ressource.  Seul l’utilisateur qui a créé des objets Tag peut modifier ceux-ci.  (Les propriétaires des ressources et les administrateurs peuvent supprimer l’objet Tag, mais pas le modifier). Le système conserve séparément les balises des utilisateurs.  Il y a donc un tableau d’objets Tag sur chaque ressource.</td></tr>
<tr><td></td><td>tag</td><td>chaîne</td><td>Balise décrivant la ressource.</td></tr>

<tr><td>FriendlyName (« friendlyName »)</td><td></td><td></td><td>Cette propriété contient un nom convivial pour une ressource. FriendlyName est une annotation singleton. Il n’est possible d’ajouter qu’un seul FriendlyName à une ressource.  Seul l’utilisateur ayant créé l’objet FriendlyName peut modifier celui-ci. (Les propriétaires des ressources et les administrateurs peuvent supprimer l’objet FriendlyName, mais pas le modifier). Le système conserve séparément les noms conviviaux des utilisateurs.</td></tr>
<tr><td></td><td>friendlyName</td><td>chaîne</td><td>Nom convivial de la ressource.</td></tr>

<tr><td>Schema (« schema »)</td><td></td><td></td><td>Le schéma décrit la structure des données.  Il répertorie les noms, les types d’attribut (colonne, attribut, champ, etc.), ainsi que d’autres métadonnées.  Ces informations sont issues de la source de données.  Le schéma est une annotation singleton ; il n’est possible d’ajouter qu’un seul schéma pour une ressource.</td></tr>
<tr><td></td><td>colonnes</td><td>Column[]</td><td>Un tableau d'objets de colonne. Ils décrivent la colonne avec des informations issues de la source de données.</td></tr>

<tr><td>ColumnDescription (« columnDescriptions »)</td><td></td><td></td><td>Cette propriété contient une description de la colonne.  Chaque utilisateur du système peut ajouter ses propres descriptions pour plusieurs colonnes (au maximum une par colonne). Seul l’utilisateur ayant créé des objets ColumnDescription peut les modifier.  (Les propriétaires des ressources et les administrateurs peuvent supprimer l’objet ColumnDescription mais pas le modifier). Le système conserve séparément les descriptions des colonnes de ces utilisateurs.  Il y a donc un tableau d’objets ColumnDescription sur chaque ressource (un par colonne pour chaque utilisateur ayant partagé sa connaissance de la colonne, plus éventuellement un autre contenant des informations dérivées de la source de données).  ColumnDescription est étroitement lié au schéma et peut donc parfois être désynchronisé. ColumnDescription peut décrire une colonne qui n’existe plus dans le schéma.  C’est à celui qui écrit qu’il incombe de synchroniser la description et le schéma.  La source de données peut également contenir des informations descriptives des colonnes. D’autres objets ColumnDescription doivent être créés lors de l’exécution de l’outil.</td></tr>
<tr><td></td><td>columnName</td><td>Chaîne</td><td>Nom de la colonne à laquelle cette description fait référence.</td></tr>
<tr><td></td><td>description</td><td>Chaîne</td><td>Brève description (2 ou 3 lignes) de la colonne.</td></tr>

<tr><td>ColumnTag (« columnTags »)</td><td></td><td></td><td>Cette propriété contient une balise pour une colonne. Chaque utilisateur du système peut ajouter plusieurs balises pour une colonne donnée et pour plusieurs colonnes. Seul l’utilisateur qui a créé des objets ColumnTag permettre les modifier. (Les propriétaires des ressources et les administrateurs peuvent supprimer l’objet ColumnTag mais pas le modifier). Le système conserve séparément les balises des colonnes de ces utilisateurs.  Il y a donc un tableau d’objets ColumnTag sur chaque ressource.  ColumnTag est étroitement lié au schéma et peut donc parfois être désynchronisé. ColumnTag peut décrire une colonne qui n’existe plus dans le schéma.  C’est à celui qui écrit qu’il incombe de synchroniser la balise de la colonne et le schéma.</td></tr>
<tr><td></td><td>columnName</td><td>Chaîne</td><td>Nom de la colonne à laquelle cette balise fait référence.</td></tr>
<tr><td></td><td>tag</td><td>Chaîne</td><td>Balise décrivant la colonne.</td></tr>

<tr><td>Expert (« experts »)</td><td></td><td></td><td>Cette propriété contient un utilisateur considéré comme un expert dans le jeu de données. Les avis d’experts (des descriptions) sont propagés vers le haut de l’expérience utilisateur lors de l’affichage des descriptions. Chaque utilisateur peut spécifier ses propres experts. Seul cet utilisateur peut modifier l’objet Experts. (Les propriétaires des ressources et les administrateurs peuvent supprimer les objets Expert mais pas les modifier).</td></tr>
<tr><td></td><td>expert</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Preview (« previews »)</td><td></td><td></td><td>L'aperçu contient un instantané des 20 premières lignes de données de la ressource. L’aperçu n’est utile que pour certains types de ressources (c’est à dire qu’il est pertinent pour Table, mais pas pour Measure).</td></tr>
<tr><td></td><td>preview</td><td>object[]</td><td>Tableau d'objets qui représentent une colonne.  Chaque objet possède un mappage de propriété vers une colonne avec une valeur pour cette colonne pour la ligne.</td></tr>

<tr><td>AccessInstruction (« accessInstructions »)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>chaîne</td><td>Le type mime du contenu.</td></tr>
<tr><td></td><td>Contenu</td><td>chaîne</td><td>Les instructions pour accéder à cette ressource de données. Il peut s’agir d’une URL, d’une adresse de messagerie ou d’un ensemble d’instructions.</td></tr>

<tr><td>TableDataProfile (« tableDataProfiles »)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Le nombre de lignes dans le jeu de données</td></tr>
<tr><td></td><td>size</td><td>long</td><td>La taille en octets du jeu de données.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>chaîne</td><td>L'heure de dernière modification du schéma</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>chaîne</td><td>L’heure de dernière modification du jeu de données (données ajoutées, modifiées ou supprimées)</td></tr>

<tr><td>ColumnsDataProfile (« columnsDataProfiles »)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colonnes</td></td><td>ColumnDataProfile[]</td><td>Tableau de profils de données de colonne.</td></tr>

<tr><td>ColumnDataClassification (« columnDataClassifications »)</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Chaîne</td><td>Le nom de la colonne que désigne cette classification.</td></tr>
<tr><td></td><td>classification ;</td><td>Chaîne</td><td>La classification des données dans cette colonne.</td></tr>

<tr><td>Documentation (« documentation »)</td><td></td><td></td><td>Une seule documentation peut être associée à une ressource donnée.</td></tr>
<tr><td></td><td>mimeType</td><td>chaîne</td><td>Le type mime du contenu.</td></tr>
<tr><td></td><td>Contenu</td><td>chaîne</td><td>Le contenu de la documentation.</td></tr>

</table>

### <a name="common-types"></a>Types courants
Les types courants peuvent être utilisés comme les types de propriétés, mais ne sont pas des éléments.

<table>
<tr><td><b>Type courant</b></td><td><b>Propriétés</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>chaîne</td><td>Décrit le type de source de données.  Par exemple : SQL Server, base de données Oracle, etc.  </td></tr>
<tr><td></td><td>objectType</td><td>chaîne</td><td>Décrit le type d’objet dans la source de données. Par exemple : table, affichage pour SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>chaîne</td><td>Requis. Décrit le protocole utilisé pour communiquer avec la source de données. Par exemple : « tds » pour SQl Server, « oracle » pour Oracle, etc. Reportez-vous à [Spécification de référence de la source de données - Structure DSL](data-catalog-dsr.md) pour obtenir la liste des protocoles actuellement pris en charge.</td></tr>
<tr><td></td><td>address</td><td>Dictionnaire<string, object></td><td>Requis. Cette propriété est un jeu de données propre au protocole qui sert à identifier la source de données référencée. Les données d’adresse sont limitées à un protocole particulier, ce qui signifie qu’elles n’ont aucun sens si le protocole n’est pas connu.</td></tr>
<tr><td></td><td>Authentification</td><td>chaîne</td><td>facultatif. Schéma d’authentification utilisé pour communiquer avec la source de données. Par exemple : windows, oauth, etc.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Dictionnaire<string, object></td><td>facultatif. Informations supplémentaires sur la façon de se connecter à une source de données.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Le serveur principal n’effectue aucune validation des propriétés fournies par rapport à AAD pendant la publication.</td></tr>
<tr><td></td><td>upn</td><td>chaîne</td><td>Adresse de messagerie unique de l'utilisateur. Doit être spécifié si objectId n’est pas fourni ou figure dans le contexte de la propriété « lastRegisteredBy » ; sinon, facultatif.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Identité AAD du groupe d’utilisateurs ou de sécurité. facultatif. Doit être spécifié si UPN n’est pas fourni ; sinon, facultatif.</td></tr>
<tr><td></td><td>firstName</td><td>chaîne</td><td>Prénom de l'utilisateur (à des fins d'affichage). facultatif. Valide uniquement dans le contexte de la propriété « lastRegisteredBy ». Ne peut pas être spécifié lors de la fourniture du principal de sécurité pour « roles », « permissions » et « experts ».</td></tr>
<tr><td></td><td>lastName</td><td>chaîne</td><td>Nom de l'utilisateur (à des fins d'affichage). facultatif. Valide uniquement dans le contexte de la propriété « lastRegisteredBy ». Ne peut pas être spécifié lors de la fourniture du principal de sécurité pour « roles », « permissions » et « experts ».</td></tr>

<tr><td>Colonne</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Nom</td><td>chaîne</td><td>Nom de la colonne ou de l'attribut.</td></tr>
<tr><td></td><td>Type</td><td>chaîne</td><td>type de données de la colonne ou de l'attribut. Les types autorisés dépendent du sourceType de données de la ressource.  Seul un sous-ensemble des types est pris en charge.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>La longueur maximale autorisée pour la colonne ou l'attribut. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr>
<tr><td></td><td>precision</td><td>byte</td><td>La précision de la colonne ou de l'attribut. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr>
<tr><td></td><td>isNullable</td><td>Booléen</td><td>Si la colonne est autorisée à avoir une valeur null ou non. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr>
<tr><td></td><td>expression</td><td>chaîne</td><td>Si la valeur est une colonne calculée, ce champ contient l’expression qui exprime la valeur. Dérivé de la source de données. Applicable uniquement à certains types de sources.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>chaîne</td><td>Nom de la colonne</td></tr>
<tr><td></td><td>Type </td><td>chaîne</td><td>Type de la colonne</td></tr>
<tr><td></td><td>min </td><td>chaîne</td><td>Valeur minimale dans le jeu de données</td></tr>
<tr><td></td><td>max </td><td>chaîne</td><td>Valeur maximale dans le jeu de données</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>Valeur moyenne dans le jeu de données</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>Écart type pour le jeu de données</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Nombre de valeurs null dans le jeu de données</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Nombre de valeurs distinctes dans le jeu de données</td></tr>


</table>

## <a name="asset-identity"></a>Identité des ressources
Azure Data Catalog utilise les propriétés de « protocol » et d’identité du conteneur de propriétés « address » de la propriété « dsl » DataSourceLocation pour générer l’identité de la ressource utilisée pour définir l’adresse de la ressource à l’intérieur de Catalog.
Par exemple : « server », « database », « schema », « object » sont des propriétés d’identité du protocole « tds ». Les combinaisons des propriétés du protocole et de l’identité sont utilisées pour générer l’identité de la ressource de table SQL Server.
Azure Data Catalog propose plusieurs protocoles de source de données intégrés qui sont répertoriés dans [Spécification de référence de la source de données - Structure DSL](data-catalog-dsr.md).
L’ensemble des protocoles pris en charge peut être étendu par programmation (consultez Informations de référence sur l’API REST Data Catalog). Les administrateurs de Catalog peuvent inscrire des protocoles de source de données personnalisés. Le tableau suivant décrit les propriétés nécessaires à l’inscription d’un protocole personnalisé.

### <a name="custom-data-source-protocol-specification"></a>Caractéristiques d’un protocole de source de données personnalisé
<table>
<tr><td><b>Type</b></td><td><b>Propriétés</b></td><td><b>Type de données</b></td><td><b>Commentaires</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>chaîne</td><td>Espace de noms du protocole. L’espace de noms doit se composer de 1 à 255 caractères, contenir une ou plusieurs parties non vides séparées par un point (.). Chaque partie doit se composer de 1 à 255 caractères, commencer par une lettre et contenir uniquement des chiffres et des lettres.</td></tr>
<tr><td></td><td>Nom</td><td>chaîne</td><td>Nom du protocole. Le nom doit se composer de 1 à 255 caractères, commencer par une lettre et contenir uniquement des chiffres, des lettres et des tirets (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Liste des propriétés d’identité ; doit contenir au moins une propriété, mais pas plus de 20. Par exemple : « server », « database », « schema », « object » sont des propriétés d’identité du protocole « tds ».</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Liste de jeux d’identité. Définit des ensembles de propriétés d’identité qui représentent l’identité valide d’une ressource. Doit contenir au moins un jeu, mais pas plus de 20. Par exemple : {« server », « database », « schema » et « object »} est un jeu d’identité pour le protocole « tds », qui définit l’identité d’une ressource de table SQL Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Nom</td><td>chaîne</td><td>Nom de la propriété. Le nom doit se composer de 1 à 100 caractères, commencer par une lettre et ne peut contenir que des chiffres et des lettres.</td></tr>
<tr><td></td><td>Type</td><td>chaîne</td><td>Type de la propriété. Les valeurs prises en charge sont les suivantes : « bool, « boolean », « byte », « guid », « int », « integer », « long », « string », « url »</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Indique si la casse doit être ignorée lors de l’utilisation de valeur de la propriété. Ne peut être spécifié que pour les propriétés de type « string ». La valeur par défaut est false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indique si la casse doit être ignorée pour chaque segment du chemin de l’URL. Ne peut être spécifié que pour les propriétés de type « url ». La valeur par défaut est [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Nom</td><td>chaîne</td><td>Nom du jeu d’identité.</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>Liste des propriétés d’identité incluses dans ce jeu d’identité. Elle ne peut pas contenir de doublons. Chaque propriété référencée par un jeu d’identité doit être définie dans la liste « identityProperties » du protocole.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Rôles et autorisations
Microsoft Azure Data Catalog offre des fonctionnalités d'autorisation pour les opérations CRUD sur les ressources et les annotations.

## <a name="key-concepts"></a>Concepts clés
Azure Data Catalog utilise deux mécanismes d'autorisation :

* Autorisation par rôle
* Autorisation basée sur les autorisations

### <a name="roles"></a>contrôleur
Il existe trois rôles : **administrateur**, **propriétaire** et **collaborateur**.  Chaque rôle a une portée et des droits, lesquels sont résumés dans le tableau suivant.

<table><tr><td><b>Rôle</b></td><td><b>Portée</b></td><td><b>Droits</b></td></tr><tr><td>Administrateur</td><td>Catalogue (toutes les ressources/annotations dans le catalogue)</td><td>Lecture Suppression ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Propriétaire</td><td>Chaque ressource (élément racine)</td><td>Lecture Suppression ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contributeur</td><td>Chaque ressource et annotation</td><td>Lecture Mise à jour Suppression ViewRoles Remarque : tous les droits sont révoqués si le droit de lecture sur l'élément est révoqué pour le collaborateur</td></tr></table>

> [!NOTE]
> Les droits de **Lecture**, **Mise à jour**, **Suppression**, **ViewRoles** sont applicables à tout élément (ressource ou annotation), tandis que **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility** et **ViewPermissions** sont uniquement applicables à la ressource racine.
> 
> **suppression** s’applique à un élément et à tout sous-élément ou élément unique inférieur. Par exemple, la suppression d’une ressource supprime également toutes les annotations pour cette ressource.
> 
> 

### <a name="permissions"></a>Autorisations
L'autorisation est une liste d'entrées de contrôle d'accès. Chaque entrée de contrôle d'accès affecte l'ensemble de droits à un principal de sécurité. Les autorisations ne peuvent être spécifiées que sur une ressource (c’est à dire, l’élément racine) et s’appliquent à la ressource et aux sous-éléments.

Pour la version préliminaire **d’Azure Data Catalog**, seul le droit de **lecture** est pris en charge dans la liste des autorisations pour activer le scénario de limitation de la visibilité d’une ressource.

Par défaut, tout utilisateur authentifié a un droit de **lecture** sur tout élément dans le catalogue, sauf si la visibilité est limitée au jeu de principaux dans les autorisations.

## <a name="rest-api"></a>de l’API REST
Les demandes d’éléments d’affichage **PUT** et **POST** peuvent être utilisées pour contrôler les rôles et les autorisations : en plus de la charge utile de l’élément, deux propriétés système peuvent être spécifiées, **roles** et **permissions**.

> [!NOTE]
> **permissions** ne peut s’appliquer qu’à un élément racine.
> 
> **Propriétaire** est uniquement applicable à un élément racine.
> 
> Par défaut, lorsqu'un élément est créé dans le catalogue, son **collaborateur** est défini sur l'utilisateur actuellement authentifié. Si tout le monde doit pouvoir mettre à jour l’élément, le **collaborateur** doit être défini sur le principal de sécurité spécial &lt;Everyone&gt; (Tout le monde) dans la propriété **roles** lors de la première publication de l’élément (voir l’exemple suivant). Le **collaborateur** ne peut pas être modifié et reste identique pendant la durée de vie d’un élément (même **l’administrateur** ou le **propriétaire** n’a pas le droit de modifier le **collaborateur**). La seule valeur prise en charge pour l’affectation explicite de **collaborateur** est &lt;Everyone&gt; : cela signifie que le **collaborateur** peut uniquement être un utilisateur qui a créé un élément ou &lt;Everyone&gt;.
> 
> 

### <a name="examples"></a>Exemples
**Définissez le collaborateur sur &lt;Everyone&gt; lors de la publication d’un élément.**
Le principal de sécurité spécial &lt;Everyone&gt; a l’objectId « 00000000-0000-0000-0000-000000000201 ».
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Certaines implémentations de client HTTP peuvent réémettre automatiquement des demandes en réponse à un message 302 du serveur mais, en général, elles suppriment les en-têtes d’autorisation de la demande. Étant donné que l’en-tête d’autorisation est nécessaire pour effectuer des demandes à Azure Data Catalog, vous devez vous assurer que l’en-tête d’autorisation est toujours fourni lors de la réémission d’une demande de redirection vers l’emplacement spécifié par Azure Data Catalog. L’exemple de code ci-dessous illustre cette utilisation de l’objet .NET HttpWebRequest.
> 
> 

**Corps**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Attribution des propriétaires et restriction de la visibilité pour un élément racine existant** : **PUT** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> Dans PUT, il n’est pas nécessaire de spécifier une charge utile d'élément dans le corps : PUT peut être utilisé pour mettre à jour uniquement les rôles et/ou les autorisations.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
