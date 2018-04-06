---
title: Azure Traffic Manager - FAQ | Microsoft Docs
description: Cet article fournit des réponses aux questions fréquemment posées sur Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kumud
ms.openlocfilehash: d9db669ab905fb51390f6ca80736af4cde13d902
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) relatif à Traffic Manager

## <a name="traffic-manager-basics"></a>Concepts de base de Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Quelle est l’adresse IP utilisée par Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il envoie des réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Par conséquent, Traffic Manager ne fournit pas de point de terminaison ou d’adresse IP pour permettre aux clients de se connecter. Si vous souhaitez une adresse IP statique pour votre service, celle-ci doit être configurée au niveau du service, pas dans Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager prend-il en charge les sessions « persistantes » ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent directement au point de terminaison du service, et non via Traffic Manager. Par conséquent, Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur.

En outre, l’adresse IP source de la requête DNS reçue par Traffic Manager appartient au service DNS récursif, pas au client. N’ayant donc aucun moyen d’effectuer le suivi de clients individuels, Traffic Manager ne peut pas implémenter de sessions « persistantes ». Cette limitation est commune à tous les systèmes de gestion du trafic DNS et n’est pas spécifique de Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Pourquoi une erreur HTTP s’affiche-t-elle quand j’utilise Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager. Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur. C’est pourquoi, toute erreur HTTP que vous voyez doit provenir de votre application. Pour que le client se connecte à l’application, toutes les étapes de résolution DNS sont accomplies. Cela inclut toute interaction de Traffic Manager sur le flux de trafic de l’application.

Un examen approfondi de l’application doit donc être effectué.

L’en-tête d’hôte HTTP envoyé à partir du navigateur du client est la source de problèmes la plus courante. Assurez-vous que l’application est configurée pour accepter l’en-tête d’hôte correct pour le nom de domaine que vous utilisez. Pour les points de terminaison utilisant Azure App Service, voir [Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Quel est l’impact de Traffic Manager sur les performances ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Étant donné que les clients se connectent directement à vos points de terminaison de service, il n’y a aucun impact sur les performances inhérent à l’utilisation de Traffic Manager une fois la connexion établie.

Étant donné que Traffic Manager s’intègre aux applications au niveau du DNS, cela nécessite l’insertion d’une recherche DNS supplémentaire dans la chaîne de résolution DNS. L’impact sur le temps de résolution DNS de Traffic Manager est minime. Traffic Manager utilise un réseau mondial de serveurs de noms, et une mise en réseau [anycast](https://en.wikipedia.org/wiki/Anycast) pour garantir que les requêtes DNS sont toujours routées vers le serveur de noms disponible le plus proche. En outre, la mise en cache des réponses DNS signifie que la latence DNS supplémentaire générée par l’utilisation de Traffic Manager s’applique uniquement à une petite partie des sessions.

La méthode de Performance route le trafic vers le point de terminaison disponible le plus proche. Le résultat est que l’impact sur les performances globales associé à cette méthode doit être minimal. Toute augmentation de la latence DNS doit être compensée par une diminution de latence du réseau vers le point de terminaison.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quels protocoles d’application puis-je utiliser avec Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Une fois la recherche DNS terminée, les clients se connectent au point de terminaison d’application directement, et non via Traffic Manager. Par conséquent, la connexion peut utiliser n’importe quel protocole d’application. Si vous sélectionnez TCP comme protocole de surveillance, la surveillance de l’intégrité du point de terminaison de Traffic Manager peut s’effectuer sans protocole d’application. Si vous choisissez de vérifier l’intégrité avec un protocole d’application, le point de terminaison doit pouvoir répondre aux requêtes HTTP ou HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Puis-je utiliser Traffic Manager avec un nom de domaine « nu » ?

Non. Les normes DNS n’autorisent pas la coexistence d’enregistrements CNAME avec d’autres enregistrements DNS du même nom. Le sommet (ou racine) d’une zone DNS contient toujours deux enregistrements DNS existants : la SOA et les enregistrements NS faisant autorité. Cela signifie qu’un enregistrement CNAME ne peut pas être créé au sommet de la zone sans violer les normes DNS.

Traffic Manager nécessite un enregistrement CNAME DNS pour mapper le nom DNS personnel. Par exemple, vous mappez www.contoso.com au nom DNS de profil Traffic Manager contoso.trafficmanager.net. En outre, le profil Traffic Manager renvoie un deuxième CNAME DNS pour indiquer le point de terminaison auquel le client doit se connecter.

Pour contourner ce problème, nous vous recommandons d’utiliser une redirection HTTP pour diriger le trafic du nom de domaine nu vers une autre URL, qui peut ensuite utiliser Traffic Manager. Par exemple, le domaine nu « contoso.com » peut rediriger les utilisateurs vers l’enregistrement CNAME « www.contoso.com » qui pointe vers le nom DNS de Traffic Manager.

La prise en charge complète des domaines nus dans Traffic Manager est suivie dans notre backlog de fonctionnalités. Vous pouvez inscrire votre prise en charge pour cette demande de fonctionnalité en [votant pour celle-ci sur le site de commentaires de notre communauté](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager considère-t-il l’adresse de sous-réseau client lors du traitement des requêtes DNS ? 
Oui, dans le cadre de recherches pour les méthodes de routage géographique et basé sur les performances, Traffic Manager tient compte, en plus de l’adresse IP source de la requête DNS qu’il reçoit (en général l’adresse IP du programme de résolution DNS), de l’adresse de sous-réseau du client si celle-ci est incluse dans la requête par le programme de résolution qui effectue la demande pour le compte de l’utilisateur final.  
Plus précisément, le document [RFC 7871 – Client Subnet in DNS Queries](https://tools.ietf.org/html/rfc7871) fournit un [mécanisme d’extension pour DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) qui peut passer l’adresse de sous-réseau du client à partir de programmes de résolution qui le prennent en charge.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Qu’est-ce que le TTL du DNS et comment affecte-t-il mes utilisateurs ?

Lorsqu’une requête DNS arrive sur Traffic Manager, il définit une valeur dans la réponse appelée durée de vie (TTL). Cette valeur, dont l’unité est en secondes, indique aux programmes de résolution DNS en aval la durée de mise en cache de cette réponse. Les programmes de résolution DNS ne mettent pas nécessairement en cache ce résultat. Toutefois, la mise en cache leur permet de répondre aux requêtes suivantes alors que le cache est désactivé, au lieu d’aller aux serveurs DNS de Traffic Manager. Cela affecte les réponses de la manière suivante :
- une durée de vie plus élevée réduit le nombre de requêtes qui arrivent sur les serveurs DNS Traffic Manager, ce qui peut réduire le coût pour le client, puisque le nombre de requêtes servies est facturé ;
- une durée de vie plus élevée peut potentiellement réduire le temps nécessaire pour effectuer une recherche DNS ;
- une durée de vie plus élevée signifie également que vos données ne reflètent pas les dernières informations sur l’intégrité obtenues par Traffic Manager via ses agents de détection.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Quelles sont les limites minimales et maximales de la durée de vie des réponses de Traffic Manager ?

Vous pouvez définir, au niveau du profil, la durée de vie DNS entre 0 et 2 147 483 647 secondes (la plage maximale conformément à la norme [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Une durée de vie de 0 signifie que les programmes de résolution DNS en aval ne mettent pas en cache les réponses aux requêtes et toutes les requêtes doivent alors atteindre les serveurs DNS Traffic Manager pour être résolues.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Méthode de routage du trafic « Geographic » (Géographique) de Traffic Manager

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quels sont les cas d’utilisation dans lesquels le routage géographique est utile ? 
Le type de routage géographique peut être utilisé dans les scénarios dans lesquels un client Azure doit distinguer ses utilisateurs en fonction de régions géographiques. Par exemple, à l’aide de la méthode de routage de trafic géographique, vous pouvez attribuer une expérience utilisateur différente en fonction de la région. Un autre exemple est le respect des mandats de souveraineté de données locales qui nécessitent que les utilisateurs d’une région spécifique soient servis uniquement par les points de terminaison existant dans cette région.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quelles sont les régions prises en charge par Traffic Manager pour le routage géographique ? 
La hiérarchie de pays/régions utilisée par Traffic Manager se trouve [ici](traffic-manager-geographic-regions.md). Lorsque cette page est maintenue à jour avec les éventuelles modifications apportées, vous pouvez également récupérer par programme les mêmes informations à l’aide de [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Comment Traffic Manager détermine-t-il l’emplacement à partir duquel un utilisateur exécute une requête ? 
Traffic Manager examine l’adresse IP source de la requête (très probablement un programme de résolution DNS local effectuant la requête pour le compte de l’utilisateur) et utilise une adresse IP interne pour le mappage des régions afin de déterminer l’emplacement. Ce mappage est mis à jour en permanence pour prendre en compte les modifications apportées à Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Est-il garanti que Traffic Manager détermine correctement l’emplacement géographique exact de l’utilisateur dans tous les cas ?
Non, Traffic Manager ne peut pas garantir que la région géographique que nous déduisons à partir de l’adresse IP source d’une requête DNS correspond toujours à l’emplacement de l’utilisateur, et ce pour les raisons suivantes : 

- Tout d’abord, comme décrit dans le Forum aux questions précédent, l’adresse IP source que nous voyons est celle d’un programme de résolution DNS qui effectue une recherche pour le compte de l’utilisateur. Bien que l’emplacement géographique du programme de résolution DNS est un bon indicateur de l’emplacement géographique de l’utilisateur, il peut également être différent selon l’encombrement du service de résolution DNS et du service de résolution DNS spécifique qu'un client a choisi d’utiliser. Par exemple, un client en Malaisie peut définir dans les paramètres de son appareil l’utilisation d’un service de résolution DNS dont le serveur DNS à Singapour peut être sélectionné pour gérer les résolutions de requête pour cet utilisateur/appareil. Dans ce cas, Traffic Manager affiche uniquement l’adresse IP du programme de résolution, qui correspond à l’emplacement à Singapour. En outre, consultez le Forum aux questions précédent concernant la prise en charge des adresses de sous-réseau client sur cette page.

- Deuxièmement, Traffic Manager utilise une table interne pour mapper l’adresse IP sur la traduction de région géographique. Bien que ce mappage est validé et mis à jour en permanence pour améliorer sa précision et prendre en compte l’évolution d’Internet, il est toujours possible que nos informations ne sont pas une représentation exacte de l’emplacement géographique de toutes les adresses IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Pour le routage géographique, un point de terminaison doit-il être situé physiquement dans la même région que celle avec laquelle il est configuré ? 
Non, l’emplacement du point de terminaison n’impose aucune restriction sur les régions qui peuvent lui être mappées. Par exemple, tous les utilisateurs d’Inde peuvent être dirigés vers un point de terminaison situé dans la région Azure Centre des États-Unis.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Puis-je affecter des régions géographiques aux points de terminaison dans un profil qui n’est pas configuré pour procéder au routage géographique ? 

Oui, si la méthode de routage d’un profil n’est pas géographique, vous pouvez utiliser [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) pour affecter des régions géographiques aux points de terminaison de ce profil. Cette configuration est ignorée dans le cas des profils de type de routage non géographique. Si vous modifiez ultérieurement un tel profil en type de routage géographique, Traffic Manager peut utiliser ces mappages.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Pourquoi une erreur est-elle générée lorsque j’essaie de modifier la méthode de routage d’un profil existant en méthode de routage géographique ?

Au moins une région doit être mappée à tous les points de terminaison sous un profil doté d’une méthode de routage géographique. Pour convertir un profil existant en type de routage géographique, vous devez tout d’abord associer les régions géographiques à tous ses points de terminaison à l’aide de [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). Si vous utilisez le portail, vous devez d’abord supprimer les points de terminaison, remplacer la méthode de routage du profil par la méthode de routage géographique, puis ajouter les points de terminaison ainsi que le mappage de leurs régions géographiques. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Pourquoi est-il vivement recommandé que les clients créent des profils imbriqués à la place de points de terminaison sous un profil avec le routage géographique activé ? 

Une région ne peut être affectée qu’à un seul point de terminaison dans un profil s’il utilise le type de routage géographique. Si ce point de terminaison n’est pas un type imbriqué auquel un profil enfant est attaché et s’il devient défectueux, Traffic Manager continue à lui envoyer le trafic puisque l’alternative de ne pas envoyer de trafic ne se révèle pas meilleure. Traffic Manager ne bascule vers aucun autre point de terminaison même si la région affectée est « parente » de la région affectée au point de terminaison qui s’est détérioré (par exemple, si un point de terminaison auquel la région Espagne est affectée devient défectueux, nous ne basculons pas vers un autre point de terminaison auquel la région Europe est affectée). De cette façon, Traffic Manager respecte les limites géographiques qu’un client a configurées dans son profil. Pour profiter du basculement vers un autre point de terminaison lorsqu’un point de terminaison est défectueux, il est recommandé d’affecter les régions géographiques aux profils imbriqués avec plusieurs points de terminaison et non des points de terminaison individuels. De cette façon, en cas d’échec d’un point de terminaison du profil enfant imbriqué, le trafic peut basculer vers un autre point de terminaison dans le même profil enfant imbriqué.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existe-t-il des restrictions quant à la version de l’API qui prend en charge ce type de routage ?

Oui, seules les versions de l’API 2017-03-01 et plus récentes prennent en charge le type de routage Géographique. Les anciennes versions de l’API ne peuvent pas être utilisées pour créer des profils de type de routage géographique ou affecter des régions géographiques à des points de terminaison. Si une ancienne version de l’API est utilisée pour récupérer les profils d’un abonnement Azure, les profils de type de routage géographique ne sont pas retournés. En outre, si vous utilisez des versions anciennes de l’API, les profils retournés qui contiennent des points de terminaison avec une affectation de régions géographiques n’affichent pas les régions géographiques affectées.

## <a name="real-user-measurements"></a>Mesures des utilisateurs réels

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Quels sont les avantages de l’utilisation des mesures des utilisateurs réels ?
Quand vous utilisez la méthode de routage basé sur les performances, Traffic Manager sélectionne la meilleure région Azure à laquelle votre utilisateur final se connecte. Pour cela, Traffic Manager inspecte l’adresse IP source et le sous-réseau du client EDNS (s’il est passé), puis les compare aux informations sur la latence réseau que le service tient à jour. La fonctionnalité Mesures des utilisateurs réels optimise ce processus pour votre base d’utilisateurs finaux : d’une part, en prenant en compte l’expérience des utilisateurs finaux dans la table de latence et, d’autre part, en veillant à ce que cette table couvre de manière adéquate les réseaux des utilisateurs finaux à partir desquels ils se connectent à Azure. Il en résulte un routage plus précis des utilisateurs finaux.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Puis-je utiliser les mesures des utilisateurs réels avec des zones non-Azure ?
La fonctionnalité Mesures des utilisateurs réels calcule et signale uniquement la latence jusqu’aux régions Azure. Si vous utilisez le routage basé sur les performances avec des points de terminaison hébergés dans des régions non-Azure, vous pouvez encore tirer parti de cette fonctionnalité en associant les informations supplémentaires sur la latence de la région Azure représentative sélectionnée à ce point de terminaison.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Quelle méthode de routage bénéficie de la fonctionnalité Mesures des utilisateurs réels ?
Les informations supplémentaires acquises par la fonctionnalité Mesures des utilisateurs réels s’appliquent uniquement aux profils qui utilisent la méthode de routage basé sur les performances. Notez que le lien Mesures des utilisateurs réels est disponible à partir de tous les profils dans le portail Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Dois-je activer la fonctionnalité Mesures des utilisateurs réels séparément pour chaque profil ?
Non, il vous suffit de l’activer une fois par abonnement pour que toutes les informations de latence mesurées et signalées soient accessibles à tous les profils.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Comment faire pour désactiver la fonctionnalité Mesures des utilisateurs réels pour mon abonnement ?
Quand vous arrêtez de collecter et de renvoyer des mesures de latence à partir de votre application cliente, la fonctionnalité Mesures des utilisateurs réels n’engendre plus de frais. Par exemple, si le script JavaScript de mesure est incorporé dans les pages web, vous pouvez cesser d’utiliser cette fonctionnalité en supprimant le code JavaScript ou en désactivant l’appel à celui-ci quand la page est affichée.

Vous pouvez aussi désactiver la fonctionnalité Mesures utilisateur réelles en supprimant votre clé. Une fois la clé supprimée, toutes les mesures envoyées à Traffic Manager avec cette clé sont ignorées.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Puis-je utiliser la fonctionnalité Mesures des utilisateurs réels avec des applications clientes autres que des pages web ?
Oui, la fonctionnalité Mesures des utilisateurs réels est conçue pour ingérer les données collectées à l’aide d’autres types de clients d’utilisateurs finaux. Ce FAQ sera mis à jour pour refléter les nouveaux types d’applications clientes pris en charge.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Combien de mesures sont effectuées à chaque affichage d’une page web sur laquelle la fonctionnalité Mesures des utilisateurs réels est activée ?
Quand la fonctionnalité Mesures des utilisateurs réels est utilisée avec le script JavaScript de mesure fourni, chaque affichage de page produit six mesures. Celles-ci sont alors signalées au service Traffic Manager. Notez que les frais qui vous sont facturés pour cette fonctionnalité varient en fonction du nombre de mesures signalées au service Traffic Manager. Si, par exemple, un utilisateur quitte votre page web avant le signalement des mesures effectuées, celles-ci ne sont pas facturées.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Y a-t-il un délai avant l’exécution du script Mesures des utilisateurs réels dans ma page web ?
Non, l’appel du script n’est pas précédé d’un délai programmé.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Puis-je configurer la fonctionnalité Mesures des utilisateurs réels de manière à mesurer uniquement certaines régions Azure ?
Non, chaque fois qu’il est appelé, le script Mesures des utilisateurs réels mesure un ensemble de six régions Azure déterminées par le service. Cet ensemble varie d’un appel à l’autre et, quand un grand nombre de ces appels se produit, la couverture de la mesure s’étend à différentes régions Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Est-il possible de limiter le nombre de mesures effectuées à un nombre spécifique ?
Le script JavaScript de mesure est incorporé dans votre page Web. C’est vous qui décidez quand l’utiliser et quand ne plus l’utiliser. Tant que le service Traffic Manager reçoit une demande de mesure d’une liste de régions Azure, un ensemble de régions est retourné.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Puis-je afficher les mesures prises par mon application cliente dans le cadre de la fonctionnalité Mesures des utilisateurs réels ?
Comme la logique des mesures s’exécute à partir de votre application cliente, vous contrôlez entièrement ce qui se passe, notamment l’affichage des mesures de latence. Traffic Manager ne génère pas un affichage de synthèse des mesures reçues sous la clé liée à votre abonnement.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Puis-je modifier le script de mesure fourni par Traffic Manager ?
Bien que vous contrôliez ce qui est incorporé dans votre page web, nous vous déconseillons fortement de changer le script de mesure pour ne pas altérer la mesure et le signalement des latences.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Est-ce que d’autres utilisateurs peuvent voir la clé que j’utilise avec la fonctionnalité Mesures des utilisateurs réels ?
Quand vous incorporez le script de mesure à une page web, d’autres utilisateurs peuvent voir le script et votre clé Mesures des utilisateurs réels. Mais il est important de savoir que cette clé est différente de votre ID d’abonnement et qu’elle est générée par Traffic Manager uniquement dans ce but. La sécurité de votre compte Azure n’est pas compromise si quelqu’un a connaissance de votre clé Mesures utilisateur réelles.

### <a name="can-others-abuse-my-rum-key"></a>Ma clé Mesures des utilisateurs réels peut-elle faire l’objet d’une utilisation malveillante ?
Bien que d’autres personnes puissent utiliser votre clé pour envoyer des informations erronées à Azure, le routage est établi sur la base de toutes les mesures que nous recevons et n’est pas affecté par quelques mesures incorrectes. Si vous avez besoin de changer votre clé, vous pouvez la regénérer (l’ancienne clé est alors ignorée).

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Dois-je placer le script JavaScript de mesure dans toutes mes pages web ?
Plus le nombre de mesures est important, plus la fonctionnalité Mesures des utilisateurs réels est avantageuse. Ceci dit, vous êtes libre de placer le script dans toutes vos pages web ou dans seulement certaines d’entre elles. Notre vous recommandons de commencer par le placer dans la page la plus visitée sur laquelle un utilisateur passe généralement cinq secondes ou plus.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Traffic Manager peut-il identifier des informations sur mes utilisateurs finaux si j’utilise la fonctionnalité Mesures des utilisateurs réels ?
Si le script JavaScript de mesure fourni est utilisé, Traffic Manager peut voir l’adresse IP du client de l’utilisateur final et l’adresse IP source du programme de résolution DNS local qu’il utilise. Traffic Manager utilise l’adresse IP du client uniquement une fois celle-ci tronquée pour ne pas pouvoir identifier l’utilisateur final ayant envoyé les mesures. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>La page web sur laquelle la fonctionnalité Mesures des utilisateurs réels est activée doit-elle utiliser Traffic Manager pour le routage ?
Non, l’utilisation de Traffic Manager n’est pas obligatoire. La partie routage de Traffic Manager fonctionne séparément de la partie Mesures des utilisateurs réels. Elles ne doivent pas nécessairement se trouver dans la même propriété web, même si cela est une bonne idée.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Dois-je héberger un service sur les régions Azure à utiliser avec la fonctionnalité Mesures des utilisateurs réels ?
Non, la fonctionnalité Mesures utilisateur réelles ne nécessite pas de composant côté serveur pour fonctionner. L’image à pixel unique téléchargée par le script JavaScript de mesure et le service qui l’exécute dans différentes régions Azure est hébergée et gérée par Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>L’utilisation de la bande passante Azure augmente-t-elle quand j’utilise la fonctionnalité Mesures des utilisateurs réels ?
Comme indiqué dans la réponse précédente, les composants côté serveur de la fonctionnalité Mesures des utilisateurs réels sont hébergés et gérés par Azure. La fonctionnalité Mesures des utilisateurs réels n’entraîne donc pas une augmentation de la bande passante Azure utilisée. Notez que ceci n’inclut pas l’utilisation de la bande passante au-delà de ce que facture Azure. Pour minimiser la bande passante utilisée, nous téléchargeons une image à pixel unique pour mesurer la latence jusqu’à une région Azure. 

## <a name="traffic-view"></a>Affichage du trafic

### <a name="what-does-traffic-view-do"></a>À quoi sert la fonctionnalité Affichage du trafic ?
Affichage du trafic est une fonctionnalité de Traffic Manager qui vous permet de découvrir plus en détail vos utilisateurs et leur expérience. Elle utilise les requêtes reçues par Traffic Manager et les tables de temps de réponse du réseau que le service tient à jour pour fournir les informations suivantes :
- Régions à partir desquelles vos utilisateurs se connectent à vos points de terminaison dans Azure
- Volume des utilisateurs se connectant à partir de ces régions
- Régions Azure vers lesquelles ils sont routés
- Expérience en matière de latence vers ces régions Azure

Ces informations sont disponibles sous forme de superposition de carte géographique et d’un tableau dans le portail. Vous pouvez aussi les télécharger en tant que données brutes.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Quels sont les avantages de la fonctionnalité Affichage du trafic ?

La fonctionnalité Affichage du trafic vous donne une vue d’ensemble du trafic reçu par vos profils Traffic Manager. Vous pouvez vous en servir pour déterminer les régions à partir desquelles votre base d’utilisateurs se connecte et, ce qui est tout aussi important, leur latence moyenne. Ces informations vous permettent d’identifier les zones sur lesquelles vous devez vous concentrer. Vous pouvez par exemple étendre votre présence Azure à une région qui peut servir ces utilisateurs avec une latence plus faible. La fonctionnalité Affichage du trafic permet de dériver un autre insight, à savoir l’affichage des modèles de trafic vers différentes régions pour faciliter la prise de décision quant à l’augmentation ou la diminution des investissements dans ces régions.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>En quoi la fonctionnalité Affichage du trafic diffère-t-elle des métriques Traffic Manager disponibles dans Azure Monitor ?

Vous pouvez utiliser Azure Monitor pour comprendre, au niveau global, le trafic reçu par votre profil et ses points de terminaison. Il vous permet également de suivre l’état d’intégrité des points de terminaison en exposant les résultats de la vérification d’intégrité. Si vous souhaitez aller plus loin et comprendre l’expérience de vos utilisateurs finaux qui se connectent à Azure au niveau régional, utilisez la fonctionnalité Affichage du trafic.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>La fonctionnalité Affichage du trafic utilise-t-elle les informations sur le sous-réseau du client EDNS ?

Les requêtes DNS présentées par Azure Traffic Manager prennent en compte les informations ECS pour améliorer la précision du routage. Mais lors de la création du jeu de données qui montre d’où les utilisateurs se connectent, la fonctionnalité Vue du trafic utilise uniquement l’adresse IP du programme de résolution DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Combien de jours de données la fonctionnalité Affichage du trafic utilise-t-elle ?

Pour créer sa sortie, la fonctionnalité Affichage du trafic traite les données des sept jours précédant la veille de votre visite. Il s’agit d’une fenêtre dynamique dans laquelle les dernières données sont utilisées à chaque visite.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Comment la fonctionnalité Affichage du trafic gère-t-elle les points de terminaison externes ?

Quand vous utilisez des points de terminaison externes hébergés en dehors des régions Azure dans un profil Traffic Manager, vous pouvez choisir de les mapper à une région Azure qui constitue un proxy pour ses caractéristiques de latence (ce qui est nécessaire si vous utilisez la méthode de routage basé sur les performances). Si un mappage à une région Azure est défini, les métriques de latence de la région Azure sont utilisées au moment de la création de la sortie Vue du trafic. Si aucune région Azure n’est spécifiée, les informations de latence sont vides dans les données correspondant à ces points de terminaison externes.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Dois-je activer la fonctionnalité Affichage du trafic pour chaque profil de mon abonnement ?

Pendant la période d’évaluation, la fonctionnalité Vue du trafic a été activée au niveau de l’abonnement. Dans le cadre des améliorations apportées avant la mise à la disponibilité générale, vous pouvez désormais activer la fonctionnalité Vue du trafic au niveau du profil, ce qui vous permet de disposer d’une activation plus précise de cette fonctionnalité. Par défaut, la fonctionnalité Vue du trafic est désactivée pour un profil.

>[!NOTE]
>Si vous avez activé la fonctionnalité Vue du trafic au niveau de l’abonnement pendant la période d’évaluation, vous devez maintenant l’activer de nouveau pour chaque profil sous cet abonnement.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Comment désactiver la fonctionnalité Affichage du trafic ? 
Vous pouvez désactiver la fonctionnalité Vue du trafic pour n’importe quel profil à l’aide du portail ou de l’API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Comment la fonctionnalité Affichage du trafic est-elle facturée ?

Le prix de la fonctionnalité Affichage du trafic est basé sur le nombre de points de données utilisés pour créer la sortie. Les requêtes reçues par votre profil sont actuellement le seul type de données pris en charge. En outre, seul le traitement effectué quand la fonctionnalité Affichage du trafic est activée vous est facturé. Cela signifie que si vous activez la fonctionnalité Affichage du trafic pendant une certaine période au cours du mois et que vous la désactivez à d’autres périodes, seuls les points de données traités quand la fonctionnalité est activée sont comptabilisés dans votre facture.

## <a name="traffic-manager-endpoints"></a>Points de terminaison Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Puis-je utiliser Traffic Manager avec des points de terminaison de plusieurs abonnements ?

L’utilisation de points de terminaison à partir de plusieurs abonnements n’est pas possible avec Azure Web Apps. Azure Web Apps requiert que tout nom de domaine personnalisé utilisé avec Web Apps ne soit utilisé que dans un seul abonnement. Il n’est pas possible d’utiliser des applications web à partir de plusieurs abonnements portant le même nom de domaine.

Pour les autres types de point de terminaison, il est possible d’utiliser Traffic Manager avec des points de terminaison provenant de plusieurs abonnements. Dans Resource Manager, vous pouvez ajouter à Traffic Manager des points de terminaison de n’importe quel abonnement tant que la personne qui configure le profil Traffic Manager dispose d’un accès en lecture au point de terminaison. Ces autorisations peuvent être accordées à l’aide du [contrôle d’accès en fonction du rôle Azure Resource Manager (RBAC)](../active-directory/role-based-access-control-configure.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Puis-je utiliser Traffic Manager avec les emplacements intermédiaires de services cloud ?

Oui. Les emplacements intermédiaires de services cloud peuvent être configurés dans Traffic Manager en tant que points de terminaison externes. Les contrôles d’intégrité sont toujours facturés au tarif des points de terminaison Azure. Comme le type de point de terminaison externe est utilisé, les modifications apportées au service sous-jacent ne sont pas sélectionnées automatiquement. Avec les points de terminaison externes, Traffic Manager ne peut pas détecter lorsque le service cloud est arrêté ou supprimé. Par conséquent, Traffic Manager continue de facturer les vérifications d’intégrité jusqu’à ce que le point de terminaison soit désactivé ou supprimé.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager prend-il en charge les points de terminaison IPv6 ?

Traffic Manager ne fournit pas actuellement de serveurs de noms adressables en IPv6. Toutefois, il prend en charge les clients IPv6 connectés à des points de terminaison IPv6. Un client n’effectue pas de requêtes DNS directement vers Traffic Manager. Au lieu de cela, il utilise un service DNS récursif. Un client utilisant uniquement IPv6 envoie des requêtes au service DNS récursif via IPv6. Après quoi le service récursif doit être en mesure de contacter les serveurs de noms Traffic Manager à l’aide du protocole IPv4.

Traffic Manager répond avec le nom DNS du point de terminaison. Pour prendre en charge un point de terminaison IPv6, un enregistrement AAAA DNS pointant le nom DNS du point de terminaison vers l’adresse IPv6 doit exister. Les contrôles d’intégrité Traffic Manager n’acceptent que les adresses IPv4. Le service doit exposer un point de terminaison IPv4 sur le même nom DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Puis-je utiliser Traffic Manager avec plusieurs applications web dans la même région ?

En règle générale, Traffic Manager est utilisé pour diriger le trafic vers des applications déployées dans des régions différentes. Vous pouvez toutefois l’utiliser dans les applications comportant plusieurs déploiements dans la même région. Les points de terminaison Azure Traffic Manager ne permettent pas d’ajouter au même profil Traffic Manager plusieurs points de terminaison d’application web provenant de la même région Azure.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Comment déplacer les points de terminaison Azure de mon profil Traffic Manager vers un autre groupe de ressources ?

Les points de terminaison Azure qui sont associés à un profil Traffic Manager sont suivis à l’aide de leur ID de ressource. Lorsqu’une ressource Azure qui est utilisée comme point de terminaison (par exemple, une adresse IP publique, un service cloud classique, une application web ou un autre profil Traffic Manager utilisé de façon imbriquée) est déplacée vers un autre groupe de ressources, son ID de ressource est modifié. Dans ce scénario, vous devez mettre à jour le profil Traffic Manager en commençant par supprimer les points de terminaison du profil avant de les ajouter à nouveau. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Surveillance des points de terminaison Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager est-il résistant aux défaillances des régions Azure ?

Traffic Manager est un composant clé de la distribution d’applications hautement disponibles dans Azure.
Pour garantir une haute disponibilité, Traffic Manager doit avoir un niveau très élevé de disponibilité et être résistant aux défaillances régionales.

Par défaut, les composants de Traffic Manager sont résistants à une panne complète de n’importe quelle région Azure. Cette tolérance s’applique à tous les composants de Traffic Manager : les serveurs de noms DNS, l’API, la couche de stockage et le service de surveillance des points de terminaison.

Dans l’éventualité peu probable d’une panne d’une région Azure entière, Traffic Manager est conçu pour continuer à fonctionner normalement. Les applications déployées dans plusieurs régions Azure peuvent se baser sur Traffic Manager pour diriger le trafic vers une instance disponible de leur application.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>En quoi le choix de l’emplacement du groupe de ressources affecte-t-il Traffic Manager ?

Traffic Manager est un service global unique. Il n’est pas régional. Le choix de l’emplacement du groupe de ressources na aucune influence sur les profils Traffic Manager déployés dans ce groupe de ressources.

Azure Resource Manager exige que tous les groupes de ressources spécifient un emplacement, qui détermine l’emplacement par défaut des ressources déployées dans ce groupe de ressources. Lorsque vous créez un profil Traffic Manager, il est créé dans un groupe de ressources. Tous les profils Traffic Manager utilisent la valeur **global** comme emplacement, à la place de la valeur par défaut du groupe de ressources.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Comment déterminer l’état d’intégrité actuel de chaque point de terminaison ?

L’état de surveillance en cours de chaque point de terminaison, en plus du profil global s’affichent dans le portail Azure. Ces informations sont également disponibles via [l’API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) et [l’interface de ligne de commande Azure multiplateforme](../cli-install-nodejs.md) de Traffic Monitor.

Vous pouvez également utiliser Azure Monitor pour suivre l’intégrité de vos points de terminaison et en voir une représentation visuelle. Pour plus d’informations sur l’utilisation d’Azure Monitor, consultez la [documentation sur la surveillance Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Puis-je surveiller les points de terminaison HTTPS ?

Oui. Traffic Manager prend en charge la détection sur HTTPS. Configurez **HTTPS** comme protocole dans la configuration de la surveillance.

Traffic manager ne peut pas fournir de validation de certificat :

* Les certificats côté serveur ne sont pas validés.
* Les certificats SNI côté serveur ne sont pas pris en charge.
* Les certificats clients ne sont pas pris en charge.

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>J’ai arrêté un point de terminaison d’application web/service cloud Azure dans mon profil Traffic Manager, mais je ne reçois pas de trafic même après un redémarrage. Comment puis-je résoudre ce problème ?

Quand un point de terminaison d’application web/service cloud Azure est arrêté, Traffic Manager cesse de vérifier son intégrité et redémarre les vérifications d’intégrité uniquement après avoir détecté le redémarrage du point de terminaison. Pour éviter ce délai, désactivez puis réactivez ce point de terminaison dans le profil Traffic Manager après avoir redémarré le point de terminaison.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Puis-je utiliser Traffic Manager même si mon application ne prend pas en charge HTTP ou HTTPS ?

Oui. Vous pouvez spécifier TCP comme protocole de surveillance et Traffic Manager peut établir une connexion TCP et attendre une réponse du point de terminaison. Si le point de terminaison répond à la demande de connexion en indiquant d’établir la connexion, dans le délai imparti, ce point de terminaison est marqué comme étant intègre.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Quelles réponses spécifiques du point de terminaison sont requises lorsque la surveillance TCP est utilisée ?

Lorsque la surveillance TCP est utilisée, Traffic Manager démarre une connexion TCP en trois temps en envoyant une requête SYN au point de terminaison au niveau du port spécifié. Puis, il attend pendant un délai défini (spécifié dans les paramètres de délai d’expiration) une réponse du point de terminaison. Si le point de terminaison répond à la requête SYN par une réponse SYN-ACK dans le délai d’expiration spécifié dans les paramètres de surveillance, ce point de terminaison est considéré comme intègre. Si la réponse SYN-ACK est reçue, Traffic Manager réinitialise la connexion en répondant par un RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>À quelle vitesse Traffic Manager éloigne-t-il mes utilisateurs d’un point de terminaison défectueux ?

Traffic Manager fournit plusieurs paramètres qui peuvent vous aider à contrôler le comportement du basculement de votre profil Traffic Manager comme suit :
- Vous pouvez spécifier que Traffic Manager tente de détecter les points de terminaison plus fréquemment en définissant l’intervalle de détection sur 10 secondes. Cela garantit que tout point de terminaison qui devient défectueux peut être détecté le plus tôt possible. 
- Vous pouvez spécifier la durée d’attente avant que l’expiration d’une demande de contrôle de l’intégrité (la valeur minimale du délai d’expiration est de 5 secondes).
- Vous pouvez spécifier le nombre d’erreurs qui peuvent se produire avant que le point de terminaison soit considéré comme défectueux. Cette valeur peut être définie sur 0, auquel cas le point de terminaison est marqué défectueux dès le premier échec au premier contrôle d’intégrité. Toutefois, l’utilisation de la valeur minimale 0 comme nombre autorisé d’échecs risque d’entraîner l’exclusion des points de terminaison de la rotation en raison de problèmes temporaires qui peuvent survenir au moment de la détection.
- Vous pouvez définir 0 comme durée de vie (TTL) minimale pour la réponse DNS. Cela signifie que les programmes de résolution DNS ne peuvent pas mettre en cache la réponse et que chaque nouvelle requête obtient une réponse qui intègre les dernières informations de contrôle d’intégrité mises à jour détenues par Traffic Manager.

À l’aide de ces paramètres, Traffic Manager peut fournir des basculements moins de 10 secondes après la défaillance d’un point de terminaison et une requête DNS est effectuée par rapport au profil correspondant.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Comment spécifier différents paramètres de surveillance pour les différents points de terminaison dans un profil ?

Les paramètres de surveillance de Traffic Manager sont définis au niveau du profil. Si vous devez utiliser un autre paramètre de surveillance pour un seul point de terminaison, il convient de définir ce point de terminaison comme [profil imbriqué](traffic-manager-nested-profiles.md) dont les paramètres de surveillance sont différents du profil parent.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Quel en-tête hôte est utilisé pour les contrôles d’intégrité des points de terminaison ?

Traffic Manager utilise des en-têtes d’hôte pour les contrôles d’intégrité HTTP et HTTPS. L’en-tête d’hôte utilisé par Traffic Manager est le nom du point de terminaison cible configuré dans le profil. La valeur utilisée dans l’en-tête hôte ne peut pas être spécifiée séparément de la propriété cible.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quelles sont les adresses IP à l’origine des contrôles d’intégrité ?

Cliquez [ici](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) pour afficher le fichier JSON qui répertorie les adresses IP à partir desquelles peuvent provenir les contrôles de Traffic Manager. Examinez les adresses IP du fichier JSON pour vérifier que les connexions entrantes à partir de ces adresses IP sont autorisées aux points de terminaison pour vérifier leur état d’intégrité.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Combien de contrôles d’intégrité de mon point de terminaison Traffic Manager effectue-t-il ?

Le nombre de contrôles d’intégrité de Traffic Manager qui atteignent votre point de terminaison varie selon les éléments suivants :
- La valeur que vous avez définie pour l’intervalle de surveillance (plus l’intervalle est petit, plus le nombre de requêtes atteignant votre point de terminaison est élevé, dans une période de temps donnée).
- Le nombre d’emplacements d’où proviennent les contrôles d’intégrité (les adresses IP d’où peuvent provenir ces contrôles sont répertoriées dans la rubrique FAQ précédente).

## <a name="traffic-manager-nested-profiles"></a>Profils Traffic Manager imbriqués

### <a name="how-do-i-configure-nested-profiles"></a>Comment configurer des profils imbriqués ?

Des profils Traffic Manager imbriqués peuvent être configurés tant à l’aide d’Azure Resource Manager, qu’à l’aide des API REST Azure classiques, d’applets de commande PowerShell et de commandes d’interface de ligne de commande Azure multiplateformes. Ils sont également pris en charge via le nouveau portail Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Combien de couches d’imbrication Traffic Manager prend-il en charge ?

Vous pouvez imbriquer jusqu’à 10 niveaux de profil. Les « boucles » ne sont pas autorisées.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Puis-je combiner d’autres types de point de terminaison avec des profils enfants imbriqués dans le même profil Traffic Manager ?

Oui. Il n’existe aucune restriction sur la manière de combiner des points de terminaison de différents types au sein d’un profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Comment le modèle de facturation s’applique-t-il aux profils imbriqués ?

Il n’y a aucun impact négatif sur la tarification en cas d’utilisation de profils imbriqués.

La facturation de Traffic Manager inclut deux composantes : des contrôles d’intégrité des points de terminaison et des millions de requêtes DNS.

* Contrôles d’intégrité des points de terminaison : aucun frais n’est facturé pour un profil enfant configuré en tant que point de terminaison dans un profil parent. La surveillance des points de terminaison dans le profil enfant est facturée comme à l’accoutumée.
* Requêtes DNS : chaque requête n’est comptabilisée qu’une seule fois. L’interrogation d’un profil parent qui retourne un point de terminaison à partir d’un profil enfant est facturée pour le profil parent uniquement.

Pour plus d’informations, voir la [page de tarification de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Y a-t-il un impact sur les performances en cas de profils imbriqués ?

Non. Non, l’utilisation de profils imbriqués n’a aucune incidence sur les performances.

Les serveurs de noms Traffic Manager parcourent la hiérarchie de profils en interne lors du traitement de chaque requête DNS. Une requête DNS adressée à un profil parent peut recevoir une réponse DNS avec un point de terminaison d’un profil enfant. Un seul enregistrement CNAME est utilisé, que vous utilisiez un profil unique ou des profils imbriqués. Il est inutile de créer un enregistrement CNAME pour chaque profil dans la hiérarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Comment Traffic Manager calcule-t-il l’intégrité d’un point de terminaison imbriqué dans un profil parent ?

Le profil parent n’effectue pas de contrôles d’intégrité directement sur le profil enfant. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant. Ces informations sont propagées vers le haut de la hiérarchie de profils imbriqués afin de déterminer l’intégrité du point de terminaison imbriqué. Le profil parent utilise cette intégrité agrégée pour déterminer si le trafic peut être dirigé vers le profil enfant.

Le tableau suivant décrit le comportement des contrôles d’intégrité de Traffic Manager pour un point de terminaison imbriqué.

| État d’analyse des profils enfants | État d’analyse des points de terminaison parents | Notes |
| --- | --- | --- |
| Désactivé. Le profil enfant a été désactivé. |Arrêté |L’état des points de terminaison parents est Arrêté, pas Désactivé. L’état Désactivé sert uniquement à indiquer que vous avez désactivé le point de terminaison dans le profil parent. |
| Détérioré. Au moins un point de terminaison du profil enfant a l’état Détérioré. |En ligne : le nombre de points de terminaison en ligne dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>CheckingEndpoint : le nombre de points de terminaison en ligne et CheckingEndpoint dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>Détérioré : dans le cas contraire. |Le trafic est acheminé vers un point de terminaison à l’état CheckingEndpoint. Si la valeur de MinChildEndpoints est trop élevée, le point de terminaison est toujours détérioré. |
| En ligne. Au moins un point de terminaison de profil enfant est en état En ligne. Aucun point de terminaison n’est en état Détérioré. |Voir ci-dessus. | |
| CheckingEndpoints. Au moins un point de terminaison de profil enfant est en état « CheckingEndpoint ». Aucun point de terminaison n’est en état « En ligne » ou « Détérioré ». |Identique à ce qui précède. | |
| Inactif. Tous les points de terminaison du profil enfant sont en état Désactivé ou Arrêté, ou ce profil n’a aucun point de terminaison. |Arrêté | |

## <a name="next-steps"></a>Étapes suivantes :
- En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](../traffic-manager/traffic-manager-monitoring.md)de Traffic Manager.
- En savoir plus sur les [méthodes de routage du trafic](../traffic-manager/traffic-manager-routing-methods.md)de Traffic Manager.
