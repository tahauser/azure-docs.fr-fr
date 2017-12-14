---
title: Extension de diagnostic de performance des machines virtuelles Azure pour Windows | Microsoft Docs
description: "Présente l’extension de diagnostic de performance des machines virtuelles Azure pour Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: d9384af2cf1d8b3f55f9ec2316046536634c124e
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extension de diagnostic de performance des machines virtuelles Azure pour Windows

## <a name="summary"></a>Résumé
L’extension de diagnostic de performance des machines virtuelles Azure permet de collecter des données de diagnostic de performance auprès de machines virtuelles Windows, d’effectuer une analyse et de fournir un rapport de résultats et recommandations pour identifier et résoudre les problèmes de performance sur les machines virtuelles. Cette extension installe un outil de résolution des problèmes appelé [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Composants requis
### <a name="operating-systems"></a>Systèmes d’exploitation
Vous pouvez installer cette extension sur les systèmes d’exploitation Windows Server 2008 R2, 2012, 2012 R2, 2016, Windows 8.1 et Windows 10.

## <a name="extension-schema"></a>Schéma de l’extension
Le code JSON suivant montre le schéma de l’extension de diagnostic de performance Azure. Cette extension nécessite le nom et la clé d’un compte de stockage pour stocker la sortie et le rapport de diagnostic. Ces valeurs sont sensibles et doivent être stockées dans une configuration de paramètre de protection. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible. Notez que storageAccountName et storageAccountKey respectent la casse. Les autres paramètres obligatoires sont répertoriés dans la section ci-dessous.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Valeurs de propriétés

|   **Name**   |**Valeur/Exemple**|       **Description**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Version de l’API
|publisher|Microsoft.Azure.Performance.Diagnostics|Espace de noms du serveur de publication de l’extension
|type|AzurePerformanceDiagnostics|Type de l’extension de machine virtuelle
|typeHandlerVersion|1.0|Version du gestionnaire de l’extension
|performanceScenario|basic|Scénario de performances pour lequel capturer les données. Les valeurs valides sont : **basic**, **vmslow**, **azurefiles** et **custom**.
|traceDurationInSeconds|300|Durée des traces si l’une des options de trace est sélectionnée.
|perfCounterTrace|p|Option permettant d’activer la trace du compteur de performances. Les valeurs valides sont **p** ou une valeur vide. Si vous ne souhaitez pas capturer cette trace, laissez la valeur vide.
|networkTrace|n|Option permettant d’activer la trace Netmon. Les valeurs valides sont **n** ou une valeur vide. Si vous ne souhaitez pas capturer cette trace, laissez la valeur vide.
|xperfTrace|x|Option permettant d’activer la trace XPerf. Les valeurs valides sont **x** ou une valeur vide. Si vous ne souhaitez pas capturer cette trace, laissez la valeur vide.
|storPortTrace|s|Option permettant d’activer la trace StorPort. Les valeurs valides sont s ou une valeur vide. Si vous ne souhaitez pas capturer cette trace, laissez la valeur vide.
|srNumber|123452016365929|Numéro de ticket de support si disponible. Ne le renseignez pas si vous ne l’avez pas.
|storageAccountName|mystorageaccount|Nom du compte de stockage pour stocker les journaux et les résultats de diagnostic.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Clé du compte de stockage.

## <a name="install-the-extension"></a>Installer l’extension

Suivez ces étapes pour installer l’extension de machine virtuelle sur des machines virtuelles Windows :

1. Connectez-vous au [Portail Azure](http://portal.azure.com).
2. Sélectionnez la machine virtuelle sur laquelle vous voulez installer cette extension.

    ![Sélectionnez la machine virtuelle](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Sélectionnez le panneau **Extensions** et cliquez sur le bouton **Ajouter**.

    ![Sélectionner des extensions](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Sélectionnez l’extension de diagnostic de performance Azure, passez en revue les termes et conditions, puis cliquez sur le bouton **Créer**.

    ![Créer l’extension de diagnostic de performance Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Indiquez les valeurs de paramètre pour l’installation et cliquez sur **OK** pour installer l’extension. Des informations complémentaires sur les scénarios de résolution des problèmes pris en charge sont disponibles [ici](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Installer l’extension](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Une fois que l’installation est accomplie, un message vous indique que l’approvisionnement a réussi.

    ![Message de confirmation de la réussite de l’approvisionnement](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > L’exécution de l’extension démarre une fois que l’approvisionnement a réussi et quelques minutes au plus sont nécessaires pour terminer l’exécution du scénario de base. Pour les autres scénarios, la durée d’exécution est spécifiée pendant l’installation.

## <a name="remove-the-extension"></a>Supprimer l’extension
Pour supprimer l’extension d’une machine virtuelle, effectuez ces étapes :

1. Connectez-vous au [portail Azure](http://portal.azure.com), sélectionnez la machine virtuelle sur laquelle supprimer cette extension, puis sélectionnez le panneau Extensions. 
2. Cliquez sur (**...** ) en regard de l’entrée Extension de diagnostic de performance dans la liste, puis sélectionnez Désinstaller.

    ![Désinstaller l’extension](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Vous pouvez également sélectionner l’entrée de l’extension et sélectionner l’option Désinstaller.

## <a name="template-deployment"></a>Déploiement de modèle
Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension de diagnostic de performance Azure pendant un déploiement de modèle Azure Resource Manager. Voici un exemple de modèle que vous pouvez utiliser avec un déploiement de modèle :

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Déploiement PowerShell
Vous pouvez utiliser la commande `Set-AzureRmVMExtension` pour déployer l’extension de diagnostic de performance de machine virtuelle Azure sur une machine virtuelle existante. Avant d’exécuter la commande, les configurations publique et privée doivent être stockées dans une table de hachage PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Informations sur les données capturées
L’outil PerfInsights collecte divers journaux, configurations, données de diagnostic, etc., selon le scénario sélectionné. Pour plus d’informations sur les données collectées par scénario, consultez la [documentation de PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Afficher et partager les résultats

La sortie de l’extension est stockée dans un dossier nommé log_collection sous le lecteur Temp (généralement D:\log_collection) par défaut. Sous ce dossier, vous pouvez voir des fichiers zip qui contiennent les journaux de diagnostic et un rapport présentant les résultats et recommandations.

Le fichier zip créé est également chargé dans le compte de stockage fourni pendant l’installation et partagé pendant 30 jours à l’aide de [signatures d’accès partagé (SAP)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Un fichier texte nommé *zipfilename*_saslink.txt est également créé dans le dossier log_collection. Ce fichier contient le lien SAP créé pour télécharger le fichier zip. Toute personne qui dispose de ce lien est en mesure de télécharger le fichier zip.

Microsoft peut utiliser ce lien SAP pour télécharger les données de diagnostic à des fins d’examen approfondi par l’ingénieur qui travaille sur votre ticket de support.

Pour afficher le rapport, il vous suffit d’extraire le fichier zip et d’ouvrir le fichier **PerfInsights Report.htm**.

Vous pouvez également télécharger le fichier zip directement à partir du portail en sélectionnant l’extension.

![Afficher l’état détaillé](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Le lien SAP affiché dans le portail peut ne pas fonctionner parfois en raison d’une URL mal formée (à cause de caractères spéciaux) pendant l’opération de codage et décodage. La solution consiste à obtenir le lien directement à partir du fichier *_saslink.txt de la machine virtuelle.

## <a name="troubleshoot-and-support"></a>Dépannage et support technique
### <a name="troubleshoot"></a>Résolution des problèmes

- L’état du déploiement de l’extension (dans la zone de notification) peut indiquer « Déploiement en cours » même si l’extension est correctement approvisionnée.

    Vous pouvez ignorer ce problème en toute sécurité tant que l’état de l’extension indique que l’extension est correctement approvisionnée.
- Vous pouvez résoudre certains problèmes survenant lors de l’installation en utilisant les journaux de l’extension. La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
