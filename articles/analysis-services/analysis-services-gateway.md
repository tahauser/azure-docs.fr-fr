---
title: "Passerelle de données locale | Microsoft Docs"
description: "Une passerelle locale est nécessaire si votre serveur Analysis Services dans Azure se connecte à des sources de données locales."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/02/2018
ms.author: owend
ms.openlocfilehash: a0af2e0448d8ce991c9bcc138d6132d216715768
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Connexion aux sources de données locales avec la passerelle de données Azure locale
La passerelle de données locale agit comme un pont, en fournissant un transfert de données sécurisé entre des sources de données locales et vos serveurs Azure Analysis Services dans le cloud. Pouvant être utilisée avec plusieurs serveurs Azure Analysis Services dans la même région, la dernière version de la passerelle fonctionne également avec Azure Logic Apps, Power BI, Power Apps et Microsoft Flow. Vous pouvez associer plusieurs services dans la même région avec une passerelle unique. 

La première configuration de la passerelle se déroule en quatre étapes :

- **Télécharger et exécuter le programme d’installation** - Cette étape installe un service de passerelle sur un ordinateur de votre organisation. Vous vous connectez également à Azure à l’aide d’un compte du Azure AD de votre [locataire](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Les comptes Azure B2B (invité) ne sont pas pris en charge.

- **Inscrire votre passerelle** - Lors de cette étape, vous spécifiez un nom et une clé de récupération pour votre passerelle et vous sélectionnez une région, pour l’inscription de votre passerelle auprès du service cloud de passerelle. Votre ressource de passerelle peut être inscrite dans n’importe quelle région, mais nous vous recommandons de l’inscrire dans la même région que vos serveurs Analysis Services. 

- **Créer une ressource de passerelle dans Azure** - Lors de cette étape, vous créez une ressource de passerelle dans votre abonnement Azure.

- **Connecter vos serveurs à vos ressources de passerelle** - Une fois que vous avez une ressource de passerelle dans votre abonnement, vous pouvez commencer à y connecter vos serveurs. Vous pouvez y connecter plusieurs serveurs et d’autres ressources.

Pour commencer immédiatement, consultez la page [Install and configure on-premises data gateway](analysis-services-gateway-install.md) (Installer et configurer la passerelle de données locale).

## <a name="how-it-works"></a>Fonctionnement
La passerelle que vous installez sur un ordinateur de votre organisation s’exécute comme un service Windows, **Passerelle de données locale**. Ce service local est inscrit auprès du service cloud de passerelle via Azure Service Bus. Vous créez ensuite la ressource de passerelle correspondante pour votre abonnement Azure. Vos serveurs Azure Analysis Services sont alors connectés à vos ressources de passerelle. Lorsque des modèles sur votre serveur doivent se connecter à vos sources de données locales pour des requêtes ou un traitement, un flux de données et de requête parcourt la ressource de passerelle, Azure Service Bus, le service de passerelle de données locale et vos sources de données. 

![Fonctionnement](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Requêtes et flux de données :

1. Une requête est créée par le service cloud avec les informations d’identification chiffrées de la source de données locale. Elle est ensuite envoyée dans une file d’attente pour être traitée par la passerelle.
2. Le service cloud de la passerelle analyse la requête et envoie la requête vers [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.
4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte aux sources de données avec ces informations d’identification.
5. La passerelle envoie la requête à la source de données pour exécution.
6. Les résultats sont renvoyés de la source de données vers la passerelle, puis vers le service cloud et votre serveur.

## <a name="windows-service-account"></a>Compte de service Windows
La passerelle de données locale est configurée afin d’utiliser *NT SERVICE\PBIEgwService* pour les informations d’identification d’ouverture de session du service Windows. Par défaut, elle dispose du droit d’ouverture de session en tant que service ; dans le contexte de l’ordinateur sur lequel vous installez la passerelle. Ces informations d’identification ne correspondent pas au même compte que celui utilisé pour se connecter aux sources de données locales ou à votre compte Azure.  

Si vous rencontrez des problèmes avec votre serveur proxy en raison de l’authentification, vous souhaiterez peut-être modifier le compte de service Windows sur un utilisateur de domaine ou un compte de service géré.

## <a name="ports"></a>Ports
La passerelle crée une connexion sortante vers Azure Service Bus. Elle communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354.  La passerelle ne nécessite pas de ports entrants.

Nous vous recommandons d’autoriser les adresses IP pour votre région de données dans votre pare-feu. Vous pouvez télécharger la [liste d’adresses IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Cette liste est actualisée chaque semaine.

> [!NOTE]
> Les adresses IP répertoriées dans la liste d’adresses IP de centre de données Azure sont en notation CIDR. Par exemple, 10.0.0.0/24 ne signifie pas 10.0.0.0 à 10.0.0.24. En savoir plus sur la [notation CIDR](http://whatismyipaddress.com/cidr).
>
>

Voici les noms de domaine complets utilisés par la passerelle.

| Noms de domaine | Ports sortants | Description |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP utilisé pour télécharger le programme d’installation. |
| *. powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Permet de tester la connectivité internet si la passerelle est inaccessible par le service Power BI. |
| *.microsoftonline-p.com |443 |Utilisé pour l’authentification en fonction de la configuration. |

### <a name="force-https"></a>Forcer les communications HTTPS avec Azure Service Bus
Vous pouvez forcer la passerelle à communiquer avec Azure Service Bus à l’aide de HTTPS au lieu de TCP direct ; toutefois, cela peut affecter considérablement les performances. Vous pouvez modifier le fichier *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* en remplaçant la valeur `AutoDetect` par `Https`. Ce fichier se trouve généralement dans *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Forum Aux Questions

### <a name="general"></a>Généralités

**Q**: Ai-je besoin d’une passerelle pour les sources de données dans le cloud, par exemple Azure SQL Database ? <br/>
**R** : Non. Une passerelle est uniquement nécessaire pour une connexion à des sources de données locales.

**Q** : La passerelle doit-elle être installée sur le même ordinateur que la source de données ? <br/>
**R** : Non. La passerelle doit juste être en mesure de se connecter au serveur, en général sur le même réseau.

<a name="why-azure-work-school-account"></a>

**Q** : Pourquoi dois-je utiliser un compte professionnel ou scolaire pour me connecter ? <br/>
**R** : Lorsque vous installez la passerelle de données locale, vous pouvez uniquement utiliser un compte professionnel ou scolaire de l’organisation. Le compte doit également faire partie du même locataire que l’abonnement pour lequel vous configurez la ressource d’une passerelle. Votre compte de connexion est stocké dans un client géré par Azure Active Directory (Azure AD). En règle générale, le nom d’utilisateur principal (UPN) de votre compte Azure AD correspond à l’adresse de messagerie.

**Q** : où mes informations d’identification sont-elles stockées ? <br/>
**R** : Les informations d’identification que vous entrez pour une source de données sont chiffrées et stockées dans le service cloud de passerelle. Les informations d’identification sont déchiffrées au niveau de la passerelle de données locale.

**Q** : Existe-t-il des exigences concernant la bande passante réseau ? <br/>
**R** : Il est recommandé d’utiliser une connexion réseau offrant un bon débit. Chaque environnement est différent et la quantité de données envoyées affecte les résultats. ExpressRoute peut vous aider à garantir un niveau de débit approprié entre les centres de données locaux et les centres de données Azure.
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
**R** : Dans Services, la passerelle se nomme « service de passerelle de données sur site ».

**Q** : Le service Windows de passerelle peut-il s’exécuter avec un compte Azure Active Directory ? <br/>
**R** : Non. Le service Windows doit avoir un compte Windows valide. Par défaut, le service sera exécuté avec le SID du service, NT SERVICE\PBIEgwService.

**Q** : Comment effectuer une prise de contrôle de passerelle ? <br/>
**R** : Pour effectuer une prise de contrôle de passerelle (en exécutant la commande Configurer/Modifier sous Panneau de configuration > Programmes), vous devez être un propriétaire de la ressource de passerelle dans Azure et posséder la clé de récupération. Les propriétaires de ressources de passerelle peuvent être configurés dans Access Control.

### <a name="high-availability"></a>Haute disponibilité et récupération d’urgence

**Q** : Quelles sont les options de récupération d’urgence disponibles ? <br/>
**R**: Vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.

**Q** : Quel avantage la clé de récupération offre-t-elle ? <br/>
**R** : La clé de récupération permet de migrer ou de récupérer les paramètres de votre passerelle en cas de récupération d’urgence.

## <a name="troubleshooting"> </a>Résolution des problèmes

**Q** : Pourquoi ne puis-je pas voir ma passerelle dans la liste des instances de passerelle quand j’essaie de créer la ressource de passerelle dans Azure ? <br/>
**R** : Il existe deux raisons possibles. Tout d’abord, une ressource est déjà créée pour la passerelle dans l’abonnement en cours ou dans un autre. Pour éliminer cette hypothèse, énumérez les ressources du type **Passerelles de données locales** à partir du portail. Veillez à sélectionner tous les abonnements quand vous énumérez toutes les ressources. Une fois la ressource créée, la passerelle ne s’affiche pas dans la liste des instances de passerelle quand vous utilisez le portail pour créer des ressources de passerelle. La seconde possibilité est que l’identité Azure AD de l’utilisateur qui a installé la passerelle est différente de l’utilisateur connecté au Portail Azure. Pour résoudre cette situation, connectez-vous au portail en utilisant le même compte que l’utilisateur qui a installé la passerelle.

**Q** : Comment puis-je voir les requêtes qui sont envoyées à la source de données locale ? <br/>
**R** : Vous pouvez activer le traçage de requête qui inclut les requêtes envoyées. N’oubliez pas de rétablir la valeur d’origine du traçage des requêtes une fois les problèmes résolus. Le fait de laisser activé le traçage des requêtes contribue à augmenter la taille des journaux.

Vous pouvez également utiliser les outils de suivi des requêtes proposés par votre source de données. Par exemple, vous pouvez utiliser Extended Events ou SQL Profiler for SQL Server et Analysis Services.

**Q** : Où se situent les journaux de la passerelle ? <br/>
**R**: Voir la section Journaux plus loin dans cet article.

### <a name="update"></a>Mise à jour avec la version la plus récente

Beaucoup de problèmes peuvent survenir si la version de la passerelle est obsolète. En règle générale, veillez toujours à utiliser la version la plus récente. Si vous n’avez pas mis à jour la passerelle depuis un mois ou plus, vous pouvez installer la dernière version de la passerelle pour vérifier si le problème se reproduit.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erreur : Impossible d’ajouter l’utilisateur au groupe. (-2147463168 PBIEgwService Performance Log Users)

Cette erreur peut apparaître si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Veillez à déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

## <a name="logs"></a>Journaux

Les fichiers journaux constituent une ressource importante lors du dépannage.

#### <a name="enterprise-gateway-service-logs"></a>Journaux du service de passerelle Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Journaux de configuration

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Journaux d’événements

Les journaux de passerelle de gestion des données et PowerBIGateway figurent sous **Journaux des applications et services**.


## <a name="telemetry"></a>Télémétrie
La télémétrie peut être utilisée pour la surveillance et la résolution des problèmes. Par défaut

**Pour activer la télémétrie**

1.  Vérifiez le répertoire client de la passerelle de données sur site sur l’ordinateur. En règle générale, il s’agit de la **passerelle de données %systemdrive%\Program Files\On-premises**. Vous pouvez également ouvrir une console de services et vérifiez le chemin d’accès au fichier exécutable : une propriété du service de passerelle de données sur site.
2.  Dans le fichier Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config du répertoire client. Modifiez le paramètre SendTelemetry sur true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Enregistrez vos modifications et redémarrez le service Windows : service de passerelle de données sur site.




## <a name="next-steps"></a>Étapes suivantes
* [Installer et configurer une passerelle de données locale](analysis-services-gateway-install.md).   
* [Gérer Analysis Services](analysis-services-manage.md)
* [Obtenir les données d’Azure Analysis Services](analysis-services-connect.md)
