---
title: "Bonnes pratiques en matière d’authentification MFA | Microsoft Docs"
description: "Ce document propose de meilleures pratiques à l’aide de l’authentification Multifacteur Azure avec des comptes Azure"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 465b6cac8ac04b48d27890893c1a4459e652dc28
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Meilleures pratiques pour l’utilisation de l’authentification multifacteur Azure avec des comptes Azure AD

La vérification en deux étapes est le choix privilégié par la plupart des organisations qui souhaitent améliorer leur processus d’authentification. Azure Multi-Factor Authentication (MFA) aide les entreprises à satisfaire les exigences des sociétés en matière de sécurité et de conformité tout en fournissant une expérience de connexion facile pour leurs utilisateurs. Cet article propose quelques conseils pour la planification de l’adoption de l’authentification Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Déployer Azure MFA dans le cloud

Il existe deux façons d’activer l’authentification Azure MFA pour tous vos utilisateurs.

* Acheter des licences pour chaque utilisateur (Azure MFA, Azure AD Premium ou Enterprise Mobility + Security)
* Créer un fournisseur d’authentification multifacteur et payer par utilisateur ou par authentification

### <a name="licenses"></a>Licences
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Si vous avez des licences Azure AD Premium ou Enterprise Mobility + Security, vous disposez déjà de l’authentification MFA. Votre organisation n’a pas besoin d’étendre la fonctionnalité de vérification en deux étapes à tous les utilisateurs. Vous devez uniquement attribuer une licence à un utilisateur. Ensuite, vous pouvez activer l’authentification MFA.

Durant la configuration de Multi-Factor Authentication, prenez en compte les conseils suivants :

* Ne créez pas de fournisseur d’authentification multifacteur par authentification. Si vous procédez ainsi, vous êtes susceptible de payer pour des requêtes de vérification d’utilisateurs qui ont déjà des licences.
* Si vous n’avez pas suffisamment de licences pour tous vos utilisateurs, vous pouvez créer un fournisseur d’authentification multifacteur par utilisateur pour couvrir le reste de votre organisation. 
* Azure AD Connect est requis uniquement si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD. Si vous utilisez un annuaire Azure AD non synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin d’Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification multi facteurs
![Fournisseur d’authentification multi facteurs](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si vous n’avez pas de licences qui incluent Azure MFA, vous pouvez créer un fournisseur d’authentification MFA. 

Lorsque vous créez le fournisseur d’authentification, vous devez sélectionner un annuaire et prendre en compte les détails suivants :

* Vous n’avez pas besoin d’un annuaire Azure AD pour créer un fournisseur d’authentification MFA, mais il vous permet de bénéficier de davantage de fonctionnalités. Les fonctionnalités suivantes sont activées lorsque vous associez le fournisseur d’authentification à un annuaire Azure AD :  
  * Étendre la vérification en deux étapes à tous les utilisateurs  
  * Offrir à vos administrateurs généraux des fonctionnalités supplémentaires, telles que le portail de gestion, les messages de bienvenue personnalisés et les rapports
* Si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD, vous avez besoin de DirSync ou d’AAD Sync. Si vous utilisez un annuaire Azure AD qui n’est pas synchronisé avec une instance locale d’Active Directory, DirSync ou AAD Sync est inutile.
* Choisissez le modèle de consommation qui correspond le mieux à votre entreprise. Une fois le modèle d’utilisation sélectionné, vous ne pourrez plus en changer. Les deux modèles sont les suivants :
  * Par authentification : vous payez pour chaque vérification. Utilisez ce modèle si vous souhaitez la vérification en deux étapes pour toute personne qui accède à une certaine application, et non pour des utilisateurs spécifiques.
  * Par utilisateur activé : vous payez pour chaque utilisateur que vous activez pour Azure MFA. Utilisez ce modèle si une partie seulement des utilisateurs possède des licences Azure AD Premium Enterprise Mobility Suite.

### <a name="supportability"></a>Prise en charge
Dans la mesure où la plupart des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre société sensibilise l’ensemble des utilisateurs à ce processus. Le fait de connaître le processus peut réduire la probabilité que les utilisateurs appellent votre support technique pour des problèmes mineurs liés à l’authentification MFA. Toutefois, pour certains scénarios, il est nécessaire de désactiver provisoirement l’authentification Multifacteur. Suivez les indications suivantes pour comprendre comment gérer ces scénarios :

* Formez votre personnel de support technique à la gestion de scénarios dans lesquels l’utilisateur se trouve dans l’impossibilité de se connecter, car l’application mobile ou le téléphone ne reçoit pas de notification ou ne passe pas d’appel téléphonique. Le support technique peut [activer un contournement à usage unique](multi-factor-authentication-whats-next.md#one-time-bypass) qui permet à un utilisateur de s’authentifier une seule fois en « contournant » la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié.
* Considérez la [fonction des adresses IP approuvées](multi-factor-authentication-whats-next.md#trusted-ips) dans Azure MFA comme un moyen de limiter la vérification en deux étapes. Cette fonction permet aux administrateurs d’un locataire géré ou fédéré de contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. Les fonctionnalités sont disponibles pour les locataires Azure AD titulaires d’une licence Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Meilleures pratiques pour un déploiement local
Si votre entreprise a décidé de se servir de sa propre infrastructure pour activer l’authentification MFA, vous devez déployer un serveur Azure Multi-Factor Authentication localement. Les composants serveur de l’authentification MFA figurent dans le diagramme suivant :

![Composants du serveur MFA par défaut : console, moteur de synchronisation, portail de gestion, service cloud](./media/multi-factor-authentication-security-best-practices/server.png)\*Non installé par défaut \**Installé, mais non activé par défaut

Le serveur Microsoft Azure Multi-Factor Authentication peut sécuriser les ressources de cloud et les ressources locales par fédération. Vous devez disposer d’AD FS et le fédérer avec votre locataire Azure AD.
Durant la configuration du serveur Multi-Factor Authentication, tenez compte des détails suivants :

* Si vous sécurisez des ressources Azure AD avec services de fédération Active Directory (AD FS), la première étape de vérification est effectuée localement avec AD FS. La seconde étape est effectuée localement en répondant à la demande.
* Il n’est pas nécessaire d’installer le serveur Azure Multi-Factor Authentication sur votre serveur de fédération AD FS. Toutefois, l’adaptateur Multi-Factor Authentication pour AD FS doit être installé sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu’il s’agit d’une version prise en charge, et installer l’adaptateur AD FS séparément sur votre serveur de fédération AD FS. 
* L’assistant d’installation de l’adaptateur AD FS Multi-Factor Authentication crée un groupe de sécurité appelé PhoneFactor Admins dans votre annuaire Active Directory, puis ajoute votre compte de service AD FS à ce groupe. Vérifiez que le groupe PhoneFactor Admins a été créé sur votre contrôleur de domaine et que le compte de service AD FS est membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

### <a name="user-portal"></a>Portail de l'utilisateur
Le portail de l’utilisateur offre des fonctionnalités libre-service et fournit un ensemble complet de fonctionnalités d’administration utilisateur. Il s’exécute sur un site web IIS (Internet Information Server). Utilisez les instructions suivantes pour configurer ce composant :

* Utiliser IIS 6 ou version ultérieure
* Installer et inscrire ASP.NET v2.0.507207
* Veiller à ce que ce serveur puisse être déployé dans un réseau de périmètre

### <a name="app-passwords"></a>Mots de passe d'application
Si votre organisation est fédérée (SSO) avec Azure AD et si vous vous apprêtez à utiliser l’authentification Azure MFA, voici ce que vous devez absolument savoir :

* Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez le mot de passe d’application.
* Les mots de passe des utilisateurs fédérés (SSO) sont stockés dans l’ID d’organisation. Si l’utilisateur quitte l’entreprise, ces informations doivent être stockées dans l’ID d’organisation à l’aide de DirSync. La désactivation/suppression de compte peut mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation/suppression des mots de passe dans Azure AD.
* Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
* Aucune authentification locale de journalisation/fonctionnalité d’audit n’est disponible pour les mots de passe d’application.
* Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de noms d’utilisateur, de mots de passe et de mots de passe d’application durant l’utilisation de la vérification en deux étapes avec les clients, selon l’emplacement où ils s’authentifient. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.
* Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Si vous avez besoin de permettre aux utilisateurs de créer des mots de passe d’application, sélectionnez l’option **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

## <a name="additional-considerations"></a>Considérations supplémentaires
Utilisez cette liste afin de connaître certaines considérations supplémentaires et obtenir des conseils pour chaque composant qui sera déployé en local :

- Configurez Azure Multi-Factor Authentication avec les [services AD FS (Active Directory Federation Services)](multi-factor-authentication-get-started-adfs.md).
- Installez et configurez le serveur Azure MFA avec [l’authentification RADIUS](multi-factor-authentication-get-started-server-radius.md).
- Installez et configurez le serveur Azure MFA avec [l’authentification IIS](multi-factor-authentication-get-started-server-iis.md).
- Installez et configurez le serveur Azure MFA avec [l’authentification Windows](multi-factor-authentication-get-started-server-windows.md).
- Installez et configurez le serveur Azure MFA avec [l’authentification LDAP](multi-factor-authentication-get-started-server-ldap.md).
- Installez et configurez le serveur Azure MFA avec la [passerelle des services Bureau à distance et serveur Azure Multi-Factor Authentication avec RADIUS](multi-factor-authentication-get-started-server-rdg.md).
- Installez et configurez la synchronisation entre le serveur Azure MFA et [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md).
- [Déployez le service web de l’application mobile du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).
- [Configuration de VPN avancée avec Azure Multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) pour les équipements VPN Cisco ASA, Citrix Netscaler et Juniper/Pulse Secure à l’aide de LDAP ou RADIUS.

## <a name="next-steps"></a>Étapes suivantes
Bien que cet article mette en évidence quelques-unes des meilleures pratiques de l’authentification Multifacteur Azure, il existe d’autres ressources que vous pouvez également utiliser au moment de la planification de votre déploiement de l’authentification Multifacteur. La liste ci-dessous présente certains articles clés qui peuvent vous aider lors de ce processus<:

* [Rapports dans Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
* [Processus d’inscription à la vérification en deux étapes](multi-factor-authentication-end-user-first-time.md)
* [Forum Aux Questions d’Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

