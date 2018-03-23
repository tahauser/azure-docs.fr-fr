---
title: Protéger les données personnelles avec les fonctionnalités de sécurité réseau Azure | Microsoft Docs
description: Protéger les données personnelles à l’aide des fonctionnalités de sécurité réseau Azure. Ceci peut être utilisé pour se conformer au Règlement général sur la protection des données (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 54aeb22a501e248105931df341d23e524448155a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Protéger les données personnelles avec les fonctionnalités de sécurité réseau : Azure Application Gateway et groupes de sécurité réseau

Cet article fournit des informations et procédures qui vous aideront à utiliser Azure Application Gateway et des groupes de sécurité réseau pour protéger les données personnelles. Ces informations peuvent soutenir vos efforts de conformité avec le Règlement général sur la protection des données (RGPD).

Un moyen de se défendre contre l’exploitation courante de certaines vulnérabilités, notamment l’injection de code SQL ou les scripts de site à site, est un élément important de toute stratégie de sécurité multicouche visant à protéger la confidentialité des données personnelles. En maintenant le trafic réseau indésirable en dehors de votre réseau virtuel Azure, vous vous protégez contre des possibilités de violation des données sensibles. Microsoft Azure vous propose des outils pour vous aider à protéger vos données contre les pirates.

## <a name="scenario"></a>Scénario

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée, Adriatique et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni.

La compagnie utilise Microsoft Azure pour stocker ses données d’entreprise dans le cloud et exécuter des applications sur des machines virtuelles qui traitent ces données et y accèdent. Ces dernières incluent des informations d’identification personnelle telles que les noms, les adresses, les numéros de téléphone et les informations de carte de crédit de sa base de clients globale. Elles incluent également des informations de ressources humaines traditionnelles telles que les adresses, les numéros de téléphone, les numéros d’identification fiscale, etc. des employés de toutes les agences de la compagnie. La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense qui inclut des informations personnelles pour effectuer le suivi des relations avec les clients actuels et anciens.

Les employés de la compagnie accèdent au réseau à partir de ses agences distantes et des agents de voyage répartis dans le monde entier ont accès à certaines ressources de l’entreprise et utilisent des applications web hébergées dans des machines virtuelles Azure pour interagir avec elles.

## <a name="problem-statement"></a>Définition du problème

La compagnie doit protéger la confidentialité des données personnelles des clients et des employés contre les attaquants qui exploitent des vulnérabilités logicielles pour exécuter du code malveillant pouvant exposer les données personnelles stockées ou utilisées par les applications cloud.

## <a name="company-goal"></a>Objectif de l’entreprise

L’objectif de la compagnie est de veiller à ce qu’aucune personne non autorisée ne puisse accéder aux réseaux virtuels Azure d’entreprise et aux applications et données qui y résident en exploitant des vulnérabilités courantes. 

## <a name="solutions"></a>solutions

Microsoft Azure fournit des mécanismes de sécurité pour empêcher le trafic indésirable d’entrer sur les réseaux virtuels Azure. Le contrôle du trafic entrant et sortant est généralement effectué par des pare-feu. Dans Azure, vous pouvez utiliser Application Gateway avec le pare-feu d’applications web et les groupes de sécurité réseau (NSG), qui agissent en tant que pare-feu distribué simple. Ces outils vous permettent de détecter et bloquer le trafic réseau indésirable.

### <a name="application-gatewayweb-application-firewall"></a>Application Gateway/Pare-feu d’applications web

Le composant [Pare-feu d’applications Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) de [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) protège les applications web qui deviennent de plus en plus les cibles d’attaques malveillantes qui exploitent des vulnérabilités connues courantes. Un WAF centralisé permet de se prémunir à la fois contre les attaques web et de simplifier la gestion de la sécurité sans avoir à modifier l’application.

Le WAF Azure traite différentes catégories d’attaques, notamment l’injection de code SQL, les scripts de site à site, les violations et anomalies de protocole HTTP, les robots, les scanneurs, les erreurs de configuration d’application courantes, le déni de service HTTP et d’autres attaques courantes comme l’injection de commandes, la dissimulation de requête HTTP, le fractionnement de réponse HTTP et les attaques par inclusion de fichier distant. 

Vous pouvez créer une passerelle d’application avec WAF ou ajouter WAF à une passerelle d’application existante. Dans les deux cas, Azure Application Gateway requiert son propre sous-réseau.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Comment créer une passerelle d’application avec WAF ? 

Pour créer une nouvelle passerelle d’application avec WAF activé, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Créer une ressource** > **Mise en réseau** > **Application Gateway**.
5. Dans le panneau **Concepts de base** qui s’affiche, entrez des valeurs dans les champs suivants : Nom, Niveau (Standard ou WAF), Taille de la référence (SKU) (Petite, Moyenne ou Grande), Nombre d’instances (2 pour la haute disponibilité), Abonnement, Groupe de ressources et Emplacement.
6. Dans le panneau **Paramètres** qui s’affiche sous **Réseau virtuel**, cliquez sur **Choisir un réseau virtuel**. Vous accédez alors au panneau Choisir un réseau virtuel.

7. Cliquez sur **Créer un nouveau** pour ouvrir le panneau **Créer un réseau virtuel**.

8. Entrez les valeurs suivantes : Nom, Espace d’adressage, Nom du sous-réseau, Plage d’adresses de sous-réseau. Cliquez sur **OK**.

9. Dans le panneau **Paramètres**, sous **Configuration d’adresse IP frontale**, choisissez le type d’adresse IP.

10. Cliquez sur **choisir une adresse IP publique,** puis **Créer un nouveau.**

11. Acceptez la valeur par défaut, puis cliquez sur **OK.**

12. Dans le panneau **Paramètres**, sous **Configuration de l’écouteur**, sélectionnez HTTP ou HTTPS sous **Protocole**. Un certificat est requis pour utiliser HTTPS.

13. Configurez les paramètres spécifiques au WAF : **État du pare-feu** (**Activé**) et **Mode du pare-feu** (**Prévention**). Si vous choisissez le mode **Détection**, le trafic est uniquement journalisé.

14. Passez en revue la page **Résumé** et cliquez sur **OK**. La passerelle Application Gateway est maintenant mise en file d’attente et créée.

Une fois la passerelle d’application créée, vous pouvez y accéder dans le portail pour poursuivre sa configuration.

![passerelle d’application créée](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Comment ajouter WAF à une application existante ?

Pour mettre à jour une passerelle d’application existante afin de prendre en charge WAF en mode Prévention, procédez comme suit :

1. Allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**.

2. Cliquez sur la passerelle d’application existante dans le panneau **Toutes les ressources**. 
>[!NOTE]
Remarque : si l’abonnement déjà sélectionné a plusieurs ressources, vous pouvez entrer le nom dans Filtrer par nom... pour accéder facilement à la zone DNS.
3. Cliquez sur **Pare-feu d’application web** et mettez à jour les paramètres de la passerelle d’application : **Mettre à niveau au niveau WAF** (coché), **État du pare-feu** (activé), **Mode du pare-feu** (Prévention). Vous devez également configurer l’ensemble de règles et des règles désactivées.

Pour plus d’informations sur la création d’une passerelle d’application avec WAF et sur la manière d’ajouter WAF à une passerelle d’application existante, consultez [Créer une passerelle d’application avec le pare-feu d’application web à l’aide du portail.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Network Security Group

Un [groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contient la liste des règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées aux [réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/) (VNet). Les NSG peuvent être associés à des sous-réseaux ou à des machines virtuelles individuelles. Lorsqu’un NSG est associé à un sous-réseau, les règles s’appliquent à toutes les ressources connectées au sous-réseau. Vous pouvez restreindre davantage le trafic en associant également un NSG à une machine virtuelle ou à une NIC.

Les NSG contiennent quatre propriétés : Nom, Région, Groupe de ressources et Règles.
>[!Note]
Même si un NSG existe dans un groupe de ressources, il peut être associé aux ressources de n’importe quel groupe de ressources, tant que ces ressources font partie de la même région Azure que le NSG.

Les règles de NSG contiennent neuf propriétés : Nom, Protocole (TCP, UDP ou \*, qui inclut ICMP ainsi que UDP et TCP), Plage de ports source, Plage de ports de destination, Préfixe d’adresse source, Préfixe d’adresse de destination, Direction (entrant ou sortant), Priorité (comprise entre 100 et 4 096) et Type d’accès (autorisation ou refus). Tous les NSG contiennent un ensemble de règles par défaut que vous pouvez supprimer ou remplacer par les règles que vous créez.

#### <a name="how-do-i-implement-nsgs"></a>Comment implémenter des NSG ?

Mettre en place des NSG nécessite une planification et il existe plusieurs considérations de conception à prendre en compte. Celles-ci incluent les limites du nombre de NSG par abonnement et de règles par NSG, la conception de réseau virtuel et de sous-réseau, les règles spéciales, le trafic ICMP, l’isolation des niveaux avec sous-réseaux, les équilibreurs de charge, etc.

Pour plus d’informations sur la planification et l’implémentation des NSG, ainsi que pour obtenir un exemple de scénario de déploiement, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Comment créer des règles dans un NSG ?

Pour créer des règles de trafic entrant dans un NSG existant, procédez comme suit :

1. Cliquez sur **Tous les services**, puis sur **Groupes de sécurité réseau**.

2. Dans la liste des NSG, cliquez sur **NSG-FrontEnd**, puis **Règles de sécurité de trafic entrant.**

3. Dans la liste des règles de sécurité de trafic entrant, cliquez sur **Ajouter.**

4. Entrez les valeurs dans les champs suivants : Nom, Priorité, Source, Protocole, Plage source, Destination, Plage de ports de destination et Action.

Après quelques secondes, la nouvelle règle s’affiche dans le NSG.

![règles de sécurité réseau](media/protect-netsec/inbound-security.png)

Pour obtenir des instructions sur la manière de créer des NSG dans des sous-réseaux, de créer des règles et d’associer un NSG à un sous-réseau frontal et principal, consultez [Créer des groupes de sécurité réseau à l’aide du portail Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Étapes suivantes

[Sécurité du réseau Azure](https://azure.microsoft.com/blog/azure-network-security/)

[Bonnes pratiques en matière de sécurité du réseau Azure](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Obtenir des informations sur un groupe de sécurité réseau](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Pare-feu d’application web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
