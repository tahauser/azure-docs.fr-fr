---
title: Déplacement de données entre des bases de données cloud mises à l’échelle | Microsoft Docs
description: Explique comment manipuler les partitions et déplacer les données via un service auto-hébergé, à l'aide des API de base de données élastique.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 9e2b231ad2e9fc5ab07532daef44da9870cef4ae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Déplacement de données entre des bases de données cloud mises à l’échelle
Si vous êtes un développeur Software as a Service et que votre application connaît subitement une forte demande, vous devez vous adapter à cette croissance. Vous pouvez donc ajouter d’autres bases de données (partitions). Comment répartir les données vers les nouvelles bases de données sans nuire à l'intégrité des données ? Utilisez l’ **outil de fractionnement et de fusion** pour déplacer les données de bases de données limitées vers de nouvelles bases de données.  

L'outil de fractionnement et de fusion fonctionne comme un service web Azure. Grâce à cet outil, un administrateur ou un développeur déplace des shardlets (les données d'une partition) entre différentes bases de données (partitions). L'outil s’appuie sur la gestion de cartes de partitions pour gérer la base de données de métadonnées de service et garantir des mappages cohérents.

![Vue d'ensemble][1]

## <a name="download"></a>Download
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Documentation
1. [Didacticiel d’outil de fusion et de fractionnement de bases de données élastiques](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Configuration de la sécurité de la fusion et du fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Configuration de la sécurité de la fusion et du fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
5. [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Outils de base de données élastique](sql-database-elastic-scale-introduction.md)
7. [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Pourquoi utiliser l’outil de fractionnement et de fusion ?
**Flexibilité**

Les applications doivent étirer la flexibilité au-delà des limites d'une seule base de données SQL Azure. Utilisez l'outil pour déplacer les données nécessaires vers de nouvelles bases de données tout en garantissant leur intégrité.

**Fractionner pour augmenter** 

Vous devez augmenter la capacité globale pour gérer une croissance exponentielle. Pour cela, créez une capacité supplémentaire en partitionnant les données et en les répartissant de manière incrémentielle entre plusieurs bases de données jusqu'à ce que les besoins en capacité soient satisfaits. Il s'agit d'un parfait exemple de la fonction de fractionnement. 

**Fusionner pour réduire**

Les besoins en capacité varient en raison du caractère saisonnier de l'entreprise. L'outil vous permet de réduire à un nombre inférieur d'unités lorsque l'activité ralentit. La fonction « fusionner » du service de fusion et de fractionnement de l'infrastructure élastique traite cette exigence. 

**Gérer les zones réactives par déplacement des shardlets**

avec plusieurs locataires par base de données, l'allocation de shardlets aux partitions peut entraîner des goulots d'étranglement sur certaines partitions. Cela nécessite la réaffectation des shardlets ou le déplacement des shardlets occupés vers des partitions nouvelles ou moins utilisées. 

## <a name="concepts--key-features"></a>Concepts et fonctionnalités clés
**Services hébergés par le client**

Le service de fractionnement/fusion est fourni comme un service hébergé par le client. Vous devez déployer et héberger le service dans votre abonnement Microsoft Azure. Le package que vous téléchargez à partir de NuGet comporte un modèle de configuration à réaliser ainsi que les informations relatives à votre déploiement. Pour plus d'informations, consultez le [didacticiel du service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md) . Étant donné que le service s'exécute dans votre abonnement Azure, vous pouvez contrôler et configurer la plupart des aspects de sécurité du service. Le modèle par défaut comprend les options permettant de configurer SSL, l’authentification client basée sur certificat, le chiffrement des informations d’identification stockées, la protection contre le déni de service et les restrictions d’adresse IP. Vous trouverez plus d’informations sur la sécurité dans le document suivant [configuration de la sécurité de la fusion et du fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md).

Le service déployé par défaut s'exécute avec un rôle de travail et un rôle web. Chacun utilise la taille de machine virtuelle A1 dans Azure Cloud Services. Même si vous ne pouvez pas modifier ces paramètres lors du déploiement du package, vous pouvez les modifier après un déploiement dans le service cloud en cours d'exécution (via le portail Azure). Notez que le rôle de travail ne doit pas être configuré pour plus d'une instance unique pour des raisons techniques. 

**Intégration des cartes de partitions**

Le service de fractionnement/fusion interagit avec la carte de partitions de l'application. Lorsque vous utilisez le service de fusion et de fractionnement pour fractionner ou fusionner des plages ou déplacer des shardlets entre les partitions, le service conserve automatiquement la carte de partitions à jour. Pour ce faire, le service se connecte à la base de données du gestionnaire des cartes de partitions de l'application et gère les plages et les mappages au fur et à mesure de l'avancement des demandes de fractionnement/fusion/déplacement. Cela garantit que la carte de partitions présente toujours une vue à jour lorsque les opérations de fusion et de fractionnement sont en cours. Les opérations de fractionnement, fusion et déplacement des shardlets sont implémentées en déplaçant un lot de shardlets à partir de la partition source vers la partition cible. Au cours de l'opération de déplacement des shardlets, les shardlets du batch en cours sont marqués comme étant hors connexion dans la carte de partitions et ne sont pas disponibles pour les connexions de routage dépendant des données utilisant l'API **OpenConnectionForKey** . 

**Connexions cohérentes des shardlets**

lorsque le déplacement des données débute pour un nouveau lot de shardlets, les connexions du routage dépendant des données à la partition stockant le shardlet sont supprimées et les connexions ultérieures à partir des API de la carte de partitions à ces shardlets sont bloquées pendant le déplacement des données afin d'éviter les incohérences. Les connexions aux autres shardlets sur la même partition seront également supprimées mais réussiront immédiatement lors d'une nouvelle tentative. Une fois le lot déplacé, les shardlets sont marqués comme étant à nouveau en ligne pour la partition cible et la source des données est supprimée de la partition source. Le service effectue ces étapes pour chaque lot jusqu'à ce que tous les shardlets aient été déplacés. Cela entraîne plusieurs opérations de suppression des connexions au cours de l'opération de fractionnement/fusion/déplacement.  

**Gestion de la disponibilité des shardlets**

la limitation de la suppression des connexions au lot en cours de shardlets comme indiqué ci-dessus restreint la portée de l'indisponibilité à un lot de shardlets à la fois. Cette approche est préférée à celle selon laquelle la partition complète reste hors connexion pour tous ses shardlets au cours d'une opération de fusion et de fractionnement. La taille d'un lot, défini comme le nombre de shardlets distincts à déplacer à la fois, est un paramètre de configuration. Elle peut être définie pour chaque opération de fusion et de fractionnement en fonction des besoins en termes de disponibilité et de performances de l'application. Notez que la plage verrouillée dans la carte de partitions peut être supérieure à la taille de lot spécifiée. En effet, le service sélectionne la taille de la plage de telle sorte que le nombre réel de valeurs de clé de partitionnement contenu dans les données correspond environ à la taille du lot. Ce point est important, en particulier pour les clés de partitionnement peu remplies. 

**Stockage des métadonnées**

Le service de fractionnement/fusion utilise une base de données pour mettre à jour son état et conserver les journaux pendant le traitement des demandes. L'utilisateur crée cette base de données dans son abonnement et lui fournit la chaîne de connexion dans le fichier de configuration pour le déploiement du service. Les administrateurs de l’organisation de l’utilisateur peuvent également se connecter à cette base de données pour examiner la progression de la demande et pour analyser des informations détaillées sur les défaillances potentielles.

**Détection de partitionnements**

Le service de fractionnement/fusion fait la distinction entre (1) les tables partitionnées, (2) les tables de référence et (3) les tables normales. La sémantique d'une opération de fractionnement/fusion/déplacement dépend du type de la table utilisée et est définie comme suit : 

* **Tables partitionnées**: les opérations de fusion, de fractionnement et de déplacement déplacent les shardlets de la partition source vers la partition cible. Après l'achèvement réussi de la demande générale, ces shardlets ne sont plus présents dans la partition source. Notez que les tables cibles doivent figurer sur la partition cible et ne doivent pas contenir de données dans la plage cible avant le traitement de l'opération. 
* **Tables de référence**: pour les tables de référence, les opérations de fractionnement, de fusion et de déplacement copient les données de la partition source vers la partition cible. Notez, néanmoins, qu'aucune modification n'intervient sur la partition cible pour une table donnée si une ligne est déjà présente dans cette table de la cible. La table doit être vide pour qu’une opération de copie de table de référence soit traitée.
* **Autres tables**: d'autres tables peuvent être présentes sur la source ou la cible d'une opération de fusion et de fractionnement. Le service de fusion et de fractionnement ignore ces tables pour les opérations de copie ou de déplacement des données. Notez, toutefois, qu’ils peuvent interférer avec ces opérations en cas de contraintes.

Les informations de comparaison des tables de référence et des tables partitionnées sont fournies par les API **SchemaInfo** dans la carte de partitions. L'exemple suivant illustre l'utilisation de ces API sur un objet « smm » donné du gestionnaire des cartes de partitions : 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Les tables « région » et « nation » sont définies comme des tables de référence et sont copiées avec les opérations de fractionnement, fusion et déplacement. Les « clients » et les « commandes » sont à leur tour définis comme tables partitionnées. C_CUSTKEY et O_CUSTKEY servent de clé de partitionnement. 

**Intégrité référentielle**

Le service de fractionnement/fusion analyse les dépendances entre les tables et utilise des relations de clé primaire/clé étrangère pour préparer les opérations de déplacement des tables de référence et des shardlets. En général, les tables de référence sont d'abord copiées dans l'ordre de dépendance, puis les shardlets sont copiés dans l'ordre de leurs dépendances au sein de chaque lot. Cela est nécessaire afin que les contraintes de clé primaire/clé étrangère sur la partition cible soient respectées lorsque les nouvelles données arrivent. 

**Cohérence des cartes de partitions et achèvement éventuel**

En cas de défaillance, le service de fractionnement/fusion reprend les opérations après toute interruption et entreprend de terminer toutes les demandes en cours. Toutefois, des situations irrécupérables peuvent survenir, par exemple, lorsque la partition cible est perdue ou irréparable. Dans ces circonstances, certains shardlets qui devaient être déplacés peuvent continuer à se trouver sur la partition source. Le service garantit que les mappages de shardlets ne sont mis à jour qu'une fois que les données nécessaires ont été copiées avec succès sur la cible. Les shardlets ne sont supprimés de la source qu'une fois que toutes leurs données ont été copiées vers la cible et que les mappages correspondants ont été mis à jour. L'opération de suppression se produit en arrière-plan alors que la plage est déjà en ligne sur la partition cible. Le service de fusion et de fractionnement garantit systématiquement l’exactitude des mappages stockés dans la carte de partitions.

## <a name="the-split-merge-user-interface"></a>Interface utilisateur du service de fusion et de fractionnement
Le package de service de fractionnement et de fusion inclut un rôle de travail et un rôle web. Le rôle web sert à envoyer des demandes de fractionnement et de fusion de manière interactive. Les principaux composants de l’interface utilisateur sont les suivants :

* Type d’opération : le type d'opération est un bouton radio qui contrôle le type d'opération effectué par le service pour une demande. Vous pouvez choisir entre des scénarios de fractionnement, de fusion et de déplacement. Vous pouvez également annuler une opération précédemment soumise. Vous pouvez utiliser les demandes de fractionnement, de fusionnement et de déplacement pour les cartes de partition de plage. La liste de cartes de partition prend uniquement en charge les opérations de déplacement.
* Carte de partitions : la section suivante de paramètres de demande aborde les informations relatives à la carte de partitions et à la base de données qui l'héberge. En particulier, vous devez fournir le nom du serveur de base de données SQL Azure et de la base de données hébergeant la carte de partitions, les informations d'identification pour se connecter à la base de données de mappage et, enfin, le nom de la carte. Actuellement, l'opération n'accepte qu'un seul ensemble d'informations d'identification. Ces dernières doivent disposer d’autorisations suffisantes pour apporter des modifications à la carte de partitions ainsi qu’aux données utilisateur des partitions.
* Plage source (fractionnement et fusion) : une opération de fractionnement et de fusion traite une plage à l’aide de sa clé basse et haute. Pour spécifier une opération avec une valeur de clé haute illimitée, cochez la case « High key is max » et ne renseignez pas le champ de clé haute. Les valeurs de clé de plage que vous spécifiez n’ont pas besoin de correspondre précisément à un mappage et à ses limites dans votre table de partition. Si vous ne spécifiez aucune limite de plage, le service déduit la plage la plus proche pour vous automatiquement. Vous pouvez utiliser le script PowerShell GetMappings.ps1 pour récupérer les mappages actuels dans une carte de partitions donnée.
* Comportement de source fractionnée (fractionner) : pour les opérations de fractionnement, définissez le moment auquel vous souhaitez fractionner la plage source. Vous effectuez cette opération en fournissant la clé de partitionnement à l'emplacement où vous souhaitez effectuer le fractionnement. Utilisez la case d’option pour indiquer si vous souhaitez déplacer la partie inférieure de la plage (à l’exception de la clé de fractionnement) ou si vous souhaitez déplacer la partie supérieure (y compris la clé de fractionnement).
* Shardlet source (déplacer) : les opérations de déplacement sont différentes des opérations de fractionnement ou de fusion, car elles ne nécessitent pas de plage pour décrire la source. Une source de déplacement est simplement identifiée par la valeur de clé de partitionnement que vous souhaitez déplacer.
* Partition cible (fractionner) : une fois que vous avez fourni les informations sur la source de l’opération de fractionnement, vous devez définir l’emplacement de copie des données en fournissant le serveur de la base de données SQL Azure et le nom de la base de donnée correspondant à la cible.
* Plage cible (fusionner) : opérations de fusion pour déplacer des shardlets vers une partition existante. Vous identifiez la partition existante en fournissant les limites de la plage existante avec laquelle vous souhaitez fusionner.
* Taille du batch : la taille du batch contrôle le nombre de shardlets qui passera en mode hors connexion en même temps pendant le déplacement des données. Il s'agit d'une valeur entière vous permettant d'utiliser des valeurs plus petites lorsque vous êtes exposé à de longues périodes de temps mort pour les shardlets. Des valeurs plus grandes augmentent le temps pendant lequel un shardlet donné est hors connexion mais peuvent améliorer les performances.
* ID de l’opération (annuler) : si une opération en cours n'est plus nécessaire, vous pouvez l'annuler en fournissant son ID dans ce champ. Vous pouvez récupérer l’ID de l’opération dans la table des états de demande (voir Section 8.1) ou dans la sortie du navigateur Web dans lequel vous avez envoyé la demande.

## <a name="requirements-and-limitations"></a>Spécifications et limitations
L'implémentation actuelle du service de fractionnement et de fusion est soumise aux conditions requises et limitations suivantes : 

* Les partitions doivent exister et être enregistrées dans la carte de partitions pour qu’une opération de fractionnement et de fusion sur ces partitions puisse être effectuée. 
* Le service ne crée actuellement pas automatiquement des tables ou d’autres objets de base de données dans le cadre de ses opérations. Cela signifie que le schéma pour toutes les tables partitionnées et les tables de référence doit exister sur la partition cible avant toute opération de fractionnement/fusion/déplacement. Les tables partitionnées en particulier doivent être vides dans la plage où de nouveaux shardlets doivent être ajoutés par une opération de fractionnement/fusion/déplacement. Sinon, l'opération fait échouer la vérification de cohérence initiale sur la partition cible. Notez également que les données de référence ne sont copiées que si la table de référence est vide et qu'il n'existe aucune garantie de cohérence en ce qui concerne les autres opérations simultanées d'écriture sur les tables de référence. Nous recommandons, lors des opérations de fractionnement et de fusion, qu’il n’y ait aucune autre opération d’écriture apportant des modifications aux tables de références.
* Le service s’appuie sur l’identité de ligne définie par un index unique ou une clé incluant la clé de partitionnement pour améliorer les performances et la fiabilité des shardlets volumineux. Cela permet au service de déplacer des données à une granularité encore plus fine que la valeur de clé de partitionnement. Cela permet de réduire la quantité maximale de l'espace de journalisation ainsi que le nombre de verrous requis lors de l'opération. Envisagez de créer un index unique ou une clé primaire incluant la clé de partitionnement sur une table donnée si vous souhaitez utiliser cette table avec des demandes de fusion/fractionnement/déplacement. Pour des raisons liées aux performances, la clé de partitionnement doit être la première colonne de la clé ou de l’index.
* Au cours du traitement des demandes, des données de shardlet peuvent être présentes à la fois sur la partition source et sur la partition cible. Cette présence est nécessaire à la protection contre les défaillances pendant le déplacement de shardlets. L'intégration du service de fractionnement et de fusion à la carte de partitions permet de s'assurer que les connexions via les API de routage dépendant des données, utilisant la méthode **OpenConnectionForKey** sur la carte de partitions, ne constatent pas d'états intermédiaires incohérents. Toutefois, lors de la connexion aux partitions source ou cible sans utiliser la méthode **OpenConnectionForKey** , des états intermédiaires incohérents peuvent apparaître pendant l'exécution de demandes de fractionnement, de fusion et de déplacement. Ce type de connexion peut afficher des résultats partiels ou en double en fonction de la synchronisation ou de la partition sous-jacente à la connexion. Actuellement, cette limitation inclut les connexions établies par les interrogations de plusieurs partitions de l’infrastructure élastique.
* La base de données de métadonnées de fractionnement et de fusion ne doit pas être partagée entre différents rôles. Par exemple, un rôle du service de fractionnement et de fusion en cours d’exécution intermédiaire doit pointer vers une base de données de métadonnées différente du rôle de production.

## <a name="billing"></a>Facturation
Le service de fractionnement de fusion s'exécute comme un service cloud dans votre abonnement Microsoft Azure. Par conséquent les frais pour les services cloud s'appliquent à votre instance du service. Sauf si vous effectuez fréquemment des opérations de fractionnement, de fusion et de déplacement, nous vous recommandons de supprimer votre service cloud de fractionnement et de fusion. Vous économiserez ainsi en coûts d'exécution ou de déploiement d'instances de service cloud. Vous pouvez redéployer et démarrer votre configuration facilement exécutable chaque fois que vous avez besoin d'effectuer des opérations de fractionnement ou de  fusion. 

## <a name="monitoring"></a>Surveillance
### <a name="status-tables"></a>Tables d'états
Le service de fractionnement et de fusion fournit la table **RequestStatus** dans la base de données de stockage des métadonnées pour la surveillance des demandes terminées et en cours. La table répertorie une ligne pour chaque demande de fractionnement et de fusion qui a été soumise à cette instance du service de fractionnement et de fusion. Elle donne les informations suivantes pour chaque demande :

* **Timestamp**: heure et date de début de la demande.
* **OperationId**: GUID qui identifie de façon unique la demande. Cette demande peut également servir à annuler l’opération alors qu’elle est encore en cours.
* **Status**: état actuel de la demande. Pour les demandes en cours, répertorie également la phase dans laquelle la demande se trouve.
* **CancelRequest**: indicateur signalant si la demande a été annulée.
* **Progress**: estimation du pourcentage d'achèvement de l'opération. Une valeur de 50 indique que l’opération est terminée à environ 50 %.
* **Details**: valeur XML qui fournit un rapport de progression plus détaillé. Le rapport de progression est régulièrement mis à jour lorsque des ensembles de lignes sont copiés de la source vers la cible. En cas de défaillances ou d’exceptions, cette colonne inclut également des informations plus détaillées sur l’échec.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Le service de fusion et de fractionnement utilise l’Azure Diagnostics basé sur Azure SDK 2.5 pour la surveillance et les diagnostics. Vous contrôlez la configuration des diagnostics, comme indiqué ici : [Activation des diagnostics dans Azure Cloud Services et Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). Le package de téléchargement comprend deux configurations de diagnostic : un pour le rôle Web et un pour le rôle de travail. Ces configurations de diagnostics pour le service suivent les instructions des [Concepts de base de Cloud Service dans Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Elle inclut les définitions permettant d'enregistrer les compteurs de performances, les journaux IIS, les journaux des événements Windows et les journaux des événements de l'application de fractionnement et de fusion. 

## <a name="deploy-diagnostics"></a>Déployer des diagnostics
Pour activer la surveillance et le diagnostic à l'aide de la configuration de diagnostic pour les rôles Web et les rôles de travail fournis par le package NuGet, exécutez les commandes suivantes à l'aide d'Azure PowerShell : 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Vous trouverez plus d'informations sur la façon de configurer et de déployer les paramètres de diagnostic ici : [Activation de diagnostics dans Azure Cloud Services et Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Récupérer les diagnostics
Vous pouvez accéder facilement aux diagnostics à partir de l’Explorateur de serveurs Visual Studio dans la partie Azure de l’arborescence de l’Explorateur de serveurs : Ouvrez une instance de Visual Studio, puis dans la barre de menus, cliquez sur Affichage et Explorateur de serveurs. Cliquez sur l’icône Azure pour vous connecter à votre abonnement Azure. Ensuite, accédez à Azure -> Stockage -> <your storage account> -> Tables -> WADLogsTable. Pour plus d’informations, consultez la page [Consultation des ressources de stockage avec l’Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

La table WADLogsTable mise en surbrillance dans la figure ci-dessus comporte les événements détaillés du journal des applications du service de fractionnement et de fusion. Notez que la configuration par défaut fournie dans le cadre du package téléchargé est destinée à un déploiement de production. Par conséquent, l'intervalle auquel les journaux et les compteurs sont extraits par les instances du service est grand (5 minutes). Pour le test et le développement, réduisez l'intervalle en ajustant les paramètres de diagnostic du rôle Web ou du rôle de travail selon vos besoins. Effectuez cet ajustement en cliquant avec le bouton droit sur le rôle dans l’Explorateur de serveurs Visual Studio (voir ci-dessus), puis en ajustant la période de transfert dans la boîte de dialogue des paramètres de configuration de Diagnostics : 

![Configuration][3]

## <a name="performance"></a>Performances
En général, les meilleures performances proviennent de niveaux de service élevés, plus performants, de la base de données SQL Azure. Des allocations d'E/S, de processeur et de mémoire plus élevées pour les niveaux de service supérieurs sont bénéfiques aux opérations de copie et de suppression en bloc que le service de fractionnement et de fusion utilise. Pour cette raison, augmentez le niveau de service pour ces bases de données pour une période limitée et définie.

Le service effectue également des requêtes de validation dans le cadre de ses opérations. Ces requêtes de validation recherchent la présence inattendue de données dans la plage cible et garantissent que les opérations de fractionnement, de fusion et de déplacement démarrent à partir d'un état cohérent. Ces requêtes fonctionnent toutes sur des plages de clés de partitionnement définies par l'étendue de l'opération de fractionnement, de fusion et de déplacement et la taille du batch fourni dans le cadre de la définition de la demande. Ces requêtes offrent de meilleures performances lorsqu'un index est présent et doté de la clé de partitionnement en tant que première colonne. 

En outre, une propriété d'unicité avec la clé de partitionnement en tant que première colonne permet au service d'utiliser une approche optimisée qui limite la consommation de ressources en termes d'espace de journalisation et de mémoire. Cette propriété d'unicité est requise pour déplacer des tailles de données importantes (au-dessus de 1 Go). 

## <a name="how-to-upgrade"></a>Mise à niveau
1. Suivez les étapes de la rubrique [Déployer un service de fractionnement et de fusion](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Modifiez votre fichier de configuration de service cloud pour votre déploiement de fractionnement et de fusion afin de refléter les nouveaux paramètres de configuration. Les informations relatives au certificat utilisé pour le chiffrement sont un nouveau paramètre obligatoire. Un moyen simple de procéder consiste à comparer le nouveau fichier de modèle de configuration du téléchargement avec votre configuration existante. Assurez-vous que vous ajoutez les paramètres de « DataEncryptionPrimaryCertificateThumbprint » et « DataEncryptionPrimary » pour le rôle Web et de travail.
3. Avant de déployer la mise à jour vers Azure, assurez-vous que toutes les opérations de fractionnement et de fusion en cours d’exécution sont terminées. Vous pouvez le faire facilement en interrogeant les tables RequestStatus et PendingWorkflows dans la base de données de métadonnées de fractionnement et de fusion pour les demandes en cours.
4. Mettez à jour votre déploiement de service cloud existant pour le fractionnement et la fusion dans votre abonnement Azure avec le nouveau package et votre fichier de configuration de service mis à jour.

Il est inutile de configurer une nouvelle base de données de métadonnées de fractionnement et de fusion pour la mise à niveau. La nouvelle version mettra automatiquement à niveau votre base de données de métadonnées existante vers la nouvelle version. 

## <a name="best-practices--troubleshooting"></a>Meilleures pratiques et dépannage :
* Définissez un locataire test et exercez vos opérations les plus importantes de fractionnement, de fusion et de déplacement avec le locataire test sur plusieurs partitions. Assurez-vous que toutes les métadonnées sont définies correctement dans la carte de partitions et que les opérations ne violent pas les contraintes ou les clés étrangères.
* Maintenez la taille des données du locataire test au-dessus de la taille maximale des données de votre locataire le plus volumineux pour garantir que vous ne rencontrerez pas de problèmes liés à la taille des données. Cela vous aide à évaluer une limite supérieure relative au temps requis pour déplacer un seul locataire. 
* Assurez-vous que votre schéma autorise les suppressions. Le service de fractionnement et de fusion requiert la possibilité de supprimer les données de la partition source une fois que les données ont été copiées dans la partition cible. Par exemple, les **déclencheurs de suppression** peuvent empêcher le service de supprimer les données de la partition source et provoquer l'échec des opérations.
* La clé de partitionnement doit être la première colonne de la définition de la clé primaire ou de l’index unique. Cela garantit les meilleures performances pour les requêtes de validation de fractionnement ou de fusion ainsi que pour les opérations de déplacement et suppression de données réelles qui fonctionnent systématiquement sur des plages de clés de partitionnement.
* Colocalisez votre service de fractionnement et de fusion dans le centre de données et dans la région où résident vos bases de données. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

