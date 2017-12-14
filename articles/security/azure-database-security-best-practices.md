---
title: "Meilleures pratiques en matière de sécurité pour les bases de données Azure | Microsoft Docs"
description: "Cet article fournit un ensemble de meilleures pratiques pour la sécurité des bases de données Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: c8f49a778ab5e32108531cab05cab7f7eb092221
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-database-security-best-practices"></a>Meilleures pratiques en matière de sécurité pour les bases de données Azure

La sécurité est une préoccupation majeure lorsqu’il s’agit de gérer des bases de données, et cela a toujours été une priorité pour Azure SQL Database. Vos bases de données peuvent être étroitement sécurisées pour permettre de répondre aux exigences réglementaires ou de sécurité, notamment HIPAA, ISO 27001/27002 et PCI DSS Level 1. Une liste à jour des certifications de conformité en matière de sécurité est disponible sur le [site du Centre de gestion de la confidentialité Microsoft](http://azure.microsoft.com/support/trust-center/services/). Vous pouvez également choisir de placer vos bases de données dans des centres de données Azure spécifiques, en fonction des exigences réglementaires.

Dans cet article, nous aborderons différentes meilleures pratiques en matière de sécurité pour les bases de données Azure. Ces meilleures pratiques sont issues de notre expérience dans le domaine de la sécurité des bases de données Azure, mais également de celle des clients comme vous.

Pour chaque bonne pratique, nous détaillons les éléments suivants :

-   Nature de la bonne pratique
-   Raison pour laquelle activer cette bonne pratique
-   Conséquence possible en cas de non-utilisation de la bonne pratique
-   Comment apprendre à utiliser la meilleure pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles au moment de la rédaction du présent document. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Les meilleures pratiques en matière de sécurité pour les bases de données Azure qui sont abordées dans le cadre de cet article sont les suivantes :

-   Utiliser des règles de pare-feu pour restreindre l’accès aux bases de données
-   Activer l’authentification pour les bases de données
-   Protéger vos données à l’aide du chiffrement
-   Protection des données en transit
-   Activer l’audit pour les bases de données
-   Activer la détection des menaces pour les bases de données


## <a name="use-firewall-rules-to-restrict-database-access"></a>Utiliser des règles de pare-feu pour restreindre l’accès aux bases de données

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour assurer la sécurité des accès, SQL Database contrôle l’accès avec des règles de pare-feu qui limitent la connectivité par adresse IP, des mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité, et des mécanismes d’autorisation qui les restreignent à certaines actions et données. Le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.

![Règles de pare-feu](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Le service Azure SQL Database Azure n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu de votre ordinateur client autorise les communications TCP sortantes sur le port 1433. Si elles ne sont pas nécessaires pour les autres applications, bloquez les connexions entrantes sur le port TCP 1433 à l’aide de règles de pare-feu.

Dans le cadre du processus de connexion, les connexions à partir des machines virtuelles Azure sont redirigées vers une autre adresse IP et un autre port, propres à chaque rôle de travail. Le numéro du port est compris entre 11000 et 11999. Pour plus d’informations sur les ports TCP, consultez [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Pour en savoir plus sur les règles de pare-feu dans la base de données SQL, voir [Règles de pare-feu de la base de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Activer l’authentification pour les bases de données
SQL Database prend en charge deux types d’authentification : l’authentification SQL et l’authentification Azure Active Directory (Azure AD Authentication).

**L’authentification SQL** est recommandée dans les cas suivants :

-   Elle permet à SQL Azure prendre en charge des environnements avec des systèmes d’exploitation mixtes, dans lesquels tous les utilisateurs ne sont pas authentifiés par un domaine Windows.
-   Permet à SQL Azure de prendre en charge des applications anciennes et des applications tierces qui requièrent l’authentification SQL Server.
-   Permet aux utilisateurs de se connecter à partir de domaines inconnus ou non approuvés. Par exemple, une application où les clients établis se connectent via des connexions SQL Server assignées pour obtenir l’état de leurs commandes.
-   Permet à SQL Azure prendre en charge les applications web où les utilisateurs créent leur propre identité.
-   Permet aux développeurs de logiciels de distribuer leurs applications à l’aide d’une hiérarchie d’autorisations complexe basée sur des connexions SQL Server connues et prédéfinies.

> [!Note]
> Toutefois, l’authentification SQL Server ne peut pas utiliser le protocole de sécurité Kerberos.

Si vous utilisez **l’authentification SQL** vous devez :

-   Gérer les informations d’identification fortes vous-même.
-   Protéger les informations d’identification dans la chaîne de connexion.
-   (Éventuellement) Protéger les informations d’identification transmises sur le réseau à partir du serveur web vers la base de données. Pour plus d’informations, consultez [How To: Connect to SQL Server Using SQL Authentication in ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx) (Procédure : Se connecter à SQL Server à l’aide de l’authentification SQL dans ASP.NET 2.0).

**L’authentification Azure Active Directory** est un mécanisme servant à se connecter à Microsoft Azure SQL Database et à [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure AD, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations. Les avantages suivants sont inclus :

-   Il fournit une alternative à l’authentification SQL Server.
-   Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
-   Permet une rotation du mot de passe dans un emplacement unique.
-   Les clients peuvent gérer les autorisations de base de données à l’aide de groupes (AAD) externes.
-   Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
-   L’authentification Azure AD utilise les utilisateurs de base de données à relation contenant-contenu pour authentifier les identités au niveau de la base de données.
-   Azure AD prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Database.
-   L’authentification Azure AD prend en charge ADFS (fédération de domaine) ou l’authentification utilisateur natif/mot de passe pour un répertoire Azure Active Directory local sans synchronisation du domaine.
-   Azure AD prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification universelle Active Directory, et notamment Multi-Factor Authentication (MFA). MFA comprend une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile). Pour plus d’informations, voir [Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

-   Créer et renseigner Azure AD.
-   Facultatif : associer ou modifier le répertoire actif actuellement associé à votre abonnement Azure.
-   Créer un administrateur Azure Active Directory pour le serveur Azure SQL Server ou pour [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configurer vos ordinateurs clients.
-   Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD.
-   Se connecter à la base de données à l’aide des identités Azure AD.

Vous trouverez des informations détaillées [ici](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Protéger vos données à l’aide du chiffrement

La fonctionnalité [TDE (Transparent Data Encryption) d’Azure SQL Database](https://msdn.microsoft.com/library/dn948096.aspx) protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans qu’il soit nécessaire de modifier l’application. Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données.

Même lorsque l’ensemble du stockage est chiffré, il est très important de chiffrer la base de données également. Cela permet d’implémenter un mécanisme approfondi de défense visant à protéger les données. Si vous utilisez Azure SQL Database et souhaitez protéger des données sensibles telles que des numéros de carte de crédit ou de sécurité sociale (USA), vous pouvez chiffrer vos bases de données au moyen du chiffrement AES 256 bits, conforme à la norme FIPS 140-2, qui répond aux exigences de nombreuses normes du secteur (HIPAA, PCI, etc.).

Il est important de comprendre que les fichiers liés à [l’extension du pool de mémoires tampons](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) ne sont pas chiffrés quand une base de données est chiffrée à l’aide de TDE. Vous devez utiliser des outils de chiffrement au niveau du système de fichiers comme [BitLocker](https://technet.microsoft.com/library/cc732774) ou le [système de fichiers EFS](https://technet.microsoft.com/library/cc700811.aspx) pour les fichiers liés à l’extension du pool de mémoires tampons.

Comme un utilisateur autorisé (administrateur de sécurité ou de base de données, par exemple) peut accéder aux données même si la base de données est chiffrée via TDE, vous devez également suivre les recommandations ci-dessous :

-   Activez l’authentification SQL au niveau de la base de données.
-   Utilisez Azure AD Authentication à l’aide des [rôles RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Les utilisateurs et les applications doivent utiliser des comptes distincts pour l’authentification. De cette façon, vous pouvez limiter les autorisations accordées aux utilisateurs et aux applications et réduire les risques d’activité malveillante.
-   Implémentez la sécurité au niveau de la base de données en utilisant des rôles de base de données fixes (comme db_datareader ou db_datawriter). Vous pouvez aussi créer des rôles personnalisés pour votre application, afin d’accorder des autorisations explicites aux objets de base de données sélectionnés.

Vous pouvez également utiliser les méthodes de chiffrement des données suivantes :

-   [chiffrement au niveau des cellules](https://msdn.microsoft.com/library/ms179331.aspx) permet de chiffrer des colonnes spécifiques, voire des cellules de données, avec des clés de chiffrement différentes.
-   Chiffrement lors de l’utilisation à l’aide d’Always Encrypted : [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) permet aux clients de chiffrer les données sensibles des applications clientes et de ne jamais divulguer les clés de chiffrement au moteur de base de données (SQL Database ou SQL Server). Always Encrypted sépare ainsi les utilisateurs qui sont propriétaires des données (et peuvent les consulter) des utilisateurs qui gèrent les données (mais n’y ont pas accès).
-   Utilisation de la sécurité au niveau des lignes : la sécurité au niveau des lignes permet aux clients de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). Pour plus d’informations, consultez [Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Protection des données en transit
La protection des données en transit doit être un aspect essentiel de votre stratégie de protection des données. Comme les données transitent entre différents emplacements, dans les deux sens, nous vous recommandons d’utiliser systématiquement les protocoles SSL/TLS pour ces déplacements de données. Dans certains cas, vous souhaiterez isoler l’intégralité du canal de communication entre votre infrastructure locale et sur le cloud, via un réseau privé virtuel (VPN).

Pour les données qui transitent entre votre infrastructure locale et Azure, vous devez envisager le recours aux dispositifs de protection appropriés, comme HTTPS ou VPN.

Pour les organisations devant sécuriser l’accès à Azure à partir de plusieurs postes de travail locaux, utilisez la fonction [VPN de site à site d’Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Pour les organisations devant sécuriser l’accès à Azure à partir de stations de travail locales ou hors sites vers Azure, pensez à utiliser le [VPN de point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Les jeux de données volumineux peuvent être transmis par le biais d’une liaison réseau étendu haut débit dédiée, comme [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si vous choisissez d’utiliser ExpressRoute, vous pouvez également chiffrer les données au niveau des applications par le biais de [SSL/TLS](https://support.microsoft.com/kb/257591) ou d’autres protocoles, pour optimiser la protection.

Si vous interagissez avec Azure Storage via le portail Azure, toutes les transactions se produisent via HTTPS. L’[API de stockage REST](https://msdn.microsoft.com/library/azure/dd179355.aspx) par le biais de HTTPS peut également être utilisée pour interagir avec [Stockage Azure](https://azure.microsoft.com/services/storage/) et [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Les organisations qui ne parviennent pas à protéger les données en transit sont plus sensibles aux [attaques d’intercepteur](https://technet.microsoft.com/library/gg195821.aspx), aux [écoutes électroniques](https://technet.microsoft.com/library/gg195641.aspx) et au piratage de session. Ces attaques peuvent être la première étape d’un processus visant à accéder à des données confidentielles.

Pour en savoir plus sur l’option de VPN Azure, consultez l’article [Planification et conception de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Activer l’audit pour les bases de données
L’audit d’une instance du moteur de base de données SQL Server ou d’une base de données individuelle implique le suivi et la journalisation des événements qui se produisent sur le moteur de base de données. L’audit SQL Server vous permet de créer des audits de serveur, qui peuvent contenir des spécifications d’audit de serveur pour les événements au niveau du serveur et des spécifications d’audit de base de données pour les événements au niveau de la base de données. Les événements audités peuvent être écrits dans les journaux d’événements ou les fichiers d’audit.

Il existe plusieurs niveaux d’audit pour SQL Server, en fonction des exigences normatives ou gouvernementales de votre installation. L’audit SQL Server fournit les outils et processus nécessaires pour activer, stocker et afficher les audits sur différents objets de base de données et de serveur.

[L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) suit les événements de base de données et les écrit dans un journal d’audit de votre compte de stockage Azure.

L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

L’audit permet et facilite le respect de normes de conformité, mais il ne garantit pas cette conformité.

Pour plus d’informations sur l’audit de base de données et sur son activation, consultez l’article [Activer l’audit et la détection des menaces sur les serveurs SQL dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Activer la détection des menaces pour les bases de données
La détection des menaces SQL vous permet de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Vous recevrez une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes SQL Threat Detection fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Par exemple, l’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

Pour en savoir plus sur la façon de configurer la détection des menaces pour votre base de données dans le portail Azure, consultez [Détection de menaces pour les bases de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

De plus, la détection des menaces SQL intègre des alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Nous vous invitons à essayer ce service gratuitement pendant 60 jours.

Pour plus d’informations sur la détection des menaces pour les bases de données et sur son activation, consultez l’article [Activer l’audit et la détection des menaces sur les serveurs SQL dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Conclusion
Azure Database est une plateforme robuste de base de données, avec un éventail complet de fonctionnalités de sécurité qui répondent à de nombreuses exigences en matière de conformité réglementaire et organisationnelles. Vous pouvez protéger les données en contrôlant l’accès physique à vos données et à l’aide de diverses options de sécurité des données au niveau des fichiers, des colonnes ou des lignes avec Transparent Data Encryption, le chiffrement au niveau des cellules ou de la sécurité au niveau des lignes. Always Encrypted active également des opérations sur les données chiffrées, ce qui simplifie le processus des mises à jour d’application. À son tour, l’accès aux journaux d’audit de l’activité SQL Database vous offre les informations dont vous avez besoin, ce qui vous permet de savoir quand et comment les utilisateurs ont accédé aux données.

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les règles de pare-feu, consultez [Règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Pour en savoir plus sur les utilisateurs et les connexions, consultez [Gérer les connexions](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Pour obtenir un didacticiel, consultez [Sécuriser votre base de données Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
