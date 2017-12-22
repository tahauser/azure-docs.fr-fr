---
title: "Configurer le déchargement SSL - Passerelle Azure Application Gateway - Portail Azure | Microsoft Docs"
description: "Cet article fournit des instructions pour la création d’une passerelle d’application pour le déchargement SSL à l’aide du portail Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Configurer une passerelle d’application pour le déchargement SSL en utilisant le portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-ssl-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Il est possible de configurer Azure Application Gateway de façon à mettre fin à la session SSL (Secure Sockets Layer) sur la passerelle pour éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.

## <a name="scenario"></a>Scénario

Le scénario suivant passe par la configuration du déchargement SSL sur une passerelle d’application existante. Le scénario suppose que vous avez déjà suivi la procédure pour [créer une passerelle d’application](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer le déchargement SSL avec une passerelle Application Gateway, un certificat est requis. Ce certificat est chargé sur la passerelle d’application et utilisé pour chiffrer et déchiffrer le trafic envoyé via SSL. Le certificat doit être partagé au format Personal Information Exchange (.pfx). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic.

## <a name="add-an-https-listener"></a>Ajouter un écouteur HTTPS

L’écouteur HTTPS recherche le trafic en fonction de sa configuration et aide à acheminer le trafic vers les pools principaux. Pour ajouter un écouteur HTTPS, procédez comme suit :

   1. Accédez au portail Azure et sélectionnez une passerelle d’application existante.

   2. Sélectionnez **Écouteurs** et cliquez sur le bouton **Ajouter** pour ajouter un écouteur.

   ![Panneau de la vue d’ensemble d’Application Gateway][1]


   3. Remplissez les informations requises suivantes pour l’écouteur et téléchargez le certificat .pfx :
      - **Nom** : nom convivial de l’écouteur.

      - **Configuration IP frontale** : il s’agit de la configuration d’IP frontale utilisée pour l’écouteur.

      - **Port frontal (nom/port)** : un nom convivial pour le port utilisé sur le serveur frontal de la passerelle d’application et le port actuellement utilisé.

      - **Protocole** : un commutateur qui détermine si HTTPS ou HTTP est utilisé pour le serveur frontal.

      - **Certificat (nom/mot de passe)** : si le déchargement SSL est utilisé, un certificat .pfx est requis pour ce paramètre. Un nom convivial et un mot de passe sont également requis.

   4. Sélectionnez **OK**.

![Ajouter un panneau pour l’écouteur][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Créer une règle et l’associer à l’écouteur

L’écouteur a été créé. Créez ensuite une règle pour gérer le trafic depuis l’écouteur. Les règles définissent la façon dont le trafic est acheminé vers les pools principaux en fonction de plusieurs paramètres de configuration. Ces paramètres incluent le protocole, le port et les sondes d’intégrité et si l’affinité de session basée sur cookies est utilisé. Pour créer et associer une règle à l’écouteur, procédez comme suit :


   1. Cliquez sur **Règles** dans la passerelle d’application, puis sélectionnez **Ajouter**.

   ![Panneau Règles d’Application Gateway][3]


   2. Sous **Ajouter une règle de base**, saisissez un nom convivial pour la règle dans le champ **Nom** et sélectionnez **l’écouteur** créé à l’étape précédente. Sélectionnez le **Pool principal** et le **Paramètre HTTP** appropriés, puis sélectionnez **OK**.

   ![Fenêtre Paramètres HTTPS][4]

Les paramètres sont maintenant enregistrés pour la passerelle Application Gateway. Le processus d’enregistrement pour ces paramètres peut prendre un certain temps avant leur disponibilité sur le portail ou via PowerShell. Une fois enregistrée, la passerelle d’application gère le chiffrement et le déchiffrement du trafic. Tout le trafic entre la passerelle d’application et les serveurs Web principaux est géré via HTTP. Toute communication avec le client via le protocole HTTPS sera renvoyée chiffrée au client.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une sonde d’intégrité personnalisée avec Azure Application Gateway, consultez [Créer une sonde d’intégrité personnalisée](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

