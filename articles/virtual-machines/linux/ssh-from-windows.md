---
title: "Utilisation de clés SSH avec Windows pour les machines virtuelles Linux | Microsoft Docs"
description: "Apprenez à créer et à utiliser des clés SSH sur un ordinateur Windows pour vous connecter à une machine virtuelle Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: 66837a3a153cda041f5351c52c8ccb1f8ccfea50
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Utilisation de clés SSH avec Windows sur Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Lorsque vous vous connectez à des machines virtuelles Linux dans Azure, vous devez utiliser un [chiffrement à clé publique](https://wikipedia.org/wiki/Public-key_cryptography) pour garantir une connexion plus sûre à votre machine virtuelle Linux. Ce processus implique un échange de clés publiques et privées à l’aide de la commande SSH (secure shell) pour vous authentifier vous-même plutôt qu’un nom d’utilisateur et un mot de passe. Les mots de passe sont vulnérables aux attaques en force brute, en particulier sur les machines virtuelles connectées à Internet comme les serveurs web. Cet article fournit une vue d’ensemble des clés SSH et explique comment générer les clés appropriées sur un ordinateur Windows.

## <a name="overview-of-ssh-and-keys"></a>Vue d’ensemble de SSH et des clés
Vous pouvez vous connecter en toute sécurité à votre machine virtuelle Linux à l’aide de clés publiques et privées :

* La **clé publique** est placée sur votre machine virtuelle Linux, ou tout autre service que vous souhaitez utiliser avec le chiffrement à clé publique.
* La **clé privée** est fournie à votre machine virtuelle Linux lorsque vous vous connectez afin de vérifier votre identité. Protégez cette clé privée. Ne la partagez pas.

Ces clés publiques et privées peuvent être utilisées sur plusieurs machines virtuelles et services. Vous n’avez pas besoin d’une paire de clés pour chaque machine virtuelle ou service auxquels vous souhaitez accéder. Pour une présentation plus détaillée, consultez [Chiffrement à clé publique](https://wikipedia.org/wiki/Public-key_cryptography).

SSH est un protocole de connexion chiffré qui permet d'ouvrir des sessions en toute sécurité à travers des connexions non sécurisées. Il s’agit du protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure. Bien que le protocole SSH lui-même offre une connexion chiffrée, l’utilisation de mots de passe avec des connexions SSH laisse néanmoins la machine virtuelle vulnérable aux attaques en force brute ou au piratage des mots de passe. Une méthode plus sûre et recommandée consiste à se connecter à une machine virtuelle à l’aide de SSH et à utiliser ces clés publiques et privées, également appelées clés SSH.

Si vous ne souhaitez pas utiliser de clés SSH, vous pouvez toujours vous connecter à vos machines virtuelles Linux à l’aide d’un mot de passe. Si votre machine virtuelle n’est pas exposée à Internet, l’utilisation de mots de passe peut être suffisante. Toutefois, vous devez toujours gérer vos mots de passe pour chaque machine virtuelle Linux et maintenir des stratégies de mot de passe efficaces, par exemple en spécifiant une longueur minimale de mot de passe et une mise à jour régulière. L’utilisation de clés SSH simplifie la gestion des informations d’identification individuelles sur plusieurs machines virtuelles.

## <a name="windows-packages-and-ssh-clients"></a>Packages Windows et clients SSH
Vous vous connectez à des machines virtuelles Linux et gérez ces machines dans Azure à l’aide d’un **client SSH**. Aucun client SSH n’est généralement installé sur les ordinateurs Windows. La mise à jour anniversaire Windows 10 a ajouté Bash pour Windows et la dernière mise à jour Windows 10 Creators Update fournit des mises à jour supplémentaires. Windows Subsystem pour Linux vous permet d’exécuter à des utilitaires et d’y accéder, comme un client SSH de façon native dans un interpréteur de commandes Bash. Vous pouvez ensuite consulter un des documents Linux, tel que [Générer des paires de clés SSH pour Linux](mac-create-ssh-keys.md). Bash pour Windows est toujours en cours de développement et est considéré comme une version bêta. Pour plus d’informations sur Bash pour Windows, consultez la page [Bash sur Ubuntu sur Windows](https://msdn.microsoft.com/commandline/wsl/about).

Si vous souhaitez utiliser un autre outil que Bash pour Windows, voici une liste des packages de clients SSH Windows que vous pouvez installer :

* [Git pour Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Quels fichiers de clés devez-vous créer ?
Azure requiert des clés publiques et privées d’au moins 2048 bits au format **ssh-rsa**. Si vous gérez des ressources Azure à l’aide du modèle de déploiement classique, vous devez également générer une clé PEM (fichier `.pem`).

Voici les scénarios de déploiement et les types de fichiers que vous utilisez dans chacun d'eux 

1. Les clés **ssh-rsa** sont requises pour tout déploiement à l’aide du [portail Azure](https://portal.azure.com) et pour les déploiements Resource Manager à l’aide de [l’interface de ligne de commande Azure](../../cli-install-nodejs.md).
   * Il s’agit généralement des clés dont la plupart des utilisateurs ont besoin.
2. Un fichier `.pem` est nécessaire pour créer des machines virtuelles à l'aide du déploiement classique. Ces clés sont prises en charge dans les déploiements standard lorsque vous utilisez le [portail Azure](https://portal.azure.com) ou [l’interface de ligne de commande Azure](../../cli-install-nodejs.md).
   * Vous devez uniquement créer ces clés et certificats supplémentaires si vous gérez des ressources créées à l’aide du modèle de déploiement classique.

## <a name="install-git-for-windows"></a>Installation de Git pour Windows
La section précédente répertoriait plusieurs packages incluant l’outil `openssl` pour Windows. Cet outil est nécessaire pour créer des clés publiques et privées. Les exemples suivants expliquent comment installer et utiliser **Git pour Windows**, bien que vous pouvez choisir le package que vous préférez. **GIT pour Windows** vous donne accès à d’autres logiciels et utilitaires open source ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) qui peuvent être utiles lorsque vous travaillez avec des machines virtuelles Linux.

1. Téléchargez et installez **Git pour Windows** à partir de l’emplacement suivant : [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. Acceptez les options par défaut pendant le processus d’installation, sauf si vous avez besoin de les modifier.
3. Exécutez **Git Bash** à partir du **menu Démarrer** > **Git** > **Git Bash**. La console ressemble à l’exemple qui suit :

    ![Interpréteur de commandes Git pour Windows Bash](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Création d’une clé privée
1. Dans votre fenêtre **Git Bash**, utilisez `openssl.exe` pour créer une clé privée. L’exemple suivant crée une clé nommée `myPrivateKey` et un certificat nommé `myCert.pem` :

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    Le résultat ressemble à l’exemple qui suit :

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Si bash signale une erreur, essayez d’ouvrir une nouvelle fenêtre **Git Bash** avec des privilèges élevés. Réexécutez ensuite la commande `openssl` .

2. Répondez aux invites pour le nom du pays, l’emplacement, le nom de l’organisation, etc.
3. Votre nouvelle clé privée et votre nouveau certificat sont créés dans votre répertoire de travail actuel. En tant que mesure de sécurité, vous devez définir les autorisations sur votre clé privée afin que vous seul puissiez y accéder :

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. La [section suivante](#create-a-private-key-for-putty) détaille l’utilisation de PuTTYgen pour afficher et utiliser la clé publique et créer une clé privée spécifique pour l’utilisation de PuTTY avec SSH dans les machines virtuelles Linux. La commande suivante génère un fichier de clé publique nommé `myPublicKey.key` que vous pouvez utiliser immédiatement :

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Si vous devez également gérer des ressources classiques, convertissez `myCert.pem` en `myCert.cer` (certificat codé DER X509). Effectuez cette étape facultative uniquement si vous avez besoin de gérer spécifiquement d’anciennes ressources classiques.

    Convertissez le certificat à l’aide de la commande suivante :

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Créer une clé privée pour PuTTY
PuTTY est un client SSH courant pour Windows. Vous pouvez utiliser le client SSH de votre choix. Pour utiliser PuTTY, vous devez créer un autre type de clé : une clé privée PuTTY (PPK). Si vous ne souhaitez pas utiliser PuTTY, ignorez cette section.

L’exemple suivant crée cette clé privée supplémentaire spécifiquement pour une utilisation avec PuTTY :

1. Utilisez **Git Bash** pour convertir votre clé privée en une clé privée RSA interprétable par PuTTYgen. L’exemple suivant crée une clé nommée `myPrivateKey_rsa` à partir de la clé existante nommée `myPrivateKey` :

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    En tant que mesure de sécurité, vous devez définir les autorisations sur votre clé privée afin que vous seul puissiez y accéder :

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Téléchargez et exécutez PuTTYgen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Cliquez sur le menu : **Fichier** > **Charger une clé privée**
4. Localisez votre clé privée (`myPrivateKey_rsa` dans l’exemple précédent). Le répertoire par défaut lorsque vous démarrez **Git Bash** est `C:\Users\%username%`. Modifiez le filtre de fichiers pour afficher **Tous les fichiers (\*.\*)** :

    ![Charger la clé privée existante dans PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Cliquez sur **Ouvrir**. Un message indique que la clé a été correctement importée :

    ![Clé correctement importée dans PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Cliquez sur **OK** pour fermer l’invite.
7. La clé publique apparaît en haut de la fenêtre **PuTTYgen**. Vous copiez et collez cette clé publique dans le portail Azure ou modèle Azure Resource Manager lorsque vous créez une machine virtuelle Linux. Vous pouvez également cliquer sur **Enregistrer la clé publique** pour enregistrer une copie sur votre ordinateur :

    ![Enregistrer le fichier de clé publique PuTTY](./media/ssh-from-windows/save-public-key.png)

    L’exemple suivant montre comment copier et coller cette clé publique dans le portail Azure lorsque vous créez une machine virtuelle Linux. Généralement, la clé publique est ensuit stockée dans `~/.ssh/authorized_keys` sur votre nouvelle machine virtuelle.

    ![Utiliser la clé publique lorsque vous créez une machine virtuelle dans le portail Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. Dans **PuTTYgen**, cliquez sur **Enregistrer la clé privée** :

    ![Enregistrer le fichier de clé privée PuTTY](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Une invite vous demande si vous souhaitez continuer sans entrer un mot de passe pour votre clé. Une phrase secrète s’apparente à un mot de passe associé à votre clé privée. Même si quelqu'un récupère votre clé privée, il ne pourra pas s’authentifier en utilisant uniquement la clé. Il a également besoin de la phrase secrète. Sans cette phrase secrète, toute personne qui récupère votre clé privée peut se connecter à une machine virtuelle ou un service utilisant cette clé. Nous vous recommandons de créer une phrase secrète. Toutefois, si vous oubliez cette phrase secrète, il sera impossible de la récupérer.
   >
   >

    Si vous souhaitez saisir une phrase secrète, cliquez sur **Non**, entrez une phrase secrète dans la fenêtre principale de PuTTYgen, puis cliquez à nouveau sur **Enregistrer une clé privée**. Sinon, cliquez sur **Oui** pour continuer sans fournir la phrase secrète facultative.
9. Entrez un nom et un emplacement pour enregistrer votre fichier PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utiliser Putty pour se connecter via SSH à une machine Linux
Là encore, PuTTY est un client SSH courant pour Windows. Vous pouvez utiliser le client SSH de votre choix. Les étapes suivantes expliquent comment utiliser votre clé privée pour vous authentifier auprès de votre machine virtuelle Azure à l’aide de SSH. Les étapes sont similaires à celles d’autres clients de clés SSH puisque vous devez toujours charger votre clé privée pour authentifier la connexion SSH.

1. Téléchargez et exécutez putty à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Renseignez le nom d’hôte ou l’adresse IP de votre machine virtuelle à partir du portail Azure :

    ![Ouvrir une nouvelle connexion PuTTY](./media/ssh-from-windows/putty-new-connection.png)
3. Avant de sélectionner **Ouvrir**, cliquez sur l’onglet **Connexion** > **SSH** > **Auth**. Recherchez et sélectionnez votre clé privée :

    ![Sélectionner votre clé privée PuTTY à des fins d’authentification](./media/ssh-from-windows/putty-auth-dialog.png)
4. Cliquez sur **Ouvrir** pour vous connecter à la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également générer les clés publiques et privées [en utilisant OS X et Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur Bash pour Windows et les avantages de disposer d’outils OSS sur votre ordinateur Windows, consultez la page [Bash sur Ubuntu sur Windows](https://msdn.microsoft.com/commandline/wsl/about).

Si vous avez des difficultés à utiliser SSH pour vous connecter à vos machines virtuelles Linux, consultez [Résoudre les connexions SSH à une machine virtuelle Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
