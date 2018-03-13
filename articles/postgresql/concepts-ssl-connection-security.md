---
title: "Configurer la connectivité SSL dans la base de données Azure pour PostgreSQL"
description: "Instructions et informations de configuration de la base de données Azure pour PostgreSQL et des applications associées afin d’utiliser correctement les connexions SSL."
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0a4a7041a905470f895921cfedf2bd94e8466966
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configurer la connectivité SSL dans la base de données Azure pour PostgreSQL
La base de données Azure pour PostgreSQL préfère connecter vos applications clientes au service PostgreSQL via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

Par défaut, le service de base de données PostgreSQL est configuré de façon à exiger une connexion SSL. Vous avez la possibilité de désactiver le recours obligatoire au protocole SSL pour la connexion à votre service de base de données si votre application cliente ne prend pas en charge la connectivité SSL. 

## <a name="enforcing-ssl-connections"></a>Appliquer les connexions SSL
Pour tous les serveurs de base de données Azure pour PostgreSQL approvisionnés par le biais du Portail Azure et d’Azure CLI, l’application de connexions SSL est activée par défaut. 

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le Portail Azure incluent les paramètres requis par les langages courants pour se connecter au serveur de base de données via SSL. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

## <a name="configure-enforcement-of-ssl"></a>Configurer l’application du protocole SSL
Si vous le souhaitez, vous pouvez désactiver l’application de la connectivité SSL. Microsoft Azure recommande de toujours activer le paramètre **Appliquer une connexion SSL** pour renforcer la sécurité.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
Accédez à votre serveur de base de données Azure pour PostgreSQL et cliquez sur **Sécurité de la connexion**. Utilisez le bouton bascule pour activer ou désactiver le paramètre **Appliquer une connexion SSL**. Ensuite, cliquez sur **Enregistrer**. 

![Sécurité de connexion - Désactiver l’application de la connexion SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Vous pouvez vérifier le paramètre en consultant la page **Vue d’ensemble** pour voir si l’indicateur **d’état de d’application SSL** est affiché.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez activer ou désactiver le paramètre **ssl-mise en œuvre** en utilisant respectivement les valeurs `Enabled` ou `Disabled` dans Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Vérification que votre application ou votre infrastructure prend en charge les connexions SSL
De nombreuses infrastructures d’applications courantes qui utilisent PostgreSQL pour leurs services de base de données, notamment Drupal et Django, n’activent pas le protocole SSL par défaut lors de l’installation. La connectivité SSL doit être activée après l’installation ou par le biais des commandes CLI propres à l’application. Si votre serveur PostgreSQL applique des connexions SSL et que l’application associée n’est pas correctement configurée, elle risque de ne pas pouvoir se connecter à votre serveur de base de données. Consultez la documentation de votre application pour savoir comment activer les connexions SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applications nécessitant la vérification du certificat pour la connectivité SSL
Dans certains cas, les applications nécessitent un fichier de certificat local généré à partir du fichier de certificat (.cer) d’une autorité de certification (AC) approuvée pour se connecter en toute sécurité. Consultez les étapes suivantes pour obtenir le fichier .cer, décoder le certificat et le lier à votre application.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Télécharger le fichier de certificat émis par l’autorité de certification (AC) 
Le certificat nécessaire pour communiquer via SSL avec votre serveur de base de données Azure pour PostgreSQL se trouve [ici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Téléchargez le fichier de certificat en local.

### <a name="download-and-install-openssl-on-your-machine"></a>Télécharger et installer OpenSSL sur un ordinateur 
Afin de décoder le fichier de certificat nécessaire pour que votre application se connecte en toute sécurité à votre serveur de base de données, vous devez installer OpenSSL sur votre ordinateur local.

#### <a name="for-linux-os-x-or-unix"></a>Pour Linux, OS X ou Unix
Les bibliothèques OpenSSL sont fournies dans le code source directement à partir [d’OpenSSL Software Foundation](http://www.openssl.org). Les instructions suivantes vous guident tout au long des étapes d’installation d’OpenSSL sur votre PC Linux. Cet article utilise des commandes qui fonctionnent bien sous Ubuntu 12.04 et les versions ultérieures.

Ouvrez une session de terminal et téléchargez OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extrayez les fichiers du package téléchargé.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Entrez le répertoire où les fichiers ont été extraits. Par défaut, le résultat se présente ainsi.

```bash
cd openssl-1.1.0e
```
Configurez OpenSSL en exécutant la commande suivante. Si vous souhaitez placer les fichiers dans un autre dossier que /usr/local/openssl, veillez à modifier les valeurs suivantes.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Maintenant qu’OpenSSL est correctement configuré, vous devez le compiler afin de convertir votre certificat. Pour cette compilation, exécutez la commande suivante :

```bash
make
```
Une fois la compilation terminée, vous êtes prêt à installer OpenSSL comme un fichier exécutable à l’aide de la commande suivante :
```bash
make install
```
Pour confirmer que vous avez installé OpenSSL sur votre système, exécutez la commande suivante et vérifiez que vous obtenez le même résultat.

```bash
/usr/local/openssl/bin/openssl version
```
Si l’opération réussit, le message suivant s’affiche.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Pour Windows
L’installation d’OpenSSL sur un PC Windows peut s’effectuer ainsi :
1. **(Recommandé)** : en utilisant la fonctionnalité Bash pour Windows intégrée à Windows 10 et versions ultérieures, OpenSSL est installé par défaut. Vous trouverez [ici](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) des instructions sur l’activation de la fonctionnalité Bash pour Windows dans Windows 10.
2. En téléchargeant une application Win32/64 fournie par la communauté. Même si OpenSSL Software Foundation ne fournit ni n’approuve aucun programme d’installation Windows spécifique, cet organisme propose une liste des programmes d’installation disponibles [ici](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Décoder le fichier de certificat
Le fichier de l’AC racine téléchargé est chiffré. Utilisez OpenSSL pour décoder le fichier de certificat. Pour ce faire, exécutez cette commande OpenSSL :

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Se connecter à la base de données Azure pour PostgreSQL avec authentification par certificat SSL
Maintenant que vous avez correctement décodé votre certificat, vous pouvez vous connecter à votre serveur de base de données en toute sécurité via SSL. Pour qu’il puisse être vérifié, le certificat de serveur doit être placé dans le fichier ~/.postgresql/root.crt qui se trouve dans le répertoire de base de l’utilisateur. (Sous Microsoft Windows, le fichier se nomme %APPDATA%\postgresql\root.crt.). Vous trouverez ci-dessous les instructions pour se connecter à la base de données Azure pour PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Avec l’utilitaire de ligne de commande psql
L’exemple suivant montre comment se connecter à votre serveur PostgreSQL à l’aide de l’utilitaire de ligne de commande psql. Utilisez le fichier créé `root.crt` et l’option `sslmode=verify-ca` ou `sslmode=verify-full`.

À l’aide de l’interface de ligne de commande PostgreSQL, exécutez la commande suivante :
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Si la commande aboutit, les résultats suivants s’affichent :
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Avec l’outil d’interface graphique utilisateur pgAdmin
Pour configurer pgAdmin 4 de façon à établir une connexion sécurisée via SSL, vous devez définir `SSL mode = Verify-CA` ou `SSL mode = Verify-Full` comme suit :

![Capture d’écran de pgAdmin - connexion - conditions du mode SSL](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les différentes options de connectivité d’application de la page [Bibliothèques de connexions d’Azure Database pour PostgreSQL](concepts-connection-libraries.md).
