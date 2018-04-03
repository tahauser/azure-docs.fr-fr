---
title: Résoudre les problèmes de sauvegarde des fichiers Azure
description: Cet article contient des informations de dépannage concernant les problèmes qui se produisent lors de la protection de vos partages de fichiers Azure.
services: backup
ms.service: backup
keywords: N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: c803118ccdafa8db0e8f8ddee608f60311f65e05
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-problems-backing-up-azure-files"></a>Résoudre les problèmes de sauvegarde des fichiers Azure
Vous pouvez résoudre les problèmes et les erreurs rencontrés pendant l’utilisation d’une sauvegarde de fichiers Azure à l’aide des informations figurant dans les tables suivantes.

## <a name="preview-boundaries"></a>Limites de la préversion
Sauvegarde de fichiers Azure est en préversion. Les scénarios de sauvegarde suivants ne sont pas pris en charge pour les partages de fichiers Azure :
- Protection des partages de fichiers Azure dans des comptes de stockage disposant d’une réplication de [stockage redondant dans une zone](../storage/common/storage-redundancy.md#zone-redundant-storage) (ZRS) ou de [stockage géoredondant avec accès en lecture](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS).
- Protection des partages de fichiers Azure dans des comptes de stockage qui ont activé les réseaux virtuels.
- Sauvegarde des partages de fichiers Azure à l’aide de PowerShell ou CLI.

### <a name="limitations"></a>Limites
- Le nombre maximum de sauvegardes planifiées par jour est de 1.
- Le nombre maximum de sauvegardes à la demande par jour est de 4.
- Utilisez les verrous de ressources sur le compte de stockage pour empêcher la suppression accidentelle des sauvegardes de votre coffre Recovery Services.
- Ne supprimez pas les instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration

## <a name="configuring-backup"></a>Configuration de la sauvegarde
Le tableau suivant concerne la configuration de la sauvegarde :

| Configuration de la sauvegarde | Conseils de résolution ou solution de contournement |
| ------------------ | ----------------------------- |
| Mon compte de stockage pour configurer la sauvegarde du partage de fichiers Azure est introuvable | <ul><li>Attendez la fin de la découverte. <li>Vérifiez si un partage de fichiers du compte de stockage est déjà protégé par un autre coffre Recovery Services. **Remarque**: tous les partages de fichiers dans un compte de stockage peuvent être protégés sous un seul coffre Recovery Services. <li>Assurez-vous que le partage de fichiers ne fait pas partie d’un compte de stockage non pris en charge.|
| Erreur : échec de la découverte des états du portail des comptes de stockage. | Si votre abonnement est partenaire (CSP activé), ignorez l’erreur. Si votre abonnement ne dispose pas du CSP activé, et que vos comptes de stockage ne peuvent pas être découverts, contactez le support technique.|
| Échec de la validation ou de l’enregistrement du compte de stockage sélectionné.| Réessayez l’opération et contactez le support technique si le problème persiste.|
| Impossible de répertorier ou de trouver les partages de fichiers du compte de stockage sélectionné. | <ul><li> Assurez-vous que le compte de stockage existe dans le groupe de ressources (et qu’il n’a pas été supprimé ou déplacé après la dernière validation ou le dernier enregistrement dans le coffre).<li>Assurez-vous que le partage de fichiers que vous souhaitez protéger n’a pas été supprimé. <li>Assurez-vous que le compte de stockage est bien pris en charge par la sauvegarde de partage de fichiers.<li>Vérifiez si le partage de fichiers est déjà protégé dans le même coffre Recovery Services.|
| La configuration de la sauvegarde de partage de fichiers (ou la configuration de la stratégie de protection) échoue. | <ul><li>Réessayez l’opération pour voir si le problème persiste. <li> Assurez-vous que le partage de fichiers que vous souhaitez protéger n’a pas été supprimé. <li> Si vous essayez de protéger plusieurs partages de fichiers en même temps, et si l’échec touche certains partages de fichiers, recommencez la configuration de la sauvegarde pour les partages de fichiers ayant échoué. |
| Impossible de supprimer le coffre Recovery Services après avoir ôté la protection d’un partage de fichiers. | Dans le portail Azure, ouvrez **Infrastructure de sauvegarde** > **Comptes de stockage** et cliquez sur **Annuler l’enregistrement** pour supprimer le compte de stockage du coffre Recovery Services.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Messages d’erreur pour les échecs des travaux de sauvegarde ou de restauration

| messages d'erreur | Conseils de résolution ou solution de contournement |
| -------------- | ----------------------------- |
| Échec de l’opération car le partage de fichiers est introuvable. | Assurez-vous que le partage de fichiers que vous souhaitez protéger n’a pas été supprimé.|
| Compte de stockage introuvable ou non pris en charge. | <ul><li>Assurez-vous que le compte de stockage existe dans le groupe de ressources et qu’il n’a pas été supprimé ou retiré après la dernière validation. <li> Assurez-vous que le compte de stockage est bien pris en charge par la sauvegarde de partage de fichiers.|
| Vous avez atteint la limite maximale d’instantanés pour ce partage de fichiers, vous serez en mesure d’en effectuer davantage une fois que les anciens ont expiré. | <ul><li> Cette erreur peut se produire lorsque vous créez plusieurs sauvegardes à la demande pour un fichier. <li> Il existe une limite de 200 instantanés par partage de fichiers, y compris ceux pris par Azure Backup. Les anciennes sauvegardes (ou instantanés) planifiées sont nettoyées automatiquement. Les sauvegardes (ou instantanés) à la demande doivent être supprimées si la limite maximale est atteinte.<li> Supprimez les sauvegardes à la demande (instantanés du partage de fichiers Azure) à partir du portail de fichiers Azure. **Remarque**: Si vous supprimez les instantanés créés par Sauvegarde Azure, vous perdez les points de récupération. |
| La sauvegarde ou la restauration du partage de fichiers a échoué en raison d’une limitation de bande passante du service de stockage. Cela peut être dû au fait que le service de stockage est occupé à traiter d’autres demandes pour le compte de stockage donné.| Réessayez l’opération après un certain temps. |
| Échec de la restauration avec le partage de fichier cible introuvable. | <ul><li>Assurez-vous que le compte de stockage sélectionné existe et que le partage de fichiers cible n’est pas supprimé. <li> Assurez-vous que le compte de stockage est bien pris en charge par la sauvegarde de partage de fichiers. |
| La sauvegarde Azure n’est pas prise en charge pour les fichiers Azure contenus dans des comptes de stockage ayant activé les réseaux virtuels. | Désactivez les réseaux virtuels sur votre compte de stockage pour assurer la réussite des opérations de sauvegarde ou de restauration. |
| Échec des travaux de sauvegarde ou de restauration en raison de l’état verrouillé du compte de stockage. | Supprimez le verrou sur le compte de stockage ou utilisez supprimer verrou au lieu de lire verrou et recommencez l’opération. |
| Échec de la récupération, car le nombre de fichiers ayant échoué dépasse le seuil. | <ul><li> Les raisons des échecs de la récupération sont répertoriées dans un fichier (chemin d’accès fourni dans les détails du travail). Traitez les échecs et recommencez l’opération de restauration uniquement pour les fichiers ayant échoué. <li> Causes courantes des échecs d’une restauration de fichiers : <br/> -vérifiez que les fichiers ayant échoué ne sont actuellement pas en cours d’utilisation, <br/> -un répertoire portant le même nom que le fichier ayant échoué existe dans le répertoire parent. |
| Échec de la récupération, car aucun fichier ne peut être récupéré. | <ul><li> Les raisons des échecs de la récupération sont répertoriées dans un fichier (chemin d’accès fourni dans les détails du travail). Traitez les échecs et recommencez l’opération de restauration uniquement pour les fichiers ayant échoué. <li> Causes courantes d’un échec de restauration de fichiers : <br/> -vérifiez que les fichiers ayant échoué ne sont actuellement pas en cours d’utilisation. <br/> -un répertoire portant le même nom que le fichier ayant échoué existe dans le répertoire parent. |
| Échec de la restauration, car l’un des fichiers de la source n’existe pas. | <ul><li> Les éléments sélectionnés ne sont pas présents dans les données du point de récupération. Pour restaurer les fichiers, fournissez la liste de fichiers correcte. <li> L’instantané du partage de fichiers qui correspond au point de récupération est supprimé manuellement. Sélectionnez un autre point de récupération et recommencez l’opération de restauration. |
| Un travail de récupération est en cours à la même destination. | <ul><li>La sauvegarde de partage de fichiers ne prend pas en charge la récupération parallèle au même partage de fichiers cible. <li>Attendez que la récupération en cours se termine, puis réessayez. Si vous ne trouvez pas un travail de récupération dans le coffre Recovery Services, vérifiez les autres coffres Recovery Services de l’abonnement. |
| Échec de l’opération de restauration, car le partage de fichiers cible est plein. | Augmentez le quota de taille du partage de fichiers cible pour prendre en charge les données de restauration, puis recommencez l’opération. |
| Un ou plusieurs fichiers n’ont pas pu être récupérés avec succès. Pour plus d’informations, vérifiez la liste des fichiers ayant échoué dans le chemin d’accès indiqué ci-dessus. | <ul> <li> Les raisons d’un échec de récupération sont répertoriées dans le fichier (chemin d’accès fourni dans les détails du travail), traitez les raisons et recommencez l’opération de restauration uniquement pour les fichiers ayant échoué. <li> Les causes courantes des échecs d’une restauration de fichiers sont : <br/> -vérifiez que les fichiers ayant échoué ne sont pas en cours d’utilisation. <br/> -un répertoire portant le même nom que les fichiers ayant échoué existe dans le répertoire parent. |

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur la sauvegarde des partages de fichiers Azure, consultez les références suivantes :
- [Sauvegarder des partages de fichiers Azure](backup-azure-files.md)
- [FAQ sur la sauvegarde des partages de fichiers Azure](backup-azure-files-faq.md)
