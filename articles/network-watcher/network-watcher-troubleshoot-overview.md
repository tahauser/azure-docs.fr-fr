---
title: Introduction à la résolution des problèmes des ressources dans Azure Network Watcher | Microsoft Docs
description: Cette page fournit une vue d’ensemble des fonctionnalités de résolution des problèmes des ressources de Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 646caa5e4aacd58377c0a2b5985a69277d00cec3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Introduction à la résolution des problèmes des ressources dans Azure Network Watcher

Les passerelles de réseau virtuel assurent une connectivité entre les ressources locales et d’autres réseaux virtuels dans Azure. L’analyse des passerelles et de leurs connexions est essentielle pour assurer la non-interruption de la communication. Network Watcher permet de résoudre les problèmes des connexions et des passerelles. Il est possible de l’appeler par le biais du portail, de PowerShell, de l’interface de ligne de commande Azure ou de l’API REST. Lorsque cette fonctionnalité est appelée, Network Watcher diagnostique l’intégrité de la passerelle ou de la connexion et retourne les résultats appropriés. La demande est une transaction en cours d’exécution longue. Les résultats sont retournés lorsque le diagnostic est terminé.

![portail][2]

## <a name="results"></a>Résultats

Les résultats préliminaires retournés donnent une vue d’ensemble de l’intégrité de la ressource. Des informations plus détaillées peuvent être fournies pour les ressources, comme indiqué dans la section suivante :

La liste suivante répertorie les valeurs retournées par l’API de résolution des problèmes :

* **startTime** : heure de début de l’appel de l’API de résolution des problèmes.
* **endTime** : heure de fin de la résolution des problèmes.
* **code** : la valeur est définie sur UnHealthy en cas d’échec du diagnostic.
* **results** : ensemble de résultats retournés sur la connexion ou la passerelle de réseau virtuel.
    * **id** : type d’erreur.
    * **summary** : résumé de l’erreur.
    * **detailed** : description détaillée de l’erreur.
    * **recommendedActions** : cette propriété est une collection des mesures recommandées à adopter.
      * **actionText** : cette valeur contient le texte qui décrit l’action à entreprendre.
      * **actionUri** : URI de la documentation sur le mode d’action.
      * **actionUriText** : brève description du texte de l’action.

Les tableaux suivants présentent les différents types d’erreur (« id » dans les résultats de la liste précédente) qui sont disponibles et indiquent si l’erreur génère des journaux.

### <a name="gateway"></a>Passerelle

| Type d’erreur | Motif | Journal|
|---|---|---|
| NoFault | Quand aucune erreur n’est détectée |OUI|
| GatewayNotFound | Passerelle introuvable ou non approvisionnée |Non |
| PlannedMaintenance |  Instance de la passerelle en maintenance  |Non |
| UserDrivenUpdate | Cette défaillance se produit lorsqu’une mise à jour utilisateur est en cours. La mise à jour peut être d’une opération de redimensionnement. | Non  |
| VipUnResponsive | Cette défaillance se produit lorsque l’instance principale de la passerelle ne peut pas être atteinte en raison d’un échec de la sonde d’intégrité. | Non  |
| PlatformInActive | Il existe un problème avec la plateforme. | Non |
| ServiceNotRunning | Le service sous-jacent ne fonctionne pas. | Non |
| NoConnectionsFoundForGateway | Aucune connexion n’existe sur la passerelle. Cette défaillance est simplement un avertissement.| Non |
| ConnectionsNotConnected | Aucune connexion n’est établie. Cette défaillance est simplement un avertissement.| OUI|
| GatewayCPUUsageExceeded | L’utilisation du processeur par la passerelle est actuellement supérieure à 95 %. | OUI |

### <a name="connection"></a>Connexion

| Type d’erreur | Motif | Journal|
|---|---|---|
| NoFault | Quand aucune erreur n’est détectée |OUI|
| GatewayNotFound | Passerelle introuvable ou non approvisionnée |Non |
| PlannedMaintenance | Instance de la passerelle en maintenance  |Non |
| UserDrivenUpdate | Cette défaillance se produit lorsqu’une mise à jour utilisateur est en cours. La mise à jour peut être d’une opération de redimensionnement.  | Non  |
| VipUnResponsive | Cette défaillance se produit lorsque l’instance principale de la passerelle ne peut pas être atteinte en raison d’un échec de la sonde d’intégrité. | Non  |
| ConnectionEntityNotFound | La configuration de la connexion est manquante | Non  |
| ConnectionIsMarkedDisconnected | La connexion est identifiée comme étant « déconnectée » |Non |
| ConnectionNotConfiguredOnGateway | Le service sous-jacent n’a pas la connexion configurée. | OUI |
| ConnectionMarkedStandy | Le service sous-jacent est identifié comme étant en veille.| OUI|
| Authentification | Incompatibilité des clés prépartagées | OUI|
| PeerReachability | La passerelle homologue n’est pas accessible. | OUI|
| IkePolicyMismatch | Les stratégies IKE de la passerelle homologue ne sont pas prises en charge par Azure. | OUI|
| WfpParse Error | Une erreur s’est produite lors de l’analyse du journal de protection des fichiers Windows. |OUI|

## <a name="supported-gateway-types"></a>Types de passerelles pris en charge

La table suivante répertorie quelles passerelles et quelles connexions sont prises en charge avec la résolution des problèmes de Network Watcher :

|  |  |
|---------|---------|
|**Types de passerelles**   |         |
|VPN      | Prise en charge        |
|ExpressRoute | Non pris en charge |
|**Types de VPN** | |
|Route-based | Prise en charge|
|Policy-based | Non pris en charge|
|**Types de connexions**||
|IPsec| Prise en charge|
|Vnet2Vnet| Prise en charge|
|ExpressRoute| Non pris en charge|
|VPNClient| Non pris en charge|

## <a name="log-files"></a>Fichiers journaux

Les fichiers journaux de dépannage des ressources sont stockés dans un compte de stockage à l’issue de la résolution des problèmes des ressources. L’image suivante illustre l’exemple de contenu d’un appel générant une erreur.

![fichier zip][1]

> [!NOTE]
> Dans certains cas, seul un sous-ensemble des fichiers journaux est écrit dans l’espace de stockage.

Pour obtenir des instructions de téléchargement des fichiers à partir des comptes de stockage Azure, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). L’explorateur de stockage peut aussi être utilisé. Pour en savoir plus sur l’explorateur de stockage, cliquez sur le lien suivant : [Explorateur de stockage](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Le fichier **ConnectionStats.txt** contient les statistiques globales de la connexion, y compris les octets d’entrée et de sortie, l’état de connexion et le moment où la connexion a été établie.

> [!NOTE]
> Si l’appel à l’API de dépannage retourne la valeur Healthy, le seul élément retourné dans le fichier zip est un fichier **ConnectionStats.txt**.

Le contenu de ce fichier est semblable à l’exemple suivant :

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Le fichier **CPUStats.txt** contient des informations sur l’utilisation du processeur et la mémoire disponible au moment du test.  Le contenu de ce fichier est semblable à l’exemple suivant :

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Le fichier **IKEErrors.txt** contient les éventuelles erreurs IKE détectées pendant l’analyse.

L’exemple suivant illustre le contenu d’un fichier IKEErrors.txt. Vos erreurs peuvent être différentes en fonction du problème.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Le fichier journal **Scrubbed-wfpdiag.txt** contient le journal de protection des fichiers Windows. Ce journal contient la journalisation des rejets de paquets et des échecs IKE/AuthIP.

L’exemple suivant illustre le contenu du fichier Scrubbed-wfpdiag.txt. Dans cet exemple, la clé partagée d’une connexion n’était pas correcte, comme vous pouvez l’observer sur la troisième ligne à partir du bas. L’exemple suivant est simplement un extrait de la totalité du journal, car ce dernier peut être très long en fonction du problème.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.Sum

Le fichier **wfpdiag.txt.sum** est un journal présentant les mémoires tampons et les événements traités.

L’exemple suivant illustre le contenu d’un fichier wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment diagnostiquer les passerelles VPN et les connexions par le biais du portail en consultant l’article [Résolution des problèmes de passerelle - Portail Azure](network-watcher-troubleshoot-manage-portal.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
