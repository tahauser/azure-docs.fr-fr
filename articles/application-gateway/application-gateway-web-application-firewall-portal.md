---
title: "Créer ou mettre à jour une passerelle Application Gateway avec un pare-feu d’applications web | Microsoft Docs"
description: "Apprenez à créer une passerelle Application Gateway avec un pare-feu d’applications web à l’aide du portail"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>Création d’une passerelle d’application avec un pare-feu d’applications web à l’aide du portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interface de ligne de commande Azure](application-gateway-web-application-firewall-cli.md)

Découvrez comment créer une passerelle d’application avec le pare-feu d’applications web (WAF) activé.

Le pare-feu WAF d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session. Un pare-feu WAF protège contre la plupart des 10 plus courantes vulnérabilités web de l’OWASP.

## <a name="scenarios"></a>Scénarios

Cet article présente deux scénarios. Dans le premier scénario, vous apprendrez à [créer une passerelle d’application avec un pare-feu WAF](#create-an-application-gateway-with-web-application-firewall). Dans le second scénario, vous apprendrez à [ajouter un pare-feu WAF à une passerelle d’application existante](#add-web-application-firewall-to-an-existing-application-gateway).

![Exemple de scénario][scenario]

> [!NOTE]
> Vous pouvez ajouter des sondes d’intégrité personnalisées, des adresses de pools principaux et des règles supplémentaires pour la passerelle d’application. Ces applications sont configurées après la configuration de la passerelle d’application et non durant le déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

 Une passerelle Application Gateway requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle d’application sur un sous-réseau, seules les passerelles d’application supplémentaires peuvent être ajoutées au sous-réseau.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle Application Gateway existante

Cet exemple met à jour une passerelle d’application existante afin de prendre en charge un pare-feu WAF en mode **Prévention**.

1. Allez dans le panneau **Favoris** du portail Azure, puis sélectionnez **Toutes les ressources**. Dans le panneau **Toutes les ressources**, sélectionnez la passerelle d’application existante. Si l’abonnement déjà sélectionné a plusieurs ressources, entrez le nom dans la zone **Filtrer par nom** pour accéder facilement à la zone DNS.

   ![Sélection d’une passerelle d’application existante][1]

2. Sélectionnez **Pare-feu d’applications web** et mettez à jour les paramètres de la passerelle d’application. Après avoir effectué la mise à jour, sélectionnez **Enregistrer**. 

3. Utilisez les paramètres suivants pour mettre à jour une passerelle d’application existante afin de prendre en charge un pare-feu WAF :

   | **Paramètre** | **Valeur** | **Détails**
   |---|---|---|
   |**Mise à niveau vers le niveau WAF**| Activé | Cette option définit le niveau de la passerelle d’application au niveau WAF.|
   |**État du pare-feu**| Activé | Ce paramètre active le pare-feu sur le WAF.|
   |**Mode du pare-feu** | Prévention | Ce paramètre indique comment un pare-feu WAF doit traiter le trafic malveillant. Le mode **Détection** consigne uniquement les événements. Le mode **Prévention** consigne les événements et bloque le trafic malveillant.|
   |**Ensemble de règles**|3.0|Ce paramètre détermine l’[ensemble de règles principal](application-gateway-web-application-firewall-overview.md#core-rule-sets) qui est utilisé pour protéger les membres du pool principal.|
   |**Configurer des règles désactivées**|Varie|Pour éviter les faux positifs potentiels, vous pouvez utiliser ce paramètre pour désactiver des [règles et groupes de règles](application-gateway-crs-rulegroups-rules.md) spécifiques.|

    >[!NOTE]
    > Lors de la mise à niveau d’une passerelle Application Gateway existante vers la référence SKU WAF, la taille de la référence (SKU) devient **medium**. Une fois la configuration terminée, vous pouvez reconfigurer ce paramètre.

    ![Paramètres de base][2-1]

    > [!NOTE]
    > Pour afficher les journaux WAF, activez les diagnostics et sélectionnez **ApplicationGatewayFirewallLog**. Choisissez un nombre d’instances de **1** à des fins de test uniquement. Il n’est pas recommandé de choisir un nombre d’instances inférieur à **2**, car cela n’est pas couvert par le contrat de niveau de service. Les petites passerelles ne sont pas disponibles lorsque vous utilisez un pare-feu WAF.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Créer une passerelle Application Gateway avec un pare-feu d’applications web

Ce scénario va :

* Créez une passerelle d’application WAF moyenne avec deux instances.
* créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
* configurer un certificat pour le déchargement SSL.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free).

2. Dans le volet **Favoris**, sélectionnez **Nouveau**.

3. Dans le panneau **Nouveau**, sélectionnez **Mise en réseau**. Dans le panneau **Mise en réseau**, sélectionnez **Application Gateway**, comme indiqué dans l’image suivante :

    ![Création d’une passerelle Application Gateway][1]

4. Dans le panneau **De base** qui s’affiche, entrez les valeurs suivantes, puis cliquez sur **OK** :

   | **Paramètre** | **Valeur** | **Détails**
   |---|---|---|
   |**Nom**|AdatumAppGateway|Nom de la passerelle Application Gateway.|
   |**Niveau**|WAF|Les valeurs disponibles sont Standard et WAF. Pour en savoir plus sur un pare-feu WAF, consultez la page [Pare-feu d’applications web (WAF)](application-gateway-web-application-firewall-overview.md).|
   |**Taille de la référence (SKU)**|Moyenne|Les options de niveau standard sont **Petite**, **Moyenne** et **Grande**. Les options de niveau WAF sont **Moyenne** et **Grande** uniquement.|
   |**Nombre d’instances**|2|Nombre d’instances de la passerelle Application Gateway pour la haute disponibilité. Utilisez un nombre d’instances de 1 à des fins de test uniquement.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement à utiliser pour créer la passerelle Application Gateway.|
   |**Groupe de ressources**|**Créer un nouveau :** AdatumAppGatewayRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Emplacement**|Ouest des États-Unis||

   ![Configuration des paramètres de base][2-2]

5. Dans le panneau **Paramètres** qui s’affiche sous **Réseau virtuel**, sélectionnez **Choisir un réseau virtuel**. Dans le panneau **Choisir un réseau virtuel**, sélectionnez **Créer**.

   ![Sélection du réseau virtuel][2]

6. Dans le panneau **Créer un réseau virtuel**, entrez les valeurs suivantes, puis cliquez sur **OK**. Le champ **Sous-réseau** dans le panneau **Paramètres** est automatiquement renseigné avec le sous-réseau sélectionné.

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Nom**|AdatumAppGatewayVNET|Nom de la passerelle Application Gateway.|
   |**Espace d’adressage**|10.0.0.0/16| Cette valeur correspond à l’espace d’adressage du réseau virtuel.|
   |**Nom du sous-réseau**|AppGatewaySubnet|Nom du sous-réseau de la passerelle Application Gateway.|
   |**Plage d’adresses de sous-réseau**|10.0.0.0/28 | Ce sous-réseau autorise plusieurs sous-réseaux supplémentaires dans le réseau virtuel pour les membres du pool principal.|

7. Dans le panneau **Paramètres**, sous **Configuration d’adresse IP frontale**, sélectionnez **Public** comme **Type d’adresse IP**.

8. Dans le panneau **paramètres** sous **Adresse IP publique**, sélectionnez **Choisir une adresse IP publique**. Dans le panneau **Choisir une adresse IP publique**, sélectionnez **Créer**.

   ![Sélection de l’adresse IP publique][3]

9. Dans le panneau **Créer une adresse IP publique**, acceptez la valeur par défaut et cliquez sur **OK**. Le champ **Adresse IP publique** est renseigné avec l’adresse IP publique que vous avez indiquée.

10. Dans le panneau **Paramètres**, sous **Configuration de l’écouteur**, sélectionnez **HTTP** sous **Protocole**. Un certificat est requis pour utiliser **HTTPS**. La clé privée pour le certificat est nécessaire. Fournissez un fichier d’exportation .pfx du certificat et entrez le mot de passe du fichier.

11. Configurez les paramètres spécifiques du pare-feu **WAF**.

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**État du pare-feu**| Activé| Ce paramètre active ou désactive le pare-feu WAF.|
   |**Mode du pare-feu** | Prévention| Ce paramètre détermine les mesures que doit adopter le pare-feu WAF concernant le trafic malveillant. Le mode **Détection** consigne uniquement le trafic. Le mode **Prévention** consigne et arrête le trafic avec une réponse 403 - Non autorisé.|


12. Consultez la page **Résumé** et cliquez sur **OK**. La passerelle Application Gateway est maintenant mise en file d’attente et créée.

13. Une fois la passerelle Application Gateway créée, accédez à celle-ci à travers le portail pour poursuivre sa configuration.

    ![Vue des ressources de la passerelle Application Gateway][10]

Ces étapes permettent de créer une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool principal, les paramètres HTTP principaux et les règles. Une fois l’approvisionnement réussi, vous pouvez modifier ces paramètres en fonction de votre déploiement.

> [!NOTE]
> Les passerelles Application Gateway créées avec la configuration WAF de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="next-steps"></a>Étapes suivantes

Pour configurer un alias de domaine personnalisé pour l’[adresse IP publique](../dns/dns-custom-domain.md#public-ip-address), vous pouvez utiliser Azure DNS ou un autre fournisseur DNS.

Pour configurer la journalisation des diagnostics et consigner les événements détectés ou bloqués par WAF, consultez [Diagnostics de la passerelle Application Gateway](application-gateway-diagnostics.md).

Pour créer des sondes d’intégrité personnalisées, consultez [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md).

Pour configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux, consultez [Configurer le déchargement SSL](application-gateway-ssl-portal.md).

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
