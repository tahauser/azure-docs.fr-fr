---
title: Composition du module Azure IoT Edge | Microsoft Docs
description: Découvrir de quoi les modules Azure IoT Edge sont constitués et comment ils peuvent être réutilisés
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7df566ced755e1e817b3107dac8f17e9f6e9b8e4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisées - vue d’ensemble

Chaque appareil IoT Edge exécute au moins deux modules : $edgeAgent et $edgeHub, qui constituent le runtime IoT Edge. En plus de ce standard (deux colonnes), n’importe quel appareil IoT Edge peut exécuter plusieurs modules pour effectuer n’importe quel processus. Lorsque vous déployez tous ces modules à la fois sur un appareil, vous avez besoin d’un moyen pour déclarer les modules inclus et le mode d’interaction entre eux. 

Le *manifeste de déploiement* est un document JSON qui décrit :

* les modules IoT Edge à déployer, ainsi que leurs options de création et de gestion ;
* la configuration du concentrateur Edge, qui inclut le mode de flux des messages entre les modules et finalement vers IoT Hub ;
* facultativement, les valeurs à définir dans les propriétés souhaitées des représentations de module, pour configurer les applications de module individuel.

Tous les appareils IoT Edge doivent être configurés avec un manifeste de déploiement. Un runtime IoT Edge nouvellement installé renvoie un code d’erreur tant qu’il n’est pas configuré avec un manifeste valide. 

Dans les didacticiels Azure IoT Edge, vous générez un manifeste de déploiement via un Assistant dans le portail Azure IoT Edge. Vous pouvez également appliquer un manifeste de déploiement par programmation à l’aide du Kit de développement logiciel (SDK) REST ou IoT Hub Service. Reportez-vous à [Déployer et surveiller][lnk-deploy] pour plus d’informations sur les déploiements IoT Edge.

## <a name="create-a-deployment-manifest"></a>Créer un manifeste de déploiement

À un niveau supérieur, le manifeste de déploiement configure les propriétés souhaitées d’un jumeau de module pour les modules IoT Edge déployés sur un appareil IoT Edge. Deux de ces modules sont toujours présents : l’agent Edge et Edge Hub.

Un manifeste de déploiement ne contenant que le runtime IoT Edge (agent et concentrateur) est valide.

Le manifeste suit la structure suivante :

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
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
```

## <a name="configure-modules"></a>Configurer des modules

En plus d’établir les propriétés souhaitées de tous les modules à déployer, vous devez indiquer au runtime IoT Edge comment les installer. Les informations de configuration et de gestion pour tous les modules est placé dans les propriétés souhaitées **$edgeAgent**. Ces informations incluent les paramètres de configuration pour l’agent Edge proprement dit. 

Pour obtenir une liste complète des propriétés qui peuvent ou doivent être incluses, consultez [Propriétés de l’agent Edge et du concentrateur Edge](module-edgeagent-edgehub.md).

Les propriétés $edgeAgent suivent cette structure :

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Déclarer des itinéraires

Edge Hub offre un moyen de router les messages entre les modules et entre les modules et IoT Hub de façon déclarative. Le concentrateur Edge gère toutes les communications, afin que les informations d’itinéraire soient placées dans les propriétés souhaitées **$edgeHub**. Vous pouvez avoir plusieurs itinéraires dans le même déploiement.

Les itinéraires sont déclarés dans les propriétés souhaitées **$edgeHub** avec la syntaxe suivante :

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Chaque itinéraire requiert une source et un récepteur, mais la condition est facultative et peut être utilisée pour filtrer les messages. 


### <a name="source"></a>Source
La source spécifie d'où proviennent les messages. Il peut s’agir de l’une des valeurs suivantes :

| Source | Description |
| ------ | ----------- |
| `/*` | Tous les messages appareil-à-cloud de n’importe quel appareil ou un module |
| `/messages/*` | Tout message appareil-à-cloud envoyé par un appareil ou un module via une sortie ou aucune |
| `/messages/modules/*` | Tout message appareil-à-cloud envoyé par un module via une sortie ou aucune |
| `/messages/modules/{moduleId}/*` | Tout message appareil-à-cloud envoyé par {moduleId} sans sortie |
| `/messages/modules/{moduleId}/outputs/*` | Tout message appareil-à-cloud envoyé par {moduleId} avec sortie |
| `/messages/modules/{moduleId}/outputs/{output}` | Tout message appareil-à-cloud envoyé par {moduleId} à l’aide de {output} |

### <a name="condition"></a>Condition
La condition est facultative dans une déclaration d’itinéraire. Si vous souhaitez transmettre tous les messages du récepteur à la source, il suffit d’omettre la clause **WHERE** entièrement. Ou vous pouvez utiliser le [langage de requête IoT Hub][lnk-iothub-query] pour filtrer certains messages ou types de messages qui répondent à la condition.

Les messages qui transitent entre les modules dans IoT Edge sont mis en forme de la même manière que les messages qui passent entre les appareils et Azure IoT Hub. Tous les messages sont au format JSON et ont les paramètres **systemProperties**, **appProperties** et **corps**. 

Vous pouvez générer des requêtes autour des trois paramètres avec la syntaxe suivante : 

* Propriétés du système : `$<propertyName>` ou `{$<propertyName>}`
* Propriétés de l’application : `<propertyName>`
* Propriétés du corps : `$body.<propertyName>` 

Pour obtenir des exemples sur la façon de créer des requêtes pour les propriétés de message, consultez [Expressions de requête des itinéraires des messages appareil-à-cloud](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).

Par exemple, sur IoT Edge, si vous voulez filtrer des messages reçus sur un appareil de passerelle à partir d’un appareil de nœud terminal. Les messages provenant des modules contiennent une propriété système appelée **connectionModuleId**. Par conséquent, si vous souhaitez router des messages à partir d’appareils de nœud terminal directement vers IoT Hub, utilisez l’itinéraire suivant pour exclure les messages de modules :

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Récepteur
Le récepteur définit où les messages sont envoyés. Il peut s’agir de l’une des valeurs suivantes :

| Récepteur | Description |
| ---- | ----------- |
| `$upstream` | Envoyer le message à IoT Hub |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Envoyer le message à l’entrée `{input}` du module `{moduleId}` |

Il est important de noter que le concentrateur Edge fournit au moins une garantie, ce qui signifie que les messages sont stockés localement au cas où un itinéraire ne pourrait pas remettre le message à son récepteur, par exemple, si le concentrateur Edge ne peut pas se connecter à IoT Hub ou si le module cible n’est pas connecté.

Le concentrateur Edge stocke les messages jusqu’à l’heure spécifiée dans la propriété `storeAndForwardConfiguration.timeToLiveSecs` des [propriétés souhaitées du concentrateur Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Définir ou mettre à jour les propriétés souhaitées 

Le manifeste de déploiement peut spécifier les propriétés souhaitées pour le jumeau de module de chaque module déployé sur l’appareil IoT Edge. Lorsque les propriétés souhaitées sont spécifiées dans le manifeste de déploiement, elles remplacent les propriétés souhaitées actuellement spécifiées dans la représentation de module.

Si vous ne spécifiez pas de propriétés souhaitées d’une représentation de module dans le manifeste de déploiement, IoT Hub ne modifiera d’aucune façon pas la représentation de module et vous ne pourrez pas définir les propriétés souhaitées par programmation.

Les mêmes mécanismes que ceux qui vous permettent de modifier des jumeaux d’appareils sont utilisés pour modifier des jumeaux de modules. Pour plus d’informations, consultez le [guide du développeur de jumeaux d’appareils](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins).   

## <a name="deployment-manifest-example"></a>Exemple de manifeste de déploiement

Voici un exemple de document JSON de manifeste de déploiement.

```json
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
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une liste complète des propriétés qui peuvent ou doivent être incluses dans $edgeAgent et $edgeHub, consultez [Propriétés de l’agent Edge et du concentrateur Edge](module-edgeagent-edgehub.md).

* Maintenant que vous savez comment les modules IoT Edge sont utilisés, [Comprendre les exigences et les outils de développement de modules IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
