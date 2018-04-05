---
title: Restreindre l’accès réseau aux ressources PaaS - Portail Azure | Microsoft Docs
description: Découvrez comment limiter et restreindre l’accès réseau aux ressources Azure, telles que le service Stockage Azure et Azure SQL Database, à l’aide de points de terminaison de service de réseau virtuel en utilisant le portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 9a64a5c1f63dc05cba6fdfa310b694e34bdba7d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service réseau virtuel en utilisant le portail Azure

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un réseau virtuel avec un sous-réseau
> * Ajouter un sous-réseau et activer un point de terminaison de service
> * Créer une ressource Azure et autoriser l’accès réseau à cette ressource uniquement à partir d’un sous-réseau
> * Déployer une machine virtuelle sur chaque sous-réseau
> * Vérifier l’accès à une ressource à partir d’un sous-réseau
> * Vérifier que l’accès à une ressource est refusé à partir d’un sous-réseau et d’Internet

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |NOM| myVirtualNetwork |
    |Espace d’adressage| 10.0.0.0/16|
    |Abonnement| Sélectionnez votre abonnement|
    |Groupe de ressources | Sélectionnez **Créer** et entrez *myResourceGroup*.|
    |Lieu| Sélectionnez **Est des États-Unis**. |
    |Nom du sous-réseau| Public|
    |Plage d’adresses de sous-réseau| 10.0.0.0/24|
    |Points de terminaison de service| Désactivé|

    ![Entrer des informations de base sur votre réseau virtuel](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)


## <a name="enable-a-service-endpoint"></a>Activer un point de terminaison de service

1. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, entrez *myVirtualNetwork.* Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
2. Ajoutez un sous-réseau au réseau virtuel. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**, comme montré dans l’image suivante :

    ![Ajouter un sous-réseau](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Sous **Ajouter un sous-réseau**, sélectionnez ou saisissez les informations suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |----|----|
    |NOM| Privé |
    |Plage d’adresses| 10.0.1.0/24|
    |Points de terminaison de service| Sélectionnez **Microsoft.Storage** sous **Services**|

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Restreindre l’accès réseau vers et à partir d’un sous-réseau

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Groupe de sécurité réseau**.
Sous **Créer un groupe de sécurité réseau**, saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |NOM| myNsgPrivate |
    |Abonnement| Sélectionnez votre abonnement|
    |Groupe de ressources | Sélectionnez **Utiliser l’existant**, puis *myResourceGroup*.|
    |Lieu| Sélectionnez **Est des États-Unis**. |

4. Une fois le groupe de sécurité réseau créé, entrez *myNsgPrivate*, dans le champ **Rechercher des ressources, services et documents** en haut du portail. Quand **myNsgPrivate** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
5. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic sortant**.
6. Sélectionnez **Ajouter**.
7. Créez une règle qui autorise un accès sortant vers les adresses IP publiques affectées au service Stockage Azure. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Plages de ports source| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination | Sélectionnez **Stockage**|
    |Plages de ports de destination| * |
    |Protocole|Quelconque|
    |Action|AUTORISER|
    |Priorité|100|
    |NOM|Allow-Storage-All|
8. Créez une règle, qui remplace une règle de sécurité par défaut, qui autorise l’accès de trafic sortant à toutes les adresses IP publiques. Répétez les étapes 6 et 7 en utilisant les valeurs suivantes :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Plages de ports source| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination| Sélectionnez **Internet**|
    |Plages de ports de destination| * |
    |Protocole|Quelconque|
    |Action|Deny|
    |Priorité|110|
    |NOM|Deny-Internet-All|

9. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant**.
10. Sélectionnez **Ajouter**.
11. Créez une règle qui autorise le trafic du protocole RDP (Remote Desktop Protocol) entrant dans le sous-réseau à partir de n’importe quel endroit. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées sur le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure. Répétez les étapes 6 et 7 en utilisant les valeurs suivantes :

    |Paramètre|Valeur|
    |----|----|
    |Source| Quelconque |
    |Plages de ports source| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination| Sélectionnez **VirtualNetwork**|
    |Plages de ports de destination| 3389 |
    |Protocole|Quelconque|
    |Action|AUTORISER|
    |Priorité|120|
    |NOM|Allow-RDP-All|

12. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
13. Sélectionnez **+ Associer**
14. Sous **Associer un sous-réseau**, sélectionnez **Réseau virtuel** puis **myVirtualNetwork** sous **Choisir un réseau virtuel**.
15. Sous **Sous-réseau**, sélectionnez **Private** puis **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure avec activation des points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de cet article comprend des étapes permettant de restreindre l’accès réseau pour un compte Stockage Azure.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d’attente**.
3. Saisissez ou sélectionnez les informations suivantes, acceptez les autres valeurs par défaut, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |NOM| Entrez un nom qui n’existe dans aucun autre emplacement Azure. Le nom doit comprendre entre 3 et 24 caractères, correspondant à des chiffres et à des lettres en minuscules.|
    |Type de compte|StorageV2 (usage général v2)|
    |Réplication| Stockage localement redondant (LRS)|
    |Abonnement| Sélectionnez votre abonnement|
    |Groupe de ressources | Sélectionnez **Utiliser l’existant**, puis *myResourceGroup*.|
    |Lieu| Sélectionnez **Est des États-Unis**. |

### <a name="create-a-file-share-in-the-storage-account"></a>Créer un partage de fichiers dans le compte de stockage

1. Une fois le compte de stockage créé, entrez le nom du compte de stockage dans le champ **Rechercher des ressources, services et documents** en haut du portail. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Fichiers**, comme illustré dans l’image suivante :

    ![Compte de stockage](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 
3. Sélectionnez **+ Partage de fichiers**, sous **File service** (Service de fichiers).
4. Entrez *my-file-share* sous **Nom**, puis sélectionnez **OK**.
5. Fermez la zone **File service** (Service de fichiers).

### <a name="enable-network-access-from-a-subnet"></a>Activer l’accès réseau à partir d’un sous-réseau

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau. Pour autoriser l’accès à partir d’un sous-réseau spécifique uniquement et refuser l’accès réseau à partir de tous les autres réseaux, procédez comme suit :

1. Sous **PARAMÈTRES** pour le compte de stockage, sélectionnez **Firewalls and virtual networks** (Pare-feu et réseaux virtuels).
2. Sous **Réseaux virtuels**, sélectionnez **Réseaux sélectionnés**.
3. Sélectionnez **Add existing virtual network** (Ajouter un réseau virtuel existant).
4. Sous **Ajouter des réseaux**, sélectionnez les valeurs suivantes puis **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Abonnement| Sélectionnez votre abonnement.|
    |Réseaux virtuels|Sélectionnez **myVirtualNetwork** sous **Réseaux virtuels**|
    |Sous-réseaux| Sélectionnez **Private** sous **Sous-réseaux**|

    ![Pare-feu et réseaux virtuels](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png) 

5. Sélectionnez **Enregistrer**.
6. Fermez la zone **Firewalls and virtual networks** (Pare-feu et réseaux virtuels).
7. Sous **PARAMÈTRES** pour le compte de stockage, sélectionnez **Clés d’accès**, comme illustré dans l’image suivante :

      ![Pare-feu et réseaux virtuels](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Notez la valeur **Clé**, car vous devrez l’entrer manuellement dans une étape ultérieure lors du mappage du partage de fichiers à une lettre de lecteur d’une machine virtuelle.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

### <a name="create-the-first-virtual-machine"></a>Créer la première machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**.
3. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |----|----|
    |NOM| myVmPublic|
    |Nom d'utilisateur|Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Sélectionnez votre abonnement.|
    |Groupe de ressources| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Lieu| Sélectionnez **Est des États-Unis**.|

    ![Entrer des informations de base sur une machine virtuelle](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Sélectionnez une taille de machine virtuelle, puis sélectionnez **Sélectionner**.
5. Sous **Paramètres**, sélectionnez **Réseau** puis **myVirtualNetwork**. Sélectionnez ensuite **Sous-réseau** puis **Public**, comme montré dans l’image suivante :

    ![Sélectionner un réseau virtuel](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
6. Dans la page **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle. Le déploiement de la machine virtuelle peut prendre quelques minutes, mais vous pouvez passer à l’étape suivante pendant la création de la machine virtuelle.

### <a name="create-the-second-virtual-machine"></a>Créer la deuxième machine virtuelle

Répétez les étapes 1 à 6, mais à l’étape 3, nommez la machine virtuelle *myVmPrivate* et à l’étape 5, sélectionnez le sous-réseau **Private**.

Le déploiement de la machine virtuelle ne nécessite que quelques minutes. Ne passez pas à l’étape suivante tant que la création n’est pas terminée et que les paramètres ne sont pas ouverts dans le portail.

## <a name="confirm-access-to-storage-account"></a>Vérifier l’accès au compte de stockage

1. Une fois la création de la machine virtuelle *myVmPrivate* terminée, Azure ouvre les paramètres de celle-ci. Connectez-vous à la machine virtuelle en sélectionnant le bouton **Connexion**, comme montré dans l’image suivante :

    ![Connexion à une machine virtuelle](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Après avoir sélectionné le bouton **Connexion**, le fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur.  
3. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. 
4. Sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.
6. Sur la machine virtuelle *myVmPrivate*, mappez le partage de fichiers Azure au lecteur Z à l’aide de PowerShell. Avant d’exécuter les commandes qui suivent, remplacez `<storage-account-key>` et `<storage-account-name>` par les valeurs que vous avez fournies ou récupérées dans [Créer un compte de stockage](#create-a-storage-account).

    ```powershell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
    ```
    
    PowerShell retourne un résultat semblable à l’exemple suivant :

    ```powershell
    Name           Used (GB)     Free (GB) Provider      Root
    ----           ---------     --------- --------      ----
    Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
    ```

    Le partage de fichiers Azure est correctement mappé au lecteur Z.

7. Vérifiez que la machine virtuelle ne dispose d’aucune connexion sortante à d’autres adresses IP publiques à partir d’une invite de commande :

    ```
    ping bing.com
    ```
    
    Vous ne recevez aucune réponse, car le groupe de sécurité réseau associé au sous-réseau *Private* n’autorise pas l’accès sortant aux adresses IP publiques autres que les adresses affectées au service Stockage Azure.

8. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Vérifier que l’accès au compte de stockage est refusé

1. Entrez *myVmPublic* dans le champ **Rechercher des ressources, services et documents** en haut du portail.
2. Quand **myVmPublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
3. Répétez les étapes 1 à 6 de [Vérifier l’accès au compte de stockage](#confirm-access-to-storage-account) pour la machine virtuelle *myVmPublic*.

    L’accès est refusé et vous recevez une erreur `New-PSDrive : Access is denied`. L’accès est refusé car la machine virtuelle *myVmPublic* est déployée sur le sous-réseau *Public*. Le sous-réseau *Public* ne dispose pas d’un point de terminaison de service activé pour Stockage Azure, et le compte de stockage autorise uniquement l’accès réseau à partir du sous-réseau *Private* et non à partir du sous-réseau *Public*.

4. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPublic*.

5. Sur votre ordinateur, accédez au [portail](https://portal.azure.com) Azure.
6. Entrez le nom du compte de stockage que vous avez créé dans le champ **Rechercher des ressources, services et documents**. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.
7. Sélectionnez **Fichiers**.
8. Vous recevez le message d’erreur indiquée dans l’image suivante :

    ![Erreur d’accès refusé](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

    L’accès est refusé, car votre ordinateur ne se trouve pas dans le sous-réseau *Private* du réseau virtuel *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez activé un point de terminaison de service pour un sous-réseau de réseau virtuel. Vous avez vu que les points de terminaison de service peuvent être activés pour les ressources déployées à l’aide de différents services Azure. Vous avez créé un compte Stockage Azure et un accès réseau à ce compte de stockage, limité aux ressources du sous-réseau du réseau virtuel. Avant de créer des points de terminaison de service sur des réseaux virtuels de production, il est recommandé de bien se familiariser avec les [points de terminaison de service](virtual-network-service-endpoints-overview.md).

Si votre compte comporte plusieurs réseaux virtuels, vous pouvez relier deux réseaux virtuels pour que les ressources de chaque réseau virtuel puissent communiquer entre elles. Passez au tutoriel suivant pour savoir comment connecter deux réseaux virtuels.

> [!div class="nextstepaction"]
> [Connecter des réseaux virtuels](./tutorial-connect-virtual-networks-portal.md)
