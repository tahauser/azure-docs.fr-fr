---
title: "Activation du débogage distant avec la remise continue | Microsoft Docs"
description: "Découvrez comment activer le débogage distant lors de l'utilisation de la remise continue pour publier sur Azure."
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 1a30b42e6e84edf9a7cef861aaf6a60e87c473d0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Activation du débogage distant lors de l'utilisation de la remise continue pour publier sur Azure
En procédant comme suit, vous pouvez activer le débogage distant dans Azure, pour des services cloud ou des machines virtuelles, quand vous utilisez la [remise continue](cloud-services-dotnet-continuous-delivery.md) afin de publier sur Azure.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Activation du débogage distant pour les services cloud
1. Dans l'agent de build, configurez l'environnement de départ pour Azure comme expliqué à la page [Génération en mode ligne de commande pour Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Le runtime de débogage distant (msvsmon.exe) étant nécessaire pour ce package, installez les **Outils de contrôle à distance de Visual Studio**.

   * [Outils de contrôle à distance de Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
   * [Outils de contrôle à distance de Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
   * [Outils de contrôle à distance de Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
   Une autre méthode consiste à copier les fichiers binaires de débogage distant à partir d'un système sur lequel Visual Studio est installé.

3. Créez un certificat comme expliqué dans [Vue d’ensemble des certificats pour Azure Cloud Services](cloud-services-certs-create.md). Conservez l'empreinte numérique de certificat .pfx et RDP et téléchargez le certificat sur le service cloud cible.
4. Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et créer des packages en activant le débogage distant. (Remplacez les chemins d'accès réels de votre système et de vos fichiers de projet pour les éléments entre crochets.)
   
   ```cmd
   msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   ```
   
   `VSX64RemoteDebuggerPath` est le chemin d’accès au dossier contenant msvsmon.exe dans les Outils de contrôle à distance de Visual Studio.
   `RemoteDebuggerConnectorVersion` est la version du SDK Azure de votre service cloud. Elle doit également correspondre à la version installée avec Visual Studio.

5. Publiez sur le service cloud cible en utilisant le package et le fichier .cscfg générés à l'étape précédente.
6. Importez le certificat (fichier .pfx) sur l'ordinateur sur lequel Visual Studio et le Kit de développement logiciel (SDK) Azure SDK pour .NET sont installés. Veillez à effectuer l’importation dans le magasin de certificats `CurrentUser\My` ; dans le cas contraire, l’association au débogueur dans Visual Studio échouera.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Activation du débogage distant pour les machines virtuelles
1. Créez une machine virtuelle Azure. Consultez [Création d’une machine virtuelle exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Créer et gérer des machines virtuelles Azure dans Visual Studio](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. Sur le portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), naviguez jusqu’à **RDP CERTIFICATE THUMBPRINT** de la machine virtuelle. Cette valeur est utilisée pour la valeur `ServerThumbprint` dans la configuration de l’extension.
3. Créez un certificat client comme expliqué dans [Vue d’ensemble des certificats pour Azure Cloud Services](cloud-services-certs-create.md) (conservez l’empreinte numérique de certificat .pfx et RDP).
4. Installez Azure PowerShell (version 0.7.4 ou ultérieure) comme indiqué dans [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
5. Exécutez le script suivant pour activer l'extension RemoteDebug. Remplacez les chemins d’accès et les données personnelles par les vôtres, notamment le nom de l’abonnement, le nom du service et l’empreinte numérique.
   
   > [!NOTE]
   > Ce script est configuré pour Visual Studio 2015. Si vous utilisez Visual Studio 2013 ou Visual Studio 2017, remplacez les affectations `$referenceName` et `$extensionName` ci-dessous par `RemoteDebugVS2013` ou `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importez le certificat (.pfx) sur l'ordinateur sur lequel Visual Studio et le Kit de développement logiciel (SDK) Azure pour .NET sont installés.

