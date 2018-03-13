---
title: "Paramètres de Registre de Cloud App Discovery pour les services de proxy | Microsoft Docs"
description: "L’objectif de cette rubrique est de présenter les étapes que vous devez effectuer pour définir le port nécessaire sur les ordinateurs exécutant l’agent Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0e227d6e15789b29b40197a9ff71b2116312da78
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Paramètres de Registre de Cloud App Discovery pour les services de proxy
L’objectif de cette rubrique est de vous expliquer comment définir le port nécessaire sur les ordinateurs exécutant l’agent Cloud App Discovery. Par défaut, l’agent Cloud App Discovery est configuré pour utiliser uniquement les ports 80 ou 443. Si vous envisagez d’installer Cloud App Discovery dans un environnement avec un serveur proxy qui utilise un port personnalisé (ni 443, ni 80), vous devez configurer vos agents pour utiliser ce port. La configuration est basée sur une clé de Registre.

> [!TIP] 
> Découvrez les améliorations apportées au nouveau service Cloud App Discovery sans agent dans Azure Active Directory (Azure AD), qui sont rendues possibles par [l’intégration à Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Modifier le port utilisé par l’ordinateur exécutant l’agent Cloud App Discovery

1. Démarrez l’Éditeur du Registre.
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Accédez à la clé de Registre **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** ou créez-la.
3. Créez une valeur de **chaînes multiples** nommée **Ports**. 
  ![Nouveau](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Pour ouvrir la boîte de dialogue **Modifier les chaînes multiples**, double-cliquez sur la valeur **Ports**.
5. Dans **Données de la valeur**, entrez les valeurs suivantes et ajoutez tous les ports personnalisés qui sont utilisés par votre organisation : <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Modifier les chaînes multiples](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier les chaînes multiples**.

## <a name="next-steps"></a>Étapes suivantes

* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md) 

