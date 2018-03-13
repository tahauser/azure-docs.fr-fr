---
title: Diagnostics dans Azure Stack
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: e823aeb4291b3e765b35181c24b41fa58c170cca
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-diagnostics-tools"></a>Outils de diagnostics Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*
 
Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux. Cela peut compliquer le diagnostic des problèmes, notamment quand les erreurs proviennent de plusieurs composants Azure Stack en interaction. 

Nos outils de diagnostic aident à garantir la simplicité d’utilisation et l’efficacité du mécanisme de collection de journaux. Le diagramme suivant illustre le fonctionnement des outils de collecte de journaux Azure Stack :

![Outils de diagnostic Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Collecteur de traces
 
Le collecteur de traces est activé par défaut et s’exécute en continu en arrière-plan pour collecter tous les journaux de suivi d’événements pour Windows (ETW) à partir des services composants d’Azure Stack. Les journaux ETW sont stockés dans un partage local commun avec une durée de vie de cinq jours. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés. La taille maximale autorisée par défaut pour chaque fichier est de 200 Mo. Une vérification de la taille a lieu toutes les deux minutes ; si le fichier actuel a une taille supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go portant sur la taille totale des fichiers générés par session d’événements. 

## <a name="log-collection-tool"></a>Outil de collecte de journaux
 
Vous pouvez utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour collecter des journaux à partir de tous les composants dans un environnement Azure Stack. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si l’équipe de support technique Azure Stack a besoin de vos journaux pour résoudre un problème, elle peut vous demander d’exécuter cet outil.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.
 
Voici quelques exemples de types de journaux collectés :
*   **Journaux de déploiement Azure Stack**
*   **Journaux des événements Windows**
*   **Journaux Panther**
*   **Journaux de cluster**
*   **Journaux de diagnostics de stockage**
*   **Journaux ETW**

Ces fichiers sont collectés et enregistrés dans un partage par le Collecteur de traces. Vous pouvez ensuite utiliser l’applet de commande PowerShell **Get-AzureStackLog** pour les collecter en cas de nécessité.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Pour exécuter Get-AzureStackLog sur un système ASDK (Kit de développement Azure Stack)
1. Connectez-vous en tant que **AzureStack\CloudAdmin** sur l’hôte.
2. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
3. Exécutez l’applet de commande PowerShell **Get-AzureStackLog**.

**Exemples :**

  Collecter tous les journaux pour tous les rôles :

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal :

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux des 2 à 8 dernières heures :

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Pour exécuter Get-AzureStackLog sur un système intégré Azure Stack

Pour exécuter l’outil de collection de journaux sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter à l’aide du point de terminaison privilégié pour collecter des journaux sur un système intégré :

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Quand vous collectez les journaux à partir du point de terminaison privilégié, spécifiez le paramètre **OutputPath** afin qu’il corresponde à un emplacement sur la machine HLH (Hardware Lifecycle Host). Vérifiez également que l’emplacement est chiffré.
- Les paramètres **OutputSharePath** et **OutputShareCredential** sont facultatifs et sont utilisés quand vous chargez des journaux vers un dossier partagé externe. Utilisez ces paramètres *en plus* de **OutputPath**. Si le paramètre **OutputPath** n’est pas spécifié, l’outil de collecte des journaux utilise le lecteur système de la machine virtuelle PEP pour le stockage. Cela peut entraîner l’échec du script, car l’espace disque est limité.
- Comme indiqué dans l’exemple précédent, vous pouvez utiliser les paramètres **FromDate** et **ToDate** pour collecter des journaux pour une période donnée. Cela peut être pratique pour les scénarios, tels que la collection de journaux après l’application d’un package de mise à jour sur un système intégré.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considérations relatives aux paramètres du kit ASDK et des systèmes intégrés

- Si vous ne spécifiez pas les paramètres **FromDate** et **ToDate**, par défaut les journaux sont collectés pour les quatre dernières heures.
- Vous pouvez utiliser le paramètre **TimeOutInMinutes** pour définir le délai d’expiration pour la collecte des journaux. Il est défini sur 150 (2,5 heures) par défaut.

- Actuellement, vous pouvez utiliser le paramètre **FilterByRole** pour filtrer la collecte de journaux en fonction des rôles suivants :

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | IPC                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Domaine                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Passerelle                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController | KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Stockage                | StorageController  |
   | URP                     | UsageBridge            | VirtualMachines    |  
   | WAS                     | WASPUBLIC              | WDS                |


### <a name="bkmk_gui"></a>Collecter les journaux à l’aide d’une interface graphique utilisateur
Au lieu de fournir les paramètres obligatoires pour que la cmdlet Get-AzureStackLog récupère les journaux Azure Stack, vous pouvez tirer parti des outils Azure Stack open source disponibles dans le dépôt GitHub d’outils Azure Stack principal, à l’adresse http://aka.ms/AzureStackTools.

Le script PowerShell **ERCS_AzureStackLogs.ps1** est stocké dans le dépôt d’outils GitHub et est mis à jour régulièrement. Pour vous assurer de disposer de la dernière version disponible, nous vous conseillons de le télécharger directement sur http://aka.ms/ERCS. Démarré à partir d’une session d’administration PowerShell, le script se connecte au point de terminaison privilégié et exécute Get-AzureStackLog avec les paramètres fournis. Si aucun paramètre n’est fourni, le script invite par défaut l’utilisateur à fournir des paramètres par le biais d’une interface graphique utilisateur.

Pour plus d’informations sur le script PowerShell ERCS_AzureStackLogs.ps1, vous pouvez regarder [cette courte vidéo](https://www.youtube.com/watch?v=Utt7pLsXEBc) ou consulter le [fichier Lisez-moi](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) du script, qui se trouve dans le référentiel GitHub d’outils Azure Stack. 

### <a name="additional-considerations"></a>Considérations supplémentaires

* L’exécution de cette commande peut prendre un certain temps, en fonction des données du ou des rôles collectées par les journaux. Les facteurs qui entrent en compte sont la durée spécifiée pour la collecte de journaux et le nombre de nœuds de l’environnement Azure Stack.
* Une fois la collecte de journaux terminée, vérifiez le dossier créé dans le paramètre **OutputPath** spécifié dans la commande.
* Les journaux de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’un rôle collectés peuvent être séparés en plusieurs fichiers zip. Pour ce type de rôle, si vous souhaitez disposer de tous les fichiers journaux décompressés dans un dossier unique, utilisez un outil qui peut effectuer cette opération en blocs (7zip, par exemple). Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extract Here**. Cette opération permet de décompresser tous les fichiers journaux de ce rôle, au sein d’un dossier fusionné unique.
* Un fichier nommé **Get-AzureStackLog_Output.log** est également créé dans le dossier qui contient les fichiers journaux compressés. Ce fichier est un journal de la sortie de la commande, qui peut être utilisé pour résoudre des problèmes lors de la collection de journaux.
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux de plusieurs composants.
    -   Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle *VirtualMachines*.
    -   Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle *BareMetal*.
    -   Les journaux des événements de cluster de basculement et Hyper-V sont collectés dans le rôle *Storage*.
    -   Les journaux ACS sont collectés dans les rôles *Storage* et *ACS*.

> [!NOTE]
> Nous appliquons des limites de taille et d’âge aux journaux collectés, car il est essentiel de garantir une utilisation efficace de votre espace de stockage afin de s’assurer que vous ne vous retrouverez pas submergé de journaux. Toutefois, lorsque vous diagnostiquez un problème, vous avez parfois besoin de journaux qui n’existent plus, à cause de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux vers un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 mois, en fonction de vos besoins. Vérifiez également que l’emplacement de stockage est chiffré.

## <a name="next-steps"></a>Étapes suivantes
[Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md)

