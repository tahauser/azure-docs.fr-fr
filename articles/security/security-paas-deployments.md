---
title: "Sécurisation des déploiements PaaS | Microsoft Docs"
description: " Comprenez les avantages d’une infrastructure PaaS pour la sécurité par rapport aux autres modèles de service cloud et découvrez les pratiques recommandées pour la sécurisation de votre déploiement PaaS Azure. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 4629e0ab6bbc9554128a923e92b269df79446b18
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-deployments"></a>Sécurisation des déploiements PaaS

Cet article fournit des informations qui vous permettent :

- de comprendre les avantages de l'hébergement d'application dans le cloud pour la sécurité ;
- d'évaluer les avantages de la plateforme en tant que service (PaaS) et autres modèles de service cloud pour la sécurité ;
- de passer pour la sécurité d'une approche orientée réseau à une approche de sécurité de périmètre orientée identité ;
- d'implémenter les bonnes pratiques recommandées de la sécurité de la PaaS.

## <a name="cloud-security-advantages"></a>Avantages du cloud en matière de sécurité
Le cloud offre certains avantages pour la sécurité. Dans un environnement local, les organisations ont probablement des obligations non respectées et des ressources limitées pour investir dans la sécurité, avec pour résultat un environnement où les pirates informatiques sont en mesure d’exploiter des vulnérabilités à tous les niveaux.

![Les avantages de l’ère du cloud en matière de sécurité][1]

Les organisations peuvent améliorer la détection des menaces et leur temps de réponse à l’aide de fonctionnalités de sécurité basées sur le cloud d’un fournisseur et l'intelligence du cloud.  En transférant les responsabilités au fournisseur de cloud, les organisations peuvent optimiser leur couverture de sécurité, ce qui leur permet de réaffecter des ressources de sécurité et leur budget à d'autres priorités de l’entreprise.

## <a name="division-of-responsibility"></a>Répartition de la responsabilité
Il est important de comprendre la répartition des responsabilités entre vous et Microsoft. Localement, vous avez toute la responsabilité, mais, lorsque vous vous déplacez vers le cloud, certaines responsabilités sont transférées à Microsoft. La matrice de responsabilité suivante montre les zones de la pile dans un déploiement SaaS, PaaS et IaaS dont vous êtes responsable et celles dont Microsoft est responsable.

![Zones de responsabilité][2]

Vous avez vos données et les identités pour tous les types de déploiement dans le cloud. Vous êtes chargé de protéger la sécurité de vos données et des identités, des ressources locales, et des composants du cloud que vous contrôlez (qui varient selon le type de service).

Les responsabilités qui sont toujours les vôtres quel que soit le type de déploiement sont les suivantes :

- Données
- Endpoints
- Compte
- gestion de l’accès

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Avantages d'un modèle de service cloud PaaS en matière de sécurité
À l’aide de la même matrice de responsabilité, passons en revue les avantages pour la sécurité d’un déploiement PaaS Azure par rapport à un déploiement local.

![Les avantages d'une PaaS en matière de sécurité][3]

En commençant par le premier élément, l’infrastructure physique, Microsoft réduit les risques courants et les responsabilités. Étant donné que le cloud Microsoft est surveillé en permanence par Microsoft, il est difficile à attaquer. Il n’y a aucun sens pour une personne malveillante de choisir le cloud Microsoft comme cible. À moins que le pirate informatique n'ait beaucoup d’argent et de ressources, il préfèrera passer à une autre cible.  

Au milieu de l'infrastructure, il n’existe aucune différence entre un déploiement PaaS et un déploiement local. Les risques sont similaires dans les couches d'application et de gestion des accès et des comptes. Dans la section suivante de cet article, nous vous présentons les bonnes pratiques pour éliminer ou réduire ces risques.

En haut de l'infrastructure (gouvernance des données et gestion des droits), vous prenez un risque qui peut être limité par la gestion de clés (la gestion des clés est traitée dans les bonnes pratiques). Même si la gestion des clés est une responsabilité supplémentaire, vous n'avez plus à gérer certaines zones d'un déploiement PaaS, et vous pouvez donc transférer des ressources vers la gestion des clés.

La plateforme Azure offre également une protection contre le DDoS renforcée à travers diverses technologies de réseau. Toutefois, tous les types de méthodes de protection contre le déni de service distribué (DDoS) basée sur le réseau ont leurs limites par lien et par centre de données. Afin d’éviter l’impact des attaques DDoS de grande envergure, vous pouvez tirer parti des capacités de cloud de base d’Azure qui permettent d'augmenter la taille des instances rapidement et de vous défendre contre les attaques DDoS. Nous aborderons la procédure plus en détail dans les articles sur les pratiques recommandées.

## <a name="modernizing-the-defenders-mindset"></a>Une nouvelle mentalité pour la défense
Avec les déploiements PaaS, votre approche globale de la sécurité évolue. Vous n'avez pas besoin de tout contrôler vous-même et partagez la responsabilité avec Microsoft.

Une autre différence importante entre les déploiements PaaS et les déploiements locaux traditionnels est une nouvelle définition du périmètre de sécurité principal. Historiquement, le périmètre de sécurité local principal était votre réseau et la plupart des conceptions de sécurité locales utilisent le réseau en tant que pivot de sécurité principal. Pour les déploiements PaaS, il est préférable de tenir compte de l’identité comme périmètre de sécurité principal.

## <a name="identity-as-the-primary-security-perimeter"></a>L'identité en tant que périmètre de sécurité principal
L'une des cinq caractéristiques essentielles du cloud computing est un large accès au réseau, ce qui rend l'approche centrée sur le réseau moins pertinente. Le cloud computing a en grande partie pour but de permettre aux utilisateurs d’accéder à des ressources, quel que soit leur emplacement. Pour la plupart des utilisateurs, l'emplacement est quelque part sur Internet.

La figure suivante montre comment le périmètre de sécurité a évolué d’un périmètre de réseau vers un périmètre d’identité. La sécurité se résume moins à la protection de votre réseau et plus à la défense de vos données, ainsi qu'à la gestion de la sécurité de vos applications et de vos utilisateurs. La principale différence est que vous pouvez axer la sécurité sur ce qui est important pour votre entreprise.

![L'identité en tant que nouveau périmètre de sécurité][4]

Initialement, les services PaaS Azure (par exemple, les rôles web et Azure SQL) fournissaient peu ou pas de défenses du périmètre de réseau traditionnelles. Il était entendu que l'élément devait être exposé sur Internet (rôle web) et que l’authentification fournit le nouveau périmètre (par exemple, BLOB ou Azure SQL).

Les pratiques de sécurité modernes partent du principe que l’adversaire a violé le périmètre du réseau. Par conséquent, les pratiques de défense modernes sont axées sur l'identité. Les organisations doivent établir un périmètre de sécurité basé sur les identités avec forte hygiène d’authentification et d’autorisation (bonnes pratiques).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Recommandations pour la gestion du périmètre d’identité

Les principes et modèles pour le périmètre du réseau existaient depuis des décennies. En revanche, l’approche basée sur l'identité comme périmètre de sécurité principal n'est pas répandue dans l'industrie. Cela étant dit, nous avons accumulé suffisamment d’expérience pour fournir des recommandations générales qui s'appliquent dans la pratique à presque tous les services PaaS.

Voici un résumé des bonnes pratiques générales pour la gestion de votre périmètre d’identité.

- **Ne perdez pas vos clés ou vos informations d’identification** Les clés et les informations d’identification sont essentielles pour sécuriser les déploiements PaaS. La perte de clés ou d'informations d’identification est un problème courant. Une bonne solution consiste à utiliser une solution centralisée où les clés et les secrets peuvent être stockés dans des modules de sécurité matériels (HSM). Azure vous fournit un HSM dans le cloud avec [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Ne placez les informations d’identification et d'autres secrets dans le code source ou dans GitHub** La seule chose pire que la perte de vos clés et des informations d’identification est qu'un tiers non autorisé y accède. Des pirates peuvent tirer parti de technologies de robot pour rechercher les clés et les secrets stockés dans des référentiels de code, notamment GitHub. Ne placez pas de clé et de secrets dans ces référentiels de code source publics.
- **Protégez vos interfaces de gestion de machine virtuelle sur des services PaaS et IaaS hybrides** Les services IaaS et PaaS s’exécutent sur des machines virtuelles (VM). Selon le type de service, plusieurs interfaces de gestion sont disponibles pour la gestion à distance directe de ces machines virtuelles. Vous pouvez utiliser les protocoles de gestion à distance tels que [le protocole SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell), [le protocole RDP (Remote Desktop)](https://support.microsoft.com/kb/186607) et [PowerShell distant](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting). En règle générale, nous vous recommandons de ne pas activer l’accès à distance direct aux machines virtuelles à partir d’Internet. S’il est disponible, vous devez utiliser d'autres approches, telles que l’utilisation d’un réseau privé virtuel dans un réseau virtuel Azure. Si aucune autre approche n'est disponible, assurez-vous que vous utilisez des phrases secrètes complexes et, si d'autres options sont disponibles, l’authentification à deux facteurs (notamment [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)).
- **Utilisez une authentification forte et des plateformes d’autorisation**

  - Utilisez des identités fédérées dans Azure AD au lieu des magasins d’utilisateurs personnalisés. Lorsque vous utilisez des identités fédérées, vous tirez parti d'une approche basée sur une plateforme et vous déléguez la gestion des identités autorisées à vos partenaires. Une approche d’identité fédérée est particulièrement importante dans des scénarios dans lesquels le contrat de certains employés prend fin et que des informations doivent être répercutées sur plusieurs systèmes d’identité et d’autorisation.
  - Utilisez des mécanismes d'authentification et d'autorisation basés sur des plateformes au lieu d'un code personnalisé. La raison est que le développement d'un code d’authentification personnalisé peut être sujet aux erreurs. La plupart de vos développeurs ne sont pas des experts en sécurité et ont peu de chance d’être conscients des subtilités et des derniers développements dans les domaines de l’authentification et l’autorisation. Le code commercial (par exemple par Microsoft) est souvent contrôlé en ce qui concerne la sécurité.
  - Utilisez l'authentification multifacteur. L’authentification multifacteur est la norme actuelle pour l’authentification et l’autorisation, car elle évite les failles de sécurité inhérentes à certains types de nom d’utilisateur et de mots de passe d’authentification. L'accès aux interfaces de gestion Azure (portail / remote PowerShell) et aux services destinés aux clients doit être conçu et configuré pour utiliser [Azure multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Utilisez des protocoles d’authentification standard, tels qu'OAuth2 et Kerberos. Ces protocoles ont été largement contrôlés et sont probablement implémentés dans le cadre de vos bibliothèques de plateforme pour l’authentification et l’autorisation.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons vu les avantages d’un déploiement PaaS Azure en matière de sécurité. Découvrez ensuite les pratiques recommandées pour sécuriser solutions PaaS web et mobiles. Nous allons commencer par Azure App Service, Azure SQL Database et Azure SQL Data Warehouse. Des liens seront fournis dans la liste suivante quand des articles sur les pratiques recommandées pour d’autres services Azure seront disponibles :

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database et Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure Storage
- Cache REDIS Azure
- Azure Service Bus
- Pare-feu d’applications web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
