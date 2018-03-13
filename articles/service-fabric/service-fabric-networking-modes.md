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
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: fafa7dc9ae84e49cdadcb047984792b353429df7
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="service-fabric-container-networking-modes"></a>Modes de mise en réseau du conteneur Service Fabric

Un cluster Azure Service Fabric pour les services de conteneur utilise le mode de mise en réseau **nat** par défaut. Lorsque plusieurs services de conteneur sont à l’écoute sur le même port et que le mode nat est utilisé, des erreurs de déploiement peuvent se produire. Pour prendre en charge plusieurs services de conteneur à l’écoute sur le même port, Service Fabric propose le mode de mise en réseau **Ouvert** (versions 5.7 et ultérieures). En mode Ouvert, chaque service de conteneur a une adresse IP interne attribuée de manière dynamique qui prend en charge plusieurs services à l’écoute sur le même port.  

Si vous avez un service de conteneur avec un point de terminaison statique dans votre manifeste de service, vous pouvez créer et supprimer de nouveaux services à l’aide du mode Ouvert sans erreurs de déploiement. Le même fichier docker-compose.yml peut également être utilisé avec les mappages de ports statiques pour créer plusieurs services.

Lorsqu’un service de conteneur redémarre ou se déplace vers un autre nœud du cluster, l’adresse IP change. Pour cette raison, nous ne recommandons pas l’utilisation de l’adresse IP attribuée de manière dynamique pour découvrir les services de conteneur. Seul le service d’affectation de noms de Service Fabric ou le service DNS doivent être utilisés pour la découverte de services. 

>[!WARNING]
>Azure permet un total de 4 096 adresses IP par réseau virtuel. Par conséquent, la somme du nombre de nœuds et du nombre d’instances de service de conteneur (utilisant le mode Ouvert) ne peuvent pas dépasser 4,096 au sein d’un réseau virtuel. Pour les scénarios de haute densités, nous recommandons le mode de mise en réseau nat.
>

## <a name="set-up-open-networking-mode"></a>Configurer le mode de mise en réseau Ouvert

1. Configurer le modèle Azure Resource Manager. Dans la section **fabricSettings**, activer le Service DNS et le fournisseur IP : 

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

2. Configurer la section de profil réseau pour autoriser la configuration de plusieurs adresses IP sur chaque nœud du cluster. L’exemple suivant définit cinq adresses IP par nœud pour un cluster Windows/Linux Service Fabric. Vous pouvez avoir cinq instances de service à l’écoute sur le port de chaque nœud.

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
 
3. Pour les clusters Windows uniquement, configurez une règle de groupe de sécurité réseau (NSG) Azure qui ouvre le port UDP/53 pour le réseau virtuel avec les valeurs suivantes :

   |Paramètre |Valeur | |
   | --- | --- | --- |
   |Priorité |2000 | |
   |NOM |Custom_Dns  | |
   |Source |VirtualNetwork | |
   |Destination | VirtualNetwork | |
   |de diffusion en continu | DNS (UDP/53) | |
   |Action | AUTORISER  | |
   | | |

4. Spécifiez le mode de mise en réseau dans le manifeste d’application pour chaque service : `<NetworkConfig NetworkType="Open">`. Le mode **Ouvrir** permet au service d’obtenir une adresse IP dédiée. Si un mode n’est pas spécifié, par défaut, le service est en mode **nat**. Dans l’exemple de manifeste suivant, les services `NodeContainerServicePackage1` et `NodeContainerServicePackage2` peuvent chacun être à l’écoute sur le même port (les deux services sont à l’écoute sur `Endpoint1`). Quand le mode de mise en réseau Ouvert est spécifié, les configurations `PortBinding` ne peuvent pas être spécifiées.

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

    Vous pouvez combiner différents modes de mise en réseau entre les services au sein d’une application d’un cluster Windows. Certains services peuvent utiliser le mode Ouvert, tandis que d’autres utilisent le mode nat. Lorsqu’un service est configuré pour utiliser le mode nat, le port d’écoute du service doit être unique.

    >[!NOTE]
    >La combinaison de modes de mise en réseau pour différents services n’est pas prise en charge sur les clusters Linux. 
    >

5. Si le mode **Ouvert** est sélectionné, la définition du **point de terminaison** dans le manifeste de service doit pointer explicitement sur le package de code correspondant au point de terminaison, même si le package de service contient un seul package de code. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```

## <a name="next-steps"></a>Étapes suivantes
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)
* [En savoir plus sur les ressources du manifeste du service Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-get-started-containers.md)
* [Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)
