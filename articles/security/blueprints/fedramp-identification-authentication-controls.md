---
title: "Solution Blueprint Sécurité et conformité Azure - Automatisation d’applications web FedRAMP - Identification et authentification"
description: "Automatisation d’applications web FedRAMP - Identification et authentification"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Identification et authentification (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 contrôle IA-1

> [!NOTE]
> Ces contrôles sont définis par le National Institute of Standards and Technology (NIST) et le ministère américain du commerce dans le cadre de la publication spéciale 800-53 révision 4 du service NIST. Pour plus d’informations sur les procédures de test et des instructions pour chaque contrôle, reportez-vous à la publication 800-53 rév. 4 du NIST.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Stratégie et procédures d’identification et d’authentification

**IA-1** L’organisation développe, documente et diffuse à [Affectation : personnel ou rôles définis par l’organisation] une stratégie d’identification et d’authentification qui traite l’objectif, l’étendue, les rôles, les responsabilités, l’engagement de gestion, la coordination au sein des entités organisationnelles et la conformité ; les procédures visant à faciliter l’implémentation de la stratégie d’identification et d’authentification et des contrôles associés ; et révise et met à jour la stratégie actuelle d’identification et d’authentification [Affectation : fréquence définie par l’organisation] ; et les procédures d’identification et d’authentification [Affectation : fréquence définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | La stratégie et les procédures d’identification et d’authentification de niveau entreprise du client peuvent être suffisantes pour traiter ce contrôle. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 contrôle IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identification et authentification (utilisateurs de l’organisation)

**IA-2** Le système d’information identifie et authentifie de manière unique les utilisateurs de l’organisation (ou les processus intervenant pour le compte des utilisateurs de l’organisation).

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les comptes créés par cette solution Blueprint possèdent des identificateurs uniques. Les comptes intégrés possédant des identificateurs non uniques sont désactivés ou supprimés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 contrôle IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes privilégiés

**IA-2 (1)** Le système d’information implémente l’authentification multifacteur pour l’accès réseau aux comptes privilégiés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’implémenter l’authentification multifacteur pour l’accès réseau aux comptes privilégiés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 contrôle IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes non privilégiés

**IA-2 (2)** Le système d’information implémente l’authentification multifacteur pour l’accès réseau aux comptes non privilégiés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’implémenter l’authentification multifacteur pour l’accès réseau aux comptes non privilégiés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 contrôle IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identification et authentification (utilisateurs de l’organisation) | Accès local aux comptes privilégiés

**IA-2 (3)** Le système d’information implémente l’authentification multifacteur pour l’accès local aux comptes privilégiés.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Le client ne dispose d’aucun accès local aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure n’autorise pas l’accès local, sauf si un accès physique est requis. L’accès d’administrateur local doit être utilisé uniquement à des fins de dépannage lorsque le serveur membre rencontre des problèmes de réseau et lorsque l’authentification de domaine ne fonctionne pas. <br /> Azure implémente l’authentification multifacteur pour l’accès local en s’aidant des mécanismes de contrôle d’accès requis pour l’accès physique à l’environnement. Les salles des centres de données Azure qui contiennent tous les systèmes d’infrastructure Azure, dans la limite du système, sont restreintes via différents mécanismes de sécurité physique, y compris la configuration requise pour l’accès au moyen de badges d’entreprise équipés de carte à puce et les appareils biométriques. Ces deux formes d’authentification sont nécessaires pour autoriser un accès physique au point d’entrée, dans les colocalisations de centres de données Azure. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 contrôle IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identification et authentification (utilisateurs de l’organisation) | Accès local aux comptes non privilégiés

**IA-2 (4)** Le système d’information implémente l’authentification multifacteur pour l’accès local aux comptes non privilégiés.

**Responsabilités :** `Azure Only`

|||
|---|---|
| **Client** | Le client ne dispose d’aucun accès local aux ressources système dans les centres de données Azure. |
| **Fournisseur (Microsoft Azure)** | Microsoft Azure considère comme privilégiés tous les comptes Microsoft Azure Government utilisés par le personnel de Microsoft Azure Government. L’authentification multifacteur est implémentée pour tous les comptes de personnel Microsoft Azure Government à l’aide de cartes à puce et de codes confidentiels, ce qui inclut l’accès local. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 contrôle IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identification et authentification (utilisateurs de l’organisation) | Authentification de groupe

**IA-2 (5)** Lorsqu’un authentificateur de groupe est utilisé, l’organisation demande aux personnes de s’authentifier au moyen d’un authentificateur individuel.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun compte partagé/de groupe n’est activé sur les ressources déployées par cette solution Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 contrôle IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes privilégiés - Protection contre les attaques par rejeu

**IA-2 (8)** Le système d’information implémente des mécanismes d’authentification capables d’offrir une protection contre les attaques par rejeu pour l’accès réseau aux comptes privilégiés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| Client | L’accès aux ressources déployées par cette solution Blueprint est protégé contre les attaques par relecture grâce à la fonctionnalité Kerberos intégrée d’Azure Active Directory, d’Active Directory et du système d’exploitation Windows. Dans l’authentification Kerberos, l’authentificateur envoyé par le client contient des données supplémentaires, comme une liste d’adresses IP chiffrées, les horodateurs client et la durée de vie de ticket. Si un paquet est relu, l’horodatage est vérifié. Si l’horodatage est antérieur à ou identique à celui d’un authentificateur précédent, le paquet est rejeté. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 contrôle IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identification et authentification (utilisateurs de l’organisation) | Accès réseau aux comptes non privilégiés - Protection contre les attaques par rejeu

**IA-2 (9)** Le système d’information implémente des mécanismes d’authentification capables d’offrir une protection contre les attaques par rejeu pour l’accès réseau aux comptes non privilégiés.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès aux ressources déployées par cette solution Blueprint est protégé contre les attaques par relecture grâce à la fonctionnalité Kerberos intégrée d’Azure Active Directory, d’Active Directory et du système d’exploitation Windows. Dans l’authentification Kerberos, l’authentificateur envoyé par le client contient des données supplémentaires, comme une liste d’adresses IP chiffrées, les horodateurs client et la durée de vie de ticket. Si un paquet est relu, l’horodatage est vérifié. Si l’horodatage est antérieur à ou identique à celui d’un authentificateur précédent, le paquet est rejeté. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 contrôle IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identification et authentification (utilisateurs de l’organisation) | Accès distant - Appareil distinct

**IA-2 (11)** Le système d’information implémente l’authentification multifacteur pour l’accès distant dans les comptes avec ou sans privilèges. En outre, l’un des facteurs est fourni par un appareil séparé du système et qui obtient l’accès. Cet appareil satisfait aux exigences définies [Affectation : niveau de sécurité du mécanisme défini par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’implémenter l’authentification multifacteur pour accéder aux ressources qu’il déploie à distance. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 contrôle IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identification et authentification (utilisateurs de l’organisation) | Acceptation des informations d’identification de la vérification d’identité personnelle

**IA-2 (12)** Le système d’information accepte et vérifie par voie électronique les informations d’identification de la vérification d’identité personnelle (PIV).

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’accepter et de vérifier les informations d’identification de la vérification d’identité personnelle (PIV). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 contrôle IA-3

#### <a name="device-identification-and-authentication"></a>Identification et authentification des appareils

**IA-3** Le système d’information identifie et authentifie de manière unique [Affectation : types d’appareils et/ou appareils spécifiques définis par l’organisation] avant d’établir une connexion [Sélection (une ou plusieurs réponses) : locale ; distante ; réseau].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’implémenter l’identification et l’authentification des appareils. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 contrôle IA-4.a

#### <a name="identifier-management"></a>Gestion de l’identificateur

**IA-4.a** L’organisation gère les identificateurs du système d’information. Elle reçoit pour cela une autorisation envoyée par [Affectation : personnel ou rôles définis par l’organisation] lui permettant d’affecter un individu, un groupe, un rôle ou un identificateur d’appareil.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de gérer les identificateurs (par exemple, les personnes, groupes, rôles et appareils) pour les ressources du client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 contrôle IA-4.b

#### <a name="identifier-management"></a>Gestion des identificateurs

**IA-4.b** L’organisation gère les identificateurs du système d’information en sélectionnant un identificateur qui identifie une personne, un groupe, un rôle ou un appareil.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Au cours du déploiement, cette solution Blueprint vous invite à entrer les identificateurs spécifiques du client pour les comptes individuels.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 contrôle IA-4.c

#### <a name="identifier-management"></a>Gestion des identificateurs

**IA-4.c** L’organisation gère les identificateurs du système d’information en affectant l’identificateur à la personne, au groupe, au rôle ou à l’appareil prévu.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de gérer les identificateurs (par exemple, les personnes, groupes, rôles et appareils) pour les ressources du client. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 contrôle IA-4.d

#### <a name="identifier-management"></a>Gestion des identificateurs

**IA-4.d** L’organisation gère les identificateurs du système d’information en empêchant la réutilisation des identificateurs pendant [Affectation : période de temps définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les comptes Active Directory et de système d’exploitation Windows locaux se voient affecter un identificateur de sécurité unique (SID). Les comptes Azure Active Directory se voient quant à eux affecter un identifiant d’objet global unique. Ces identifiants uniques ne peuvent pas être réutilisés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 contrôle IA-4.e

#### <a name="identifier-management"></a>Gestion des identificateurs

**IA-4.e** L’organisation gère les identificateurs du système d’information en désactivant l’identificateur après [Affectation : période d’inactivité définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint implémente une tâche planifiée demandant à Active Directory de désactiver automatiquement les comptes après 35 jours d’inactivité. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 contrôle IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Gestion des identificateurs | Identifier l’état utilisateur

**IA-4 (4)** L’organisation gère les identificateurs individuels en identifiant de manière unique chaque individu en tant que [Affectation : caractéristique définie par l’organisation et révélant un état individuel].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Azure Active Directory et Active Directory prennent en charge la désignation des sous-traitants, fournisseurs et autres types d’utilisateur à l’aide des conventions d’affectation de noms appliquées à leurs identificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 contrôle IA-5.a

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.a** L’organisation gère les authentificateurs du système d’information en vérifiant, dans le cadre de leur distribution initiale, l’identité de la personne, du groupe, du rôle ou de l’appareil recevant l’authentificateur.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de gérer les authentificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 contrôle IA-5.b

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.b** L’organisation gère les authentificateurs du système d’information en établissant un contenu d’authentification initial pour les authentificateurs qu’elle définit.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le contenu initial de l’authentificateur pour les comptes créés par cette solution Blueprint répond aux conditions définies dans le contrôle IA-5 (1), à partir du moment où elles sont validées lors de leur spécification par le client pendant le déploiement.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 contrôle IA-5.c

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.c** L’organisation gère les authentificateurs du système d’information en s’assurant que le niveau de sécurité du mécanisme est suffisant pour utiliser les authentificateurs comme prévu.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les authentificateurs utilisés par cette solution Blueprint répondent aux conditions de niveau de sécurité définies par FedRAMP. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 contrôle IA-5.d

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.d** L’organisation gère les authentificateurs du système d’information en établissant et en implémentant des procédures administratives pour leur distribution initiale, les authentificateurs perdus, compromis ou endommagés et la révocation des authentificateurs.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de gérer les authentificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 contrôle IA-5.e

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.e** L’organisation gère les authentificateurs du système d’information en modifiant le contenu par défaut des authentificateurs avant l’installation du système d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Les valeurs par défaut de tous les authentificateurs des composants de cette solution Blueprint ont été modifiées. Ils sont spécifiés par les clients pendant le déploiement de cette solution. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 contrôle IA-5.f

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.f** L’organisation gère les authentificateurs du système d’information en établissant des restrictions quant à la durée de vie minimale et maximale et des conditions de réutilisation.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de gérer les authentificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 contrôle IA-5.g

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.g** L’organisation gère les authentificateurs du système d’information en les modifiant ou en les actualisant [Affectation : période définie par l’organisation selon le type d’authentificateur].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe est établie puis configurée pour implémenter des restrictions de durée de vie de mot de passe (60 jours). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 contrôle IA-5.h

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.h** L’organisation gère les authentificateurs du système d’information en protégeant le contenu de l’authentificateur contre toute divulgation et modification non autorisée.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint implémente Key Vault pour protéger le contenu des authentificateurs contre toute divulgation et modification non autorisées. Les authentificateurs suivants sont stockés dans Key Vault : mot de passe Azure pour déployer le compte, mot de passe administrateur de la machine virtuelle, mot de passe de compte de service SQL Server. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 contrôle IA-5.i

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.i** L’organisation gère les authentificateurs du système d’information en exigeant des utilisateurs qu’ils adoptent des mesures de sécurité spécifiques visant à protéger les authentificateurs et en faisant en sorte que les appareils implémentent ces mêmes mesures.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint implémente Key Vault pour protéger le contenu des authentificateurs contre toute divulgation et modification non autorisées. Les authentificateurs suivants sont stockés dans Key Vault : mot de passe Azure pour déployer le compte, mot de passe administrateur de la machine virtuelle, mot de passe de compte de service SQL Server. Key Vault permet de chiffrer les clés et les secrets (par exemple, les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 contrôle IA-5.j

#### <a name="authenticator-management"></a>Gestion des authentificateurs

**IA-5.j** L’organisation gère les authentificateurs du système d’information en modifiant les authentificateurs des comptes de groupe ou de rôle, lorsque l’appartenance à ces comptes change.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucun compte partagé/de groupe n’est activé sur les ressources déployées par cette solution Blueprint. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 contrôle IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).a** Dans le cadre de l’authentification basée sur mot de passe, le système d’information applique au mot de passe une complexité minimale de [Affectation : exigences définies par l’organisation en termes de casse, de nombre de caractères, de combinaison de majuscules, minuscules, chiffres et caractères spéciaux, y compris la configuration minimale requise pour chaque type].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe est établie et configurée afin d’appliquer des exigences de complexité de mot de passe aux comptes locaux de machine virtuelle et aux comptes Active Directory.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 contrôle IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).b** Dans le cadre de l’authentification basée sur mot de passe, le système d’information applique au moins le nombre de caractères modifiés suivant lors de la création de nouveaux mots de passe : [Affectation : nombre défini par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser l’authentification basée sur mot de passe au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 contrôle IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).c** Dans le cadre de l’authentification basée sur mot de passe, le système d’information stocke et transmet uniquement les mots de passe protégés par chiffrement.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Azure Active Directory permet de garantir que tous les mots de passe sont protégés par chiffrement pendant leur stockage et leur transmission. Les mots de passe stockés par Active Directory et en local, sur des machines virtuelles Windows déployées, font l’objet d’un hachage automatique dans le cadre de la fonctionnalité de sécurité intégrée. Les sessions d’authentification du Bureau à distance sont sécurisées à l’aide du protocole TLS pour assurer la protection des authentificateurs lors de la transmission. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 contrôle IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).d** Dans le cadre de l’authentification basée sur mot de passe, le système d’information applique au mot de passe une durée de vie minimale et maximale de [Affectation : durée de vie minimale et maximale définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe est établie et configurée pour appliquer des restrictions sur les mots de passe avec une durée de vie minimale (1 jour) et maximale (60 jours) concernant les comptes locaux et Active Directory. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 contrôle IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).e** Dans le cadre de l’authentification basée sur mot de passe, le système d’information interdit la réutilisation du mot de passe pendant [Affectation : nombre défini par l’organisation] générations.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Azure Blueprint déploie un contrôleur de domaine auquel toutes les machines virtuelles déployées sont jointes. Une stratégie de groupe est établie et configurée pour appliquer des restrictions sur les conditions de réutilisation (24 mots de passe) concernant les comptes locaux et Active Directory. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 contrôle IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur mot de passe

**IA-5 (1).f** Dans le cadre de l’authentification basée sur mot de passe, le système d’information permet d’utiliser un mot de passe temporaire pour les ouvertures de session système, et il le modifie immédiatement en un mot de passe permanent.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Azure Active Directory est utilisé pour gérer le contrôle d’accès au système d’information. À chaque création de compte ou génération de mot de passe temporaire, Azure Active Directory est utilisé pour demander à l’utilisateur de modifier le mot de passe à la connexion suivante. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 contrôle IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur une infrastructure à clé publique

**IA-5 (2) .a** Dans le cadre de l’authentification basée sur une infrastructure à clé publique, le système d’information valide les certifications en construisant et en vérifiant un chemin d’accès de certification menant à une ancre d’approbation acceptée, y compris en vérifiant les informations sur l’état du certificat.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser l’authentification basée sur une infrastructure à clé publique au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 contrôle IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur une infrastructure à clé publique

**IA-5 (2).b** Dans le cadre de l’authentification basée sur une infrastructure à clé publique, le système d’information applique un accès autorisé à la clé privée correspondante.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser l’authentification basée sur une infrastructure à clé publique au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 contrôle IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur une infrastructure à clé publique

**IA-5 (2).c** Dans le cadre de l’authentification basée sur une infrastructure à clé publique, le système d’information mappe l’identité authentifiée au compte de la personne ou du groupe.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser l’authentification basée sur une infrastructure à clé publique au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 contrôle IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur une infrastructure à clé publique

**IA-5 (2).d** Dans le cadre de l’authentification basée sur une infrastructure à clé publique, le système d’information implémente un cache local des données de révocation pour prendre en charge la détection et la validation du chemin d’accès en cas d’incapacité à accéder aux informations de révocation via le réseau.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser l’authentification basée sur une infrastructure à clé publique au sein des ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 contrôle IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Gestion des authentificateurs | Inscription de tiers physiques ou de confiance

**IA-5 (3)** L’organisation exige que le processus d’inscription habilité à recevoir [Affectation : types d’authentificateurs et/ou authentificateurs spécifiquement définis par l’organisation] les traite [Sélection : en personne ; via un tiers de confiance] devant [Attribution : autorité d’inscription définie par l’organisation] avec l’autorisation de [Affectation : personnel ou rôle défini par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’inscrire les authentificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 contrôle IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Gestion des authentificateurs | Prise en charge automatisée du niveau de sécurité du mot de passe

**IA-5 (4)** L’organisation utilise des outils automatisés pour déterminer si les authentificateurs de mot de passe sont suffisamment sécurisés pour répondre aux conditions définies [Affectation : exigences définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| Client | Les comptes d’utilisateur déployés avec cette solution Blueprint sont les comptes AD et d’utilisateurs locaux. Ces deux éléments fournissent des mécanismes qui forcent la conformité avec les exigences de mot de passe établies pour permettre la création d’un mot de passe initial, ainsi que pendant la modification des mots de passe. Azure Active Directory est l’outil automatisé utilisé pour déterminer si le niveau de sécurité des authentificateurs de mot de passe est suffisamment fort pour satisfaire les restrictions de longueur, de complexité, de rotation et de durée de vie établies dans le contrôle IA-5 (1). Azure Active Directory permet de s’assurer que le niveau de sécurité de l’authentificateur de mot de passe au moment de la création répond à ces normes. Les mots de passe spécifiés par le client et utilisés pour déployer cette solution sont vérifiés pour répondre aux exigences de niveau de sécurité des mots de passe. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 contrôle IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Gestion des authentificateurs | Protection des authentificateurs

**IA-5 (6)** L’organisation protège les authentificateurs d’après la catégorie de sécurité des informations à laquelle l’utilisation de l’authentificateur permet d’accéder.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de protéger les authentificateurs. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 contrôle IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Gestion des authentificateurs | Aucun authentificateur statique non chiffré incorporé

**IA-5 (7)** L’organisation garantit qu’aucun authentificateur statique non chiffré n’est incorporé dans les applications ou les scripts d’accès, ni stocké sur les touches de fonction.

**Responsabilités :** `Customer Only`

|||
|---|---|
| Client | Cette solution Blueprint ne permet aucune utilisation des authentificateurs statiques non chiffrés dans les applications, les scripts d’accès ou les touches de fonction. Un script ou une application qui utilise un authentificateur passe un appel à un conteneur Azure Key Vault avant chaque utilisation. L’accès aux conteneurs Azure Key Vault fait l’objet d’un audit, ce qui permet de détecter les violations de cette interdiction si un compte de service est utilisé pour accéder à un système sans appel correspondant au conteneur Azure Key Vault. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 contrôle IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Gestion des authentificateurs | Plusieurs comptes de système d’information

**IA-5 (8)** L’organisation implémente [Affectation : protections de sécurité définies par l’organisation] pour gérer les risques de compromission, au cas où des personnes posséderaient des comptes sur plusieurs systèmes d’information.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client peut s’appuyer sur les dispositifs de protection de sécurité au niveau de l’entreprise afin de gérer les risques associés aux personnes possédant des comptes sur plusieurs systèmes. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 contrôle IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Gestion des authentificateurs | Authentification basée sur les jetons matériels

**IA-5 (11)** Dans le cadre de l’authentification basée sur les jetons matériels, le système d’information utilise des mécanismes qui répondent aux conditions définies [Affectation : exigences de qualité des jetons définies par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser des mécanismes pour répondre aux exigences de qualité de l’authentification basée sur les jetons matériels. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 contrôle IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Gestion des authentificateurs | Expiration des authentificateurs mis en cache

**IA-5 (13)** Le système d’information interdit l’utilisation des authentificateurs mis en cache après [Affectation : période de temps définie par l’organisation].

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Aucune ressource déployée par cette solution Blueprint n’est configurée pour autoriser l’utilisation d’authentificateurs mis en cache. L’authentification des ordinateurs virtuels déployés nécessite la saisie d’un authentificateur au moment de l’authentification. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 - Contrôle IA-6

#### <a name="authenticator-feedback"></a>Commentaires au sujet des authentificateurs

**IA-6** Le système d’information masque les commentaires apportés aux informations d’authentification pendant le processus d’authentification. L’objectif est de protéger les informations contre toute exploitation ou utilisation possible par des personnes non autorisées.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | L’accès aux ressources déployées par cette solution Blueprint s’effectue via le Bureau à distance et s’appuie sur l’authentification Windows. Les sessions d’authentification Windows masquent par défaut les mots de passe saisis pendant une session d’authentification.  |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 contrôle IA-7

#### <a name="cryptographic-module-authentication"></a>Authentification du module de chiffrement

**IA-7** Le système d’information implémente des mécanismes d’authentification dans un module de chiffrement. Ces derniers répondent aux exigences des lois fédérales en vigueur, ainsi que des décrets, directives, stratégies, réglementations, normes et conseils applicables à une telle authentification.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Cette solution Blueprint utilise l’authentification Windows, le Bureau à distance et BitLocker. Ces composants peuvent être configurés d’après les modules de chiffrement FIPS 140 validés. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 contrôle IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identification et authentification (utilisateurs extérieurs à l’organisation)

**IA-8** Le système d’information identifie et authentifie de manière unique les utilisateurs extérieurs à l’organisation (ou les processus intervenant pour le compte des utilisateurs extérieurs à l’organisation).

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’identifier et d’authentifier les utilisateurs extérieurs à l’organisation qui accèdent aux ressources qu’il déploie. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 contrôle IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identification et authentification (utilisateurs extérieurs à l’organisation) | Acceptation des informations d’identification de la vérification d’identité personnelle émanant d’autres agences

**IA-8 (1)** Le système d’information accepte et vérifie par voie électronique les informations d’identification de la vérification d’identité personnelle (PIV) émanant d’autres agences fédérales.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’accepter et de vérifier les informations d’identification de la vérification d’identité personnelle (PIV) émanant d’autres agences fédérales. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 contrôle IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identification et authentification (utilisateurs extérieurs à l’organisation) | Acceptation des informations d’identification tierces

**IA-8 (2)** Le système d’information accepte uniquement les informations d’identification tierces approuvées par la FICAM.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’accepter uniquement les informations d’identification tierces qui ont été approuvées par l’initiative TFS (Trust Framework Solutions) de la FICAM (Federal Identity, Credential, and Access Management). |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 contrôle IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identification et authentification (utilisateurs extérieurs à l’organisation) | Utilisation des produits approuvés par la FICAM

**IA-8 (3)** L’organisation utilise uniquement les composants de système d’information approuvés par la FICAM dans [Affectation : systèmes d’information définis par l’organisation] pour accepter les informations d’identification tierces.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé d’utiliser uniquement les ressources approuvées par l’initiative TFS de la FICAM pour accepter les informations d’identification tierces. |
| **Fournisseur (Microsoft Azure)** | Non applicable |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 contrôle IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identification et authentification (utilisateurs extérieurs à l’organisation) | Utilisation de profils émis par la FICAM

**IA-8 (4)** Le système d’information se conforme aux profils émis par la FICAM.

**Responsabilités :** `Customer Only`

|||
|---|---|
| **Client** | Le client est chargé de se conformer aux profils émis par l’initiative TFS (Trust Framework Solutions) de la FICAM (Federal Identity, Credential, and Access Management). |
| **Fournisseur (Microsoft Azure)** | Non applicable |
