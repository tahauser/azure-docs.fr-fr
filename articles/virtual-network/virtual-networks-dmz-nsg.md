---
title: "Exemple de zone Azure DMZ – Créer une zone DMZ simple avec des groupes de sécurité réseau | Microsoft Docs"
description: "Générer une zone DMZ avec des groupes de sécurité réseau (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exemple 1 – Créer une zone DMZ simple à l’aide de groupes de sécurité réseau avec un modèle Azure Resource Manager
[Revenir à la page Meilleures pratiques relatives aux frontières de sécurité][HOME]

> [!div class="op_single_selector"]
> * [Modèle Resource Manager](virtual-networks-dmz-nsg.md)
> * [Classic - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Cet exemple crée une zone DMZ primitive avec quatre serveurs Windows et des groupes de sécurité réseau. Cet exemple décrit chacune des sections de modèle correspondantes pour vous aider à mieux comprendre chaque étape. Il comporte également une section Scénario de trafic (Traffic Scenario) qui explique en détail et étape par étape l’évolution du trafic à travers les couches de défense dans la zone DMZ. Enfin, dans la section de référence se trouve l’intégralité du modèle de code et des instructions permettant d’élaborer l’environnement destiné à tester et à expérimenter différents scénarios. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Réseau de périmètre entrant avec groupe de sécurité réseau][1]

## <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, un abonnement contient les ressources suivantes :

* un seul groupe de ressources.
* un réseau virtuel avec deux sous-réseaux « FrontEnd » et « BackEnd »,
* un groupe de sécurité réseau est appliqué aux deux sous-réseaux,
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »),
* Une adresse IP publique associée au serveur d’applications web

La section Références comporte un lien vers un modèle Azure Resource Manager qui génère l’environnement décrit dans cet exemple. La création de machines virtuelles et de réseaux virtuels, bien qu’effectuée par l’exemple de script, n'est pas décrite en détail dans ce document. 

**Pour créer cet environnement** (des instructions détaillées figurent dans la section Références de ce document) ;

1. Déployez le modèle Resource Manager dans : [Modèles de démarrage rapide Azure][Template]
2. Installez l’exemple d’application dans : [Exemple de script d'application][SampleApp]

>[!NOTE]
>Pour établir une connexion RDP à des serveurs principaux dans cette instance, le serveur IIS est utilisé comme une « jump box ». Établissez d'abord une connexion RDP au serveur IIS, puis au serveur principal à partir d'une connexion IIS Server. Une adresse IP publique peut être associée à chaque carte réseau du serveur pour faciliter la connexion RDP.
> 
>

Les sections suivantes fournissent une description détaillée des groupes de sécurité réseau et de leur fonctionnement pour cet exemple, grâce à l’examen des lignes clés du modèle Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Pour cet exemple, un groupe NSG est créé, puis chargé avec six règles. 

>[!TIP]
>En règle générale, vous devez d’abord créer les règles d’« autorisation » spécifiques, puis les règles de « refus » plus générales. La priorité établit les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic répond à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).
>
>

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1. Le trafic DNS interne (port 53) est autorisé
2. Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé.
3. Le trafic HTTP (port 80) à partir d’Internet vers le serveur web (IIS01) est autorisé.
4. Tout trafic (tous les ports) en provenance d’IIS01 vers AppVM1 est autorisé.
5. Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6. Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Lorsque ces règles sont associées à chacun des sous-réseaux, si une requête HTTP entrante en provenance d’HTTP arrive d’Internet à destination du serveur web, les 3 règles (autorisation) et les 5 règles (refus) s’appliquent. Cependant, comme la règle 3 a une priorité plus élevée, elle seule s’applique, et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au serveur web. Si le même trafic tentait d’atteindre le serveur DNS01, la règle 5 (Refus) serait la première à s’appliquer et le trafic ne serait pas autorisé à accéder au serveur. La règle 6 (Refus) bloque la communication du sous-réseau frontal vers le sous-réseau principal (excepté le trafic autorisé dans les règles 1 et 4), ce qui protège le réseau principal en cas d’attaque d’une personne mal intentionnée sur l’application web sur le serveur frontal. Cette personne aurait alors un accès limité au réseau principal « protégé » (uniquement les ressources exposées sur le serveur AppVM01).

Il existe une règle par défaut qui autorise le trafic sortant vers Internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour appliquer une stratégie de sécurité au trafic entrant dans les deux sens, un routage défini par l'utilisateur est requis, comme détaillé dans « Exemple 3 » à la [page Meilleures pratiques relatives aux frontières de sécurité][HOME].

Chaque règle est décrite plus en détail comme suit :

1. Une ressource de groupe de sécurité réseau doit être instanciée pour contenir les règles :

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. La première règle de cet exemple autorise le trafic DNS entre tous les réseaux internes au serveur DNS sur le sous-réseau du serveur principal. La règle comporte certains paramètres importants :
  * « destinationAddressPrefix » - Les règles peuvent utiliser un type spécial de préfixe d’adresse appelé « balise par défaut » ; ces balises sont des identificateurs fournis par le système qui permettent de facilement adresser une catégorie supérieure de préfixes d’adresses. Cette règle utilise la balise par défaut « Internet » pour identifier toute adresse en dehors du réseau virtuel. Les autres étiquettes de préfixe sont VirtualNetwork et AzureLoadBalancer.
  * « Direction » indique le sens du trafic qui sera appliqué à cette règle. La direction est déterminée du point de vue du sous-réseau ou de la machine virtuelle (selon l'élément auquel ce groupe de sécurité réseau est lié). Donc si Direction est « Entrant » et si le trafic entre dans le sous-réseau, la règle s’applique et le trafic quittant le sous-réseau n’est pas concerné.
  * « Priorité » définit l’ordre dans lequel le flux de trafic est évalué. Plus le numéro de priorité est faible, plus la priorité de la règle est élevée. Lorsqu’une règle s’applique à un flux de trafic spécifique, aucune autre règle n’est traitée. Donc si une règle de priorité 1 autorise le trafic et une règle de priorité 2 le refuse, si les deux règles s’appliquent, alors le trafic est autorisé à circuler (dans la mesure où la règle 1 a une priorité plus élevée et est appliquée, aucune autre règle n’est prise en compte).
  * « Accès » indique si le trafic concerné par cette règle est bloqué (« Deny ») ou autorisé (« Allow »).

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Cette règle autorise le trafic RDP à circuler depuis Internet vers le port RDP de n’importe quel serveur du sous-réseau lié. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Cette règle autorise le trafic Internet entrant à accéder au serveur web. Cette règle ne modifie pas le comportement du routage. La règle n’autorise que le trafic destiné à IIS01. Donc, si le trafic en provenance d’Internet a pour destination le serveur web, cette règle l’autorise et arrête le traitement des règles suivantes. (Dans la règle de priorité 140, tout autre trafic Internet entrant est bloqué). Si vous traitez uniquement le trafic HTTP, cette règle peut encore être restreinte pour autoriser uniquement le trafic à destination du Port 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Cette règle autorise le trafic à passer du serveur IIS01 au serveur AppVM01. Une autre règle bloque tout autre trafic du serveur frontal vers le serveur principal. Pour améliorer cette règle, si le port est connu, il doit être ajouté. Par exemple, si le serveur IIS accède uniquement au serveur SQL Server sur AppVM01, la plage de ports de destination doit passer de « * » (tout) à 1433 (le port SQL), ce qui réduit la surface vulnérable sur AppVM01 au cas où l’application web serait compromise.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Cette règle refuse le trafic en provenance d’Internet vers des serveurs sur le réseau. Avec les règles de priorité 110 et 120, elle permet uniquement au trafic Internet entrant d’accéder au pare-feu et aux ports RDP d’autres serveurs et bloque tout le reste. Cette règle est une règle de « prévention de défaillance » qui bloque tous les flux inattendus.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. La règle finale refuse le trafic du sous-réseau du serveur frontal vers le sous-réseau du serveur principal. Dans la mesure où il s’agit d’une règle entrante uniquement, le trafic en sens inverse est autorisé (du serveur principal vers le serveur frontal).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Scénarios de trafic
#### <a name="allowed-internet-to-web-server"></a>(*Autorisé*) Internet vers le serveur web
1. Un utilisateur Internet demande une page HTTP à partir de l’adresse IP publique de la carte réseau associée à la carte réseau IIS01
2. L’adresse IP publique transmet le trafic au réseau virtuel vers IIS01 (serveur web)
3. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
  1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
  2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
  3. La règle NSG 3 (Internet pour IIS01) s’applique, le trafic est autorisé, arrêter le traitement.
4. Le trafic parvient à l’adresse IP interne du serveur web IIS01 (10.0.1.5).
5. IIS01 écoute le trafic web, reçoit cette requête et commence à traiter la demande.
6. IIS01 demande des informations au serveur SQL Server sur AppVM01
7. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
8. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
  1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
  2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
  3. La règle NSG 3 (Internet vers le pare-feu) ne s’applique pas, passer à la règle suivante
  4. La règle NSG 4 (IIS01 vers AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles
9. AppVM01 reçoit la requête SQL et répond
10. Comme il n’existe aucune règle sur le trafic sortant sur le sous-réseau du serveur principal, la réponse est autorisée
11. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
  1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
  2. La règle du système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé.
12. Le serveur IIS reçoit la réponse SQL, complète la réponse HTTP et l’envoie au demandeur
13. Comme il n’existe aucune règle sortante sur le sous-réseau du serveur frontal, la réponse est autorisée, et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-rdp-to-iis-server"></a>(*Autorisé*) Connexion RDP vers le serveur IIS
1. Un administrateur de serveur sur Internet demande une session RDP IIS01 sur l’adresse IP publique de la carte réseau associée à la carte réseau IIS01 (cette adresse IP publique est accessible via le portail ou PowerShell)
2. L’adresse IP publique transmet le trafic au réseau virtuel vers IIS01 (serveur web)
3. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
  1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
  2. La règle NSG 2 (RDP) s’applique, le trafic est autorisé, arrêter le traitement des règles
4. En l’absence de réseau sortant, les règles par défaut s’appliquent et le retour de trafic est autorisé
5. La session RDP est activée
6. IIS01 demande le nom et le mot de passe de l'utilisateur

>[!NOTE]
>Pour établir une connexion RDP à des serveurs principaux dans cette instance, le serveur IIS est utilisé comme une « jump box ». Établissez d'abord une connexion RDP au serveur IIS, puis au serveur principal à partir d'une connexion IIS Server.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Autorisé*) recherche DNS du serveur web sur le serveur DNS
1. Le serveur Web Server IIS01 a besoin d’un flux de données sur www.data.gov, mais doit résoudre l’adresse.
2. La configuration réseau du réseau virtuel définit DNS01 (10.0.2.4 sur le sous-réseau du serveur principal) comme serveur DNS principal, IIS01 envoie la requête DNS à DNS01
3. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
4. Le sous-réseau du serveur principal entame le traitement du réseau entrant :
  * La règle NSG 1 (DNS) s’applique, le trafic est autorisé, arrêter le traitement des règles
5. Le serveur DNS reçoit la demande
6. Le serveur DNS n’a pas d’adresse en cache et demande à un serveur DNS racine sur Internet.
7. Aucune règle sortante sur le sous-réseau du serveur principal, le trafic est autorisé
8. Un serveur Internet DNS répond, car cette session a été initialisée en interne, la réponse est autorisée
9. Le serveur DNS met en cache la réponse et répond à la demande initiale à IIS01
10. Aucune règle sortante sur le sous-réseau du serveur principal, le trafic est autorisé
11. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
  1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
  2. La règle système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé
12. IIS01 reçoit la réponse de la part de DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Autorisé*) fichier d’accès de serveur Web sur AppVM01
1. IIS01 demande un fichier sur AppVM01
2. Aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
3. Le sous-réseau du serveur principal commence le traitement de la règle de trafic entrant :
  1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
  2. La règle NSG 2 (RDP) ne s’applique pas. Passer à la règle suivante.
  3. La règle NSG 3 (Internet vers le IIS01) ne s’applique pas. Passer à la règle suivante.
  4. La règle NSG 4 (IIS01 vers AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles.
4. AppVM01 reçoit la demande et répond avec un fichier (en supposant que l’accès est autorisé)
5. Comme il n’existe aucune règle sur le trafic sortant sur le sous-réseau du serveur principal, la réponse est autorisée
6. Le sous-réseau du serveur frontal commence le traitement de la règle de trafic entrant :
  1. Aucune règle NSG ne s’applique au trafic entrant en provenance du sous-réseau du serveur principal vers le sous-réseau du serveur frontal, par conséquent aucune des règles NSG ne s’applique
  2. La règle du système par défaut autorisant le trafic entre sous-réseaux autorise le trafic, le trafic est donc autorisé.
7. Le serveur IIS reçoit la demande.

#### <a name="denied-rdp-to-backend"></a>(*Refusé*) connexion RDP vers le serveur principal
1. Un utilisateur Internet tente d'établir une connexion RDP au serveur AppVM01
2. Étant donné qu’aucune adresse IP publique n'est associée à la carte réseau de ce serveur, ce trafic n'accède jamais au réseau virtuel et n'atteint pas le serveur
3. Toutefois, si une adresse IP publique a été activée pour une raison quelconque, la règle du groupe de sécurité réseau 2 (RDP) autorise ce trafic

>[!NOTE]
>Pour établir une connexion RDP à des serveurs principaux dans cette instance, le serveur IIS est utilisé comme une « jump box ». Établissez d'abord une connexion RDP au serveur IIS, puis au serveur principal à partir d'une connexion IIS Server.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Refusé*) Web vers le serveur principal
1. Un utilisateur Internet tente d’accéder à un fichier sur AppVM01
2. Étant donné qu’aucune adresse IP publique n'est associée à la carte réseau de ce serveur, ce trafic n'accède jamais au réseau virtuel et n'atteint pas le serveur
3. Si une adresse IP publique a été activée pour une raison quelconque, la règle du groupe de sécurité réseau 5 (Internet vers le réseau virtuel) bloque ce trafic

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Refusé*) recherche DNS web sur le serveur DNS
1. Un utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01
2. Étant donné qu’aucune adresse IP publique n'est associée à la carte réseau de ce serveur, ce trafic n'accède jamais au réseau virtuel et n'atteint pas le serveur
3. Si une adresse IP publique a été activée pour une raison quelconque, la règle du groupe de sécurité réseau 5 (Internet vers le réseau virtuel) bloque ce trafic. (Remarque : cette règle 1 (DNS) ne s’applique pas car l’adresse source des demandes est Internet et que la règle 1 s’applique uniquement au réseau local virtuel comme source)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Refusé*) Accès SQL au serveur web
1. Un utilisateur Internet demande des données SQL à partir de IIS01
2. Étant donné qu’aucune adresse IP publique n'est associée à la carte réseau de ce serveur, ce trafic n'accède jamais au réseau virtuel et n'atteint pas le serveur
3. Si une adresse IP publique a été activée pour une raison quelconque, le sous-réseau frontal commence le traitement des règles entrantes :
  1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
  2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
  3. La règle NSG 3 (Internet pour IIS01) s’applique, le trafic est autorisé, arrêter le traitement.
4. Le trafic parvient à l’adresse IP de IIS01 (10.0.1.5).
5. IIS01 n’est pas à l’écoute sur le port 1433, donc aucune réponse à la demande

## <a name="conclusion"></a>Conclusion
Cet exemple est un moyen relativement simple et direct d’isoler le sous-réseau du serveur principal du trafic entrant.

Vous trouverez d’autres exemples et une vue d’ensemble des frontières de sécurité réseau [ici][HOME].

## <a name="references"></a>Références
### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Cet exemple utilise un modèle Azure Resource Manager prédéfini dans un référentiel GitHub géré par Microsoft et ouvert à la communauté. Ce modèle peut être déployé directement depuis GitHub ou téléchargé et modifié selon vos besoins. 

Le modèle principal figure dans un fichier nommé « azuredeploy.json. » Ce modèle peut être soumis via PowerShell ou l’interface CLI (avec le fichier « azuredeploy.parameters.json » associé) pour déployer ce modèle. Le moyen le plus simple consiste à utiliser le bouton « Déployer dans Azure » sur la page README.md de GitHub.

Pour déployer le modèle qui génère cet exemple à partir de GitHub et du portail Azure, procédez comme suit :

1. À partir d’un navigateur, accédez au [modèle][Template]
2. Cliquez sur le bouton « Déployer dans Azure » (ou le bouton « Visualiser » pour afficher une représentation graphique de ce modèle)
3. Entrez le compte de stockage, le nom d’utilisateur et le mot de passe dans le panneau Paramètres, puis cliquez sur **OK**
5. Créez un groupe de ressources pour ce déploiement (vous pouvez utiliser un groupe existant, mais il est recommandé d'en créer un nouveau pour obtenir de meilleurs résultats)
6. Si nécessaire, modifiez les paramètres Abonnement et Emplacement de votre réseau virtuel.
7. Cliquez sur **Consulter les termes et conditions**, lisez les termes du contrat, puis cliquez sur **Acheter** si vous êtes d’accord.
8. Cliquez sur **Créer** pour commencer le déploiement de ce modèle.
9. Une fois le déploiement correctement terminé, accédez au groupe de ressources créé pour ce déploiement afin d'en afficher les ressources configurées.

>[!NOTE]
>Ce modèle permet d'établir une connexion RDP uniquement au serveur IIS01 (recherchez l’adresse IP publique pour IIS01 sur le portail). Pour établir une connexion RDP à des serveurs principaux dans cette instance, le serveur IIS est utilisé comme une « jump box ». Établissez d'abord une connexion RDP au serveur IIS, puis au serveur principal à partir d'une connexion IIS Server.
>
>

Pour supprimer ce déploiement, supprimez le groupe de ressources (toutes les ressources enfants seront également supprimées).

#### <a name="sample-application-scripts"></a>Exemples de scripts d’application
Une fois le modèle correctement exécuté, vous pouvez configurer le serveur web et le serveur d’application avec une application web simple permettant le test avec cette configuration DMZ. Pour installer un exemple d’application et d’autres exemples de zone DMZ, vous en trouverez à l’adresse suivante : [Exemple de script d’application][SampleApp]

## <a name="next-steps"></a>Étapes suivantes

* Déployer cet exemple
* Générer l’exemple d’application
* Tester différents flux de trafic via cette zone DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Zone DMZ avec groupe de sécurité réseau (NSG)"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md