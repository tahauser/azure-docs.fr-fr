---
title: Prise en main de Data Catalog | Microsoft Docs
description: "Didacticiel de bout en bout présentant des scénarios et des fonctionnalités d’Azure Data Catalog."
documentationcenter: 
services: data-catalog
author: steelanddata
manager: jhubbard
editor: 
tags: 
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: spelluru
ms.openlocfilehash: d67f8871bbdd87e8a67057e6e8c5d4d770e5cad6
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-azure-data-catalog"></a>Prise en main d’Azure Data Catalog
Azure Data Catalog est un service cloud entièrement géré qui sert de système d’inscription et de découverte des ressources de données d’entreprise. Pour une présentation détaillée, consultez l’article [Qu’est-ce qu’Azure Data Catalog ?](data-catalog-what-is-data-catalog.md).

Ce didacticiel vous permet de commencer à utiliser Azure Data Catalog. Dans ce didacticiel, vous effectuez les procédures suivantes :

| Procédure | Description |
|:--- |:--- |
| [Approvisionner Data Catalog](#provision-data-catalog) |Dans cette procédure, vous créez ou configurez Azure Data Catalog. Vous effectuez cette étape uniquement si le catalogue n’a pas été configuré auparavant. Vous ne pouvez avoir qu’un seul catalogue de données par organisation (domaine Microsoft Azure Active Directory), même si plusieurs abonnements sont associés à votre compte Azure. |
| [Inscrire des ressources de données](#register-data-assets) |Dans cette procédure, vous inscrivez les ressources de données de l’exemple de base de données AdventureWorks2014 auprès du catalogue de données. L’inscription est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données, puis copiées dans le catalogue. La source de données et les ressources de données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour faciliter leur découverte et leur compréhension. |
| [Découvrir les ressources de données](#discover-data-assets) |Dans cette procédure, vous utilisez le portail Azure Data Catalog pour découvrir les ressources de données qui ont été inscrites à l’étape précédente. Une fois qu’une source de données a été inscrite auprès d’Azure Data Catalog, ses métadonnées sont indexées par le service. Les utilisateurs peuvent ainsi rechercher facilement les données dont ils ont besoin. |
| [Annoter les ressources de données](#annotate-data-assets) |Dans cette procédure, vous fournissez des annotations (des informations telles que des descriptions, des balises, de la documentation ou des experts) pour les ressources de données. Ces informations complètent les métadonnées extraites de la source de données et rendent ces dernières plus compréhensibles. |
| [Se connecter aux ressources de données](#connect-to-data-assets) |Dans cette procédure, vous ouvrez les ressources de données dans des outils clients intégrés (comme Excel et SQL Server Data Tools) et dans un outil non intégré (SQL Server Management Studio). |
| [Gérer les ressources de données](#manage-data-assets) |Dans cette procédure, vous configurez la sécurité de vos ressources de données. Data Catalog ne permet pas aux utilisateurs d’accéder aux données proprement dites. Le propriétaire de la source de données contrôle l’accès aux données. <br/><br/> Data Catalog vous permet de découvrir les sources de données et d’afficher les **métadonnées** relatives aux sources inscrites dans le catalogue. Il peut arriver, cependant, que certaines sources de données ne doivent être visibles que pour des utilisateurs spécifiques ou des membres de groupes spécifiques. Pour ces scénarios, vous pouvez utiliser Data Catalog pour vous approprier les ressources de données inscrites dans le catalogue et contrôler ensuite la visibilité des ressources que vous détenez. |
| [Supprimer les ressources de données](#remove-data-assets) |Dans cette procédure, vous apprenez à supprimer des ressources de données du catalogue de données. |

## <a name="tutorial-prerequisites"></a>Configuration requise pour le didacticiel
### <a name="azure-subscription"></a>Abonnement Azure
Pour configurer Azure Data Catalog, vous devez être le propriétaire ou le copropriétaire d'un abonnement Azure.

Les abonnements Azure vous permettent d’organiser l'accès aux ressources du service cloud telles qu’Azure Data Catalog. Ils vous permettent également de contrôler le signalement, la facturation et le paiement des ressources utilisées. Chaque abonnement peut disposer d’une configuration de facturation et de paiement différente. Vous pouvez donc avoir différents abonnements et différents plans par département, projet, bureau régional, etc. Chaque service cloud appartient à un abonnement. Vous devez donc avoir un abonnement avant de configurer Azure Data Catalog. Pour plus d’informations, consultez [Manage Accounts, Subscriptions, and Administrative Roles](../active-directory/active-directory-how-subscriptions-associated-directory.md) (Gérer les comptes, les abonnements et les rôles d’administrateur).

Si vous n'êtes pas abonné, vous pouvez créer un compte d'essai gratuit en quelques minutes. Pour plus d’informations, consultez la page [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="azure-active-directory"></a>Azure Active Directory
Pour configurer Azure Data Catalog, vous devez être connecté avec un compte d’utilisateur Azure Active Directory (Azure AD). Vous devez être propriétaire ou copropriétaire d’un abonnement Azure.  

Azure AD permet à votre entreprise de gérer facilement les identités et les accès, à la fois dans le cloud et en local. Vous pouvez utiliser un seul compte professionnel ou scolaire pour vous connecter à n’importe quelle application web locale ou dans le cloud. Azure Data Catalog utilise Azure AD pour authentifier la connexion. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Active Directory ?](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configuration de la stratégie Azure Active Directory
Il arrive que vous puissiez vous connecter au portail d’Azure Data Catalog, mais que lorsque vous tentez de vous connecter à l’outil de référencement de la source de données, un message d’erreur s’affiche et vous empêche de vous connecter. Cette erreur peut se produire lorsque vous êtes sur le réseau d’entreprise ou lorsque vous vous y connectez.

L’outil de référencement utilise *l’authentification par formulaire* pour valider les connexions des utilisateurs avec Azure Active Directory. Pour une connexion réussie, un administrateur Azure Active Directory doit activer l’authentification par formulaire dans la *stratégie d’authentification globale*.

La stratégie d’authentification globale vous permet d’activer séparément des méthodes d’authentification pour les connexions intranet et extranet, comme illustré dans l’image suivante. Des erreurs de connexion peuvent survenir si l’authentification par formulaire n’est pas activée pour le réseau à partir duquel vous vous connectez.

 ![Stratégie d’authentification globale Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Pour plus d’informations, consultez [Configuration des stratégies d’authentification](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Approvisionner Data Catalog
Vous ne pouvez approvisionner qu’un seul catalogue de données par organisation (domaine Azure Active Directory). Par conséquent, si le propriétaire ou le copropriétaire d’un abonnement Azure qui fait partie de ce domaine Azure Active Directory a déjà créé un catalogue, vous ne pourrez pas en créer un autre, même si vous disposez de plusieurs abonnements Azure. Pour tester si un catalogue de données a été créé par un utilisateur dans votre domaine Azure Active Directory, accédez à la [page d’accueil Azure Data Catalog](http://azuredatacatalog.com) et vérifiez si le catalogue y figure. Ignorez la procédure suivante et passez à la section suivante si un catalogue a déjà été créé pour vous.    

1. Accédez à la [page du service Data Catalog](https://azure.microsoft.com/services/data-catalog) et cliquez sur **Prise en main**.
   
    ![Azure Data Catalog--page d’accueil marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Connectez-vous à l’aide d’un compte d’utilisateur propriétaire ou copropriétaire d’un abonnement Azure. Une fois que vous êtes connecté, la page suivante s’affiche.
   
    ![Azure Data Catalog--approvisionner data catalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Spécifiez un **nom** pour le catalogue de données, **l’abonnement** à utiliser et **l’emplacement** du catalogue.
4. Développez **Prix appliqués** et sélectionnez une **édition** d’Azure Data Catalog (gratuite ou standard).
    ![Azure Data Catalog--sélectionner édition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Développez **Utilisateurs du catalogue** et cliquez sur **Ajouter** pour ajouter des utilisateurs au catalogue de données. Vous êtes automatiquement ajouté à ce groupe.
    ![Azure Data Catalog--utilisateurs](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Développez **Administrateurs du catalogue** et cliquez sur **Ajouter** pour ajouter des administrateurs supplémentaires au catalogue de données. Vous êtes automatiquement ajouté à ce groupe.
    ![Azure Data Catalog--administrateurs](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Cliquez sur **Créer un catalogue** pour créer le catalogue de données de votre organisation. Une fois le catalogue créé, la page d’accueil du catalogue de données s’affiche.
    ![Azure Data Catalog--créé](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Rechercher un catalogue de données dans le portail Azure
1. Dans un onglet distinct du navigateur web ou dans une fenêtre de navigateur web distincte, accédez au [portail Azure](https://portal.azure.com) et connectez-vous en utilisant le compte que vous avez utilisé pour créer le catalogue de données à l’étape précédente.
2. Cliquez sur **Parcourir**, puis sur **Catalogue de données**.
   
    ![Azure Data Catalog--parcourir Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Le catalogue de données que vous avez créé s’affiche.
   
    ![Azure Data Catalog--afficher le catalogue dans une liste](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Cliquez sur le catalogue que vous avez créé. Le panneau **Catalogue de données** s’affiche dans le portail.
   
   ![Azure Data Catalog--panneau dans le portail ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Vous pouvez afficher les propriétés du catalogue de données et les mettre à jour. Par exemple, cliquez sur **Niveau tarifaire** et modifiez l’édition.
   
    ![Azure Data Catalog--niveau tarifaire](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Exemple de base de données Adventure Works
Dans ce didacticiel, vous inscrivez les ressources de données (tables) de l’exemple de base de données AdventureWorks2014 pour le moteur de base de données SQL Server. Vous pouvez cependant utiliser n’importe quelle source de données prise en charge si vous préférez travailler avec des données plus familières et mieux adaptées à votre rôle. Pour obtenir la liste des sources de données prises en charge, consultez l’article [Sources de données prises en charge](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Installer la base de données Adventure Works OLTP 2014
La base de données Adventure Works prend en charge les scénarios de traitement des transactions en ligne standard pour un fabricant fictif de bicyclettes (Adventure Works Cycles), y compris les produits, les ventes et les achats. Dans ce didacticiel, vous inscrivez des informations sur les produits dans Azure Data Catalog.

Pour installer l’exemple de base de données Adventure Works :

1. Téléchargez [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) sur CodePlex.
2. Pour restaurer la base de données sur votre ordinateur, suivez les instructions de l’article [Restaurer une sauvegarde de base de données (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx)ou suivez les étapes suivantes :
   1. Ouvrez SQL Server Management Studio et connectez-vous au moteur de base de données SQL Server.
   2. Cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Restaurer la base de données**.
   3. Sous **Restaurer la base de données**, sélectionnez l’option **Appareil** comme **Source** et cliquez sur **Parcourir**.
   4. Sous **Sélectionner les unités de sauvegarde**, cliquez sur **Ajouter**.
   5. Accédez au dossier qui contient le fichier **AdventureWorks2014.bak**, sélectionnez le fichier, puis cliquez sur **OK** pour fermer la boîte de dialogue **Localiser le fichier de sauvegarde**.
   6. Cliquez sur **OK** pour fermer la boîte de dialogue **Sélectionner les unités de sauvegarde**.    
   7. Cliquez sur **OK** pour fermer la boîte de dialogue **Restaurer la base de données**.

Vous pouvez désormais inscrire les ressources de données de l’exemple de base de données Adventure Works auprès d’Azure Data Catalog.

## <a name="register-data-assets"></a>Inscrire des ressources de données
Dans cet exercice, vous allez utiliser l’outil de référencement pour inscrire les ressources de données de la base de données Adventure Works auprès du catalogue. L’inscription est le processus par lequel les métadonnées structurelles clés (telles que les noms, les types et les emplacements) sont extraites de la source de données et des ressources qu’elle contient, puis copiées dans le catalogue. La source de données et les ressources de données restent au même endroit, mais les métadonnées sont utilisées par le catalogue pour faciliter leur découverte et leur compréhension.

### <a name="register-a-data-source"></a>Référencer une source de données
1. Accédez à la [page d’accueil Azure Data Catalog](http://azuredatacatalog.com) , puis cliquez sur **Publier des données**.
   
   ![Azure Data Catalog--bouton Publier des données](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Cliquez sur **Lancer l’application** pour télécharger, installer et exécuter l’outil de référencement sur votre ordinateur.
   
   ![Azure Data Catalog--bouton de lancement](media/data-catalog-get-started/data-catalog-launch-application.png)
3. Sur la page **Bienvenue**, cliquez sur **Connexion**, puis entrez vos informations d’identification.     
   
    ![Azure Data Catalog--page d’accueil](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. Sur la page **Microsoft Azure Data Catalog**, cliquez sur **SQL Server** et **Suivant**.
   
    ![Azure Data Catalog--sources de données](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Entrez les propriétés de connexion SQL Server pour **AdventureWorks2014** (voir l’exemple ci-dessous), puis cliquez sur **SE CONNECTER**.
   
   ![Azure Data Catalog--paramètres de connexion à SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Enregistrez les métadonnées de votre ressource de données. Dans cet exemple, vous inscrivez les objets **Production/Product** à partir de l’espace de noms AdventureWorks Production :
   
   1. Dans l’arborescence **Hiérarchie du serveur**, développez **AdventureWorks2014**, puis cliquez sur **Production**.
   2. Appuyez sur la touche CTRL et cliquez sur **Product**, **ProductCategory**, **ProductDescription** et **ProductPhoto**.
   3. Cliquez sur la **flèche de déplacement des objets sélectionnés** (**>**). Cette action déplace tous les objets sélectionnés dans la liste **Objets à inscrire** .
      
      ![Didacticiel Azure Data Catalog--parcourir et sélectionner des objets](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Sélectionnez **Include a Preview (Inclure un aperçu)** pour inclure un aperçu instantané des données. L’instantané inclut jusqu’à 20 enregistrements de chaque table et est copié dans le catalogue.
   5. Sélectionnez **Inclure le profil de données** pour inclure un instantané des statistiques des objets du profil de données (par exemple, les valeurs minimale, maximale et moyenne d’une colonne ou le nombre de lignes).
   6. Dans le champ **Add tags (Ajouter des balises)**, entrez **adventure works, cycles**. Cette action ajoute des étiquettes de recherche à ces ressources de données. Les étiquettes sont un excellent moyen d’aider les utilisateurs à trouver une source de données inscrite.
   7. Spécifiez le nom d’un **expert** pour ces données (facultatif).
      
      ![Didacticiel Azure Data Catalog--objets à inscrire](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Cliquez sur **INSCRIRE**. Azure Data Catalog enregistre les objets que vous avez sélectionnés. Dans cet exercice, les objets sélectionnés à partir d’Adventure Works sont enregistrés. L’outil de référencement extrait les métadonnées de la ressource de données et les copie dans le service Azure Data Catalog. Les données restent à leur emplacement d’origine, toujours sous le contrôle des administrateurs et des stratégies du système actuel.
      
      ![Azure Data Catalog--objets inscrits](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Pour afficher les objets de source de données que vous avez inscrits, cliquez sur **Afficher le portail**. Dans le portail Azure Data Catalog, vérifiez que les quatre tables et la base de données sont affichés en mode Grille.
      
      ![Objets dans le portail Azure Data Catalog ](media/data-catalog-get-started/data-catalog-view-portal.png)

Dans cet exercice, vous avez inscrit des objets à partir de l’exemple de base de données Adventure Works afin qu’ils soient facilement détectables par les utilisateurs de l’ensemble de votre organisation. Dans l’exercice suivant, vous apprenez à découvrir les ressources de données inscrites.

## <a name="discover-data-assets"></a>Découvrir les ressources de données
Dans Azure Data Catalog, la découverte utilise deux mécanismes principaux : la recherche et le filtrage.

La recherche est conçue pour être intuitive et puissante. Par défaut, les termes de recherche sont comparés à toutes les propriétés du catalogue, notamment aux annotations fournies par l’utilisateur.

Le filtrage est conçu pour compléter la recherche. Vous pouvez sélectionner des caractéristiques spécifiques telles que les experts, le type de source de données, le type d’objet et les balises, pour afficher les ressources de données correspondantes et pour limiter les résultats de la recherche aux ressources correspondantes.

En combinant recherche et filtrage, vous pouvez parcourir rapidement les sources de données qui ont été inscrites auprès d’Azure Data Catalog pour découvrir celles qui vous sont utiles.

Dans cet exercice, vous utilisez le portail Azure Data Catalog pour découvrir les ressources de données que vous avez enregistrées dans l’exercice précédent. Pour plus d’informations sur la syntaxe de recherche, consultez l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx) .

Voici quelques exemples de découverte des ressources de données dans le catalogue.  

### <a name="discover-data-assets-with-basic-search"></a>Découvrir les ressources de données à l’aide de la fonction de recherche de base
La recherche de base vous permet d’effectuer des recherches dans le catalogue en utilisant un ou plusieurs termes de recherche. Les résultats sont toutes les ressources correspondant à une propriété quelconque avec un ou plusieurs termes spécifiés.

1. Cliquez sur **Accueil** dans le portail Azure Data Catalog. Si vous avez fermé le navigateur web, accédez à la [page d’accueil Azure Data Catalog](https://www.azuredatacatalog.com).
2. Dans la zone de recherche, entrez `cycles` et appuyez sur **ENTRÉE**.
   
    ![Azure Data Catalog--recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Vérifiez que les quatre tables et la base de données (AdventureWorks2014) s’affichent dans les résultats. Vous pouvez basculer entre le **mode Grille** et le **mode Liste** en cliquant sur les boutons de la barre d’outils, comme l’illustre l’image suivante. Le mot clé de recherche est mis en surbrillance dans les résultats de recherche, car l’option **Mettre en surbrillance** est **ACTIVÉE**. Vous pouvez également spécifier le nombre de **résultats par page** dans les résultats de recherche.
   
    ![Azure Data Catalog--résultats de recherche de texte de base](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    Le panneau **Recherches** et le panneau **Propriétés** s’affichent respectivement à gauche et à droite. Le panneau **Recherches** vous permet de modifier les critères de recherche et de filtrer les résultats. Le panneau **Propriétés** affiche les propriétés d’un objet sélectionné dans la grille ou liste.
4. Cliquez sur **Product** dans les résultats de la recherche. Cliquez sur les onglets **Aperçu**, **Colonnes**, **Profil de données** et **Documentation**, ou cliquez sur la flèche pour développer le volet inférieur.  
   
    ![Azure Data Catalog--volet inférieur](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    L’aperçu des données de la table **Product** s’affiche dans l’onglet **Aperçu**.  
5. Cliquez sur l’onglet **Colonnes** pour rechercher des informations sur les colonnes (comme le **nom** et le **type** de données) dans la ressource de données.
6. Cliquez sur l’onglet **Profil de données** pour afficher le profilage des données (par exemple, le nombre de lignes, la taille des données ou la valeur minimale dans une colonne) dans la ressource de données.
7. Filtrez les résultats à l’aide de l’option **Filtres** située sur la gauche. Par exemple, cliquez sur **Table** comme **Type d’objet**. Les quatre tables s’affichent, mais pas la base de données.
   
    ![Azure Data Catalog--filtrer les résultats de recherche](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Découvrir les ressources de données à l’aide de la fonction de recherche d’étendue de la propriété
L’étendue de la propriété vous permet de découvrir les ressources de données dans lesquelles le terme recherché correspond à la propriété spécifiée.

1. Désactivez le filtre **Table** sous **Type d’objet** dans **Filtres**.  
2. Dans la zone de recherche, entrez `tags:cycles` et appuyez sur **ENTRÉE**. Consultez l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx) pour connaître toutes les propriétés que vous pouvez utiliser pour effectuer des recherches dans le catalogue de données.
3. Vérifiez que les quatre tables et la base de données (AdventureWorks2014) s’affichent dans les résultats.  
   
    ![Data Catalog--résultats de recherche d’étendue de la propriété](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Enregistrer la recherche
1. Dans le volet **Recherches** de la section **Recherche actuelle**, nommez la recherche et cliquez sur **Enregistrer**.
   
    ![Azure Data Catalog--enregistrer la recherche](media/data-catalog-get-started/data-catalog-save-search.png)
2. Vérifiez que la recherche enregistrée s’affiche sous **Recherches enregistrées**.
   
    ![Azure Data Catalog--recherches enregistrées](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Sélectionnez l’une des actions que vous pouvez effectuer sur la recherche enregistrée (**Renommer**, **Supprimer**, **Définir comme recherche par défaut**).
   
    ![Azure Data Catalog--options de recherche enregistrées](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Opérateurs booléens
Vous pouvez élargir ou affiner votre recherche à l’aide des opérateurs booléens.

1. Dans la zone de recherche, entrez `tags:cycles AND objectType:table`et appuyez sur **ENTRÉE**.
2. Vérifiez que les résultats contiennent uniquement les tables (et pas la base de données).  
   
    ![Azure Data Catalog--opérateur booléen dans la recherche](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Parenthèses de regroupement
Les parenthèses de regroupement vous permettent de grouper les parties de la requête à isoler logiquement, notamment avec des opérateurs booléens.

1. Dans la zone de recherche, entrez `name:product AND (tags:cycles AND objectType:table)` et appuyez sur **ENTRÉE**.
2. Vérifiez que seule la table **Product** s’affiche dans les résultats de la recherche.
   
    ![Azure Data Catalog--recherche par regroupement](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Opérateurs de comparaison
Les opérateurs de comparaison vous permettent d’utiliser des comparaisons autres que l’égalité pour les propriétés comportant des types de données numériques et de date.

1. Dans la zone de recherche, entrez `lastRegisteredTime:>"06/09/2016"`.
2. Désactivez le filtre **Table** sous **Type d’objet**.
3. Appuyez sur **ENTRÉE**.
4. Vérifiez que les tables **Product**, **ProductCategory**, **ProductDescription** et **ProductPhoto**, ainsi que la base de données AdventureWorks2014 que vous avez enregistrées s’affichent dans les résultats de recherche.
   
    ![Azure Data Catalog--résultats de recherche par comparaison](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Consultez l’article [How to discover data assets (Découverte des ressources de données)](data-catalog-how-to-discover.md) pour plus d’informations sur la découverte des ressources de données et l’article [Data Catalog Search syntax reference (Informations de référence sur la syntaxe de recherche dans Data Catalog)](https://msdn.microsoft.com/library/azure/mt267594.aspx) pour connaître la syntaxe de recherche.

## <a name="annotate-data-assets"></a>Annoter les ressources de données
Dans cet exercice, vous allez utiliser le portail Azure Data Catalog pour annoter (ajouter des descriptions, des balises ou des experts) les ressources de données préalablement inscrites dans le catalogue. Les annotations que vous ajoutez compléteront et améliorent les métadonnées structurelles extraites de la source de données au cours de l’inscription et facilitent la découverte et la compréhension des ressources de données.

Dans cet exercice, vous annotez une ressource de données unique (ProductPhoto). Vous ajoutez un nom convivial et une description à la ressource de données ProductPhoto.  

1. Accédez à la [page d’accueil Azure Data Catalog](https://www.azuredatacatalog.com) et recherchez avec `tags:cycles` les ressources de données que vous avez enregistrées.  
2. Cliquez sur **ProductPhoto** dans les résultats de la recherche.  
3. Entrez **Images de produit** comme **Nom convivial** et **Photos de produit pour les documents marketing** comme **Description**.
   
    ![Azure Data Catalog--description d’une photo de produit](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    La **Description** aide les autres utilisateurs à découvrir et à comprendre pourquoi et comment utiliser la ressource de données sélectionnée. Vous pouvez également ajouter des balises supplémentaires et afficher les colonnes. Maintenant, vous pouvez essayer de rechercher et de filtrer pour découvrir les ressources de données à l’aide des métadonnées descriptives que vous avez ajoutées au catalogue.

Vous pouvez également effectuer les opérations suivantes dans cette page :

* Ajouter des experts pour la ressource de données. Cliquez sur **Ajouter** in the **Experts** .
* Ajouter des balises au niveau du jeu de données. Cliquez sur **Ajouter** in the **Balises** . Une balise peut être une balise utilisateur ou une balise glossaire. L’édition Standard de Data Catalog comprend un glossaire métier qui permet aux administrateurs de catalogue de définir une taxonomie commerciale centrale. Les utilisateurs du catalogue peuvent ensuite annoter les ressources de données avec la terminologie du glossaire. Pour plus d’informations, consultez l’article [Comment configurer le glossaire métier pour un balisage géré](data-catalog-how-to-business-glossary.md)
* Ajouter des balises au niveau des colonnes. Cliquez sur **Ajouter** under **Balises** pour la colonne que vous souhaitez annoter.
* Ajouter une description au niveau des colonnes. Entrez une **description** pour une colonne. Vous pouvez également afficher les métadonnées de description extraites de la source de données.
* Ajoutez des informations sur le champ **Demander l’accès** pour expliquer aux utilisateurs comment demander un accès à la ressource de données.
  
    ![Azure Data Catalog--ajouter des balises, des descriptions](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Sélectionnez l’onglet **Documentation** et fournissez une documentation pour la ressource de données. La documentation Azure Data Catalog vous permet d’utiliser votre catalogue de données comme référentiel de contenu pour créer une narration complète de vos ressources de données.
  
    ![Azure Data Catalog--onglet Documentation](media/data-catalog-get-started/data-catalog-documentation.png)

Vous pouvez également ajouter une annotation à plusieurs ressources de données. Par exemple, vous pouvez sélectionner toutes les ressources de données que vous avez inscrites et leur spécifier un expert.

![Azure Data Catalog--annoter plusieurs ressources de données](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog prend en charge une approche basée sur le crowdsourcing pour les annotations. Tous les utilisateurs de Data Catalog peuvent ajouter des balises (utilisateur ou de glossaire), des descriptions et d’autres métadonnées afin que tout utilisateur ayant un point de vue sur une ressource de données et son utilisation puisse capturer ce point de vue et le mettre à la disposition des autres utilisateurs.

Consultez l’article [How to annotate data assets (Annotation des ressources de données)](data-catalog-how-to-annotate.md) pour obtenir des informations détaillées sur l’annotation des ressources de données.

## <a name="connect-to-data-assets"></a>Se connecter aux ressources de données
Dans cet exercice, vous ouvrez les ressources de données dans un outil client intégré (Excel) et dans un outil non intégré (SQL Server Management Studio) à l’aide des informations de connexion.

> [!NOTE]
> Il est important de rappeler qu’Azure Data Catalog ne vous donne pas accès à la source de données elle-même : il vous permet simplement de découvrir et de comprendre plus facilement cette source de données. Lorsque vous vous connectez à une source de données, l’application cliente que vous choisissez utilise vos informations d’identification Windows ou vous invite, si nécessaire, à saisir vos informations d’identification. Si l’accès à la source de données ne vous a pas été préalablement accordé, vous devez obtenir cet accès avant de pouvoir vous connecter.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Se connecter à une ressource de données à partir d’Excel
1. Sélectionnez **Produit** dans les résultats de la recherche. Dans la barre d’outils, cliquez sur **Ouvrir dans**, puis sur **Excel**.
   
    ![Azure Data Catalog--se connecter à une ressource de données](media/data-catalog-get-started/data-catalog-connect1.png)
2. Dans la fenêtre de téléchargement, cliquez sur **Ouvrir** . Cette expérience peut varier en fonction du navigateur.
   
    ![Azure Data Catalog--télécharger un fichier de connexion Excel](media/data-catalog-get-started/data-catalog-download-open.png)
3. Dans la fenêtre **Avis de sécurité Microsoft Excel**, cliquez sur **Activer**.
   
    ![Azure Data Catalog--fenêtre contextuelle de sécurité Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Conservez les valeurs par défaut dans la boîte de dialogue **Importer des données** et cliquez sur **OK**.
   
    ![Azure Data Catalog--données d’importation Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. La source de données s’affiche dans Excel.
   
    ![Azure Data Catalog--table de produits dans Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Dans cet exercice, vous vous êtes connecté aux ressources de données découvertes à l’aide d’Azure Data Catalog. Le portail Azure Data Catalog vous permet de vous connecter directement à l’aide d’applications clientes intégrées au menu **Ouvrir dans** . Vous pouvez également vous connecter avec l’application de votre choix en utilisant les informations d’emplacement de connexion incluses dans les métadonnées des ressources. Exemple : vous pouvez utiliser SQL Server Management Studio pour vous connecter à la base de données AdventureWorks2014 afin d’accéder aux données des ressources de données inscrites dans ce didacticiel.

1. Ouvrez **SQL Server Management Studio**.
2. Dans la boîte de dialogue **Se connecter au serveur**, entrez le nom du serveur dans le volet **Propriétés** du portail Azure Data Catalog.
3. Utilisez l’authentification et les informations d’identification appropriées pour accéder à la ressource de données. Si vous n’avez pas accès à la ressource, utilisez les informations du champ **Demander l’accès** pour l’obtenir.
   
    ![Azure Data Catalog--demander l’accès](media/data-catalog-get-started/data-catalog-request-access.png)

Cliquez sur **Afficher les chaînes de connexion** pour afficher et copier les chaînes de connexion OLEDB, ODBC et ADF.NET dans le Presse-papiers pour les utiliser dans votre application.

## <a name="manage-data-assets"></a>Gérer les ressources de données
Dans cette étape, vous apprenez à configurer la sécurité de vos ressources de données. Data Catalog ne permet pas aux utilisateurs d’accéder aux données proprement dites. Le propriétaire de la source de données contrôle l’accès aux données.

Data Catalog vous permet de découvrir les sources de données et d’afficher les métadonnées relatives aux sources inscrites dans le catalogue. Il peut arriver, cependant, que certaines sources de données ne doivent être visibles que pour des utilisateurs spécifiques ou des membres de groupes spécifiques. Pour ces scénarios, vous pouvez utiliser Data Catalog pour vous approprier les ressources de données inscrites dans le catalogue et contrôler ensuite la visibilité des ressources que vous détenez.

> [!NOTE]
> Les fonctionnalités de gestion décrites dans cet exercice sont disponibles uniquement dans l’édition Standard d’Azure Data Catalog, et non dans l’édition gratuite.
> Dans Azure Data Catalog, vous pouvez vous approprier des ressources de données, ajouter des copropriétaires aux ressources de données et définir la visibilité de ces ressources.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>S’approprier les ressources de données et restreindre leur visibilité
1. Accédez à la [page d’accueil Azure Data Catalog](https://www.azuredatacatalog.com). Dans la zone de texte **Recherche**, entrez `tags:cycles` et appuyez sur **ENTRÉE**.
2. Cliquez sur un élément dans la liste des résultats, puis sur **Prendre possession** dans la barre d’outils.
3. Dans la section **Gestion** du panneau **Propriétés**, cliquez sur **Prendre possession**.
   
    ![Azure Data Catalog--prendre possession](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Pour restreindre la visibilité, sélectionnez **Propriétaires et ces utilisateurs** dans la section **Visibilité** et cliquez sur **Ajouter**. Entrez les adresses de messagerie des utilisateurs dans la zone de texte et appuyez sur **ENTRÉE**.
   
    ![Azure Data Catalog--restreindre l’accès](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Supprimer les ressources de données
Dans cet exercice, vous utilisez le portail Azure Data Catalog pour supprimer les aperçus de données des ressources de données inscrites et pour supprimer les ressources de données du catalogue.

Dans Azure Data Catalog, vous pouvez supprimer une ou plusieurs ressources.

1. Accédez à la [page d’accueil Azure Data Catalog](https://www.azuredatacatalog.com).
2. Dans la zone de texte **Recherche**, entrez `tags:cycles` et cliquez sur **ENTRÉE**.
3. Sélectionnez un élément dans la liste des résultats et cliquez sur **Supprimer** dans la barre d’outils, comme illustré dans l’image suivante :
   
    ![Azure Data Catalog--supprimer un élément de grille](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Si vous utilisez le mode Liste, la case à cocher se situe à gauche de l’élément, comme indiqué dans l’image suivante :
   
    ![Azure Data Catalog--supprimer un élément de liste](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Vous pouvez également sélectionner et supprimer plusieurs ressources de données comme indiqué dans l’image suivante :
   
    ![Azure Data Catalog--supprimer plusieurs ressources de données](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> Par défaut, le catalogue permet aux utilisateurs d’inscrire n’importe quelle source de données et de supprimer toutes les ressources de données préalablement inscrites. Les fonctionnalités de gestion incluses dans l’édition Standard d’Azure Data Catalog offrent des options supplémentaires qui permettent l’appropriation de ressources, ce qui limite les fonctions de découverte et de suppression des ressources à certains utilisateurs.
> 
> 

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez exploré les fonctionnalités essentielles d’Azure Data Catalog, notamment l’inscription, l’annotation, la découverte et la gestion des ressources de données d’entreprise. Maintenant que vous avez terminé ce didacticiel, il est temps de passer à la pratique. Vous pouvez commencer dès aujourd’hui en enregistrant les sources de données que vous et votre équipe utilisez, et en invitant vos collègues à utiliser le catalogue.

## <a name="references"></a>Références
* [How to register data assets (Inscription des ressources de données)](data-catalog-how-to-register.md)
* [How to discover data assets (Découverte des ressources de données)](data-catalog-how-to-discover.md)
* [How to annotate data assets (Annotation des ressources de données)](data-catalog-how-to-annotate.md)
* [How to document data assets (Documentation des ressources de données)](data-catalog-how-to-documentation.md)
* [How to connect to data assets (Connexion aux ressources de données)](data-catalog-how-to-connect.md)
* [Gestion des ressources de données](data-catalog-how-to-manage.md)

