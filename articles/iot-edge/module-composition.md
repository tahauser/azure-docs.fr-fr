---
title: Composition du module Azure IoT Edge | Microsoft Docs
description: "Découvrir de quoi les modules Azure IoT Edge sont constitués et comment ils peuvent être réutilisés"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisées - vue d’ensemble

Azure IoT Edge vous permet de composer plusieurs modules IoT Edge avant de les déployer sur des appareils IoT Edge. Cet article explique les principaux concepts relatifs à la composition de plusieurs modules IoT Edge avant le déploiement. 

## <a name="the-deployment-manifest"></a>Le manifeste de déploiement
Le *manifeste de déploiement* est un document JSON qui décrit :

* les modules IoT Edge à déployer, ainsi que leurs options de création et de gestion ;
* la configuration de Edge Hub, décrivant comment les messages doivent transiter entre les modules, et entre les modules et IoT Hub ;
* facultativement, les valeurs à définir dans les propriétés souhaitées des représentations de module, pour configurer les applications de module individuel.

Dans les didacticiels Azure IoT Edge, vous générez un manifeste de déploiement via un Assistant dans le portail Azure IoT Edge. Vous pouvez également appliquer un manifeste de déploiement par programmation à l’aide du Kit de développement logiciel (SDK) REST ou IoT Hub Service. Reportez-vous à [Déployer et surveiller][lnk-deploy] pour plus d’informations sur les déploiements IoT Edge.

À un niveau supérieur, le manifeste de déploiement configure les propriétés souhaitées d’un jumeau de module pour les modules IoT Edge déployés sur un appareil IoT Edge. Deux de ces modules sont toujours présents : l’agent Edge et Edge Hub.

Le manifeste suit la structure suivante :

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

Un exemple de manifeste de déploiement est fourni à la fin de cette section.

> [!IMPORTANT]
> Tous les appareils IoT Edge doivent être configurés avec un manifeste de déploiement. Un runtime IoT Edge nouvellement installé renvoie un code d’erreur tant qu’il n’est pas configuré avec un manifeste valide. 

### <a name="specify-the-modules"></a>Spécifier les modules
Les propriétés souhaitées de la représentation de module de l’agent Edge contiennent : les modules souhaités, leurs options de configuration et de gestion, ainsi que les paramètres de configuration de l’agent Edge.

À un niveau supérieur, cette section du manifeste contient les références vers les images de module et les options de gestion des modules runtime IoT Edge (agent Edge et Edge Hub), ainsi que les modules spécifiés par l’utilisateur.

Reportez-vous aux [propriétés souhaitées de l’agent Edge][lnk-edgeagent-desired] pour une description détaillée de cette section du manifeste.

> [!NOTE]
> Un manifeste de déploiement ne contenant que le runtime IoT Edge (agent et Hub) est valide.


### <a name="specify-the-routes"></a>Spécifier les itinéraires
Edge Hub offre un moyen de router les messages entre les modules et entre les modules et IoT Hub de façon déclarative.

Les itinéraires utilisent la syntaxe suivante :

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

La *source* peut être l’un des éléments suivants :

| Source | Description |
| ------ | ----------- |
| `/*` | Tous les messages appareil-à-cloud de n’importe quel appareil ou un module |
| `/messages/*` | Tout message appareil-à-cloud envoyé par un appareil ou un module via une sortie ou aucune |
| `/messages/modules/*` | Tout message appareil-à-cloud envoyé par un module via une sortie ou aucune |
| `/messages/modules/{moduleId}/*` | Tout message appareil-à-cloud envoyé par {moduleId} sans sortie |
| `/messages/modules/{moduleId}/outputs/*` | Tout message appareil-à-cloud envoyé par {moduleId} avec sortie |
| `/messages/modules/{moduleId}/outputs/{output}` | Tout message appareil-à-cloud envoyé par {moduleId} à l’aide de {output} |

La condition peut être n’importe quelle condition prise en charge par le [langage de requête IoT Hub] [ lnk-iothub-query] pour les règles de routage IoT Hub.

Le récepteur peut être l’un des suivants :

| Récepteur | Description |
| ---- | ----------- |
| `$upstream` | Envoyer le message à IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Envoyer le message à l’entrée `{input}` du module `{moduleId}` |

Notez que Edge Hub fournit des garanties Au moins une fois, ce qui signifie que les messages sont stockés localement au où un itinéraire ne pourrait pas remettre le message à son récepteur, par exemple, Edge Hub ne peut pas se connecter à IoT Hub ou le module cible n’est pas connecté.

Edge Hub stocke les messages jusqu’à l’heure spécifiée dans la propriété `storeAndForwardConfiguration.timeToLiveSecs` des propriétés souhaitées de Edge Hub.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Spécification des propriétés souhaitées de la représentation de module

Le manifeste de déploiement peut spécifier les propriétés souhaitées de la représentation de module de chacun des modules utilisateur spécifiés dans la section de l’agent Edge.

Lorsque les propriétés souhaitées sont spécifiées dans le manifeste de déploiement, elles remplacent les propriétés souhaitées actuellement spécifiées dans la représentation de module.

Si vous ne spécifiez pas de propriétés souhaitées d’une représentation de module dans le manifeste de déploiement, IoT Hub ne modifiera d’aucune façon pas la représentation de module et vous ne pourrez pas définir les propriétés souhaitées par programmation.

Les mêmes mécanismes que ceux qui vous permettent de modifier des jumeaux d’appareils sont utilisés pour modifier des jumeaux de modules. Pour plus d’informations, consultez le [guide du développeur de jumeaux d’appareils](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins).   

### <a name="deployment-manifest-example"></a>Exemple de manifeste de déploiement

Voici un exemple de document JSON de manifeste de déploiement.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>Référence : Représentation de module d’agent Edge

La représentation de module de l’agent Edge est appelée `$edgeAgent` et coordonne les communications entre l’agent Edge exécuté sur un appareil et IoT Hub.
Les propriétés souhaitées sont définies lors de l’application d’un manifeste de déploiement sur un appareil spécifique dans le cadre d’un déploiement d’appareil unique ou à grande échelle. Consultez [Déploiement et surveillance][lnk-deploy] pour plus d’informations sur le déploiement de modules sur des appareils IoT Edge.

### <a name="edge-agent-twin-desired-properties"></a>Propriétés souhaitées pour la représentation d’agent Edge

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| schemaVersion | Doit être "1.0" | OUI |
| runtime.type | Doit être "docker" | OUI |
| runtime.settings.minDockerVersion | Définie sur la version Docker minimale requise par ce manifeste de déploiement | OUI |
| runtime.settings.loggingOptions | Champ de chaîne JSON contenant les options de journalisation du conteneur d’agent Edge. [Options de journalisation Docker][lnk-docker-logging-options] | Non  |
| systemModules.edgeAgent.type | Doit être "docker" | OUI |
| systemModules.edgeAgent.settings.image | URI de l’image de l’agent Edge. Actuellement, l’agent Edge ne peut pas se mettre à jour lui-même. | OUI |
| systemModules.edgeAgent.settings.createOptions | Champ de chaîne JSON contenant les options de création du conteneur d’agent Edge. [Options de création Docker][lnk-docker-create-options] | Non  |
| systemModules.edgeAgent.configuration.id | ID du déploiement ayant déployé ce module. | Il est défini par IoT Hub lorsque ce manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |
| systemModules.edgeHub.type | Doit être "docker" | OUI |
| systemModules.edgeHub.type | Doit être "running" | OUI |
| systemModules.edgeHub.restartPolicy | Doit être "always" | OUI |
| systemModules.edgeHub.settings.image | URI de l’image de Edge Hub. | OUI |
| systemModules.edgeHub.settings.createOptions | Champ de chaîne JSON contenant les options de création du conteneur Edge Hub. [Options de création Docker][lnk-docker-create-options] | Non  |
| systemModules.edgeHub.configuration.id | ID du déploiement ayant déployé ce module. | Il est défini par IoT Hub lorsque ce manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |
| modules.{moduleId}.version | Chaîne définie par l’utilisateur représentant la version de ce module. | OUI |
| modules.{moduleId}.type | Doit être "docker" | OUI |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | OUI |
| modules.{moduleId}.settings.image | URI de l’image du module. | OUI |
| modules.{moduleId}.settings.createOptions | Champ de chaîne JSON contenant les options de création du conteneur de module. [Options de création Docker][lnk-docker-create-options] | Non  |
| modules.{moduleId}.configuration.id | ID du déploiement ayant déployé ce module. | Il est défini par IoT Hub lorsque ce manifeste est appliqué à l’aide d’un déploiement. Ne fait pas partie d’un manifeste de déploiement. |

### <a name="edge-agent-twin-reported-properties"></a>Propriétés signalées pour la représentation d’agent Edge

Les propriétés signalées pour l’agent Edge incluent trois informations principales :

1. l’état de l’application de dernières propriétés souhaitées affichées ;
2. l’état des modules en cours d’exécution sur l’appareil, comme signalé par l’agent Edge ; et
3. une copie des propriétés souhaitées en cours d’exécution sur l’appareil.

Cette dernière information est utile au cas où les dernières propriétés souhaitées ne seraient pas appliquées avec succès par le runtime, et que l’appareil exécuterait encore un manifeste de déploiement précédent.

> [!NOTE]
> Les propriétés signalées de l’agent Edge sont utiles car elles peuvent être interrogées avec le [langage de requête IoT Hub][lnk-iothub-query] pour connaître l’état de déploiements à grande échelle. Reportez-vous à [Déploiements][lnk-deploy] pour plus d’informations sur l’utilisation de cette fonctionnalité.

Le tableau suivant n’inclut pas les informations copiées à partir des propriétés souhaitées.

| Propriété | Description |
| -------- | ----------- |
| lastDesiredVersion | Cet int fait référence à la dernière version des propriétés souhaitées traitées par l’agent Edge. |
| lastDesiredStatus.code | Il s’agit du code d’état faisant référence à dernières propriétés souhaitées observées par l’agent Edge. Valeurs autorisées : `200` Réussite, `400` Configuration non valide, `412` Version de schéma non valide, `417` les propriétés souhaitées sont vides, `500` Échec |
| lastDesiredStatus.description | Texte de description de l’état |
| deviceHealth | `healthy` si l’état du runtime de tous les modules est `running` ou `stopped`, sinon, `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` si l’état du runtime de tous les modules définis par le déploiement {deploymentId} est `running` ou `stopped`, sinon, `unhealthy` |
| runtime.platform.OS | Signalement du système d’exploitation exécuté sur l’appareil |
| runtime.platform.architecture | Signalement de l’architecture de l’UC sur l’appareil |
| systemModules.edgeAgent.runtimeStatus | État signalé de l’agent Edge : {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Texte de description de l’état signalé de l’agent Edge. |
| systemModules.edgeHub.runtimeStatus | État actuel du hub Edge : { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Texte de description de l’état actuel de Edge Hub en cas de défaillance. |
| systemModules.edgeHub.exitCode | En cas de sortie, le code de sortie signalé par le conteneur Edge Hub |
| systemModules.edgeHub.startTimeUtc | Heure du dernier démarrage de Edge Hub |
| systemModules.edgeHub.lastExitTimeUtc | Heure de la dernière sortie de Edge Hub |
| systemModules.edgeHub.lastRestartTimeUtc | Heure du dernier redémarrage de Edge Hub |
| systemModules.edgeHub.restartCount | Nombre de redémarrages de ce module dans le cadre de la stratégie de redémarrage. |
| modules.{moduleId}.runtimeStatus | État actuel du module : { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Texte de description de l’état actuel du module en cas de défaillance. |
| modules.{moduleId}.exitCode | En cas de sortie, le code de sortie signalé par le conteneur de module |
| modules.{moduleId}.startTimeUtc | Heure du dernier démarrage du module |
| modules.{moduleId}.lastExitTimeUtc | Heure de la dernière sortie du module |
| modules.{moduleId}.lastRestartTimeUtc | Heure du dernier redémarrage du module |
| modules.{moduleId}.restartCount | Nombre de redémarrages de ce module dans le cadre de la stratégie de redémarrage. |

## <a name="reference-edge-hub-module-twin"></a>Référence : Représentation de module de Edge Hub

La représentation de module de Edge Hub est appelée `$edgeHub` et coordonne les communications entre Edge Hub exécuté sur un appareil et IoT Hub.
Les propriétés souhaitées sont définies lors de l’application d’un manifeste de déploiement sur un appareil spécifique dans le cadre d’un déploiement d’appareil unique ou à grande échelle. Consultez [Déploiements][lnk-deploy] pour plus d’informations sur le déploiement de modules sur des appareils IoT Edge.

### <a name="edge-hub-twin-desired-properties"></a>Propriétés souhaitées pour la représentation de Edge Hub

| Propriété | Description | Requise dans le manifeste de déploiement |
| -------- | ----------- | -------- |
| schemaVersion | Doit être "1.0" | OUI |
| routes.{routeName} | Chaîne représentant un itinéraire Edge Hub. | L’élément `routes` peut être présent mais vide. |
| storeAndForwardConfiguration.timeToLiveSecs | Durée en secondes pendant laquelle Edge Hub conserve les messages en cas de points de terminaison de routage déconnectés, par exemple, déconnectés d’IoT Hub ou d’un module local | OUI |

### <a name="edge-hub-twin-reported-properties"></a>Propriétés signalées pour la représentation de Edge Hub

| Propriété | Description |
| -------- | ----------- |
| lastDesiredVersion | Cet int fait référence à la dernière version des propriétés souhaitées traitées par Edge Hub. |
| lastDesiredStatus.code | Il s’agit du code d’état faisant référence à dernières propriétés souhaitées observées par Edge Hub. Valeurs autorisées : `200` Réussite, `400` Configuration non valide, `500` Échec |
| lastDesiredStatus.description | Texte de description de l’état |
| clients.{device or module identity}.status | État de connectivité de cet appareil ou module. Valeurs possibles {"connected" \| "disconnected"}. Seules les identités de module peuvent être à l’état déconnecté. Les appareils en aval se connectant à Edge Hub ne s’affichent que lorsqu’ils sont connectés. |
| clients.{device or module identity}.lastConnectTime | Dernière connexion de l’appareil ou du module |
| clients.{device or module identity}.lastDisconnectTime | Dernière déconnexion de l’appareil ou du module |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment les modules IoT Edge sont utilisés, [Comprendre les exigences et les outils de développement de modules IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
