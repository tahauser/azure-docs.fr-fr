---
title: "Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services avec PowerShell"
description: "Configuration de l’application de service cloud Azure à l’aide de PowerShell pour autoriser les connexions Bureau à distance"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 84fe7ba418399562b6e36ed009c5e6e47fbe24da
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services avec PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes rencontrés par votre application lorsqu'elle est en cours d'exécution.

Cet article décrit comment activer Bureau à distance sur vos rôles Service Cloud à l’aide de PowerShell. Voir [Installer et configurer Azure PowerShell](/powershell/azure/overview) pour connaître les conditions requises pour cet article. PowerShell utilise l’extension Bureau à distance, ce qui vous permet d’activer le Bureau à distance après avoir déployé l’application.

## <a name="configure-remote-desktop-from-powershell"></a>Configurer le Bureau à distance à partir de PowerShell

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) permet d’activer le Bureau à distance sur des rôles spécifiés ou sur tous les rôles de déploiement de votre service cloud. L’applet de commande vous permet de spécifier le nom d’utilisateur et le mot de passe de l’utilisateur de bureau à distance par le biais du paramètre *informations d’identification* qui accepte un objet PSCredential.

Si vous utilisez PowerShell de manière interactive, vous pouvez facilement définir l’objet PSCredential en appelant l’applet de commande [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Cette commande affiche une boîte de dialogue vous permettant de saisir le nom d’utilisateur et un mot de passe utilisateur distant de manière sécurisée.

Dans la mesure où PowerShell sera utilisé dans les scénarios d’automatisation, vous pouvez également configurer l’objet **PSCredential** d’une façon qui ne nécessite pas l’interaction utilisateur. Vous devez d'abord configurer un mot de passe sécurisé. Commencez par spécifier un mot de passe en texte brut et convertissez-le en chaîne sécurisée à l’aide [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vous devez ensuite convertir cette chaîne sécurisée en chaîne standard chiffrée avec [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Vous pouvez maintenant enregistrer cette chaîne chiffrée standard dans un fichier [Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Vous pouvez également créer un fichier de mot de passe sécurisé pour ne pas avoir à retaper le mot de passe à chaque fois. En outre, un fichier de mot de passe sécurisé est préférable à un fichier texte brut. Utilisez l’opération PowerShell suivante pour créer un fichier de mot de passe sécurisé :

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Lors de la définition du mot de passe, assurez-vous que vous remplissez les [exigences de complexité](https://technet.microsoft.com/library/cc786468.aspx).

Pour créer l’objet d’informations d’identification à partir du fichier de mot de passe sécurisé, vous devez lire le contenu du fichier et le reconvertir en chaîne sécurisée à l’aide [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) accepte également un paramètre *Expiration* qui spécifie une valeur **DateTime** à laquelle le compte d’utilisateur expire. Par exemple, vous pouvez configurer le compte pour qu'il expire quelques jours après la date et l'heure actuelles.

Cet exemple PowerShell vous montre comment définir l’extension Bureau à distance sur un service cloud :

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Vous pouvez également spécifier l’emplacement de déploiement et les rôles desquels vous souhaitez supprimer l’extension de bureau distant. Si ces paramètres ne sont pas spécifiés, l’applet de commande active le bureau distant sur tous les rôles dans l’emplacement de déploiement de l'environnement de **production** .

L’extension Bureau à distance est associée au déploiement. Si vous créez un nouveau déploiement du service, vous devrez activer le Bureau à distance sur ce déploiement. Si vous souhaitez toujours que le Bureau à distance soit activé, vous devez envisager d’intégrer les scripts PowerShell dans votre flux de travail de déploiement.

## <a name="remote-desktop-into-a-role-instance"></a>Bureau à distance dans une instance de rôle

L’applet de commande [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) est utilisée pour les services bureau à distance dans une instance de rôle spécifique de votre service cloud. Vous pouvez utiliser le paramètre *LocalPath* pour télécharger le fichier RDP localement. Ou vous pouvez utiliser le paramètre *Lauch* pour lancer directement la boîte de dialogue Connexion Bureau à distance pour accéder à l’instance du rôle de service cloud.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Vérifiez si l’extension des services de Bureau à distance est activée sur un service

L’applet de commande [Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) montre si le Bureau à distance est activé ou désactivé sur un déploiement de service. L’applet de commande renvoie le nom d’utilisateur de l’utilisateur de bureau à distance et les rôles sur lesquels l’extension de bureau distant est activée. Par défaut, cela se produit sur l’emplacement de déploiement, et vous pouvez choisir d’utiliser l’emplacement intermédiaire à la place.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Supprimer l’extension de bureau distant d’un service

Si vous avez déjà activé l’extension du bureau à distance sur un déploiement et si vous devez mettre à jour les paramètres du bureau distant, vous devez d’abord supprimer l’extension, puis la réactiver avec les nouveaux paramètres. Par exemple, si vous souhaitez définir un nouveau mot de passe pour le compte d’utilisateur distant, ou si le compte a expiré. Cette opération est obligatoire uniquement sur les déploiements existants dont l’extension de bureau à distance est activée. Pour les nouveaux déploiements, vous pouvez simplement appliquer directement l’extension.

Pour supprimer l’extension de bureau à distance à partir du déploiement, vous pouvez utiliser l’applet de commande [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) . Vous pouvez également spécifier l’emplacement de déploiement et le rôle duquel vous souhaitez supprimer l’extension de bureau distant.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Pour supprimer complètement la configuration d’extension, vous devez appeler l’applet de commande *remove* avec le paramètre **UninstallConfiguration** .
>
> Le paramètre **UninstallConfiguration** désinstalle toute configuration d’extension appliquée au service. Chaque configuration de l’extension est associée à la configuration du service. L’appel de l’applet de commande *remove* sans **UninstallConfiguration** dissocie le <mark>déploiement</mark> de la configuration d’extension, supprimant ainsi de manière efficace l’extension. Cependant, la configuration d’extension reste associée au service.

## <a name="additional-resources"></a>Ressources supplémentaires

[Configuration des services cloud](cloud-services-how-to-configure-portal.md)
