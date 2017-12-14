---
title: "Services de domaine Azure Active Directory : scénarios de déploiement | Microsoft Docs"
description: "Scénarios de déploiement pour les Services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: 11844fb8fabada9d863fe4adf0839ae6fa2ed101
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>Scénarios de déploiement et cas d'usage
Dans cette section, nous examinons quelques scénarios et cas pratiques qui tirent parti des services de domaine Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Administration sécurisée et simple des machines virtuelles Azure
Vous pouvez utiliser les services de domaine Azure Active Directory pour gérer vos machines virtuelles plus facilement. Des machines virtuelles Azure peuvent être jointes au domaine géré, ce qui vous permet d’utiliser vos informations d’identification AD d’entreprise pour vous connecter. Cette approche contribue à éviter les soucis de gestion des informations d’identification telles que la maintenance des comptes d’administrateur locaux sur chacune de vos machines virtuelles Azure.

Les machines virtuelles du serveur qui sont jointes au domaine géré peuvent également être gérées et sécurisées à l’aide de la stratégie de groupe. Vous pouvez appliquer des lignes de base de sécurité requises pour vos machines virtuelles Azure et les verrouiller conformément aux instructions de sécurité de l’entreprise. Par exemple, vous pouvez utiliser les fonctionnalités de gestion de stratégie de groupe pour restreindre les types d’applications pouvant être exécutés sur ces machines virtuelles.

![Administration simple des machines virtuelles Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Les serveurs et les autres infrastructures étant en fin de vie, Contoso transfère de nombreuses applications actuellement hébergées en local vers le cloud. La norme informatique actuelle exige que les serveurs hébergeant les applications d’entreprise soient joints à un domaine et gérés au moyen d’une stratégie de groupe. L’administrateur informatique de Contoso souhaite joindre à un domaine les machines virtuelles déployées dans Azure, afin de faciliter l’administration. Par conséquent, les administrateurs et les utilisateurs peuvent se connecter à l’aide de leurs informations d’identification d’entreprise. Dans le même temps, les machines peuvent être configurées pour se conformer aux lignes de base de sécurité requises à l’aide de la stratégie de groupe. Contoso préférerait ne pas avoir à déployer, surveiller et gérer les contrôleurs de domaine dans Azure pour garantir la sécurité des machines virtuelles Azure. Les services de domaine Azure AD sont idéaux pour ce cas d’utilisation.

**Notes de déploiement**

Considérez ces quelques points importants pour ce scénario de déploiement :

* Les domaines gérés fournis par les services de domaine Azure AD offrent par défaut une structure d’unité d’organisation plate. Toutes les machines jointes au domaine résident dans une unité d’organisation plate. Toutefois, vous pouvez choisir de créer des unités d’organisation personnalisées.
* Les services de domaine Azure AD prennent en charge une stratégie de groupe simple sous forme d’un objet de stratégie de groupe intégré pour les conteneurs d’utilisateurs et d’ordinateurs par défaut. Vous pouvez créer des objets GPO personnalisés et les cibler vers des unités d’organisation personnalisées.
* Les services de domaine Azure AD prennent en charge le schéma de l’objet de base de l’ordinateur AD. Vous ne pouvez pas étendre le schéma de l’objet de l’ordinateur.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Le transfert d’une application sur site qui utilise l’authentification de liaison LDAP pour les services d’Infrastructure Azure
![Liaison LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso dispose d’une application sur site qui a été achetée auprès d’un ISV il y a de nombreuses années. L’application est actuellement en mode de maintenance par l’ISV et nécessite des modifications hors de prix pour Contoso. Cette application possède un serveur frontal web qui collecte les informations d’identification de l’utilisateur à l’aide d’un formulaire web et authentifie ensuite les utilisateurs en effectuant une liaison LDAP vers l’annuaire Active Directory d’entreprise. Contoso souhaite migrer cette application vers les services d’infrastructure Azure. Il est préférable que l’application fonctionne en l’état, sans modification. En outre, les utilisateurs doivent pouvoir s’authentifier en utilisant leurs informations d’identification d’entreprise existantes, sans avoir à former les utilisateurs à une nouvelle procédure. En d’autres termes, les utilisateurs finaux doivent ignorer l’endroit depuis lequel l’application s’exécute, et la migration doit être transparente pour eux.

**Notes de déploiement**

Considérez ces quelques points importants pour ce scénario de déploiement :

* Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
* Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Les utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe en libre-service Azure AD, soit depuis le répertoire local. Ces modifications sont automatiquement synchronisées et disponibles dans le domaine géré.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Transfert d’une application locale qui utilise la lecture LDAP pour accéder à l’annuaire de service d’infrastructure Azure
Contoso possède une application métier locale, développée il y a presque dix ans. Cette application est orientée répertoire et a été conçue pour fonctionner avec Windows Server AD. L’application utilise le protocole LDAP (Lightweight Directory Access Protocol) pour lire les informations/attributs sur les utilisateurs à partir d’Active Directory. L’application ne modifie pas les attributs ou dans le cas contraire, écrit dans le répertoire. Contoso souhaite migrer cette application vers les services d’infrastructure Azure et mettre hors-service l’ancien matériel local qui héberge actuellement cette application. L’application ne peut pas être réécrite pour utiliser des API Active Directory modernes comme REST Azure AD Graph. Par conséquent, une option de transfert est souhaitée dans laquelle l’application peut être migrée pour s’exécuter dans le cloud, sans modification de code ou réécriture de l’application.

**Notes de déploiement**

Considérez ces quelques points importants pour ce scénario de déploiement :

* Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
* Assurez-vous que l’application ne nécessite pas un schéma Active Directory étendu/personnalisé. Les extensions de schéma ne sont pas prises en charge par les services de domaine Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migration d’une application de service ou démon local vers les services d’infrastructure Azure
Certaines applications sont constituées de plusieurs niveaux, dont l’un doit effectuer des appels authentifiés à un niveau principal, tel qu’une base de données. Les comptes de service Active Directory sont couramment utilisés pour ces cas d’utilisation. Vous pouvez transférer ces applications vers les services d’infrastructure Azure et utiliser les services de domaine Azure AD pour les besoins d’identité de ces applications. Vous pouvez choisir d’utiliser le même compte de service synchronisé depuis votre répertoire local vers Azure AD. Vous pouvez également commencer par créer une unité d’organisation personnalisée, puis créer un compte de service distinct dans cette unité d’organisation afin de déployer ces applications.

![Compte de service à l’aide de l’authentification intégrée de Windows](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso est équipé d’une application de coffre de logiciel personnalisée qui inclut un site web frontal, un serveur SQL et un serveur FTP principal. L’authentification intégrée de Windows des comptes de service est utilisée pour authentifier le site web frontal auprès du serveur FTP. Le serveur web frontal est configuré pour fonctionner comme un compte de service. Le serveur principal est configuré pour autoriser l’accès depuis le compte de service pour le serveur web frontal. Contoso préfère ne pas avoir à déployer une machine virtuelle de contrôleur de domaine dans le cloud pour déplacer cette application vers les services d’infrastructure Azure. L’administrateur informatique de Contoso peut déployer les serveurs hébergeant le site web frontal, le serveur SQL et le serveur FTP pour les machines virtuelles Azure. Ces machines sont ensuite jointes à un domaine géré par les services de domaine Azure AD. Elles pourront ensuite utiliser le même compte de service dans leur répertoire local pour les besoins d’authentification de l’application. Ce compte de service est synchronisé avec le domaine géré des services de domaine Azure AD et est disponible pour utilisation.

**Notes de déploiement**

Considérez ces quelques points importants pour ce scénario de déploiement :

* Assurez-vous que l’application utilise un ensemble nom d’utilisateur/mot de passe pour l’authentification. L’authentification basée sur un certificat/carte à puce n’est pas prise en charge par les services de domaine Azure AD.
* Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Les utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe en libre-service Azure AD, soit depuis le répertoire local. Ces modifications sont automatiquement synchronisées et disponibles dans le domaine géré.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Déploiements des services Bureau à distance Windows Server dans Azure
Vous pouvez utiliser les Services de domaine Azure AD pour fournir des services de domaine AD gérés à vos serveurs Bureau à distance déployés dans Azure.

Pour plus d’informations sur ce scénario de déploiement, consultez le guide pratique pour [intégrer les Services de domaine Azure AD à votre déploiement RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clusters HDInsight joints à un domaine (préversion)
Vous pouvez configurer un cluster Azure HDInsight qui est joint à un domaine géré Azure AD Domain Services avec Apache Ranger activé. Créez et appliquez les stratégies Hive via Apache Ranger et autorisez les utilisateurs (par exemple, les scientifiques de données) à se connecter à Hive à l’aide des outils ODBC, par exemple Excel, tableau, etc. Microsoft travaille à l’ajout d’autres charges de travail, comme HBase, Spark et Storm sur le cluster HDInsight joint à un domaine.

Pour plus d’informations sur ce scénario de déploiement, consultez [Configurer des clusters HDInsight joints à un domaine (préversion)](../hdinsight/domain-joined/apache-domain-joined-configure.md).
