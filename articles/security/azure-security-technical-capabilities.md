---
title: "Fonctionnalités techniques de la sécurité Azure | Microsoft Docs"
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
ms.openlocfilehash: 536745d869ea3bcd0beedad3712597b00e600796
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-security-technical-capabilities"></a>Fonctionnalités techniques de la sécurité Azure

Afin d’aider les clients Azure, actuels et futurs, à comprendre et à utiliser les différentes fonctionnalités de sécurité qui sont disponibles dans et autour de la plateforme Azure, Microsoft a développé une série de livres blancs, de présentations, de bonnes pratiques et de listes de vérifications sur la sécurité. Les rubriques sont aussi précises que variées et sont mises à jour régulièrement. Ce document fait partie de cette série, comme mentionné dans la section Résumé ci-dessous. Des informations complémentaires sur cette série consacrée à la sécurité Azure sont mises à votre disposition à cette adresse (URL).

## <a name="azure-platform"></a>Plateforme Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) est une plateforme cloud, composée de services d’infrastructure et d’application, avec des services de données intégrés et une analytique poussée, ainsi que des outils et des services de développement, tous hébergés dans les centres de données du cloud public de Microsoft. Les clients utilisent la plateforme Azure pour un grand nombre de fonctionnalités et de scénarios différents : calcul de base, mise en réseau, stockage, services d’application web et mobiles, sans oublier les scénarios entièrement basés sur le cloud (par exemple l’Internet des objets). La plateforme peut être utilisée avec les technologies open source et déployée en tant que cloud hybride à moins d’être hébergée dans le centre de données d’un client. Azure fournit des technologies cloud qui permettent aux entreprises de réduire leurs coûts, d’innover rapidement et de gérer les systèmes de façon proactive. Lorsque vous générez ou faites migrer des ressources informatiques vers un fournisseur de cloud, vous comptez sur les capacités de cette organisation à protéger vos applications et vos données, avec les services et les contrôles qu’elle vous fournit pour gérer la sécurité de vos ressources cloud.

Microsoft Azure est le seul fournisseur de cloud computing à offrir une plateforme d’applications sécurisée et homogène et une infrastructure IaaS pour que les équipes travaillent selon leurs différentes compétences de cloud computing et niveaux de complexité de projets, avec des services de données intégrés et une analytique qui révèlent l’intelligence des données où qu’elles se trouvent, dans les plateformes Microsoft et non-Microsoft, dans des outils et des frameworks ouverts, en donnant le choix d’intégrer le cloud à l’environnement local et de déployer des services cloud Azure dans des centres de données locaux. Dans le cadre de la solution Microsoft Trusted Cloud, les clients comptent sur Azure et sur sa sécurité, sa fiabilité, sa conformité, sa confidentialité et son vaste réseau de personnes, de partenaires et de processus leaders sur le marché pour prendre en charge les organisations dans le cloud.

Avec Microsoft Azure, vous pouvez :

- Innover plus vite avec le cloud.

- Obtenir des insights pour dynamiser les décisions et les applications métier.

- Créer sans limite et déployer où que vous soyez.

- Protéger vos activités.

## <a name="scope"></a>Thème abordé

L’idée-force de ce livre blanc s’intéresse aux fonctions et fonctionnalités de sécurité prenant en charge les composants essentiels de Microsoft Azure, c’est-à-dire [Stockage Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL Databases](https://docs.microsoft.com/azure/sql-database/), le [modèle de machine virtuelle de Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/) ainsi que les outils et l’infrastructure qui les gèrent tous. Ce livre blanc cible principalement les fonctionnalités techniques de Microsoft Azure qui sont mises à votre disposition et à celle de vos clients pour protéger la sécurité et la confidentialité de leurs données.

L’importance de comprendre ce modèle de responsabilité partagée est vitale pour les clients qui migrent vers le cloud. Les fournisseurs de cloud offrent des avantages considérables en termes d’efforts sur la conformité et la sécurité, mais ces avantages ne soustraient pas le client à sa responsabilité de protection de ses utilisateurs, de ses applications et de ses offres de service.

En ce qui concerne les solutions IaaS, le client est responsable ou partage la responsabilité pour ce qui est de la sécurisation et de la gestion du système d’exploitation, de la configuration réseau, des applications, de l’identité, des clients et des données.  Les solutions PaaS s’élaborent sur des déploiements IaaS, le client est toujours responsable ou a une part de responsabilité au niveau de la sécurisation et de la gestion des applications, de l’identité, des clients et des données. Pour les solutions SaaS, cela étant, la responsabilité du client continue d’être engagée. Il doit s’assurer que les données sont classées correctement, et partage une responsabilité de gestion de ses utilisateurs et de ses appareils de point de terminaison.

Ce document ne traite pas en détail les composants de la plateforme Microsoft Azure connexes, tels que Sites Web Microsoft Azure, Azure Active Directory, HDInsight, Media Services et d’autres services qui viennent s’ajouter aux composants de base. Même si un minimum d’informations générales sont données ici, les lecteurs sont censés connaître les concepts fondamentaux d’Azure, tels que décrits dans les autres documentations de référence fournies par Microsoft et incluses dans les liens indiqués dans ce livre blanc.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Fonctionnalités techniques de sécurité disponibles pour s’acquitter de la responsabilité de l’utilisateur (client) - Vue d’ensemble

Microsoft Azure fournit des services qui permettent aux clients de répondre aux besoins de sécurité, de confidentialité et de conformité. L’illustration suivante explique les différents services Azure mis à la disposition des utilisateurs pour créer une infrastructure d’applications sécurisée et conforme, s’appuyant sur des normes industrielles.

![Fonctionnalités techniques de sécurité disponibles - Vue d’ensemble](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Gérer et contrôler l’identité et l’accès utilisateur (protection)

Azure vous aide à protéger les informations professionnelles et personnelles en vous permettant de gérer les informations d’identification et les identités des utilisateurs, et d’en contrôler l’accès.

### <a name="azure-active-directory"></a>Azure Active Directory

Les solutions de gestion des identités et accès de Microsoft aident les services informatiques à protéger l’accès aux applications et ressources dans le centre de données d’entreprise comme dans le cloud, en activant des niveaux supplémentaires de validation telles que l’authentification multifacteur et les stratégies d’accès conditionnel. En surveillant les activités suspectes via les fonctions avancées de reporting, d’audit et d’alertes de sécurité, vous êtes en mesure de limiter les problèmes de sécurité potentiels. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) fournit une authentification unique à des milliers d’applications cloud (SaaS) et assure un accès aux applications web que vous exécutez en local.

Azure Active Directory (Azure AD) présente de nombreux avantages en termes de sécurité :

- Création et gestion d’une identité unique pour chaque utilisateur de l’entreprise hybride, tout en maintenant la synchronisation des utilisateurs, des groupes et des appareils

- Accès par authentification unique à vos applications, notamment à des milliers d’applications SaaS pré-intégrées

- Sécurisation de l’accès aux applications en appliquant une authentification multifacteur basée sur des règles pour les applications aussi bien locales que cloud

- Accès à distance sécurisé aux applications web locales via le proxy d’application Azure AD

Le [portail Azure Active Directory](http://aad.portal.azure.com/) fait partie du portail Azure. À partir de ce tableau de bord, vous pouvez obtenir une vue d’ensemble de l’état de votre organisation et vous plonger facilement dans la gestion des accès aux annuaires, aux utilisateurs ou aux applications.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Voici les principales fonctionnalités de gestion des identités Azure :

- Authentification unique

- Authentification multifacteur

- Surveillance de la sécurité, alertes et rapports Machine Learning

- Gestion des identités et des accès des consommateurs

- Enregistrement de l’appareil

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>Authentification unique

Avec l’[authentification unique (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/), vous pouvez accéder à toutes les applications et à toutes les ressources dont vous avez besoin pour travailler, en vous connectant une seule fois avec un seul compte d’utilisateur. Une fois connecté, vous accédez à toutes les applications qui vous sont indispensables, sans avoir besoin de vous réauthentifier (par exemple, en tapant un mot de passe).

De nombreuses entreprises s’appuient sur des applications SaaS, comme Office 365, Box et Salesforce, pour accroître la productivité des utilisateurs finaux. Historiquement, le personnel informatique devait créer et mettre à jour chaque compte d’utilisateur dans chaque application SaaS et les utilisateurs devaient mémoriser un mot de passe pour chaque application SaaS.

[Azure AD étend Active Directory local dans le cloud](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), ce qui permet aux utilisateurs d’utiliser leur compte professionnel principal, non seulement pour se connecter à leurs appareils liés au domaine et aux ressources de l’entreprise, mais aussi à toutes les applications SaaS et web nécessaires à leur travail.

Non seulement les utilisateurs n’ont plus besoin de gérer plusieurs noms d’utilisateur et mots de passe, mais l’accès aux applications peut être automatiquement activé ou désactivé en fonction des groupes de l’organisation et de leur statut d’employé. [Azure AD ajoute des contrôles de sécurité et de gouvernance de l’accès](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) qui vous permettent de gérer de manière centralisée l’accès des utilisateurs sur les différentes applications SaaS.

#### <a name="multi-factor-authentication"></a>Authentification multifacteur

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) est une méthode d’authentification qui nécessite l’utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. [MFA contribue à sécuriser](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Cette méthode fournit une authentification forte par le biais de diverses options de vérification : appel téléphonique, SMS, notification par application mobile ou code de vérification et jetons OAuth tiers.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Surveillance de la sécurité, alertes et rapports Machine Learning

Vous pouvez protéger votre entreprise grâce à la surveillance de la sécurité, aux alertes et aux rapports Machine Learning qui identifient les comportements d’accès incohérents. Vous pouvez utiliser les rapports d'accès et d'utilisation Azure Active Directory pour obtenir une visibilité complète sur l'intégrité et la sécurité du répertoire de votre société. Grâce à ces informations, un administrateur de répertoire est capable de déterminer plus précisément les risques de sécurité potentiels et donc de les atténuer au maximum.

Dans le portail Azure ou le [portail Azure Active Directory](http://aad.portal.azure.com/), les [rapports](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) sont classés comme suit :

- Rapports d’anomalies : contiennent les événements de connexion qui peuvent nous sembler anormaux. Notre objectif est de vous faire part de ces activités et de vous permettre de décider si un événement est suspect.

- Rapports d’application intégrée : fournissent des indications sur l’utilisation des applications du cloud au sein de votre société. Azure Active Directory permet d’intégrer des milliers d'applications du cloud.

- Rapports d’erreurs : indiquent les erreurs qui peuvent survenir lors de la configuration de comptes sur des applications externes.

- Rapports spécifiques à l’utilisateur : affichent les données d’activité relatives aux appareils/connexions d’un utilisateur spécifique.

- Journaux d’activité : contiennent un enregistrement de tous les événements audités durant les dernières 24 heures, les derniers 7 jours ou les derniers 30 jours, des modifications d’activité de groupes, et des activités d’enregistrement et de réinitialisation de mot de passe.

#### <a name="consumer-identity-and-access-management"></a>Gestion des identités et des accès des consommateurs

Pour les applications utilisées par les consommateurs, [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) offre un service de gestion de l’identité, global et hautement disponible, qui s’adapte à des centaines de millions d’identités. Le service peut être intégré sur l’ensemble des plateformes web et mobiles. Vos consommateurs peuvent se connecter à toutes vos applications via une expérience personnalisable en utilisant leurs comptes de réseaux sociaux existants ou en créant des comptes avec de nouvelles informations d’identification.

Auparavant, les développeurs d’applications qui souhaitaient [inscrire et connecter les consommateurs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) à leurs applications auraient écrit leur propre code. Et ils auraient utilisé des systèmes ou des bases de données locaux pour stocker les noms d'utilisateur et les mots de passe. Azure Active Directory B2C offre à votre organisation un meilleur moyen d’intégrer la gestion des identités des consommateurs dans vos applications grâce à une plateforme sécurisée reposant sur des normes et à un ensemble complet de stratégies extensibles.

Lorsque vous utilisez Azure Active Directory B2C, vos consommateurs peuvent s’inscrire auprès de vos applications à l’aide de leurs comptes sociaux existants (Facebook, Google, Amazon, LinkedIn) ou en créant des informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe).

#### <a name="device-registration"></a>Inscription d’appareil

[L’inscription d’appareil Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/device-management-introduction) constitue la base des scénarios [d’accès conditionnel](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-device-registration-on-premises-setup) basé sur les appareils. Lors de l’inscription d’un appareil, Azure AD Device Registration fournit une identité à l’appareil qui sera utilisée pour l’authentifier lors de la connexion de l’utilisateur. L’appareil authentifié et les attributs de l’appareil peuvent alors être utilisés pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées sur le cloud et localement.

Quand ils sont associés à une solution de [gestion des appareils mobiles (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) comme Intune, les attributs de l’appareil dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur l’appareil. Cela vous permet de créer des règles d’accès conditionnel qui imposent que l’accès à partir des appareils réponde à vos critères de sécurité et de conformité.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

Le service [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) vous permet de gérer, de contrôler et de surveiller vos identités privilégiées et l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft, tels qu’Office 365 ou Microsoft Intune.

Les utilisateurs doivent parfois effectuer des opérations privilégiées dans des ressources Azure ou Office 365, ou dans d'autres applications SaaS. Cela signifie souvent que les entreprises doivent leur donner un accès privilégié permanent à Azure AD. C’est un risque de sécurité croissant pour les ressources hébergées dans le cloud, car les entreprises ne peuvent pas suffisamment surveiller ce que ces utilisateurs font avec leurs privilèges d'administrateur. En outre, si un compte d’utilisateur disposant d’un accès privilégié est compromis, cette seule faille peut affecter sa sécurité globale sur le cloud. Azure AD Privileged Identity Management contribue à minimiser ce risque.

Grâce à Azure AD Privileged Identity Management, vous pouvez :

- Identifier les utilisateurs qui ont un rôle d’administrateur dans Azure AD

- Activer à la demande un accès administrateur « juste à temps » aux services Microsoft Online Services comme Office 365 et Intune

- Obtenir des rapports sur l'historique des accès administrateur et sur les modifications apportées aux affectations de l'administrateur

- Recevoir des alertes sur l'accès à un rôle privilégié

#### <a name="identity-protection"></a>Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) est un service de sécurité offrant une vue centralisée des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. Identity Protection utilise des fonctionnalités existantes de détection des anomalies d’Azure Active Directory (disponibles via les rapports d’activités anormales d’Azure AD) et introduit de nouveaux types d’événements à risque capables de détecter les anomalies en temps réel.

## <a name="secured-resource-access-in-azure"></a>Accès des ressources sécurisées dans Azure

Le contrôle des accès dans Azure s’envisage d’abord dans une perspective de facturation. Le propriétaire d’un compte Azure accessible par le biais du [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) est l’administrateur de compte. Les abonnements sont un conteneur de facturation, mais ils constituent également une limite de sécurité : chaque abonnement a un administrateur de service qui peut ajouter, supprimer et modifier des ressources Azure dans cet abonnement à l’aide du portail Azure. L’administrateur de sécurité par défaut d’un nouvel abonnement est l’administrateur de compte, mais ce dernier peut changer l’administrateur de sécurité dans le Centre des comptes Azure.

![Accès des ressources sécurisées dans Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Les abonnements sont également associés à un annuaire. L’annuaire définit un ensemble d'utilisateurs. Ceux-ci peuvent être des utilisateurs professionnels ou scolaires qui ont créé l’annuaire, ou bien des utilisateurs externes (autrement dit, des comptes Microsoft). Les abonnements sont accessibles par une partie des utilisateurs de l’annuaire qui ont été affectés comme administrateur de service ou comme coadministrateur ; la seule exception est que, pour des raisons d'héritage, les comptes Microsoft (anciennement Windows Live ID) peuvent être affectés comme administrateur de service ou comme co-admistrateur sans être présents dans l’annuaire.

Les entreprises orientées sécurité doivent s’efforcer de donner aux employés les autorisations exactes dont ils ont besoin. Un trop grand nombre d’autorisations peut exposer un compte aux attaquants. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. Le [contrôle d’accès en fonction du rôle (RBAC) dans Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) permet de résoudre ce problème en offrant une gestion précise de l’accès pour Azure.

![Accès des ressources sécurisées ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. Plutôt que de donner à tous des autorisations illimitées au sein de votre abonnement ou de vos ressources Azure, vous pouvez autoriser uniquement certaines actions. Par exemple, vous pouvez utiliser le RBAC pour permettre à un employé donné de gérer les machines virtuelles dans un abonnement, tandis qu’un autre pourra gérer les bases de données SQL au sein du même abonnement.

![Accès aux ressources sécurisées dans Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Chiffrement et sécurité des données dans Azure (protection)

Pour assurer la protection des données dans le cloud, l’un des facteurs clés consiste à tenir compte des états que les données peuvent présenter, mais aussi des contrôles disponibles pour ces états. Pour les bonnes pratiques de chiffrement et de sécurité des données dans Azure, les recommandations que nous formulons s’articulent autour des états suivants des données :

- Au repos : cela inclut tous les objets de stockage, conteneurs et types d’informations présents de manière statique sur un support physique, qu’il s’agisse d’un disque magnétique ou d’un disque optique.

- En transit : lorsque des données sont transférées entre des composants, des emplacements ou des programmes (sur le réseau, par exemple) via un bus de service (depuis un emplacement local vers le cloud, ou vice-versa, y compris via des connexions hybrides comme ExpressRoute), ou lors d’un processus d’entrée/sortie, on parle de données « en transit ».

### <a name="encryption-at-rest"></a>Chiffrement au repos

Pour appliquer le chiffrement au repos, vous devez effectuer ce qui suit :

Prendre en charge au moins un des modèles de chiffrement recommandés qui sont répertoriés dans le tableau suivant pour chiffrer les données.

| Modèles de chiffrement |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Chiffrement serveur | Chiffrement serveur | Chiffrement serveur | Chiffrement client
| Chiffrement côté serveur à l’aide de clés gérés par le service | Chiffrement côté serveur à l’aide de clés gérées par le client dans Azure Key Vault | Chiffrement côté serveur à l’aide de clés gérés par le client local |
| • Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement <br> • Microsoft gère les clés <br>• Fonctionnalité cloud complète | • Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement<br>• Le client contrôle les clés par le biais d’Azure Key Vault<br>• Fonctionnalité cloud complète | • Les fournisseurs de ressources Azure effectuent les opérations de chiffrement et de déchiffrement <br>• Le client contrôle les clés localement <br> • Fonctionnalité cloud complète| • Les services Azure ne peuvent pas voir les données déchiffrées <br>• Les clients conservent les clés localement (ou dans d’autres banques d’informations sécurisées). Les clés ne sont pas disponibles pour les services Azure <br>• Fonctionnalité cloud réduite|

### <a name="enabling-encryption-at-rest"></a>Activation du chiffrement au repos

**Identifier tous les emplacements où vos données sont stockées**

Le chiffrement au repos vise à chiffrer toutes les données. En agissant ainsi, la possibilité qu’il puisse manquer des données importantes ou des emplacements persistants est éliminée. Énumérez toutes les données stockées par votre application. 

> [!Note] 
> Pas simplement les « données d’application » ou les « informations d’identification personnelle », mais les données relatives à l’application, y compris les métadonnées de compte (mappages d’abonnement, informations de contrat, informations d’identification personnelle).

Réfléchissez aux banques d’informations que vous utilisez pour stocker les données. Par exemple : 

- Stockage externe (par exemple, SQL Azure, Document DB, HDInsights, Data Lake, etc.)

- Stockage temporaire (n’importe quel cache local incluant les données du locataire)

- Cache en mémoire (peut être intégré dans le fichier d’échange)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Optimiser la prise en charge existante du chiffrement au repos dans Azure

Pour chaque banque d’informations que vous utilisez, tirez parti de la prise en charge existante pour le chiffrement au repos.

- Stockage Azure : Voir [Chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/storage-service-encryption)

- SQL Azure : Voir [Transparent Data Encryption (TDE), SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Machine virtuelle & stockage sur disque local ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Pour le stockage de disque local et de machine virtuelle, utilisez Azure Disk Encryption où il est pris en charge :

#### <a name="iaas"></a>IaaS

Les services avec des machines virtuelles IaaS (Windows ou Linux) doivent utiliser [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) pour chiffrer les volumes contenant des données client.

#### <a name="paas-v2"></a>PaaS v2

Les services qui s’exécutent sur PaaS v2 au moyen de Service Fabric peuvent utiliser Azure Disk Encryption pour le groupe de machines virtuelles identiques, afin de chiffrer leurs machines virtuelles PaaS v2.

#### <a name="paas-v1"></a>PaaS v1

Actuellement, Azure Disk Encryption n’est pas pris en charge sur PaaS v1. Par conséquent, vous devez utiliser le chiffrement au niveau de l’application pour chiffrer les données persistantes au repos.  Cela inclut, mais n’est pas limité aux données d’application, fichiers temporaires, journaux et vidages sur incident.

La plupart des services devraient tenter de tirer parti du chiffrement d’un fournisseur de ressources de stockage. Certains services doivent procéder à un chiffrement explicite, par exemple, le matériel de clé persistante (certificats, clé racine / principale) doit être stocké dans Key Vault.

Si vous prenez en charge le chiffrement côté service avec des clés gérées par le client, celui-ci doit trouver le moyen de nous faire parvenir la clé. Le moyen recommandé et pris en charge pour ce faire est l’intégration à Azure Key Vault. Dans ce cas, les clients peuvent ajouter et gérer leurs clés dans Azure Key Vault. Un client peut apprendre à utiliser Azure Key Vault en consultant [Bien démarrer avec Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Pour l’intégration à Azure Key Vault, vous devez ajouter du code pour demander une clé à Azure Key Vault lorsqu’elle est nécessaire pour le déchiffrement.

- Consultez [Azure Key Vault – Étape par étape](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) pour obtenir plus d’informations sur l’intégration à Azure Key Vault.

Si vous prenez en charge les clés gérées par le client, vous devez fournir une expérience utilisateur (UX) pour que le client puisse spécifier quel Key Vault (ou l’URI Key Vault) utiliser.

Comme le chiffrement au repos implique le chiffrement de l’hôte, de l’infrastructure et des données du locataire, la perte des clés due à une défaillance du système ou à des activités malveillantes peut signifier la perte de toutes les données chiffrées. Il est donc capital que votre solution de chiffrement au repos dispose d’un historique de récupération d’urgence complet, résistant aux défaillances du système et aux activités malveillantes.

Les services qui implémentent le chiffrement au repos sont en général toujours vulnérables au niveau des données ou des clés de chiffrement qui restent non chiffrés sur le lecteur hôte (par exemple, dans le fichier d’échange du système d’exploitation hôte). Par conséquent, les services doivent s’assurer que le volume hôte soit chiffré pour leurs services. Pour faciliter cette opération, l’équipe Compute a activé le déploiement du chiffrement de l’hôte, qui utilise [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP et des extensions vers l’agent et le service DCM pour chiffrer le volume hôte.

La plupart des services sont implémentés sur des machines virtuelles Azure standard. Ces services devraient automatiquement récupérés le[chiffrement de l’hôte](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lorsque Compute l’active. Pour les services qui s’exécutent dans les clusters gérés par Compute, le chiffrement de l’hôte est activé automatiquement en même temps que Windows Server 2016 est déployé.

### <a name="encryption-in-transit"></a>Chiffrement en transit

La protection des données en transit doit être un aspect essentiel de votre stratégie de protection des données. Comme les données transitent entre différents emplacements, dans les deux sens, nous vous recommandons d’utiliser systématiquement les protocoles SSL/TLS pour ces déplacements de données. Dans certains cas, vous souhaiterez isoler l’intégralité du canal de communication entre votre infrastructure locale et sur le cloud, via un réseau privé virtuel (VPN).

Pour les données qui transitent entre votre infrastructure locale et Azure, vous devez envisager le recours aux dispositifs de protection appropriés, comme HTTPS ou VPN.

Pour les organisations devant sécuriser l’accès à Azure à partir de plusieurs postes de travail locaux, utilisez la fonction [VPN de site à site d’Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Pour les organisations devant sécuriser l’accès à Azure à partir d’un seul poste de travail local, utilisez la fonction [VPN de point à site d’Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Les jeux de données volumineux peuvent être transmis par le biais d’une liaison réseau étendu haut débit dédiée, comme [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vous choisissez d’utiliser ExpressRoute, vous pouvez également chiffrer les données au niveau des applications par le biais de [SSL/TLS](https://support.microsoft.com/kb/257591) ou d’autres protocoles, pour optimiser la protection.

Si vous interagissez avec Azure Storage via le portail Azure, toutes les transactions se produisent via HTTPS. L’[API de stockage REST](https://msdn.microsoft.com/library/azure/dd179355.aspx) par le biais de HTTPS peut également être utilisée pour interagir avec [Stockage Azure](https://azure.microsoft.com/services/storage/) et [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Les organisations qui ne parviennent pas à protéger les données en transit sont plus sensibles aux [attaques d’intercepteur](https://technet.microsoft.com/library/gg195821.aspx), aux [écoutes électroniques](https://technet.microsoft.com/library/gg195641.aspx) et au piratage de session. Ces attaques peuvent être la première étape d’un processus visant à accéder à des données confidentielles.

Vous pouvez en savoir plus sur l’option de VPN Azure en lisant l’article [Planification et conception de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Application du chiffrement des données au niveau fichier

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utilise des stratégies de chiffrement, d’identité et d’autorisation pour vous aider à sécuriser vos fichiers et vos e-mails. Azure RMS peut fonctionner sur plusieurs appareils (téléphones, tablettes et PC), en protégeant les données au sein de votre organisation et en dehors de cette dernière. Cette fonctionnalité est possible, car Azure RMS ajoute un niveau de protection qui reste avec les données, même lorsqu’elles quittent les limites de votre organisation.

Quand vous utilisez Azure RMS pour protéger vos fichiers, vous recourez au chiffrement standard avec prise en charge complète de la norme [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Lorsque vous tirez parti d’Azure RMS pour la protection des données, vous avez l’assurance que la protection reste avec le fichier, même s’il est copié sur un stockage qui n’est pas sous le contrôle du département informatique (service de stockage cloud, par exemple). Il en va de même pour les fichiers partagés par e-mail ; ils sont protégés en tant que pièces jointes à un message électronique. Des instructions expliquant comment ouvrir la pièce jointe protégée sont fournies.
Si vous planifiez l’adoption d’Azure RMS, nous vous recommandons de suivre les conseils ci-après :

- Installez l’[application de partage RMS](https://technet.microsoft.com/library/dn339006.aspx) Cette application s’intègre avec les applications Office en installant un complément Office, afin que les utilisateurs puissent directement protéger leurs fichiers, en toute simplicité.

- Configurez des applications et des services pour prendre en charge Azure RMS.

- Créez des [modèles personnalisés](https://technet.microsoft.com/library/dn642472.aspx) qui reflètent les besoins de votre entreprise (exemple : un modèle portant sur des données ultra-secrètes, qui doit être appliqué à tous les e-mails ultra-secrets).

Les organisations bénéficiant d’une [classification](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) et d’une protection insuffisantes des données sont plus vulnérables aux fuites de données. Si la protection des fichiers n’est pas appropriée, les organisations ne pourront pas optimiser leur visibilité, ni surveiller les abus ou empêcher tout accès malveillant aux fichiers.

> [!Note]
> Vous pouvez en savoir plus sur Azure RMS en lisant l’article [Prise en main d’Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Sécuriser votre application (protection)
Si Azure est responsable de la sécurisation de l’infrastructure et de la plateforme sur laquelle votre application s’exécute, il vous incombe de sécuriser votre application elle-même. En d’autres termes, vous devez développer, déployer et gérer votre code d’application et le contenu de manière sécurisée. Sinon, votre code d’application ou le contenu peut encore être vulnérable aux menaces.

### <a name="web-application-firewall-waf"></a>Pare-feu d’applications web (WAF)
Le [pare-feu d’applications Web (WAF, Web Application Firewall)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) est une fonctionnalité de [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) qui protège vos applications web de manière centralisée contre les vulnérabilités et exploits courants.

Le pare-feu d’applications web suit les règles des [Ensembles de règles de base OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Les applications Web sont de plus en plus la cible d’attaques malveillantes qui exploitent des vulnérabilités connues. Les types d’attaques les plus courantes sont l’injection de code SQL, les attaques de script site à site, entre autres. Empêcher ces attaques dans le code d’application peut se révéler difficile et nécessiter une maintenance rigoureuse, des mises à jour correctives ainsi que la surveillance au niveau de plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé facilite grandement la gestion de la sécurité et offre une meilleure garantie de protection aux administrateurs de l’application contre les menaces ou les intrusions. Une solution WAF peut également réagir plus rapidement à une menace de sécurité en exécutant la mise à jour corrective d’une vulnérabilité connue dans un emplacement central plutôt que de sécuriser individuellement chacune des applications web. Les passerelles d’application existantes peuvent être facilement converties en une passerelle d’application avec un pare-feu d’applications web.

Le pare-feu d’applications web protège notamment des vulnérabilités web courantes suivantes :

- Protection contre les injections de code SQL

- Protection de l’exécution de script de site à site

- Protection contre les attaques web courante comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)

- Protection contre les violations de protocole HTTP

- Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept

- Protection contre les robots, les crawlers et les scanneurs

- Détection des erreurs de configuration d’application courantes (par exemple, Apache, IIS, etc.)

> [!Note]
> Pour une liste plus détaillée des règles et de leurs protections, consultez ces [Ensembles de règles de base](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets).

Azure dispose de plusieurs fonctionnalités simples d’utilisation pour aider à sécuriser le trafic entrant et sortant de votre application. Azure aide également les clients à sécuriser leur code d’application en fournissant des fonctionnalités en externe pour analyser votre application web et y rechercher des vulnérabilités.

- [Configuration de l'authentification Azure Active Directory pour votre application](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Sécurisation du trafic vers votre application avec l'activation du protocole TLS/SSL (Transport Layer Security) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Affectation de force de tout le trafic entrant sur la connexion HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Activation du protocole HSTS (Strict Transport Security)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Restriction de l'accès à votre application selon l'adresse IP du client](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restriction de l'accès à votre application selon le comportement du client - fréquence des demandes et accès concurrentiel](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Analyse du code de votre application web pour rechercher les vulnérabilités à l'aide de l'analyse Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configuration de l'authentification mutuelle TLS pour exiger des certificats client pour la connexion à votre application web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Configuration d'un certificat client à utiliser à partir de votre application pour la connexion sécurisée à des ressources externes](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Suppression des en-têtes standard de serveur pour éviter la prise d'empreinte de votre application par des outils](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Connexion sécurisée de votre application aux ressources d'un réseau privé à l'aide d'un VPN de point à site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Connexion sécurisée de votre application aux ressources d'un réseau privé à l'aide de connexions hybrides](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure App Service utilise la même solution anti-programme malveillant qu’Azure Cloud Services et que les ordinateurs virtuels. Pour en savoir plus à ce sujet, consultez notre [documentation sur les logiciels anti-programme malveillant](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Sécuriser votre réseau (protection)
Microsoft Azure inclut une infrastructure réseau solide pour prendre en charge les exigences de connectivité de vos applications et services. La connectivité réseau est possible entre les ressources hébergées dans Azure, entre les ressources hébergées sur site et dans Azure, mais aussi vers et à partir d’Internet et d’Azure.

L’[infrastructure réseau d’Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) vous permet de connecter en toute sécurité les ressources Azure entre elles en utilisant des [réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Un réseau virtuel est une isolation logique du réseau cloud Azure dédié à votre abonnement. Vous pouvez connecter des réseaux virtuels à vos réseaux locaux.

![Sécuriser votre réseau (protection)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Si vous avez besoin d’un contrôle d’accès au niveau du réseau de base (reposant sur l’adresse IP et les protocoles TCP ou UDP), vous pouvez utiliser des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Un groupe de sécurité réseau est un pare-feu de filtrage des paquets avec état qui vous permet de contrôler l’accès sur la base d’un algorithme à [5 tuples](https://www.techopedia.com/definition/28190/5-tuple).

La mise en réseau Azure permet de personnaliser le comportement de routage du trafic réseau sur vos réseaux virtuels Azure. Pour cela, vous pouvez configurer les [itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) dans Azure.

Le [tunneling forcé](https://www.petri.com/azure-forced-tunneling) est un mécanisme que vous pouvez utiliser pour empêcher vos services de se connecter aux appareils sur Internet.

Azure prend en charge la connectivité d’une liaison de réseau étendu dédiée entre votre réseau local et un réseau virtuel Microsoft Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). La liaison entre Azure et votre site se fait via une connexion dédiée qui ne passe pas par l’Internet public. Si votre application Azure s’exécute dans plusieurs centres de données, vous pouvez utiliser [Microsoft Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pour acheminer intelligemment les requêtes des utilisateurs vers différentes instances de l’application. Vous pouvez aussi acheminer le trafic vers des services qui ne s’exécutent pas dans Azure s’ils sont accessibles par Internet.

## <a name="virtual-machine-security-protect"></a>Sécurité de la machine virtuelle (protection)

Les [Machines virtuelles Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/) vous permettent de déployer un large éventail de solutions de calcul de façon agile. Avec la prise en charge de Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP et Azure BizTalk Services, vous pouvez déployer des charges de travail et des langages variés sur la plupart des systèmes d’exploitation.

Azure met à votre disposition des [logiciels anti-programmes malveillants](https://docs.microsoft.com/azure/security/azure-security-antimalware) provenant de fournisseurs de sécurité tels que Microsoft, Symantec, Trend Micro et Kaspersky. Ceux-ci permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces.

Microsoft Antimalware pour Azure Cloud Services et Machines virtuelles offre une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, logiciels espions et autres logiciels malveillants. Microsoft Antimalware fournit des alertes configurables lorsqu’un logiciel malveillant ou indésirable connu tente de s’installer ou de s’exécuter sur vos systèmes Azure.

[Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) est une solution évolutive qui protège les données de vos applications, sans investissement en capital et avec des frais de fonctionnement minimaux. Les erreurs rencontrées par les applications peuvent endommager vos données et les erreurs humaines peuvent introduire des bogues dans vos applications. Avec Sauvegarde Azure, vos machines virtuelles exécutant Windows et Linux sont protégées.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) aide à coordonner la réplication, le basculement et la récupération des charges de travail et des applications afin qu’elles soient disponibles à partir d’un site secondaire si votre site principal tombe en panne.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Assurer la conformité : Liste des points à vérifier avec une prudence mesurée pour les services cloud (protection)

Microsoft a développé la [liste des points à vérifier avec une prudence mesurée pour les services cloud](https://aka.ms/cloudchecklist.download), afin d’aider les organisations à faire preuve d’une sage circonspection lorsqu’elles envisagent une migration vers le cloud. Cette liste fournit une structure à une organisation, quels que soient sa taille et son type — entreprises privées et organisations du secteur public, notamment tous les niveaux de l’administration et les associations —, en lui permettant d’identifier ses propres performances, services, gestion des données, de même que ses exigences et objectifs de gouvernance. L’organisation peut alors comparer les offres de divers fournisseurs de services cloud tout en jetant en définitive les bases d’un contrat de service cloud.

Cette liste de vérifications préalables fournit un cadre qui s’aligne précisément sur les clauses d’une nouvelle norme internationale des contrats de service cloud, la norme ISO/IEC 19086. Cette norme offre un ensemble unifié de réflexions, afin d’aider les organisations à prendre des décisions concernant leur adoption du cloud, tout en constituant une base commune qui permet la comparaison entre les offres de service cloud.

La liste de vérifications préalables favorise une migration vers le cloud soigneusement étudiée, en apportant une aide structurée et une approche aussi cohérente que reproductible qui permet de choisir un fournisseur de services cloud.

Opter pour le cloud ne relève plus d’un choix purement technologique. Attendu que les conditions requises de la liste de vérifications préalables abordent tous les aspects d’une organisation, celles-ci servent à réunir l’ensemble des principaux décideurs internes : le directeur des systèmes d’information et le chef de la sécurité informatique, ainsi que des juristes, des acheteurs professionnels, des spécialistes en gestion des risques et en conformité. L’efficacité des processus de prise de décision s’en trouve accrue, et l’enracinement des décisions dans un raisonnement sain est renforcé, ce qui réduit la probabilité de blocages imprévus sur le chemin menant à l’adoption.

Par ailleurs, la liste de vérifications préalables :

- Expose les points de discussion fondamentaux qui se posent aux décideurs dès le début du processus d’adoption du cloud

- Alimente les débats approfondis au sein de l’entreprise sur la réglementation et les objectifs propres de l’organisation en matière de confidentialité, d’informations d’identification personnelle et de sécurité des données

- Aide les organisations à identifier les problèmes potentiels qui pourraient avoir un impact sur un projet cloud

- Fournit une série logique de questions, avec les mêmes conditions, définitions, mesures et résultats pour chaque fournisseur, afin de simplifier le processus de comparaison des offres émanant de différents fournisseurs de services cloud

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Validation de la sécurité des applications et de l’infrastructure Azure (détection)

[Azure Operational Security](https://docs.microsoft.com/azure/security/azure-operational-security) comprend les services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et d’autres ressources dans Microsoft Azure.

![Validation de la sécurité (détection)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure Operational Security repose sur un framework qui intègre les connaissances acquises par le biais de diverses possibilités spécifiques à Microsoft, notamment Microsoft Security Development Lifecycle (SDL), le programme Microsoft Security Response Center et une connaissance approfondie des cybermenaces.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) est la solution de gestion informatique pour le cloud hybride. Utilisée seule ou pour étendre votre déploiement System Center existant, OMS vous donne la flexibilité et le contrôle pour gérer votre infrastructure sur le cloud.

![Microsoft Operations Management Suite (OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Avec OMS, vous pouvez gérer n’importe quelle instance dans n’importe quel cloud, notamment local, Azure, AWS, Windows Server, Linux, VMware et OpenStack, à moindre coût par rapport aux solutions concurrentes. Conçu pour les environnements cloud, OMS offre une nouvelle approche de la gestion de votre entreprise, qui constitue le moyen le plus rapide et le plus économique pour répondre aux nouveaux défis métiers et pour gérer les nouvelles charges de travail, applications et environnements cloud.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) assure des services de surveillance pour OMS en collectant les données de ressources gérées et en les regroupant dans un référentiel central. Ces données peuvent comprendre des événements, des données de performances ou des données personnalisées fournies par le biais de l’API. Une fois collectées, les données sont disponibles pour les fonctions de génération d’alertes, d’analyse et d’exportation.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Cette méthode vous permet de regrouper les données issues de différentes sources, et de combiner des données de vos services Azure avec votre environnement local existant. En outre, cette approche dissocie clairement la collecte des données de l’exécution d’actions sur ces dernières, de sorte que toutes les actions sont disponibles sur tous les types de données.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires.

Voici quelques exemples :

- Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants

- Configuration de groupes de sécurité réseau et de règles pour contrôler le trafic vers les machines virtuelles

- Approvisionnement de pare-feu d’applications web pour protéger vos applications web contre les attaques ciblées

- Déploiement de mises à jour système manquantes

- Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

Le Centre de sécurité collecte, analyse et intègre automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

- Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes

- Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows

- Des attaques par force brute contre des machines virtuelles

- Des alertes de sécurité de logiciels anti-programme malveillant et de pare-feu intégrés

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) propose des références à des informations sur des types de ressources spécifiques. Il offre des services de visualisation, requête, routage, alertes, mise à l’échelle et automatisation pour les données de l’infrastructure Azure (journal d’activité) et pour chaque ressource Azure (journaux de diagnostic).

Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) En outre, vous pouvez utiliser les données de surveillance pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

L’audit de sécurité de votre réseau est essentiel pour détecter ses vulnérabilités et assurer la conformité avec votre modèle de gouvernance réglementaire et de sécurité informatique. Grâce à l’affichage du groupe de sécurité, vous pouvez récupérer le groupe de sécurité réseau configuré et ses règles de sécurité, ainsi que les règles de sécurité en vigueur. Avec la liste des règles appliquées, vous pouvez identifier les ports ouverts et évaluer la vulnérabilité du réseau.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.

### <a name="storage-analytics"></a>Storage analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) peut stocker des mesures qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux demandes adressées à un service de stockage. Les transactions sont indiquées au niveau de l'opération d'API ainsi qu'au niveau du service de stockage, et la capacité est indiquée au niveau du service de stockage. Les données de métriques peuvent être utilisées pour analyser l'utilisation du service de stockage, diagnostiquer les problèmes au niveau des demandes effectuées auprès du service de stockage et améliorer les performances des applications qui utilisent un service.

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Ce service détecte automatiquement les problèmes de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application. Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité. Il fonctionne avec des applications sur un large éventail de plates-formes, notamment .NET, Node.js et J2EE, hébergées sur site ou dans le cloud. Il s’intègre à votre processus DevOps et offre des points de connexion à divers outils de développement.

Il analyse les éléments suivants :

- **Taux de demandes, temps de réponse et taux d’échec** : identifiez les pages les plus consultées, à quel moment de la journée, et déterminez où se trouvent vos utilisateurs. Identifiez les pages qui offrent les meilleures performances. Si vos temps de réponse et votre taux d’échec augmentent lorsqu’il y a plus de requêtes, vous avez peut-être un problème de ressources.

- **Taux de dépendance, temps de réponse et taux d’échec** : déterminez si des services externes vous ralentissent.

- **Exceptions** : analysez les statistiques agrégées, ou choisissez des instances en particulier et explorez l’arborescence des appels de procédure et les requêtes connexes. Les exceptions de serveur et de navigateur sont signalées.

- **Consultations de pages et performances de chargement** : indiquées par le navigateur de vos utilisateurs.

- **Appels AJAX à partir de pages web** : taux, temps de réponse et taux d’échec.

- **Nombre de sessions et d’utilisateurs**.

- **Compteurs de performances** de vos ordinateurs serveurs Windows ou Linux, par exemple le processeur, la mémoire et l’utilisation du réseau.

- **Diagnostics d’hébergement** de Docker ou Azure.

- **Journaux de suivi des diagnostics** de votre application : pour pouvoir mettre en corrélation les événements de suivi avec les demandes.

- **Mesures et événements personnalisés**, vous les écrivez vous-même dans le code client ou serveur, pour effectuer le suivi des événements commerciaux, tels que les articles vendus ou les matchs gagnés.

L’infrastructure de votre application est généralement constituée de plusieurs composants (peut-être une machine virtuelle, un compte de stockage et un réseau virtuel ou une application web, une base de données, un serveur de base de données et 3 services de tiers). Vous ne voyez pas ces composants comme des entités distinctes, mais plutôt comme des parties associées et interdépendantes d’une seule et même entité. Vous avez alors besoin de regrouper le déploiement, la gestion et la surveillance de ces différentes parties. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) vous permet de travailler avec les ressources de votre solution en tant que groupe.

Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre solution dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Le gestionnaire de ressources assure la sécurité, les fonctions d’audit et de balisage pour vous aider à gérer vos ressources après le déploiement.

**Avantages liés à l’utilisation de Resource Manager**

Resource Manager offre plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller toutes les ressources de votre solution comme un groupe, plutôt que de les gérer individuellement.

- Vous pouvez déployer votre solution à plusieurs reprises tout au long du cycle de vie de développement et avoir ainsi l’assurance que vos ressources présentent un état cohérent lors de leur déploiement.

- Vous pouvez gérer votre infrastructure à l’aide de modèles déclaratifs plutôt que de scripts.

- Vous pouvez définir les dépendances entre les ressources, de façon à les déployer dans le bon ordre.

- Vous pouvez appliquer le contrôle d’accès à tous les services dans votre groupe de ressources, car le contrôle d’accès en fonction du rôle (RBAC) est intégré en mode natif à la plateforme de gestion.

- Vous pouvez appliquer des balises aux ressources pour organiser logiquement toutes les ressources de votre abonnement.

- Vous pouvez clarifier la facturation de votre organisation en affichant les coûts d’un groupe de ressources partageant la même balise.

> [!Note]
> Resource Manager propose une nouvelle façon de déployer et de gérer vos solutions. Si vous avez utilisé un modèle de déploiement antérieur et souhaitez obtenir des informations sur les modifications, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité, lisez nos rubriques détaillées sur la sécurité :

- [Audit et journalisation](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cybercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Conception et sécurité opérationnelle](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Chiffrement](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gestion de l’identité et de l’accès](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Sécurité du réseau](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gestion des menaces](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
