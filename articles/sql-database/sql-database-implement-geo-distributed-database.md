---
title: Implémenter une solution de base de données SQL Azure géo-distribuée | Microsoft Docs
description: Découvrez comment configurer votre base de données et votre application SQL Azure pour le basculement vers une base de données répliquée et tester le basculement.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,business continuity
ms.topic: tutorial
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: ea94a311d409d8c5d6142746dc1009ff67ef3a82
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="implement-a-geo-distributed-database"></a>Implémenter une base de données géo-distribuée

Dans ce didacticiel, vous configurez une application et une base de données SQL Azure pour le basculement vers une région distante, puis testez votre plan de basculement. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer des utilisateurs de base de données et leur accorder des autorisations
> * Configurer une règle de pare-feu au niveau de la base de données
> * Créer un [groupe de basculement de géoréplication](sql-database-geo-replication-overview.md)
> * Créer et compiler une application Java pour interroger une base de données SQL Azure
> * Effectuer une simulation de récupération d'urgence

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.


## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

- La dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) est installée. 
- Une base de données SQL Azure est installée. Ce didacticiel utilise la base de données exemple AdventureWorksLT nommée **mySampleDatabase** créée à partir de l’un des démarrages rapides suivants :

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)
   - [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

- Vous avez identifié une méthode pour exécuter les scripts SQL sur votre base de données. Vous pouvez utiliser l’un des outils de requête suivants :
   - L’éditeur de requêtes dans le [portail Azure](https://portal.azure.com). Pour plus d’informations sur l’utilisation de l’éditeur de requêtes dans le portail Azure, consultez la section [Utilisez l’éditeur de requêtes pour vous connecter et interroger des données](sql-database-get-started-portal.md#query-the-sql-database).
   - La dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), qui est un environnement intégré pour la gestion des infrastructures SQL, allant de SQL Server à SQL Database pour Microsoft Windows.
   - La nouvelle version de [Visual Studio Code](https://code.visualstudio.com/docs), qui est un éditeur de code graphique pour Linux, macOS et Windows et qui prend en charge les extensions, y compris [l’extension mssql](https://aka.ms/mssql-marketplace) pour l’exécution de requêtes dans Microsoft SQL Server, Azure SQL Database et SQL Data Warehouse. Pour plus d’informations sur l’utilisation de cet outil avec la base de données SQL Azure, consultez l’article [Utilisez VS Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Créer des utilisateurs de base de données et accorder des autorisations

Connectez-vous à votre base de données et créez des comptes d’utilisateur à l’aide de l’un des outils de requête suivant :

- L’éditeur de requêtes dans le portail Azure
- SQL Server Management Studio
- Visual Studio Code

Ces comptes d’utilisateur sont répliqués automatiquement vers votre serveur secondaire (et restent synchronisés). Pour utiliser SQL Server Management Studio ou Visual Studio Code, vous devrez peut-être configurer une règle de pare-feu si vous vous connectez à partir d’un client à une adresse IP pour laquelle vous n’avez pas encore configuré de pare-feu. Pour des instructions plus détaillées, consultez la section [Créer une règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Dans une fenêtre de requête, exécutez la requête suivante pour créer deux comptes d’utilisateur dans votre base de données. Ce script accorde des autorisations **db_owner** pour le compte **app_admin** et accorde les autorisations **SÉLECTIONNER** et **METTE À JOUR** pour le compte **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Créer un pare-feu au niveau de la base de données

Créez une [règle de pare-feu au niveau de la base de données](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) pour votre base de données SQL. Cette règle de pare-feu au niveau de la base de données effectue automatiquement la réplication vers le serveur secondaire que vous allez créer dans ce didacticiel. Pour vous simplifier la tâche (dans ce didacticiel), utilisez l’adresse IP publique de l’ordinateur sur lequel vous effectuez les étapes de ce didacticiel. Pour déterminer l’adresse IP utilisée pour la règle de pare-feu au niveau du serveur pour votre ordinateur actuel, consultez la section [Créer une règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).  

- Dans la fenêtre de requête ouverte, remplacez la requête précédente par la requête suivante, en remplaçant les adresses IP par les adresses IP appropriées pour votre environnement.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Créer un groupe de basculement automatique de géoréplication active 

À l’aide d’Azure PowerShell, créez un [groupe de basculement automatique de géoréplication active](sql-database-geo-replication-overview.md) entre votre serveur SQL Azure existant et le nouveau serveur SQL Azure vide dans une région Azure, puis ajoutez votre base de données exemple au groupe de basculement.

> [!IMPORTANT]
> Ces commandes cmdlets nécessitent Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Remplissez les variables de vos scripts PowerShell en utilisant les valeurs de votre serveur existant et de la base de données exemple et fournissez une valeur globalement unique pour le nom du groupe de basculement.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Créez un serveur de sauvegarde vide dans votre région de basculement.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Créez un groupe de basculement entre les deux serveurs.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Ajoutez votre base de données au groupe de basculement.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Installer le logiciel Java

Les étapes de cette section supposent que vous connaissez le développement avec Java et que vous ne savez pas utiliser la base de données SQL Azure. 

### <a name="mac-os"></a>**Mac OS**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre projet Java. Entrez les commandes suivantes pour installer **brew** et **Maven** : 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Pour obtenir des instructions détaillées sur l’installation et la configuration des environnements Java et Maven, rendez-vous sur la page [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Création d’une application à l’aide de SQL Server), sélectionnez **Java**, sélectionnez **MacOS**, puis suivez les instructions détaillées pour la configuration de Java et Maven aux étapes 1.2 et 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre projet Java. Entrez les commandes suivantes pour installer **Maven** :

```bash
sudo apt-get install maven
```

Pour obtenir des instructions détaillées sur l’installation et la configuration des environnements Java et Maven, rendez-vous sur la page [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Création d’une application à l’aide de SQL Server), sélectionnez **Java**, sélectionnez **Ubuntu**, puis suivez les instructions détaillées pour la configuration de Java et Maven aux étapes 1.2, 1.3 et 1.4.

### <a name="windows"></a>**Windows**
Installez [Maven](https://maven.apache.org/download.cgi) à l’aide du programme d’installation officiel. Utilisez Maven pour gérer les dépendances ainsi que pour créer, tester et exécuter votre projet Java. Pour obtenir des instructions détaillées sur l’installation et la configuration des environnements Java et Maven, rendez-vous sur la page [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Création d’une application à l’aide de SQL Server), sélectionnez **Java**, sélectionnez Windows, puis suivez les instructions détaillées pour la configuration de Java et Maven aux étapes 1.2 et 1.3.

## <a name="create-sqldbsample-project"></a>Créer le projet SqlDbSample

1. Dans la console de commandes (par exemple Bash), créez un projet Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Saisissez **Y** et cliquez sur **Entrée**.
3. Remplacez les répertoires dans votre projet nouvellement créé.

   ```bash
   cd SqlDbSamples
   ```

4. À l’aide de votre éditeur habituel, ouvrez le fichier pom.xml dans le dossier de votre projet. 

5. Ajoutez le pilote JDBC Microsoft pour la dépendance de SQL Server à votre projet Maven en ouvrant votre éditeur de texte habituel et en copiant-collant les lignes suivantes dans votre fichier pom.xml. Ne remplacez pas les valeurs existantes préremplies dans le fichier. La dépendance JDBC doit être collée dans la section « dependencies » plus large ( ).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Spécifiez la version de Java à utiliser pour compiler le projet en ajoutant la section « properties » suivante dans le fichier pom.xml, après la section « dependencies ». 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Ajoutez la section « build » suivante dans le fichier pom.xml après la section « properties » pour la prise en charge des fichiers manifeste dans des fichiers JAR.       

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```
8. Enregistrez et fermez le fichier pom.xml.
9. Ouvrez le fichier App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) et remplacez son contenu avec le contenu suivant. Remplacez le nom du groupe de basculement par le nom de votre groupe de basculement. Si vous avez modifié le nom de la base de données, l’utilisateur ou le mot de passe, modifiez aussi ces valeurs.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Enregistrez et fermez le fichier App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Compiler et exécuter le projet SqlDbSample

1. Dans la console de commandes, exécutez la commande suivante.

   ```bash
   mvn package
   ```
2. Lorsque vous avez terminé, exécutez la commande suivante pour exécuter l’application (elle s’exécute pendant environ 1 heure, sauf si vous l’arrêtez manuellement) :

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Effectuer une simulation de récupération d’urgence

1. Appelez le basculement manuel du groupe de basculement. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Observez les résultats de l’application pendant le basculement. Certaines insertions échouent pendant l’actualisation du cache du DNS.     

3. Découvrez le rôle que votre serveur de récupération d’urgence exécute.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Effectuez une restauration automatique.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Observez les résultats de l’application pendant la restauration automatique. Certaines insertions échouent pendant l’actualisation du cache du DNS.     

6. Découvrez le rôle que votre serveur de récupération d’urgence exécute.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir l’article [Groupes de basculement et géoréplication active](sql-database-geo-replication-overview.md).
