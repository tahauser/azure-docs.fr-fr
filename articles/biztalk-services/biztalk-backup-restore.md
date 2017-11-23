---
title: "Créer et restaurer une sauvegarde dans BizTalk Services | Microsoft Docs"
description: "BizTalk Services offre des fonctionnalités de sauvegarde et de restauration. Apprenez à créer et à restaurer une sauvegarde et à déterminer les éléments sauvegardés. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-backup-and-restore"></a>Sauvegarde et restauration de BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services offre des fonctionnalités de sauvegarde et de restauration. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Les connexions hybrides NE sont PAS sauvegardées, quelle que soit l’édition. Vous devez recréer vos connexions hybrides.


## <a name="before-you-begin"></a>Avant de commencer
* Il se peut que les fonctionnalités de sauvegarde et de restauration ne soient pas disponibles dans toutes les éditions. Consultez [BizTalk Services : tableau comparatif des éditions](biztalk-editions-feature-chart.md).
* Le contenu des sauvegardes peut être restauré vers le même service BizTalk ou vers un nouveau service BizTalk. Pour restaurer le service BizTalk à l'aide du même nom, le service BizTalk existant doit être supprimé et le nom doit être disponible. Après avoir supprimé un service BizTalk, la disponibilité du même nom peut prendre du temps. Si vous ne pouvez pas attendre que le même nom soit disponible, restaurez vers un nouveau service BizTalk.
* BizTalk Services peut être restauré vers la même édition ou une édition supérieure. La restauration de BizTalk Services vers une édition inférieure, à partir de la sauvegarde créée, n'est pas prise en charge.
  
    Par exemple, une sauvegarde à l'aide de l'édition De base peut être restaurée vers l'édition Premium alors qu'une sauvegarde dans l'autre sens est impossible.
* Les numéros de contrôle EDI sont sauvegardés pour assurer la continuité des numéros de contrôle. Si des messages sont traités après la dernière sauvegarde, la restauration du contenu de cette sauvegarde peut entraîner des numéros de contrôle en double.
* Si un lot contient des messages actifs, traitez-le **avant** d'effectuer une sauvegarde. Lors de la création d'une sauvegarde (à la demande ou planifiée), les messages contenus dans des lots ne sont jamais stockés. 
  
    **En cas de sauvegarde présentant des messages actifs dans un lot, ceux-ci ne sont pas sauvegardés et sont donc perdus.**
* Facultatif : dans le portail BizTalk Services, arrêtez toutes les opérations de gestion.

## <a name="create-a-backup"></a>Création d'une sauvegarde
Une sauvegarde peut être effectuée à tout moment et vous la contrôlez complètement. Pour créer une sauvegarde, utilisez l’[API REST pour la gestion de BizTalk Services sur Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Restauration
Pour restaurer une sauvegarde, utilisez l’[API REST pour la gestion de BizTalk Services sur Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Après avoir restauré une sauvegarde
Le service BizTalk est systématiquement restauré dans un état **Suspendu** . Dans cet état, vous pouvez apporter des modifications à la configuration avant que le nouvel environnement ne soit fonctionnel :

* Si vous avez créé des applications de service BizTalk à l'aide du Kit de développement logiciel (SDK) Azure BizTalk Services, vous devrez peut-être mettre à jour les informations d'identification Access Control (ACS) qui s'y rapportent pour qu'elles fonctionnent dans l'environnement restauré.
* Vous restaurez un service BizTalk pour répliquer un environnement de service BizTalk existant. Dans ce cas de figure, s'il existe des contrats configurés dans le portail BizTalk Services d'origine utilisant un dossier FTP source, vous devrez peut-être mettre à jour les contrats de l'environnement récemment restauré de manière à utiliser un autre dossier FTP source. Sinon, vous risquez de vous retrouver avec deux contrats différents tentant d'extraire le même message.
* Si vous avez procédé à la restauration pour plusieurs environnements de service BizTalk, veillez à cibler l'environnement approprié dans les applications Visual Studio, applets de commande PowerShell, API REST ou API OM du portail de gestion du partenaire commercial.
* Il est recommandé de configurer des sauvegardes automatisées dans l'environnement du service BizTalk récemment restauré.

## <a name="what-gets-backed-up"></a>Éléments sauvegardés
Dans le cadre d'une sauvegarde, les éléments suivants sont sauvegardés :

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Éléments sauvegardés</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portail Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>Configuration et exécution</td> 
<td>
<ul>
<li>Détails du partenaire et du profil</li>
<li>Contrats partenaires</li>
<li>Assemblys personnalisés déployés</li>
<li>Ponts déployés</li>
<li>Certificats</li>
<li>Transformations déployées</li>
<li>Pipelines</li>
<li>Modèles créés et enregistrés dans le portail BizTalk Services</li>
<li>Mappages X12 ST01 et GS01</li>
<li>Numéros de contrôle (EDI)</li>
<li>Valeurs MIC des messages AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</tr> 
<tr>
<td>Certificat SSL</td> 
<td>
<ul>
<li>Données du certificat SSL</li>
<li>Mot de passe du certificat SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Paramètres du service BizTalk</td> 
<td>
<ul>
<li>Nombre d'unités mises à l'échelle</li>
<li>Édition</li>
<li>Version du produit</li>
<li>Région/centre de données</li>
<li>Espace de noms et clé Access Control Service (ACS)</li>
<li>Chaîne de connexion à la base de données de suivi</li>
<li>Chaîne de connexion au compte de stockage d'archive</li>
<li>Chaîne de connexion au compte de stockage de surveillance</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Autres éléments</strong></td>
</tr> 
<tr>
<td>Base de données des suivis</td> 
<td>Lors de la création du service BizTalk, les détails relatifs à la base de données de suivi sont entrés, y compris le serveur de la base de données SQL Azure et le nom de la base de données de suivi. La base de données de suivi n'est pas automatiquement sauvegardée.
<br/><br/>
<strong>Important</strong><br/>
Si la base de données de suivi est supprimée et qu'elle doit être récupérée, une sauvegarde précédente est nécessaire. En l'absence de sauvegarde précédente, la base de données de suivi et les données associées ne pourront pas être récupérées. Dans ce cas, créez une base de données de suivi portant le même nom. La géo-réplication est recommandée.</td>
</tr> 
</table>

## <a name="next"></a>Suivant
Pour créer Azure BizTalk Services, accédez à [BizTalk Services : Provisionnement](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Voir aussi
* [Sauvegarde d'un service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restauration d'un service BizTalk depuis une sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services : Provisionnement](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Limitation dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

