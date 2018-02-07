---
title: "Créer visuellement des fabriques de données Azure | Microsoft Docs"
description: "Découvrez comment créer visuellement des fabriques de données Azure"
services: data-factory
documentationcenter: 
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
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Création visuelle de fabriques de données
Avec l’expérience Azure Data Factory UX, les utilisateurs peuvent visuellement créer et déployer des ressources dans leur fabrique de données sans écrire une seule ligne de code. Grâce à cette interface sans code, vous pouvez glisser-déposer des activités sur le canevas d’un pipeline, effectuer des séries de tests, déboguer de manière itérative, mais aussi déployer et surveiller les exécutions de votre pipeline. Vous pouvez choisir d’utiliser l’outil ADF UX de deux manières :

1. Travailler directement avec le service Data Factory
2. Configurer VSTS Git Integration pour la collaboration, le contrôle du code source ou la gestion des versions

## <a name="authoring-with-data-factory"></a>Création avec Data Factory
La première option consiste à créer directement avec le mode Data Factory. Cette approche est différente de la création à l’aide de VSTS Code Repository dans la mesure où il n’existe aucun référentiel qui stocke les entités JSON de vos modifications, ni optimisé pour la collaboration ou la gestion des versions.

![Configurer une fabrique de données](media/author-visually/configure-data-factory.png)

En mode Data Factory, il n’existe que le mode 'Publier'. Les modifications que vous apportez sont publiées directement dans le service Data Factory.

![Publier une fabrique de données](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Création avec VSTS Git Integration
La création avec VSTS Git Integration permet le contrôle du code source et la collaboration pendant la création de vos pipelines de fabrique de données. Les utilisateurs ont la possibilité d’associer une fabrique de données à un référentiel de compte VSTS Git pour le contrôle du code source, la collaboration, la gestion des versions, etc. Un seul compte VSTS GIT peut avoir plusieurs référentiels. Toutefois, un référentiel VSTS Git ne peut-être associé qu’à une seule fabrique de données. Si vous n’avez déjà un compte VSTS et un référentiel, créez-les [ici](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Configurer un référentiel Git VSTS avec Azure Data Factory
Les utilisateurs peuvent configurer un référentiel VSTS GIT avec une fabrique de données via deux méthodes.

#### <a name="method-1-lets-get-started-page"></a>Méthode 1 : page 'Let's get started' (Prise en main)

Accédez à la page 'Let's get started' (Prise en main) puis cliquez sur 'Configurer le référentiel de code'

![Configurer le référentiel de code](media/author-visually/configure-repo.png)

À partir de là, un panneau latéral s’affiche pour la configuration des paramètres du référentiel.

![Configurer les paramètres du référentiel](media/author-visually/repo-settings.png)
* **Type de référentiel** : Visual Studio Team Services Git (actuellement, Github n'est pas pris en charge.)
* **Comptes Visual Studio Team Services**: le nom du compte est disponible à l’adresse https://{nom du compte}.visualstudio.com. Connectez-vous [ici](https://www.visualstudio.com/team-services/git/) à votre compte VSTS puis accédez à votre profil Visual Studio pour visualiser vos référentiels et projets
* **Nom du projet :** le nom du projet est disponible à l’adresse https://{nom du compte}.visualstudio.com/{nom du projet}
* **Nom du référentiel :** nom du référentiel. Les projets VSTS contiennent des référentiels Git pour gérer votre code source, à mesure que votre projet se développe. Créez un nouveau référentiel ou utilisez un référentiel existant dans le projet.
* **Import existing Data Factory resources to repository** (Importer des ressources Data Factory existantes dans le référentiel) : en cochant cette case, vous pouvez importer les ressources actuelles de votre fabrique de données, créées sur le canevas UX, vers le référentiel VSTS GIT associé, au format JSON. Cette action exporte chaque ressource individuellement (autrement dit, les services et jeux de données liés sont exportés dans des fichiers JSON distincts).    Si vous désactivez cette case à cocher, les ressources existantes ne sont pas importées dans le référentiel Git.

#### <a name="method-2-from-authoring-canvas"></a>Méthode 2 : à partir du canevas de création

Dans le « canevas de création », cliquez sur le menu déroulant « Data Factory » sous le nom de votre fabrique de données. Puis, cliquez sur « Configurer le référentiel de code ». Comme avec la **méthode 1**, un panneau latéral s’affiche pour la configuration des paramètres du référentiel. Consultez les sections précédentes pour plus d’informations sur les paramètres.

![Configurer le référentiel de code 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Contrôle de version
Le contrôle de version, également appelée contrôle du code source, permet aux développeurs de collaborer sur le code et de suivre les modifications apportées à la base de code. Le contrôle du code source est un outil essentiel pour les projets impliquant plusieurs développeurs.

Une fois associé à une fabrique de données, chaque référentiel VSTS Git comporte une branche principale. De là, chaque utilisateur qui a accès au référentiel VSTS Git dispose de deux options pour apporter des modifications : Sync et Publish.

![Sync Publish](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Synchronisation

Lorsque vous cliquez sur « sync », vous pouvez extraire les modifications de la branche principale vers votre branche locale, ou transmettre les modifications de votre branche locale à la branche principale.

![Synchronisation des modifications](media/author-visually/sync-change.png)

#### <a name="publish"></a>Publish
 Publiez les modifications de la branche principale au service Data Factory.

> [!NOTE]
> La **branche principale n’est pas représentative de ce qui est déployé dans le service Data Factory.** La branche principale *doit* être publiée manuellement sur le service Data Factory.




## <a name="expression-language"></a>Langage d’expression

Les utilisateurs peuvent spécifier des expressions dans la définition des valeurs de propriété en utilisant le langage d’expression pris en charge par Azure Data Factory. Consultez [Expressions et fonctions dans Azure Data Factory](control-flow-expression-language-functions.md) pour plus d’informations sur les expressions prises en charge.

Spécifiez des expressions dans les valeurs de propriété de l’expérience utilisateur comme suit.

![Langage d’expression](media/author-visually/expression-language.png)

## <a name="parameters"></a>parameters
Les utilisateurs peuvent spécifier des paramètres pour les pipelines et les jeux de données, dans l’onglet « Paramètres ». En outre, utilisez facilement les paramètres des propriétés en appuyant sur « Ajouter du contenu dynamique ».

![Contenu dynamique](media/author-visually/dynamic-content.png)

De là, vous pouvez utiliser un paramètre existant ou spécifier un nouveau paramètre dans votre valeur de propriété.

![parameters](media/author-visually/parameters.png)

## <a name="feedback"></a>Commentaires
Cliquez sur l’icône « Commentaires » pour nous (Microsoft) envoyer vos commentaires sur les différentes fonctionnalités ou les problèmes auxquels vous êtes confrontés.

![Commentaires](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>étapes suivantes

Pour en savoir plus sur la surveillance et la gestion des pipelines, consultez l’article [Surveiller et gérer les pipelines par programmation](monitor-programmatically.md)
