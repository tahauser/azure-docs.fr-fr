---
title: "Développement local avec l’émulateur Azure Cosmos DB | Microsoft Docs"
description: "L’émulateur Azure Cosmos DB vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais."
services: cosmos-db
documentationcenter: 
keywords: "Émulateur Azure Cosmos DB"
author: David-Noble-at-work
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: danoble
ms.openlocfilehash: 1991157330f6607efcf42ad42694c6b4d19fe609
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Utilisation de l’émulateur Azure Cosmos DB pour le développement local et le test

<table>
<tr>
  <td><strong>Fichiers binaires</strong></td>
  <td>[Télécharger MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Source Docker</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
L’émulateur Azure Cosmos DB fournit un environnement local qui émule le service Azure Cosmos DB à des fins de développement. L’émulateur Azure Cosmos DB vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos DB, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

> [!NOTE]
> Pour le moment, l’Explorateur de données de l’émulateur ne prend en charge que les collections d’API SQL et MongoDB. Les conteneurs Cassandra, Graph et Table ne sont pas entièrement pris en charge. 

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Installation de l’émulateur
> * Authentification des demandes
> * Utilisation de l’Explorateur de données dans l’émulateur
> * Exportation de certificats SSL
> * Appel de l’émulateur à partir de la ligne de commande
> * Exécution de l’émulateur sur Docker pour Windows
> * Collecte des fichiers de trace
> * Résolution de problèmes

Nous vous recommandons de commencer par visionner la vidéo suivante, où Kirill Gavrylyuk montre comment prendre en main l’émulateur Azure Cosmos DB. Notez que la vidéo fait référence à l’émulateur en tant qu’émulateur de DocumentDB, mais que l’outil proprement dit a été renommé Émulateur d’Azure Cosmos DB depuis l’enregistrement de la vidéo. Toutes les informations contenues dans la vidéo restent exactes pour l’émulateur d’Azure Cosmos DB. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Fonctionnement de l’émulateur
L’émulateur Azure Cosmos DB fournit une émulation haute fidélité du service Azure Cosmos DB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, notamment la prise en charge de la création et de l’interrogation des documents JSON, la configuration et la mise à l’échelle des collections, et l’exécution des procédures stockées et des déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos DB et les déployer vers Azure à l’échelle mondiale en apportant une seule modification à la configuration du point de terminaison de connexion pour Azure Cosmos DB.

Nous avons créé une émulation locale haute fidélité du service Azure Cosmos DB réel, mais l’implémentation de l’émulateur Azure Cosmos DB est différente de celle du service. Par exemple, l’émulateur Azure Cosmos DB utilise les composants du système d’exploitation standard, notamment le système de fichiers local pour la persistance, et la pile de protocole HTTPS pour la connectivité. Cela signifie que certaines fonctionnalités qui s’appuient sur l’infrastructure Azure, comme la réplication globale, la latence en quelques millisecondes pour les lectures/écritures et les niveaux de cohérence ajustables ne sont pas disponibles via l’émulateur Azure Cosmos DB.

## <a name="differences-between-the-emulator-and-the-service"></a>Différences entre l’émulateur et le service 
L’émulateur Azure Cosmos DB étant un environnement émulé exécuté sur une station de travail pour développeur locale, il existe des différences de fonctionnalités entre l’émulateur et un compte Azure Cosmos DB dans le cloud :

* L’émulateur Azure Cosmos DB prend en charge uniquement un compte fixe et une clé principale connue.  La régénération de clé n’est pas possible dans l’émulateur Azure Cosmos DB.
* L’émulateur Azure Cosmos DB n’est pas un service de stockage évolutif et ne prend pas en charge un grand nombre de collections.
* L’émulateur Azure Cosmos DB ne simule pas différents [niveaux de cohérence Azure Cosmos DB](consistency-levels.md).
* L’émulateur Azure Cosmos DB ne simule pas la [réplication dans plusieurs régions](distribute-data-globally.md).
* L’émulateur Azure Cosmos DB ne prend pas en charge les substitutions de quota de service disponibles dans le service Azure Cosmos DB (par exemple, les limites de taille de document ou l’augmentation du stockage d’une collection partitionnée).
* Votre copie de l’émulateur Azure Cosmos DB n’a peut-être pas été mise à jour avec les dernières modifications apportées au service Azure Cosmos DB. Nous vous conseillons donc de consulter la section [Planificateur de capacité Azure Cosmos DB](https://www.documentdb.com/capacityplanner) pour évaluer avec précision les besoins en débit de production (RU) de votre application.

## <a name="system-requirements"></a>Conditions requises pour le système
L’émulateur Azure Cosmos DB nécessite la configuration matérielle et logicielle suivante :

* Configuration logicielle requise
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
*   Conditions matérielles minimales requises
  * 2 Go de RAM
  * 10 Go d’espace disque disponible

## <a name="installation"></a>Installation
Vous pouvez télécharger et installer l’émulateur Azure Cosmos DB à partir du [Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator), ou vous pouvez exécuter l’émulateur sur Docker pour Windows. Pour obtenir des instructions sur l’utilisation de l’émulateur sur Docker pour Windows, consultez [Exécution sur Docker](#running-on-docker). 

> [!NOTE]
> Pour installer, configurer et exécuter l’émulateur Azure Cosmos DB, vous devez disposer de privilèges d’administrateur sur l’ordinateur.

## <a name="running-on-windows"></a>Exécution sur Docker

Pour démarrer l’émulateur Azure Cosmos DB, sélectionnez le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper **Émulateur Azure Cosmos DB**, puis sélectionnez l’émulateur dans la liste des applications. 

![Sélectionnez le bouton Démarrer ou appuyez sur la touche Windows, commencez à taper **Azure Cosmos DB**, puis sélectionnez l’émulateur dans la liste des applications](./media/local-emulator/database-local-emulator-start.png)

Lorsque l’émulateur est en cours d’exécution, une icône est affichée dans la zone de notification de la barre des tâches Windows. ![Notification dans la barre des tâches de l'émulateur local Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

L’émulateur Azure Cosmos DB s’exécute par défaut sur l’ordinateur local (« localhost ») et écoute sur le port 8081.

L’émulateur Azure Cosmos DB est installé par défaut dans le répertoire `C:\Program Files\Azure Cosmos DB Emulator`. Vous pouvez également démarrer et arrêter l’émulateur à partir de la ligne de commande. Pour plus d’informations, consultez la section [Référence de l’outil en ligne de commande](#command-line).

## <a name="start-data-explorer"></a>Démarrer l’Explorateur de données

Au démarrage de l’émulateur Azure Cosmos DB, l’Explorateur de données Azure Cosmos DB s’ouvre automatiquement dans votre navigateur. L’adresse apparaît sous la forme [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si vous fermez l’explorateur et que vous souhaitez le rouvrir ultérieurement, vous pouvez ouvrir l’URL dans votre navigateur ou la lancer à partir de l’émulateur Azure Cosmos DB dans l’icône de la barre d’état système Windows, comme indiqué ci-dessous.

![Lanceur de l’Explorateur de données de l’émulateur local Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Recherche de mises à jour
L’Explorateur de données indique si une nouvelle mise à jour est disponible en téléchargement. 

> [!NOTE]
> Il n’est pas garanti que vous puissiez accéder aux données créées dans une version de l’émulateur Azure Cosmos DB à partir d’une autre version. Si vous devez rendre vos données persistantes à long terme, nous vous recommandons de stocker ces données dans un compte Azure Cosmos DB plutôt que dans l’émulateur Azure Cosmos DB. 

## <a name="authenticating-requests"></a>Authentification des demandes
Tout comme avec Azure Cosmos DB dans le cloud, chaque demande que vous effectuez auprès de l’émulateur Azure Cosmos DB doit être authentifiée. L’émulateur Azure Cosmos DB prend en charge uniquement un compte fixe et une clé d’authentification connue pour l’authentification de la clé principale. Ce compte et cette clé sont les seules informations d’identification autorisées pour une utilisation avec l’émulateur Azure Cosmos DB. Il s'agit de :

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La clé principale prise en charge par l’émulateur Azure Cosmos DB est destinée à être utilisée uniquement avec l’émulateur. Vous ne pouvez pas utiliser votre compte et votre clé Azure Cosmos DB de production avec l’émulateur Azure Cosmos DB. 

> [!NOTE] 
> Si vous avez démarré l’émulateur avec l’option /Key, utilisez la clé générée au lieu de « C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== »

En outre, comme pour le service Azure Cosmos DB, l’émulateur Azure Cosmos DB prend en charge uniquement une communication sécurisée via SSL.

## <a name="running-on-a-local-network"></a>Exécution sur un réseau local

Vous pouvez exécuter l’émulateur sur un réseau local. Pour activer l’accès réseau, spécifiez l’option /AllowNetworkAccess à la [ligne de commande](#command-line-syntax) qui nécessite également que vous indiquiez /KEY = key_string ou/keyfile = file_name. Vous pouvez initialement utiliser /GenKeyFile = nom_fichier pour générer un fichier avec une clé aléatoire.  Passez ensuite à/keyfile = nom_fichier ou/Key = contents_of_file.

Pour activer l’accès réseau pour la première fois, l’utilisateur doit arrêter l’émulateur et supprimer son répertoire de données (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Développement avec l’émulateur
Une fois que l’émulateur Azure Cosmos DB est exécuté sur votre bureau, vous pouvez utiliser n’importe quel [SDK Azure Cosmos DB](sql-api-sdk-dotnet.md) pris en charge ou [l’API REST Azure Cosmos DB](/rest/api/documentdb/) pour interagir avec lui. L’émulateur Azure Cosmos DB inclut également un Explorateur de données intégré qui vous permet de créer des collections pour les API SQL et MongoDB, ainsi que d’afficher et de modifier des documents sans avoir à écrire de code.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Si vous utilisez la [prise en charge du protocole Azure Cosmos DB pour MongoDB](mongodb-introduction.md), utilisez la chaîne de connexion suivante :

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Vous pouvez utiliser les outils existants comme [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) pour vous connecter à l’émulateur Azure Cosmos DB. Vous pouvez également migrer des données entre l’émulateur Azure Cosmos DB et le service Azure Cosmos DB à l’aide de [l’outil de migration de données Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Si vous avez démarré l’émulateur avec l’option /Key, utilisez la clé générée au lieu de « C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== »

À l’aide de l’émulateur Azure Cosmos DB, vous pouvez, par défaut, créer jusqu’à 25 collections à partition unique ou 1 collection partitionnée. Pour plus d’informations sur la modification de cette valeur, voir la section relative à la [définition de la valeur PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exporter le certificat SSL

Les langages .NET et le runtime utilisent le magasin de certificats Windows pour se connecter en toute sécurité à l’émulateur local Azure Cosmos DB. D’autres langages ont leur propre méthode de gestion et de l’utilisation des certificats. Java utilise son propre [magasin de certificats](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), tandis que Python utilise des [wrappers de socket](https://docs.python.org/2/library/ssl.html).

Pour obtenir un certificat à utiliser avec les langages et les runtimes qui ne s’intègrent pas au magasin de certificats Windows, vous devrez l'exporter à l’aide du Gestionnaire de certificats Windows. Vous pouvez le démarrer en exécutant le fichier certlm.msc ou suivre les instructions étape par étape de la publication [Exporter les certificats de l’émulateur Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Une fois le certificat en cours d’exécution, ouvrez les certificats personnels comme indiqué ci-dessous et exportez le certificat avec le nom convivial "DocumentDBEmulatorCertificate" en tant que fichier X.509 (.cer) codé en Base 64.

![Certificat SSL de l’émulateur local Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Le certificat X.509 peut être importé dans le magasin de certificats Java en suivant les instructions de la publication [Ajout d’un certificat au magasin de certificats d’autorité de certification Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Une fois le certificat importé dans le magasin de certificats, les applications Java et MongoDB peuvent se connecter à l’émulateur Azure Cosmos DB.

Lors de la connexion à l’émulateur à partir des Kits de développement logiciel (SDK) Python et Node.js, la vérification SSL est désactivée.

## <a id="command-line"></a>Référence sur l’outil en ligne de commande
À partir de l’emplacement d’installation, vous pouvez utiliser la ligne de commande pour démarrer et arrêter l’émulateur, configurer les options et effectuer d’autres opérations.

### <a name="command-line-syntax"></a>Syntaxe de ligne de commande

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Pour afficher la liste des options, tapez `CosmosDB.Emulator.exe /?` dans l’invite de commandes.

<table>
<tr>
  <td><strong>Option</strong></td>
  <td><strong>Description</strong></td>
  <td><strong>Commande</strong></td>
  <td><strong>Arguments</strong></td>
</tr>
<tr>
  <td>[aucun argument]</td>
  <td>Démarre l’émulateur Azure Cosmos DB avec des paramètres par défaut.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Aide]</td>
  <td>Affiche la liste des arguments de ligne de commande pris en charge.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Permet d’obtenir l’état de l’émulateur Azure Cosmos DB. L’état est indiqué par le code de sortie : 1 = démarrage, 2 = exécution, 3 = arrêté. Un code de sortie négatif indique qu’une erreur s’est produite. Aucune autre sortie n’est générée.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Arrête l’émulateur Azure Cosmos DB.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Spécifie le chemin d’accès dans lequel stocker les fichiers de données. La valeur par défaut est %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt; : un chemin accessible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Spécifie le numéro de port à utiliser pour l'émulateur.  La valeur par défaut est 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt; : numéro de port unique</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Spécifie le numéro de port à utiliser pour l'API de compatibilité MongoDB. La valeur par défaut est 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt; : numéro de port unique</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Spécifie les ports à utiliser pour une connectivité directe. Les valeurs par défaut sont 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt; : liste séparée par des virgules de 4 ports</td>
</tr>
<tr>
  <td>Clé</td>
  <td>Clé d’autorisation pour l’émulateur. La clé doit être le codage en base 64 d’un vecteur de 64 octets.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;clé&gt; : la clé doit être le codage en base 64 d’un vecteur de 64 octets</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Spécifie que le comportement de limitation de taux de demandes est activé.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Spécifie que le comportement de limitation de taux de demandes est désactivé.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Ne pas afficher l’interface utilisateur de l’émulateur.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Ne pas afficher l’Explorateur de données au démarrage.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Spécifie le nombre maximal de collections partitionnées. Pour plus d’informations, voir la section [Modification du nombre de collections](#set-partitioncount).</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt; : nombre maximal autorisé de collections à partition unique. Valeur par défaut : 25. Valeur maximale autorisée : 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Spécifie le nombre par défaut des partitions pour une collection partitionnée.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount =&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; La valeur par défaut est 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Permet d’accéder à l’émulateur sur un réseau. Vous devez également passer/Key =&lt;key_string&gt; ou/keyfile =&lt;nom_fichier&gt; pour activer l’accès réseau.</td>
  <td>CosmosDB.Emulator.exe AllowNetworkAccess /Key =&lt;key_string&gt;<br><br>or<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Ne pas ajuster les règles de pare-feu lors de l’utilisation de /AllowNetworkAccess.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Générer une nouvelle clé d’autorisation et Générer une nouvelle clé d’autorisation et l’enregistrer dans le fichier spécifié. La clé générée peut être utilisée avec les options /Key or /KeyFile.</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;chemin du fichier de clé&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Cohérence</td>
  <td>Définir le niveau de cohérence par défaut pour le compte.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;cohérence&gt; : la valeur doit être l’un des [niveaux de cohérence](consistency-levels.md) proposés (Session, Strong, Eventual ou BoundedStaleness).  La valeur par défaut est Session.</td>
</tr>
<tr>
  <td>?</td>
  <td>Afficher le message d’aide.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Modification du nombre de collections

À l’aide de l’émulateur Azure Cosmos DB, vous pouvez, par défaut, créer jusqu’à 25 collections à partition unique ou 1 collection partitionnée. En modifiant la valeur **PartitionCount**, vous pouvez créer jusqu'à 250 collections à partition unique ou 10 collections partitionnées, ou n’importe quelle combinaison des deux qui ne dépasse pas 250 partitions uniques (où 1 collection partitionnée = 25 collections à partition unique).

Si vous tentez de créer une collection après le dépassement du nombre de partitions actuel, l’émulateur lève une exception ServiceUnavailable comportant le message suivant.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Pour modifier le nombre de collections disponibles dans l’émulateur Azure Cosmos DB, procédez comme suit :

1. Supprimez toutes les données locales de l’émulateur Azure Cosmos DB en cliquant avec le bouton droit sur l’icône **Émulateur Azure Cosmos DB** dans la zone d’état, puis en cliquant sur **Reset Data...** (Réinitialiser les données...).
2. Supprimez toutes les données de l’émulateur dans le dossier C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Quittez toutes les instances ouvertes en cliquant avec le bouton droit sur l’icône **Émulateur Azure Cosmos DB** dans la zone d’état, puis en cliquant sur **Quitter**. Quitter l’ensemble des instances peut prendre une minute.
4. Installez la dernière version de [l’émulateur Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Lancez l’émulateur avec l’indicateur PartitionCount en définissant une valeur <= 250. Par exemple : `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Contrôle de l’émulateur

L’émulateur est fourni avec un module PowerShell pour le démarrage, l’arrêt, la désinstallation et la récupération de l’état du service. Pour l’utiliser :

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

Sinon, placez le répertoire `PSModules` dans votre `PSModulesPath`, puis importez-le de la manière suivante :

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Voici un récapitulatif des commandes permettant de contrôler l’émulateur à partir de PowerShell :

### `Get-CosmosDbEmulatorStatus`

Retourne l’une de ces valeurs ServiceControllerStatus : ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator [-NoWait]`

Démarre l’émulateur. Par défaut, la commande attend que l’émulateur soit prêt à accepter les requêtes. Utilisez l’option -NoWait si vous souhaitez que l’applet de commande soit retournée dès le démarrage de l’émulateur.

### `Stop-CosmosDbEmulator [-NoWait]`

Arrête l’émulateur. Par défaut, cette commande attend que l’émulateur soit complètement arrêté. Utilisez l’option -NoWait si vous souhaitez que l’applet de commande soit retournée dès le début du processus d’arrêt de l’émulateur.

### `Uninstall-CosmosDbEmulator [-RemoveData]`

Désinstalle l’émulateur et supprime éventuellement l’intégralité du contenu de $env:LOCALAPPDATA\CosmosDbEmulator.
L’applet de commande garantit que l’émulateur est arrêté avant d’être désinstallé.

## <a name="running-on-docker"></a>Exécution sur Docker

L’émulateur Azure Cosmos DB peut être exécuté sur Docker pour Windows. L’émulateur ne fonctionne pas sur Docker pour Oracle Linux.

Une fois [Docker pour Windows](https://www.docker.com/docker-windows) installé, basculez vers les conteneurs Windows en double-cliquant sur l’icône Docker dans la barre d’outils et en sélectionnant **Switch to Windows containers (Basculer vers les conteneurs Windows)**.

Ensuite, extrayez l’image de l’émulateur à partir de Docker Hub en exécutant la commande suivante à partir de l’interface de votre choix.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Pour démarrer l’image, exécutez les commandes suivantes :

Depuis la ligne de commande :
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

À partir de PowerShell :
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

La réponse ressemble à ce qui suit :

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Utilisez maintenant le point de terminaison et la clé principale de la réponse sur votre client et importez le certificat SSL sur votre hôte. Pour importer le certificat SSL, procédez comme suit à partir d’une invite de commandes administrateur :

Depuis la ligne de commande :
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

À partir de PowerShell :
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Si vous fermez l’interpréteur de commandes interactif une fois que l’émulateur a été démarré, le conteneur de l’émulateur s’éteint.

Pour ouvrir l’Explorateur de données, accédez à l’URL suivante dans votre navigateur. Le point de terminaison de l’émulateur est fourni dans le message de réponse ci-dessus.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>Résolution de problèmes

Utilisez les conseils suivants pour vous aider à résoudre les problèmes rencontrés avec l’émulateur Azure Cosmos DB :

- Si vous avez installé une nouvelle version de l’émulateur et si vous rencontrez des erreurs, réinitialisez vos données. Pour réinitialiser vos données, cliquez avec le bouton droit sur l’icône de l’émulateur Azure Cosmos DB dans la zone d’état, puis cliquez sur Réinitialiser les données. Si cela ne résout pas les erreurs, vous pouvez désinstaller, puis réinstaller l’application. Pour obtenir des instructions, consultez [Désinstaller l’émulateur local](#uninstall).

- Si l’émulateur Azure Cosmos DB se bloque, collectez les fichiers de vidage dans le dossier sous c:\Utilisateurs\nom_utilisateur\AppData\Local\CrashDumps, compressez-les et joignez-les à un e-mail que vous envoyez à l’adresse [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Si des incidents se produisent dans CosmosDB.StartupEntryPoint.exe, exécutez la commande suivante à partir d’une invite de commandes administrateur : `lodctr /R`. 

- Si vous rencontrez un problème de connectivité, [collectez les fichiers de trace](#trace-files), compressez-les et joignez-les à un e-mail à [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Si vous recevez un message **Service indisponible**, il se peut que l’émulateur n’arrive pas à initialiser la pile réseau. Vérifiez si les clients Pulse Secure ou Juniper Networks sont installés, car leurs pilotes de filtre réseau peuvent être à l’origine du problème. La désinstallation des pilotes de filtre de réseau tiers permet généralement de résoudre le problème.

### <a id="trace-files"></a>Collecter les fichiers de trace

Pour collecter des traces de débogage, exécutez les commandes suivantes à partir d’une invite de commande d’administration :

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Regardez la barre d’état système pour vérifier que le programme s’est arrêté ; l’arrêt peut prendre une minute. Vous pouvez aussi simplement cliquer sur **Quitter** dans l’interface utilisateur de l’émulateur Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduisez le problème. Si l’Explorateur de données ne fonctionne pas, attendez quelques secondes pour que le navigateur s’ouvre afin d’intercepter l’erreur.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Accédez à `%ProgramFiles%\Azure Cosmos DB Emulator` et recherchez le fichier docdbemulator_000001.etl.
7. Envoyez le fichier .etl ainsi que les étapes de reproduction à [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) pour le débogage.

### <a id="uninstall"></a>Désinstaller l’émulateur local

1. Quittez toutes les instances ouvertes de l’émulateur local en cliquant avec le bouton droit sur l’icône de l’émulateur Azure Cosmos DB dans la zone d’état, puis en cliquant sur Quitter. Quitter l’ensemble des instances peut prendre une minute.
2. Dans la zone de recherche Windows, tapez **Applications et fonctionnalités**, puis cliquez sur le résultat **Applications et fonctionnalités (paramètres système)**.
3. Dans la liste des applications, faites défiler la page jusqu’à trouver **Émulateur Azure Cosmos DB**, sélectionnez-le, cliquez sur **Désinstaller**, puis confirmez en cliquant de nouveau sur **Désinstaller**.
4. Lorsque l’application est désinstallée, accédez au dossier C:\Users\<utilisateur>\AppData\Local\CosmosDBEmulator et supprimez-le. 

## <a name="change-list"></a>Liste des modifications

Pour connaître le numéro de version, cliquez avec le bouton droit sur l’icône de l’émulateur local dans la barre des tâches, puis cliquez sur l’élément de menu À propos.

### <a name="1201084-released-on-february-14-2018"></a>Version : 1.20.108.4 publiée le 14 février 2018

Cette version contient une nouvelle fonctionnalité et deux correctifs de bogues. Merci aux utilisateurs qui nous ont permis de trouver et de corriger ces problèmes.

#### <a name="bug-fixes"></a>Résolution des bogues

1. L’émulateur fonctionne désormais sur les ordinateurs ayant 1 ou 2 cœurs (ou unités centrales virtuelles).

   Cosmos DB alloue des tâches pour effectuer divers services. Le nombre de tâches allouées est un multiple du nombre de cœurs présents sur l’hôte. Le multiple par défaut fonctionne bien dans les environnements de production comprenant un nombre élevé de cœurs. Toutefois, sur les ordinateurs équipés d’un ou deux processeurs, aucune tâche n’est allouée pour exécuter ces services lorsque ce multiple est appliqué.

   Nous avons corrigé ce problème en remplaçant la configuration de l’émulateur. Nous appliquons désormais un multiple de 1. Le nombre de tâches allouées pour exécuter divers services est désormais égal au nombre de cœurs présents sur l’hôte.

   Il s’agissait de notre priorité pour cette version. Nous avons découvert que de nombreux environnements de développement et de test hébergeant l’émulateur comprennent un ou deux cœurs.

2. L’émulateur ne nécessite plus l’installation du package redistribuable Microsoft Visual C++ 2015.

   Nous avons constaté que les nouvelles installations de Windows (poste de travail et serveur) n’incluent pas ce package redistribuable. Par conséquent, les binaires du package redistribuable sont désormais fournis avec l’émulateur.

#### <a name="features"></a>Caractéristiques

De nombreux utilisateurs nous ont rapporté leur souhait de pouvoir ajouter des scripts à l’émulateur. Nous avons donc ajouté à cette version la capacité d’ajout de script. L’émulateur inclut désormais un module PowerShell qui lui permet de démarrer, de s’arrêter, d’obtenir son état et de se désinstaller : `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>Version 1.20.91.1 publiée le 26 janvier 2018

* Activation du pipeline d’agrégation MongoDB par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez :

> [!div class="checklist"]
> * Installé l’émulateur local
> * Exécuté l’émulateur sur Docker pour Windows
> * Authentifié des requêtes
> * Utilisé l’Explorateur de données dans l’émulateur
> * Exporté des certificats SSL
> * Appelé l’émulateur à partir de la ligne de commande
> * Collecté les fichiers de trace

Dans ce didacticiel, vous avez appris à utiliser l’émulateur local pour un développement local gratuit. Vous pouvez maintenant passer à l’étape suivante du didacticiel et découvrir comment exporter les certificats SSL de l’émulateur. 

> [!div class="nextstepaction"]
> [Exporter les certificats de l’émulateur Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
