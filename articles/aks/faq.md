---
title: Forum aux questions sur Azure Container Service
description: "Apporte des réponses à certaines des questions les plus fréquemment posées sur Azure Container Service."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 73c49510512c9148f4fee98423b14770fa8602b9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Forum aux questions sur Azure Container Service (AKS)

Cet article aborde les questions fréquemment posées sur Azure Container Service (AKS).

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>Quelles régions Azure bénéficieront d’Azure Container Service (AKS) ? 

- Centre du Canada 
- Est du Canada 
- Centre des États-Unis 
- Est des États-Unis 
- Asie du Sud-Est 
- Europe de l'Ouest 
- Ouest des États-Unis 2 

## <a name="when-will-additional-regions-be-added"></a>Quand d’autres régions seront-elles ajoutées ? 

D’autres régions sont ajoutées au fur et à mesure de l’augmentation de la demande.

## <a name="are-security-updates-applied-to-aks-nodes"></a>Les mises à jour de sécurité s’appliquent-elles aux nœuds AKS ? 

Les mises à jour de sécurité du système d’exploitation s’appliquent aux nœuds du cluster la nuit, mais aucun redémarrage n’est effectué. Si nécessaire, il est possible de redémarrer les nœuds sur le portail ou avec l’interface Azure CLI. Lors de la mise à niveau d’un cluster, la dernière image Ubuntu est utilisée et toutes les mises à jour de sécurité sont appliquées (avec redémarrage).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>Recommandez-vous aux clients d’utiliser ACS ou AKS ? 

La version finale d’Azure Container Service (AKS) sera disponible plus tard. Par conséquent, nous vous recommandons de créer des clusters POC, de développement et de test dans AKS, mais des clusters de production dans ACS-Kubernetes.  

## <a name="when-will-acs-be-deprecated"></a>Quand le service ACS sera-t-il déconseillé ? 

ACS sera déconseillé à peu près au moment de la publication de la version finale d’AKS. Vous aurez 12 mois après cette date pour migrer vos clusters sur AKS. Pendant cette période, vous pourrez exécuter toutes les opérations ACS.

## <a name="does-aks-support-node-autoscaling"></a>AKS prend-il en charge la mise à l’échelle automatique des nœuds ? 

La mise à l’échelle automatique des nœuds n’est pas prise en charge, mais elle est intégrée à la feuille de route. Nous vous proposons d’aller voir du côté de cette [implémentation de la mise à l’échelle automatique][auto-scaler].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Pourquoi deux groupes de ressources sont-ils créés avec AKS ? 

Chaque cluster Azure Container Service est contenu dans deux groupes de ressources. Le premier, créé par vous, contient uniquement la ressource Azure Container Service. Le deuxième, créé automatiquement lors du déploiement, contient toutes les ressources d’infrastructure du cluster, telles que les ordinateurs virtuels et les ressources de mise en réseau et de stockage. Ce groupe de ressources est créé pour faciliter le nettoyage des ressources. 

Le groupe de ressources créé automatiquement a un nom similaire au suivant :

```
MC_myResourceGRoup_myAKSCluster_eastus
```

Lorsque vous ajoutez des ressources Azure à utiliser avec le cluster Kubernetes, telles que des comptes de stockage ou des adresses IP publiques réservées, ces ressources doivent être créées dans le groupe de ressources créé automatiquement.   

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault est-il intégré à AKS ? 

Non, mais cette intégration est planifiée. En attendant, vous pouvez essayer la solution [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  