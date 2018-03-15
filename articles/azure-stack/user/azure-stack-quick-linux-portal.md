---
title: "Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle dans le portail"
description: "Guide de démarrage rapide avec Azure Stack - Créer une machine virtuelle Linux dans le portail"
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/11/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: d4aef23e2de327fabb1f0304d8a3db1497d55827
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Créer une machine virtuelle Linux avec le portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez créer des machines virtuelles Azure Stack dans le portail Azure Stack. Cette méthode fournit une interface utilisateur basée sur le navigateur permettant de créer et de configurer une machine virtuelle et toutes les ressources associées. Ce guide de démarrage rapide montre comment créer rapidement une machine virtuelle Linux et y installer un serveur web.

## <a name="prerequisites"></a>Prérequis

* **Une image Linux dans la Place de Marché Azure Stack**

   La Place de Marché Azure Stack ne contient pas d’image Linux par défaut. Par conséquent, avant de créer une machine virtuelle Linux, vérifiez que l’opérateur Azure Stack a téléchargé l’image **Ubuntu Server 16.04 LTS** à l’aide de la procédure décrite dans la rubrique [Télécharger des éléments de la Place de Marché à partir d’Azure vers Azure Stack](../azure-stack-download-azure-marketplace-item.md).

* **Accès à un client SSH**

   Si vous utilisez le Kit de développement Azure Stack (ASDK), vous n’aurez peut-être pas accès à un client SSH dans votre environnement. Si c’est le cas, vous pouvez choisir parmi plusieurs packages qui incluent un client SSH. Vous pouvez par exemple installer PuTTY, qui inclut un client SSH et un générateur de clé SSH (puttygen.exe). Pour plus d’informations sur les options possibles, consultez l’article suivant en rapport avec Azure : [Utilisation de clés SSH avec Windows sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Ce guide de démarrage rapide utilise PuTTY pour générer des clés SSH et établir une connexion à la machine virtuelle Linux. Pour télécharger et installer PuTTY, accédez à [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH

Vous avez besoin d’une paire de clés SSH pour effectuer ce guide de démarrage rapide. Si vous disposez déjà d’une paire de clés SSH, vous pouvez ignorer cette étape.

1. Accédez au dossier d’installation PuTTY (l’emplacement par défaut est ```C:\Program Files\PuTTY```) et exécutez ```puttygen.exe```.
2. Dans la fenêtre PuTTY Key Generator (Générateur de clé PuTTY), vérifiez que le type de clé à générer (**Type of key to generate**) est **RSA** et que le nombre de bits dans une clé générée (**Number of bits in a generated key**) est **2048**. Quand vous êtes prêt, cliquez sur **Generate** (Générer).

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Pour terminer le processus de génération de clé, placez le curseur de la souris dans la fenêtre PuTTY Key Generator (Générateur de clé PuTTY).
4. Une fois la clé générée, cliquez sur **Save public key** (Enregistrer la clé publique) et **Save private key** (Enregistrer la clé privée) pour enregistrer vos clés privée et publique dans des fichiers.

   ![Clés puTTY](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Se connecter au portail Azure Stack

Connectez-vous au portail Azure Stack. L’adresse du portail Azure Stack varie en fonction du produit Azure Stack auquel vous vous connectez :

* Pour le Kit de développement Azure Stack (ASDK), accédez à https://portal.local.azurestack.external.
* Pour un système intégré Azure Stack, accédez à l’URL fournie par votre opérateur Azure Stack.

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure Stack.

2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Cliquez sur **Créer**.

4. Tapez les informations relatives à la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH**. Quand vous collez votre clé publique SSH (que vous avez enregistrée dans un fichier précédemment), veillez à supprimer tout espace blanc de début ou de fin. Lorsque vous avez terminé, cliquez sur **OK**.

   ![Informations de base sur la machine virtuelle](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Sélectionnez **D1_V2** pour la machine virtuelle.

   ![Taille de la machine](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. Dans la page **Paramètres**, conservez les valeurs par défaut et cliquez sur **OK**.

7. Dans la page **Résumé**, cliquez sur **OK** pour démarrer le déploiement de machine virtuelle.


## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

1. Cliquez sur **Se connecter** dans la page de la machine virtuelle. Une chaîne de connexion SSH qui peut être utilisée pour se connecter à la machine virtuelle apparaît.

   ![Connecter la machine virtuelle](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Ouvrez PuTTY.
3. Dans l’écran **Configuration PuTTY**, sous **Catégorie**, développez **SSH**, puis cliquez sur **Auth**. Cliquez sur **Parcourir** et sélectionnez le fichier de clé privée que vous avez enregistré précédemment.

   ![Clé privée puTTY](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. Sous **Catégorie**, faites défiler vers le haut et cliquez sur **Session**.
5. Dans la zone **Nom d’hôte (ou adresse IP)**, collez la chaîne de connexion à partir du portail Azure Stack affichée précédemment. Dans cet exemple, la chaîne est ```asadmin@192.168.102.34```.
 
   ![Session PuTTY](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Cliquez sur **Ouvrir** pour ouvrir une session sur la machine virtuelle.

   ![Session Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Installer NGINX

Utilisez le script Bash suivant pour mettre à jour les sources de package et installer le dernier package NGINX sur la machine virtuelle. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Une fois que vous avez terminé, quittez la session SSH et revenez à la page Vue d’ensemble de la machine virtuelle dans le portail Azure Stack.


## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Un groupe de sécurité réseau (NSG) sécurise les trafics entrant et sortant. Quand une machine virtuelle est créée à partir du portail Azure Stack, une règle de trafic entrant est créée sur le port 22 pour les connexions SSH. Comme cette machine virtuelle héberge un serveur web, vous devez créer une règle NSG pour le port 80.

1. Sur la page **Vue d’ensemble** de la machine virtuelle, cliquez sur le nom du **Groupe de ressources**.
2. Sélectionnez le **groupe de sécurité réseau** pour la machine virtuelle. Le groupe de sécurité réseau peut être identifié à l’aide de la colonne **Type**. 
3. Dans le menu de gauche, sous **Paramètres**, cliquez sur **Règles de sécurité entrantes**.
4. Cliquez sur **Add**.
5. Dans **Nom**, tapez **http**. Assurez-vous que l’option **Plage de ports** est définie sur 80 et l’option **Action** sur **Autoriser**. 
6. Cliquez sur **OK**.


## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX

Avec NGINX installé et le port 80 ouvert sur votre machine virtuelle, le serveur web est maintenant accessible à l’adresse IP publique de la machine virtuelle. L’adresse IP publique est indiquée dans la page Vue d’ensemble de la machine virtuelle dans le portail Azure Stack.

Ouvrez un navigateur web et accédez à ```http://<public IP address>```.

![Site par défaut NGINX](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir de la page de la machine virtuelle, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Linux simple ainsi qu’une règle de groupe de sécurité réseau, et installé un serveur web. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).

