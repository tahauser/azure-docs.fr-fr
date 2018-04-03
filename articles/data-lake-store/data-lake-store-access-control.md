---
title: Vue d’ensemble du contrôle d’accès dans Data Lake Store | Microsoft Docs
description: Comprendre le fonctionnement du contrôle d’accès dans Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: a2e29fd6f2dbd4bd573b780a14bd09c0cd03395f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="access-control-in-azure-data-lake-store"></a>Contrôle d’accès dans Azure Data Lake Store

Azure Data Lake Store met en œuvre un modèle de contrôle d’accès dérivé de HDFS, lui-même issu du modèle de contrôle d’accès POSIX. Cet article présente les notions de base du modèle de contrôle d’accès pour Data Lake Store. Pour en savoir plus sur le modèle de contrôle d’accès HDFS, consultez [HDFS Permissions Guide (Guide des autorisations HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listes de contrôle d’accès sur les fichiers et dossiers

Il existe deux types de listes de contrôle d’accès (ACL) : les **ACL d’accès** et les **ACL par défaut**.

* **ACL d’accès** : elles contrôlent l’accès à un objet. Les fichiers et les dossiers ont tous des ACL d’accès.

* **ACL par défaut** : « modèle » d’ACL associées à un dossier, qui déterminent les ACL d’accès pour tous les éléments enfants créés dans ce dossier. Les fichiers n’ont pas d’ACL par défaut.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Les ACL d’accès et les ACL par défaut ont la même structure.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> La modification de l’ACL par défaut d’un parent n’affecte pas l’ACL d’accès ni l’ACL par défaut des éléments enfants qui existent déjà.
>
>

## <a name="users-and-identities"></a>Utilisateurs et identités

Chaque fichier et dossier dispose d’autorisations distinctes pour ces identités :

* L’utilisateur propriétaire du fichier
* Le groupe propriétaire
* Les utilisateurs nommés
* Les groupes nommés
* Tous les autres utilisateurs

Les identités des utilisateurs et des groupes sont des identités Azure Active Directory (Azure AD). Sauf mention contraire, dans le contexte de Data Lake Store, un « utilisateur » désigne un utilisateur Azure AD ou un groupe de sécurité Azure AD.

## <a name="permissions"></a>Autorisations

Les autorisations sur un objet de système de fichiers sont **Lecture**, **Écriture** et **Exécution**. Elles peuvent être utilisées sur les fichiers et les dossiers comme l’indique le tableau ci-dessous :

|            |    Fichier     |   Dossier |
|------------|-------------|----------|
| **Lecture (R)** | Permet de lire le contenu d’un fichier | Requiert les autorisations **Lecture** et **Exécution** pour répertorier le contenu du dossier|
| **Écriture (W)** | Permet d’écrire ou d’ajouter du contenu dans un fichier | Requiert les autorisations **Écriture** et **Exécution** pour créer des éléments enfants dans un dossier |
| **Exécution (X)** | Ne signifie rien dans le contexte de Data Lake Store | Requise pour parcourir les éléments enfants d’un dossier |

### <a name="short-forms-for-permissions"></a>Formes abrégées des autorisations

**RWX** correspond à **Lecture + Écriture + Exécution**. Il existe une forme numérique plus condensée dans laquelle **Lecture = 4**, **Écriture = 2** et **Exécution = 1**. Les autorisations sont représentées par la somme de ces chiffres. Voici quelques exemples.

| Forme numérique | Forme abrégée |      Signification     |
|--------------|------------|------------------------|
| 7            | RWX        | Lecture + Écriture + Exécution |
| 5.            | R-X        | Lecture + Exécution         |
| 4            | R--        | Lire                   |
| 0            | ---        | | Aucune autorisation         |


### <a name="permissions-do-not-inherit"></a>Les autorisations ne se transmettent pas en héritage

Dans le modèle POSIX utilisé par Data Lake Store, les autorisations d’un élément sont stockées sur l’élément lui-même. En d’autres termes, les autorisations d’un élément ne peuvent pas être héritées des éléments parents.

## <a name="common-scenarios-related-to-permissions"></a>Scénarios courants liés aux autorisations

Voici quelques scénarios courants pour vous aider à comprendre les autorisations nécessaires pour effectuer certaines opérations sur un compte Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Autorisations nécessaires pour lire un fichier

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Pour le fichier à lire, l’appelant doit disposer de l’autorisation **Lecture**.
* Pour tous les dossiers dans l’arborescence contenant le fichier, l’appelant doit disposer de l’autorisation **Exécution**.

### <a name="permissions-needed-to-append-to-a-file"></a>Autorisations nécessaires pour ajouter du contenu à un fichier

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Pour le fichier auquel ajouter du contenu, l’appelant doit disposer de l’autorisation **Écriture**.
* Pour tous les dossiers qui contiennent le fichier, l’appelant doit disposer de l’autorisation **Exécution**.

### <a name="permissions-needed-to-delete-a-file"></a>Autorisations nécessaires pour supprimer un fichier

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Pour le dossier parent, l’appelant doit disposer des autorisations **Écriture + Exécution**.
* Pour tous les autres dossiers du chemin du fichier, l’appelant doit disposer de l’autorisation **Exécution**.



> [!NOTE]
> Les autorisations d’écriture ne sont pas nécessaires pour supprimer le fichier, tant que les deux conditions précédentes sont remplies.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Autorisations nécessaires pour énumérer un dossier

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Pour le dossier à énumérer, l’appelant doit disposer des autorisations **Lecture + Exécution**.
* Pour tous les dossiers parents, l’appelant doit disposer de l’autorisation **Exécution**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Affichage des autorisations dans le portail Azure

Dans le panneau **Explorateur de données** du compte Data Lake Store, cliquez sur **Accès** pour afficher les ACL du fichier ou du dossier qui apparaît dans l’Explorateur de données. Cliquez sur **Accès** pour afficher les ACL du dossier **catalog** dans le compte **mydatastore**.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

La partie supérieure de ce panneau affiche les autorisations des propriétaires. (Dans la capture d’écran, l’utilisateur propriétaire est Bob.) Les ACL d’accès assignées sont ensuite affichées. 

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Cliquez sur **Affichage avancé** pour afficher la vue détaillée indiquant les ACL par défaut, le masque et une description des superutilisateurs.  Ce panneau permet également de définir de manière récursive les ACL d’accès et par défaut pour les fichiers et les dossiers enfants en fonction des autorisations du dossier actif.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Le super utilisateur

Un super utilisateur a plus de droits que tous les utilisateurs de Data Lake Store. Un super utilisateur :

* possède les autorisations RWX sur **tous** les fichiers et dossiers ;
* peut modifier les autorisations sur n’importe quel fichier ou dossier ;
* peut modifier l’utilisateur ou le groupe propriétaire d’un fichier ou d’un dossier.

Dans Azure, un compte Data Lake Store possède plusieurs rôles Azure :

* Propriétaires
* Contributeurs
* Lecteurs

Toutes les personnes ayant le rôle **Propriétaire** pour un compte Data Lake Store sont automatiquement considérées comme des super utilisateurs pour ce compte. Pour en savoir plus, consultez [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md).
Si vous souhaitez créer un rôle personnalisé de contrôle d’accès en fonction du rôle (RBAC) qui possède des autorisations de super utilisateur, il doit disposer des autorisations suivantes :
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>L’utilisateur propriétaire

L’utilisateur qui a créé l’élément est automatiquement l’utilisateur propriétaire de l’élément. Les utilisateurs propriétaires peuvent :

* modifier les autorisations de leurs fichiers ;
* modifier le groupe propriétaire d’un fichier détenu, tant que l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> L’utilisateur propriétaire *ne peut pas* modifier l’utilisateur propriétaire d’un fichier ou d’un dossier. Seuls les super utilisateurs peuvent modifier l’utilisateur propriétaire d’un fichier ou d’un dossier.
>
>

## <a name="the-owning-group"></a>Le groupe propriétaire

Dans les ACL POSIX, chaque utilisateur est associé à un « groupe principal ». Par exemple, l’utilisateur « Alice » peut appartenir au groupe « Finance ». Alice peut appartenir à plusieurs groupes, mais un groupe est toujours désigné comme son groupe principal. Dans POSIX, lorsqu’Alice crée un fichier, son groupe principal est défini comme groupe propriétaire de ce fichier (en l’occurrence, « finance »).

Lorsqu’un nouvel élément de système de fichiers est créé, Data Lake Store affecte une valeur au groupe propriétaire.

* **Cas 1** : le dossier racine « / ». Ce dossier est créé lors de la création d’un compte Data Lake Store. Dans ce cas, le groupe propriétaire est celui de l’utilisateur qui a créé le compte.
* **Cas 2** (tous les autres cas) : lorsqu’un élément est créé, le groupe propriétaire est copié à partir du dossier parent.

Sinon, le groupe propriétaire se comporte comme pour les autorisations assignées à d’autres utilisateurs/groupes.

Le groupe propriétaire peut être modifié par :
* un super utilisateur ;
* l’utilisateur propriétaire, si l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> Le groupe propriétaire *ne peut pas* modifier les ACL d’un fichier ou d’un dossier.

## <a name="access-check-algorithm"></a>Algorithme de vérification des accès

L’illustration suivante représente l’algorithme de vérification des accès pour les comptes Data Lake Store.

![Algorithme d’ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Le masque et les « autorisations effectives »

Le **masque** est une valeur RWX utilisée pour limiter l’accès aux **utilisateurs nommés**, au **groupe propriétaire** et aux **groupes nommés** lors de l’exécution de l’algorithme de vérification des accès. Voici les concepts clés du masque.

* Le masque crée des « autorisations effectives ». En d’autres termes, il modifie les autorisations lors de la vérification des accès.
* Le propriétaire du fichier et un super utilisateur peuvent modifier le masque directement.
* Le masque peut supprimer des autorisations pour créer l’autorisation effective. Mais il *ne peut pas* ajouter d’autorisations à l’autorisation effective.

Prenons quelques exemples. Dans l’exemple ci-dessous, le masque est défini sur **RWX**, ce qui signifie qu’il ne supprime aucune autorisation. Les autorisations effectives de l’utilisateur nommé, du groupe propriétaire et du groupe nommé ne sont pas modifiées lors de la vérification des accès.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Dans l’exemple suivant, le masque est réglé sur **R-X**. Cela signifie qu’il **désactive les autorisations d’écriture** de l’**utilisateur nommé**, du **groupe propriétaire** et du **groupe nommé** lors de la vérification des accès.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

À titre de référence, voici où se trouve le masque d’un fichier ou d’un dossier dans le portail Azure.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Pour un nouveau compte Data Lake Store, le masque de l’ACL d’accès et du dossier racine (« / ») est défini par défaut sur RWX.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Autorisations sur les nouveaux fichiers et dossiers

Lorsqu’un nouveau fichier ou dossier est créé dans un dossier existant, l’ACL par défaut sur le dossier parent détermine :

- L’ACL par défaut et l’ACL d’accès d’un dossier enfant
- L’ACL d’accès d’un fichier enfant (les fichiers n’ont pas d’ACL par défaut)

### <a name="the-access-acl-of-a-child-file-or-folder"></a>ACL d’accès d’un fichier ou dossier enfant

Lorsqu’un fichier ou un dossier enfant est créé, l’ACL par défaut du parent est copiée comme ACL d’accès du fichier ou du dossier enfant. En outre, si un **autre** utilisateur possède les autorisations RWX dans l’ACL par défaut du parent, il est totalement supprimé de l’ACL d’accès de l’élément enfant.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Dans la plupart des cas, les informations ci-dessus constituent tout ce que vous devez savoir sur la manière dont l’ACL d’accès d’un élément enfant est définie. Toutefois, si vous connaissez les systèmes POSIX et que vous souhaitez comprendre dans le détail comment cette transformation est réalisée, consultez la section [Rôle de l’umask lors de la création de l’ACL d’accès pour les nouveaux fichiers et dossiers](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) , plus loin dans cet article.


### <a name="a-child-folders-default-acl"></a>ACL par défaut pour un dossier enfant

Lors de la création d’un dossier enfant sous un dossier parent, l’ACL par défaut du dossier parent est copiée telle quelle, comme ACL par défaut du dossier enfant.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Rubriques avancées permettant de comprendre les ACL dans Data Lake Store

Voici quelques rubriques avancées pour vous aider à comprendre comment les ACL sont déterminées pour les fichiers ou dossiers Date Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Rôle de l’umask lors de la création de l’ACL d’accès pour les nouveaux fichiers et dossiers

Dans un système compatible POSIX, le concept général est que l’umask est une valeur 9 bits sur le dossier parent, qui permet de transformer l’autorisation de l’**utilisateur propriétaire**, du **groupe propriétaire** et des **autres rôles** sur l’ACL d’accès d’un nouveau fichier ou dossier enfant. Les bits d’un umask identifient les bits à désactiver dans l’ACL d’accès de l’élément enfant. Ce qui permet d’empêcher, de manière sélective, la propagation des autorisations de l’**utilisateur propriétaire**, du **groupe propriétaire** et des **autres rôles**.

Dans un système HDFS, l’umask est généralement une option de configuration à l’échelle du site, contrôlée par les administrateurs. Data Lake Store utilise un **umask à l’échelle du compte** qui ne peut pas être modifié. Le tableau suivant présente l’umask de Data Lake Store.

| Groupe d'utilisateurs  | Paramètre | Effet sur l’ACL d’accès du nouvel élément enfant |
|------------ |---------|---------------------------------------|
| utilisateur propriétaire | ---     | Aucun effet                             |
| groupe propriétaire| ---     | Aucun effet                             |
| autre       | RWX     | Supprimer les autorisations Lecture + Écriture + Exécution         |

L’illustration suivante représente ce umask en action. L’effet immédiat est la suppression des autorisations **Lecture + Écriture + Exécution** pour les **autres** utilisateurs. Dans la mesure où l’umask n’a pas spécifié de bits pour l’**utilisateur propriétaire** et le **groupe propriétaire**, ces autorisations ne sont pas modifiées.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>Le sticky bit

Le sticky bit est une fonctionnalité avancée d’un système de fichiers POSIX. Dans le contexte de Data Lake Store, il est peu probable que le sticky bit soit nécessaire.

Le tableau suivant montre comment le sticky bit fonctionne dans Data Lake Store.

| Groupe d'utilisateurs         | Fichier    | Dossier |
|--------------------|---------|-------------------------|
| Sticky bit **DÉSACTIVÉ** | Aucun effet   | Aucun effet           |
| Sticky bit **ACTIVÉ**  | Aucun effet   | Empêche une personne autre que les **super utilisateurs** et **l’utilisateur propriétaire** d’un élément enfant de supprimer ou de renommer cet élément enfant.               |

Le sticky bit n’est pas affiché dans le portail Azure.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Questions les plus fréquentes sur les ACL dans Data Lake Store

Voici quelques questions fréquentes concernant les ACL dans Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>Dois-je activer la prise en charge des ACL ?

Non. Le contrôle d’accès via les ACL est toujours activé pour les comptes Data Lake Store.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Quelles sont les autorisations nécessaires pour supprimer de manière récursive un dossier et son contenu ?

* L’utilisateur doit disposer des autorisations **Écriture + Exécution** sur le dossier parent.
* L’utilisateur doit disposer des autorisations **Lecture + Écriture + Exécution** sur le dossier à supprimer et sur tous ses sous-dossiers.

> [!NOTE]
> L’autorisation Écriture n’est pas nécessaire pour supprimer des fichiers situés dans des dossiers. En outre, le dossier racine « / » ne peut **jamais** être supprimé.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Qui est le propriétaire d’un fichier ou d’un dossier ?

Le créateur d’un fichier ou d’un dossier en devient le propriétaire.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Quel groupe est propriétaire d’un fichier ou d’un dossier lors de sa création ?

Le groupe propriétaire est copié à partir du groupe propriétaire du dossier dans lequel le fichier ou le dossier est créé.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Je suis l’utilisateur propriétaire d’un fichier, mais je n’ai pas les autorisations RWX dont j’ai besoin. Que faire ?

L’utilisateur propriétaire peut modifier les autorisations du fichier pour s’accorder les autorisations RWX dont il a besoin.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Lorsque j’examine les ACL dans le portail Azure, je vois des noms d’utilisateur alors que, dans les API, je vois des GUID. Pourquoi ?

Dans les ACL, les entrées sont stockées sous forme de GUID qui correspondent aux utilisateurs dans Azure AD. Les API renvoient les GUID en l’état. Le portail Azure tente de simplifier l’utilisation des ACL en convertissant les GUID en noms conviviaux lorsque cela est possible.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Pourquoi est-ce que je vois parfois des GUID dans les ACL lorsque j’utilise le portail ?

Un GUID apparaît lorsque l’utilisateur n’existe plus dans Azure AD. Cela se produit généralement lorsque l’utilisateur a quitté l’entreprise ou que son compte a été supprimé dans Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Data Lake Store prend-il en charge l’héritage des ACL ?

Non, mais les ACL par défaut peuvent être utilisées pour définir les ACL des fichiers et dossiers enfants nouvellement créés sous le dossier parent.  

### <a name="what-is-the-difference-between-mask-and-umask"></a>Quelle est la différence entre le masque et umask ?

| masque | umask|
|------|------|
| La propriété **mask** est disponible sur tous les fichiers et dossiers. | **umask** est une propriété du compte Data Lake Store. Il existe donc qu’un seul umask dans Data Lake Store.    |
| La propriété mask sur un fichier ou dossier peut être modifiée par l’utilisateur propriétaire ou le groupe propriétaire d’un fichier, ou par un super utilisateur. | Aucun utilisateur ou super utilisateur ne peut modifier la propriété umask. Il s’agit d’une valeur constante non modifiable.|
| La propriété mask est utilisée lors de l’exécution de l’algorithme de vérification des accès pour déterminer si un utilisateur a le droit d’effectuer une opération sur un fichier ou dossier. Le rôle du masque est de créer des « autorisations effectives » au moment de la vérification des accès. | L’umask n’est pas du tout utilisé lors de la vérification des accès. L’umask est utilisé pour déterminer l’ACL d’accès des nouveaux éléments enfants d’un dossier. |
| Le masque est une valeur RWX de 3 bits qui s’applique à l’utilisateur nommé, au groupe propriétaire et au groupe nommé lors de la vérification des accès.| L’umask est une valeur de 9 bits qui s’applique à l’utilisateur propriétaire, au groupe propriétaire et aux **autres rôles** d’un nouvel enfant.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Comment en savoir plus sur le modèle de contrôle d’accès POSIX ?

* [Listes de contrôle d’accès (ACL) POSIX sur Linux](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [HDFS Permission Guide (Guide des autorisations HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [Forum aux questions POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [ACL POSIX sous Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL using access control lists on Linux (ACL utilisant des listes de contrôle d’accès sous Linux)](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Voir aussi

* [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)
