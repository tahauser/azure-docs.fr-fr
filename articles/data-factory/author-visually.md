---
title: Création visuelle dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser la création visuelle dans Azure Data Factory
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: 977fd59b746d13e9bf331edc32c63dd5a21c69f7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
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

Lorsque vous utilisez les **Canevas de création** UX pour une création directe avec le service Data Factory, seul le mode **Tout publier** est disponible. Les modifications que vous apportez sont publiées directement dans le service Data Factory.

![Mode Publier](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Créer avec VSTS Git Integration
La création avec VSTS Git Integration prend en charge le contrôle du code source et la collaboration pendant l’utilisation de vos pipelines de fabrique de données. Vous pouvez associer une fabrique de données à un référentiel de compte VSTS Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Un compte VSTS Git peut avoir plusieurs référentiels, mais un référentiel VSTS Git ne peut être associé qu’à une seule fabrique de données. Si vous n’avez pas un compte ou un référentiel VSTS, suivez [ces instructions](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) pour créer vos ressources.

> [!NOTE]
> Vous pouvez stocker les fichiers de scripts et de données dans un référentiel GIT VSTS. Toutefois, vous devez charger les fichiers manuellement dans le stockage Azure. Un pipeline Data Factory ne charge pas automatiquement les fichiers de scripts ou de données stockés dans un référentiel GIT VSTS vers le stockage Azure.

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
| **Azure Active Directory** | Le nom de votre abonné Azure AD. | <your tenant name> |
| **Compte Visual Studio Team Services** | Le nouveau de votre compte VSTS. Vous pouvez rechercher le nom de votre compte VSTS à l’adresse `https://{account name}.visualstudio.com`. Vous pouvez vous [connecter à votre compte VSTS](https://www.visualstudio.com/team-services/git/) pour accéder à votre profil Visual Studio et visualiser vos référentiels et projets. | \<le nom de votre compte> |
| **Nom du projet** | Le nom de votre projet VSTS. Vous pouvez rechercher le nom de votre projet VSTS à l’adresse `https://{account name}.visualstudio.com/{project name}`. | \<le nom de votre projet VSTS> |
| **RepositoryName** | Le nom de votre référentiel de code VSTS. Les projets VSTS contiennent des référentiels Git pour gérer votre code source, à mesure que votre projet se développe. Vous pouvez créer un nouveau référentiel ou utiliser un référentiel existant déjà présent dans le projet. | \<le nom de votre référentiel de code VSTS> |
| **Branche de collaboration** | Votre branche de collaboration VSTS qui sera utilisée pour la publication. Par défaut, il s’agit de `master`. Modifiez cette valeur au cas où vous souhaitez publier des ressources à partir d’une autre branche. | \<le nom de votre branche de collaboration> |
| **Dossier racine** | Votre dossier racine de votre branche de collaboration VSTS. | \<le nom de votre dossier racine > |
| **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) | Permet de spécifier s’il faut importer des ressources de fabrique de données existantes à partir de l’expérience utilisateur **Canevas de création** dans un référentiel Git VSTS. Activez la case pour importer vos ressources de fabrique de données dans le référentiel Git associé au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts). Lorsque cette case n’est pas activée, les ressources existantes ne sont pas importées. | Activée (par défaut) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Méthode de configuration 2 : Canevas de création de l’expérience utilisateur
Dans l’expérience utilisateur Azure Data Factory **Canevas de création**, recherchez votre fabrique de données. Sélectionnez le menu déroulant **Fabrique de données** et cliquez sur **Configure code repository** (Configurer le référentiel de code)à.

Un volet de configuration apparaît. Pour plus d’informations sur les paramètres de configuration, consultez les descriptions dans <a href="#method1">Méthode de configuration 1</a>.

![Configurer les paramètres du référentiel de code pour la création de l’expérience utilisateur](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Utiliser le contrôle de version
Les systèmes de contrôle de version (également appelé _contrôle du code source_) permettent aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

Chaque référentiel VSTS Git associé à une fabrique de données comporte une branche de collaboration. (`master` est la branche de collaboration par défaut). Les utilisateurs peuvent également créer des branches de fonctionnalités en cliquant sur **+ Nouvelle branche** et en procédant à un développement dans les branches de fonctionnalités.

![Modifier le code via la synchronisation ou la publication](media/author-visually/sync-publish.png)

Lorsque vous êtes prêt pour le développement des fonctionnalités dans votre branche de fonctionnalités, vous pouvez cliquer sur **Créer une demande de tirage (pull request)**. Ceci vous dirigera vers GIT VSTS où vous pouvez augmenter les demandes de tirage, procéder à des révisions du code et fusionner les modifications dans votre branche de collaboration. (`master` est la valeur par défaut). Vous êtes uniquement autorisé à publier sur le service Data Factory à partir de votre branche de collaboration. 

![Créer une nouvelle demande de tirage (pull request)](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Publier les modifications de code
Après avoir fusionné des modifications dans la branche de collaboration (`master` est la valeur par défaut), sélectionnez **Publier** pour publier manuellement les modifications de votre code dans la branche principale pour le service Data Factory.

![Publier les modifications apportées au service Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> La branche principale n’est pas représentative de ce qui est déployé dans le service Data Factory. La branche principale *doit* être publiée manuellement sur le service Data Factory.

## <a name="use-the-expression-language"></a>Utiliser le langage d’expression
Vous pouvez spécifier des expressions pour les valeurs de propriété en utilisant le langage d’expression pris en charge par Azure Data Factory. 

Spécifiez des expressions pour les valeurs de propriété en sélectionnant **Ajouter du contenu dynamique**:

![Utiliser le langage d’expression](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Utiliser les fonctions et paramètres

Vous pouvez utiliser des fonctions ou spécifier des paramètres pour des pipelines et des jeux de données dans le **générateur d’expressions** Data Factory :

Pour plus d’informations sur les expressions prises en charge, consultez [Expressions et fonctions dans Azure Data Factory](control-flow-expression-language-functions.md).

![Ajouter du contenu dynamique](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Fournir des commentaires
Sélectionnez **Feedback** (Commentaire) pour donner votre avis sur les fonctionnalités ou informer Microsoft de problèmes avec l’outil :

![Commentaires](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md).
