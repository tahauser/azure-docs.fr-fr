---
title: Création visuelle dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser la création visuelle dans Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 209afba99ac2b43c252d93c32930908ec1f957f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Création visuelle dans Azure Data Factory
L’expérience utilisateur Azure Data Factory vous permet de créer et de déployer visuellement des ressources dans votre fabrique de données sans avoir à écrire du code. Vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline. Vous pouvez utiliser l’expérience utilisateur pour la création visuelle de deux manières :

- Créer directement avec le service Data Factory.
- Créer avec Visual Studio Team Services (VSTS) Git Integration pour la collaboration, le contrôle du code source ou la gestion des versions.

## <a name="author-directly-with-the-data-factory-service"></a>Créer directement avec le service Data Factory
Deux éléments distinguent la création visuelle avec le service de Data Factory diffère de la création visuelle avec VSTS :

- Le service Data Factory n’inclut pas un référentiel pour stocker les entités JSON de vos modifications.
- Le service Data Factory n’est pas optimisé pour la collaboration ou le contrôle de version.

![Configurer le service Data Factory ](media/author-visually/configure-data-factory.png)

Lorsque vous utilisez l’expérience utilisateur **Canevas de création** pour créer directement avec le service Data Factory, seul le mode **Publier** est disponible. Les modifications que vous apportez sont publiées directement dans le service Data Factory.

![Mode Publier](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Créer avec VSTS Git Integration
La création avec VSTS Git Integration prend en charge le contrôle du code source et la collaboration pendant l’utilisation de vos pipelines de fabrique de données. Vous pouvez associer une fabrique de données à un référentiel de compte VSTS Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Un compte VSTS Git peut avoir plusieurs référentiels, mais un référentiel VSTS Git ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas un compte ou un référentiel VSTS, suivez [ces instructions](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) pour créer vos ressources.

> [!NOTE]
> Un pipeline de fabrique de données ne peut pas accéder aux fichiers stockés dans un référentiel Git VSTS. Par conséquent, vous ne pouvez pas stocker les fichiers qui sont utilisés par les activités de pipeline de fabrique de données - par exemple, les fichiers de données et les fichiers de script - dans un référentiel Git VSTS.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>Configurer un référentiel Git VSTS avec Azure Data Factory
Vous pouvez configurer un référentiel VSTS GIT avec une fabrique de données via deux méthodes.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Méthode de configuration 1 : page Let’s get started (Prise en main)
Dans Azure Data Factory, accédez à la page **Let’s get started** (Prise en main). Sélectionnez **Configure Code Repository** (Configurer le référentiel de code) :

![Configurer un référentiel de code VSTS](media/author-visually/configure-repo.png)

Le volet de configuration **Paramètres du référentiel** apparaît :

![Configurer les paramètres du référentiel de code](media/author-visually/repo-settings.png)

Le volet affiche les paramètres du référentiel de code VSTS suivants :

| Paramètre | Description | Valeur |
|:--- |:--- |:--- |
| **Type de référentiel** | Le type de référentiel de code VSTS.<br/>**Remarque** : GitHub n’est actuellement pas pris en charge. | Git Visual Studio Team Services |
| **Compte Visual Studio Team Services** | Le nouveau de votre compte VSTS. Vous pouvez rechercher le nom de votre compte VSTS à l’adresse `https://{account name}.visualstudio.com`. Vous pouvez vous [connecter à votre compte VSTS](https://www.visualstudio.com/team-services/git/) pour accéder à votre profil Visual Studio et visualiser vos référentiels et projets. | \<le nom de votre compte> |
| **Nom du projet** | Le nom de votre projet VSTS. Vous pouvez rechercher le nom de votre projet VSTS à l’adresse `https://{account name}.visualstudio.com/{project name}`. | \<le nom de votre projet VSTS> |
| **RepositoryName** | Le nom de votre référentiel de code VSTS. Les projets VSTS contiennent des référentiels Git pour gérer votre code source, à mesure que votre projet se développe. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le projet. | \<le nom de votre référentiel de code VSTS> |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Permet de spécifier s’il faut importer des ressources de fabrique de données existantes à partir de l’expérience utilisateur **Canevas de création** dans un référentiel Git VSTS. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Méthode de configuration 2 : Canevas de création de l’expérience utilisateur
Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans <a href="#method1">Méthode de configuration 1</a>.

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Utiliser le contrôle de version
Les systèmes de contrôle de version (également appelé _contrôle du code source_) permettent aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

Chaque référentiel VSTS Git associé à une fabrique de données comporte une branche principale. Lorsque vous avez accès à un référentiel Git de VSTS, vous pouvez modifier le code en choisissant **Synchroniser** ou **Publier** :

![Modifier le code via la synchronisation ou la publication](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Synchroniser les modifications de code
Après avoir cliqué sur **Synchroniser**, vous pouvez extraire les modifications de la branche principale vers votre branche locale, ou transmettre les modifications de votre branche locale à la branche principale.

![Synchroniser les modifications de code](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Publier les modifications de code
Sélectionnez **Publier** pour publier manuellement les modifications de code de la branche principale dans le service Data Factory.

> [!IMPORTANT]
> La branche principale n’est pas représentative de ce qui est déployé dans le service Data Factory. La branche principale *doit* être publiée manuellement sur le service Data Factory.

## <a name="use-the-expression-language"></a>Utiliser le langage d’expression
Vous pouvez spécifier des expressions pour les valeurs de propriété en utilisant le langage d’expression pris en charge par Azure Data Factory. Pour plus d’informations sur les expressions prises en charge, consultez [Expressions et fonctions dans Azure Data Factory](control-flow-expression-language-functions.md).

Spécifiez les expressions des valeurs de propriété en utilisant l’expérience utilisateur **Canevas de création** :

![Utiliser le langage d’expression](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Spécifier les paramètres
Vous pouvez spécifier les paramètres des pipelines et des jeux de données dans l’onglet **Paramètres** d’Azure Data Factory. Vous pouvez facilement utiliser les paramètres dans les propriétés en sélectionnant **Ajouter du contenu dynamique** :

![Ajouter du contenu dynamique](media/author-visually/dynamic-content.png)

Vous pouvez utiliser les paramètres existants ou spécifier de nouveaux paramètres pour les valeurs de propriété :

![Spécifier les paramètres des valeurs de propriété](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Fournir des commentaires
Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

![Commentaires](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
