---
title: Considérations relatives à la sécurité de SQL Server dans Azure | Microsoft Docs
description: Cette rubrique fournit des instructions générales pour la sécurisation de SQL Server en cours d’exécution sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: jroth
ms.openlocfilehash: b91638b6b76675711150323bee4aa9ad9d9a73d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure

Cette rubrique comprend des instructions de sécurité générales pour créer un accès sécurisé aux instances SQL Server dans une machine virtuelle Azure.

Azure est conforme à plusieurs règlements et normes de l’industrie et vous permet de créer une solution conforme avec SQL Server exécuté sur une machine virtuelle. Pour plus d’informations sur les exigences réglementaires relatives à Azure, voir [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Contrôler l’accès à la machine virtuelle SQL

Lorsque vous créez votre machine virtuelle SQL Server, déterminez comment contrôler attentivement les personnes qui ont accès à la machine et à SQL Server. En règle générale, vous devez systématiquement :

- Restreindre l’accès à SQL Server aux applications et aux clients qui en ont besoin.
- Suivre les meilleures pratiques de gestion des comptes d’utilisateur et des mots de passe.

Les sections suivantes fournissent des suggestions sur ces différents points.

## <a name="secure-connections"></a>Sécuriser les connexions

Lorsque vous créez une machine virtuelle SQL Server avec une image de galerie, l’option **Connectivité SQL Server** vous donne la possibilité de choisir **Local (sur la machine virtuelle uniquement)**, **Privé (dans un réseau virtuel)** ou **Public (Internet)**.

![Connectivité SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Pour une sécurité optimale, choisissez l’option la plus restrictive dans votre scénario. Par exemple, si vous exécutez une application qui accède à SQL Server sur la même machine virtuelle, **Local** constitue l’option la plus sûre. Si vous exécutez une application Azure qui requiert un accès à SQL Server, l’option **Privé** sécurise les communications vers SQL Server uniquement dans le [réseau virtuel Azure](../../../virtual-network/virtual-networks-overview.md) spécifié. Si vous avez besoin d’un accès **Public** (Internet) à la machine virtuelle SQL Server, assurez-vous de bien suivre les autres meilleures pratiques présentées dans cette rubrique afin de réduire votre surface d’exposition aux attaques.

Les options sélectionnées dans le portail utilisent les règles de sécurité du trafic entrant sur les machines virtuelles [Groupe de sécurité réseau](../../../virtual-network/virtual-networks-nsg.md) pour autoriser ou refuser le trafic réseau vers votre machine virtuelle. Vous pouvez modifier ou créer de nouvelles règles de groupe de sécurité réseau entrantes pour autoriser le trafic vers le port SQL Server (1433, par défaut). Vous pouvez également spécifier des adresses IP spécifiques qui sont autorisées à communiquer sur ce port.

![Règles de groupe de sécurité réseau](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Outre les règles de groupe de sécurité réseau pour limiter le trafic réseau, vous pouvez utiliser le Pare-feu Windows sur la machine virtuelle.

Lorsque vous utilisez des points de terminaison avec le modèle de déploiement classique, supprimez-les sur la machine virtuelle si vous n’en avez pas besoin. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, voir [Gestion de l’ACL sur un point de terminaison](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint) Cela n’est pas nécessaire pour les machines virtuelles qui utilisent le Gestionnaire des ressources.

Enfin, pensez à activer l’option de connexion chiffrée pour l’instance du moteur de base de données SQL Server dans votre machine virtuelle Azure. Configurez l’instance SQL Server avec un certificat signé. Pour plus d’informations, voir [Activation des connexions chiffrées dans le moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) et [Syntaxe de la chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Utiliser un port non défini par défaut

Par défaut, SQL Server écoute un port bien connu, le port 1433. Pour une sécurité renforcée, configurez SQL Server de sorte que l’écoute se fasse sur un port non défini par défaut, comme le port 1401. Si vous approvisionnez une image de galerie SQL Server dans le portail Azure, vous pouvez spécifier ce port dans le panneau **Paramètres SQL Server**.

Pour configurer ce paramètre après l’approvisionnement, vous avez deux options :

- Pour les machines virtuelles du Gestionnaire des ressources, vous pouvez sélectionner **Configuration de SQL Server** dans le panneau de vue d’ensemble de la machine virtuelle. Cela vous permet de modifier le port.

  ![Modification du port TCP dans le portail](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Vous pouvez configurer manuellement le port pour les machines virtuelles Classic ou pour les machines virtuelles SQL Server qui n’étaient pas approvisionnées avec le portail, en vous connectant à distance à la machine virtuelle. Pour obtenir les étapes de configuration, consultez [Configurer un serveur pour écouter un port TCP spécifique](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Si vous utilisez cette technique manuelle, vous devez également ajouter une règle de Pare-feu Windows pour autoriser le trafic entrant sur ce port TCP.

> [!IMPORTANT]
> Il est judicieux de spécifier un port qui n’est pas défini par défaut si votre port SQL Server est ouvert aux connexions Internet publiques.

Lorsque SQL Server écoute un port non défini par défaut, vous devez spécifier le port au moment où vous vous connectez. Par exemple, imaginons un scénario dans lequel l’adresse IP du serveur est 13.55.255.255 et SQL Server écoute le port 1401. Pour vous connecter à SQL Server, vous devez spécifier `13.55.255.255,1401` dans la chaîne de connexion.

## <a name="manage-accounts"></a>Gérer les comptes

Il n’est pas souhaitable que les intrus puissent deviner facilement les noms de compte ou les mots de passe. Suivez les conseils ci-après :

- Créez un seul compte d’administrateur local avec un nom différent de **Administrator**.

- Utilisez des mots de passe forts complexes pour tous vos comptes. Pour plus d’informations sur la création d’un mot de passe sécurisé, consultez l’article [Créer un mot de passe sécurisé](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la machine virtuelle SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est affecté par l’installation. Nous recommandons que la connexion **SA** ne soit ni utilisée ni activée. Si vous devez avoir une connexion SQL, utilisez l’une des méthodes suivantes :

  - Créez un compte SQL avec un nom unique et qui est membre de **sysadmin**. Vous pouvez le faire dans le portail en activant l’**authentification SQL** lors de l’approvisionnement.

    > [!TIP] 
    > Si vous n’activez pas l’authentification SQL lors de l’approvisionnement, vous devez modifier manuellement le mode d’authentification pour définir le mode **Mode d’authentification SQL Server et Windows**. Pour plus d’informations, consultez [Modifier le mode d’authentification du serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Si vous devez utiliser la connexion **SA**, activez la connexion après l’approvisionnement et assignez un nouveau mot de passe sécurisé.

## <a name="follow-on-premises-best-practices"></a>Suivre les meilleures pratiques locales

Outre les pratiques décrites dans cette rubrique, nous vous recommandons d’examiner et de mettre en œuvre les pratiques de sécurité locales traditionnelles, le cas échéant. Pour plus d’informations, consultez [Considérations sur la sécurité pour une installation SQL Server](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Étapes suivantes

Si vous vous intéressez également à l’optimisation des performances, voir [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](virtual-machines-windows-sql-server-iaas-faq.md).

