---
title: "Déployer LEMP sur une machine virtuelle Linux dans Azure | Microsoft Docs"
description: Didacticiel - Installer la pile LEMP sur une machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: c77cd0148a7e3e7b99e90e29bc1499dae8f95028
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Installer un serveur web LEMP sur une machine virtuelle Azure
Cet article vous guide à travers le déploiement d’un serveur web NGINX, de celui de MySQL et de PHP (la pile LEMP) sur une machine virtuelle Ubuntu dans Azure. Pouvant également être installée dans Azure, la pile LEMP est une alternative à la très répandue [pile LAMP](tutorial-lamp-stack.md). Pour voir le serveur LEMP fonctionner, vous pouvez éventuellement installer et configurer un site WordPress. Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu (la lettre « L » dans la pile LEMP)
> * Ouvrez le port 80 pour le trafic web
> * Installer NGINX, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur le serveur LEMP


Ce programme d’installation est destiné aux tests rapides ou à la preuve de concept.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installer NGINX, MySQL et PHP

Exécutez la commande suivante pour mettre à jour les sources de package Ubuntu et installer NGINX, MySQL et PHP. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Vous êtes invité à installer les packages et les autres dépendances. Lorsque vous y êtes invité, définissez un mot de passe racine pour MySQL, puis [Entrée] pour continuer. Suivez les invites restantes. Ce processus installe les extensions PHP minimales requises pour utiliser PHP avec MySQL. 

![Page de mot de passe racine MySQL][1]

## <a name="verify-installation-and-configuration"></a>Vérifier l’installation et la configuration


### <a name="nginx"></a>NGINX

Vérifiez la version de NGINX à l’aide de la commande suivante :
```bash
nginx -v
```

Avec NGINX installé et le port 80 ouvert sur votre machine virtuelle, le serveur web est désormais accessible à partir d’internet. Pour afficher la page d’accueil de NGINX, ouvrez un navigateur web et entrez l’adresse IP publique de la machine virtuelle. Utilisez l’adresse IP publique dont vous vous êtes servi pour vous connecter par SSH à la machine virtuelle :

![Page par défaut de NGINX][3]


### <a name="mysql"></a>MySQL

Vérifiez la version de MySQL avec la commande suivante (remarquez le paramètre `V` avec une majuscule) :

```bash
mysql -V
```

Pour sécuriser l’installation de MySQL, exécutez le script `mysql_secure_installation`. Si vous configurez uniquement un serveur temporaire, vous pouvez ignorer cette étape. 

```bash
mysql_secure_installation
```

Entrez un mot de passe racine pour MySQL et configurez les paramètres de sécurité de votre environnement.

Si vous souhaitez essayer des fonctionnalités de MySQL (créer une base de données MySQL, ajouter des utilisateurs ou changer des paramètres de configuration), connectez-vous à MySQL. Cette étape n’est pas nécessaire pour suivre ce didacticiel. 


```bash
mysql -u root -p
```

Lorsque vous avez terminé, quittez l’invite de mysql en tapant `\q`.

### <a name="php"></a>PHP

Vérifiez la version de PHP avec la commande suivante :

```bash
php -v
```

Configurez NGINX pour utiliser l’interface PHP-FPM (PHP FastCGI Process Manager). Exécutez les commandes suivantes pour sauvegarder le fichier de configuration du bloc serveur NGINX d’origine et modifier ensuite le fichier original dans l’éditeur de votre choix :

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

Dans l’éditeur, remplacez le contenu de `/etc/nginx/sites-available/default` par ce qui suit. Consultez les commentaires pour lire une explication sur les paramètres. Remplacez l’adresse IP publique de votre machine virtuelle par *yourPublicIPAddress* (votre adresse IP publique) et conservez les autres paramètres. Puis enregistrez le fichier.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Vérifiez la configuration de NGINX à la recherche d’erreurs de syntaxe :

```bash
sudo nginx -t
```

Si la syntaxe est correcte, redémarrez NGINX avec la commande suivante :

```bash
sudo service nginx restart
```

Si vous voulez poursuivre le test, créez une page d’informations PHP rapide pour l’afficher dans un navigateur. La commande suivante crée la page d’informations PHP :

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



À présent, vous pouvez vérifier la page d’informations PHP que vous avez créée. Ouvrez un navigateur web et accédez à `http://yourPublicIPAddress/info.php`. Remplacez l’adresse IP publique de votre machine virtuelle. Elle doit ressembler à cette image.

![Page d’informations PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un serveur LEMP dans Azure. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu
> * Ouvrez le port 80 pour le trafic web
> * Installer NGINX, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur la pile LEMP

Passez au didacticiel suivant pour savoir comment mieux protéger les serveurs SSL à l’aide des certificats SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web avec SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
