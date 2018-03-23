---
title: Résoudre les problèmes liés à l’agent Linux pour Azure Log Analytics | Microsoft Docs
description: Décrit les symptômes, les causes et les solutions aux problèmes courants liés à l’agent Linux pour Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 80d7e39b284554ebfa8cac4488e1663b3e3648e8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Guide pratique pour résoudre les problèmes liés à l’agent Linux pour Log Analytics

Cet article aide à dépanner les erreurs que vous pourriez rencontrer avec Linux pour Log Analytics et suggère des solutions possibles pour les résoudre.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problème : impossible de se connecter via le proxy à Log Analytics

### <a name="probable-causes"></a>Causes probables
* Le proxy spécifié pendant l’intégration est incorrect
* Les points de terminaison des services Log Analytics et Azure Automation ne figurent pas dans la liste verte de votre centre de données 

### <a name="resolutions"></a>Résolutions
1. Réintégrez le service Log Analytics à l’agent OMS pour Linux à l’aide de la commande suivante avec l’option `-v` activée. Cela autorise une sortie détaillée de l’agent qui se connecte via le proxy au Service OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Pour vérifier que vous avez correctement configuré l’agent de manière à communiquer via un serveur proxy, consultez la section [Mettre à jour les paramètres du proxy](log-analytics-agent-manage.md#update-proxy-settings).    
* Vérifiez que les points de terminaison suivants du service Log Analytics figurent dans la liste verte :

    |Ressource de l'agent| Ports | Direction |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Trafic entrant et sortant |  
    |*.oms.opinsights.azure.com | Port 443| Trafic entrant et sortant |  
    |*.blob.core.windows.net | Port 443| Trafic entrant et sortant |  
    |* .azure-automation.net | Port 443| Trafic entrant et sortant | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problème : Vous recevez une erreur 403 lorsque vous tentez d’effectuer l’intégration

### <a name="probable-causes"></a>Causes probables
* La date et l’heure sont incorrectes sur le serveur Linux 
* L’ID et la clé d’espace de travail utilisés sont incorrects

### <a name="resolution"></a>Résolution :

1. Vérifiez l’heure sur votre serveur Linux avec la commande date. Si l’heure est à +/-15 minutes de l’heure actuelle, l’intégration échoue. Pour corriger ce problème, mettez à jour de la date et/ou le fuseau horaire de votre serveur Linux. 
2. Vérifiez que vous avez installé la dernière version de l’agent OMS pour Linux.  Celle-ci vous avertit si le décalage horaire est à l’origine de l’échec d’intégration.
3. Relancez l’intégration avec la bonne clé et le bon identifiant d’espace de travail en suivant les instructions d’installation fournies précédemment dans cette rubrique.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problème : Vous voyez une erreur 500 et 404 dans le fichier journal juste après l’intégration
Il s’agit d’un problème connu qui se produit lors du premier chargement de données Linux dans un espace de travail Log Analytics. Cela n’affecte pas les données envoyées ni l’expérience du service.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problème : vous ne voyez pas toutes les données dans le portail Azure

### <a name="probable-causes"></a>Causes probables

- Échec de l’intégration au service Log Analytics
- Blocage de la connexion au service Log Analytics
- Les données de l’Agent OMS pour Linux sont sauvegardées

### <a name="resolutions"></a>Résolutions
1. Vérifiez si l’intégration du service Log Analytics a réussi en vous assurant que le fichier suivant existe : `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Relancer l’intégration à l’aide des instructions de la ligne de commande `omsadmin.sh`
3. Si vous utilisez un proxy, reportez-vous à la procédure de résolution de proxy fournie précédemment.
4. Dans certains cas, lorsque l’agent OMS pour Linux ne peut pas communiquer avec le service, les données de l’agent sont mises en file d’attente dans la taille de mémoire tampon saturée de 50 Mo. L’Agent OMS pour Linux doit être redémarré en exécutant la commande suivante : `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ce problème est résolu dans l’agent version 1.1.0-28 et versions ultérieures.

