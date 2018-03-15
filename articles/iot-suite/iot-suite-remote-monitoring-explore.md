---
title: "Bien démarrer avec la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel utilise des scénarios simulés pour introduire la solution préconfigurée de surveillance à distance. Ces scénarios sont créés quand vous déployez la solution préconfigurée de surveillance à distance pour la première fois."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 96d701860abcc645b37d0420fe352da2adeb992f
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>Explorer les fonctionnalités de la solution préconfigurée de surveillance à distance

Ce didacticiel vous montre les fonctionnalités clés de la solution de surveillance à distance. À cette fin, le didacticiel présente les scénarios client courants à l’aide d’une application IoT simulée pour une société appelée Contoso.

Le didacticiel vous aide à comprendre les scénarios IoT classiques que la solution de surveillance à distance fournit prêts à l’emploi.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Visualiser et filtrer les appareils sur le tableau de bord
> * Répondre à une alarme
> * Mettre à jour le microprogramme de vos appareils
> * Organiser vos ressources
> * Arrêter et démarrer les appareils simulés

La vidéo suivante offre une présentation pas à pas de la solution de surveillance à distance :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez effectuer le didacticiel [Déployer la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-deploy.md).

## <a name="the-contoso-sample-iot-deployment"></a>Exemple de déploiement IoT Contoso

Vous pouvez utiliser l’exemple de déploiement IoT Contoso pour comprendre les scénarios de base que la solution de surveillance à distance fournit prêts à l’emploi. Ces scénarios sont basés sur des déploiements IoT réels. Vous allez très probablement choisir de personnaliser la solution de surveillance à distance en fonction de vos besoins, mais l’exemple Contoso vous permet d’apprendre les notions de base.

> [!NOTE]
> Si vous avez utilisé l’interface CLI pour déployer la solution préconfigurée, le fichier `deployment-{your deployment name}-output.json` contient des informations sur le déploiement, telles que l’URL d’accès à l’exemple déployé.

L’exemple Contoso provisionne un ensemble d’appareils simulés et de règles pour agir sur ces derniers. Une fois que vous avez compris les scénarios de base, vous pouvez continuer à explorer d’autres fonctionnalités de la solution dans [Effectuer une surveillance des appareils avancée à l’aide de la solution de surveillance à distance](iot-suite-remote-monitoring-monitor.md).

Contoso est une société qui gère de nombreuses ressources dans différents environnements. Contoso souhaite exploiter la puissance des applications IoT cloud pour surveiller et gérer à distance plusieurs ressources à partir d’une application centralisée. Les sections suivantes récapitulent la configuration initiale de l’exemple Contoso :

> [!NOTE]
> La démonstration Contoso est une manière parmi d’autres de provisionner des appareils simulés et de créer des règles. D’autres options de provisionnement incluent la création de vos propres appareils personnalisés. Pour en savoir plus sur la façon de créer vos propres règles et des appareils, consultez [Gérer et configurer vos appareils](iot-suite-remote-monitoring-manage.md) et [Détecter les problèmes à l’aide de règles de seuil](iot-suite-remote-monitoring-automate.md).

### <a name="contoso-devices"></a>Appareils Contoso

Contoso utilise différents types d’appareils connectés. Ces appareils, qui remplissent différents rôles pour l’entreprise, envoient différents flux de données de télémétrie. En outre, à chaque type d’appareil correspondent des propriétés d’appareil et des méthodes prises en charge différentes.

Le tableau suivant récapitule les types d’appareils provisionnés :

| Type d’appareil        | Télémétrie                                  | properties                                  | Balises                    | Méthodes                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller (Refroidisseur)            | Temperature, Humidity, Pressure (Température, Humidité, Pression)            | Type, Firmware version, Model (Type, Version du microprogramme, Modèle)               | Location, Floor, Campus (Emplacement, Étage, Campus) | Reboot, Firmware Update, Emergency Valve Release, Increase Pressure (Redémarrage, Mise à jour du microprogramme, Déclenchement de la soupape de sécurité, Augmentation de la pression)                          |
| Prototyping device (Appareil de prototypage) | Temperature, Pressure, Geo-location (Température, Pression, Géolocalisation)        | Type, Firmware version, Model (Type, Version du microprogramme, Modèle)               | Location, Mode (Emplacement, Mode)          | Reboot, Firmware Update, Move device, Stop device, Temperature release, Temperature increase (Redémarrage, Mise à jour du microprogramme, Déplacement de l’appareil, Arrêt de l’appareil, Relâchement de la température, Augmentation de la température) |
| Engine (Moteur)             | Tank fuel level, Coolant sensor, Vibration (Niveau du réservoir de carburant, Capteur du liquide de refroidissement, Vibration) | Type, Firmware version, Model (Type, Version du microprogramme, Modèle)               | Location, Floor, Campus (Emplacement, Étage, Campus) | Restart, Firmware Update, Empty tank, Fill tank (Redémarrage, Mise à jour du microprogramme, Vidage du réservoir, Remplissage du réservoir)                                              |
| Truck (Camion)              | Geo-location, Speed, Cargo temperature (Géolocalisation, Vitesse, Température de la cargaison)     | Type, Firmware version, Model (Type, Version du microprogramme, Modèle)               | Location, Load (Emplacement, Chargement)          | Lower cargo temperature, Increase cargo temperature, Firmware update (Réduction de la température de la cargaison, Augmentation de la température de la cargaison, Mise à jour du microprogramme)                         |
| Elevator (Élévateur)           | Floor, Vibration, Temperature (Étage, Vibration, Température)              | Type, Firmware version, Model, Geo-location (Type, Version du microprogramme, Modèle, Géolocalisation) | Location, Campus (Emplacement, Campus)        | Stop elevator, Start elevator, Firmware update (Arrêt de l’élévateur, Démarrage de l’élévateur, Mise à jour du microprogramme)                                               |

> [!NOTE]
> L’exemple de démonstration Contoso provisionne deux appareils par type. Pour chaque type, l’un fonctionne correctement dans les limites définies comme normales par Contoso, tandis que l’autre connaît des dysfonctionnements. Dans la section suivante, vous allez étudier les règles que Contoso définit pour les appareils. Ces règles définissent les limites du comportement correct.

### <a name="contoso-rules"></a>Règles Contoso

Les opérateurs chez Contoso connaissent les seuils qui déterminent si un appareil fonctionne correctement. Par exemple, un refroidisseur ne fonctionne pas correctement si la pression qu’il indique est supérieure à 250 PSI. Le tableau suivant présente les règles de seuil que Contoso définit pour chaque type d’appareil :

| Nom de la règle | Description | Seuil | Niveau de gravité | Appareils concernés |
| --------- | ----------- | --------- | -------- | ---------------- |
| Pression de refroidissement trop élevée | Émet une alerte si les refroidisseurs atteignent des niveaux de pression supérieurs à la normale   |P>250 psi       | Critique | Refroidisseurs            |
| Température des appareils de prototypage trop élevée  | Émet une alerte si les appareils de prototypage atteignent des niveaux de température supérieurs à la normale  |T > 80&deg; F |Critique | Appareils de prototypage |
| Réservoir de moteur vide  | Émet une alerte si le réservoir est vide                     | F < 5 gallons | info     | Moteurs             |
| Température de la cargaison supérieure à la normale | Émet une alerte si la température de la cargaison du camion est supérieure à la normale                 | T < 45&deg; F |Avertissement  | Camions              |
| Vibration d’élévateur arrêtée      | Émet une alerte si l’élévateur s’arrête complètement (selon le niveau de vibration)                     | V < 0,1 mm |Avertissement  | Élévateurs           |

### <a name="operate-the-contoso-sample-deployment"></a>Exploiter l’exemple de déploiement Contoso

Vous venez de voir la configuration initiale dans l’exemple Contoso. Les sections suivantes décrivent les trois scénarios dans l’exemple de Contoso qui illustrent la façon dont un opérateur peut utiliser la solution préconfigurée.

## <a name="respond-to-a-pressure-alarm"></a>Répondre à une alarme de pression

Ce scénario montre comment identifier une alarme déclenchée par un appareil de refroidissement et réagir à celle-ci. Le refroidisseur se trouve à Redmond, dans l’édifice 43, à l’étage 2.

En tant qu’opérateur, vous voyez dans le tableau de bord une alarme liée à la pression d’un refroidisseur. Vous pouvez effectuer un panoramique et un zoom avant sur la carte pour afficher plus de détails.

1. Dans la page **Tableau de bord**, dans la grille **System Alarms** (Alarmes système), vous pouvez voir l’alarme **Chiller pressure too high** (Pression de refroidissement trop élevée). Le refroidisseur est également mis en surbrillance sur la carte :

    ![Tableau de bord affichant l’alarme de pression et l’appareil sur la carte](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. Pour afficher les détails de l’appareil et les données de télémétrie, cliquez sur le refroidisseur en surbrillance sur la carte. Les données de télémétrie montrent un pic de pression :

    ![Choisir un appareil sur la carte pour afficher les détails correspondants](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. Fermer **Détails de l’appareil**.

1. Pour accéder à la page **Maintenance**, choisissez **Maintenance** dans le menu de navigation.

Dans la page **Maintenance**, vous pouvez afficher les détails de la règle qui a déclenché l’alarme de pression de refroidissement.

1. La liste des notifications affiche le nombre de fois où l’alarme s’est déclenchée, d’accusés de réception et d’alarmes ouvertes et fermées :

    ![Page Maintenance affichant la liste des alarmes qui ont été déclenchées](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. La première alarme dans la liste est la plus récente. Cliquez sur l’alarme **Chiller Pressure Too High** (Pression de refroidissement trop haute) pour afficher les appareils et les données de télémétrie associés. Les données de télémétrie montrent des pics de pression pour le refroidisseur :

    ![Page Maintenance affichant les données de télémétrie de l’alarme sélectionnée](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

Vous avez désormais identifié le problème qui a déclenché l’alarme, ainsi que l’appareil associé. En tant qu’opérateur, vous devez ensuite accuser réception de l’alarme et résoudre le problème.

1. Pour indiquer que vous travaillez maintenant sur l’alarme, définissez **Alarm status** (État de l’alarme) sur **Acknowledged** (Réception confirmée) :

    ![Sélectionner l’alarme et accuser réception de celle-ci](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. Pour agir sur le refroidisseur, sélectionnez-le, puis choisissez **Planifier**. Sélectionnez **EmergencyValveRelease**, ajoutez un nom de tâche **ChillerPressureRelease**, puis choisissez **Appliquer**. Ces paramètres créent un travail qui s’exécute immédiatement :

    ![Sélectionner l’appareil et planifier une action](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. Pour afficher l’état du travail, revenez à la page **Maintenance** et affichez la liste des travaux dans la vue **Tâches**. Vous pouvez voir que le travail a été exécuté pour libérer la pression de la soupape sur le refroidisseur :

    ![État des tâches dans la vue Tâches](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

Enfin, vérifiez que les valeurs de télémétrie issues du refroidisseur sont revenues à la normale.

1. Pour afficher la grille des alarmes, accédez à la page **Tableau de bord**.

1. Pour afficher les données de télémétrie d’appareil, sélectionnez l’appareil auquel est associée l’alarme d’origine sur la carte et vérifiez qu’il refonctionne normalement.

1. Pour fermer l’incident, accédez à la page **Maintenance**, sélectionnez l’alarme et définissez l’état sur **Fermé** :

    ![Sélectionner et fermer l’alarme](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>Mettre à jour le microprogramme de l’appareil

Contoso fait le test d’un nouveau type d’appareil sur le terrain. Dans le cadre du cycle de test, vous devez vérifier que les mises à jour du microprogramme de l’appareil fonctionnent correctement. Les étapes suivantes vous montrent comment utiliser la solution de surveillance à distance pour mettre à jour le microprogramme sur plusieurs appareils.

Pour effectuer les tâches de gestion d’appareil nécessaires, utilisez la page **Appareils**. Commencez par filtrer tous les appareils de prototypage :

1. Accédez à la page **Appareils**. Choisissez le filtre **Prototyping devices** (Appareils de prototypage) dans la liste déroulante **Filtres** :

    ![Utiliser un filtre sur la page des appareils](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > Cliquez sur **Gérer** pour gérer les filtres disponibles.

1. Sélectionnez un des appareils de prototypage :

    ![Sélectionner un appareil dans la page des appareils](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. Cliquez sur le bouton **Planifier**, puis choisissez **Firmware update** (Mise à jour de microprogramme). Renseignez les champs **Job name** (Nom de la tâche) et **Firmware Version** (Version du microprogramme) et **Firmware URI** (URI du microprogramme). Choisissez **Apply** (Appliquer) pour que le travail s’exécute maintenant :

    ![Planifier la mise à jour du microprogramme sur l’appareil](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > Avec les appareils simulés, vous pouvez utiliser l’URL de votre choix dans le champ **Firmware URI** (URI du microprogramme) et une valeur quelconque pour **Firmware Version** (Version du microprogramme). Les appareils simulés n’accèdent pas à l’URL.

1. Notez le nombre d’appareils concernés par le travail, puis choisissez **Appliquer** :

    ![Soumettre le travail de mise à jour du microprogramme](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

Vous pouvez utiliser la page **Maintenance** pour suivre l’exécution du travail.

1. Pour afficher la liste des travaux, accédez à la page **Maintenance**, puis cliquez sur **Tâches**.

1. Recherchez l’événement associé au travail que vous avez créé. Vérifiez que le processus de mise à jour du microprogramme a été correctement lancé.

Vous pouvez créer un filtre pour vérifier correctement la mise à jour de la version du microprogramme.

1. Pour créer un filtre, accédez à la page **Appareil** et sélectionnez **Gérer les filtres** :

    ![Gérer les filtres d’appareils](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Créez un filtre qui inclut uniquement les appareils dotés de la nouvelle version du microprogramme :

    ![Créer le filtre d’appareils](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. Retournez à la page **Appareils** et vérifiez que l’appareil dispose de la nouvelle version du microprogramme.

## <a name="organize-your-assets"></a>Organiser vos ressources

Contoso possède deux équipes différentes pour les activités de service sur le terrain :

* L’équipe Smart Building gère les refroidisseurs, les élévateurs et les moteurs.
* L’équipe Smart Vehicle gère camions et les appareils de prototypage.

En tant qu’opérateur, pour que vous puissiez plus facilement organiser et gérer vos appareils, vous souhaitez étiqueter ces derniers avec le nom d’équipe approprié.

Vous pouvez créer des noms d’étiquette à utiliser avec les appareils.

1. Pour afficher tous les appareils, accédez à la page **Appareils** et choisissez le filtre **Tous les appareils** :

    ![Afficher tous les appareils](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. Sélectionnez les appareils **Trucks** (Camions) et **Prototyping** (Prototypage). Sélectionnez ensuite **Étiquette** :

    ![Sélectionner les appareils prototypage et camion](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. Choisissez **Étiquette**, puis créez une étiquette appelée **FieldService** avec comme valeur **ConnectedVehicle**. Choisissez un nom pour le travail. Cliquez alors sur **Appliquer** :

    ![Ajouter une étiquette aux appareils prototypage et camion](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. Sélectionnez les appareils **Chiller** (Refroidisseur), **Elevator** (Élévateur), et **Engine** (Moteur). Sélectionnez ensuite **Étiquette** :

    ![Sélectionner les appareils refroidisseur, moteur et élévateur](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. Choisissez **Étiquette**, puis créez une étiquette appelée **FieldService** avec comme valeur **SmartBuilding**. Choisissez un nom pour le travail. Cliquez ensuite sur **Enregistrer** :

    ![Ajouter une étiquette aux appareils refroidisseur, moteur et élévateur](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

Vous pouvez utiliser les valeurs des étiquettes pour créer des filtres.

1. Dans la page **Appareils**, choisissez **Gérer les filtres** :

    ![Gérer les filtres d’appareils](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. Créez un filtre qui utilise le nom d’étiquette **FieldService** et la valeur **SmartBuilding**. Enregistrez le filtre sous le nom **Smart Building**.

1. Créez un filtre qui utilise le nom d’étiquette **FieldService** et la valeur **ConnectedVehicle**. Enregistrer le filtre sous le nom **Connected Vehicle**.

L’opérateur Contoso peut à présent interroger les appareils en fonction de l’équipe d’exploitation sans avoir besoin d’apporter de modifications aux appareils.

## <a name="stop-simulated-devices"></a>Arrêter les appareils simulés

Vous pouvez utiliser le menu Paramètres pour arrêter les appareils simulés. Cela aide à réduire les coûts de test et d’exploration de la solution. Pour démarrer ou arrêter les appareils simulés :

1. Choisissez l’icône **Paramètres**.

1. Activez ou désactivez le bouton bascule **En cours d’exécution** :

    ![Menu Paramètres](media/iot-suite-remote-monitoring-explore/settings.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

>[!div class="checklist"]
> * Visualiser et filtrer les appareils sur le tableau de bord
> * Répondre à une alarme
> * Mettre à jour le microprogramme de vos appareils
> * Organiser vos ressources
> * Arrêter et démarrer les appareils simulés

La solution de surveillance à distance n’ayant plus de secrets pour vous, nous vous suggérons d’en découvrir les fonctionnalités avancées :

* [Surveillez vos appareils](./iot-suite-remote-monitoring-monitor.md).
* [Gérez vos appareils](./iot-suite-remote-monitoring-manage.md).
* [Automatisez votre solution avec des règles](./iot-suite-remote-monitoring-automate.md).
* [Gérez votre solution](./iot-suite-remote-monitoring-maintain.md).
