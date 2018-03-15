---
title: "Installation d’une passerelle de données locale - Azure Logic Apps | Microsoft Docs"
description: "Avant d’accéder à des sources de données locales, installez la passerelle de données locale pour accélérer le transfert et le chiffrement de données entre les sources de données locales et les applications logiques."
keywords: "accéder aux données, localement, transfert de données, chiffrement, sources de données"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: e061f24f3160de82548c4debf6da5821318ad2fb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Installer la passerelle de données locale pour Azure Logic Apps

Pour que vos applications logiques puissent accéder à des sources de données locales, vous devez installer et configurer la passerelle de données locale. La passerelle agit comme un pont permettant un transfert et un chiffrement de données rapides entre les systèmes locaux et vos applications logiques. La passerelle relaie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. Pour en savoir plus, voir [Fonctionnement de la passerelle de données](#gateway-cloud-service).

La passerelle prend en charge les connexions aux sources de données locales suivantes :

*   BizTalk Server 2016
*   DB2  
*   Système de fichiers
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*   SharePoint
*   SQL Server
*   Teradata

Ces étapes montrent comment installer la passerelle de données locale avant de [configurer une connexion entre la passerelle et vos applications logiques](./logic-apps-gateway-connection.md). Pour plus d’informations sur les connecteurs pris en charge, voir [Connecteurs pour Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list). 

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

*   [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Configuration requise

**Minimum**

* .NET Framework 4.5
* Version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

**Recommended**

* Processeur 8 cœurs
* 8 Go de mémoire
* Version 64 bits de Windows 2012 R2 (ou version ultérieure)

**Points importants à prendre en compte**

* Installez la passerelle de données locale sur un seul ordinateur local.
Vous ne pouvez pas installer la passerelle sur un contrôleur de domaine.

   > [!TIP]
   > Vous ne devez pas nécessairement installer la passerelle sur le même ordinateur que votre source de données. Pour réduire la latence, vous pouvez installer la passerelle le plus près possible de votre source de données, ou sur le même ordinateur, en supposant que vous disposiez des autorisations nécessaires.

* N’installez pas la passerelle sur un ordinateur qui s’éteint, passe en mode veille ou ne se connecte pas à Internet, car la passerelle ne peut pas s’exécuter dans de telles circonstances. De même, les performances de la passerelle peuvent être moindres sur un réseau sans fil.

* Pendant l’installation, vous devez vous connecter avec un [compte professionnel ou scolaire](https://docs.microsoft.com/azure/active-directory/sign-up-organization) géré par Azure Active Directory (Azure AD), et non un compte Microsoft.

  > [!TIP]
  > Si vous souhaitez utiliser un compte Microsoft qui possède un abonnement Visual Studio avec MSDN, commencez par [créer un répertoire (client) dans Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) avec votre compte Microsoft, ou utilisez le répertoire par défaut. Ajoutez un utilisateur avec un mot de passe au répertoire, puis donnez-lui accès à votre abonnement. Vous pourrez alors vous connecter au cours de l’installation de la passerelle avec ce nom d’utilisateur et ce mot de passe.

  Vous devez utiliser le même compte professionnel ou scolaire par la suite dans le portail Azure lorsque vous créez et associez une ressource de passerelle à votre installation de passerelle. Vous sélectionnerez ensuite cette ressource de passerelle lors de la création de la connexion entre votre application logique et la source de données locale. [Pourquoi dois-je utiliser un compte professionnel ou scolaire Azure AD ?](#why-azure-work-school-account)

  > [!TIP]
  > Si vous avez souscrit une offre Office 365 sans fournir votre adresse de messagerie professionnelle réelle, votre adresse de connexion peut ressembler à ceci : jeff@contoso.onmicrosoft.com. 

* Si vous disposez d’une passerelle existante que vous configurez avec un programme d’installation antérieur à la version 14.16.6317.4, vous ne pouvez pas modifier l’emplacement de votre passerelle en exécutant le programme d’installation le plus récent. Toutefois, vous pouvez utiliser le programme d’installation le plus récent pour configurer une nouvelle passerelle avec l’emplacement que vous souhaitez à la place.
  
  Si vous avez un programme d’installation de passerelle antérieur à la version 14.16.6317.4, mais n’avez pas encore installé votre passerelle, vous pouvez télécharger et utiliser le programme d’installation le plus récent.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Installer la passerelle de données

1. [Téléchargez et exécutez le programme d’installation de passerelle sur un ordinateur local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Lisez et acceptez les conditions d’utilisation et la déclaration de confidentialité.

3. Spécifiez le chemin d’accès sur votre ordinateur local où vous souhaitez installer la passerelle.

4. Lorsque vous y êtes invité, connectez-vous à votre compte Azure professionnel ou scolaire, pas à un compte Microsoft.

   ![Connexion avec le compte professionnel ou scolaire Azure](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Inscrivez maintenant la passerelle installée auprès du [service cloud de passerelle](#gateway-cloud-service). Choisissez **Inscrivez une nouvelle passerelle sur cet ordinateur**.

   Le service cloud de passerelle chiffre et stocke les informations d’identification de votre source de données et les détails de la passerelle. 
   Le service achemine également les requêtes et leurs résultats entre votre application logique, la passerelle de données locale et votre source de données locale.

6. Entrez un nom pour votre installation de passerelle. Créez une clé de récupération, puis confirmez votre clé de récupération. 

   > [!IMPORTANT] 
   > Votre clé de récupération doit contenir au moins huit caractères. Veillez à enregistrer et à conserver la clé en lieu sûr. Vous avez également besoin de cette clé pour migrer, restaurer ou contrôler une passerelle existante.

   1. Pour modifier la région par défaut pour le service cloud de passerelle et l’Azure Service Bus utilisé par votre installation de passerelle, choisissez **Changer la région**.

      ![Modification de la région](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      La région par défaut est la région associée à votre client Azure AD.

   2. Dans le volet suivant, cliquez sur la liste déroulante **Sélectionnez une région** pour choisir une autre région.

      ![Sélection d’une autre région](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Par exemple, vous pouvez sélectionner la même région que celle de votre application logique, ou sélectionner la région la plus proche de votre source de données locale afin de réduire la latence. Votre ressource de passerelle et votre application logique se trouvent dans des emplacements différents.

      > [!IMPORTANT]
      > Vous ne pouvez pas modifier cette région après l’installation. Cette région détermine et restreint également l’emplacement où vous pouvez créer la ressource Azure pour votre passerelle. Par conséquent, lorsque vous créez votre ressource de passerelle dans Azure, assurez-vous que l’emplacement de la ressource correspond à la région que vous avez sélectionnée lors de l’installation de la passerelle.
      > 
      > Si vous souhaitez utiliser une autre région pour votre passerelle ultérieurement, vous devrez configurer une nouvelle passerelle.

   3. Une fois ces opérations effectuées, sélectionnez **Terminé**.

7. Suivez à présent ces étapes dans le portail Azure pour [créer une ressource Azure pour votre passerelle](../logic-apps/logic-apps-gateway-connection.md). 

Pour en savoir plus, voir [Fonctionnement de la passerelle de données](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migrer, restaurer ou contrôler une passerelle existante

Pour effectuer ces tâches, vous devez disposer de la clé de récupération spécifiée lors de l’installation de la passerelle.

1. Dans le menu Démarrer de votre ordinateur, choisissez **Passerelle de données locale**.

2. Une fois le programme d’installation ouvert, connectez-vous avec le compte professionnel ou scolaire utilisé précédemment pour installer la passerelle.

3. Choisissez **Migrer, restaurer ou contrôler une passerelle existante**.

4. Fournissez le nom et la clé de récupération de la passerelle que vous souhaitez migrer, restaurer ou contrôler.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Compte de service Windows

La passerelle de données locale fonctionne comme un service Windows ; elle est configurée pour utiliser `NT SERVICE\PBIEgwService` comme informations d’identification pour la connexion au service Windows. Par défaut, la passerelle dispose du droit « Ouvrir une session en tant que service » sur l’ordinateur sur lequel vous installez la passerelle. Pour créer et gérer la passerelle sur le Portail Azure, le compte de service Windows doit avoir au moins les autorisations **Collaborateur**. 

> [!NOTE]
> Le compte de service Windows diffère du compte utilisé pour la connexion à des sources de données locales, et du compte professionnel ou scolaire Azure utilisé pour se connecter aux services cloud.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Redémarrez la passerelle

Comme tout autre service Windows, vous pouvez démarrer et arrêter ce service de plusieurs façons. Par exemple, vous pouvez ouvrir une invite de commandes avec des autorisations élevées sur l’ordinateur sur lequel la passerelle s’exécute, puis exécuter l’une des commandes suivantes :

* Pour arrêter le service, exécutez cette commande :
  
    `net stop PBIEgwService`

* Pour démarrer le service, exécutez cette commande :
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Configuration d’un pare-feu ou d’un proxy

La passerelle crée une connexion sortante vers [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Pour spécifier les informations proxy pour votre passerelle, consultez [Configurer les paramètres proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Pour vérifier si votre pare-feu ou proxy risque de bloquer des connexions, vérifiez que votre ordinateur peut réellement se connecter à Internet et à [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). À partir d’une invite PowerShell, exécutez la commande suivante :

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Cette commande teste uniquement la connectivité réseau et la connectivité à Azure Service Bus. Par conséquent, la commande n’a rien à voir avec la passerelle ou le service cloud de passerelle qui chiffre et stocke vos informations d’identification et les détails de la passerelle. 
>
> Par ailleurs, cette commande est disponible uniquement sur Windows Server 2012 R2 ou version ultérieure, et sur Windows 8.1 ou version ultérieure. Dans les versions de système d’exploitation antérieures, vous pouvez utiliser Telnet pour tester la connectivité. Pour en savoir plus, voir [Solutions Azure Service Bus et hybrides](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Les résultats devraient être similaires à cet exemple :

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si **TcpTestSucceeded** n’est pas défini sur **true**, il se peut que vous soyez bloqué par un pare-feu. Pour être exhaustif, remplacez les valeurs **ComputerName** et **Port** par celles répertoriées dans la section [Configuration des ports](#configure-ports) de cet article.

Le pare-feu peut également bloquer des connexions qu’Azure Service Bus tente d’établir avec des centres de données Microsoft Azure. Si ce scénario se produit, approuvez (débloquez) toutes les adresses IP de ces centres de données dans votre région. Pour ces adresses IP, vous pouvez [obtenir la liste des adresses IP Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configuration des ports

La passerelle crée une connexion sortante vers [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) et communique sur les ports de sortie suivants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354. La passerelle ne nécessite pas de ports entrants. Pour en savoir plus, voir [Solutions Azure Service Bus et hybrides](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| Noms de domaine | Ports sortants | Description |
| ------------ | -------------- | ----------- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Permet de tester la connectivité Internet lorsque la passerelle est inaccessible par le service Power BI. | 
||||

Si vous devez mettre sur liste approuvée des adresses IP au lieu des domaines, vous pouvez télécharger et utiliser la [liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dans certains cas, les connexions Azure Service Bus s’effectuent avec l’adresse IP plutôt qu’avec le nom de domaine complet.

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-data-gateway-work"></a>Fonctionnement de la passerelle de données

La passerelle de données assure une communication rapide et sécurisée entre votre application logique, le service cloud de passerelle et votre source de données locale. 

![diagramme de flux de passerelle de données locale](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ainsi, quand l’utilisateur dans le cloud interagit avec un élément connecté à votre source de données locale :

1. Le service cloud de passerelle crée une requête, ainsi que les informations d’identification chiffrées pour la source de données, puis envoie la requête vers la file d’attente afin que la passerelle la traite.

2. Le service cloud de passerelle analyse la requête et envoie les demandes à Azure Service Bus.

3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.

4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte à la source de données avec ces informations d’identification.

5. La passerelle envoie la requête à la source de données pour exécution.

6. Les résultats sont renvoyés de la source de données vers la passerelle, puis au service cloud de passerelle. Le service cloud de passerelle utilise ensuite les résultats.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="general"></a>Généralités

**Q**: Ai-je besoin d’une passerelle pour des sources de données dans le cloud telles que SQL Azure ? <br/>
**R** : Non. Une passerelle se connecte uniquement aux sources de données locales.

**Q** : La passerelle doit-elle être installée sur le même ordinateur que la source de données ? <br/>
**R** : Non. La passerelle se connecte à la source de données en utilisant les informations de connexion fournies. En ce sens, considérez la passerelle comme une application cliente. La passerelle doit juste être en mesure de se connecter au nom du serveur qui a été fourni.

<a name="why-azure-work-school-account"></a>

**Q** : Pourquoi dois-je utiliser un compte professionnel ou scolaire Azure pour me connecter ? <br/>
**R** : Lorsque vous installez la passerelle de données locale, vous pouvez uniquement utiliser un compte professionnel ou scolaire Azure. Votre compte de connexion est stocké dans un client géré par Azure Active Directory (Azure AD). En règle générale, le nom d’utilisateur principal (UPN) de votre compte Azure AD correspond à l’adresse de messagerie.

**Q** : où mes informations d’identification sont-elles stockées ? <br/>
**R** : Les informations d’identification que vous entrez pour une source de données sont chiffrées et stockées dans le service cloud de passerelle. Les informations d’identification sont déchiffrées au niveau de la passerelle de données locale.

**Q** : Existe-t-il des exigences concernant la bande passante réseau ? <br/>
**R** : Nous vous recommandons d’utiliser une connexion réseau offrant un bon débit. Chaque environnement est différent et la quantité de données envoyées affecte les résultats. ExpressRoute peut vous aider à garantir un niveau de débit approprié entre les centres de données locaux et les centres de données Azure.
Vous pouvez utiliser l’application tierce Azure Speed Test pour mesurer votre débit.

**Q** : Quelle est la latence d’exécution des requêtes adressées à une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**R** : Pour réduire la latence du réseau, installez la passerelle le plus près possible de la source de données. Si vous pouvez installer la passerelle sur la source de données réelle, cette proximité réduit le temps de latence. Songez également aux centres de données. Par exemple, si votre service utilise le centre de données États-Unis de l’Ouest et que SQL Server est hébergé sur une machine virtuelle Azure, votre machine virtuelle Azure doit également se situer dans les États-Unis de l’Ouest. Cette proximité réduit la latence et évite des frais d’acheminement sur la machine virtuelle Azure.

**Q** : Comment les résultats sont-ils renvoyés vers le cloud ? <br/>
**R** : Les résultats sont envoyés via Azure Service Bus.

**Q** : existe-t-il des connexions entrantes vers la passerelle à partir du cloud ? <br/>
**R** : Non. La passerelle utilise des connexions sortantes vers Azure Service Bus.

**Q** : Que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**R** : Vérifiez les ports et les hôtes que la passerelle utilise.

**Q** : Comment s’appelle le service Windows réel ?<br/>
**R** : Dans Services, la passerelle est appelée Service Enterprise Gateway Power BI.

**Q** : Le service Windows de passerelle peut-il s’exécuter avec un compte Azure Active Directory ? <br/>
**R** : Non. Le service Windows doit avoir un compte Windows valide. Par défaut, le service sera exécuté avec le SID du service, NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence

**Q** : Quelles sont les options de récupération d’urgence disponibles ? <br/>
**R**: Vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.

**Q** : Quel avantage la clé de récupération offre-t-elle ? <br/>
**R** : La clé de récupération permet de migrer ou de récupérer les paramètres de votre passerelle en cas de récupération d’urgence.

**Q** : Existe-t-il des plans pour activer des scénarios de haute disponibilité avec la passerelle ? <br/>
**R**: Certains connecteurs prennent en charge les scénarios de haute disponibilité, tels que le connecteur Système de fichiers et d’autres. Pour plus d’informations, consultez [Clusters à haute disponibilité pour la passerelle de données locale](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

## <a name="troubleshooting"></a>Résolution de problèmes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q** : Comment puis-je voir les requêtes qui sont envoyées à la source de données locale ? <br/>
**R** : Vous pouvez activer le traçage de requête qui inclut les requêtes envoyées. N’oubliez pas de rétablir la valeur d’origine du traçage des requêtes une fois les problèmes résolus. Le fait de laisser activé le traçage des requêtes contribue à augmenter la taille des journaux.

Vous pouvez également utiliser les outils de suivi des requêtes proposés par votre source de données. Par exemple, vous pouvez utiliser Extended Events ou SQL Profiler for SQL Server et Analysis Services.

**Q** : Où se situent les journaux de la passerelle ? <br/>
**R** : Consultez la section Outils plus loin dans cet article.

### <a name="update-to-the-latest-version"></a>Mise à jour avec la version la plus récente

Beaucoup de problèmes peuvent survenir si la version de la passerelle est obsolète. En règle générale, veillez toujours à utiliser la version la plus récente. Si vous n’avez pas mis à jour la passerelle depuis un mois ou plus, vous pouvez installer la dernière version de la passerelle pour vérifier si le problème se reproduit.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erreur : Impossible d’ajouter l’utilisateur au groupe. (-2147463168 PBIEgwService Performance Log Users)

Cette erreur peut apparaître si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Veillez à déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

## <a name="tools"></a>Outils

### <a name="collect-logs-from-the-gateway-configurer"></a>Collecter des journaux à partir de l’outil de configuration de passerelle

Vous pouvez recueillir plusieurs journaux pour la passerelle. Commencez toujours par consulter les journaux !

#### <a name="installer-logs"></a>Journaux du programme d’installation

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Journaux de configuration

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Journaux du service de passerelle Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Journaux d’événements

Les journaux de passerelle de gestion des données et PowerBIGateway figurent sous **Journaux des applications et services**.

### <a name="fiddler-trace"></a>Trace Fiddler

[Fiddler](http://www.telerik.com/fiddler) est un outil gratuit développé par Telerik, qui surveille le trafic HTTP. Il permet de visualiser le trafic entre le service Power BI et l’ordinateur client. Ce service peut également indiquer les éventuelles erreurs et autres informations connexes.

## <a name="next-steps"></a>Étapes suivantes
    
* [Connexion à des données locales à partir d’applications logiques](../logic-apps/logic-apps-gateway-connection.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
