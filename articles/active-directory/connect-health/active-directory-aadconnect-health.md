---
title: "Surveillez votre infrastructure d’identité locale dans le cloud."
description: "Cette page décrit Azure AD Connect Health et vous explique pourquoi l’utiliser."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: f8082b632e9fef4aa5d46deb35e415f2b1c55dda
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud
Azure Active Directory (Azure AD) Connect Health vous permet de surveiller et d’analyser votre infrastructure d’identité locale et les services de synchronisation. Il vous permet de maintenir une connexion fiable à Office 365 et à Microsoft Online Services en fournissant des fonctionnalités de surveillance de vos composants d’identification clés tels que les serveurs Active Directory Federation Services (AD FS), les serveurs Azure AD Connect (moteur de synchronisation), les contrôleurs de domaine Active Directory, etc. En outre, les points de données clés sur ces composants sont facilement accessibles, ce qui vous permet d’obtenir des données d’utilisation et d’autres informations importantes pour prendre des décisions avisées.

Ces informations sont toutes présentées dans le [portail Azure AD Connect Health](https://aka.ms/aadconnecthealth). Dans le portail Azure AD Connect Health, vous pouvez afficher les alertes, surveiller les performances, analyser les utilisations, etc. Azure AD Connect Health prend en compte l’intégrité des composants d’identité clé, le tout à un seul endroit.

![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Au fur et à mesure que le nombre de fonctionnalités d’Azure AD Connect Health augmente, le portail fournit un tableau de bord unique en fonction de l’identité. Vous obtenez ainsi un environnement encore plus robuste, intégré et sain pour vos utilisateurs, afin d’augmenter leur capacité à effectuer des actions.
## <a name="why-use-azure-ad-connect-health"></a>Pourquoi utiliser Azure AD Connect Health ?
Lorsque vous intégrez vos répertoires locaux avec Azure AD, vos utilisateurs gagnent en productivité car ils disposent d’une identité commune pour accéder aux ressources cloud et locales. Toutefois, cette intégration nécessite que vous vous assuriez que cet environnement est sécurisé pour que les utilisateurs puissent accéder aux ressources locales et cloud en toute sécurité depuis n’importe quel appareil. Azure AD Connect Health vous aide à surveiller et à obtenir facilement des informations sur l’infrastructure d’identité locale utilisée pour accéder à Office 365 ou à d’autres applications Azure AD. Son installation est aussi simple que celle d’un agent sur chacun de vos serveurs d’identité local.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Utilisation d’Azure AD Connect Health pour AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health pour AD FS prend en charge AD FS 2.0 dans Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Cette prise en charge inclut également la surveillance de tous les serveurs proxy AD FS ou serveurs proxy d’application web qui prennent en charge l’authentification pour l’accès extranet. Avec une installation simple et économique de l’agent d’intégrité, Azure AD Connect Health pour AD FS fournit l’ensemble des fonctionnalités clés suivantes :

* Analyse des alertes pour savoir quand les serveurs AD FS et proxy AD FS ne sont pas sains
* Notifications par courrier électronique pour les alertes critiques
* Tendances des données de performance, utiles pour la planification des capacités d’AD FS
* Analyse de l’utilisation des connexions AD FS avec un autre tableau croisé dynamique (applications, utilisateurs, emplacement réseau, etc.), utile pour comprendre comment AD FS est utilisé
* Rapports AD FS comme le Top 50 des utilisateurs avec des tentatives ayant échoué en raison de noms d’utilisateur/mots de passe incorrects et leur dernière adresse IP
  
Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)

La vidéo suivante fournit une vue d’ensemble d’Azure AD Connect Health pour AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>

>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health pour la synchronisation surveille et fournit des informations sur les synchronisations effectuées entre votre instance Active Directory locale et Azure AD. Azure AD Connect Health pour la synchronisation fournit les fonctionnalités clés suivantes :

* Analyse grâce à des alertes, pour savoir quand un serveur Azure AD Connect (moteur de synchronisation) n’est pas intègre
* Notifications par courrier électronique pour les alertes critiques
* Analyse opérationnelle de synchronisation, notamment les graphiques de latence pour les opérations de synchronisation et les tendances dans différentes opérations, en particulier les ajouts, les mises à jour, les suppressions
* Rapide aperçu des informations sur les propriétés de synchronisation et la dernière réussite de l’exportation vers Azure AD
* Rapports sur les erreurs de synchronisation de niveau objet \(ne nécessite pas Azure AD Premium\)

Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md)

La vidéo suivante fournit une vue d’ensemble d’Azure AD Connect Health pour la synchronisation.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Utilisation d’Azure AD Connect Health pour AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health pour Active Directory Domain Service (AD DS) permet de surveiller les contrôleurs de domaine installés sur Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. L’installation de l’agent d’intégrité vous permet de surveiller votre environnement local AD DS à partir du cloud. Azure AD Connect Health pour AD DS fournit les fonctionnalités clé suivantes :

* Alertes de surveillance pour détecter quand les contrôleurs de domaine sont défectueux et notifications par e-mail pour les alertes critiques
* Tableau de bord des contrôleurs de domaine qui fournit un aperçu rapide de l’intégrité et de l’état de fonctionnement de vos contrôleurs de domaine
* Tableau de bord d’état de réplication fournissant les dernières informations de réplication, ainsi que des liens vers des guides de dépannage lorsque des erreurs sont détectées
* Accès rapide n’importe où aux graphiques de données de compteurs de performances populaires, nécessaires pour la résolution des problèmes et à des fins de surveillance

Pour en savoir plus sur l’[Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)

La vidéo suivante fournit une vue d’ensemble d’Azure AD Connect Health pour AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Démarrer avec Azure AD Connect Health
Pour vous familiariser avec Azure AD Connect Health, procédez comme suit :

1. Accédez à [Obtenir Azure AD Premium](../active-directory-get-started-premium.md) ou [Démarrer une version d’évaluation](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Téléchargez et installez les agents Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) sur vos serveurs d’identité.
3. Affichez le tableau de bord d’Azure AD Connect Health sur [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Avant de pouvoir consulter des données sur votre tableau de bord Azure AD Connect Health, il vous faudra installer les agents Azure AD Connect Health sur vos serveurs cibles.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Téléchargement et installation de l’agent Azure AD Connect Health
* Vérifiez que vous [disposez de la configuration requise](active-directory-aadconnect-health-agent-install.md#requirements) pour Azure AD Connect Health.
* Prise en main d’Azure AD Connect Health pour AD FS
    * [Téléchargez l’agent Azure AD Connect Health pour AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Consultez les instructions d’installation](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Prise en main d’Azure AD Connect Health pour la synchronisation
    * [Téléchargez et installez la dernière version d’Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). L’agent d’intégrité pour la synchronisation sera ajouté dans le cadre de l’installation d’Azure AD Connect (version 1.0.9125.0 ou ultérieure).
* Prise en main d’Azure AD Connect Health pour AD DS
    * [Téléchargez l’agent Azure AD Connect Health pour AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Consultez les instructions d’installation](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portail Azure AD Connect Health
Ce portail vous permet d’afficher des alertes, de surveiller les performances et d’analyser les utilisations. L’URL https://aka.ms/aadconnecthealth vous amène au panneau principal d’Azure AD Connect Health. Considérez les panneaux comme des fenêtres. Dans le panneau principal, vous pouvez voir **Démarrage rapide**, les services offerts dans Azure AD Connect Health et d’autres options de configuration. Consultez la capture d’écran suivante et la brève explication qui l’accompagne. Après le déploiement des agents, le service de contrôle d’intégrité s’identifie automatiquement pour les services qu’Azure AD Connect Health analyse.

> [!NOTE]
> Pour plus d’informations sur les licences, consultez la [FAQ Azure AD Connect Health](active-directory-aadconnect-health-faq.md) ou la [page de tarification d’Azure AD](https://aka.ms/aadpricing).
    
![portail Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Démarrage rapide** : lorsque vous sélectionnez cette option, le panneau **Démarrage rapide** s’ouvre. Vous pouvez télécharger l’agent Azure AD Connect Health en sélectionnant **Obtenir les outils**. Vous pouvez également accéder à la documentation et fournir des commentaires.
* **Active Directory Federation Services** : cette option affiche tous les services AD FS actuellement surveillés par Azure AD Connect Health. Lorsque vous sélectionnez une instance, le panneau qui s’ouvre fournit des informations sur cette instance de service. Il comporte une vue d’ensemble, les propriétés, les alertes, les données de surveillance et une analyse de l’utilisation. Pour en savoir plus sur les fonctionnalités, consultez [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (synchronisation)** : cette option affiche les serveurs Azure AD Connect actuellement surveillés par Azure AD Connect Health. Lorsque vous sélectionnez une entrée, le panneau qui s’ouvre fournit des informations sur vos serveurs Azure AD Connect. Pour en savoir plus sur les fonctionnalités, consultez [Utilisation d’Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services** : cette option affiche toutes les forêts AD DS actuellement surveillées par Azure AD Connect Health. Lorsque vous sélectionnez une forêt, le panneau qui s’ouvre fournit des informations sur cette forêt. Ces informations incluent une vue d’ensemble des informations essentielles, le tableau de bord des contrôleurs de domaine, le tableau de bord d’état de réplication, des alertes et la surveillance. Pour en savoir plus sur les fonctionnalités, consultez [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md).
* **Configurer** : cette section inclut des options permettant d’activer ou de désactiver les éléments suivants :

  - Mise à jour automatique de l’agent Azure AD Connect Health vers la version la plus récente : votre système est automatiquement mis à jour vers la dernière version disponible de l’agent Azure AD Connect Health. Cette option est activée par défaut.
  - Autorisation de l’accès, par Microsoft, aux données d’intégrité de votre répertoire Azure AD à des fins exclusives de résolution des problèmes : si cette option est activée, Microsoft pourra consulter les données auxquelles vous avez accès. L’accès à ces informations simplifient la résolution des problèmes. Elle est désactivée par défaut.

## <a name="related-links"></a>Liens connexes
* [Installation de l’agent Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Utilisation d’Azure AD Connect Health avec AD DS](active-directory-aadconnect-health-adds.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historique des versions d’Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
