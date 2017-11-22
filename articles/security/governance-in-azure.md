---
title: Gouvernance dans Azure | Microsoft Docs
description: "Découvrez les services informatiques cloud qui incluent une large sélection d’instances de calcul et de services pouvant être mis à l’échelle automatiquement pour répondre aux besoins de votre application ou de votre entreprise."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 10a92f301584d4e676da9a1eb229667925a6c72c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="governance-in-azure"></a>Gouvernance dans Azure

Nous savons que la sécurité est la priorité dans le cloud et combien il est important que vous trouviez des informations précises et à jour sur la sécurité Azure. L’une des meilleures raisons d’utiliser Azure pour vos applications et services est de tirer parti de sa large gamme de fonctionnalités et outils de sécurité. Ces outils et fonctionnalités permettent de créer des solutions sécurisées sur la plateforme Azure sécurisée.

Le présent article « Gouvernance dans Azure » propose une vue complète des fonctionnalités de gouvernance de Microsoft Azure pour vous aider à mieux comprendre l’ensemble des contrôles de gouvernance implémentés dans Microsoft Azure, en prenant en compte les perspectives opérationnelles de Microsoft et celles du client.

## <a name="azure-platform"></a>Plateforme Azure

Azure est une plateforme de services de cloud public qui prend en charge un large éventail de systèmes d’exploitation, de langages de programmation, d’infrastructures, d’outils, de bases de données et d’appareils. Elle permet d’exécuter des conteneurs Linux avec l’intégration de Docker, de créer des applications avec JavaScript, Python, .NET, PHP, Java et Node.js, de créer des serveurs principaux pour des appareils iOS, Android et Windows. Les services de cloud public Azure prennent en charge les technologies auxquelles des millions de développeurs et de professionnels de l’informatique font déjà confiance.

Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de services de cloud public, vous comptez sur les capacités de cette organisation à protéger vos applications et données avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. En outre, Azure vous offre de nombreuses options de sécurité, ainsi que la possibilité de contrôler ces options pour vous permettre de personnaliser la sécurité et de répondre ainsi aux exigences uniques des déploiements de votre organisation.

Ce document vous permettra de comprendre comment les fonctionnalités de gouvernance d’Azure peuvent vous aider à répondre à ces besoins.

## <a name="abstract"></a>Résumé

La gouvernance cloud de Microsoft Azure fournit une approche d’audit et de conseils intégrée afin de passer en revue l’utilisation de la plateforme Azure par les organisations et de les conseiller dans ce domaine. La gouvernance cloud de Microsoft Azure fait référence aux processus de prise de décisions, aux critères et aux stratégies impliqués dans la planification, l’architecture, l’acquisition, le déploiement, le fonctionnement et la gestion du cloud computing.

Afin de créer un plan pour la gouvernance cloud de Microsoft Azure, vous devez examiner attentivement les utilisateurs, les processus et les technologies actuellement en place, puis créer des frameworks qui facilitent la prise en charge continue par le service informatique des besoins métier, tout en fournissant aux utilisateurs finaux la possibilité d’utiliser les puissantes fonctionnalités de Microsoft Azure.

Ce document explique comment vous pouvez obtenir un niveau élevé de gouvernance de vos ressources informatiques dans Microsoft Azure. Ce document peut vous aider à comprendre les fonctionnalités de sécurité et de gouvernance intégrées à Microsoft Azure.

Vous trouverez ci-dessous les thèmes principaux relatifs à la gouvernance traités dans ce document :

- Implémentation de stratégies, de processus et de procédures en fonction des objectifs de l’organisation.

- Sécurité et conformité continue aux normes de l’organisation

- Alertes et monitoring

## <a name="implementation-of-policies-processes-and-procedures"></a>Implémentation de stratégies, de processus et de procédures 

Les responsables ont défini des rôles et des responsabilités pour surveiller la mise en œuvre de la stratégie de sécurité des informations et de la continuité opérationnelle dans Azure. Les responsables Microsoft Azure sont chargés de surveiller la sécurité et les pratiques de continuité au sein de leurs équipes respectives (y compris celles composées de tiers) et d’optimiser la conformité avec les stratégies de sécurité, les processus et les normes.

Voici les facteurs impliqués :

- Approvisionnement des comptes

- Contrôles de l’abonnement

- Contrôles de l’accès en fonction du rôle

- Gestion des ressources

- Suivi des ressources

- Contrôle des ressources critiques

- Accès par API aux informations de facturation

- Contrôles de mise en réseau

## <a name="account-provisioning"></a>Approvisionnement des comptes

Définir la hiérarchie du compte est une étape importante pour utiliser et structurer les services Azure au sein d’une entreprise. C’est aussi la base de la structure de gouvernance. Les clients disposant d’un contrat Entreprise sont en mesure de subdiviser l’environnement en services, comptes et abonnements.

![Approvisionnement des comptes](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Si vous ne disposez pas d’un contrat Entreprise, pensez à utiliser les [balises Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) au niveau de l’abonnement pour définir la hiérarchie. Un abonnement Azure est l’unité de base contenant toutes les ressources. Il définit également plusieurs limites au sein d’Azure, par exemple le nombre de cœurs, de ressources, etc. Les abonnements peuvent contenir des [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), qui à leur tour peuvent contenir des ressources. Les principes [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) s’appliquent à ces trois niveaux.

Chaque entreprise est différente et la hiérarchie utilisant des balises Azure (pour les clients ne disposant pas d’un contrat Entreprise) permet une grande flexibilité dans la manière dont Azure est organisé au sein de l’entreprise. Avant de déployer des ressources dans Microsoft Azure, vous devez modéliser la hiérarchie et en comprendre l’impact sur la facturation, l’accès aux ressources et la complexité.

## <a name="subscription-controls"></a>Contrôles de l’abonnement

L’abonnement contrôle la manière dont l’utilisation des ressources est signalée et facturée. Il est possible de configurer les abonnements pour obtenir une facturation et un paiement séparés. Comme mentionné plus haut, un seul compte Azure peut contenir plusieurs abonnements. Les abonnements peuvent être utilisés pour déterminer l’utilisation des ressources Azure par plusieurs services au sein d’une entreprise.

Par exemple, une entreprise ayant un service informatique, un service RH et un service marketing avec différents projets en cours d’exécution. Ces services peuvent être facturés séparément en fonction de leur utilisation des ressources Azure, comme les machines virtuelles. Ainsi, nous pouvons contrôler les finances de chaque service.

Les abonnements Azure établissent trois paramètres :

- un ID d’abonné unique ;

- un emplacement de facturation ;

- un ensemble de ressources disponibles.

Pour une personne, cela inclut un ID de compte Microsoft, un numéro de carte de crédit et l’ensemble des services Azure. Mais Microsoft impose des limites de consommation en fonction du type d’abonnement.

Les hiérarchies d’inscription Azure définissent la structure des services dans un contrat Entreprise. Enterprise Portal permet aux clients de diviser l’accès aux ressources Azure associées à un contrat Entreprise en fonction de hiérarchies flexibles personnalisables selon les besoins uniques d’une organisation. Le modèle de hiérarchie doit correspondre à la structure géographique et de gestion d’une organisation afin que la facturation et l’accès aux ressources associés puissent être pris en compte avec précision.

Les trois modèles de niveau supérieur sont le modèle fonctionnel, l’unité commerciale et le modèle géographique. Les services sont utilisés en tant que base administrative pour les regroupements de comptes. Au sein de chaque service, des abonnements peuvent être affectés aux comptes, ce qui crée des silos pour la facturation et plusieurs limites clés dans Azure (par exemple, nombre de machines virtuelles, comptes de stockage, etc.).

![Contrôles de l’abonnement](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Pour les organisations disposant d’un contrat Entreprise, les abonnements Azure respectent une hiérarchie à quatre niveaux :

- administrateur d’inscription d’entreprise ;

- administrateur de service ;

- propriétaire du compte ;

- Administrateur de services fédérés

Cette hiérarchie régit les éléments suivants :

- Relation de facturation

- Administration des comptes

- Contrôle d’accès en fonction du rôle (RBAC) aux artefacts

- Limites

- Limites

  - Utilisation et facturation (carte de tarifs basée sur le numéro de l’offre)

  - limites

  - Réseau virtuel

- Associé à 1 AAD (1 AAD associé à plusieurs abonnements)

- Associé à un compte d’inscription d’entreprise

## <a name="role-based-access-controls"></a>Contrôles de l’accès en fonction du rôle

Lorsqu’Azure a été initialement lancé, les contrôles d’accès à un abonnement étaient basiques : administrateur ou coadministrateur. L’accès à un abonnement dans le modèle classique impliquait l’accès à toutes les ressources dans le portail. Ce manque de contrôle précis a conduit à une prolifération d’abonnements afin de fournir un niveau de contrôle d’accès raisonnable pour une inscription Azure.

![Contrôles de l’accès en fonction du rôle](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Cette prolifération d’abonnements n’est plus nécessaire. Avec un contrôle d’accès en fonction du rôle, vous pouvez affecter des utilisateurs aux rôles standard (par exemple, les rôles « lecteur » et « rédacteur » courants). Vous pouvez également définir des rôles personnalisés.

Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) permet une gestion précise de l’accès pour Azure. L’utilisation de RBAC vous permet de n’accorder que les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Les entreprises orientées sécurité doivent s’efforcer de donner aux employés les autorisations exactes dont ils ont besoin. Un trop grand nombre d’autorisations expose un compte aux attaques. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. Le contrôle d’accès en fonction du rôle (RBAC) Azure permet de résoudre ce problème en offrant une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. Plutôt que de donner à tous des autorisations illimitées au sein de votre abonnement ou de vos ressources Azure, vous pouvez autoriser uniquement certaines actions.

Par exemple, vous pouvez utiliser le RBAC pour permettre à un employé donné de gérer les machines virtuelles dans un abonnement, tandis qu’un autre pourra gérer les bases de données SQL au sein du même abonnement.

Le contrôle d’accès en fonction du rôle Azure comporte trois rôles de base qui s’appliquent à tous les types de ressources :

- **Propriétaire** dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes.

- **Contributeur** peut créer et gérer tous les types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes.

- **Lecteur** peut consulter les ressources Azure existantes.

Les autres rôles RBAC dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de contributeur de machine virtuelle permet à l’utilisateur de créer et gérer des machines virtuelles. Il ne lui donne pas accès au réseau virtuel ou au sous-réseau auquel la machine virtuelle se connecte.

[Rôles RBAC intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) répertorie les rôles disponibles dans Azure. Elle indique les opérations et l’étendue que chaque rôle intégré accorde aux utilisateurs.

Accordez les accès en affectant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans une étendue donnée. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement. Un rôle attribué à une étendue parent accorde également l’accès aux enfants qu’elle contient.

Par exemple, un utilisateur ayant accès à un groupe de ressources peut gérer toutes les ressources qu’il contient, telles que les sites Web, les machines virtuelles et les sous-réseaux.

RBAC Azure prend uniquement en charge les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Il ne peut pas autoriser toutes les opérations au niveau des données pour les ressources Azure. Par exemple, vous pouvez autoriser une personne à gérer les comptes de stockage, mais non les objets blob ou les tables dans un compte de stockage. De même, une base de données SQL peut être gérée, mais pas les tables qu’elle contient.

Si vous souhaitez plus d’informations sur la gestion des droits d’accès avec RBAC, consultez [Prise en main de la gestion des accès dans le portail Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).

Vous pouvez également [créer un rôle personnalisé](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) dans le contrôle d’accès en fonction du rôle (RBAC) Azure si aucun des rôles intégrés ne répond à vos besoins d’accès spécifiques. Il est possible de créer des rôles personnalisés à l’aide [d’Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), de [l’interface CLI Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) et de [l’API REST](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). À l’instar des rôles intégrés, des rôles personnalisés peuvent être affectés à des utilisateurs, des groupes et des applications dans l’étendue des abonnements, des groupes de ressources et des ressources.

Dans chaque abonnement, vous pouvez accorder jusqu’à 2000 attributions de rôles.

## <a name="resource-management"></a>Gestion des ressources

À l’origine, Azure fournissait uniquement le modèle de déploiement classique. Dans ce modèle, chaque ressource existait indépendamment. Il n’existait aucun moyen de regrouper des ressources associées. Au lieu de cela, il fallait suivre manuellement les ressources qui composaient la solution ou l’application manuellement, et veiller à les gérer selon une approche coordonnée.

Pour déployer une solution, il fallait soit créer chaque ressource individuellement via le portail Azure, soit créer un script qui déployait toutes les ressources dans le bon ordre. Pour supprimer une solution, il fallait supprimer chaque ressource individuellement. Il était difficile d’appliquer et de mettre à jour des stratégies de contrôle d’accès pour des ressources liées. Enfin, il n’était pas possible d’appliquer des balises aux ressources afin de les étiqueter à l’aide de termes qui facilitent la surveillance des ressources et la gestion de la facturation.

En 2014, Azure a introduit le modèle de déploiement Resource Manager, et avec celui-ci le concept de groupe de ressources. Un groupe de ressources est un conteneur de ressources qui partagent un cycle de vie commun. Le modèle de déploiement Resource Manager présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution comme un groupe, plutôt que de gérer ces services individuellement.

- Vous pouvez déployer votre solution à plusieurs reprises tout au long de son cycle de vie et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

- Vous pouvez appliquer le contrôle d’accès à toutes les ressources de votre groupe de ressources, et ces stratégies sont appliquées automatiquement lorsque de nouvelles ressources sont ajoutées au groupe de ressources.

- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.

- Vous pouvez utiliser JavaScript Objet Notation (JSON) pour définir l’infrastructure de votre solution. Le fichier JSON est connu sous le nom de modèle Resource Manager.

- Vous pouvez définir les dépendances entre les ressources afin de les déployer dans le bon ordre.

![Gestion des ressources](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager vous permet de répartir des ressources dans des groupes explicites pour la gestion, la facturation ou l’affinité naturelle. Comme mentionné précédemment, Azure dispose de deux modèles de déploiement. Dans l’ancien [modèle classique](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), l’unité de base de gestion était l’abonnement. Il était difficile de répartir des ressources au sein d’un abonnement, ce qui entraînait la création d’un grand nombre d’abonnements. Avec le modèle Resource Manager, des groupes de ressources ont été introduits.

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. [Le groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.

Pour des recommandations sur les modèles, voir [Bonnes pratiques relatives à la création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyse les dépendances pour vérifier que les ressources sont créées dans l’ordre approprié. Si une ressource dépend d’une valeur d’une autre ressource (par exemple, une machine virtuelle ayant besoin d’un compte de stockage pour les disques), vous devez définir une dépendance.

>[!Note]
>Pour plus d’informations, consultez [Définition de dépendances dans des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Vous pouvez également utiliser le modèle pour les mises à jour de l’infrastructure. Par exemple, vous pouvez ajouter une ressource à votre solution et ajouter des règles de configuration pour les ressources qui sont déjà déployées. Si le modèle spécifie de créer une ressource, mais que cette ressource existe déjà, Azure Resource Manager effectue une mise à jour au lieu de créer une autre ressource. Azure Resource Manager met à jour l’actif existant vers l’état qu’il présenterait s’il s’agissait d’une nouvelle ressource.

Resource Manager fournit des extensions pour les cas qui nécessitent des opérations supplémentaires, comme l’installation d’un logiciel non inclus dans la configuration.

## <a name="resource-tracking"></a>Suivi des ressources

Comme les utilisateurs de votre organisation ajoutent des ressources à l’abonnement, il devient plus en plus important d’associer des ressources au service, au client et à l’environnement appropriés. Vous pouvez attacher des métadonnées aux ressources par le biais de balises. Vous utilisez des [balises](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) pour fournir des informations sur la ressource ou le propriétaire. Les balises vous permettent non seulement d’agréger et de regrouper les ressources de différentes façons, mais également d’utiliser ces données pour les besoins de facturation interne.

Utilisez des balises lorsque vous disposez d’un ensemble complexe de groupes de ressources et de ressources et que vous souhaitez les visualiser de la façon qui vous convient le mieux. Par exemple, vous pouvez baliser des ressources qui jouent un rôle similaire dans votre organisation ou qui appartiennent au même département.

Sans balises, les utilisateurs de votre organisation peuvent créer plusieurs ressources qui peuvent s’avérer difficiles à identifier et à gérer ultérieurement. Par exemple, vous pouvez souhaiter supprimer toutes les ressources d’un projet. Si ces ressources ne sont pas balisées pour le projet, vous devez les rechercher manuellement. Le balisage constitue un levier important pour réduire les coûts inutiles dans votre abonnement.

Les ressources ne doivent pas nécessairement appartenir au même groupe de ressources pour partager une balise. Vous pouvez créer votre propre taxonomie de balise pour vous assurer que tous les utilisateurs de votre organisation utiliseront des balises communes plutôt que d’appliquer par inadvertance des balises légèrement différentes (telles que « dépt » au lieu de « département »).

Les stratégies de ressources vous permettent de créer des règles standard pour votre organisation. Vous pouvez créer des stratégies qui garantissent que les ressources sont balisées avec les valeurs appropriées.

> [!Note]
> Pour plus d’informations, consultez [Initiative de la stratégie de facturation en fonction de balises](../azure-policy/scripts/billing-tags-policy-init.md).

Vous avez également la possibilité de consulter les ressources balisées via le portail Azure.

Le [rapport d’utilisation](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) de votre abonnement inclut les noms et valeurs de balises, ce qui vous permet de répartir les coûts en fonction des balises.

> [!Note]
> Pour plus d’informations sur les balises, consultez [Initiative de la stratégie de facturation en fonction de balises](../azure-policy/scripts/billing-tags-policy-init.md).

Les limites suivantes s’appliquent aux balises :

- Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires clé/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires clé/valeur de balise.

- Le nom de la balise est limité à 512 caractères.

- La valeur de la balise est limitée à 256 caractères.

- Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.

Si vous devez associer plus de 15 valeurs à une ressource, utilisez une chaîne JSON pour la valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à une seule clé de balise.

### <a name="tags-and-billing"></a>Balises et facturation

Les balises vous permettent de regrouper les données de facturation. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux balises pour regrouper l’utilisation par centre de coûts. Vous pouvez également utiliser des balises pour catégoriser les coûts par environnement d’exécution ; par exemple, l’utilisation de la facturation pour les machines virtuelles en cours d’exécution dans l’environnement de production.

Vous pouvez récupérer des informations sur les balises par le biais des [API Resource Usage et RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) ou du fichier de valeurs séparées par des virgules (CSV). Vous téléchargez le fichier d’utilisation depuis le [portail des comptes Azure](https://account.windowsazure.com/) ou depuis le [portail EA](https://ea.azure.com/).

>[!Note]
> Pour plus d’informations sur l’accès par programme aux informations de facturation, consultez [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Lorsque vous téléchargez le fichier CSV d’utilisation pour les services qui prennent en charge les balises avec la facturation, les balises s’affichent dans la colonne Balises.

## <a name="critical-resource-controls"></a>Contrôles des ressources critiques

À mesure que votre organisation ajoute des services principaux à l’abonnement, il devient de plus en plus important de s’assurer que ces services sont disponibles afin d’éviter une interruption de service. Les [verrous de ressource](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vous permettent de limiter les opérations sur les ressources de grande valeur si leur suppression ou leur modification pourraient avoir un impact significatif sur vos applications ou votre infrastructure de cloud. Vous pouvez appliquer des verrous à un abonnement, un groupe de ressources ou une ressource. En règle générale, vous appliquez des verrous à des ressources fondamentales telles que les réseaux virtuels, les passerelles et les comptes de stockage.

Les verrous de ressource prennent actuellement en charge deux valeurs : CanNotDelete et ReadOnly. CanNotDelete signifie que les utilisateurs (disposant des droits appropriés) peuvent toujours lire ou modifier une ressource, mais pas la supprimer. ReadOnly signifie que les utilisateurs autorisés ne peuvent pas supprimer ou modifier une ressource.

Les verrous Resource Manager s’appliquent uniquement aux opérations qui se produisent dans le plan de gestion, c’est-à-dire les opérations envoyées à <https://management.azure.com>. Les verrous ne limitent pas la manière dont les ressources exécutent leurs propres fonctions. Les modifications des ressources sont limitées, mais pas les opérations sur les ressources. Par exemple, un verrou ReadOnly sur une base de données SQL vous empêche de supprimer ou de modifier cette base de données, mais il ne vous empêche pas de créer, mettre à jour ou supprimer les données qu'elle contient.

L’application de **ReadOnly** peut produire des résultats inattendus, car certaines opérations qui ressemblent à des opérations de lecture nécessitent des actions supplémentaires. Par exemple, le placement d’un verrou **ReadOnly** sur un compte de stockage empêche tous les utilisateurs de répertorier les clés. L’opération de listage de clés est gérée via une demande POST, car les clés retournées sont disponibles pour les opérations d’écriture.

![Contrôles des ressources critiques](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Autre exemple : le placement d’un verrou ReadOnly sur une ressource App Service empêche l’Explorateur de serveurs Visual Studio d’afficher les fichiers de la ressource, car cette interaction requiert un accès en écriture.

Contrairement au contrôle d'accès basé sur les rôles, vous utilisez des verrous de gestion pour appliquer une restriction à tous les utilisateurs et rôles. Pour en savoir plus sur la définition des autorisations pour les utilisateurs et les rôles, consultez [Contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Lorsque vous appliquez un verrou à une étendue parente, toutes les ressources de cette étendue héritent du même verrou. Même les ressources que vous ajoutez par la suite héritent du verrou du parent. Le verrou le plus restrictif de l’héritage est prioritaire.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions Microsoft.Authorization/ _ou Microsoft.Authorization/locks/_. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.

## <a name="api-access-to-billing-information"></a>Accès par API aux informations de facturation

Utilisez les API de facturation Azure pour extraire les données d’utilisation et de ressources dans vos outils d’analyse de données préférés. Les API d’utilisation des ressources Azure et RateCard peuvent vous aider à prévoir vos coûts avec précision et à les gérer. Les API sont implémentées en tant que fournisseur de ressources et font partie intégrante de la famille d’API exposées par Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>API Azure Resource Usage (préversion)

Utiliser l’[API Azure Resource Usage](https://msdn.microsoft.com/library/azure/mt219003) pour obtenir une estimation de vos données de consommation Azure. L’API comprend :

- **Contrôle d’accès en fonction du rôle Azure** : configurez les stratégies d’accès dans le [portail Azure](https://portal.azure.com/) ou par le biais des [cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données d’utilisation de l’abonnement. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. Ajoutez l’appelant au rôle Lecteur de facture, Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure spécifique.

- **Agrégations horaires ou quotidiennes** : les appelants peuvent indiquer s’ils souhaitent visualiser leurs données d’utilisation Azure par intervalles de temps horaires ou quotidiens. Par défaut, les données sont présentées par jour.

- **Métadonnées d’instance (balises de ressource incluses)** : obtenez des détails de niveau instance, comme l’URI de ressource complet (/subscriptions/{subscription-id}/..), des informations sur le groupe de ressources et des balises de ressource. Les métadonnées vous aideront à allouer de façon déterministe et par programme l’utilisation par les balises, pour les cas d’utilisation comme la facturation interne.

- **Métadonnées sur les ressources** : des détails sur les ressources, comme le nom du compteur, la catégorie du compteur, la sous-catégorie du compteur, l’unité et la région offrent à l’appelant une meilleure compréhension des ressources consommées. Nous nous efforçons également d’aligner la terminologie des métadonnées de ressource dans l’ensemble du portail Azure, des fichiers CSV sur l’utilisation d’Azure et sur la facturation Contrat Entreprise (EA), ainsi que dans toutes les autres expériences destinées au public, afin de vous permettre de mettre en corrélation les données des différentes expériences.

- **Utilisation pour tous les types d’offre** : les données d’utilisation sont accessibles pour tous les types d’offre, tels que Paiement à l’utilisation, MSDN, Engagement monétaire, Crédit monétaire et Contrat Entreprise (EA).

**API Azure Resource RateCard (préversion)**

Utilisez l’API Azure Resource RateCard pour obtenir la liste des ressources Azure disponibles et une estimation des informations de prix pour chacune de ces ressources. L’API comprend :

- **Contrôle d’accès en fonction du rôle Azure** : configurez vos stratégies d’accès dans le portail Azure ou par le biais des cmdlets Azure PowerShell pour spécifier les utilisateurs ou les applications qui peuvent accéder aux données de RateCard. Les appelants doivent utiliser les jetons Azure Active Directory standard pour l’authentification. Ajoutez l’appelant au rôle Lecteur, Propriétaire ou Collaborateur pour être en mesure d’accéder aux données d’utilisation relatives à un abonnement Azure particulier.

- **Prise en charge des offres Paiement à l’utilisation, MSDN, Engagement monétaire et Crédit monétaire (offre EA non prise en charge)** : cette API fournit des informations de tarif au niveau des offres Azure. L’appelant de cette API doit transmettre les informations d’offre pour obtenir les détails et les tarifs des ressources. Nous sommes actuellement pas en mesure de fournir les tarifs EA, car les offres EA présentent des tarifs par inscription personnalisés. Voici quelques-uns des scénarios autorisés par l’utilisation combinée des API Usage et RateCard :

- **Dépenses Azure au cours du mois** : utilisez la combinaison des API d’utilisation et RateCard pour obtenir une meilleure idée de vos dépenses de cloud au cours du mois. Vous pouvez analyser les compartiments horaires et quotidiens des estimations d’utilisation et de facturation.

- **Configurer des alertes** : utilisez les API d’utilisation et RateCard pour obtenir une estimation de la consommation et de la facturation, et configurer des alertes en fonction des ressources ou des valeurs monétaires.

- **Prédiction de facture** : obtenez votre estimation de consommation et votre dépense cloud et appliquez des algorithmes d’apprentissage automatique pour prédire le montant de la facture à la fin du cycle de facturation.

- **Analyse des coûts avant consommation** : utilisez l’API RateCard pour prédire le montant de votre facture pour votre utilisation prévue lorsque vous déplacez vos charges de travail dans Azure. Si vous disposez de charges de travail existantes dans d’autres clouds publics ou privés, vous pouvez également mapper votre utilisation sur les tarifs Azure afin d’obtenir une meilleure estimation de vos dépenses Azure. Cette estimation vous donne la possibilité d’ajouter un tableau croisé dynamique sur l’offre et de comparer les différents types d’offre au-delà du paiement à l’utilisation, notamment l’engagement monétaire et le crédit monétaire. L’API vous donne également la possibilité de voir les différences de coût par région et vous permet d’effectuer une analyse de scénarios des coûts pour vous aider à prendre des décisions en matière de déploiement.

- **Analyse de scénarios** : vous pouvez déterminer s’il est plus rentable d’exécuter les charges de travail dans une autre région ou dans une autre configuration de la ressource Azure. Les coûts des ressources Azure peuvent varier en fonction de la région Azure que vous utilisez.

- Vous pouvez également déterminer si un autre type d’offre Azure propose un meilleur tarif pour une ressource Azure.

## <a name="networking-controls"></a>Contrôles de mise en réseau

L’accès aux ressources peut être interne (dans le réseau de l’entreprise) ou externe (par le biais d’internet). Il est facile pour les utilisateurs de votre organisation de placer par inadvertance des ressources au mauvais endroit et de potentiellement les exposer à un accès malveillant. Comme pour les appareils locaux, les entreprises doivent ajouter des contrôles appropriés pour s’assurer que les utilisateurs d’Azure prennent les bonnes décisions.

![Contrôles de mise en réseau](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Pour la gouvernance de l’abonnement, nous identifions des ressources principales qui fournissent le contrôle d’accès de base. Les ressources principales sont constituées des éléments suivants :

### <a name="network-connectivity"></a>Connectivité réseau

Les [réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sont des objets Conteneur de sous-réseaux. Bien qu’ils ne soient pas strictement nécessaires, ils sont souvent utilisés lors de la connexion d’applications à des ressources d’entreprise internes. Le service Réseau virtuel Azure vous permet de connecter en toute sécurité des ressources Azure entre elles en utilisant des réseaux virtuels.

Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez également connecter des réseaux virtuels à votre réseau local.

Voici les fonctionnalités des réseaux virtuels Azure :

- **Isolement** : les réseaux virtuels sont isolés les uns des autres. Vous pouvez créer des réseaux virtuels distincts pour le développement, le test et la production, qui utilisent les mêmes blocs d’adresses CIDR. À l’inverse, vous pouvez créer plusieurs réseaux virtuels qui utilisent différents blocs d’adresses CIDR et connecter les réseaux entre eux. Vous pouvez segmenter un réseau virtuel en plusieurs sous-réseaux. Azure fournit une résolution de noms interne pour les machines virtuelles et les instances de rôle Services cloud connectées à un réseau virtuel. Vous pouvez également configurer un réseau virtuel afin d’utiliser vos propres serveurs DNS au lieu d’utiliser la résolution de noms interne Azure.

- **Connectivité Internet** : toutes les machines virtuelles Azure et instances de rôle Services cloud connectées à un réseau virtuel ont accès à Internet, par défaut. Vous pouvez également activer l’accès entrant à des ressources spécifiques, en fonction de vos besoins.

- **Connectivité des ressources Azure** : les ressources Azure telles que les Services cloud et les machines virtuelles peuvent être connectées au même réseau virtuel. Les ressources peuvent se connecter entre elles à l’aide d’adresses IP privées, même si elles se trouvent dans des sous-réseaux différents. Azure fournit un routage par défaut entre les sous-réseaux, les réseaux virtuels et les réseaux locaux, sans que vous ayez à configurer et gérer ces itinéraires.

- **Connectivité de réseau virtuel** : les réseaux virtuels peuvent être connectés entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec n’importe quelle ressource sur n’importe quel autre réseau virtuel.

- **Connectivité locale** : les réseaux virtuels peuvent être connectés à des réseaux locaux via des connexions réseau privées entre votre réseau et Azure, ou via une connexion VPN de site à site sur Internet.

- **Filtrage du trafic** : le trafic réseau entrant et sortant des machines virtuelles et des instances de rôle Services cloud peut être filtré par adresse IP source et port, adresse IP de destination et port, et protocole.

- **Routage** : vous pouvez également remplacer le routage Azure par défaut en configurant votre propre routage ou en utilisant des itinéraires BGP via une passerelle réseau.

## <a name="network-access-controls"></a>Contrôles d’accès réseau

Les [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sont similaires à un pare-feu et fournissent des règles pour la manière dont une ressource peut « communiquer » à travers le réseau. Ils permettent un contrôle granulaire sur la manière dont un sous-réseau (ou une machine virtuelle) peut se connecter à Internet ou à d’autres sous-réseaux dans le même réseau virtuel, ou même si une connexion doit être établie.

Un groupe de sécurité réseau (NSG) contient une liste de règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées aux réseaux virtuels Azure (VNet). Les NSG peuvent être associés à des sous-réseaux, à des machines virtuelles spécifiques (Classic) ou à des interfaces réseau (NIC) individuelles attachées à des machines virtuelles (Resource Manager).

Lorsqu’un NSG est associé à un sous-réseau, les règles s’appliquent à toutes les ressources connectées au sous-réseau. Vous pouvez restreindre davantage le trafic en associant également un NSG à une machine virtuelle ou à une NIC.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Sécurité et conformité continue aux normes de l’organisation

Chaque entreprise a des besoins différents et tire des avantages différents des solutions cloud. Mais les clients ont tous les mêmes préoccupations de base concernant la migration vers le cloud. Ils souhaitent conserver le contrôle de leurs données et veulent que leurs données soient sécurisées et privées, tout en maintenant la transparence et la conformité.

Voici ce que les clients veulent obtenir des fournisseurs de solutions cloud :

- **Sécuriser les données** : même s’ils reconnaissent que le cloud peut accroître la sécurité des données et le contrôle administratif, les responsables informatiques s’inquiètent toujours du fait que la migration vers le cloud les rendra plus vulnérables face aux attaques que leurs solutions internes actuelles.

- **Maintenir la confidentialité des données** : les services cloud représentent des défis uniques pour les entreprises en termes de confidentialité. À mesure que les entreprises se tournent vers le cloud pour réduire les coûts d’infrastructure et améliorer leur flexibilité, elles s’inquiètent également de perdre le contrôle sur l’emplacement de stockage de leurs données, à savoir qui y a accès et comment elles sont utilisées.

- **Conserver le contrôle** : alors même qu’elles tirent parti du cloud pour déployer des solutions plus innovantes, les entreprises s’inquiètent de perdre le contrôle de leurs données. Suite aux révélations récentes sur des agences gouvernementales accédant à des données client, par des moyens légaux et illégaux, les directeurs informatiques s’inquiètent de stocker leurs données sur le cloud.

- **Promouvoir la transparence** : la sécurité, la confidentialité et le contrôle sont importants pour les décideurs d’entreprise, mais ils veulent également la possibilité de vérifier indépendamment comment leurs données sont stockées, utilisées et sécurisées.

- **Respecter la conformité** : alors que les entreprises étendent leur utilisation des technologies cloud, la complexité et la portée des normes et des réglementations ne cessent d’évoluer. Les entreprises veulent être sûres que leurs normes de conformité seront respectées et que la conformité évoluera parallèlement aux réglementations.

## <a name="security-configuration-monitoring-and-alerting"></a>Configuration de la sécurité, monitoring et alertes

Les abonnés Azure peuvent gérer leurs environnements cloud à partir de différents périphériques, comme les stations de travail de gestion, les ordinateurs de développement ou encore les périphériques d’utilisateurs finaux privilégiés, qui disposent d’autorisations spécifiques. Dans certains cas, les fonctions d’administration sont effectuées par le biais de consoles web, comme le portail Azure. Des connexions directes peuvent aussi être établies avec Azure à partir de systèmes locaux sur des réseaux privés virtuels (VPN), Terminal Services, des protocoles d’application cliente ou l’API de gestion des services Azure (SMAPI) (par programmation). Par ailleurs, les points de terminaison de client peuvent être joints au domaine ou isolés et non gérés, comme les tablettes ou les smartphones.

Bien que les fonctions de gestion et d’accès offrent de nombreuses options, le déploiement de cloud peut se révéler plus délicat. La gestion, le suivi et l’audit peuvent être plus complexes pour les actions d’administration. Des menaces de sécurité peuvent également survenir avec l’accès non réglementé aux points de terminaison de client utilisés pour la gestion des services cloud. Les stations de travail personnelles ou communes destinées au développement et à la gestion de l’infrastructure introduisent des menaces imprévisibles, notamment avec la navigation Web (par exemple, lors d’attaques de point d’eau) ou la messagerie électronique (par exemple, ingénierie sociale et hameçonnage).

La surveillance, la journalisation et l’audit aident à suivre et à comprendre les activités d’administration, mais il n’est pas toujours possible d’auditer toutes les actions en détail compte tenu de la quantité de données générées. Il est toutefois recommandé d’auditer l’efficacité des stratégies de gestion.

Utilisez la gouvernance de sécurité Azure à partir des GPO AD DS pour contrôler toutes les interfaces Windows de l’administrateur, comme le partage de fichiers. Incluez les stations de travail de gestion dans l’audit, la surveillance et la journalisation. Effectuez le suivi des accès et de l’utilisation au niveau administrateur et développeur.

### <a name="azure-security-center"></a>Azure Security Center

[L’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) fournit une vue centralisée de l’état de la sécurité des ressources dans les abonnements et fournit des recommandations qui aident à empêcher la compromission des ressources. Il peut autoriser des stratégies plus granulaires (par exemple, l’application de stratégies à des groupes de ressources spécifiques qui permettent à l’entreprise d’adapter sa position en fonction des risques auxquels elle est confrontée).

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité. Une fois que vous avez activé les [stratégies de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Les informations sur la configuration du réseau sont instantanément disponibles.

Azure Security Center constitue une combinaison des meilleures pratiques en matière d’analyse et de gestion de la stratégie de sécurité pour toutes les ressources dans un abonnement Azure. Cet outil puissant et facile à utiliser permet aux équipes de sécurité et aux responsables de la gestion des risques d’éviter les menaces de sécurité, de les détecter et d’y répondre, car il collecte et analyse automatiquement les données de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires, comme les logiciels anti-programmes malveillants et les pare-feu.

En outre, Azure Security Center applique une analytique avancée, notamment Machine Learning et l’analyse comportementale, tout en exploitant des informations globales concernant les menaces provenant de produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes. La [gouvernance de sécurité](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) peut être appliquée globalement au niveau de l’abonnement ou elle peut être adaptée à des exigences spécifiques et granulaires qui s’appliquent à des ressources individuelles via la définition de la stratégie.

Enfin, Azure Security Center analyse l’intégrité de sécurité des ressources en fonction de ces stratégies et utilise ces fonctionnalités pour présenter des tableaux de bord informatifs et générer des alertes pour des événements tels que la détection de logiciels malveillants ou des tentatives de connexions à partir d’adresses IP malveillantes.

>[!Note]
> Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Security Center collecte les données de vos machines virtuelles afin d’évaluer l’état de leur sécurité, de fournir des recommandations en matière de sécurité et de vous avertir des menaces. Lorsque vous accédez au Centre de sécurité pour la première fois, la collecte de données est activée sur toutes les machines virtuelles de votre abonnement. La collecte des données est recommandée, mais vous pouvez refuser cette fonctionnalité en la [désactivant](https://docs.microsoft.com/azure/security-center/security-center-faq) dans la stratégie de Security Center.

Enfin, l’Azure Security Center est une plateforme ouverte qui permet aux partenaires de Microsoft et aux éditeurs de logiciels indépendants de créer un logiciel qui se connecte à l’Azure Security Center pour améliorer leurs fonctionnalités.

Azure Security Center surveille les ressources Azure suivantes :

- Machines virtuelles (notamment les services cloud)

- Réseaux virtuels Azure

- Service SQL Azure

- Solutions de partenaires intégrées à votre abonnement Azure, par exemple un pare-feu d’applications web sur les machines virtuelles et sur [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="operations-management-suite"></a>Operations Management Suite

Le [programme de sécurité et de gouvernance](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) des informations de l’équipe de maintenance et de développement logiciel du Service OMS prend en charge ses besoins métier, et respecte les lois et réglementations décrites sur les sites [Centre de confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/) et [Microsoft Trust Center Compliance](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Ceux-ci expliquent également comment OMS fixe les exigences de sécurité, identifie les contrôles de sécurité, et gère et analyse les risques. Chaque année, nous révisons les stratégies, normes, procédures et directives.

Chaque membre de l’équipe de développement d’OMS reçoit une formation formelle en matière de sécurité des applications. En interne, nous utilisons un système de contrôle de version pour le développement de logiciels. Chaque projet de logiciel est protégé par le système de contrôle de version.

Microsoft dispose d’une équipe dédiée à la sécurité et à la conformité, qui surveille et évalue tous les services en son sein. Les responsables de la sécurité des informations qui constituent l’équipe ne sont pas associés aux services d’ingénierie qui développent OMS. Les responsables de la sécurité ont leur propre chaîne de gestion, et effectuent des évaluations indépendantes des produits et services pour s’assurer de la sécurité et de la conformité de ceux-ci.

Operations Management Suite (également désigné sous l’acronyme OMS) est un ensemble de services de gestion qui ont été conçus dans le cloud dès le début. Plutôt que de déployer et de gérer des ressources locales, les composants OMS sont entièrement hébergés dans Azure. OMS ne requiert qu’une configuration minime et vous permet d’être opérationnel en quelques minutes seulement.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Le fait que les services OMS s’exécutent dans le cloud ne signifie pas qu’ils ne peuvent pas gérer votre environnement local de manière efficace.

Il vous suffit de placer un agent sur tout ordinateur Windows ou Linux de votre centre de données pour que cet agent envoie des données à Log Analytics, qui les analysera en même temps que toutes les autres données collectées à partir des services cloud ou locaux. Utilisez les services de sauvegarde Azure et Azure Site Recovery pour tirer profit des capacités du cloud en matière de sauvegarde et de haute disponibilité pour vos ressources locales.

Les runbooks figurant dans le cloud ne peuvent généralement pas accéder à vos ressources locales, mais vous pouvez aussi installer un agent sur un ou plusieurs ordinateurs qui hébergeront des runbooks dans votre centre de données. Lorsque vous démarrez un runbook, vous devez simplement indiquer si vous souhaitez l’exécuter dans le cloud ou sur un Worker local.

Les fonctionnalités fondamentales d’OMS sont fournies par un ensemble de services qui s’exécutent dans Azure. Chaque service assure une fonction de gestion spécifique, et vous pouvez combiner plusieurs services pour mettre en œuvre différents scénarios de gestion.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure Operation Manager étend ses fonctionnalités en fournissant des solutions de gestion. Les [solutions de gestion](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) constituent des ensembles de logiques prépackagés qui implémentent un scénario de gestion tirant profit d’un ou de plusieurs services OMS.

![Gestion des opérations Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Microsoft et ses partenaires mettent à votre disposition différentes solutions que vous pouvez facilement ajouter à votre abonnement Azure pour augmenter la valeur de votre investissement dans la solution OMS.

En tant que partenaire, vous pouvez créer vos propres solutions pour prendre en charge vos applications et services et les fournir aux utilisateurs par le biais de la plateforme Place de marché Azure ou des modèles de démarrage rapide.

## <a name="performance-alerting-and-monitoring"></a>Alertes et monitoring relatifs aux performances

### <a name="alerting"></a>Génération d’alertes

Les alertes sont une méthode d’analyse des mesures des ressources événements ou journaux Azure, puis d’avertissement quand une condition spécifiée est remplie.

**Alertes dans différents services Azure**

Les alertes sont disponibles dans différents services, notamment les suivants :

- Application Insights : prend en charge les tests web et les alertes de mesure.

>[!Note]
> Consultez [Définir des alertes dans Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) et [Analyser la disponibilité et la réactivité d’un site web](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Log Analytics (Operations Management Suite) : prend en charge le routage des journaux de diagnostic et d’activité vers Log Analytics. Operations Management Suite autorise les alertes de mesure, de journal, etc.

>[!Note]
> Pour plus d’informations, consultez Alertes dans [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure Monitor : prend en charge les alertes sur la base de valeurs de mesures et d’événements de journal d’activité. Vous pouvez utiliser [l’API REST Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) pour gérer les alertes.

>[!Note]
> Pour plus d’informations, consultez [Utilisation du portail Azure, de PowerShell ou de l’interface de ligne de commande pour créer des alertes](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Surveillance

Les problèmes de performances dans votre application cloud peuvent affecter votre entreprise. Avec plusieurs composants interconnectés et de nouvelles versions fréquentes, des dégradations peuvent se produire à tout moment. Et si vous développez une application, vos utilisateurs découvrent généralement des problèmes que vous n’avez pas trouvés dans le test. Vous devez prendre connaissance de ces problèmes immédiatement et disposer d’outils pour diagnostiquer et résoudre les problèmes. Microsoft Azure a une gamme d’outils permettant d’identifier ces problèmes.

**Comment surveiller mes applications cloud Azure ?**

Il existe une gamme d’outils pour la surveillance des services et applications Azure. Certaines de leurs fonctionnalités se chevauchent. C’est en partie pour des raisons historiques et en partie en raison du flou entre le développement et le fonctionnement d’une application.

Voici les outils principaux :

- **Azure Monitor** est l’outil de base pour la surveillance des services s’exécutant sur Azure. Il vous fournit des données au niveau de l’infrastructure sur le débit d’un service et l’environnement. Si vous gérez toutes vos applications dans Azure et décidez d’augmenter ou de diminuer les ressources, Azure Monitor vous donne le nécessaire pour démarrer.

- **Application Insights** peut être utilisé pour le développement et comme solution de surveillance de la production. Il fonctionne en installant un package dans votre application et vous donne donc une vue plus interne de ce qui se passe. Ses données comprennent les temps de réponse des dépendances, les traces des exceptions, le débogage des captures instantanées et les profils d’exécution. Il offre de puissants outils intelligents pour analyser toutes ces données de télémétrie afin de vous aider à déboguer une application et vous aider à comprendre ce que les utilisateurs font avec. Vous pouvez savoir si un pic dans les temps de réponse est causé par un élément dans une application, ou un problème de ressource externe. Si vous utilisez Visual Studio et que l’application est en cause, vous pouvez accéder directement aux lignes de code problématiques pour les corriger.

- **Log Analytics** est destiné à ceux qui souhaitent optimiser les performances et planifier la maintenance des applications exécutées en production. Il est basé dans Azure. Il collecte et agrège les données provenant de nombreuses sources, mais avec un délai de 10 à 15 minutes. Il fournit une solution de gestion informatique globale holistique pour Azure, en local et dans les infrastructures cloud tierces (par exemple, Amazon Web Services). Il fournit des outils plus riches pour analyser les données de plusieurs sources, en acceptant des requêtes complexes sur tous les journaux et en pouvant générer des alertes de façon proactive pour des conditions spécifiées. Vous pouvez même collecter des données personnalisées dans son référentiel central pour les interroger et visualiser.

- **System Center Operations Manager (SCOM)** pour gérer et surveiller les installations cloud à grande échelle. Vous le connaissez peut-être déjà comme outil de gestion local pour Windows Server en local et les solutions cloud basées sur Hyper-V, mais il peut également s’intégrer à et gérer des applications Azure. Entre autres choses, il peut installer Application Insights sur des applications en direct existantes. Si une application est en panne, vous en êtes informé sous quelques secondes.


## <a name="next-steps"></a>Étapes suivantes

- [Meilleures pratiques relatives à la création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Exemples d’implémentation de la gouvernance des abonnements Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).
