---
title: "Résoudre les problèmes de déploiement d’OpenShift dans Azure | Microsoft Docs"
description: "Résolvez les problèmes de déploiement d’OpenShift dans Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 35e554d3a9c7e7d56546ae9723c33eb59e906472
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Résoudre les problèmes de déploiement d’OpenShift dans Azure

Si votre cluster OpenShift ne se déploie pas correctement, essayez ces tâches de dépannage pour circonscrire le problème. Affichez l’état du déploiement et comparez-le à la liste suivante de codes de sortie :

- Code de sortie 3 : vos nom d’utilisateur/mot de passe ou ID d’organisation/clé d’activation d’abonnement Red Hat sont incorrects
- Code de sortie 4 : votre ID de pool Red Hat est incorrect ou aucun droit n’est disponible
- Code de sortie 5 : impossible de provisionner le volume de pool dynamique Docker
- Code de sortie 6 : l’installation du cluster OpenShift a échoué
- Code de sortie 7 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de solution cloud Azure a échoué ; problème de fichier de configuration maître sur le nœud master
- Code de sortie 8 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de solution cloud Azure a échoué ; problème de fichier de configuration de nœud sur le nœud master
- Code de sortie 9 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de solution cloud Azure a échoué ; problème de fichier de configuration de nœud sur un nœud infrastructure ou application
- Code de sortie 10 : l’installation du cluster OpenShift a réussi, mais la configuration du fournisseur de solution cloud Azure a échoué ; correction des nœuds master ou impossibilité de définir master comme non planifiable
- Code de sortie 11 : le déploiement des métriques a échoué
- Code de sortie 12 : le déploiement de la journalisation a échoué

Pour les codes de sortie 7 à 10, le cluster OpenShift a été installé, mais la configuration du fournisseur de solution cloud Azure a échoué. Vous pouvez établir une connexion SSH au nœud master (OpenShift Origin) ou au nœud bastion (OpenShift Container Platform) et, à partir de là, établir une connexion SSH à chacun des nœuds du cluster pour résoudre les problèmes.

Une cause courante des échecs correspondant aux codes de sortie 7 à 9 est que le principal du service ne disposait pas des autorisations appropriées sur l’abonnement ou le groupe de ressources. Si tel est le problème, attribuez les autorisations appropriées, puis réexécutez manuellement le script qui a échoué et tous les scripts suivants.

Veillez à redémarrer le service qui a échoué (par exemple, systemctl restart atomic-openshift-node.service) avant de réexécuter les scripts.

Pour un dépannage supplémentaire, établissez une connexion SSH à votre nœud master sur le port 2200 (OpenShift Origine) ou nœud bastion sur le port 22 (OpenShift Container Platform). Vous devez être à la racine (sudo su-), puis accéder au répertoire suivant : /var/lib/waagent/custom-script/download.

Ce dernier contient des dossiers nommés « 0 » et « 1 ». Dans chacun de ces dossiers, vous voyez deux fichiers, « stderr » et « stdout ». Examinez ces fichiers afin de déterminer la cause de la défaillance.
