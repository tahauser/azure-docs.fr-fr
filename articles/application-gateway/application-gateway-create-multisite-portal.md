---
title: "Héberger plusieurs sites sur la passerelle Azure Application Gateway | Microsoft Docs"
description: "Cette page fournit des instructions pour configurer une passerelle Application Gateway Azure pour l’hébergement de plusieurs applications web sur la même passerelle avec le portail Azure."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurer une passerelle Application Gateway existante pour l’hébergement de plusieurs applications web

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-multisite-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

L’hébergement de plusieurs sites vous permet de déployer plusieurs applications web sur la même passerelle Application Gateway. Il s’appuie sur la présence de l’en-tête de l’hôte dans la requête HTTP entrante pour déterminer l’écouteur qui doit recevoir le trafic. L’écouteur dirige ensuite le trafic vers le pool principal approprié tel que configuré dans la définition des règles de la passerelle. Dans les applications web SSL, la passerelle Application Gateway s’appuie sur l’extension d’indication du nom du serveur (SNI) pour choisir l’écouteur approprié au trafic web. Une utilisation courante de l’hébergement de plusieurs sites consiste à équilibrer la charge des demandes pour différents domaines Web entre différents pools de serveurs principaux. De même, plusieurs sous-domaines du même domaine racine pourraient également être hébergés sur la même passerelle Application Gateway.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com et fabrikam.com avec deux pools de serveurs principaux : un pool de serveurs contoso et un pool de serveurs fabrikam. Une configuration similaire pourrait servir à héberger des sous-domaines hôtes comme app.contoso.com et blog.contoso.com.

![scénario multisite][multisite]

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario ajoute la prise en charge de plusieurs sites à une passerelle Application Gateway existante. Pour ce scénario, une passerelle Application Gateway existante doit être disponible pour pouvoir procéder à la configuration. Consultez [Créer une passerelle Application Gateway à l’aide du portail](application-gateway-create-gateway-portal.md) pour apprendre à créer une passerelle Application Gateway de base dans le portail.

Procédez comme suit pour mettre à jour une passerelle Application Gateway :

1. Créez des pools principaux à utiliser pour chaque site.
2. Créez un écouteur pour chaque site que la passerelle Application Gateway prend en charge.
3. Créez des règles pour associer chaque écouteur au serveur principal approprié.

## <a name="requirements"></a>Configuration requise

* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique. Le nom de domaine complet peut également être utilisé.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). Pour les passerelles Application Gateway activées pour plusieurs sites, le nom d’hôte et les indicateurs SNI sont également ajoutés.
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Les règles sont traitées dans l’ordre où elles sont répertoriées, et le trafic est orienté selon la première règle correspondante, quelle que soit sa spécificité. Par exemple, si une règle utilise un écouteur de base et qu’une autre utilise un écouteur multisite sur le même port, la règle avec l’écouteur multisite doit être répertoriée avant la règle avec l’écouteur de base pour que la règle multisite fonctionne comme prévu. 
* **Certificats :** chaque écouteur requiert un certificat unique, dans cet exemple, 2 écouteurs sont créés pour plusieurs sites. Deux certificats .pfx et leurs mots de passe doivent être créés.

## <a name="create-back-end-pools-for-each-site"></a>Créer des pools principaux pour chaque site

Un pool principal pour chaque site que cette passerelle Application Gateway prend en charge est nécessaire, dans cet exemple, 2 sont créés : un pour contoso11.com et un fabrikam11.com.

### <a name="step-1"></a>Étape 1 :

Accédez à une passerelle Application Gateway existante dans le portail Azure (https://portal.azure.com). Sélectionnez **Pools principaux** et cliquez sur **Ajouter**

![ajouter des pools principaux][7]

### <a name="step-2"></a>Étape 2

Fournissez les informations sur le pool principal **pool1**, en ajoutant les adresses IP ou les noms de domaine complets des serveurs principaux, puis cliquez sur **OK**

![paramètres du pool principal pool1][8]

### <a name="step-3"></a>Étape 3 :

Dans le panneau des pools principals, cliquez sur **Ajouter** pour ajouter un autre pool principal, **pool2**, en ajoutant les adresses IP ou les noms de domaine complets des serveurs principaux, puis cliquez sur **OK**

![paramètres du pool principal pool2][9]

## <a name="create-listeners-for-each-back-end"></a>Créer des écouteurs pour chaque serveur principal

Application Gateway se base sur des en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites web sur les mêmes adresses IP et port. L’écouteur de base créé dans le portail ne contient pas cette propriété.

### <a name="step-1"></a>Étape 1

Cliquez sur **Écouteurs** sur la passerelle Application Gateway existante et cliquez sur **Multisite** pour ajouter le premier écouteur.

![panneau Vue d’ensemble des écouteurs][1]

### <a name="step-2"></a>Étape 2 :

Fournissez les informations sur l’écouteur. Dans cet exemple la terminaison SSL est configurée, créez un nouveau port de serveur frontal. Téléchargez le certificat .pfx à utiliser pour la terminaison SSL. La seule différence dans ce panneau par rapport au panneau d’écouteur de base standard est le nom d’hôte.

![panneau Propriétés d’écouteur][2]

### <a name="step-3"></a>Étape 3 :

Cliquez sur **Multisite** et créez un autre écouteur comme décrit dans l’étape précédente pour le deuxième site. Veillez à utiliser un certificat différent pour le deuxième écouteur. La seule différence dans ce panneau par rapport au panneau d’écouteur de base standard est le nom d’hôte. Fournissez les informations sur l’écouteur et cliquez sur **OK**.

![panneau Propriétés d’écouteur][3]

> [!NOTE]
> La création d’écouteurs dans le portail Azure pour la passerelle Application Gateway est une tâche longue. Un certain temps peut être nécessaire pour créer les deux écouteurs dans ce scénario. Une fois la tâche terminée, les écouteurs s’affichent dans le portail comme indiqué dans l’image suivante :

![vue d’ensemble de l’écouteur][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Créer des règles pour associer des écouteurs aux pools principaux

### <a name="step-1"></a>Étape 1

Accédez à une passerelle Application Gateway existante dans le portail Azure (https://portal.azure.com). Sélectionnez **Règles** et choisissez la règle existante par défaut **rule1**, puis cliquez sur **Modifier**.

### <a name="step-2"></a>Étape 2

Renseignez le panneau des règles comme indiqué dans l’image suivante. Choisissez le premier écouteur et le premier pool, puis cliquez sur **Enregistrer** lorsque vous avez terminé.

![modifier une règle existante][6]

### <a name="step-3"></a>Étape 3 :

Cliquez sur **Règle de base** pour créer la deuxième règle. Remplissez le formulaire avec le deuxième écouteur et le deuxième pool principal, puis cliquez sur **OK** à enregistrer.

![panneau ajouter une règle de base][10]

La configuration d’une passerelle Application Gateway existante avec la prise en charge de plusieurs sites via le portail Azure est terminée par ce scénario.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment protéger vos sites web grâce au [Pare-feu d’applications web sur Application Gateway](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
