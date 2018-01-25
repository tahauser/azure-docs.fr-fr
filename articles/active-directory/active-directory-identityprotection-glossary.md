---
title: "Glossaire d’Azure Active Directory Identity Protection | Microsoft Docs"
description: "Glossaire d’Azure Active Directory Identity Protection"
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité, glossaire"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 30cf3911d0f22e2d9351fc606cd6697ef437e452
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossaire d’Azure Active Directory Identity Protection
### <a name="at-risk-user"></a>À risque (utilisateur)
Utilisateur associé à un ou plusieurs événements à risque actifs. 

### <a name="atypical-sign-in-location"></a>Emplacement de connexion atypique
Connexion effectuée depuis une zone géographique inhabituelle pour l’utilisateur spécifique, pour des utilisateurs similaires ou pour le client.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Module de sécurité d’Azure Active Directory offrant une vue consolidée des événements à risque et des vulnérabilités potentielles qui affectent les identités d’une organisation.

### <a name="conditional-access"></a>Accès conditionnel
Stratégie de sécurisation des accès aux ressources. Les règles d’accès conditionnel sont stockées dans Azure Active Directory et sont évaluées par Azure AD avant tout octroi d’accès à la ressource.  Il existe, par exemple, des règles de restriction d’accès en fonction de l’emplacement de l’utilisateur, de l’intégrité de l’appareil ou de la méthode d’authentification de l’utilisateur.

### <a name="credentials"></a>Informations d'identification
Informations contenant à la fois une identification et une preuve d’identification et qui permettent l’accès aux ressources locales et réseau. Les noms d’utilisateur et mots de passe, les cartes à puce et les certificats sont des exemples d’informations d’identification.

### <a name="event"></a>Événement
Enregistrement d’une activité dans Azure Active Directory.

### <a name="false-positive-risk-event"></a>Faux positif (événement à risque)
État d’un événement à risque défini manuellement par un utilisateur du service Identity Protection, indiquant que l’événement à risque a été examiné et marqué à tort comme un événement à risque.

### <a name="identity"></a>Identité
Personne ou entité devant faire l’objet d’une vérification au moyen d’une authentification basée sur certains critères, tels qu’un mot de passe ou un certificat.

### <a name="identity-risk-event"></a>Événement de risque d’identité
Événement AAD ayant été marqué comme anormal par le service Identity Protection et qui peut impliquer la corruption d’une identité.

### <a name="ignored-risk-event"></a>Ignoré (événement à risque)
État d’un événement à risque défini manuellement par un utilisateur du service Identity Protection et qui indique que l’événement à risque a été clos sans mise en place d’une action corrective.

### <a name="impossible-travel-from-atypical-locations"></a>Déplacement impossible à partir d’emplacements inhabituels
Événement à risque déclenché lorsque le service détecte deux connexions du même utilisateur, dont au moins une effectuée depuis un emplacement inhabituel, et pour lesquelles le délai entre les connexions est inférieur au temps minimum nécessaire pour se déplacer physiquement entre ces emplacements.  

### <a name="investigation"></a>Investigation
Processus consistant à vérifier les activités, les journaux et les autres informations pertinentes concernant un événement à risque, dans le but de déterminer la nécessité d’appliquer des mesures de correction ou d’atténuation, de comprendre si l’identité a été corrompue et de quelle manière, et d’identifier la manière dont l’identité corrompue a été exploitée.

### <a name="leaked-credentials"></a>Informations d’identification divulguées
Événement à risque déclenché lorsque nos chercheurs découvrent que les informations d’identification de l’utilisateur actuel (nom d’utilisateur et mot de passe) sont publiées dans le web invisible.

### <a name="mitigation"></a>Atténuation
Action visant à limiter ou éliminer la probabilité qu’un pirate exploite une identité ou un appareil corrompu, mais qui ne permet pas de rétablir la sécurité de l’identité ou de l’appareil en question. Une atténuation ne résout pas les précédents événements à risque associés à l’identité ou à l’appareil.

### <a name="multi-factor-authentication"></a>Authentification multifacteur
Méthode d’authentification imposant l’utilisation d’au moins deux modes d’authentification, par exemple un objet en la possession de l’utilisateur (certificat), une information connue de l’utilisateur (noms d’utilisateur, mots de passe ou phrases secrètes), des attributs physiques (empreinte numérique) ou encore des attributs personnels (signature personnelle).

### <a name="offline-detection"></a>Détection en mode hors connexion
Détection d’anomalies et évaluation du risque d’un événement, tel qu’une tentative de connexion consécutive à l’événement, concernant un événement qui a déjà eu lieu.

### <a name="policy-condition"></a>Condition de stratégie
Partie d’une stratégie de sécurité qui définit les entités (groupes, utilisateurs, applications, plateformes d’appareils, états d’appareils, plages d’adresses IP, types de client) incluses dans la stratégie ou qui en sont exclues.

### <a name="policy-rule"></a>Règle de stratégie
Partie d’une stratégie de sécurité qui décrit les circonstances entraînant le déclenchement de la stratégie ainsi que les actions exécutées lors du déclenchement de la stratégie.

### <a name="prevention"></a>Prévention
Action visant à protéger l’organisation des dommages liés à l’utilisation abusive d’une identité ou d’un appareil potentiellement ou effectivement corrompu. Une action de prévention ne garantit nullement la sécurité de l’appareil ou de l’identité, de même qu’elle n’a aucun effet sur les événements à risque passés.

### <a name="privileged-user"></a>Privilégié (utilisateur)
Utilisateur qui, au moment d’un événement à risque, possédait des autorisations d’administration permanentes ou temporaires sur une ou plusieurs ressources dans Azure Active Directory. Il peut s’agir, par exemple, d’un administrateur global, d’un administrateur de facturation, d’un administrateur de service, d’un administrateur d’utilisateurs ou encore d’un administrateur de mots de passe. 

### <a name="real-time"></a>Temps réel
Voir « Détection en temps réel ».

### <a name="real-time-detection"></a>Détection en temps réel
Détection d’anomalies et évaluation du risque d’un événement, tel qu’une tentative de connexion, effectuée avant d’autoriser la poursuite de la progression d’un événement.

### <a name="remediated-risk-event"></a>Corrigé (événement à risque)
État d’un événement à risque défini automatiquement par le service Identity Protection et qui indique que l’événement à risque a été résolu grâce à la mise en place d’une action corrective standard pour ce type d’événement à risque. Par exemple, lors de la réinitialisation du mot de passe de l’utilisateur, de nombreux événements à risque indiquant une corruption de l’ancien mot de passe seront résolus automatiquement.

### <a name="remediation"></a>Correction
Action visant à sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement corrompu. Une mesure de correction permet de rétablir la sécurité de l’identité ou de l’appareil et de résoudre les anciens événements à risque associés à l’identité ou à l’appareil.

### <a name="resolved-risk-event"></a>Résolu (événement à risque)
État d’un événement à risque défini manuellement par un utilisateur du service Identity Protection qui indique que l’utilisateur a exécuté une action corrective appropriée en dehors d’Identity Protection et que l’événement à risque doit être considéré comme clos.

### <a name="risk-event-status"></a>État d’un événement à risque
Propriété d’un événement à risque qui indique si l’événement est actif ou, s’il est clos, la raison de cet état.

### <a name="risk-event-type"></a>Type d’événement à risque
Catégorie de l’événement à risque indiquant le type d’anomalie à l’origine de l’événement à risque.

### <a name="risk-level-risk-event"></a>Niveau de risque (événement à risque)
Indication (élevée, moyenne ou faible) de la gravité de l’événement à risque, visant à aider les utilisateurs d’Identity Protection à hiérarchiser les actions à entreprendre afin de réduire le risque auquel s’expose leur organisation. 

### <a name="risk-level-sign-in"></a>Niveau de risque (connexion)
Indication (élevée, moyenne ou faible) indiquant la probabilité qu’un tiers tente d’utiliser l’identité de l’utilisateur dans le cadre d’une connexion spécifique.

### <a name="risk-level-user-compromise"></a>Niveau de risque (compromission de l’utilisateur)
Indication (élevée, moyenne ou faible) de la probabilité de corruption d’une identité.

### <a name="risk-level-vulnerability"></a>Niveau de risque (vulnérabilité)
Indication (élevée, moyenne ou faible) de la gravité de la vulnérabilité, visant à aider les utilisateurs d’Identity Protection à hiérarchiser les actions à entreprendre afin de réduire le risque auquel s’expose leur organisation.

### <a name="secure-identity"></a>Sécurisé (identité)
Application d’une mesure corrective (par exemple, changement de mot de passe ou réimageage d’un ordinateur) dans le but de rétablir la sécurité d’une identité potentiellement corrompue.

### <a name="security-policy"></a>Stratégie de sécurité
Ensemble de règles et conditions de stratégie. Une stratégie peut être appliquée à des entités, telles que des utilisateurs, groupes, applications, appareils, plateformes d’appareils, états d’appareils, plages d’adresses IP et types de clients Auth2.0. Lorsqu’une stratégie est activée, elle est évaluée chaque fois qu’un jeton est généré pour une ressource sur une entité incluse dans la stratégie.

### <a name="sign-in-v"></a>Se connecter (v)
Processus d’authentification d’une identité dans Azure Active Directory.

### <a name="sign-in-n"></a>Connexion (n)
Processus ou action d’authentification d’une identité dans Azure Active Directory et événement qui capture cette opération.

### <a name="sign-in-from-anonymous-ip-address"></a>Connexion à partir d’une adresse IP anonyme
Événement à risque déclenché après une réussite de connexion à partir d’une adresse IP ayant été identifiée comme adresse IP proxy anonyme.

### <a name="sign-in-from-infected-device"></a>Connexion à partir d’un appareil infecté
Événement à risque déclenché lors d’une connexion provenant d’une adresse IP utilisée par un ou plusieurs appareils corrompus qui tentent activement de communiquer avec un serveur robot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Connexion à partir d’une adresse IP affichant une activité suspecte
Événement à risque déclenché après une réussite de connexion à partir d’une adresse IP associée à un grand nombre de tentatives de connexion effectuées pendant une courte période sur plusieurs comptes d’utilisateur.

### <a name="sign-in-from-unfamiliar-location"></a>Connexion à partir d’un emplacement inconnu
Événement à risque déclenché lorsqu’un utilisateur parvient à se connecter depuis un nouvel emplacement (adresse IP, latitude/longitude et ASN).

### <a name="sign-in-risk"></a>Risque à la connexion
Voir « Niveau de risque (connexion) »

### <a name="sign-in-risk-policy"></a>Stratégie en matière de risque à la connexion
Stratégie d’accès conditionnel consistant à évaluer le risque associé à une connexion spécifique et qui applique des mesures d’atténuation à partir de règles et de conditions prédéfinies.

### <a name="user-compromise-risk"></a>Risque de compromission de l’utilisateur
Voir « Niveau de risque (compromission de l’utilisateur) »

### <a name="user-risk"></a>Risque de l’utilisateur
Voir « Niveau de risque (compromission de l’utilisateur) ».

### <a name="user-risk-policy"></a>Stratégie de risque d’utilisateur
Stratégie d’accès conditionnel consistant à évaluer la connexion et qui applique des mesures d’atténuation à partir de règles et de conditions prédéfinies.

### <a name="users-flagged-for-risk"></a>Utilisateurs associés à un indicateur de risque
Utilisateurs associés à des événements à risque actifs ou corrigés

### <a name="vulnerability"></a>Vulnérabilité
Configuration ou condition dans Azure Active Directory qui rend l’annuaire vulnérable aux attaques ou aux menaces.

## <a name="see-also"></a>Voir aussi
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

