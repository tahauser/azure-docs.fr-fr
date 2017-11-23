---
title: "Configurer les modes de mise en réseau pour les services de conteneur Azure Service Fabric | Microsoft Docs"
description: "Découvrez comment configurer les différents modes de mise en réseau pris en charge par Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 855e315f66858210875039f91f7f05055ff7d9b9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Modes de mise en réseau du conteneur Service Fabric

Le mode de mise en réseau par défaut proposé dans le cluster Service Fabric pour les services de conteneur est `nat`. Avec le mode de mise en réseau `nat`, plusieurs services de conteneurs sont à l’écoute des mêmes résultats de port dans les erreurs de déploiement. Pour exécuter plusieurs services à l’écoute sur le même port, Service Fabric prend en charge le mode de mise en réseau `Open` (version 5.7 ou ultérieure). Avec le mode de mise en réseau `Open`, chaque service de conteneur obtient une adresse IP affectée dynamiquement, permettant à plusieurs services d’être à l’écoute sur le même port.   

Par conséquent, avec un seul type de service disposant d’un point de terminaison statique défini dans le manifeste de service, de nouveaux services peuvent être créés et supprimés sans erreurs de déploiement à l’aide du mode de mise en réseau `Open`. De même, il est possible d’utiliser le fichier `docker-compose.yml` avec les mappages de ports statiques pour la création de plusieurs services.

Il n’est pas recommandé d’utiliser l’adresse IP assignée dynamiquement pour découvrir des services, car l’adresses IP change lorsque le service redémarre ou se déplace vers un autre nœud. Utilisez uniquement le **Service Fabric Naming Service** ou le **Service DNS** pour la découverte de service. 


> [!WARNING]
> Au total, seules 4 096 adresses IP sont autorisées par réseau virtuel dans Azure. Par conséquent, la somme du nombre de nœuds et du nombre d’instances de service de conteneur (avec la mise en réseau `Open`) ne peut pas dépasser 4 096 dans un réseau virtuel. Pour de tels scénarios à haute densité, le mode de mise en réseau `nat` est recommandé.
>

## <a name="setting-up-open-networking-mode"></a>Configuration du mode de mise en réseau ouvert

1. Définir le modèle Azure Resource Manager en activant le service DNS et le fournisseur IP sous `fabricSettings`. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. Configurer la section de profil réseau pour autoriser la configuration de plusieurs adresses IP sur chaque nœud du cluster. L’exemple suivant définit cinq adresses IP par nœud (vous pouvez ainsi avoir cinq instances de service à l’écoute sur le port de chaque nœud) pour un cluster Service Fabric Windows/Linux.

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. Pour les clusters Windows uniquement, configurez une règle de groupe de sécurité réseau ouvrant le port UDP/53 pour le réseau virtuel avec les valeurs suivantes :

   | Priorité |    Nom    |    Source      |  Destination   |   Service    | Action |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | AUTORISER  |


4. Spécifiez le mode de mise en réseau dans le manifeste d’application pour chaque service `<NetworkConfig NetworkType="Open">`.  Le mode `Open` permet au service d’obtenir une adresse IP dédiée. Si aucun mode n’est pas spécifié, la valeur par défaut est le mode de base `nat`. Ainsi, dans l’exemple de manifeste suivant, `NodeContainerServicePackage1` et `NodeContainerServicePackage2` peuvent chacun être à l’écoute sur le même port (les deux services sont à l’écoute sur `Endpoint1`). Quand le mode de mise en réseau `Open` est précisé, vous ne pouvez pas spécifier de configurations `PortBinding`.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Vous pouvez combiner différents modes de mise en réseau entre les services au sein d’une application d’un cluster Windows. Par conséquent, vous pouvez avoir des services sur les modes de mise en réseau `Open` et `nat`. Lorsqu’un service est configuré avec le mode `nat`, son port d’écoute doit être unique. La combinaison de modes de mise en réseau pour différents services n’est pas prise en charge sur les clusters Linux. 


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez obtenu des informations sur les modes de mise en réseau offerts par Service Fabric.  

* [Modèle d'application Service Fabric](service-fabric-application-model.md)
* [Ressources du manifeste de Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-get-started-containers.md)
* [Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)
