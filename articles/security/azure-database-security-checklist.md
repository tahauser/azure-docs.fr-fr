---
title: "Liste de contrôle de la sécurité des bases de données Azure | Microsoft Docs"
description: "Cet article fournit un ensemble de listes de contrôle pour la sécurité des bases de données Azure."
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
ms.openlocfilehash: 11fa0d01463e398ae4106e1a70dc4c51b8698ff4
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-database-security-checklist"></a>Liste de contrôle de la sécurité des bases de données Azure

Pour aider à améliorer la sécurité, les bases de données Azure incluent un nombre de contrôles de sécurité intégrés que vous pouvez utiliser pour limiter et contrôler l’accès.

Vous avez notamment vu les points suivants :

-   Pare-feu qui vous permet de créer des [règles de pare-feu](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) limitant la connectivité par adresse IP
-   Pare-feu au niveau du serveur accessible depuis le portail Azure
-   Règles de pare-feu au niveau de la base de données accessibles à partir de SSMS
-   Connectivité sécurisée à votre base de données avec des chaînes de connexion sécurisées
-   Gestion de l’accès des utilisateurs
-   Chiffrement des données
-   Audit de base de données SQL
-   Détection de menaces pour les bases de données SQL

## <a name="introduction"></a>Introduction
Le cloud computing requiert de nouveaux modèles de sécurité avec lesquels de nombreux utilisateurs d’applications, administrateurs de bases de données et programmeurs ne sont pas familiarisés. Par conséquent, certaines organisations hésitent à implémenter une infrastructure de cloud pour la gestion des données en raison des risques de sécurité perçus. Pour une grande part toutefois, ces inquiétudes peuvent être calmées par une meilleure compréhension des fonctionnalités de sécurité intégrées dans Microsoft Azure et Microsoft Azure SQL Database.

## <a name="checklist"></a>Liste de contrôle
Nous vous recommandons de lire l’article [Azure Database Security Best Practices](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) (Meilleures pratiques en matière de sécurité des bases de données Azure) avant de passer cette liste de contrôle en revue. Vous pourrez tirer le meilleur parti de cette liste de contrôle après avoir découvert ces meilleures pratiques. Vous pourrez ensuite utiliser cette liste de contrôle pour vous assurer que vous avez géré les problèmes importants en matière de sécurité des bases de données Azure.


|Catégorie de la liste de contrôle| Description|
| ------------ | -------- |
|**Protection les données**||
| <br> Chiffrement en mouvement/transit| <ul><li>[Transport Layer Security](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), pour le chiffrement des données lorsqu’elles se déplacent vers les réseaux.</li><li>Les bases de données requièrent une communication sécurisée depuis les clients, avec l’utilisation du protocole [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) sur le protocole TLS (Transport Layer Security).</li></ul> |
|<br>Chiffrement au repos| <ul><li>[Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242), lorsque les données inactives sont stockées physiquement dans un format numérique.</li></ul>|
|**Contrôle des accès**||  
|<br> Accès à la base de données | <ul><li>[Authentification](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) AD (Azure Active Directory), qui utilise des identités gérées par Azure Active Directory.</li><li>[Autorisation](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access), pour accorder aux utilisateurs les privilèges minimum nécessaires.</li></ul> |
|<br>Accès aux applications| <ul><li>[Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131) (avec une stratégie de sécurité, tout en limitant l’accès au niveau des lignes selon l’identité des utilisateurs, leur rôle ou le contexte d’exécution).</li><li>[Masquage des données dynamiques](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) (avec autorisation et stratégie, limite l’exposition des données sensibles en les masquant pour les utilisateurs sans privilège).</li></ul>|
|**Surveillance proactive**||  
| <br>Suivi et détection| <ul><li>L’[audit](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) suit les événements de base de données et les écrit dans un journal d’audit ou d’activité dans votre [compte de stockage Azure](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Suivi de l’intégrité des bases de données Azure à l’aide des [journaux d’activité Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>La [détection des menaces](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. </li></ul> |
|<br>Azure Security Center| <ul><li>[Surveillance des données](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases), avec Azure Security Center comme solution de surveillance de la sécurité centralisée pour SQL et d’autres services Azure.</li></ul>|     

## <a name="conclusion"></a>Conclusion
Azure Database est une plateforme robuste de base de données, avec un éventail complet de fonctionnalités de sécurité qui répondent à nombreuses exigences en matière de conformité réglementaire et organisationnelles. Vous pouvez facilement protéger les données en contrôlant l’accès physique à vos données et à l’aide de diverses options de sécurité des données au niveau des fichiers, des colonnes ou des lignes avec le chiffrement transparent des données, le chiffrement au niveau des cellules ou de la sécurité au niveau des lignes. Always Encrypted active également des opérations sur les données chiffrées, ce qui simplifie le processus des mises à jour d’application. À son tour, l’accès aux journaux d’audit de l’activité SQL Database vous offre les informations dont vous avez besoin, ce qui vous permet de savoir quand et comment les utilisateurs ont accédé aux données.

## <a name="next-steps"></a>Étapes suivantes
Quelques étapes simples suffisent pour améliorer la protection d’une base de données contre les utilisateurs malveillants ou tout accès non autorisé. Ce didacticiel vous apprend à effectuer les opérations suivantes :

- Définir des [règles de pare-feu](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) pour votre serveur et/ou base de données.
- Protéger vos données à l’aide du [chiffrement](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Activer l’[audit Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

