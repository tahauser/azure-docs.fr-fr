---
title: "Configurer la connectivité SSL pour se connecter en toute sécurité à la base de données Azure pour MySQL | Microsoft Docs"
description: "Instructions pour configurer correctement la base de données Azure pour MySQL et les applications associées afin d’utiliser correctement les connexions SSL"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 289d1c4c0ffd2667c49c5625e72780d54a71ceb5
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurer la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL
Azure Database pour MySQL prend en charge la connexion de votre serveur Azure Database pour MySQL aux applications clientes à l’aide de SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="step-1-obtain-ssl-certificate"></a>Étape 1 : obtenir un certificat SSL
Téléchargez le certificat nécessaire pour communiquer via le protocole SSL avec votre serveur Azure Database pour MySQL à partir de [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) et enregistrez le fichier de certificat sur votre disque local (ce didacticiel utilise c:\ssl à titre d’exemple).
**Pour Microsoft Internet Explorer et Microsoft Edge :** une fois le téléchargement terminé, renommez le certificat BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Étape 2 : création d’une liaison SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connexion au serveur à l’aide de MySQL Workbench via le protocole SSL
Configurez MySQL Workbench pour vous connecter en toute sécurité via le protocole SSL. Dans la boîte de dialogue Configurer une nouvelle connexion, accédez à l’onglet **SSL**. Dans le champ **SSL CA File:**, entrez l’emplacement du fichier **BaltimoreCyberTrustRoot.crt.pem**. 
![enregistrer une vignette personnalisée](./media/howto-configure-ssl/mysql-workbench-ssl.png) Pour les connexions existantes, vous pouvez lier SSL en cliquant avec le bouton droit sur l’icône de connexion et en choisissant Modifier. Accédez ensuite à l’onglet **SSL**, puis liez le fichier de certificat.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connexion au serveur à l’aide de l’interface de ligne de commande MySQL via le protocole SSL
Vous pouvez également lier le certificat SSL dans l’interface de ligne de commande MySQL en exécutant la commande suivante :
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Étape 3 : application des connexions SSL dans Azure 
### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
À partir du portail Azure, accédez à votre serveur de base de données Azure pour MySQL, puis cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**, puis cliquez sur **Enregistrer**. Microsoft recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez activer ou désactiver le paramètre **ssl-enforcement** en utilisant respectivement les valeurs Enabled ou Disabled dans Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Étape 4 : vérifier votre connexion SSL
Exécuter la commande mysql **status** pour vérifier que vous êtes connecté à votre serveur MySQL à l’aide de SSL :
```dos
mysql> status
```
Vérifiez que la connexion est chiffrée en examinant la sortie, qui doit indiquer : **SSL: Cipher in use is AES256-SHA** 

## <a name="sample-code"></a>Exemple de code
Pour établir une connexion sécurisée vers une base de données Azure Database pour MySQL sur SSL à partir de votre application, consultez les exemples de code suivants :

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@myserver4demo', 
        password='yourpassword', 
        database='quickstartdb', 
        host='myserver4demo.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@myserver4demo', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'myserver4demo.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'myserver4demo.mysql.database.azure.com', 
        :username => 'myadmin@myserver4demo',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@myserver4demo' , 'yourpassword', 'myserver4demo.mysql.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les différentes options de connectivité d’application de la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md)
