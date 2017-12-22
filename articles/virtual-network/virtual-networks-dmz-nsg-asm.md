---
title: "Exemple de zone Azure DMZ – Créer une zone DMZ simple avec des groupes de sécurité réseau | Microsoft Docs"
description: "Générer une zone DMZ avec des groupes de sécurité réseau (NSG)"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Exemple 1 : Générer une zone DMZ simple en utilisant des groupes de sécurité réseau (NSG) avec Classic PowerShell
[Revenir à la page Meilleures pratiques relatives aux frontières de sécurité][HOME]

> [!div class="op_single_selector"]
> * [Modèle Resource Manager](virtual-networks-dmz-nsg.md)
> * [Classic - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Cet exemple crée une zone DMZ primitive avec quatre serveurs Windows et des groupes de sécurité réseau. Cet exemple décrit chacune des commandes PowerShell correspondantes pour vous aider à mieux comprendre chaque étape. Il comporte également une section Scénario de trafic (Traffic Scenario) qui explique en détail et étape par étape l’évolution du trafic à travers les couches de défense dans la zone DMZ. Enfin, dans la section de référence se trouve l’intégralité du code et des instructions permettant d’élaborer l’environnement destiné à tester et à expérimenter différents scénarios. 

![Réseau de périmètre entrant avec groupe de sécurité réseau][1]

## <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, un abonnement contient les ressources suivantes :

* deux services cloud : « FrontEnd001 », « BackEnd001 »,
* Un réseau virtuel « CorpNetwork » avec deux sous-réseaux « FrontEnd » et « BackEnd »
* un groupe de sécurité réseau est appliqué aux deux sous-réseaux,
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)

Dans la section Références figure un script PowerShell qui génère une grande partie l’environnement décrit dans cet exemple. La création de machines virtuelles et de réseaux virtuels, bien qu’effectuée par l’exemple de script, ne figure pas en détail dans ce document. 

Pour créer l’environnement :

1. Enregistrer le fichier XML de configuration réseau contenu dans la section Références (mis à jour avec les noms, l’emplacement et les adresses IP correspondant à un scénario donné)
2. Mettre à jour les variables de l’utilisateur dans le script pour qu’elles correspondent à l’environnement dans lequel le script est exécuté (abonnements, noms de service, etc.)
3. Exécuter le script dans PowerShell

>[!Note]
>La région indiquée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.
>
>

Une fois que le script s’exécute correctement, d’autres opérations peuvent être effectuées. Dans la section Références, il existe deux scripts servant à configurer le serveur web et le serveur d’application avec une application web simple permettant le test avec cette configuration réseau de périmètre DMZ.

Les sections suivantes fournissent une description détaillée des groupes de sécurité réseau et de leur fonctionnement pour cet exemple, grâce à l’examen des lignes clés du script PowerShell.

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Pour cet exemple, un groupe NSG est créé, puis chargé avec six règles. 

> [!TIP]
> En règle générale, vous devez d’abord créer les règles d’« autorisation » spécifiques, puis les règles de « refus » plus générales. La priorité établit les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic répond à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).
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

Il existe une règle par défaut qui autorise le trafic sortant vers Internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, l’itinéraire défini par l’utilisateur est requis et cette opération est expliquée dans l’exemple 3 ci-dessous à la [page Meilleures pratiques relatives aux frontières de sécurité][HOME].

Chaque règle est abordée plus en détail par la suite (**Remarque**: tous les éléments de la liste ci-dessous commençant par un signe dollar (par exemple : $NSGName) sont des variables définies par l’utilisateur à partir du script de la section Références de ce document) :

1. Tout d’abord, un groupe de sécurité réseau doit être généré. Il contiendra les règles :

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. La première règle de cet exemple autorise le trafic DNS entre tous les réseaux internes au serveur DNS sur le sous-réseau du serveur principal. La règle comporte certains paramètres importants :
   
   * « Type » indique le sens du trafic qui sera appliqué à cette règle. La direction est déterminée du point de vue du sous-réseau ou de la machine virtuelle (selon l'élément auquel ce groupe de sécurité réseau est lié). Donc si le Type est « Entrant » et si le trafic entre dans le sous-réseau, la règle s’applique et le trafic quittant le sous-réseau n’est pas concerné.
   * « Priorité » définit l’ordre dans lequel le flux de trafic est évalué. Plus le numéro de priorité est faible, plus la priorité de la règle est élevée. Lorsqu’une règle s’applique à un flux de trafic spécifique, aucune autre règle n’est traitée. Donc si une règle de priorité 1 autorise le trafic et une règle de priorité 2 le refuse, si les deux règles s’appliquent, alors le trafic est autorisé à circuler (dans la mesure où la règle 1 a une priorité plus élevée et est appliquée, aucune autre règle n’est prise en compte).
   * « Action » indique si le trafic concerné par cette règle est bloqué ou autorisé.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Cette règle autorise le trafic RDP à circuler depuis Internet vers le port RDP de n’importe quel serveur du sous-réseau lié. Cette règle utilise deux types spéciaux de préfixes d’adresses ; « VIRTUAL_NETWORK » et « INTERNET ». Ces balises constituent un moyen simple de traiter une catégorie plus importante de préfixes d’adresses.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Cette règle autorise le trafic Internet entrant à accéder au serveur web. Cette règle ne modifie pas le comportement du routage. La règle n’autorise que le trafic destiné à IIS01. Donc, si le trafic en provenance d’Internet a pour destination le serveur web, cette règle l’autorise et arrête le traitement des règles suivantes. (Dans la règle de priorité 140, tout autre trafic Internet entrant est bloqué). Si vous traitez uniquement le trafic HTTP, cette règle peut encore être restreinte pour autoriser uniquement le trafic à destination du Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Cette règle autorise le trafic à passer du serveur IIS01 au serveur AppVM01. Une autre règle bloque tout autre trafic du serveur frontal vers le serveur principal. Pour améliorer cette règle, si le port est connu, il doit être ajouté. Par exemple, si le serveur IIS accède uniquement au serveur SQL Server sur AppVM01, la plage de ports de destination doit passer de « * » (tout) à 1433 (le port SQL), ce qui réduit la surface vulnérable sur AppVM01 au cas où l’application web serait compromise.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Cette règle refuse le trafic en provenance d’Internet vers des serveurs sur le réseau. Avec les règles de priorité 110 et 120, elle permet uniquement au trafic Internet entrant d’accéder au pare-feu et aux ports RDP d’autres serveurs et bloque tout le reste. Cette règle est une règle de « prévention de défaillance » qui bloque tous les flux inattendus.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. La règle finale refuse le trafic du sous-réseau du serveur frontal vers le sous-réseau du serveur principal. Dans la mesure où il s’agit d’une règle entrante uniquement, le trafic en sens inverse est autorisé (du serveur principal vers le serveur frontal).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Scénarios de trafic
#### <a name="allowed-internet-to-web-server"></a>(*Autorisé*) Internet vers le serveur web
1. Un utilisateur Internet demande une page HTTP en provenance de FrontEnd001.CloudApp.Net (service cloud face à Internet)
2. Le service Cloud transfère le trafic via un point de terminaison ouvert sur le port 80 vers IIS01 (serveur web)
3. Le sous-réseau du serveur frontal commence le traitement des règles de trafic entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 3 (Internet pour IIS01) s’applique, le trafic est autorisé, arrêter le traitement.
4. Le trafic parvient à l’adresse IP interne du serveur web IIS01 (10.0.1.5).
5. IIS01 écoute le trafic web, reçoit cette requête et commence à traiter la demande.
6. IIS01 demande des informations au serveur SQL Server sur AppVM01
7. Comme il n'existe aucune règle sortante sur le sous-réseau du serveur frontal, le trafic est autorisé
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

#### <a name="allowed-rdp-to-backend"></a>(*Autorisé*) RDP vers le serveur principal
1. L’administrateur du serveur sur Internet demande une session RDP AppVM01 sur BackEnd001.CloudApp.Net:xxxxx, où xxxxx est le numéro de port attribué de façon aléatoire au trafic RDP vers AppVM01 (le port attribué se trouve sur le portail Azure ou via PowerShell)
2. Le sous-réseau du serveur principal entame le traitement du réseau entrant :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) s’applique, le trafic est autorisé, arrêter le traitement des règles
3. En l’absence de réseau sortant, les règles par défaut s’appliquent et le retour de trafic est autorisé
4. La session RDP est activée
5. AppVM01 demande le nom et le mot de passe de l'utilisateur

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
7. Le serveur IIS reçoit le fichier

#### <a name="denied-web-to-backend-server"></a>(*Refusé*) Web vers le serveur principal
1. Un utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net
2. Comme il n’y a aucun point de terminaison ouvert pour le partage de fichiers, ce trafic ne passe pas le service cloud et n’atteint pas le serveur
3. Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG 5 (Internet vers le réseau virtuel) bloque ce trafic

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Refusé*) recherche DNS web sur le serveur DNS
1. Un utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01 par le biais du service BackEnd001.CloudApp.Net
2. Comme aucun point de terminaison n’est ouvert pour DNS, ce trafic ne passe pas le service Cloud et n’atteint pas le serveur
3. Si les points de terminaison ont été ouverts pour une raison quelconque, la règle NSG 5 (Internet vers réseau virtuel) bloque ce trafic (Remarque : cette règle 1 (DNS) ne s’applique pas pour deux raisons : tout d’abord l’adresse source est sur Internet, et cette règle s’applique uniquement lorsque la source locale est le réseau virtuel local ; de plus, s’il s’agit d’une règle d’autorisation, le trafic n’est jamais refusé)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Refusé*) Accès web vers SQL via le pare-feu
1. Un utilisateur Internet demande des données SQL de FrontEnd001.CloudApp.Net (Service cloud face à Internet)
2. Comme aucun point de terminaison n’est ouvert pour SQL, ce trafic ne franchit pas le service cloud et n’atteint pas le pare-feu
3. Si des points de terminaison étaient ouverts pour une raison quelconque, le sous-réseau frontal commence le traitement des règles entrantes :
   1. La règle NSG 1 (DNS) ne s’applique pas, passer à la règle suivante
   2. La règle NSG 2 (RDP) ne s’applique pas, passer à la règle suivante
   3. La règle NSG 3 (Internet pour IIS01) s’applique, le trafic est autorisé, arrêter le traitement.
4. Le trafic parvient à l’adresse IP de IIS01 (10.0.1.5).
5. IIS01 n’est pas à l’écoute sur le port 1433, donc aucune réponse à la demande

## <a name="conclusion"></a>Conclusion
Cet exemple est un moyen relativement simple et direct d’isoler le sous-réseau du serveur principal du trafic entrant.

Vous trouverez d’autres exemples et une vue d’ensemble des frontières de sécurité réseau [ici][HOME].

## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et configuration réseau
Enregistrez le script complet dans un fichier de script PowerShell. Enregistrez la configuration réseau dans un fichier nommé « NetworkConf1.xml ».
Modifiez les variables définies par l’utilisateur selon vos besoins puis exécutez le script.

#### <a name="full-script"></a>Script complet
Ce script exécutera les actions suivantes en fonction des variables définies par l’utilisateur ;

1. Connexion à un abonnement Azure
2. Créez un compte de stockage.
3. Création d’un réseau virtuel et de deux sous-réseaux, comme indiqué dans le fichier de configuration du réseau
4. Génération de quatre machines virtuelles Windows Server
5. Configurez un groupe de sécurité réseau, notamment :
   * Création d’un groupe de sécurité réseau
   * Ajout de règles à ce dernier
   * La liaison du groupe de sécurité réseaux au sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur un PC ou un serveur connecté à Internet.

> [!IMPORTANT]
> Lorsque ce script est exécuté, des avertissements ou autres messages d’information peuvent s’afficher dans PowerShell. Seuls les messages d’erreur affichés en rouge sont source de préoccupation.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

  BackEnd Service (BackEnd subnet 10.0.2.0/24)
   DNS01      - 10.0.2.4
   AppVM01    - 10.0.2.5
   AppVM02    - 10.0.2.6

#>

# Fixed Variables
    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
    $VMName = @()
    $ServiceName = @()
    $VMFamily = @()
    $img = @()
    $size = @()
    $SubnetName = @()
    $VMIP = @()

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
        Return}
    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

  # Update Subscription Pointer to New Storage Account
    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

# Validation
$FatalError = $false

If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
     $FatalError = $true}

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
            $FatalError = $true}
        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

If ($FatalError) {
    Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
    Return}
Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

# Create VNET
    Write-Host "Creating VNET" -ForegroundColor Cyan 
    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

# Create Services
    Write-Host "Creating Services" -ForegroundColor Cyan
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Fichier de configuration réseau
Enregistrez ce fichier XML avec l’emplacement mis à jour et ajoutez le lien vers ce fichier à la variable $NetworkConfigFile dans le script précédent.

```XML
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
        <DnsServer name="Level3" IPAddress="209.244.0.3" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FrontEnd">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BackEnd">
            <AddressPrefix>10.0.2.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
        <DnsServersRef>
          <DnsServerRef name="DNS01" />
          <DnsServerRef name="Level3" />
        </DnsServersRef>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Exemples de scripts d’application
Si vous souhaitez installer un exemple d’application et d’autres exemples de zone DMZ, vous en trouverez à l’adresse suivante : [Exemple de script d’application][SampleApp]

## <a name="next-steps"></a>Étapes suivantes
* Mise à jour et enregistrement du fichier XML
* Exécution du script PowerShell pour générer l’environnement
* Installation de l’exemple d’application
* Tester différents flux de trafic via cette zone DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Zone DMZ avec groupe de sécurité réseau (NSG)"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

