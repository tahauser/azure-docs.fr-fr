
1. Pour élever les privilèges, entrez :
   
        sudo -s
   
    Saisissez votre mot de passe.
2. Pour installer l'édition MySQL Community Server, entrez :
   
        zypper install mysql-community-server
   
    Patientez lors du téléchargement et de l’installation de MySQL.
3. Pour définir MySQL pour qu'il démarre lorsque le système démarre, entrez :
   
        insserv mysql
4. Démarrez le démon MySQL (mysqld) manuellement avec cette commande :
   
        rcmysql start
   
    Pour vérifier l’état du démon MySQL, entrez :
   
        rcmysql status
   
    Pour arrêter le démon MySQL, entrez :
   
        rcmysql stop
   
   > [!IMPORTANT]
   > Après l’installation, le mot de passe racine MySQL est vide par défaut. Il est recommandé d’exécuter le script **mysql\_secure\_installation** qui permet de sécuriser MySQL. Le script vous invite à modifier le mot de passe racine MySQL, à supprimer les comptes d'utilisateurs anonymes, à désactiver les connexions racine à distance, à supprimer les bases de données de test et à recharger la table des privilèges. Il est recommandé de répondre oui à toutes ces options et de modifier le mot de passe racine.
   > 
   > 
5. Tapez ici pour exécuter le script d'installation MySQL de script :
   
        mysql_secure_installation
6. Connectez-vous à MySQL :
   
        mysql -u root -p
   
    Entrez le mot de passe racine MySQL (que vous avez modifié à l’étape précédente) pour ouvrir une invite de commandes vous permettant d’écrire des instructions SQL pour interagir avec la base de données.
7. Pour créer un utilisateur MySQL, exécutez la commande suivante dans l’invite **mysql>** :
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Notez que les points-virgules (;) à la fin des lignes sont indispensables pour finir les commandes.
8. Pour créer une base de données et accorder les autorisations utilisateur `mysqluser` sur cette dernière, exécutez les commandes suivantes :
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Notez que les noms et les mots de passe des utilisateurs de base de données sont uniquement utilisés par les scripts pour se connecter à la base de données.  Les noms des comptes d’utilisateurs de base de données ne représentent pas nécessairement les comptes d’utilisateurs du système.
9. Pour vous connecter à partir d'un autre ordinateur, tapez :
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    où `ip-address` est l’adresse IP de l’ordinateur à partir duquel vous vous connectez à MySQL.
10. Pour quitter l'utilitaire d’administration de base de données MySQL, entrez :
    
        quit

## <a name="add-an-endpoint"></a>Ajout d’un point de terminaison
1. Après l'installation de MySQL, vous devez configurer un point de terminaison pour que MySQL soit accessible à distance. Connectez-vous au [portail Azure][AzurePortal]. Cliquez sur **Machines virtuelles**, sur le nom de la nouvelle machine virtuelle, puis sur **Points de terminaison**.
2. Cliquez sur **Ajouter** en bas de la page.
3. Ajoutez un point de terminaison nommé « MySQL », le protocole **TCP** ainsi que les ports **Public** et **Privé** définis sur « 3306 ».
4. Pour vous connecter à distance à la machine virtuelle à partir de votre ordinateur, tapez :
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Par exemple, pour la machine virtuelle créée dans ce didacticiel, saisissez la commande suivante :
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://portal.azure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
