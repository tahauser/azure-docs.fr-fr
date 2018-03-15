---
title: "Se connecter à Azure Database pour MySQL à partir de code C++"
description: "Ce guide de démarrage rapide fournit un exemple de code C++ que vous pouvez utiliser pour vous connecter à la base de données Azure pour MySQL et pour interroger les données de cette dernière."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: C++
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 41a56e1325c62a71880395c666e67c740742c3f9
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-connectorc-to-connect-and-query-data"></a>Base de données Azure pour MySQL : utiliser Connector/C++ pour vous connecter et interroger des données
Ce guide de démarrage rapide explique comment se connecter à une base de données Azure Database pour MySQL à l’aide d’une application C#. Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cette rubrique part du principe que vous connaissez les bases du développement C++ et que vous ne savez pas utiliser Azure Database pour MySQL.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un des guides suivants :
- [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Il vous faudra également :
- Installer [.NET Framework](https://www.microsoft.com/net/download)
- Installez [Visual Studio](https://www.visualstudio.com/downloads/)
- Installer [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Installer [Boost](http://www.boost.org/)

## <a name="install-visual-studio-and-net"></a>Installer Visual Studio et .NET
Les étapes de cette section supposent que vous êtes familiarisé avec l’utilisation de .NET.

### <a name="windows"></a>**Windows**
- Installez Visual Studio 2017 Community, un environnement de développement intégré (IDE) complet, extensible et gratuit qui permet de créer des applications modernes pour Android, iOS et Windows, ainsi que des applications web et de base de données et des services cloud. Vous pouvez installer la totalité de .NET Framework ou simplement .NET Core : les extraits de code du guide de démarrage rapide fonctionnent avec les deux. Si votre ordinateur est déjà équipé de Visual Studio, ignorez les deux étapes suivantes.
   1. Téléchargez le [programme d’installation de Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Exécutez le programme d’installation et suivez que les invites.

### <a name="configure-visual-studio"></a>**Configurer Visual Studio**
1. Dans Visual Studio, sélectionnez Propriété du projet > Propriétés de configuration > C/C++ > Éditeur de liens > Général > Répertoires de bibliothèques supplémentaires, puis ajoutez le répertoire lib\opt du connecteur c++ (c’est-à-dire C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt).
2. Dans Visual Studio, sélectionnez Propriété du projet > Propriétés de configuration > C/C++ > Général > Autres répertoires include :
   - Ajoutez le répertoire include/ du connecteur C++ (c’est-à-dire C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\include\).
   - Ajoutez le répertoire racine de la bibliothèque Boost (c’est-à-dire C:\boost_1_64_0\).
3. Dans Visual Studio, sélectionnez Propriété du projet > Propriétés de configuration > C/C++ > Éditeur de liens > Entrée > Dépendances supplémentaires, puis ajoutez mysqlcppconn.lib dans le champ de texte.
4. Copiez le fichier mysqlcppconn.dll du dossier de la bibliothèque de connecteurs C++ de l’étape 3 vers le même répertoire que l’exécutable de l’application, ou ajoutez-le à la variable d’environnement pour permettre à votre application de le retrouver.

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations requises pour vous connecter à la base de données Azure pour MySQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur de base de données Azure pour MySQL](./media/connect-cpp/1_server-overview-name-login.png)

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide d’instructions SQL **CREATE TABLE** et **INSERT INTO**. Le code utilise la classe sql::Driver avec la méthode connect() pour établir une connexion à MySQL. Le code utilise ensuite les méthodes createStatement() et execute() pour exécuter les commandes de base de données. 

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://mydemoserver.mysql.database.azure.com:3306/quickstartdb", "myadmin@mydemoserver", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    stmt = con>createStatement();
    stmt>execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt>execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con>prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt>setString(1, "banana");
    pstmt>setInt(2, 150);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "orange");
    pstmt>setInt(2, 154);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "apple");
    pstmt>setInt(2, 100);
    pstmt>execute();
    cout << "One row inserted." << endl;
    
    delete pstmt;   
    delete con;
    system("pause");
    return 0;

```

## <a name="read-data"></a>Lire les données

Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. Le code utilise la classe sql::Driver avec la méthode connect() pour établir une connexion à MySQL. Ensuite, le code utilise les méthodes prepareStatement() et executeQuery() pour exécuter les commandes de sélection. Il utilise alors next() pour accéder aux enregistrements suivants dans les résultats et, enfin, getInt() et getString() pour analyser les valeurs de l’enregistrement.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://mydemoserver.mysql.database.azure.com:3306/quickstartdb", "myadmin@mydemoserver", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

//  select  
    pstmt = con>prepareStatement("SELECT * FROM inventory;");
    result = pstmt>executeQuery();  
    
    while (result>next())
        printf("Reading from table=(%d, %s, %d)\n", result>getInt(1), result>getString(2).c_str(), result>getInt(3));   
    
    delete result;
    delete pstmt;   
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et lire les données à l’aide d’une instruction SQL **UPDATE**. Le code utilise la classe sql::Driver avec la méthode connect() pour établir une connexion à MySQL. Ensuite, le code utilise les méthodes prepareStatement() et executeQuery() pour exécuter les commandes de mise à jour. 

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://mydemoserver.mysql.database.azure.com:3306/quickstartdb", "myadmin@mydemoserver", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

    //update
    pstmt = con>prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt>setInt(1, 200);
    pstmt>setString(2, "banana");
    pstmt>executeQuery();
    printf("Row updated\n");
    
    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et lire les données à l’aide d’une instruction SQL **DELETE**. Le code utilise la classe sql::Driver avec la méthode connect() pour établir une connexion à MySQL. Ensuite, le code utilise les méthodes prepareStatement() et executeQuery() pour exécuter les commandes de suppression.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://mydemoserver.mysql.database.azure.com:3306/quickstartdb", "myadmin@mydemoserver", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
        
    //delete
    pstmt = con>prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt>setString(1, "orange");
    result = pstmt>executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migrer une base de données MySQL vers une base de données Azure pour MySQL à l’aide des images mémoire et de la restauration](concepts-migrate-dump-restore.md)
