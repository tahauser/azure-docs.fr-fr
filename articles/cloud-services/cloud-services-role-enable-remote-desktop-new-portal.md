---
title: Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services | Microsoft Docs
description: Configuration de l’application de service cloud Azure pour autoriser les connexions Bureau à distance
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: 2169fd95f51b468770a2e1e4c185d493babf220f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services

> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes rencontrés par votre application lorsqu'elle est en cours d'exécution.

Vous pouvez activer une connexion Bureau à distance dans votre rôle pendant le développement en incluant les modules Bureau à distance dans votre définition de service. Vous pouvez aussi activer le Bureau à distance via l’extension Bureau à distance. Cette deuxième approche est recommandée, car elle vous permet d’activer le Bureau à distance sans avoir à redéployer votre application.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configurer le Bureau à distance à partir du portail Azure

Le portail Azure utilise l’approche basée sur l’extension Bureau à distance, ce qui vous permet d’activer le Bureau à distance même après avoir déployé l’application. Les paramètres **Bureau à distance** de votre service cloud permettent d’activer le Bureau à distance, de changer le compte Administrateur local utilisé pour se connecter aux machines virtuelles ou le certificat employé dans l’authentification, et de définir la date d’expiration.

1. Cliquez sur **Services cloud** et sélectionnez le nom du service cloud, puis **Bureau à distance**.

    ![Bureau à distance des Services Cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Choisissez si vous souhaitez activer le Bureau à distance pour un rôle individuel ou pour tous les rôles, puis modifier la valeur du sélecteur en **Activé**.

3. Renseignez les champs requis pour le nom d’utilisateur, le mot de passe, la date d’expiration et le certificat.

    ![Bureau à distance des Services Cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Toutes les instances de rôle sont redémarrées lorsque vous activez pour la première fois le Bureau à distance et que vous cochez la case **OK**. Pour éviter un redémarrage, le certificat utilisé pour chiffrer le mot de passe doit être installé sur le rôle. Pour éviter un redémarrage, [téléchargez un certificat pour le service cloud](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) , puis revenez à cette boîte de dialogue.

4. Dans **Roles**, sélectionnez le rôle que vous voulez mettre à jour ou sélectionnez **Tous** pour tous les rôles.

5. Une fois les mises à jour de la configuration terminées, sélectionnez **Enregistrer**. Vos instances de rôles pourront recevoir les connexions quelques instants plus tard.

## <a name="remote-into-role-instances"></a>À distance dans les instances de rôle

Une fois que le Bureau à distance aura été activé sur les rôles, vous pourrez initier une connexion directement à partir du Portail Azure :

1. Cliquez sur **Instances** pour ouvrir les paramètres **Instances**.
2. Sélectionnez une instance de rôle pour laquelle la fonctionnalité Bureau à distance est configurée.
3. Cliquez sur **Connecter** afin de télécharger un fichier RDP pour l’instance de rôle.

    ![Bureau à distance des Services Cloud](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Cliquez sur **Ouvrir**, puis sur **Connecter** pour démarrer la connexion Bureau à distance.

>[!NOTE]
> Si votre service cloud se trouve derrière un groupe de sécurité réseau, il peut être nécessaire de créer des règles qui autorisent le trafic sur les ports **3389** et **20000**.  Bureau à distance utilise le port **3389**.  Les instances de service cloud sont soumis à l’équilibrage de charge, donc vous ne pouvez pas contrôler directement à quelle instance vous vous connectez.  Les agents *RemoteForwarder* et *RemoteAccess* gèrent le trafic RDP, et permettent au client d’envoyer un cookie RDP et de spécifier une instance individuelle à laquelle se connecter.  Les agents *RemoteForwarder* et *RemoteAccess* exigent que le port **20000*** soit ouvert : il peut être bloqué si vous avez défini un groupe de sécurité réseau.

## <a name="additional-resources"></a>Ressources supplémentaires

[Configuration des services cloud](cloud-services-how-to-configure-portal.md)
