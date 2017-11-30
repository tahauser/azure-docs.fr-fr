---
title: "Plan de traitement des paiements Azure - Conditions relatives à l’identité"
description: Condition 8 de la norme PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Conditions relatives à l’identité pour les environnements conformes à la norme PCI DSS 
## <a name="pci-dss-requirement-8"></a>Condition 8 de la norme PCI DSS

**Identifier et authentifier l’accès aux composants du système**

> [!NOTE]
> Ces conditions ont été définies par le [Conseil des normes de sécurité PCI (Payment Card Industry)](https://www.pcisecuritystandards.org/pci_security/) dans le cadre de la [norme PCI DSS (PCI Data Security Standard) version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Pour obtenir des informations sur les procédures de test et des instructions pour chacune des conditions, reportez-vous à la documentation de la norme PCI DSS.

Affecter une identification (ID) unique à chaque personne bénéficiant d’un accès garantit que chaque personne est responsable de ses actions. Une fois la responsabilité établie, les actions effectuées sur les systèmes et les données critiques sont effectuées par des processus et des utilisateurs connus et autorisés.
L’efficacité d’un mot de passe est en grande partie déterminée par la conception et l’implémentation du système d’authentification ; en particulier, le nombre de tentatives de saisie de mot de passe autorisées et les méthodes de sécurité mises en place pour protéger les mots de passe utilisateur au point d’entrée, lors de leur transmission et dans le stockage.

> [!NOTE]
> Ces exigences s’appliquent à tous les comptes, y compris les comptes de point de vente, avec des fonctionnalités d’administration, et tous les comptes utilisés pour afficher ou accéder aux données des titulaires de cartes ou accéder aux systèmes avec les données des titulaires de cartes. Cela inclut les comptes utilisés par les fournisseurs et autres tiers (par exemple, pour le dépannage ou la maintenance). Ces exigences ne s’appliquent pas aux comptes utilisés par les consommateurs (les titulaires de carte, par exemple). Toutefois, les exigences 8.1.1, 8.2, 8.5, 8.2.3 à 8.2.5 et 8.1.6 à 8.1.8 ne sont pas destinées à s’appliquer aux comptes d’utilisateurs au sein d’une application de paiement pour point de vente ayant uniquement accès à un numéro de carte à la fois afin de faciliter une transaction unique (comptes de caisse, par exemple).
 
## <a name="pci-dss-requirement-81"></a>Condition 8.1 de la norme PCI DSS

**8.1** Définir et mettre en œuvre des stratégies et procédures pour assurer une gestion efficace de l’identification des utilisateurs pour les utilisateurs non consommateurs et les administrateurs sur tous les composants du système comme suit.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso présente un cas d’usage et décrit comment bien utiliser les administrateurs pour l’exemple de déploiement.|



### <a name="pci-dss-requirement-811"></a>Condition 8.1.1 de la norme PCI DSS

**8.1.1** Affecter à tous les utilisateurs un ID unique avant de les autoriser à accéder aux composants du système ou aux données des titulaires de cartes.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso implémente Azure Active Directory et le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory pour garantir que tous les utilisateurs disposent d’un ID unique. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Condition 8.1.2 de la norme PCI DSS

**8.1.2** Contrôler l’ajout, la suppression et la modification des ID utilisateur, des informations d’identification et des autres objets d’identification.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso implémente Azure Active Directory et le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory pour garantir que tous les utilisateurs disposent d’un ID unique. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Condition 8.1.3 de la norme PCI DSS

**8.1.3** Révoquer immédiatement l’accès des utilisateurs résiliés.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso utilise Azure Active Directory pour la gestion des utilisateurs. La révocation des utilisateurs peut être effectuée dans Active Directory.|



### <a name="pci-dss-requirement-814"></a>Condition 8.1.4 de la norme PCI DSS

**8.1.4** Supprimer ou désactiver des comptes utilisateur inactifs dans les 90 jours.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso utilise Azure Active Directory pour la gestion des utilisateurs. L’option `-enableADDomainPasswordPolicy` peut être définie de manière à assurer l’expiration des mots de passe dans 90 jours.|



### <a name="pci-dss-requirement-815"></a>Condition 8.1.5 de la norme PCI DSS

**8.1.5** Gérer les identifiants utilisés par des tiers pour accéder à, prendre en charge ou assurer la maintenance des composants du système via l’accès à distance comme suit :
- Activé uniquement pendant le temps nécessaire et désactivé en cas de non-utilisation.
- Surveillé lorsqu’il est utilisé.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a adopté des stratégies de sécurité d’entreprise, notamment une stratégie sur la sécurité des informations. Ces stratégies ont été approuvées, publiées et communiquées à Microsoft Azure. La stratégie de sécurité des informations exige que l’accès aux ressources Microsoft Azure ne soit accordé qu’en présence d’une justification professionnelle et de l’autorisation préalable du propriétaire des ressources, et selon les principes du « besoin de connaître » et des « privilèges minimum ». De plus, la stratégie définit également les exigences du cycle de gestion des accès, notamment la configuration de l’accès, l’authentification, l’autorisation de l’accès, la suppression des droits d’accès et l’examen périodique des accès. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | La démonstration du Webstore Contoso a implémenté Azure Active Directory et le contrôle d’accès en fonction du rôle Azure Active Directory pour gérer l’accès des utilisateurs à l’installation. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Condition 8.1.6 de la norme PCI DSS

**8.1.6** Limiter les tentatives d’accès répétées en verrouillant l’identifiant utilisateur après six tentatives maximum.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso a implémenté une séparation claire des responsabilités pour tous les utilisateurs de la démonstration. Pour plus d’informations, consultez Azure Active Directory Identity Protection dans la section [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>Condition 8.1.7 de la norme PCI DSS

**8.1.7** Définir la durée du verrouillage sur un minimum de 30 minutes ou jusqu'à ce qu’un administrateur active l’identifiant utilisateur.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Les clients sont chargés de créer, faire appliquer et surveiller une stratégie de mot de passe conforme aux exigences de la norme PCI DSS.|



### <a name="pci-dss-requirement-818"></a>Condition 8.1.8 de la norme PCI DSS

**8.1.8** Si une session a été inactive pendant plus de 15 minutes, demander que l’utilisateur s’authentifie à nouveau pour réactiver le terminal ou la session.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Les clients sont chargés de créer, faire appliquer et surveiller une stratégie de mot de passe conforme aux exigences de la norme PCI DSS.|



## <a name="pci-dss-requirement-82"></a>Condition 8.2 de la norme PCI DSS

**8.2** Outre l’affectation d’un identifiant unique, assurer la bonne gestion de l’authentification des utilisateurs pour les utilisateurs non consommateurs et les administrateurs sur tous les composants du système en utilisant au moins l’une des méthodes suivantes pour authentifier tous les utilisateurs :
- Un élément connu, comme un mot de passe ou une phrase secrète
- Un élément physique, comme un appareil à jeton ou une carte à puce
- Un élément vous concernant particulièrement, comme la biométrie

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | L’implémentation du Webstore Contoso pour l’authentification multifacteur a été désactivée pour faciliter la démonstration. L’authentification multifacteur peut être implémentée à l’aide d’[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Condition 8.2.1 de la norme PCI DSS

**8.2.1** À l’aide d’un chiffrement fort, rendre toutes les informations d’identification (par exemple, les mots de passe ou phrases secrètes) illisibles pendant la transmission et le stockage sur l’ensemble des composants du système.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a établi des procédures de gestion des clés pour gérer les clés de chiffrement tout au long de leur cycle de vie (par exemple, la génération, la distribution et la révocation). Microsoft Azure utilise l’infrastructure de clé publique d’entreprise de Microsoft. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso met en œuvre des mots de passe forts, décrits dans le guide de déploiement. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Condition 8.2.2 de la norme PCI DSS

**8.2.2** Vérifier l’identité de l’utilisateur avant de modifier les informations d’identification ; par exemple, effectuer des réinitialisations de mot de passe, provisionner de nouveaux jetons ou générer de nouvelles clés.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Microsoft Azure a établi des procédures de gestion des clés pour gérer les clés de chiffrement tout au long de leur cycle de vie (par exemple, la génération, la distribution et la révocation). Microsoft Azure utilise l’infrastructure de clé publique d’entreprise de Microsoft. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso met en œuvre des mots de passe forts, décrits dans le guide de déploiement. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>Condition 8.2.3 de la norme PCI DSS

**8.2.3** Les phrases secrètes/mots de passe doivent respecter les conditions suivantes :
- Longueur minimale d’au moins sept caractères.
- Contenir des caractères alphabétiques et numériques.
Sinon, les mots de passe/phrases secrètes doivent présenter une complexité au moins équivalente aux paramètres spécifiés ci-dessus.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso met en œuvre des mots de passe forts, décrits dans le guide de déploiement.|



### <a name="pci-dss-requirement-824"></a>Condition 8.2.4 de la norme PCI DSS

**8.2.4** Modifier les mots de passe/phrases secrètes des utilisateurs au moins une fois tous les 90 jours.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso utilise Azure Active Directory pour la gestion des utilisateurs. L’option `-enableADDomainPasswordPolicy` peut être définie de manière à assurer l’expiration des mots de passe au moins une fois tous les 90 jours.|



### <a name="pci-dss-requirement-825"></a>Condition 8.2.5 de la norme PCI DSS

**8.2.5** Ne pas autoriser une personne à soumettre un nouveau mot de passe/phrase secrète identique à l’un des quatre derniers mots de passe/phrases secrètes utilisés.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso met en œuvre des mots de passe forts, décrits dans le guide de déploiement. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Condition 8.2.6 de la norme PCI DSS

**8.2.6** Définir les mots de passe/phrases secrètes pour la première utilisation et la réinitialisation sur une valeur unique pour chaque utilisateur et modifier immédiatement après la première utilisation.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso met en œuvre des mots de passe forts, décrits dans le guide de déploiement. Pour plus d’informations, consultez [Aide PCI - Gestion des identités](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Condition 8.3 de la norme PCI DSS

**8.3** Sécuriser tous les accès administratifs non-console et tous les accès à distance sur l’environnement de données de titulaires de carte (CDE) à l’aide de l’authentification multifacteur.

> [!NOTE]
> L’authentification multifacteur nécessite d’utiliser au moins deux des trois méthodes d’authentification (voir la condition 8.2 pour obtenir une description des méthodes d’authentification). Utiliser deux fois un facteur (par exemple, à l’aide de deux mots de passe distincts) n’est pas considéré comme une authentification multifacteur.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les administrateurs Azure sont tenus d’utiliser l’authentification multifacteur lorsqu’ils effectuent des tâches de maintenance et d’administration sur les systèmes et serveurs Azure. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). L’authentification multifacteur n’est pas implémentée pour la démonstration.|



### <a name="pci-dss-requirement-831"></a>Condition 8.3.1 de la norme PCI DSS

**8.3.1** Intégrer l’authentification multifacteur pour tous les accès non-console dans le CDE pour le personnel bénéficiant d’un accès administratif.

> [!NOTE]
> Cette condition sera considérée comme une bonne pratique jusqu’au 31 janvier 2018, après quoi elle deviendra obligatoire.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les administrateurs Azure sont tenus d’utiliser l’authentification multifacteur lorsqu’ils effectuent des tâches de maintenance et d’administration sur les systèmes et serveurs Azure. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). L’authentification multifacteur n’est pas implémentée pour la démonstration.|



### <a name="pci-dss-requirement-832"></a>Condition 8.3.2 de la norme PCI DSS

**8.3.2** Intégrer une authentification multifacteur pour tous les accès réseau à distance (utilisateur et administrateur, y compris l’accès de tiers pour le dépannage ou la maintenance) depuis un point extérieur au réseau de l’entité.


**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Les administrateurs Azure sont tenus d’utiliser l’authentification multifacteur lorsqu’ils effectuent des tâches de maintenance et d’administration sur les systèmes et serveurs Azure. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). L’authentification multifacteur n’est pas implémentée pour la démonstration.|



## <a name="pci-dss-requirement-84"></a>Condition 8.4 de la norme PCI DSS

**8.4** Documenter et communiquer les stratégies et les procédures d’authentification à tous les utilisateurs, notamment :
- Conseils pour choisir des informations d’identification fortes
- Conseils à l’attention des utilisateurs pour assurer la protection de leurs informations d’identification
- Instructions pour la non-réutilisation des mots de passe précédemment utilisés
- Instructions pour la modification des mots de passe en cas de risque de compromission

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Les clients sont tenus de respecter les instructions et de documenter et communiquer les procédures et stratégies d’authentification à l’ensemble des utilisateurs.|



## <a name="pci-dss-requirement-85"></a>Condition 8.5 de la norme PCI DSS

**8.5** Ne pas utiliser d’identifiants ou de mots de passe groupés, partagés ou génériques, ou d’autres méthodes d’authentification comme suit :
- Les identifiants utilisateur génériques sont désactivés ou supprimés.
- Les identifiants utilisateur partagés n’existent pas pour l’administration du système et d’autres fonctions stratégiques.
- Les identifiants utilisateur partagés et génériques ne sont pas utilisés pour administrer des composants du système.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). L’authentification multifacteur n’est pas implémentée pour la démonstration.|



### <a name="pci-dss-requirement-851"></a>Condition 8.5.1 de la norme PCI DSS

**8.5.1** **Condition supplémentaire pour les fournisseurs de services uniquement :** Les fournisseurs de service avec accès à distance au site du client (par exemple, pour le dépannage des systèmes ou serveurs des points de vente) doivent utiliser des informations d’identification uniques (mot de passe ou phrase secrète, par exemple) pour chaque client. 

> [!NOTE]
> Cette condition ne concerne pas les fournisseurs d’hébergement partagé qui accèdent à leur propre environnement d’hébergement, dans lequel de nombreux environnements de clients sont hébergés.

**Responsabilités :&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable aux clients de Microsoft Azure. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Non applicable aux clients de Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Condition 8.6 de la norme PCI DSS

**8.6** Lorsque d’autres mécanismes d’authentification sont utilisés (par exemple, des jetons de sécurité physique ou logique, des cartes à puce, des certificats, etc.), l’utilisation de ces mécanismes doit être assignée comme suit :
- Les mécanismes d’authentification doivent être assignés à un compte individuel et non partagées entre plusieurs comptes.
- Des contrôles physiques et/ou logiques doivent avoir été mis en place pour garantir que seul le compte concerné puisse utiliser ce mécanisme d’accès.

**Responsabilités :&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso crée trois comptes pendant le déploiement : admin, sqladmin et edna (pendant la démonstration, l’utilisateur par défaut se connectait à l’application web). L’authentification multifacteur n’est pas implémentée pour la démonstration. Tous les accès sont gérés par [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) qui permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. |



## <a name="pci-dss-requirement-87"></a>Condition 8.7 de la norme PCI DSS

**8.7** Tous les accès aux bases de données contenant des données de titulaires de cartes (y compris l’accès aux applications, administrateurs et tous les autres utilisateurs) sont limités comme suit :
- Tous les accès utilisateur aux bases de données, ainsi que les requêtes et les actions sur les bases de données, s’effectuent par le biais des méthodes de programmation.
- Seuls les administrateurs de base de données ont la possibilité d’accéder directement ou d’interroger des bases de données.
- Les identifiants des applications de base de données peuvent uniquement être utilisés par les applications (et non par des utilisateurs ou d’autres processus non applicatifs).

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Le Webstore Contoso protège toutes les données de titulaires de cartes avec Azure Key Vault et le chiffrement des enregistrements est décrit dans la documentation relative au déploiement. Pour plus d’informations, consultez [Aide PCI - Chiffrement](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Condition 8.8 de la norme PCI DSS

**8.8** Assurer que les stratégies de sécurité et les procédures opérationnelles pour l’identification et l’authentification sont documentées, utilisées et connues de toutes les parties concernées.

**Responsabilités :&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fournisseur<br />(Microsoft&nbsp;Azure)** | Non applicable. |
| **Client<br />(Plan&nbsp;PCI&#8209;DSS)** | Les clients sont tenus de s’assurer que les stratégies de sécurité et les procédures opérationnelles pour l’identification et l’authentification sont documentées, utilisées et connues de toutes les parties concernées.|




