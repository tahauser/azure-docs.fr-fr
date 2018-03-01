---
title: "Forum aux questions sur l’intégration des journaux Azure | Microsoft Docs"
description: "Cet article répond à des questions sur l’intégration des journaux Azure."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 02/16/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 615bfb1ea86d31733fc1db7139cd995fbbbac7aa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-log-integration-faq"></a>Forum aux questions sur l’intégration des journaux Azure

Cet article contient les réponses à certaines questions fréquemment posées sur l’intégration des journaux Azure.

>[!IMPORTANT]
>La méthode recommandée pour intégrer les journaux Azure consiste à utiliser le connecteur Azure Monitor de votre fournisseur SIEM et à suivre les [instructions](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) ci-après. Toutefois, si votre fournisseur SIEM ne propose pas de connecteur pour Azure Monitor, vous pouvez utiliser le service Azure Log Integration de façon temporaire (s’il prend en charge votre système SIEM) jusqu’à ce qu’un tel connecteur soit disponible.

Azure Log Integration est un service du système d’exploitation Windows que vous pouvez utiliser pour intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité associés à vos applications.

## <a name="is-the-azure-log-integration-software-free"></a>Le logiciel d’intégration des journaux Azure est-il disponible ?
Oui. Il n’existe aucun frais pour le logiciel d’intégration des journaux Azure.

## <a name="where-is-azure-log-integration-available"></a>Où l’intégration des journaux Azure est-elle disponible ?

Elle est actuellement disponible dans les versions commerciales d’Azure et dans Azure Government, mais n’est pas disponible en Chine ni en Allemagne.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Comment puis-je voir les comptes de stockage desquels l’intégration des journaux Azure extrait des journaux de machines virtuelles Azure ?
Exécutez la commande **AzLlog source list**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Comment puis-je savoir de quel abonnement proviennent les journaux d’intégration Azure ?

Dans le cas des journaux d’audit qui sont placés dans les répertoires **AzureResourcemanagerJson**, l’ID d’abonnement figure dans le nom du fichier journal. Il en va de même pour les journaux dans le dossier **AzureSecurityCenterJson**. Par exemple : 

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Les noms des journaux d’audit Azure Active Directory incluent l’ID de locataire.

Le nom des journaux de diagnostic qui sont lus à partir d’un Event Hub ne comprend pas l’ID d’abonnement. Toutefois, il comprend le nom convivial spécifié lors de la création de la source Event Hub. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Comment puis-je mettre à jour la configuration du proxy ?
Si vos paramètres de proxy ne permettent pas un accès direct au stockage Azure, ouvrez le fichier **AZLOG. EXE. CONFIGURATION** dans **c:\Program Files\Microsoft Azure Log Integration**. Mettez à jour le fichier de manière à y inclure la section **defaultProxy** avec l’adresse de proxy de votre organisation. Une fois la mise à jour effectuée, arrêtez et démarrez le service à l’aide des commandes **net stop AzLog** et **net start AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Comment puis-je consulter les informations d’abonnement dans des événements Windows ?
Ajoutez l’ID d’abonnement au nom convivial lors de l’ajout de la source :

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
L’XML de l’événement a les métadonnées suivantes, notamment l’ID d’abonnement :

![XML de l’événement][1]

## <a name="error-messages"></a>messages d'erreur
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Pourquoi le message d’erreur suivant s’affiche-t-il quand j’exécute la commande ```AzLog createazureid``` ?
Error:

  *Échec de l’application AAD - Locataire 72f988bf-86f1-41af-91ab-2d7cd011db37 - Raison = « Interdit » - Message = « Privilèges insuffisants pour terminer l’opération ». (Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = ’Forbidden’ - Message = ’Insufficient privileges to complete the operation.’)*

La commande **Azlog createazureid** tente de créer un principal du service dans tous les locataires Azure AD pour les abonnements auxquels la connexion Azure a accès. Si votre connexion Azure n'a que le rôle d’Invité dans ce locataire Azure AD, la commande échoue avec « Privilèges insuffisants pour terminer l’opération ». Demandez à l’administrateur du locataire d’ajouter votre compte en tant qu’utilisateur dans le locataire.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Lors de l’exécution de la commande **azlog authorize**, pourquoi le message d’erreur suivant est-il généré ?
Error:

  *Avertissement à la création d’attribution de rôle : AuthorizationFailed : le client janedo@microsoft.com’ avec l’ID d’objet « fe9e03e4-4dad-4328-910f-fd24a9660bd2 » n’est pas autorisé à effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur « / subscriptions/70d95299-d689-4c97-b971-0d8ff0000000 ».*

La commande **Azlog authorize** attribue le rôle de lecteur au principal du service Azure AD (créé avec **Azlog createazureid**) dans les abonnements fournis. Si la connexion Azure n’a pas le rôle Coadministrateur ou un Propriétaire de l’abonnement, elle échoue avec le message d’erreur « Échec de l’autorisation ». Le contrôle d’accès en fonction du rôle (RBAC) d’Azure du Coadministrateur ou du Propriétaire est nécessaire pour effectuer cette action.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Où puis-je trouver la définition des propriétés dans le journal d’audit ?
Consultez l'article :

* [Opérations d’audit avec Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Liste des événements de gestion dans un abonnement dans l’API REST Azure Monitor (en anglais)](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Où puis-je trouver plus d’informations sur les alertes de l’Azure Security Center ?
Consultez la rubrique [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Comment puis-je modifier les données collectées avec les diagnostics de la machine virtuelle ?
Pour plus d’informations sur la manière d’obtenir, modifier et définir la configuration des diagnostics Azure, consultez la page [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

L’exemple suivant obtient la configuration des diagnostics Azure :

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

L’exemple suivant modifie la configuration des diagnostics Azure. Dans cette configuration, seuls les ID d’événement 4624 et 4625 sont collectés dans le journal des événements de sécurité. Des événements Microsoft Antimalware pour Azure sont collectés dans le journal des événements système. Pour plus d’informations sur l’utilisation d’expressions XPath, consultez [Consommation d’événements](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

L’exemple suivant définit la configuration des diagnostics Azure :

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Après avoir apporté les modifications, vérifiez le compte de stockage pour vous assurer que les événements corrects sont collectés.

Si vous rencontrez des problèmes pendant l’installation et la configuration, ouvrez une [demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pour ce faire, sélectionnez **Intégration du journal** comme service pour lequel vous demandez de l’aide.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Puis-je utiliser l’intégration des journaux Azure pour intégrer des journaux Network Watcher dans mon SIEM ?

Azure Network Watcher génère de grandes quantités d’informations de journalisation. Ces journaux ne sont pas destinés à être envoyés à un serveur SIEM. La seule destination prise en charge pour les journaux Network Watcher est un compte de stockage. L’intégration des journaux Azure ne prend pas en charge la lecture de ces journaux et leur mise à disposition d’un serveur SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
