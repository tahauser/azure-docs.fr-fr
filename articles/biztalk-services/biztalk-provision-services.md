---
title: "Créer des services Azure BizTalk Services dans le portail Azure | Microsoft Docs"
description: "Apprenez à approvisionner ou à créer des services Azure BizTalk Services dans le portail Azure ; MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 61776b19ba0ee273b78e3b0a6f610e5701251dd0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Créer des services BizTalk Services à l’aide du portail Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Pour vous connecter au portail Azure, vous devez disposer d’un compte Azure et d’un abonnement Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Consultez [Version d'évaluation gratuite d'Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Créer un service BizTalk

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Selon l'état du service BizTalk, certaines opérations ne peuvent pas être effectuées. Pour en obtenir la liste, consultez [BizTalk Services : Tableau comparatif des états](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Étapes postérieures à l’approvisionnement
* [Installer le certificat sur un ordinateur local](#InstallCert)
* [Ajouter un certificat prêt pour la production](#AddCert)
* [Obtenir l'espace de noms Access Control](#ACS)

#### <a name="InstallCert"></a>Installer le certificat sur un ordinateur local

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Ajouter un certificat prêt pour la production

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Obtenir l'espace de noms Access Control

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms de contrôle d'accès. L’espace de noms Access Control est automatiquement créé pour votre service BizTalk.

Les valeurs Access Control values peuvent être utilisées avec n'importe quelle application. Lorsqu'Azure BizTalk Services est créé, cet espace de noms Access Control contrôle l'authentification avec votre déploiement de service BizTalk. Si vous souhaitez modifier l'abonnement ou gérer l'espace de noms, sélectionnez **ACTIVE DIRECTORY** dans le volet de navigation gauche, puis votre espace de noms. La barre des tâches affiche la liste des options.

Cliquez sur **Manage** pour ouvrir le portail de gestion Access Control. Dans le portail de gestion de contrôle d’accès, le service BizTalk utilise les **identités de service** :  
![Identités de service ACS dans le portail de gestion de contrôle d'accès][ACSServiceIdentities]

L'identité de service Access Control est un ensemble d'informations d'identification qui permet aux applications ou aux clients de s'authentifier directement auprès du contrôle d'accès et de recevoir un jeton.

> [!IMPORTANT]
> Le service BizTalk utilise **Propriétaire** comme identité de service par défaut et la valeur **Mot de passe**. Si vous utilisez la valeur de clé symétrique plutôt que la valeur de mot de passe, l’erreur ci-après peut survenir.<br/><br/>*Impossible de se connecter au compte de service de gestion de contrôle d’accès avec les informations d’identification spécifiées.*
> 
> 

[Gestion de votre espace de noms ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx) répertorie quelques instructions et recommandations.

## <a name="requirements-explained"></a>Explication des exigences
Ces exigences ne s’appliquent pas à l’édition gratuite.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Ce dont vous avez besoin</strong></td>
        <td><strong>Raison</strong></td>
</tr>
<tr>
<td>Abonnement Azure</td>
<td>L’abonnement détermine qui peut se connecter à Azure. Le titulaire du compte crée l’abonnement dans <a HREF="https://account.windowsazure.com/Subscriptions"> Abonnements Azure</a>.
<br/><br/>
Le compte Azure peut avoir plusieurs abonnements et peut être géré par toute personne y étant autorisée. Par exemple, le titulaire de votre compte Azure crée un abonnement appelé <em>BizTalkServiceSubscription</em> et donne aux administrateurs BizTalk de votre société (par exemple, ContosoBTSAdmins@live.com) un accès à cet abonnement. Dans ce scénario, les administrateurs BizTalk se connectent à Azure et disposent des droits d’administrateur complets pour tous les services hébergés dans l’abonnement, y compris Azure BizTalk Services. Comme ils ne sont pas les titulaires du compte Azure, ils n'ont aucun accès aux informations de facturation.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> L’article relatif à la gestion des abonnements et des comptes de stockage dans Azure</a> fournit des informations complémentaires.
</td>
</tr>
<tr>
<td>Base de données SQL Azure</td>
<td>Stocke les tables, vues et procédures stockées utilisées par le service BizTalk, y compris les données de suivi.
<br/><br/>
Lorsque vous créez un service BizTalk, vous pouvez utiliser un serveur SQL Azure existant, une base de données SQL Azure existante ou créer automatiquement un serveur ou une base de données.
<br/><br/>
La mise à l'échelle de la base de données SQL est automatiquement configurée. En général, la mise à l'échelle par défaut est suffisante pour un service BizTalk. La modification de la mise à l'échelle a des conséquences sur la tarification. Voir <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Comptes et facturation dans la base de données SQL Azure</a>
<br/><br/>
<strong>Remarques</strong>
<br/>
<ul>
<li> Lorsque vous créez un serveur et une base de données SQL Azure, les services Azure sont automatiquement activés. Le service BizTalk nécessite l'activation des services Azure.</li>
<li>Si vous créez une base de données SQL Azure sur un serveur SQL Azure existant, les règles de pare-feu du serveur ne sont pas modifiées. Il est donc possible que d'autres services Azure ne soient pas autorisés à accéder aux bases de données du serveur.</li>
</ul>
</td>
</tr>
<tr>
<td>Espace de noms de contrôle d'accès Azure</td>
<td>Permet de s'authentifier auprès d'Azure BizTalk Services. Lorsque vous déployez un projet BizTalk Services à partir de Visual Studio, vous entrez cet espace de noms de contrôle d'accès. Lorsque vous créez un service BizTalk, l’espace de noms Access Control est automatiquement créé.</td>
</tr>

<tr>
<td>un compte Azure Storage.</td>
<td>Donne accès aux tables, objets blob et files d'attente utilisés par votre service BizTalk pour enregistrer ce qui suit :

<ul>
<li>Fichiers journaux qui surveillent le service BizTalk. </li>
<li>Lors de la création d'un contrat X12 ou AS2 entre des partenaires, vous pouvez activer la fonctionnalité d'archivage pour stocker les propriétés des messages. Ces données sont enregistrées dans le compte de stockage.</li>
</ul>
<br/>
Lorsque vous créez un service BizTalk, vous pouvez utiliser un compte de stockage existant ou en créer un automatiquement.
<br/><br/>
Les paramètres de stockage par défaut sont suffisants pour un service BizTalk.
<br/><br/>
Lorsque vous créez un compte de stockage, une clé primaire et une clé secondaire sont automatiquement créées. Ces clés contrôlent l’accès à votre compte de stockage. Le service BizTalk utilise automatiquement la clé primaire.
<br/><br/>
Pour plus d’informations, consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Stockage</a>.
</td>
</tr>

<tr>
<td>Certificat privé SSL</td>
<td>
Quand un service Azure BizTalk est créé, une URL HTTPS qui inclut le nom de votre service BizTalk est également créée. Cette URL est automatiquement configurée pour utiliser un certificat auto-signé à des fins de développement uniquement. Pour la production, vous avez besoin d'un certificat SSL privé.
<br/><br/>
<strong>Informations importantes sur le certificat SSL</strong>

<ul>
<li>La date d'expiration du certificat doit être comprise dans les 5 prochaines années.</li>
<li>Tous les certificats privés exigent un mot de passe. Retenez ce mot de passe. Il est également recommandé de communiquer ce mot de passe à vos administrateurs.</li>
<li>Les certificats auto-signés sont utilisés dans des environnements de test/développement. Lorsque vous utilisez des certificats auto-signés, importez le certificat dans votre magasin de certificats personnels et dans le magasin de certificats Autorités de certification racines de confiance.</li>
</ul>
<br/>Lorsque vous envoyez la demande de certificat de production à votre autorité de certification, indiquez les propriétés de certificat suivantes :
<br/>

<ul>
<li><strong>Utilisation avancée de la clé</strong> : au minimum, Azure BizTalk Services exige l’authentification du serveur.</li>
<li><strong>Nom commun</strong> : entrez le nom de domaine complet (FQDN) de l’URL de votre service Azure BizTalk. Voir <a HREF="#CreateService">Création d’un service BizTalk</a> dans cet article.</li>
</ul>
<br/>
Un certificat nouveau ou différent peut être ajouté après la création du service BizTalk.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>les connexions hybrides
Quand vous créez un service Azure BizTalk, l'onglet **Connexions hybrides** est disponible :

![Onglet Connexions hybrides][HybridConnectionTab]

Les connexions hybrides permettent de connecter un site web Azure ou un service mobile Azure à toute ressource locale utilisant un port TCP statique, par exemple SQL Server, MySQL, les API web HTTP, Mobile Services et la plupart des services web personnalisés.  Les connexions hybrides et le service d'adaptateur BizTalk sont différents. Le service d'adaptateur BizTalk permet de connecter Azure BizTalk Services à un système métier local.

 Pour en savoir plus, en particulier sur la création et la gestion des connexions hybrides, consultez [Connexions hybrides](integration-hybrid-connection-overview.md) .

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé un service BizTalk, passez en revue les différents [onglets de BizTalk Services : Tableau de bord, Surveiller et Mettre à l’échelle](biztalk-dashboard-monitor-scale-tabs.md). Votre service BizTalk est prêt pour vos applications. Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Voir aussi
* [Tableau comparatif des éditions de BizTalk Services](biztalk-editions-feature-chart.md)<br/>
* [Tableau comparatif des états du service BizTalk](biztalk-service-state-chart.md)<br/>
* [Sauvegarde et restauration de BizTalk Services](biztalk-backup-restore.md)<br/>
* [Limitation dans BizTalk Services](biztalk-throttling-thresholds.md)<br/>
* [Nom et clé de l'émetteur dans BizTalk Services](biztalk-issuer-name-issuer-key.md)<br/>
* [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Connexions hybrides](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
