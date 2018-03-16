---
title: "Services de domaine Azure Active Directory : caractéristiques | Microsoft Docs"
description: "Caractéristiques des services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: af0670a44937bb14df9afc206ea4601c0b9289a2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="azure-ad-domain-services"></a>Services de domaine Azure AD
## <a name="features"></a>Caractéristiques
Les fonctionnalités suivantes sont disponibles dans les domaines gérés des services de domaine Azure AD.

* **Expérience de déploiement simple :** vous pouvez activer les services de domaine Azure AD pour votre client Azure AD en quelques clics. Que votre client Azure AD soit un client cloud ou qu’il soit synchronisé avec votre annuaire local, votre domaine géré peut être configuré rapidement.
* **Prise en charge de la jonction de domaine :** vous pouvez facilement joindre des ordinateurs au domaine dans le réseau virtuel Azure où votre domaine géré est disponible. La jonction de domaine sur les systèmes d’exploitation serveur et client Windows fonctionne en toute transparence par rapport aux domaines pris en charge par les services de domaine Azure AD. Vous pouvez également recourir aux outils de jonction de domaine automatisée par rapport à ces domaines.
* **Une instance de domaine par annuaire Azure AD :** vous pouvez créer un seul domaine Active Directory par annuaire Azure AD.
* **Création de domaines avec des noms personnalisés :** vous pouvez créer des domaines avec des noms personnalisés (par exemple, « contoso100.com ») à l’aide des services de domaine Azure AD. Vous pouvez utiliser des noms de domaine vérifiés ou non vérifiés. Si vous le souhaitez, vous pouvez également créer un domaine avec le suffixe de domaine intégré (c’est-à-dire, *.onmicrosoft.com) proposé par votre annuaire Azure AD.
* **Intégration à Azure AD :** vous n’avez pas besoin de configurer ou de gérer la réplication sur les services de domaine Azure AD. Les comptes d’utilisateur, les appartenances aux groupes et les informations d’identification (mots de passe) issus de votre annuaire Azure AD sont automatiquement disponibles dans les services de domaine Azure AD. Les nouveaux utilisateurs ou groupes ou les modifications apportées aux attributs dans votre client Azure AD ou dans votre annuaire local sont automatiquement synchronisés avec les services de domaine Azure AD.
* **Authentification NTLM et Kerberos :** grâce à la prise en charge de l’authentification NTLM et Kerberos, vous pouvez déployer des applications qui reposent sur l’authentification intégrée de Windows.
* **Utilisation de vos informations d’identification/mots de passe d’entreprise :** les mots de passe des utilisateurs dans votre client Azure AD fonctionnent avec les services de domaine Azure AD. Les utilisateurs peuvent utiliser leurs informations d’identification d’entreprise pour joindre des machines à des domaines, se connecter de manière interactive ou via le bureau à distance et s’authentifier sur le domaine géré.
* **Prise en charge des liaisons LDAP et des lectures LDAP :** vous pouvez utiliser des applications qui reposent sur les liaisons LDAP pour authentifier les utilisateurs dans des domaines pris en charge par les services de domaine Azure AD. En outre, les applications qui utilisent des opérations de lecture LDAP pour interroger les attributs des utilisateurs ou ordinateurs à partir de l’annuaire peuvent également fonctionner auprès des services de domaine Azure AD.
* **LDAP sécurisé (LDAPS) :** vous pouvez activer l’accès au répertoire via LDAP sécurisé (LDAPS). L’accès LDAP sécurisé est disponible au sein du réseau virtuel par défaut. Toutefois, vous pouvez activer également l’accès LDAP sécurisé via internet.
* **Stratégie de groupe :** vous pouvez utiliser un seul objet de stratégie de groupe intégré pour les conteneurs des utilisateurs et des ordinateurs afin d’imposer la conformité aux stratégies de sécurité nécessaires pour les comptes d’utilisateur et aux ordinateurs joints au domaine. Vous pouvez également créer vos propres objets de stratégie de groupe personnalisés et les affecter à des unités d’organisation personnalisées pour [gérer la stratégie de groupe](active-directory-ds-admin-guide-administer-group-policy.md).
* **Gérer les DNS :** les membres du groupe AAD DC Administrators peuvent gérer les DNS de votre domaine géré à l’aide d'outils d’administration DNS courants comme le composant logiciel enfichable DNS Administration MMC.
* **Créer des unités organisationnelles (UO) personnalisées :** les membres du groupe AAD DC Administrators peuvent créer des unités organisationnelles personnalisées au sein du domaine géré. Ces utilisateurs bénéficient de privilèges administratifs complets sur les unités organisationnelles personnalisées et peuvent donc ajouter/supprimer des comptes de service, des ordinateurs, des groupes, etc. au sein de ces unités organisationnelles personnalisées.
* **Disponibilité dans plusieurs régions Azure** : consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles.
* **Haute disponibilité :** les services de domaine Azure AD offrent une haute disponibilité pour votre domaine. Vous bénéficiez ainsi d’une disponibilité de service et d’une tolérance aux pannes plus élevées. La surveillance intégrée de l’intégrité permet de corriger automatiquement les défaillances en configurant de nouvelles instances pour remplacer les instances défectueuses et fournir un service continu pour votre domaine.
* **Utilisation d’outils de gestion familiers :** vous pouvez utiliser les outils de gestion Windows Server Active Directory familiers tels que le Centre d’administration Active Directory ou Active Directory PowerShell pour administrer des domaines gérés.
