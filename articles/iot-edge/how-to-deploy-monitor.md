---
title: "Déployer et surveiller des modules pour Azure IoT Edge | Microsoft Docs"
description: "Gérer les modules qui s’exécutent sur des appareils Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cc7d1e290465d9254cbd7fe9e8ba71cc740b0368
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Déployer et surveiller des modules IoT Edge à l’échelle : aperçu

Azure IoT Edge vous permet de déplacer l’analytique vers la périphérie et fournit une interface cloud afin de pouvoir gérer et surveiller vos appareils IoT Edge sans avoir à accéder physiquement à chacun d’eux. La capacité à gérer à distance des appareils est de plus en plus importante à mesure que les solutions Internet des objets (IoT) deviennent de plus en plus volumineuses et complexes. Azure IoT Edge est conçu pour prendre en charge vos objectifs professionnels, quel que soit le nombre d’appareils que vous ajoutez.

Vous pouvez gérer les appareils individuellement et y déployer des modules un à la fois. Toutefois, si vous souhaitez apporter des modifications à grande échelle à des appareils, vous pouvez créer un **déploiement IoT Edge**. Les déploiements sont des processus dynamiques qui vous permettent de déployer plusieurs modules sur plusieurs appareils à la fois, de suivre l’état et l’intégrité des modules, et d’apporter des modifications si nécessaire. 

## <a name="identify-devices-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de pouvoir créer un déploiement, vous devez être en mesure de spécifier les appareils concernés. Azure IoT Edge identifie les appareils à l’aide de **balises** dans le jumeau d’appareil. Chaque appareil peut avoir plusieurs balises, et vous pouvez les définir comme bon vous semble pour votre solution. Par exemple, si vous gérez un campus de bâtiments intelligents, vous pouvez ajouter les balises suivantes à un appareil :

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Pour plus d’informations sur les balises et les jumeaux d’appareils, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Créer un déploiement

1. Accédez à votre IoT Hub dans le [portail Azure][lnk-portal]. 
1. Sélectionnez **IoT Edge (préversion)**.
1. Sélectionnez **Ajouter le déploiement IoT Edge**.

La création d’un déploiement nécessite cinq étapes. Les sections suivantes les décrivent en détail. 

### <a name="step-1-name-and-label"></a>Étape 1 : nom et étiquette

1. Donnez à votre déploiement un nom unique. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`.
1. Ajoutez des étiquettes pour faciliter le suivi de vos déploiements. Les étiquettes sont des paires **Nom**, **Valeur** qui décrivent votre déploiement. Par exemple, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Sélectionnez **Suivant** pour passer à l’étape 2. 

### <a name="step-2-add-modules-optional"></a>Étape 2 : ajouter des modules (facultatif)

Vous pouvez ajouter deux types de modules à un déploiement. Le premier est un module basé sur un service Azure, tel que Compte de stockage ou Stream Analytics. Le deuxième est un module basé sur votre propre code. Vous pouvez ajouter plusieurs modules de chaque type à un déploiement. 

Si vous créez un déploiement sans module, il supprime tous les modules existants des appareils. 

>[!NOTE]
>Azure Machine Learning et Azure Functions ne prennent pas encore en charge le déploiement de service Azure automatisé. Utilisez un déploiement de module personnalisé pour ajouter manuellement ces services à votre déploiement. 

Pour ajouter un module à partir d’Azure Stream Analytics, effectuez les étapes suivantes :
1. Sélectionnez **Importer le module Azure Stream Analytics IoT Edge**.
1. Utilisez les menus déroulants pour sélectionner les instances de service Azure que vous souhaitez déployer.
1. Sélectionnez **Enregistrer** pour ajouter votre module au déploiement. 

Pour ajouter du code personnalisé en tant que module, ou pour ajouter manuellement un module de service Azure, effectuez les étapes suivantes :
1. Sélectionnez **Ajouter module IoT Edge**.
1. Donnez un **Nom** à votre module.
1. Dans le champ **URI de l’image**, entrez l’image de conteneur Docker pour votre module. 
1. Spécifiez les **options de création de conteneur** qui doivent être passées au conteneur. Pour plus d’informations, consultez [docker create][lnk-docker-create].
1. Utilisez le menu déroulant pour sélectionner une **Stratégie de redémarrage**. Choisissez parmi les options suivantes : 
   * **Toujours** : le module redémarre toujours s’il s’arrête pour une raison quelconque.
   * **Jamais** : le module ne redémarre jamais s’il s’arrête pour une raison quelconque.
   * **On-failed (Échec)** : le module redémarre s’il se bloque, mais pas s’il est fermé correctement. 
   * **On-unhealthy (Défectueux)** : le module redémarre s’il se bloque ou s’il retourne un état défectueux. C’est à chaque module d’implémenter la fonction d’état d’intégrité. 
1. Utilisez le menu déroulant pour sélectionner **l’État souhaité** pour le module. Choisissez parmi les options suivantes :
   * **En cours d’exécution** : il s’agit de l’option par défaut. Le module commence à s’exécuter immédiatement après son déploiement.
   * **Arrêté** : après son déploiement, le module reste inactif jusqu’à ce que vous-même ou un autre module demandiez son démarrage.
1. Sélectionnez **Activer** si vous souhaitez ajouter des balises ou des propriétés au jumeau de module. 
1. Sélectionnez **Enregistrer** pour ajouter votre module au déploiement. 

Une fois que vous avez configuré tous les modules pour un déploiement, sélectionnez **Suivant** pour passer à l’étape 3.

### <a name="step-3-specify-routes-optional"></a>Étape 3 : spécifier des itinéraires (facultatif)

Les itinéraires définissent comment les modules communiquent les uns avec les autres dans un déploiement. Spécifiez tous les itinéraires pour votre déploiement, puis sélectionnez **Suivant** pour passer à l’étape 4. 

### <a name="step-4-target-devices"></a>Étape 4 : cibler des appareils

Utilisez la propriété tags à partir de vos appareils pour cibler des appareils spécifiques qui doivent recevoir ce déploiement. 

Étant donné que plusieurs déploiements peuvent cibler le même appareil, vous devez donner à chaque déploiement un numéro de priorité. En cas de conflit, le déploiement avec la priorité la plus élevée prévaut. Si deux déploiements ont le même numéro de priorité, celui qui a été créé le plus récemment prévaut. 

1. Entrez un entier positif pour la **Priorité** du déploiement.
1. Entrez une **Condition cible** pour déterminer quels sont les appareils ciblés par ce déploiement. La condition est basée sur les balises de jumeau d’appareil et doit correspondre au format de l’expression. Par exemple : `tags.environment='test'`. 
1. Sélectionnez **Suivant** pour passer à l’étape finale.

### <a name="step-5-review-template"></a>Étape 5 : vérifier le modèle

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**.

## <a name="monitor-a-deployment"></a>Surveiller un déploiement

Pour afficher les détails d’un déploiement et surveiller les appareils qui l’exécutent, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure][lnk-portal] et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge (préversion)**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge][1]

1. Inspectez la liste des déploiements. Pour chaque déploiement, vous pouvez consulter les détails suivants :
   * **ID** : nom du déploiement.
   * **Target condition (Condition cible)** : balise utilisée pour définir les appareils ciblés.
   * **Priorité** : numéro de priorité du déploiement.
   * **IoT Edge agent status (État de l’agent IoT Edge)** : nombre d’appareils ayant reçu le déploiement, ainsi que leur état d’intégrité. 
   * **Unhealthy modules (Modules défectueux)** : nombre de modules dans le déploiement qui signalent des erreurs. 
   * **Creation time (Heure de création)** : horodatage de création du déploiement. Cet horodatage sert à départager deux déploiements ayant la même priorité. 
1. Sélectionnez le déploiement que vous souhaitez surveiller.  
1. Inspectez les détails du déploiement. Vous pouvez utiliser les onglets pour afficher des détails spécifiques sur les appareils qui ont reçu le déploiement : 
   * **Targeted (Ciblé)** : appareils Edge qui remplissent la condition cible. 
   * **Applied (Appliqué)** : appareils Edge ciblés qui ne sont pas ciblés par un autre déploiement de priorité plus élevée. Il s’agit des appareils qui reçoivent réellement le déploiement. 
   * **Reporting success (Réussite signalée)** : appareils Edge ayant signalé au service que les modules ont été déployés avec succès. 
   * **Reporting failure (Échec signalé)** : appareils Edge ayant signalé au service que le déploiement d’un ou plusieurs modules a échoué. Pour examiner l’erreur en détail, vous devez vous connecter à distance à ces appareils et consulter les fichiers journaux. 
   * **Reporting unhealthy modules (Modules défectueux signalés)** : appareils Edge ayant signalé au service que le déploiement d’un ou plusieurs modules a réussi, mais présentant maintenant des erreurs. 

## <a name="modify-a-deployment"></a>Modifier un déploiement

Quand vous modifiez un déploiement, les modifications sont répliquées immédiatement sur tous les appareils ciblés. 

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :
* Si un appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que ce déploiement a la priorité la plus élevée pour cet appareil, ce déploiement est appliqué à l’appareil. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible, il désinstalle ce déploiement et prend le déploiement suivant dans l’ordre de priorité. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible et ne remplit la condition cible d’aucun autre déploiement, aucun changement ne se produit sur l’appareil. L’appareil continue à exécuter ses modules actuels dans leur état actuel, mais il n’est plus géré dans le cadre de ce déploiement. Dès qu’il remplit la condition cible d’un autre déploiement, il désinstalle ce déploiement et prend le nouveau. 

Pour modifier un déploiement, effectuez les étapes suivantes : 

1. Connectez-vous au [portail Azure][lnk-portal] et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge (préversion)**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge][1]

1. Sélectionnez le déploiement à modifier. 
1. Mettez à jour les champs suivants : 
   * Condition cible 
   * Étiquettes 
   * Priorité 
1. Sélectionnez **Enregistrer**.
1. Suivez les étapes fournies dans [Surveiller un déploiement][anchor-monitor] pour observer le déploiement des modifications. 

## <a name="delete-a-deployment"></a>Supprimer un déploiement

Quand vous supprimez un déploiement, tous les appareils prennent leur déploiement suivant dans l’ordre de priorité. Si vos appareils ne remplissent la condition cible d’aucun autre déploiement, les modules ne sont pas supprimés lors de la suppression du déploiement. 

1. Connectez-vous au [portail Azure][lnk-portal] et accédez à votre IoT Hub. 
1. Sélectionnez **IoT Edge (préversion)**.
1. Sélectionnez **Déploiements IoT Edge**. 

   ![Afficher les déploiements IoT Edge][1]

1. Utilisez la case à cocher pour sélectionner le déploiement à supprimer. 
1. Sélectionnez **Supprimer**.
1. Un message vous informe que cette action va supprimer ce déploiement et restaurer tous les appareils à leur état précédent.  Cela signifie qu’un déploiement avec une priorité inférieure sera utilisé.  Si aucun autre déploiement n’est ciblé, aucun module n’est supprimé. Si les clients souhaitent procéder ainsi, ils doivent créer un déploiement avec zéro module et le déployer sur les mêmes appareils. Sélectionnez **Oui** si vous souhaitez continuer. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [déploiement des modules sur des appareils Edge][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
