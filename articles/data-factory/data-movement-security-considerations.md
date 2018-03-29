---
title: Considérations en matière de sécurité dans Azure Data Factory | Microsoft Docs
description: Décrit l’infrastructure de sécurité de base qu’utilisent les services de déplacement des données dans Azure Data Factory pour sécuriser vos données.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: abnarain
ms.openlocfilehash: 56602e269a441f9541314424190da04be2c4add5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Considérations de sécurité relatives au déplacement des données dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-data-movement-security-considerations.md)
> * [Version 2 - Préversion](data-movement-security-considerations.md)

Cet article décrit l’infrastructure de sécurité de base qu’utilisent les services de déplacement des données dans Azure Data Factory pour vous aider à sécuriser vos données. Les ressources de gestion Data Factory reposent sur l’infrastructure de sécurité Azure et utilisent toutes les mesures de sécurité possibles proposées par Azure.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Considérations en matière de sécurité des déplacements de données pour Data Factory version 1](v1/data-factory-data-movement-security-considerations.md).

Dans une solution Data Factory, vous créez un ou plusieurs [pipelines](concepts-pipelines-activities.md) de données. Un pipeline constitue un regroupement logique d’activités qui exécutent ensemble une tâche. Ces pipelines se trouvent dans la région où la fabrique de données a été créée. 

Même si Data Factory est disponible uniquement dans les régions États-Unis de l’Est, États-Unis de l’Est 2 et Europe de l’Ouest (version 2 préversion), le service de déplacement des données est disponible [globalement dans plusieurs régions](concepts-integration-runtime.md#azure-ir). Si le service de déplacement des données n’est pas encore déployé dans cette région, le service Data Factory s’assure que les données ne quittent pas cette zone ou région géographique à moins que vous ne demandiez explicitement au service d’utiliser une autre région. 

Azure Data Factory ne stocke aucune donnée à l’exception des informations d’identification du service lié pour les banques de données cloud, chiffrées à l’aide de certificats. Grâce à Data Factory, vous créez des workflows pilotés par les données afin d’orchestrer le déplacement de données entre les [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats), ainsi que le traitement des données à l’aide des [services de calcul](compute-linked-services.md) situés dans d’autres régions ou dans un environnement local. Il vous permet également analyser et gérer des workflows au moyen de Kits de développement logiciel (SDK) et d’Azure Monitor.

Le déplacement des données à l’aide de Data Factory a obtenu les certifications suivantes :
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Si la conformité Azure vous intéresse et que vous désirez savoir comment Azure sécurise sa propre infrastructure, consultez le [Centre de confidentialité Microsoft](https://www.microsoft.com/trustcenter).

Cet article présente les principes de sécurité à prendre en compte dans les deux scénarios de déplacement de données suivants : 

- **Scénario cloud** : dans ce scénario, votre source et votre destination sont toutes deux accessibles publiquement via Internet. Cela inclut les services de stockage cloud managés comme le stockage Azure, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, les services SaaS tels que Salesforce et les protocoles Web tels que FTP et OData. Recherchez une liste complète des sources de données prises en charge dans [Banques de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scénario hybride** : dans ce scénario, votre source ou votre destination est derrière un pare-feu ou à l’intérieur d’un réseau d’entreprise local. Ou bien, la banque de données est un réseau ou un réseau virtuel (le plus souvent la source) et n’est pas accessible publiquement. Les serveurs de base de données hébergés sur des machines virtuelles sont également inclus dans ce scénario.

## <a name="cloud-scenarios"></a>Scénarios cloud

### <a name="securing-data-store-credentials"></a>Sécurisation des informations d’identification des banques de données

- **Stocker les informations d’identification chiffrées dans un magasin managé Azure Data Factory**. Data Factory permet de protéger les informations d’identification de vos banques de données en les chiffrant à l’aide de certificats managés par Microsoft. Ces certificats sont remplacés tous les deux ans (avec renouvellement des certificats et migration des informations d’identification). Ces informations d’identification chiffrées sont stockées de manière sécurisée dans un compte de stockage Azure managé par les services de gestion Azure Data Factory. Pour plus d’informations sur la sécurité du stockage Azure, consultez [Vue d’ensemble de la sécurité du stockage Azure](../security/security-storage-overview.md).
- **Stocker les informations d’identification dans Azure Key Vault**. Vous pouvez également stocker les informations d’identification de la banque de données dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory récupère les informations d’identification lors de l’exécution d’une activité. Pour plus d’informations, consultez [Store credential in Azure Key Vault](store-credentials-in-key-vault.md) (Stocker les informations d’identification dans Azure Key Vault).

### <a name="data-encryption-in-transit"></a>Chiffrement des données en transit
Tous les transferts de données entre les services de déplacement des données dans Data Factory et une banque de données cloud s’effectuent via un canal HTTPS ou TLS sécurisé, si la banque de données cloud prend en charge HTTPS ou TLS.

> [!NOTE]
> Toutes les connexions à Azure SQL Database et à Azure SQL Data Warehouse doivent être chiffrées (via SSL/TLS) lorsque les données sont en transit depuis et vers la base de données. Lorsque vous créez un pipeline à l’aide de JSON, ajoutez la propriété de chiffrement et définissez sa valeur sur **true** dans la chaîne de connexion. Pour le stockage Azure, vous pouvez utiliser **HTTPS** dans la chaîne de connexion.

### <a name="data-encryption-at-rest"></a>Chiffrement des données au repos
Certaines banques de données prennent en charge le chiffrement des données au repos. Nous vous recommandons d’activer le mécanisme de chiffrement des données pour ces banques de données. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data Encryption (TDE) de Microsoft Azure SQL Data Warehouse vous aide à vous protéger contre les menaces d’activités malveillantes, par le biais d’un chiffrement et d’un déchiffrement en temps réel de vos données au repos. Ce comportement est transparent pour le client. Pour plus d’informations, consultez [Sécuriser une base de données dans SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de données SQL Azure
Azure SQL Database prend également en charge TDE (Transparent Data Encryption) qui vous permet de vous protéger contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel des données, sans qu’il soit nécessaire de modifier l’application. Ce comportement est transparent pour le client. Pour plus d’informations, consultez [Transparent Data Encryption avec SQL Database et Data Warehouse](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store assure également le chiffrement des données stockées dans le compte. Une fois activé, Data Lake Store chiffre automatiquement les données avant qu’elles soient rendues persistantes, il les déchiffre avant qu’elles soient récupérées, les rendant transparentes pour le client qui accède aux données. Pour plus d’informations, consultez [Sécurité dans Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Stockage Blob Azure et stockage Table Azure
Le stockage Blob Azure et le stockage Table Azure prennent en charge le SSE (Storage Service Encryption), qui chiffre automatiquement vos données avant qu’elles ne soient persistantes dans le stockage et les déchiffre avant leur récupération. Pour plus d’informations, consultez [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 prend en charge le chiffrement des données au repos côté client et côté serveur. Pour plus d’informations, consultez [Protection des données à l’aide du chiffrement](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift prend en charge le chiffrement du cluster pour les données au repos. Pour plus d’informations, consultez [Amazon Redshift Database Encryption (Chiffrement de base de données Amazon Redshift)](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce prend en charge Shield Platform Encryption qui permet de chiffrer tous les fichiers, pièces jointes et champs personnalisés. Pour plus d’informations, consultez [Understanding the Web Server OAuth Authentication Flow (Comprendre le flux d’authentification Web Server OAuth)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Scénarios hybrides
Pour les scénarios hybrides, un runtime d’intégration auto-hébergé doit être installé au sein d’un réseau local, d’un réseau virtuel (Azure) ou d’un cloud privé virtuel (Amazon). Le runtime d’intégration auto-hébergé doit être en mesure d’accéder aux banques de données locales. Pour plus d’informations sur le runtime d’intégration auto-hébergé, consultez [Comment créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime). 

![canaux de runtime d’intégration auto-hébergé](media/data-movement-security-considerations/data-management-gateway-channels.png)

Le canal de commande autorise la communication entre les services de déplacement des données dans Data Factory et le runtime d’intégration auto-hébergé. La communication contient des informations relatives à l’activité. Le canal de données est utilisé pour transférer des données entre les banques de données locales et les banques de données cloud.    

### <a name="on-premises-data-store-credentials"></a>Informations d’identification des banques de données locales
Les informations d’identification associées à vos banques de données locales sont toujours chiffrées et locales. Elles peuvent être stockées localement sur la machine du runtime d’intégration auto-hébergé ou dans le stockage managé Azure Data Factory (à l’instar des informations d’identification du magasin cloud). 

- **Stocker des informations d’identification localement**. Si vous souhaitez chiffrer et stocker des informations d’identification localement sur le runtime d’intégration auto-hébergé, suivez les étapes sur [Chiffrer des informations d’identification pour les banques de données locales dans Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Tous les connecteurs prennent en charge cette option. Le runtime d’intégration auto-hébergé utilise [l’API de protection des données (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) Windows pour chiffrer les données sensibles et les informations d’identification. 

   Utilisez la cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** pour chiffrer les informations d’identification et les informations sensibles du service lié. Vous pouvez ensuite utiliser le JSON retourné (avec l’élément **EncryptedCredential** dans la chaîne de connexion) pour créer un service lié à l’aide de la cmdlet **Set-AzureRmDataFactoryV2LinkedService**.  

- **Stocker dans le stockage managé Azure Data Factory**. Si vous utilisez directement la cmdlet **Set-AzureRmDataFactoryV2LinkedService** avec les chaînes de connexion et les informations d’identification incluses dans le JSON, le service lié est chiffré et stocké dans le stockage managé Azure Data Factory. Les informations sensibles sont toujours chiffrées par le certificat et Microsoft gère ces certificats.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Ports utilisés pendant le chiffrement du service lié sur le runtime d’intégration auto-hébergé
Par défaut, PowerShell utilise le port 8050 sur la machine disposant du runtime d’intégration auto-hébergé pour garantir une communication sécurisée. Ce port peut être modifié en cas de besoin.  

![Port HTTPS pour la passerelle](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Chiffrement en transit
Tous les transferts de données s’effectuent via un canal sécurisé HTTPS et TLS via TCP pour empêcher les attaques de l’intercepteur pendant la communication avec les services Azure.

Vous pouvez également utiliser un [VPN IPSec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Azure ExpressRoute](../expressroute/expressroute-introduction.md) pour renforcer la sécurité du canal de communication entre votre réseau local et Azure.

Le réseau virtuel Azure est une représentation logique de votre réseau dans le cloud. Vous pouvez connecter un réseau local à votre réseau virtuel Azure en configurant VPN IPSec (de site à site) ou ExpressRoute (homologation privée).    

Le tableau suivant récapitule les recommandations pour la configuration du réseau et du runtime d’intégration auto-hébergé selon différentes combinaisons d’emplacements source et de destination pour le déplacement de données hybrides.

| Source      | Destination                              | Configuration réseau                    | Installation du runtime d’intégration                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Local | Machines virtuelles et services cloud déployés au sein de réseaux virtuels | VPN IPSec (de point à site ou de site à site) | Le runtime d’intégration auto-hébergé peut être installé en local ou sur une machine virtuelle Azure au sein d’un réseau virtuel. |
| Local | Machines virtuelles et services cloud déployés au sein de réseaux virtuels | ExpressRoute (homologation privée)           | Le runtime d’intégration auto-hébergé peut être installé en local ou sur une machine virtuelle Azure au sein d’un réseau virtuel. |
| Local | Services Azure disposant d’un point de terminaison public | ExpressRoute (homologation publique)            | Le runtime d’intégration auto-hébergé doit être installé en local. |

Les images suivantes décrivent l’utilisation du runtime d’intégration auto-hébergé pour le déplacement de données entre une base de données locale et les services Azure à l’aide d’ExpressRoute et d’un VPN IPSec (avec un réseau virtuel) :

**ExpressRoute**

![Utiliser ExpressRoute avec passerelle](media/data-movement-security-considerations/express-route-for-gateway.png) 

**VPN IPSec**

![VPN IPSec avec passerelle](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-addresses"></a>Configurations de pare-feu et des adresses IP de mise en liste verte

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Configuration requise du pare-feu pour un réseau local/privé  
Dans une entreprise, un pare-feu d’entreprise s’exécute sur le routeur central de l’organisation. Le pare-feu Windows s’exécute en tant que démon sur la machine locale sur laquelle est installé le runtime d’intégration auto-hébergé. 

Le tableau suivant indique les exigences de ports et de domaines sortants pour les pare-feu d’entreprise :

| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter aux services de déplacement des données dans Data Factory. |
| `*.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure lorsque vous utilisez la fonctionnalité [copie intermédiaire](copy-activity-performance.md#staged-copy). |
| `*.frontend.clouddatahub.net` | 443            | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. |
| `*.database.windows.net`      | 1433           | (Facultatif) Nécessaire lorsque vous copiez depuis ou vers Azure SQL Database ou Azure SQL Data Warehouse. Utilisez la fonctionnalité copie intermédiaire pour copier des données vers Azure SQL Database ou Azure SQL Data Warehouse sans ouvrir le port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Facultatif) Nécessaire lorsque vous copiez depuis ou vers Azure Data Lake Store. |

> [!NOTE] 
> Vous devrez peut-être gérer les ports ou les domaines de mise en liste verte au niveau du pare-feu d’entreprise tel que requis par les sources de données respectives. Ce tableau utilise uniquement Azure SQL Database, Azure SQL Data Warehouse et Azure Data Lake Store comme exemples.   

Le tableau suivant indique les exigences de ports entrants pour le pare-feu Windows :

| Ports entrants | Description                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Requis par la cmdlet de chiffrement PowerShell comme décrit dans la rubrique[Chiffrer des informations d’identification pour des banques de données locales dans Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md) et par l’application du gestionnaire des informations d’identification pour définir en toute sécurité les informations d’identification pour les banques de données locales sur le runtime d’intégration auto-hébergé. |

![Configuration requise des ports de la passerelle](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Configurations IP et mise en liste verte dans des banques de données
Certaines banques de données dans le cloud exigent également que mettiez sur liste verte l’adresse IP de la machine qui accède au magasin. Vérifiez que l’adresse IP de la machine runtime d’intégration auto-hébergé est mise en liste verte ou configurée correctement dans le pare-feu.

Les banques de données cloud suivantes exigent que vous mettiez sur liste verte l’adresse IP de la machine runtime d’intégration auto-hébergé. Il est possible que certaines de ces banques de données ne requièrent pas par défaut la mise en liste verte des adresses IP. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Le runtime d’intégration auto-hébergé peut-il être partagé entre différentes fabriques de données ?**

Nous ne prenons pas encore en charge cette fonctionnalité. mais y travaillons activement.

**Quelle sont les exigences de ports pour assurer un bon fonctionnement du runtime d’intégration auto-hébergé ?**

Le runtime d’intégration auto-hébergé établit des connexions HTTP pour l’accès à Internet. Les ports sortants 443 et 80 doivent être ouverts pour que le runtime d’intégration auto-hébergé puisse établir cette connexion. Ouvrez le port entrant 8050 uniquement au niveau de la machine (et non au niveau du pare-feu d’entreprise) pour l’application du gestionnaire des informations d’identification. Si vous utilisez Azure SQL Database ou Azure SQL Data Warehouse comme source ou destination, vous devez également ouvrir le port 1433 également. Pour en savoir plus, consultez la section [Configurations du pare-feu et adresses IP de mise en liste verte](#firewall-configurations-and-whitelisting-ip-address-of-gateway). 


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les performances de l’activité de copie Azure Data Factory, consultez [Guide des performances et d’optimisation de l'activité de copie](copy-activity-performance.md).

 
