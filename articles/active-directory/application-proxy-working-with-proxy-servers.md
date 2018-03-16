---
title: Travailler avec des serveurs proxy locaux existants et Azure AD | Microsoft Docs
description: Explique comment travailler avec des serveurs proxy locaux existants.
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 85b45a828dbb06325b6a1c9453117849f48a27c8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Travailler avec des serveurs proxy locaux existants

Cet article explique comment configurer les connecteurs de proxy d’application Azure Active Directory (Azure AD) pour fonctionner avec des serveurs proxy sortants. Il est destiné aux clients avec des environnements réseau qui ont des proxys existants.

Nous allons commencer par examiner les scénarios de déploiement principaux suivants :
* Configurer des connecteurs pour contourner vos proxys sortants locaux.
* Configurer des connecteurs pour utiliser un proxy sortant afin d’accéder au proxy d’application Azure AD.

Pour plus d’informations sur le fonctionnement des connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md).

## <a name="bypass-outbound-proxies"></a>Proxys sortants de contournement

Les connecteurs ont des composants de système d’exploitation sous-jacents qui effectuent des demandes sortantes. Ces composants tentent automatiquement de trouver un serveur proxy sur le réseau à l’aide de la découverte automatique de proxy web (protocole WPAD).

Les composants du système d’exploitation tentent de localiser un serveur proxy en effectuant une recherche DNS pour wpad.domainsuffix. Si la recherche renvoie un DNS, une requête HTTP est alors effectuée sur l’adresse IP pour wpad.dat. Cette requête devient le script de configuration de proxy dans votre environnement. Le connecteur utilise ce script pour sélectionner un serveur proxy sortant. Toutefois, le trafic du connecteur peut ne pas toujours passer, en raison des paramètres de configuration supplémentaires nécessaires sur le serveur proxy.

Vous pouvez configurer le connecteur de sorte qu’il contourne votre proxy local pour vous assurer qu’il utilise une connexion directe aux services Azure. C’est ce que nous recommandons, tant que votre stratégie réseau le permet, car cela signifie que vous aurez une configuration de moins à mettre à jour.

Pour désactiver l’utilisation d’un proxy sortant pour le connecteur, modifiez le fichier C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section *system.net* indiquée dans cet exemple de code :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Pour vérifier que le service de mise à jour du connecteur ignore également le proxy, apportez une modification semblable au fichier ApplicationProxyConnectorUpdaterService.exe.config. Ce fichier se trouve dans C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Veillez à faire des copies des fichiers d’origine, au cas où vous devriez restaurer les fichiers par défaut.

## <a name="use-the-outbound-proxy-server"></a>Utiliser le serveur proxy sortant

Certains environnements clients requièrent que tout le trafic sortant passe par un proxy sortant, sans exception. Par conséquent, le contournement du serveur proxy n’est pas une option.

Vous pouvez configurer le trafic du connecteur pour passer par le serveur proxy sortant comme indiqué dans le diagramme suivant :

 ![Configuration du trafic de connecteur pour passer par un proxy sortant vers un proxy d’application Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Conséquence de la présence seule de trafic sortant, il est inutile de configurer l’accès entrant à travers vos pare-feu.

>[!NOTE]
>Le proxy d’application ne prend pas en charge l’authentification auprès d’autres proxys. Les comptes du service réseau de mise à jour/connecteur doivent être en mesure de se connecter au proxy sans avoir à se connecter avec l’authentification.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Étape 1 : Configurer le connecteur et les services liés pour passer par le proxy sortant

Si WPAD est activé dans l’environnement et correctement configuré, le connecteur détecte automatiquement le serveur proxy sortant et tente de l’utiliser. Toutefois, vous pouvez configurer explicitement le connecteur pour passer par un proxy sortant.

Pour ce faire, modifiez le fichier C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config et ajoutez la section *system.net* indiquée dans cet exemple de code. Modifiez *proxyserver:8080* afin de refléter le nom de votre serveur proxy local ou l’adresse IP et le port qu’il écoute.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Configurez ensuite le service de mise à jour du connecteur pour utiliser le proxy en apportant une modification similaire au fichier C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Étape 2 : Configurer le serveur proxy pour autoriser le passage du trafic à partir du connecteur et des services connexes

Il y a quatre aspects à prendre en compte au niveau du proxy sortant :
* Règles sortantes du proxy
* Authentification du proxy
* Ports du proxy
* Inspection SSL

#### <a name="proxy-outbound-rules"></a>Règles sortantes du proxy
Autorisez l’accès aux points de terminaison suivants pour l’accès au service de connecteur :

* * .msappproxy.net
* *.servicebus.windows.net

Pour l’inscription initiale, autorisez l’accès aux points de terminaison suivants :

* login.windows.net
* login.microsoftonline.com

Si vous ne pouvez pas autoriser la connectivité par le nom de domaine complet et devez spécifier des plages d’adresses IP à la place, utilisez ces options :

* Autoriser l’accès sortant du connecteur vers toutes les destinations.
* Autorisez l’accès sortant du connecteur à toutes les [plages d’adresses IP de centre de données Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Le problème lié à l’utilisation de la liste de plages d’adresses IP de centre de données Azure est qu’elle est mise à jour chaque semaine. Vous devez mettre un processus en place pour garantir que vos règles d’accès sont mises à jour en conséquence. La seule utilisation d’un sous-ensemble des adresses IP peut entraîner une rupture de votre configuration.

#### <a name="proxy-authentication"></a>Authentification du proxy

L’authentification proxy n'est actuellement pas prise en charge. Notre recommandation actuelle est de permettre au connecteur d’accéder de façon anonyme aux destinations Internet.

#### <a name="proxy-ports"></a>Ports du proxy

Le connecteur établit les connexions sortantes SSL à l’aide de la méthode CONNECT. Cette méthode sert à définir un tunnel via le serveur proxy sortant. Configurez le serveur proxy pour autoriser le tunneling vers les ports 443 et 80.

>[!NOTE]
>Lorsque Service Bus s’exécute via le protocole HTTPS, il utilise le port 443. Toutefois, par défaut, Service Bus tente des connexions TCP directes et bascule sur HTTPS uniquement si la connectivité directe échoue.

#### <a name="ssl-inspection"></a>Inspection SSL
N’utilisez pas l’inspection SSL pour le trafic de connecteur, car cela entraîne des problèmes pour le trafic du connecteur. Le connecteur utilise un certificat pour s’authentifier auprès du service Proxy d’application et ce certificat peut se perdre pendant l’inspection SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Résoudre les problèmes courants de proxy de connecteur et de connectivité du service
Vous devriez maintenant voir tout le trafic transitant par le proxy. Si vous rencontrez des problèmes, les informations de résolution des problèmes suivantes devraient vous aider.

Le meilleur moyen d’identifier et de résoudre les problèmes de connectivité de connecteur consiste à prendre une capture réseau au démarrage du service de connecteur. Voici quelques conseils rapides sur la capture et le filtrage de traces réseau.

Vous pouvez utiliser l’outil de surveillance de votre choix. Dans le cadre de cet article, nous avons utilisé Microsoft Message Analyzer. Vous pouvez [le télécharger à partir de Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Les exemples suivants sont spécifiques de Message Analyser, mais les principes peuvent être appliqués à n’importe quel outil d’analyse.

### <a name="take-a-capture-of-connector-traffic"></a>Effectuez une capture du trafic de connecteur

Pour commencer la résolution des problèmes, procédez comme suit :

1. À partir de services.msc, arrêtez le service de connecteur de proxy d’application Azure AD.

   ![Service de connecteur de proxy d’application Azure AD dans services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. Exécutez Message Analyser en tant qu’administrateur.
3. Sélectionnez **Start local trace (Démarrer la trace locale)**.

   ![Démarrer la capture réseau](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. Démarrez le service de connecteur de proxy d’application Azure AD.
4. Arrêtez la capture réseau.

   ![Arrêter la capture réseau](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Vérifier si le trafic du connecteur ignore les proxys sortants

Si vous avez configuré votre connecteur de proxy d’application de sorte à ce qu’il ignore les serveurs proxy et qu’il se connecte directement au service de proxy d’application, recherchez dans la capture réseau les tentatives de connexion TCP qui ont échoué. 

Utilisez le filtre Message Analyzer pour identifier ces tentatives. Entrez `property.TCPSynRetransmit` dans la zone de filtre, puis sélectionnez **Appliquer**. 

Un paquet SYN est le premier paquet envoyé pour établir une connexion TCP. Si ce paquet ne renvoie aucune réponse, le SYN est renvoyé. Vous pouvez utiliser le filtre précédent pour voir les SYN retransmis. Ensuite, vous pouvez vérifiez si ces SYN correspondent à du trafic lié à un connecteur.

Si vous vous attendez à ce que le connecteur établisse des connexions directes aux services Azure, les réponses SynRetransmit sur le port 443 sont une indication que vous avez un problème de réseau ou de pare-feu.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Vérifier si le trafic du connecteur utilise des proxys sortants

Si vous avez configuré le trafic du connecteur de votre proxy d’application de sorte à ce qu’il passe par les serveurs proxy, recherchez les connexions HTTPS à votre serveur proxy qui ont échoué. 

Pour filtrer la capture réseau de sorte à obtenir ces tentatives de connexion, entrez `(https.Request or https.Response) and tcp.port==8080` dans le filtre Message Analyzer, en remplaçant 8080 par le port de votre service proxy. Sélectionnez **Appliquer** pour voir les résultats du filtre. 

Le filtre précédent affiche uniquement les requêtes et réponses HTTPS vers/depuis le port du proxy. Vous recherchez les requêtes CONNECT indiquant une communication avec le serveur proxy. En cas de succès, vous obtenez une réponse HTTP OK (200).

Si vous voyez d’autres codes de réponse, comme 407 ou 502, cela signifie que le proxy nécessite une authentification ou n’autorise pas le trafic pour une raison quelconque. À ce stade, vous impliquez l’équipe de support technique de votre serveur proxy.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)

- Si vous avez des problèmes de connectivité du connecteur, posez votre question sur le [forum Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou créez un ticket auprès de notre équipe de support.
