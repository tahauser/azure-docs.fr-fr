---
title: "Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexions hybrides dans BizTalk Services | Microsoft Docs"
description: "En savoir plus sur les contrôles et l’analyse de performances pour BizTalk Services"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Passer en revue les onglets Tableau de bord, Surveiller, Mettre à l’échelle, Configurer et Connexion hybride

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Après avoir créé votre service BizTalk et déployé votre application, vous pouvez modifier certains paramètres du service BizTalk et surveiller les performances de l'application. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Une nouvelle fenêtre apparaît avec les onglets suivants : Cette rubrique décrit ces onglets.

## <a name="quickstart-quickstartquickstart"></a>Démarrage rapide (![Démarrage rapide][Quickstart])
Selon l'édition de BizTalk Services, toutes les options énumérées peuvent ne pas être disponibles. 

<table border="1">
    <tr>
        <td><strong>Obtention des outils</strong></td>
        <td>Téléchargez le Kit SDK de BizTalk Services pour installer les modèles de projet Visual Studio sur votre ordinateur de développement local. Ces modèles créent les projets Visual Studio <strong>BizTalk Services</strong> (pont) et <strong>Artefacts de service BizTalk</strong> (Transformation) qui sont déployés sur votre service BizTalk.
        <br/><br/>Les rubriques 
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services</a> et <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installation du Kit de développement logiciel (SDK) Azure BizTalk Services</a> présentent les procédures de prise en main.
        </td>
    </tr>
    <tr>
        <td><strong>Créer des accords partenaires</strong></td>
        <td>Ouvre le portail Azure BizTalk Services hébergé sur Azure, qui vous permet d'ajouter des partenaires et de créer des contrats EDI X12, AS2 et EDIFACT.
        <br/><br/>La rubrique 
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration d'EDI, d'AS2 et d'EDIFACT sur le portail BizTalk Services</a> décrit la procédure de prise en main.
        </td>
    </tr>

<tr>
        <td><strong>En savoir plus sur les services BizTalk</strong></td>
        <td>Pour en savoir plus sur les services BizTalk d’Azure, accédez à l’<a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Espace formations</a>.</td>
</tr>
</table>


Dans la barre des tâches située en bas, vous pouvez :

<table border="1">

<tr>
<td><strong>Gérer</strong> le déploiement de votre application</td>
<td>Ouvre le portail Azure BizTalk Services Celui-ci représente l'accès à la configuration de l'échange de données informatisé (EDI, Electronic Data Interchange), y compris en ce qui concerne l'ajout de partenaires et la création de contrats X12, AS2 et EDIFACT.
<br/><br/>
Cela revient à utiliser <strong>Créer des accords partenaires</strong> sous l’onglet <strong>Démarrage rapide</strong>.
<br/><br/>La rubrique 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration de composants pour la messagerie EDI sur le portail BizTalk Services</a> fournit des informations supplémentaires sur le portail BizTalk Services.</td>
</tr>

<tr>
<td><strong>Informations de connexion</strong> de l’espace de noms Access Control</td>
<td>Lorsque vous sélectionnez Informations de connexion, l'espace de noms Access Control, l'émetteur par défaut et la clé par défaut s'affichent. Vous pouvez copier ces valeurs.
<br/><br/>
Vous pouvez également ouvrir le portail Access Control. La rubrique <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Créer un espace de noms Access Control</a> fournit des informations supplémentaires sur le portail Access Control.</td>
</tr>

<tr>
<td><strong>Synchroniser les clés</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés de chiffrement contrôlent l'accès à votre compte de stockage. Votre service BizTalk utilise automatiquement la clé primaire. Les <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.
<br/><br/>
Supposons que vous vouliez que le service BizTalk utilise une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Sélectionnez votre service BizTalk, puis <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Une fois que c'est fait, le service BizTalk commence à utiliser la clé secondaire.</li>
<li>Sélectionnez votre compte de stockage et regénérez la clé primaire. Rappelez-vous que votre service BizTalk utilise la clé secondaire.</li>
<li>Sélectionnez votre service BizTalk, puis <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s'agit de la nouvelle clé primaire que vous avez régénérée.</li>
<li>Sélectionnez votre compte de stockage et regénérez la clé secondaire.</li>
</ol>
<br/>
On parle de « clés de substitution » pour décrire ce processus. L'objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Lorsque vous sélectionnez Supprimer, votre service BizTalk et tous les éléments qui y ont été déployés sont supprimés.</td>
</tr>
</table>


## <a name="dashboard"></a>Tableau de bord
Selon l'édition de BizTalk Services, toutes les options énumérées peuvent ne pas être disponibles. 

Lorsque vous sélectionnez le nom de votre service BizTalk, l'onglet Tableau de bord apparaît. Dans le tableau de bord, vous pouvez :

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Vue d’ensemble de l’utilisation : montre le nombre de connexions hybrides utilisées.
Affiche également l'utilisation des données en Go. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Graphique métrique : illustre une liste fixe de mesures de performances.
Ces mesures fournissent des valeurs en temps réel concernant l'intégrité du service BizTalk. Vous pouvez également spécifier les valeurs **Relatives** et **Absolues**, ainsi que l’**Intervalle** de temps des mesures affichées sur le graphique. 

Pour obtenir une description de ces mesures de performances, accédez à la section [Mesures disponibles](#Metrics) de cette rubrique.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Aperçu rapide : dresse la liste des propriétés de votre service BizTalk.
<table border="1">

<tr>
<td><strong>Mettre à jour les informations d’identification de la base de données de suivi</strong></td>
<td>Modifie le nom d'utilisateur et le mot de passe utilisés pour se connecter à la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Mettre à jour le certificat SSL</strong></td>
<td>Permet de mettre à jour le service BizTalk pour utiliser un autre certificat SSL. Un certificat SSL auto-signé est automatiquement créé lorsque vous <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">créez le service BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Télécharger le certificat</strong></td>
<td>Vous pouvez télécharger le certificat SSL utilisé par votre service BizTalk sur une machine locale.</td>
</tr>
<tr>
<td><strong>État</strong></td>
<td>Affiche le statut actuel de votre service BizTalk. Voir <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Tableau comparatif des états de BizTalk Services</a>. </td>
</tr>
<tr>
<td><strong>URL du service</strong></td>
<td>URL de votre service BizTalk. Il s’agit de l’<strong>URL de domaine</strong> entrée lors de la création de votre service BizTalk.</td>
</tr>
<tr>
<td><strong>Adresse IP virtuelle (VIP) publique</strong></td>
<td>Adresse IP attribuée à votre service BizTalk. Elle est utilisée pour tous les points de terminaison d’entrée et représente l'adresse source pour le trafic sortant. L'adresse IP appartient à votre service BizTalk si il est créé. Si vous supprimez le service BizTalk, l'adresse IP est attribuée à un autre service BizTalk.</td>
</tr>
<tr>
<td><strong>Espace de noms ACS</strong></td>
<td>Permet de s'authentifier auprès du service BizTalk.</td>
</tr>
<tr>
<td><strong>Édition</strong></td>
<td>Répertorie l'édition entrée lorsque le service BizTalk est créé.</td>
</tr>
<tr>
<td><strong>Emplacement</strong></td>
<td>Affiche la région géographique hébergeant votre service BizTalk.</td>
</tr>
<tr>
<td><strong>Créé</strong></td>
<td>Affiche la date et l'heure de la création du service BizTalk.</td>
</tr>
<tr>
<td><strong>Base de données des suivis</strong></td>
<td>Nom de la base de données SQL Azure stockant les tables de suivi utilisées par votre service BizTalk. 
<br/><br/>La rubrique 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explication des exigences</a> fournit des détails sur la base de données de suivi.</td>
</tr>
<tr>
<td><strong>Stockage de surveillance/archivage</strong></td>
<td>Nom du compte de stockage Azure stockant la sortie de la surveillance de votre service BizTalk.
<br/><br/>La rubrique 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explication des exigences</a> fournit des détails sur le compte de stockage.</td>
</tr>
<tr>
<td><strong>Nom d’abonnement</strong></td>
<td>Répertorie l’abonnement qui héberge votre service BizTalk. L’abonnement gère l’accès.</td>
</tr>
<tr>
<td><strong>Identifiant d’abonnement</strong></td>
<td>Lorsqu’un abonnement est créé, un ID d’abonnement est automatiquement généré. Lors de l’utilisation d’API REST, il vous faudra peut-être entrer l’ID d’abonnement.</td>
</tr>
</table>

[BizTalk Services : Provisionnement](http://go.microsoft.com/fwlink/p/?LinkID=302280) récapitule les étapes de la création d’un service BizTalk.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gérer, Informations de connexion, Clés de synchronisation et Supprimer dans la barre des tâches :
<table border="1">

<tr>
<td><strong>Gérer</strong> le déploiement de votre application</td>
<td>Ouvre le portail Azure BizTalk Services. Celui-ci représente l'accès à la configuration de l'échange de données informatisé (EDI, Electronic Data Interchange), y compris en ce qui concerne l'ajout de partenaires et la création de contrats X12, AS2 et EDIFACT.
<br/><br/>
Cela revient à utiliser <strong>Créer des accords partenaires</strong> sous l’onglet <strong>Démarrage rapide</strong>.
<br/><br/>La rubrique 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuration de composants pour la messagerie EDI sur le portail BizTalk Services</a> fournit des informations supplémentaires sur le portail BizTalk Services.</td>
</tr>
<tr>
<td><strong>Informations de connexion</strong> de l’espace de noms Access Control</td>
<td>Affiche l'espace de noms Access Control, l'émetteur par défaut et les valeurs de clé par défaut qui peuvent être copiés.
<br/><br/>
Vous pouvez également ouvrir le portail Access Control. Utiliser ce portail équivaut à utiliser l’option Active Directory dans le volet de navigation de gauche.
<br/><br/>La rubrique 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Créer un espace de noms Access Control</a> fournit des informations supplémentaires sur le portail Access Control.</td>
</tr>
<tr>
<td><strong>Synchroniser les clés</strong> dans le compte de stockage</td>
<td>Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés de chiffrement contrôlent l'accès à votre compte de stockage. Votre service BizTalk utilise automatiquement la clé primaire. Les <strong>Clés de synchronisation</strong> permettent aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.
<br/><br/>
Supposons que vous vouliez que le service BizTalk utilise une nouvelle clé primaire pour le compte de stockage. Pour ce faire :
<br/><br/>
<ol>
<li>Sélectionnez votre service BizTalk, puis <strong>Clés de synchronisation</strong>. Sélectionnez la clé secondaire. Une fois que c'est fait, le service BizTalk commence à utiliser la clé secondaire.</li>
<li>Sélectionnez votre compte de stockage et regénérez la clé primaire. Rappelez-vous que votre service BizTalk utilise la clé secondaire.</li>
<li>Sélectionnez votre service BizTalk, puis <strong>Clés de synchronisation</strong>. À présent, sélectionnez la clé primaire. Il s'agit de la nouvelle clé primaire que vous avez régénérée.</li>
<li>Sélectionnez votre compte de stockage et regénérez la clé secondaire.</li>
</ol>
<br/>
On parle de « clés de substitution » pour décrire ce processus. L'objectif est de permettre aux utilisateurs de basculer entre la clé primaire et la clé secondaire sans interrompre le service BizTalk.</td>
</tr>

<tr>
<td><strong>Supprimer</strong> votre application</td>
<td>Votre service BizTalk et tous les éléments qui y ont été déployés sont supprimés.</td>
</tr>
</table>


## <a name="monitor"></a>Surveiller
Ne s'applique pas à l'édition gratuite.

Lorsque vous sélectionnez le nom de votre service BizTalk, l'onglet Surveiller est disponible et affiche les éléments suivants :

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Graphique métrique : affiche les mesures de performances sélectionnées.
Ces mesures fournissent des valeurs en temps réel concernant l'intégrité du service BizTalk. Vous choisissez les mesures de performances devant s'afficher (jusqu'à six mesures de performances simultanément). 

Vous pouvez également spécifier les valeurs **Relatives** et **Absolues**, ainsi que l’**Intervalle** de temps des mesures affichées. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Pour supprimer ou afficher des mesures dans le graphique :
1. Sélectionnez l'onglet **Surveiller** .
2. Sélectionnez **Ajouter des métriques** dans la barre des tâches :  
   ![Sélectionnez Ajouter des métriques.][AddMetrics]
3. Vérifiez les mesures de performances que vous souhaitez afficher.
4. Sélectionnez la coche pour revenir à l'onglet **Surveiller** .
5. Sélectionnez le cercle en regard de la mesure pour afficher la valeur associée dans le graphique.  
   
    Par exemple, la mesure **Utilisation du processeur** apparaît en grisé et son résultat n’apparaît pas dans le graphique :  
   ![La mesure Utilisation du processeur apparaît en grisé][GrayedMetric]  
   
    Sélectionnez le cercle grisé pour activer la mesure **Utilisation du processeur** et afficher son résultat dans le graphique :  
   ![La mesure Utilisation du processeur est activée][EnabledMetric]
6. Pour supprimer une mesure du graphique affiché et de la liste, sélectionnez **Supprimer une métrique** dans la barre des tâches. Pour réintégrer la mesure dans la liste, sélectionnez **Ajouter des métriques** dans la barre des tâches, vérifiez la mesure, puis cochez la case pour revenir à l’onglet **Surveiller**. Sélectionnez le cercle en grisé pour activer la mesure.

## <a name="Metrics"></a>Mesures disponibles
Les mesures/compteurs de performances suivants sont disponibles :

<table border="1">

<tr>
<td><strong>Latence aller-retour</strong></td>
<td>Affiche le temps moyen en millisecondes (ms) nécessaire au traitement d'un message depuis sa réception jusqu'à son traitement complet par le service BizTalk sur tous les ponts. Seuls les messages correctement traités sont comptabilisés.<br/><br/>
Lorsque les événements suivants se produisent, un horodatage est créé :
<ul>
<li>Le message accède à la passerelle</li>
<li>Le message est acheminé vers la destination</li>
<li>Une réponse de la destination est reçue</li>
<li>Un accusé de réception est envoyé à la passerelle par la destination</li>
</ul>
<br/>
Cette mesure affiche le résultat du calcul suivant :
<br/><br/>
[Accusé de réception envoyé à la passerelle par la destination] - [Le message accède à la passerelle]</td>
</tr>
<tr>
<td><strong>Défaillances à la source</strong></td>
<td>Affiche le nombre total de messages n'ayant pas pu être traités par le service BizTalk lors de l'extraction de messages à partir des points de terminaison sources.</td>
</tr>
<tr>
<td><strong>Utilisation du processeur</strong></td>
<td>Indique le pourcentage de temps processeur de toutes les instances de rôle.</td>
</tr>
<tr>
<td><strong>Latence de traitement</strong></td>
<td>Affiche le délai moyen en millisecondes (ms) requis pour le traitement d'un message par le service BizTalk sur tous les ponts, à l'exclusion du temps passé au niveau des destinations. Seuls les messages correctement traités sont comptabilisés.<br/><br/>
Lorsque l'un des événements suivants se produit, un horodatage est créé :

<ul>
<li>Le message accède à la passerelle</li>
<li>Le message est acheminé vers la destination</li>
<li>Une réponse de la destination est reçue</li>
<li>Un accusé de réception est envoyé à la passerelle par la destination</li>
</ul>
<br/>Cette mesure affiche le résultat du calcul suivant :<br/><br/>
[Un accusé de réception est envoyé à la passerelle par la destination] - [Le message accède à la passerelle] - [Une réponse de la destination est reçue] + [Le message est acheminé vers la destination]</td>
</tr>
<tr>
<td><strong>Défaillances dans le processus</strong></td>
<td>Affiche le nombre total de messages ayant échoué pendant le traitement par le service BizTalk sur tous les ponts pendant un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages envoyés</strong></td>
<td>Affiche le nombre total de messages envoyés par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un message envoyé à partir d'un pipeline atteint la destination d'itinéraire. Cette mesure n'indique pas qu'un message a été traité correctement.<br/><br/>
Dans un scénario de réponse à la demande, la mesure est incrémentée lorsque la destination d'itinéraire renvoie un accusé de réception au pipeline.</td>
</tr>
<tr>
<td><strong>Messages reçus</strong></td>
<td>Affiche le nombre total de messages reçus par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un nouveau message est reçu par le pipeline.</td>
</tr>
<tr>
<td><strong>Messages en cours de traitement</strong></td>
<td>Affiche le nombre total de messages en cours de traitement par le service BizTalk pendant un intervalle de temps.</td>
</tr>
<tr>
<td><strong>Messages traités</strong></td>
<td>Affiche le nombre total de messages correctement traités par le service BizTalk sur tous les ponts pendant un intervalle de temps. Cette mesure est incrémentée lorsqu'un message est correctement reçu par le pipeline et acheminé vers la destination.</td>
</tr>
</table>


## <a name="scale"></a>Mettre à l'échelle
Dans l'onglet Mettre à l'échelle, vous pouvez ajouter ou soustraire le nombre d'unités utilisées par votre service BizTalk. Par défaut, une seule unité est configurée. Vous pouvez ajouter des unités supplémentaires afin de mettre à l'échelle votre service BizTalk. Lorsque vous augmentez la mise à l'échelle, vous augmentez le débit. La quantité de ressources augmente également, y compris les ponts déployés, les contrats, les connexions métier et la puissance de traitement. Par exemple, vous augmentez la mise à l'échelle de 1 à 2 unités. Dans ce cas, vous pouvez doubler le nombre de ponts, les contrats, les connexions métier et la puissance de traitement.

Certaines éditions BizTalk n'offrent pas de possibilité de mise à l'échelle. Dans ce cas, une seule unité est autorisée. Pour déterminer le nombre d'unités auquel votre édition peut être mise à l'échelle, consultez le [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md).

L'augmentation du nombre d'unités peut avoir un impact sur les tarifs. Si vous augmentez les unités, sélectionnez **Enregistrer** pour afficher un message indiquant s'il y a un impact sur la facturation. Vous choisissez ensuite de continuer. Lorsque vous augmentez le nombre d'unités, le statut du service BizTalk passe d'Actif à Mis à jour. En état de mise à jour, votre service BizTalk continue de s'exécuter.

[Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md) définit une « unité ».

## <a name="configure"></a>Configuration
Ne s'applique pas aux connexions hybrides.

Définit l'état de la sauvegarde sur Aucun ou Automatique. Lorsqu'il est défini sur Aucun, aucune sauvegarde n'est automatiquement créée. Lorsqu'il est défini sur Automatique, vous configurez l'emplacement de la sauvegarde, la fréquence de la sauvegarde et la durée de conservation des fichiers de sauvegarde. 

[Sauvegarde et restauration de BizTalk Services](biztalk-backup-restore.md) fournit les détails. 

## <a name="HybridConnections"></a>Connexions hybrides
Les connexions hybrides connectent une application Azure, telle que Web Apps ou Mobile Services dans Azure App Service, à une ressource locale utilisant un port TCP statique, tel que SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés. Les connexions hybrides sont managées dans BizTalk Services.

Pour créer ou gérer des connexions hybrides dans Azure BizTalk Services, consultez [Connexions hybrides](integration-hybrid-connection-overview.md).

## <a name="next"></a>Suivant
À présent que vous connaissez bien les différents onglets, vous pouvez accéder à davantage d'informations sur les fonctionnalités Azure BizTalk Services :

* [Limitation dans BizTalk Services](biztalk-throttling-thresholds.md)  
* [Nom et clé de l'émetteur dans BizTalk Services](biztalk-issuer-name-issuer-key.md)  
* [Sauvegarde et restauration de BizTalk Services](biztalk-backup-restore.md)

## <a name="see-also"></a>Voir aussi
* [Connexions hybrides](integration-hybrid-connection-overview.md)  
* [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](biztalk-editions-feature-chart.md)  
* [BizTalk Services : Provisionnement](biztalk-provision-services.md)  
* [BizTalk Services : tableau comparatif des états du service BizTalk](biztalk-service-state-chart.md)  
* [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

