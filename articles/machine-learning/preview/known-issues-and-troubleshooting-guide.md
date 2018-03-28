---
title: Problèmes connus et guide de dépannage | Microsoft Docs
description: Liste des problèmes connus et guide de dépannage
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 62207fa20c4660d1e828053ee73953cb68af1b9d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - Problèmes connus et guide de dépannage 
Cet article vous permet de rechercher et corriger les erreurs ou défaillances rencontrées dans le cadre de l’utilisation de l’application Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Rechercher le numéro de build de Workbench
Lors de la communication avec l’équipe de support, il est important de préciser le numéro de build de l’application Workbench. Sous Windows, pour accéder au numéro de build, cliquez sur le menu **Aide** et choisissez **À propos d’Azure ML Workbench**. Sous macOS, cliquez sur le menu **Azure ML Workbench** et choisissez **À propos d’Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Forum MSDN Machine Learning
Nous avons un forum MSDN sur leque vous pouvez poster des questions. L’équipe produit surveille le forum activement. Le forum URL est [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Collecter des informations de diagnostic
Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Les fichiers journaux se trouvent aux emplacements suivants :

### <a name="installer-log"></a>Journal du programme d’installation
Si vous rencontrez des problème lors de l’installation, les fichiers journaux du programme d’installation sont ici :

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Vous pouvez compresser le contenu de ces répertoires et nous l’envoyer à des fins de diagnostics.

### <a name="workbench-desktop-app-log"></a>Journal d’application de bureau Workbench
Si vous rencontrez des difficultés pour vous connecter ou si le bureau Workbench se bloque, vous trouverez les fichiers journaux à l’emplacement suivant :
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Vous pouvez compresser le contenu de ces répertoires et nous l’envoyer à des fins de diagnostics.

### <a name="experiment-execution-log"></a>Journal d’exécution de la commande experiment
Si un script particulier échoue lors de l’envoi à partir de l’application de bureau, essayez de le renvoyer par le biais de la commande `az ml experiment submit` de l’interface CLI. Vous devriez obtenir un message d’erreur complet au format JSON contenant une valeur **ID d’opération**. Envoyez-nous le fichier JSON, notamment **l’ID d’opération**, et nous vous aiderons à diagnostiquer le problème. 

Si l’envoi d’un script particulier réussit, mais que son exécution échoue, le script doit afficher **l’ID d’exécution** pour identifier cette exécution spécifique. Vous pouvez créer un package avec les fichiers journaux appropriés à l’aide de la commande suivante :

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

La commande `az ml experiment diagnostics` génère un fichier `diagnostics.zip` dans le dossier racine du projet. Le fichier ZIP contient le dossier de projet complet à l’état de sa dernière exécution, ainsi que des informations de journalisation. Veillez à supprimer toutes les informations sensibles que vous ne souhaitez pas inclure avant de nous envoyer le fichier de diagnostics.

## <a name="send-us-a-frown-or-a-smile"></a>Envoyez-nous un smiley mécontent (ou un sourire)

Lorsque vous utilisez Azure ML Workbench, vous pouvez également nous envoyer un smiley mécontent (ou un sourire) en cliquant sur l’émoticône dans l’angle inférieur gauche de l’interpréteur de commandes de l’application. Vous pouvez éventuellement choisir d’inclure votre adresse e-mail (de sorte que nous puissions vous contacter), et/ou une capture d’écran de l’état actuel. 

## <a name="known-service-limits"></a>Limites connues du service
- Taille maximale autorisée pour le dossier du projet : 25 Mo.
    >[!NOTE]
    >Cette limite ne s’applique pas aux dossiers `.git`, `docs` et `outputs`. Ces noms de dossier respectent la casse. Si vous travaillez avec des fichiers volumineux, reportez-vous à [Persistance des modifications et gestion des fichiers volumineux](how-to-read-write-files.md).

- Temps d’exécution maximal autorisé pour les expérimentations : sept jours

- Taille maximale du fichier suivi dans le dossier `outputs` après une exécution : 512 Mo
  - Autrement dit, si votre script génère un fichier de plus de 512 Mo dans le dossier de sortie, ce fichier n’est pas collecté. Si vous travaillez avec des fichiers volumineux, reportez-vous à [Persistance des modifications et gestion des fichiers volumineux](how-to-read-write-files.md).

- Les clés SSH ne sont pas prises en charge lors de la connexion à un ordinateur distant ou un cluster Spark via SSH. Seul le mode nom d’utilisateur/mot de passe est actuellement pris en charge.

- Lorsque vous utilisez un cluster HDInsight en tant que cible de calcul, il doit utiliser l’objet blob Azure comme stockage principal. L’utilisation du stockage Azure Data Lake n’est pas prise en charge.

- Les transformations de clustering de texte ne sont pas prises en charge sur Mac.

- La bibliothèque RevoScalePy est uniquement prise en charge sous Windows ou Linux (dans des conteneurs Docker). Pas de prise en charge sur macOS.

- Les bloc-notes Jupyter ont une taille limite de 5 Mo en cas d’ouverture à partir de l’application Workbench. Vous pouvez ouvrir des blocs-notes volumineux à partir de l’interface CLI à l’aide de la commande « az ml notebook start », puis nettoyer les sorties de cellules afin de réduire la taille du fichier.

## <a name="cant-update-workbench"></a>Impossible de mettre à jour Workbench
Quand une nouvelle mise à jour est disponible, la page d’accueil de l’application Workbench affiche un message vous informant de sa disponibilité. Un badge de mise à jour doit s’afficher en bas à gauche de l’application sur l’icône représentant une cloche. Cliquez sur le badge et suivez l’Assistant Installation pour installer la mise à jour. 

![image de la mise à jour](./media/known-issues-and-troubleshooting-guide/update.png)

Si vous ne voyez pas la notification, essayez de redémarrer l’application. Si vous ne voyez toujours pas la notification de mise à jour après le redémarrage, cela peut être dû à plusieurs choses.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Vous lancez Workbench à partir d’un raccourci épinglé dans la barre des tâches
Vous avez peut-être déjà installé la mise à jour, mais le raccourci épinglé pointe encore vers les anciens éléments sur le disque. Pour contrôler si c’est le cas, accédez au dossier `%localappdata%/AmlWorkbench`, vérifiez si la dernière version y est installée et examinez la propriété du raccourci épinglé pour voir où elle pointe. Si nécessaire, supprimez simplement l’ancien raccourci, lancez Workbench à partir du menu Démarrer et créez éventuellement un nouveau raccourci épinglé dans la barre des tâches.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Vous avez installé Workbench à l’aide du lien « Installer Azure ML Workbench » sur une machine virtuelle DSVM Windows
Malheureusement, il n’existe aucune solution simple pour résoudre ce problème. Vous devez effectuer les étapes suivantes pour supprimer les éléments installés, puis télécharger le programme d’installation le plus récent pour effectuer une nouvelle installation de Workbench : 
   - Supprimer le dossier `C:\Users\<Username>\AppData\Local\amlworkbench`
   - Supprimer le script `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Supprimer le raccourci du Bureau qui lance le script ci-dessus
   - télécharger le programme d’installation https://aka.ms/azureml-wb-msi et réinstaller.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Bloqué à l’écran « Vérification du compte d’expérimentation » après vous être connecté
Une fois la connexion établie, l'application Workbench peut rester bloquée sur un écran vide avec un message indiquant « Vérification du compte d'expérimentation » avec une roue en rotation. Pour résoudre ce problème, procédez comme suit :
1. Arrêt de l'application
2. Supprimez le fichier suivant :
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Redémarrez l’application.

## <a name="cant-delete-experimentation-account"></a>Impossible de supprimer un compte d’expérimentation
Vous pouvez utiliser l’interface CLI pour supprimer un compte Expérimentation, mais vous devez supprimer au préalable les espaces de travail enfants et les projets enfants au sein de ces espaces de travail enfants. Autrement, vous voyez l’erreur « Impossible de supprimer une ressource avant la suppression des ressources imbriquées ».

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Vous pouvez également supprimer les projets et espaces de travail dans l’application Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Impossible d’ouvrir le fichier si le projet se trouve dans OneDrive
Si vous avez Windows 10 Fall Creators Update et que votre projet est créé dans un dossier local mappé à OneDrive, vous constaterez peut-être que vous ne pouvez ouvrir aucun fichier dans Workbench. Il s’agit d’un bogue, introduit par Fall Creators Update, qui provoque l’échec du code node.js dans un dossier OneDrive. Ce bogue sera bientôt résolu par Windows Update, mais en attendant ne créez pas de projets dans un dossier OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nom de fichier trop long sous Windows
Si vous utilisez Workbench sur Windows, vous risquez d’atteindre la limite maximale de longueur de nom de fichier par défaut (260 caractères), ce qui vous sera signifié par l’erreur « Le chemin d’accès spécifié est introuvable ». Vous pouvez modifier un paramètre de clé de Registre pour permettre des noms de chemin de fichier beaucoup plus longs. Consultez [cet article](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) pour plus d’informations sur la façon de définir la clé de Registre _MAX_PATH_.

## <a name="interrupt-cli-execution-output"></a>Interrompre la sortie de l’exécution de l’interface CLI
Si vous démarrez une expérimentation à l’aide de `az ml experiment submit` ou `az ml notebook start` et que vous souhaitez interrompre la sortie : 
- Sur Windows, utilisez la combinaison de touches Ctrl-Pause sur le clavier.
- Sur macOS, utilisez Ctrl-C.

Notez que cela interrompt uniquement le flux de sortie dans la fenêtre de l’interface CLI. Cela n’arrête pas un travail en cours d’exécution. Si vous souhaitez annuler un travail en cours, utilisez la commande `az ml experiment cancel -r <run_id> -t <target name>`.

Sur les ordinateurs Windows dont les claviers n’ont pas de touche Pause, les alternatives possibles sont Fn-B, Ctrl-Fn-B ou Fn+Échap. Pour plus d’informations sur une combinaison de touches spécifique, consultez la documentation du fabricant de votre matériel.

## <a name="docker-error-read-connection-refused"></a>Erreur Docker « read: connection refused »
Lors de l’exécution avec un conteneur Docker local, l’erreur suivante est susceptible de s’afficher : 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Vous pouvez résoudre ce problème en modifiant le serveur DNS de Docker à partir de `automatic` sur une valeur fixe de `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Supprimer l’erreur d’exécution de machine virtuelle « no tty present »
Lors de l’exécution avec un conteneur Docker sur une machine Linux distante, vous êtes susceptible de rencontrer le message d’erreur suivant :
```
sudo: no tty present and no askpass program specified.
``` 
Cela peut se produire si vous utilisez le portail Azure pour modifier le mot de passe racine d’une machine virtuelle Ubuntu Linux. 

Azure Machine Learning Workbench requiert un accès sans mot de passe aux Sudoers pour s’exécuter sur des hôtes distants. Pour ce faire, le plus simple consiste à utiliser _visudo_ pour modifier le fichier suivant (vous pouvez créer le fichier s’il n’existe pas) :

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Il est important de modifier le fichier avec _visudo_ et non avec une autre commande. _visudo_ effectue des vérifications syntaxiques automatiques de tous les fichiers de configuration sudo. En cas de fichier Sudoers incorrect syntaxiquement, vous risquez de ne plus pouvoir accéder au sudo.

Insérez la ligne suivante à la fin du fichier :

```
username ALL=(ALL) NOPASSWD:ALL
```

Où _nom d’utilisateur_ est le nom qu’Azure Machine Learning Workbench utilisera pour se connecter à votre hôte distant.

La ligne doit être placée après #includedir "/etc/sudoers.d", sinon elle risque d’être remplacée par une autre règle.

Si vous avez une configuration sudo plus complexe, nous vous conseillons de consulter la documentation sudo pour Ubuntu disponible ici : https://help.ubuntu.com/community/Sudoers

L’erreur ci-dessus peut également se produire si vous n’utilisez pas une VM Linux basée sur Ubuntu dans Azure comme cible d’exécution. Nous prenons en charge uniquement les VM Linux basées sur Ubuntu pour les exécutions à distance. 

## <a name="vm-disk-is-full"></a>Disque de machine virtuelle plein
Par défaut lorsque vous créez une nouvelle VM Linux dans Azure, vous disposez d’un disque de 30 Go pour le système d’exploitation. Le moteur Docker utilise par défaut le même disque pour extraire des images et exécuter des conteneurs. Cela peut saturer le disque du système d’exploitation, et vous voyez une erreur de type « Disque de VM plein » lorsque cela se produit.

Un correctif rapide consiste à supprimer toutes les images Docker que vous n’utilisez plus. C’est précisément ce à quoi sert la commande Docker suivante. (Bien sûr vous devez intégrer un SSH dans la machine virtuelle afin d’exécuter la commande Docker à partir d’un interpréteur de commandes bash.)

```
$ docker system prune -a
```

Vous pouvez également ajouter un disque de données et configurer le moteur Docker pour utiliser le disque de données afin d’y stocker des images. Voici [comment ajouter un disque de données](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Vous pouvez ensuite [modifier où Docker stocke les images](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Ou bien, vous pouvez développer le disque du système d’exploitation, sans avoir à modifier la configuration du moteur Docker. Voici [comment étendre le disque du système d’exploitation](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Partage du lecteur C dans Windows
Si l’exécution se fait dans un conteneur Docker local sur Windows, la définition de `sharedVolumes` sur `true` dans le fichier `docker.compute` sous `aml_config` peut améliorer les performances d’exécution. Toutefois, cela requiert que vous partagiez le lecteur C dans l’_outil Docker pour Windows_. Si vous ne parvenez pas à partager le lecteur C, essayez les conseils suivants :

* Vérifier le partage sur le lecteur C à l’aide de l’Explorateur de fichiers
* Ouvrir les paramètres de carte réseau et désinstaller/réinstaller « Partage de fichiers et d’imprimantes pour les réseaux Microsoft » pour vEthernet
* Ouvrir les paramètres Docker et partager le lecteur C à partir des paramètres Docker
* Les modifications apportées au mot de passe Windows affectent le partage. Ouvrez l’Explorateur de fichiers, repartagez le lecteur C et entrez le nouveau mot de passe.
* Vous pouvez également rencontrer un problème de pare-feu lorsque vous tentez de partager votre lecteur C avec Docker. Ce [post Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) peut s’avérer utile.
* Lorsque vous partagez le lecteur C à l’aide d’informations d’identification de domaine, le partage peut cesser de fonctionner sur les réseaux où le contrôleur de domaine n’est pas accessible (par exemple, réseau domestique, Wi-Fi public, etc.). Pour plus d’informations, consultez [ce post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Vous pouvez également éviter le problème de partage, contre un léger amoindrissement des performances en définissant `sharedVolumne` sur `false` dans le fichier `docker.compute`.

## <a name="wipe-clean-workbench-installation"></a>Réinitialiser l’installation de Workbench
Cette opération n’est généralement pas nécessaire, mais au cas où vous devriez réinitialiser une installation, voici les étapes à effectuer :

- Sous Windows :
  - Veillez d’abord à utiliser l’applet _Ajout / Suppression de programmes_ dans le _Panneau de configuration_ pour supprimer l’entrée de l’application _Azure Machine Learning Workbench_.  
  - Ensuite, vous pouvez télécharger et exécuter l’un des scripts suivants :
    - [Script de ligne de commande Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Script Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Vous devrez peut-être exécuter `Set-ExecutionPolicy Unrestricted` dans une fenêtre PowerShell avec élévation de privilèges avant de pouvoir exécuter le script.)
- Sur macOS :
  - Téléchargez et exécutez simplement le [script macOS bash shell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).


## <a name="some-useful-docker-commands"></a>Quelques commandes Docker utiles

Voici quelques commandes Docker utiles :

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
