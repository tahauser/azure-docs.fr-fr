---
title: "Décisions de déploiement pour les systèmes intégrés Azure Stack | Microsoft Docs"
description: "Déterminez les décisions relatives à la planification du déploiement pour les systèmes Azure Stack à plusieurs nœuds."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d4aaf5af4fdb9ff5d185ef14333db4e204b9a955
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Décisions relatives à la planification du déploiement pour les systèmes intégrés Azure Stack
Si vous êtes intéressé par un système intégré Azure Stack, vous devez prendre connaissance de [plusieurs éléments relatifs à la planification](azure-stack-deployment-planning.md) du déploiement Azure Stack et déterminer ensuite de quelle façon le système sera intégré à votre centre de données. En outre, vous devez définir avec précision comment vous allez intégrer Azure Stack dans votre environnement de cloud hybride. Cet article fournit une vue d’ensemble de ces décisions, y compris celles relatives à la connexion à Azure, au magasin d’identités et au modèle de facturation.

Si vous décidez d’acheter un système intégré, votre fournisseur de matériel OEM vous guide durant une grande partie du processus de planification pour plus en détails. Ils effectueront également le déploiement réel.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Choisir un modèle de connexion pour le déploiement Azure Stack
Vous pouvez choisir de déployer Azure Stack en le connectant ou non à internet (et à Azure). Pour tirer le meilleur parti de Azure Stack, y compris les scénarios hybrides entre Azure Stack et Azure, vous souhaiterez le connecter à Azure. Ce choix détermine les options disponibles pour votre magasin d’identités (Azure Active Directory ou services de fédération Active Directory (AD FS)) et le modèle de facturation (facturation à l’utilisation ou selon la capacité), tel que résumé dans le schéma et le tableau suivants : 

![Scénarios de facturation et de déploiement Azure Stack](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Ce choix est déterminant. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. Vous ne pourrez pas revenir en arrière sans avoir à redéployer l’intégralité du système.  


|Options|Déploiement connecté à Azure|Déploiement déconnecté d’Azure|
|-----|-----|-----|
|Azure AD|![Prise en charge](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Prise en charge](media/azure-stack-deployment-decisions/check.png)|![Prise en charge](media/azure-stack-deployment-decisions/check.png)|
|Facturation à l’utilisation|![Prise en charge](media/azure-stack-deployment-decisions/check.png)| |
|Facturation selon la capacité|![Prise en charge](media/azure-stack-deployment-decisions/check.png)|![Prise en charge](media/azure-stack-deployment-decisions/check.png)|
|Télécharger les packages de mise à jour directement sur Azure Stack|![Prise en charge](media/azure-stack-deployment-decisions/check.png)|  |

Une fois que vous avez choisi le modèle de connexion à Azure à utiliser pour le déploiement Azure Stack, vous devrez prendre d’autres décisions liées au modèle de connexion pour sélectionner le magasin d’identités et le mode de facturation. 

## <a name="next-steps"></a>étapes suivantes
- En savoir plus sur [les décisions de déploiement connecté à Azure pour Azure Stack](azure-stack-connected-deployment.md)
- En savoir plus sur [les décisions de déploiement déconnecté de Azure pour Azure Stack](azure-stack-disconnected-deployment.md)
