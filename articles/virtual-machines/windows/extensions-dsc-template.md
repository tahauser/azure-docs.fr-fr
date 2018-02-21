---
title: "Extension de configuration d’état souhaité avec des modèles Azure Resource Manager | Microsoft Docs"
description: "Définition du modèle Resource Manager pour la configuration d’état souhaité dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: f638d1530541526316f6e409f1efd44f136992a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extension de configuration d’état souhaité avec des modèles Azure Resource Manager

Cet article décrit le modèle Azure Resource Manager destiné au [gestionnaire de l’extension Configuration d’état souhaité](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

*Remarque : vous pourriez rencontrer des exemples de schéma légèrement différents. *
*La modification de schéma s’est produite au cours de la mise à jour du mois d’octobre 2016.* 
 *Les détails sont indiqués dans la section de cette page intitulée*
*[Mise à jour de l’ancien format](##Updating-from-the-Previous-Format)*.

## <a name="template-example-for-a-windows-vm"></a>Exemple de modèle pour une machine virtuelle sous Windows

L’extrait de code suivant va dans la section « Resource » du modèle.
L’Extension DSC hérite des propriétés d’extension par défaut documentées dans la [classe VirtualMachineExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.72",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                    "configurationArguments": {
                        {
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        "RegistrationUrl" : "[parameters('registrationUrl1')]",
                        "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
```

## <a name="template-example-for-windows-vmss"></a>Exemple de modèle pour un VMSS Windows

Un nœud VMSS comporte une section « properties » avec l’attribut « VirtualMachineProfile », « extensionProfile ». L’extension Configuration d’état souhaité (DSC) est ajoutée sous « extensions ».

L’Extension DSC hérite des propriétés d’extension par défaut documentées dans la [classe VirtualMachineScaleSetExtension](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.72",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configurationArguments": {
                                {
                                    "Name": "RegistrationKey",
                                    "Value": {
                                        "UserName": "PLACEHOLDER_DONOTUSE",
                                        "Password": "PrivateSettingsRef:registrationKeyPrivate"
                                    },
                                },
                                "RegistrationUrl" : "[parameters('registrationUrl1')]",
                                "NodeConfigurationName" : "nodeConfigurationNameValue1"
                        }
                        },
                        "protectedSettings": {
                            "Items": {
                                        "registrationKeyPrivate": "[parameters('registrationKey1']"
                                    }
                        }
                    }
                ]
            }
        }
```

## <a name="detailed-settings-information"></a>Informations détaillées sur les paramètres

Voici le schéma pour la section « settings » des paramètres de l’extension DSC Azure dans un modèle Azure Resource Manager.

*Pour obtenir la liste des arguments pour le script de configuration par défaut,*
*, consultez la section ci-dessous intitulée*
*[Script de configuration par défaut](##Default-Configuration-Script)*.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  }
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Détails

| Nom de la propriété | type | DESCRIPTION |
| --- | --- | --- |
| settings.wmfVersion |chaîne |Spécifie la version de Windows Management Framework qui doit être installée sur votre machine virtuelle. Lorsque cette propriété est définie sur « latest », la dernière version de WMF est installée. Les seules valeurs possibles actuellement pour cette propriété sont **4.0 », « 5.0 », « 5.0PP », and « latest »**. Les valeurs possibles font l’objet de mises à jour. La valeur par défaut est « latest ». |
| settings.configuration.url |chaîne |Spécifie l’adresse URL de téléchargement de votre fichier .zip de configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationUrlSasToken sur la valeur du jeton SAP. Cette propriété est requise si la propriété settings.configuration.script ou settings.configuration.function est définie. Si aucune valeur n’est indiquée pour ces propriétés, l’extension appellera le script de configuration par défaut pour définir les métadonnées du gestionnaire de configuration locale et les arguments doivent être fournis. |
| settings.configuration.script |chaîne |Spécifie le nom de fichier du script qui contient la définition de votre configuration DSC. Ce script doit se trouver dans le dossier racine du fichier .zip téléchargé depuis l’URL spécifiée par la propriété configuration.url. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.script est définie. Si aucune valeur n’est indiquée pour ces propriétés, l’extension appellera le script de configuration par défaut pour définir les métadonnées du gestionnaire de configuration locale et les arguments doivent être fournis. |
| settings.configuration.function |chaîne |Spécifie le nom de votre configuration DSC. La configuration nommée doit se trouver dans le script défini par configuration.script. Cette propriété est requise si la propriété settings.configuration.url ou settings.configuration.function est définie. Si aucune valeur n’est indiquée pour ces propriétés, l’extension appellera le script de configuration par défaut pour définir les métadonnées du gestionnaire de configuration locale et les arguments doivent être fournis. |
| settings.configurationArguments |Collection |Définit les paramètres à transmettre à votre configuration DSC. Cette propriété n’est pas chiffrée. |
| settings.configurationData.url |chaîne |Spécifie l’URL de téléchargement de votre fichier de données de configuration (.psd1) à utiliser comme entrée pour votre configuration DSC. Si l’accès à l’URL fournie nécessite un jeton SAP, vous devez définir la propriété protectedSettings.configurationDataUrlSasToken sur la valeur du jeton SAP. |
| settings.privacy.dataEnabled |chaîne |Active ou désactive la collecte télémétrique. Les seules valeurs possibles pour cette propriété sont **« Enable », « Disable », « » ou « $null »**. Le fait de laisser cette propriété vide ou de la définir sur $null active la télémétrie. La valeur par défaut est « ». [Plus d’informations](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Collection |Définit d’autres emplacements de téléchargement de WMF. [Plus d’informations](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Collection |Définit les paramètres à transmettre à votre configuration DSC. Cette propriété est chiffrée. |
| protectedSettings.configurationUrlSasToken |chaîne |Spécifie le jeton SAP permettant d’accéder à l’URL définie par configuration.url. Cette propriété est chiffrée. |
| protectedSettings.configurationDataUrlSasToken |chaîne |Spécifie le jeton SAP permettant d’accéder à l’URL définie par configurationData.url. Cette propriété est chiffrée. |

## <a name="default-configuration-script"></a>Script de configuration par défaut

Pour plus d’informations sur ces valeurs, consultez la page de documentation [Paramètres de base du gestionnaire de configuration locale](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#basic-settings).
Seules les propriétés du gestionnaire de configuration locale dans le tableau ci-dessous sont configurables à l’aide d’un script de configuration de l’extension DSC.

| Nom de la propriété | type | DESCRIPTION |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |Propriété requise. Spécifie la clé utilisée pour un nœud à inscrire avec le service Azure Automation en tant que mot de passe d’un objet d’informations d’identification PowerShell. Cette valeur peut être découverte automatiquement à l’aide de la méthode listkeys sur le compte Automation et doit être sécurisée comme un paramètre protégé. |
| settings.configurationArguments.RegistrationUrl |chaîne |Propriété requise. Spécifie l’URL du point de terminaison Azure Automation où le nœud tente de s’inscrire. Cette valeur peut être découverte automatiquement à l’aide de la méthode de référence sur le compte Automation. |
| settings.configurationArguments.NodeConfigurationName |chaîne |Propriété requise. Spécifie la configuration de nœud dans le compte Azure Automation à affecter au nœud. |
| settings.configurationArguments.ConfigurationMode |chaîne |Spécifie le mode pour le gestionnaire de configuration locale. Les options valides sont « ApplyOnly », « ApplyandMonitor » et « ApplyandAutoCorrect ».  La valeur par défaut est « ApplyandMonitor ». |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Spécifie la fréquence à laquelle le gestionnaire de configuration locale tentera de vérifier avec les mises à jour du compte Automation.  La valeur par défaut est 30.  La valeur minimale est 15. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Spécifie la fréquence à laquelle le gestionnaire de configuration locale valide la configuration actuelle.  La valeur par défaut est 15.  La valeur minimale est 15. |
| settings.configurationArguments.RebootNodeIfNeeded | booléenne | Spécifie si un nœud peut être redémarré automatiquement si une opération DSC le demande.  La valeur par défaut est false. |
| settings.configurationArguments.ActionAfterReboot | chaîne | Spécifie ce qu’il se passe après un redémarrage lors de l’application d’une configuration. Les options valides sont « ContinueConfiguration » et « StopConfiguration ». La valeur par défaut est « ContinueConfiguration ». |
| settings.configurationArguments.AllowModuleOverwrite | booléenne | Spécifie si le gestionnaire de configuration locale remplacera les modules existants sur le nœud.  La valeur par défaut est false. |

## <a name="settings-vs-protectedsettings"></a>settings vs protectedSettings

Tous les paramètres sont enregistrés dans un fichier texte de paramètres sur la machine virtuelle.
Les propriétés définies sous « settings » sont des propriétés publiques, car elles ne sont pas chiffrées dans le fichier texte de paramètres.
Les propriétés définies sous « protectedSettings » sont chiffrées avec un certificat et ne sont pas affichées en texte brut dans ce fichier sur la machine virtuelle.

Si des informations d’identification sont requises pour la configuration, elles peuvent être incluses dans protectedSettings :

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>exemples

L’exemple suivant est le comportement par défaut de l’extension DSC, qui consiste à fournir des paramètres de métadonnées au gestionnaire de configuration locale et de s’inscrire auprès du service DSC Azure Automation.
Les arguments de configuration sont nécessaires et seront passés au script de configuration par défaut pour définir les métadonnées du gestionnaire de configuration locale.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
  }
},
"protectedSettings": {
    "Items": {
                "registrationKeyPrivate": "[parameters('registrationKey1']"
            }
}
```

## <a name="example-using-configuration-script-in-azure-storage"></a>Exemple à l’aide d’un script de configuration dans le stockage Azure

L’exemple suivant est tiré de la section « Prise en main » de l’article [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Cet exemple utilise des modèles Resource Manager au lieu d’applets de commande pour déployer l’extension.
Enregistrez la configuration « IisInstall.ps1 », placez-la dans un fichier .zip et chargez le fichier dans une URL accessible.
Cet exemple utilise le stockage Blob Azure, mais il est possible de télécharger les fichiers .zip depuis n’importe quel emplacement arbitraire.

Dans le modèle Azure Resource Manager, le code suivant demande à la VM de télécharger le fichier correct et d’exécuter la fonction PowerShell appropriée :

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Mise à jour depuis le format précédent

Tous les paramètres au format précédent (qui contient les propriétés publiques ModulesUrl, ConfigurationFunction, SasToken ou Properties) s’adaptent automatiquement au format actuel et s’exécutent exactement comme avant.

Le schéma suivant est ce à quoi les paramètres précédents ressemblaient :

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Voici comment le format précédent s’adapte au format actuel :

| Nom de la propriété | Équivalent dans le schéma précédent |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Première partie de settings.ConfigurationFunction (avant « \\\\ ») |
| settings.configuration.function |Deuxième partie de settings.ConfigurationFunction (après « \\\\ ») |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sans jeton SAP) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Jeton SAP de protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Dépannage : code d’erreur 1100

Le code d’erreur 1100 indique qu’il existe un problème avec l’entrée utilisateur pour l’extension DSC.
Le texte de ces erreurs est variable et peut changer.
Voici certaines des erreurs que vous pouvez rencontrer et comment vous pouvez les résoudre.

### <a name="invalid-values"></a>Valeurs non valides

« Privacy.dataCollection is '{0}'.
The only possible values are '', 'Enable' et 'Disable'.
« WmfVersion est '{0}'.
Les seules valeurs possibles sont les suivantes : and 'latest'".

Problème : Une valeur fournie n’est pas autorisée.

Solution : Remplacer la valeur non valide par une valeur valide.
Consultez le tableau dans la section Détails.

### <a name="invalid-url"></a>URL non valide

« ConfigurationData.url is '{0}'. This is not a valid URL » (configurationData.url est « {0} ». Il ne s’agit pas d’une URL valide.) « DataBlobUri is '{0}'. This is not a valid URL » (DataBlobUri est « {0} ». Il ne s’agit pas d’une URL valide.) « Configuration.url is '{0}'. This is not a valid URL » (configuration.url est « {0} ». Il ne s’agit pas d’une URL valide.)

Problème : Une URL fournie n’est pas valide.

Solution : Vérifiez toutes les URL que vous avez fournies.
Assurez-vous que toutes les URL se résolvent en emplacements valides auxquels l’extension peut accéder sur l’ordinateur distant.

### <a name="invalid-configurationargument-type"></a>Type configurationArguments non valide

« Invalid configurationArguments type {0} » (Type configurationArguments {0} non valide)

Problème : La propriété configurationArguments ne peut pas se résoudre en objet de table de hachage.

Solution : Faites de votre propriété configurationArguments une table de hachage.
Suivez le format fourni dans l’exemple précédent.
Prenez garde aux guillemets, aux virgules et aux accolades.

### <a name="duplicate-configurationarguments"></a>Propriétés configurationArguments en double

« Found duplicate arguments '{0}' in both public and protected configurationArguments » (Arguments « {0} » en double trouvés dans les paramètres configurationArguments publics et protégés)

Problème : Les arguments configurationArguments dans les paramètres publics et protégés contiennent des propriétés portant le même nom.

Solution : Supprimez l’une des propriétés en double.

### <a name="missing-properties"></a>Propriétés manquantes
« Configuration.function requires that configuration.url or configuration.module is specified » (configuration.url ou configuration.module doit être spécifié pour configuration.function.)

« Configuration.url requires that configuration.script is specified » (configuration.script doit être spécifié pour configuration.url)

« Configuration.script requires that configuration.url is specified » (configuration.url doit être spécifié pour configuration.script)

« Configuration.url requires that configuration.function is specified » (configuration.function doit être spécifié pour configuration.url)

« Configuration.script requires that configuration.url is specified » (configuration.url doit être spécifié pour configurationUrlSasToken)

« ConfigurationDataUrlSasToken requires that configurationData.url is specified » (configurationData.url doit être spécifié pour configurationDataUrlSasToken)

Problème : Une propriété définie a besoin d’une autre propriété qui est manquante.

Solutions :

- Fournissez la propriété manquante.
- Supprimez la propriété qui a besoin de la propriété manquante.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur DSC et les groupes identiques de machines virtuelles dans [Utilisation des groupes identiques de machines virtuelles avec l’extension DSC Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).

En savoir plus sur la [Gestion des informations d’identification sécurisées de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur le gestionnaire d’extensions DSC Azure, voir [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).
