---
title: "Questions fréquentes (FAQ) concernant les problèmes de configuration et de gestion pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur la configuration et la gestion pour Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: genli
ms.openlocfilehash: 2a20ee1df23df683c49444e8fb3ffdb2085b174f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de configuration et de gestion pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de configuration et de gestion pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Comment ajouter des directives « nosniff » à mon site web ?
Pour empêcher les clients de détecter les types MIME, ajoutez un paramètre au fichier *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Vous pouvez également ajouter ce paramètre dans IIS. Utilisez la commande suivante avec l’article [tâches courantes de démarrage](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Comment personnaliser IIS pour un rôle web ?
Utilisez le script de démarrage IIS à partir de l’article [tâches courantes de démarrage](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="i-cannot-scale-beyond-x-instances"></a>Je ne parviens pas à mettre à l’échelle au-delà de X instances
Le nombre de cœurs que vous pouvez utiliser est limité dans votre abonnement Azure. La mise à l’échelle ne fonctionnera pas si vous avez utilisé tous les cœurs disponibles. Par exemple, si vous avez une limite de 100 cœurs, cela signifie que vous pouvez avoir 100 instances de machine virtuelle A1 pour votre service cloud ou 50 instances de machine virtuelle A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Comment implémenter un accès en fonction du rôle pour les services cloud ?
Les services cloud ne prennent pas en charge le modèle de contrôle d’accès en fonction du rôle (RBAC), car il ne s’agit pas d’un service Azure Resource Manager.

Consultez [Comparaison entre RBAC Azure et les administrateur d’abonnements classiques](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Comment définir le délai d’inactivité d’Azure Load Balancer ?
Vous pouvez spécifier le délai d’expiration dans votre fichier de définition de service (csdef) comme ceci :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Pour plus d’informations, consultez [Nouveau : délai d’inactivité configurable pour Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/).

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Les ingénieurs internes Microsoft peuvent-ils se connecter à des instances de service cloud sans autorisation via RDP ?
Microsoft suit un processus strict qui ne permet pas à ses ingénieurs internes de se connecter à votre service cloud via RDP sans une autorisation écrite (par courrier électronique ou toute autre communication écrite) de la part du propriétaire ou de son représentant.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Pourquoi la chaîne de certificats du certificat SSL de mon service cloud est-elle incomplète ?
Nous recommandons aux clients d’installer la chaîne de certificats complète (certificat feuille, certificats intermédiaires et certificat racine) au lieu du seul certificat feuille. Quand vous installez uniquement le certificat feuille, vous faites confiance à Windows pour générer la chaîne de certificats en parcourant la liste CTL. Si Azure ou Windows Update rencontre des problèmes réseau ou DNS intermittents pendant que Windows tente de valider le certificat, celui-ci peut être considéré comme non valide. En installant la chaîne de certificats complète, ce problème peut être évité. L’article du blog intitulé [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) (Comment installer un certificat SSL chaîné) explique comment effectuer cette opération.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Comment associer une adresse IP statique à mon service cloud ?
Pour configurer une adresse IP statique, vous devez créer une adresse IP réservée. Cette adresse IP réservée peut être associée à un nouveau service cloud ou à un déploiement existant. Pour plus d’informations, consultez les documents suivants :
* [Comment créer une adresse IP réservée](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Réserver l’adresse IP d’un service cloud existant](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associer une adresse IP réservée à un nouveau service cloud](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associer une adresse IP réservée à un déploiement en cours d’exécution](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration de service](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Quelle est la limite de quota de mon service cloud ?
Consultez [Limites spécifiques des services](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Pourquoi le lecteur de la machine virtuelle de mon service cloud dispose-t-il d’aussi peu d’espace disque ?
Il s’agit d’un comportement normal qui ne devrait pas causer de problèmes à votre application. La journalisation est activée pour le lecteur % des machines virtuelles Azure PaaS, ce qui dans l’absolu a pour effet de consommer normalement le double d’espace que les fichiers. Cependant, cela ne pose pas de problème, et ce pour plusieurs raisons.

La taille du lecteur %approot% est calculée selon la formule <taille du fichier .cspkg + taille maximale du journal > + marge d’espace libre> ou est égale à 1,5 Go, la valeur la plus grande étant retenue. La taille de votre machine virtuelle n’a pas d’incidence sur ce calcul. (La taille de machine virtuelle affecte uniquement la taille du lecteur C: temporaire.) 

L’écriture sur le lecteur %approot% n’est pas prise en charge. Si vous écrivez sur la machine virtuelle Azure, vous devez le faire dans une ressource LocalStorage temporaire (ou une autre option, telle que le Stockage Blob, Azure Files, etc..). Autrement dit, la quantité d’espace libre dans le dossier %approot % n’est pas significative. Pour savoir avec certitude si votre application écrit ou non sur le lecteur %approot%, vous pouvez toujours laisser votre service s’exécuter pendant quelques jours et comparer sa taille avant et après. 

Azure n’écrit rien sur le lecteur %approot%. Une fois le disque dur virtuel créé à partir de votre fichier .cspkg et monté dans la machine virtuelle Azure, votre application est la seule à pouvoir écrire sur ce lecteur. 

Les paramètres de journal n’étant pas configurables, vous ne pouvez pas les désactiver.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quelles fonctionnalités et capacités IPS/ID et DDOS de base trouve-t-on sur Azure ?
Azure propose des fonctionnalités IPS/IDS sur les serveurs physiques des centres de données pour assurer une protection contre les menaces. Par ailleurs, les clients peuvent déployer des solutions de sécurité tierces, telles que des pare-feu d’applications web, des pare-feu réseau, des logiciels anti-programmes malveillants, des systèmes de détection et de prévention des intrusions (IDS/IPS), etc. Pour plus d’informations, consultez [Protégez vos données et vos biens en respectant les normes internationales de sécurité](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft surveille en continu les serveurs, les réseaux et les applications pour détecter les menaces. L’approche concertée de la gestion des menaces d’Azure utilise également la détection d’intrusion, la prévention des attaques par déni de service distribué (DDoS), le test de pénétration, l’analytique des comportements, la détection d’anomalies et le Machine Learning pour renforcer constamment ses défenses et réduire les risques. Microsoft Antimalware pour Azure protège les services cloud et les machines virtuelles Azure. Vous avez la possibilité de déployer des solutions de sécurité tierces supplémentaires, telles que des pare-feu d’applications web, des pare-feu réseau, des logiciels anti-programmes malveillants, des systèmes de détection et de prévention des intrusions (IDS/IPS), etc.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Pourquoi IIS cesse-t-il d’écrire dans le répertoire des journaux ?
Vous avez épuisé le quota de stockage local réservé à l’écriture dans le répertoire des journaux. Pour corriger ce problème, vous avez trois solutions :
* Activez les diagnostics pour IIS et déplacez régulièrement les diagnostics sur le Stockage Blob.
* Supprimez manuellement les fichiers journaux du répertoire de journalisation.
* Augmentez la limite de quota pour les ressources locales.

Pour plus d’informations, consultez les documents suivants :
* [Stocker et afficher des données de diagnostic dans le stockage Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Le service Journaux IIS cesse d’écrire dans le service cloud](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>À quoi sert le « certificat de chiffrement Windows Azure Tools pour les extensions » ?
Ces certificats sont créés automatiquement chaque fois qu’une extension est ajoutée au service cloud. Il s’agit le plus souvent de l’extension WAD ou RDP, mais il peut s’agit d’autres extensions, notamment Antimalware ou Log Collector. Ces certificats sont utilisés uniquement pour chiffrer et déchiffrer la configuration privée de l’extension. Dans la mesure où la date d’expiration n’est jamais vérifiée, l’expiration du certificat ne pose pas de problème. 

Vous pouvez ignorer ces certificats. Si vous voulez nettoyer les certificats, vous pouvez essayer de tous les supprimer. Azure génère une erreur si vous essayez de supprimer un certificat en cours d’utilisation.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Comment générer une demande de signature de certificat sans se connecter à l’instance via le protocole RDP ?
Consultez le document d’instructions suivant :

>[Obtention d’un certificat pour une utilisation avec Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Notez qu’une demande de signature de certificat consiste simplement en un fichier texte. Il N’EST PAS nécessaire de le créer à partir de la machine sur laquelle le certificat est finalement utilisé. Même si ce document est écrit pour un service d’application (App Service), la création d’une demande de signature de certificat est générique et s’applique aussi aux services cloud.

## <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Comment puis-je ajouter une extension Antimalware de manière automatisée pour mes services cloud ?

Vous pouvez activer une extension Antimalware à l’aide du script PowerShell dans la tâche de démarrage. Suivez les étapes décrites dans les articles suivants pour l’implémenter : 
 
- [Créer une tâche de démarrage PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Pour plus d’informations sur les scénarios de déploiement Antimalware et la façon de l’activer à partir du portail, consultez [Scénarios de déploiement Antimalware](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

## <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Comment activer l’Indication du nom de serveur (SNI) pour les services cloud ?

Vous pouvez activer SNI dans les services cloud en utilisant l’une des méthodes suivantes :

### <a name="method-1-use-powershell"></a>Méthode 1 : Via PowerShell

La liaison SNI peut être configurée à l’aide de la cmdlet PowerShell **New-WebBinding** dans une tâche de démarrage pour une instance de rôle de service cloud comme indiqué ci-dessous :
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Comme décrit [ici](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags peut adopter l’une des valeurs suivantes :

|Valeur|Signification|
------|------
|0|Pas de SNI|
|1|SNI activée |
|2 |Liaison non SNI qui utilise le magasin de certificats central|
|3|Liaison SNI qui utilise le magasin de certificats central |
 
### <a name="method-2-use-code"></a>Méthode 2 : Via code

La liaison SNI peut également être configurée via code au cours du démarrage de rôle comme décrit dans ce [billet de blog](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/) :

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Si vous utilisez l’une des approches ci-dessus, les certificats respectifs (*.pfx) pour les noms d’hôte spécifiques doivent être installés au préalable sur les instances de rôle à l’aide d’une tâche de démarrage ou via code pour activer la liaison SNI.

## <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Comment puis-je ajouter des balises à mon service cloud Azure ? 

Le service cloud est une ressource classique. Seules les ressources créées via Azure Resource Manager prennent en charge les balises. Vous ne pouvez pas appliquer de balises à des ressources classiques comme un service cloud. 

## <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Quelles prochaines fonctionnalités Service cloud dans le portail Azure peuvent aider à gérer et surveiller des applications ?

* La possibilité de générer un nouveau certificat de protocole RDP (Remote Desktop Protocol) est prévue prochainement. Vous pouvez également exécuter ce script :

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
* La possibilité de choisir l’option « blob » ou « local » pour votre emplacement de chargement csdef et cscfg est prévue prochainement. À l’aide de [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), vous pouvez définir chaque valeur d’emplacement.
* La possibilité de surveiller les métriques au niveau de l’instance. Des fonctionnalités de surveillance supplémentaires sont disponibles dans [Surveillance de Services cloud](cloud-services-how-to-monitor.md).


## <a name="how-to-enable-http2-on-cloud-services-vm"></a>Comment activer HTTP/2 sur la machine virtuelle de services cloud ?

Windows 10 et Windows Server 2016 prennent en charge HTTP/2 à la fois côté client et côté serveur. Si votre client (navigateur) se connecte au serveur IIS sur TLS qui négocie HTTP/2 via les extensions TLS, vous n’avez pas besoin d’effectuer de modifications côté serveur. En effet, sur TLS, l’en-tête h2-14 spécifiant l’utilisation de HTTP/2 est envoyé par défaut. En revanche, si votre client envoie un en-tête de mise à niveau pour mettre à niveau vers HTTP/2, vous devez effectuer la modification ci-dessous côté serveur pour garantir la correcte mise à niveau et l’établissement d’une connexion HTTP/2. 

1. Exécutez regedit.exe.
2. Accédez à la clé de Registre : HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Créez une valeur DWORD nommée **DuoEnabled**.
4. Définissez-la sur 1.
5. Redémarrez votre serveur.
6. Accédez à votre **site web par défaut** et sous **Liaisons**, créez une liaison TLS avec le certificat auto-signé qui vient d’être créé. 

Pour plus d'informations, consultez les pages suivantes :

- [HTTP/2 sur IIS](https://blogs.iis.net/davidso/http2)
- [Vidéo : HTTP/2 dans Windows 10 : navigateur, applications et serveur web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Notez que les étapes ci-dessus peuvent être automatisées via une tâche de démarrage de manière à pouvoir effectuer les modifications ci-dessus dans le Registre système chaque fois qu’une nouvelle instance PaaS est créée. Pour plus d’informations, consultez [Comment configurer et exécuter des tâches de démarrage pour un service cloud](cloud-services-startup-tasks.md).

 
Une fois terminé, vous pouvez vérifier si la connexion HTTP/2 a été activée ou non en utilisant l’une des méthodes suivantes :

- Activez la version de protocole dans les journaux IIS et consultez ces derniers. Vous y trouverez les informations sur HTTP/2. 
- Activez l’outil de développement F12 dans Internet Explorer/Edge et basculez vers l’onglet Réseau pour vérifier le protocole. 

Pour plus d’informations, consultez [HTTP/2 sur IIS](https://blogs.iis.net/davidso/http2).
