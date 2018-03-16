---
title: "Créer par programmation des tableaux de bord Azure | Microsoft Docs"
description: "Cet article explique comment créer par programmation des tableaux de bord Azure."
services: azure-portal
documentationcenter: 
author: adamab
manager: timlt
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: adamab
ms.openlocfilehash: 0e84ff61346f2788cf475cee2783ba2056a091ad
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="programmatically-create-azure-dashboards"></a>Créer par programmation des tableaux de bord Azure

Ce document vous guide tout au long des processus de création par programmation et de publication de tableaux de bord Azure. Le tableau de bord illustré ci-dessous sert de référence dans tout le document.

![exemple de tableau de bord](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Vue d'ensemble

Les tableaux de bord partagés dans Azure sont des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) tout comme les machines virtuelles et les comptes de stockage.  Ainsi, vous pouvez les gérer par programmation via les [API REST Azure Resource Manager](/rest/api/), l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure), les [commandes Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.2.0) et de nombreuses fonctionnalités du [portail Azure](https://portal.azure.com) générées sur ces API pour faciliter la gestion des ressources.  

Chacun de ces outils et API offre des moyens de créer, répertorier, récupérer, modifier et supprimer des ressources.  Étant donné que les tableaux de bord sont des ressources, vous pouvez choisir l’API/outil que vous préférez utiliser.

Quel que soit l’outil utilisé, vous devez construire une représentation JSON de votre objet tableau de bord avant de pouvoir appeler une API de création de ressources. Cet objet contient des informations sur les parties (également appelées parties de contrôle) présentes sur le tableau de bord. Ces informations incluent les tailles, les positions, les ressources auxquelles elles sont liées et toutes les personnalisations de l’utilisateur.

La façon la plus pratique de créer ce document JSON consiste à utiliser [le portail](https://portal.azure.com/) pour ajouter et positionner vos parties de manière interactive. Vous exportez ensuite le document JSON. Enfin, vous créez un modèle à partir du résultat à des fins d’utilisation ultérieure dans des scripts, programmes et outils de déploiement.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

Pour créer un tableau de bord, utilisez la commande Nouveau tableau de bord de l’écran principal du portail.

![commande Nouveau tableau de bord](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Vous pouvez ensuite utiliser la galerie de parties de contrôle pour rechercher et ajouter des parties. Les parties s’ajoutent par glisser-déposer. Certaines parties prennent en charge le redimensionnement via une poignée, tandis que d’autres ont des tailles fixes indiquées dans leur menu contextuel.

### <a name="drag-handle"></a>Poignée de redimensionnement
![poignée de redimensionnement](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Tailles fixes via le menu contextuel
![menu contextuel des tailles](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Partager le tableau de bord

Une fois que vous avez configuré le tableau de bord selon vos besoins, les étapes suivantes consistent à le publier (à l’aide de la commande Partager), puis à utiliser l’Explorateur de ressources pour extraire le document JSON.

![commande Partager](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Quand vous cliquez sur la commande Partager, une boîte de dialogue vous invite à choisir l’abonnement et le groupe de ressources dans lesquels publier. N’oubliez pas que [vous devez avoir accès en écriture](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) à l’abonnement et au groupe de ressources choisis.

![partage et accès](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Extraire la représentation JSON du tableau de bord

La publication ne prend que quelques secondes.  Quand vous avez terminé, l’étape suivante consiste à accéder à l’[Explorateur de ressources](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) pour extraire le document JSON.

![parcourir l’Explorateur de ressources](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

À partir de l’Explorateur de ressources, accédez à l’abonnement et au groupe de ressources que vous avez choisis. Ensuite, cliquez sur la ressource de tableau de bord fraîchement publiée pour révéler le document JSON.

![JSON dans l’Explorateur de ressources](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Créer un modèle à partir du document JSON

L’étape suivante consiste à créer un modèle à partir de ce document JSON pour pouvoir le réutiliser par programmation avec les API de gestion des ressources, les outils en ligne de commande appropriés ou au sein du portail.

Il n’est pas nécessaire de comprendre entièrement la structure JSON du tableau de bord pour créer un modèle. Dans la plupart des cas, vous allez conserver la structure et la configuration de chaque partie, puis paramétrer l’ensemble des ressources Azure vers lesquelles elles pointent. Examinez votre tableau de bord JSON exporté et recherchez toutes les occurrences d’ID de ressource Azure. Notre exemple de tableau de bord comporte plusieurs parties de contrôle qui pointent toutes vers une seule machine virtuelle Azure. En effet, notre tableau de bord ne s’intéresse qu’à cette ressource unique. Si vous recherchez « /subscriptions » dans l’exemple de document JSON (inclus à la fin du document), vous trouvez plusieurs occurrences de cet ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Pour publier ce tableau de bord pour toutes les prochaines machines virtuelles, vous devez paramétrer chaque occurrence de cette chaîne dans le document JSON. 

Il existe deux types d’API qui créent des ressources dans Azure. Les [API impératives](https://docs.microsoft.com/rest/api/resources/resources) qui créent une seule ressource à la fois et un système de [déploiement basé sur un modèle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) capable d’orchestrer la création de plusieurs ressources dépendantes avec un seul appel d’API. Ce dernier prend en charge de manière native le paramétrage et la création de modèles ; c’est pour cela que nous l’utilisons pour notre exemple.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Créer par programmation un tableau de bord à partir de votre modèle à l’aide d’un déploiement de modèle

Azure offre la possibilité d’orchestrer le déploiement de plusieurs ressources. Vous créez un modèle de déploiement qui exprime l’ensemble des ressources à déployer, ainsi que les relations qui existent entre elles.  Le format JSON de chaque ressource est le même que si vous les créiez une par une. La différence est que le [langage de modèle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) ajoute quelques concepts comme les variables, les paramètres, les fonctions de base, etc. Cette syntaxe étendue est uniquement prise en charge dans le contexte d’un déploiement de modèle ; elle ne fonctionne pas si vous l’utilisez avec les API impératives mentionnées plus tôt.

Si vous faites ce choix, alors le paramétrage doit être effectué à l’aide de la syntaxe de paramètre du modèle.  Vous remplacez toutes les instances de l’ID de ressource que nous avons trouvé précédemment, comme indiqué ici.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Exemple de propriété JSON avec ID de ressource codé en dur
`id: “/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1”`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Exemple de propriété JSON convertie en version paramétrable selon des paramètres de modèle

`id: "[resourceId(parameters('virtualMachineResourceGroup'), ‘Microsoft.Compute/virtualMachines’, parameters('virtualMachineName'))]"`

Vous devez également déclarer certaines métadonnées de modèle requises et les paramètres situés en haut du modèle json comme les suivants :

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Vous pouvez voir le modèle complet à la fin de ce document.__

Une fois que vous avez créé votre modèle, vous pouvez le déployer à l’aide des [API REST](https://docs.microsoft.com/rest/api/resources/deployments), de [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), de l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/group/deployment#az_group_deployment_create) ou de la [page de déploiement de modèle du portail](https://portal.azure.com/#create/Microsoft.Template).

Voici deux versions de notre exemple de document JSON de tableau de bord. La première est la version que nous avons exportée à partir du portail et qui était déjà liée à une ressource. La seconde est la version de modèle que vous pouvez lier par programmation à toute machine virtuelle et déployer à l’aide d’Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Représentation JSON de notre exemple de tableau de bord (avant la création du modèle)

Voici ce que vous pouvez vous attendre à obtenir si vous suivez les instructions précédentes pour extraire la représentation JSON d’un tableau de bord déjà déployé. Notez les identificateurs de ressource codée en dur qui indiquent que ce tableau de bord pointe vers une machine virtuelle Azure spécifique.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Représentation sous forme de modèle de notre exemple de tableau de bord

La version du modèle du tableau de bord a défini trois paramètres appelés __virtualMachineName__, __virtualMachineResourceGroup__ et __dashboardName__.  Les paramètres vous permettent de faire pointer ce tableau de bord vers une autre machine virtuelle Azure à chaque déploiement. Les ID paramétrables sont mis en surbrillance pour indiquer que ce tableau de bord peut être configuré par programmation et déployé de sorte à pointer vers toute machine virtuelle Azure. Le moyen le plus simple de tester cette fonctionnalité consiste à copier le modèle suivant et à le coller dans la [page de déploiement de modèle du portail Azure](https://portal.azure.com/#create/Microsoft.Template). 

Cet exemple déploie un tableau de bord par lui-même, mais le langage de modèle vous permet de déployer plusieurs ressources et de regrouper un ou plusieurs tableaux de bord à leurs côtés. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```