---
title: Azure AD Connect et règlement général sur la protection des données (RGPD) | Microsoft Docs
description: Ce document explique comment obtenir la conformité RGPD avec Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>Conformité à RGPD et Azure AD Connect 

En mai 2018, une loi européenne relative à la confidentialité, le [Règlement général sur la protection des données (RGPD)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), va entrer en vigueur. Le règlement RGPD établit de nouvelles règles que doivent respecter les sociétés, les administrations, les ONG et d’autres organisations qui proposent des biens et services aux personnes de l’Union européenne ou qui collectent et analysent des données liées aux résidents de l’Union européenne. Le règlement RGPD s’applique, quel que soit votre pays de résidence. 

Microsoft met à disposition des produits et des services permettant de mieux répondre aux exigences du règlement RGPD. Pour plus d’informations sur la stratégie Microsoft Privacy, consultez le [Centre de confidentialité](https://www.microsoft.com/trustcenter).

>[!NOTE] 
>Cet article porte sur Azure AD Connect et sur la conformité au règlement RGPD.  Pour plus d’informations sur Azure AD Connect Health et sur la conformité au règlement RGPD, [cliquez ici](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

La conformité au règlement général sur la protection des données des installations Azure AD Connect peut être obtenue de deux manières :

1.  Sur demande, en extrayant les données d’une personne, puis en supprimant ces données des installations
2.  En garantissant qu’aucune donnée n’est conservée plus de 48 heures

L’équipe Azure AD Connect recommande la deuxième option, car elle est beaucoup plus facile à implémenter et à tenir à jour.

Un serveur de synchronisation Azure AD Connect stocke les données suivantes, qui sont concernées par la conformité RGPD :
1.  Données relatives à un individu stockées dans la **base de données Azure AD Connect**
2.  Données stockées dans les fichiers du **Journal des événements Windows** qui peuvent contenir des informations sur un individu
3.  Données stockées dans les **fichiers journaux de l’installation Azure AD Connect** qui peuvent contenir des informations sur un individu

Pour être conforme au règlement RGPD, les clients Azure AD Connect doivent respecter les directives suivantes :
1.  Supprimer régulièrement le contenu du dossier où se trouvent les fichiers journaux de l’installation Azure AD Connect (au moins toutes les 48 heures)
2.  Ce produit peut également créer des journaux d’événements.  Pour plus d’informations sur les journaux d’événements, consultez [cette documentation](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Les données relatives à un individu sont automatiquement supprimées de la base de données Azure AD Connect lorsqu’elles sont supprimées du système source dont elles proviennent. L’obtention de la conformité RGPD ne nécessite aucune action de la part des administrateurs.  Toutefois, elle nécessite que les données d’Azure AD Connect soient synchronisées avec votre source de données au moins tous les deux jours.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Supprimer le contenu du dossier où se trouvent les journaux d’installation Azure AD Connect
Vérifiez régulièrement le contenu du dossier **c:\programdata\aadconnect** et supprimez-le, à l’exception du fichier **PersistedState.Xml**. Ce fichier contient l’état de l’installation précédente d’Azure AD Connect, et est utilisé lorsqu’une mise à niveau est effectuée. Ce fichier ne contient pas de données relatives à un individu et ne doit pas être supprimé.

>[!IMPORTANT]
>Ne supprimez pas le fichier PersistedState.xml.  Ce fichier ne contient aucune information utilisateur. Il contient l’état de l’installation précédente.

Vous pouvez passer en revue et supprimer ces fichiers à l’aide de l’Explorateur Windows, ou vous pouvez utiliser un script comme celui qui suit pour effectuer les actions nécessaires :


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Planifier une exécution de ce script toutes les 48 heures
Suivez les étapes ci-dessous pour planifier l’exécution de ce script toutes les 48 heures.

1.  Enregistrez le script dans un fichier portant l’extension **&#46;PS1**, ouvrez le Panneau de configuration, puis cliquez sur **Systèmes et sécurité**.
    ![Système](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  Sous le titre Outils d’administration, cliquez sur **Tâches planifiées**.
    ![Task](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  Dans le Planificateur de tâches, cliquez avec le bouton droit sur **Bibliothèque du Planificateur de tâches**, puis cliquez sur **Créer une tâche de base...**
4.  Entrez le nom de la nouvelle tâche, puis cliquez sur **Suivant**.
5.  Sélectionnez **Tous les jours** pour le déclencheur, puis cliquez sur **Suivant**.
6.  Choisissez de répéter tous les **2 jours**, puis cliquez sur **Suivant**.
7.  Sélectionnez **Démarrer un programme**, puis cliquez sur **Suivant**.
8.  Tapez **PowerShell** dans le champ Programme/script. Ensuite, dans le champ **Ajouter des arguments (facultatif)**, entrez le chemin complet du script que vous avez créé précédemment, puis cliquez sur **Suivant**.
9.  L’écran suivant montre un récapitulatif de la tâche que vous êtes sur le point de créer. Vérifiez les valeurs, puis cliquez sur **Terminer** pour créer la tâche.



## <a name="next-steps"></a>Étapes suivantes
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health et RGPD](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
