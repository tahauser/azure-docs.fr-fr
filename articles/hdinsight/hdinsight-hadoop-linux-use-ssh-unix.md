---
title: Utiliser SSH avec Hadoop - Azure HDInsight | Microsoft Docs
description: "Vous pouvez accéder à un cluster HDInsight à l’aide de SSH (Secure Shell). Ce document fournit des informations sur la connexion à HDInsight à l’aide des commandes SSH et SCP à partir de clients Windows, Linux, Unix ou macOS."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: commandes hadoop dans linux,commandes hadoop linux,hadoop macos,ssh hadoop,cluster ssh hadoop
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 1a1fbac1eb4c529ea89f0f4c72b3317718ac45ea
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Se connecter à HDInsight (Hadoop) à l’aide de SSH

Découvrez comment utiliser [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) pour vous connecter en toute sécurité à Hadoop sur Azure HDInsight. 

HDInsight peut utiliser Linux (Ubuntu) comme système d’exploitation pour les nœuds du cluster Hadoop. Le tableau suivant contient les informations d’adresse et de port nécessaires lors de la connexion à HDInsight basé sur Linux à l’aide d’un client SSH :

| Adresse | Port | Se connecte au... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Nœud de périmètre (R Server sur HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nœud de périmètre (n’importe quel autre type de cluster, si un nœud de périmètre existe) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nœud principal primaire |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nœud principal secondaire |

> [!NOTE]
> Remplacez `<edgenodename>` par le nom du nœud de périmètre.
>
> Remplacez `<clustername>` par le nom de votre cluster.
>
> Si votre cluster contient un nœud de périmètre, nous vous recommandons de __toujours vous connecter au nœud de périmètre__ à l’aide de SSH. Les nœuds principaux hébergent des services qui sont essentiels au bon fonctionnement de Hadoop. Le nœud de périmètre exécute uniquement ce que vous placez dans celui-ci.
>
> Pour plus d’informations sur l’utilisation des nœuds de périmètre, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]
> Lorsque vous vous connectez pour la première fois à HDInsight, votre client SSH peut afficher un message d’avertissement, indiquant que l’authenticité de l’hôte ne peut pas être établie. À l’invite, sélectionnez Oui pour ajouter l’hôte à la liste de serveurs approuvés de votre client SSH.
>
> Si vous êtes connecté à un serveur portant le même nom, vous pouvez recevoir un message d’avertissement indiquant que la clé d’hôte stockée ne correspond pas à la clé d’hôte du serveur. Consultez la documentation de votre client SSH pour savoir comment supprimer l’entrée existante associée au nom du serveur.

## <a name="ssh-clients"></a>Clients SSH

Les systèmes Linux, Unix et macOS fournissent les commandes `ssh` et `scp`. Le client `ssh` est généralement utilisé pour créer une session de ligne de commande à distance avec un système basé sur Linux ou Unix. Le client `scp` est utilisé pour copier des fichiers en toute sécurité entre votre client et le système distant.

Microsoft Windows n’installe pas de clients SSH par défaut. Les clients `ssh` et `scp` sont disponibles pour Windows dans les packages suivants :

* Client OpenSSH (bêta) : dans la mise à jour Fall Creators Update, accédez à __Paramètres__ > __Applications et fonctionnalités__ > __Gestion des fonctionnalités facultatives__ > __Ajouter une fonctionnalité__ et sélectionnez le __Client OpenSSH__. 

    > [!NOTE]
    > Si les commandes `ssh` et `scp` ne sont pas disponibles dans PowerShell après l’activation de cette fonctionnalité, déconnectez-vous et reconnectez-vous.

* [Bash sur Ubuntu sur Windows 10](https://msdn.microsoft.com/commandline/wsl/about) : les commandes `ssh` et `scp` sont disponibles via le Bash sur la ligne de commande Windows.

* [Client OpenSSH (bêta)](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/): il s’agit d’une fonctionnalité facultative introduite dans la mise à jour Windows 10 Fall Creators Update.

* [Azure Cloud Shell](../cloud-shell/quickstart.md) : Cloud Shell fournit un environnement Bash dans votre navigateur, ainsi que les commandes `ssh`, `scp` et d’autres commandes Linux courantes.

* [Git (https://git-scm.com/)](https://git-scm.com/) : les commandes `ssh` et `scp` sont disponibles via la ligne de commande GitBash.

Il existe également plusieurs clients SSH graphiques, tels que [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) et [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Alors que ces clients peuvent être utilisés pour se connecter à HDInsight, le processus de connexion est différent de celui utilisant l’utilitaire `ssh`. Pour plus d’informations, consultez la documentation du client graphique que vous utilisez.

## <a id="sshkey"></a>Authentification : clés SSH

Les clés SSH utilisent le [chiffrement à clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) pour authentifier des sessions SSH. Les clés SSH sont plus sûres que les mots de passe et fournissent un moyen simple pour sécuriser l’accès à votre cluster Hadoop.

Si votre compte SSH est sécurisé à l’aide d’une clé, le client doit fournir la clé privée correspondante lorsque vous vous connectez :

* La plupart des clients peuvent être configurés pour utiliser une __clé par défaut__. Par exemple, le client `ssh` recherche une clé privée à l’emplacement `~/.ssh/id_rsa` sur des environnements Linux et Unix.

* Vous pouvez spécifier le __chemin d’accès à une clé privée__. Avec le client `ssh`, le paramètre `-i` est utilisé pour spécifier le chemin d’accès à la clé privée. Par exemple : `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Si vous avez __plusieurs clés privées__ à utiliser avec différents serveurs, envisagez d’utiliser un utilitaire tel que [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). L’utilitaire `ssh-agent` peut être utilisé pour sélectionner automatiquement la clé à utiliser lors de l’établissement d’une session SSH.

> [!IMPORTANT]
>
> Si vous sécurisez votre clé privée avec une phrase secrète, vous devez entrer la phrase secrète lors de l’utilisation de la clé. Les utilitaires tels que `ssh-agent` peuvent mettre en cache le mot de passe pour plus de commodité.

### <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH

Utilisez la commande `ssh-keygen` pour créer des fichiers de clés publiques et privées. La commande suivante génère une paire de clés RSA 2 048 bits qui peut être utilisée avec HDInsight :

    ssh-keygen -t rsa -b 2048

Vous êtes invité à renseigner diverses informations pendant le processus de création de la clé. Par exemple, l’emplacement dans lequel sont stockées les clés ou vos préférences en termes d’utilisation de phrase secrète. Une fois le processus terminé, deux fichiers sont créés ; une clé publique et une clé privée.

* La __clé publique__ est utilisée pour créer un cluster HDInsight. La clé publique est dotée d’une extension de `.pub`.

* La __clé privée__ est utilisée pour authentifier votre client au cluster HDInsight.

> [!IMPORTANT]
> Vous pouvez sécuriser vos clés à l’aide d’une phrase secrète. Une phrase secrète est effectivement un mot de passe sur votre clé privée. Même si quelqu’un obtient votre clé privée, il doit avoir la phrase secrète pour utiliser la clé.

### <a name="create-hdinsight-using-the-public-key"></a>Créer un cluster HDInsight à l’aide de la clé publique

| Méthode de création | Utilisation de la clé publique |
| ------- | ------- |
| **Portail Azure** | Décochez l’option __Use same password as cluster login__ (Utiliser le même mot de passe que pour la connexion au cluster), puis sélectionnez __Clé publique__ comme type d’authentification SSH. Enfin, sélectionnez le fichier de clé publique ou collez le contenu texte du fichier dans le champ __SSH public key__ (Clé publique SSH).</br>![Boîte de dialogue de clé publique SSH lors de la création du cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Utilisez le paramètre `-SshPublicKey` de l’applet de commande `New-AzureRmHdinsightCluster` et transmettez le contenu de la clé publique sous forme de chaîne.|
| **Azure CLI 1.0** | Utilisez le paramètre `--sshPublicKey` de la commande `azure hdinsight cluster create` et transmettez le contenu de la clé publique sous forme de chaîne. |
| **Modèle Resource Manager** | Pour obtenir un exemple d’utilisation des clés SSH avec un modèle, consultez [Deploy HDInsight on Linux (w/ Azure Storage, SSH key)](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Déployer HDInsight sur Linux (avec Stockage Azure, clé SSH). L’élément `publicKeys` dans le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) est utilisé pour transmettre les clés à Azure lors de la création du cluster. |

## <a id="sshpassword"></a>Authentification : mot de passe

Les comptes SSH peuvent être sécurisés à l’aide d’un mot de passe. Lorsque vous vous connectez à HDInsight à l’aide de SSH, vous êtes invité à entrer le mot de passe.

> [!WARNING]
> Microsoft déconseille d’utiliser l’authentification par mot de passe pour SSH. Les mots de passe peuvent être devinés et sont vulnérables aux attaques en force brute. Nous vous recommandons plutôt d’utiliser des [clés SSH pour l’authentification](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Créer un cluster HDInsight à l’aide d’un mot de passe

| Méthode de création | Spécification du mot de passe |
| --------------- | ---------------- |
| **Portail Azure** | Par défaut, le compte utilisateur SSH a le même mot de passe que le compte de connexion du cluster. Pour utiliser un autre mot de passe, décochez l’option __Use same password as cluster login__ (Utiliser le même mot de passe que pour la connexion au cluster), puis entrez le mot de passe dans le champ __SSH password__ (Mot de passe SSH).</br>![Boîte de dialogue de mot de passe SSH lors de la création du cluster HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Utilisez le paramètre `--SshCredential` de l’applet de commande `New-AzureRmHdinsightCluster` et transmettez un objet `PSCredential` qui contient le nom de compte et mot de passe utilisateur SSH. |
| **Azure CLI 1.0** | Utilisez le paramètre `--sshPassword` de la commande `azure hdinsight cluster create` et fournissez la valeur de mot de passe. |
| **Modèle Resource Manager** | Pour obtenir un exemple d’utilisation d’un mot de passe avec un modèle, consultez [Deploy HDInsight cluster with Storage and SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Déployer un cluster HDInsight avec Stockage Azure et un mot de passe SSH). L’élément `linuxOperatingSystemProfile` dans le fichier [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) est utilisé pour transmettre le nom de compte et mot de passe SSH à Azure lors de la création du cluster.|

### <a name="change-the-ssh-password"></a>Modifier le mot de passe SSH

Pour plus d’informations sur la modification du mot de passe du compte utilisateur SSH, consultez la section __Modifier les mots de passe__ du document [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-portal-linux.md#change-passwords).

## <a id="domainjoined"></a>Authentification : HDInsight joint à un domaine

Si vous utilisez un __cluster HDInsight joint à un domaine__, vous devez utiliser la commande `kinit` après vous être connecté via SSH. Cette commande vous invite à renseigner un utilisateur de domaine et un mot de passe, et authentifie votre session auprès du domaine Active Directory Azure associé au cluster.

Pour plus d’informations, consultez la section [Configurer des clusters HDInsight joints à un domaine (version préliminaire)](./domain-joined/apache-domain-joined-configure.md).

## <a name="connect-to-nodes"></a>Se connecter à des nœuds

Les nœuds principaux et le nœud de périmètre (le cas échéant) sont accessibles sur Internet sur les ports 22 et 23.

* Lorsque vous vous connectez aux __nœuds principaux__, utilisez le port __22__ pour vous connecter au nœud principal primaire et le port __23__ pour vous connecter au nœud principal secondaire. Le nom de domaine complet à utiliser est `clustername-ssh.azurehdinsight.net`, où `clustername` est le nom de votre cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* Lorsque vous vous connectez au __nœud de périphérie__, utilisez le port 22. Le nom de domaine complet est `edgenodename.clustername-ssh.azurehdinsight.net`, où `edgenodename` est un nom que vous avez fourni lors de la création du nœud de périmètre. `clustername` est le nom du cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> Les exemples précédents reposent sur l’hypothèse que vous utilisez l’authentification par mot de passe, ou que l’authentification par certificat s’effectue automatiquement. Si vous utilisez une paire de clés SSH pour l’authentification, et que le certificat n’est pas utilisé automatiquement, utilisez le paramètre `-i` pour spécifier la clé privée. Par exemple : `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Une fois que vous êtes connecté, l’invite change de façon à indiquer le nom d’utilisateur SSH et le nœud auquel vous êtes connecté. Par exemple, lorsque vous êtes connecté au nœud principal primaire sous le nom `sshuser`, l’invite est `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-zookeeper-nodes"></a>Se connecter à des nœuds Zookeeper et Worker

Les nœuds Worker et Zookeeper ne sont pas directement accessibles depuis internet. Ils sont accessibles à partir des nœuds principaux ou des nœuds de périmètre du cluster. Pour vous connecter a d’autres nœuds, procédez comme suit :

1. Utilisez SSH pour vous connecter au nœud principal ou de périphérie :

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. À partir de la connexion SSH vers le nœud principal ou de périphérie, utilisez la commande `ssh` pour vous connecter à un nœud de travail dans le cluster :

        ssh sshuser@wn0-myhdi

    Pour récupérer la liste des noms de nœuds, consultez le document [Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Si le compte SSH est sécurisé à l’aide d’un __mot de passe__, entrez le mot de passe lors de la connexion.

Si le compte SSH est sécurisé à l’aide de __clés SSH__, assurez-vous que le transfert SSH est activé sur le client.

> [!NOTE]
> Une autre méthode permettant d’accéder directement à tous les nœuds du cluster consiste à installer HDInsight dans un réseau virtuel Azure. Ensuite, vous pouvez joindre votre machine à distance au même réseau virtuel et accéder directement à tous les nœuds du cluster.
>
> Pour plus d’informations, consultez [Extension des capacités de HDInsight à l’aide d’Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurer le transfert de l’agent SSH

> [!IMPORTANT]
> Les étapes suivantes partent du principe que vous disposez d’un système basé sur Linux ou UNIX et que vous utilisez Bash sur Windows 10. Si ces étapes ne fonctionnent pas pour votre système, vous devrez peut-être consulter la documentation de votre client SSH.

1. Avec un éditeur de texte, ouvrez `~/.ssh/config`. Si ce fichier n’existe pas, créez-le en entrant `touch ~/.ssh/config` dans la ligne de commande.

2. Ajoutez le texte suivant au fichier `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Remplacez les informations __Hôte__ avec l’adresse du nœud que vous connectez à l’aide de SSH. L’exemple précédent utilise le nœud de périmètre. Cette entrée permet de configurer le transfert de l’agent SSH pour le nœud spécifié.

3. Testez le transfert de l’agent SSH à l’aide de la commande suivante à partir du terminal :

        echo "$SSH_AUTH_SOCK"

    Cette commande renvoie des informations semblables au texte suivant :

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Si aucun élément n’est renvoyé, alors `ssh-agent` n’est pas en cours d’exécution. Pour plus d’informations, consultez les informations de scripts de démarrage de l’agent dans [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Utilisation de l’agent SSH avec SSH) ou consultez la documentation de votre client SSH.

4. Après avoir vérifié que **ssh agent** est en cours d’exécution, utilisez la commande suivante pour ajouter votre clé privée SSH à l’agent :

        ssh-add ~/.ssh/id_rsa

    Si votre clé privée est stockée dans un fichier différent, remplacez `~/.ssh/id_rsa` par le chemin d’accès au fichier.

5. Connectez-vous aux nœuds principaux ou aux nœuds de périmètre du cluster à l’aide de SSH. Utilisez ensuite la commande SSH pour vous connecter à un nœud Worker ou Zookeeper. La connexion est établie à l’aide de la clé transmise.

## <a name="copy-files"></a>Copie des fichiers

L’utilitaire `scp` vous permet de copier des fichiers à partir ou à destination de nœuds spécifiques dans le cluster. Par exemple, la commande ci-après copie le fichier `test.txt` du système local vers le nœud principal primaire :

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Étant donné qu’aucun chemin d’accès n’est spécifié après le caractère `:`, le fichier est placé dans le répertoire de base `sshuser`.

L’exemple ci-après copie le fichier `test.txt` du répertoire de base `sshuser` du nœud principal primaire vers le système local :

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]
> La commande `scp` peut uniquement accéder au système de fichiers de nœuds spécifiques au sein du cluster. Elle ne permet pas d’accéder aux données du stockage compatible avec HDFS pour le cluster.
>
> Utilisez la commande `scp` lorsque vous avez besoin de charger une ressource en vue de son utilisation à partir d’une session SSH. Par exemple, chargez un script Python, puis exécutez le script à partir d’une session SSH.
>
> Pour plus d’informations sur le chargement direct de données dans le stockage compatible avec HDFS, consultez les documents suivants :
>
> * [HDInsight avec Stockage Azure](hdinsight-hadoop-use-blob-storage.md)
>
> * [HDInsight avec Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md)
* [Extension des capacités de HDInsight à l’aide d’Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)