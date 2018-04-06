---
title: 'Services de domaine Azure Active Directory : dépannage des alertes | Microsoft Docs'
description: Dépannage des alertes pour les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 436fa31b9fd1231b38b39d911d9b6c2d4829461d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services : dépannage des alertes
Cet article fournit des guides de dépannage pour les alertes que vous pouvez rencontrer sur votre domaine géré.


Choisissez les étapes de résolution qui correspondent à l’ID ou au message d’erreur de l’alerte.

| **ID de l'alerte** | **Message d'alerte** | **Résolution :** |
| --- | --- | :--- |
| AADDS001 | *LDAP sécurisé sur Internet est activé pour le domaine géré. Toutefois, l’accès au port 636 n’est pas verrouillé à l’aide d’un Groupe de sécurité réseau (NSG). Cela peut exposer les comptes d’utilisateur sur le domaine géré aux attaques par force brute.* | [Configuration de LDAP sécurisé incorrecte](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *L’annuaire Azure AD associé à votre domaine géré a peut-être été supprimé. Le domaine géré n’est plus dans une configuration prise en charge. Microsoft ne peut pas surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [Répertoire manquant](#aadds100-missing-directory) |
| AADDS101 | *Les services de domaine Azure AD ne peuvent pas être activés dans un annuaire Azure AD B2C.* | [Azure AD B2C est en cours d’exécution dans ce répertoire](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Un principal de service requis pour que les services de domaine Azure AD fonctionnent correctement a été supprimé de votre annuaire Azure AD. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [Principal de service manquant](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *La plage d’adresses IP pour le réseau virtuel dans lequel vous avez activé les services de domaine Azure AD est dans une plage d’adresses IP publiques. Les services de domaine Azure AD doivent être activés dans un réseau virtuel avec une plage d’adresses IP privées. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.* | [L’adresse est dans une plage d’adresses IP publiques](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft ne peut pas atteindre les contrôleurs de domaine pour ce domaine géré. Cela peut se produire si un groupe de sécurité réseau (NSG) configuré sur votre réseau virtuel bloque l’accès à un domaine géré. Une autre raison possible est s’il existe un itinéraire défini par l’utilisateur qui bloque le trafic entrant à partir d’Internet.* | [Erreur réseau](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *Le principal du service avec l’application ID « d87dcbc6-a371-462e-88e3-28ad15ec4e64 » a été supprimé puis recréé. Ce principal du service gère un autre principal du service et une application utilisés pour la synchronisation du mot de passe. Le principal du service managé et/ou l’application ne sont pas autorisés dans le principal du service nouvellement créé, ils ne peuvent donc pas être managés par notre service. Cela signifie que le principal du service nouvellement créé ne pourra pas mettre à jour les anciennes applications managées et que la synchronisation des mots de passe sera affectée.* | [La synchronisation du mot de passe est obsolète](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *La dernière synchronisation du domaine managé avec Azure AD a eu lieu le [date]. Les utilisateurs sont peut-être dans l’impossibilité de se connecter au domaine managé, ou les appartenances aux groupes ne sont peut-être pas synchronisées avec Azure AD.* | [Il n’y a pas eu de synchronisation depuis un certain temps.](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *La dernière sauvegarde du domaine managé a eu lieu le [date].* | [Il n’y a pas eu de sauvegarde depuis un certain temps.](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *Le certificat LDAP sécurisé pour le domaine managé va expirer le XX.* | [Expiration du certificat LDAP sécurisé](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *Le domaine managé est suspendu, car l’abonnement Azure associé au domaine n’est pas actif.* | [Suspension en raison de l’abonnement désactivé](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *Le domaine managé est suspendu en raison d’une configuration non valide. Le service n’a pas pu gérer, corriger ou mettre à jour les contrôleurs du domaine managé depuis un certain temps.* | [Suspension en raison d’une configuration non valide](#aadds504-suspension-due-to-an-invalid-configuration) |


## <a name="aadds100-missing-directory"></a>AADDS100 : Répertoire manquant
**Message d'alerte :**

*L’annuaire Azure AD associé à votre domaine géré a peut-être été supprimé. Le domaine géré n’est plus dans une configuration prise en charge. Microsoft ne peut pas surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

**Résolution :**

Cette erreur est généralement causée par un déplacement incorrect de votre abonnement Azure vers un nouvel annuaire Azure AD, et par la suppression de l’ancien répertoire Azure AD qui est toujours associé à des services de domaine Azure AD.

Cette erreur est irrécupérable. Pour résoudre ce problème, vous devez [supprimer votre domaine géré existant](active-directory-ds-disable-aadds.md) puis le recréer dans votre nouveau répertoire. Si vous avez des difficultés à le supprimer, contactez l’équipe produit des services de domaine Azure Active Directory [pour obtenir de l’aide](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101 : Azure AD B2C est en cours d’exécution dans ce répertoire
**Message d'alerte :**

*Les services de domaine Azure AD ne peuvent pas être activés dans un annuaire Azure AD B2C.*

**Résolution :**

>[!NOTE]
>Pour pouvoir continuer à utiliser les services de domaine Azure AD, vous devez recréer votre instance de services de domaine Azure AD dans un répertoire hors Azure AD B2C.

Pour restaurer votre service, procédez comme suit :

1. [Supprimez votre domaine géré](active-directory-ds-disable-aadds.md) de votre annuaire Azure AD existant.
2. Créez un répertoire qui n’est pas un répertoire Azure AD B2C.
3. Suivez le guide de [Prise en main](active-directory-ds-getting-started.md) pour recréer un domaine géré.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103 : L’adresse est dans une plage d’adresses IP publiques

**Message d'alerte :**

*La plage d’adresses IP pour le réseau virtuel dans lequel vous avez activé les services de domaine Azure AD est dans une plage d’adresses IP publiques. Les services de domaine Azure AD doivent être activés dans un réseau virtuel avec une plage d’adresses IP privées. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

**Résolution :**

> [!NOTE]
> Pour résoudre ce problème, vous devez supprimer votre domaine géré existant et le créer de nouveau dans un réseau virtuel avec une plage d’adresses IP privées. Ce processus cause une interruption.

Avant de commencer, lisez la section **Espace d’adressage IPv4** de [cet article](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

À l’intérieur du réseau virtuel, les machines peuvent effectuer des requêtes sur les ressources Azure qui se trouvent dans la même plage d’adresses IP que celles configurées pour le sous-réseau. Toutefois, étant donné que le réseau virtuel est configuré pour cette plage, ces requêtes sont routées au sein du réseau virtuel et n’atteignent pas les ressources web prévues. Cette configuration risque d’entraîner des erreurs imprévisibles avec Azure AD Domain Services.

**Si vous avez la plage d’adresses IP dans l’Internet configuré dans votre réseau virtuel, vous pouvez ignorer cette alerte. Il est toutefois impossible pour Azure AD Domain Services de respecter les termes du contrat [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) avec cette configuration, car elle peut entraîner des erreurs imprévisibles.**


1. [Supprimez votre domaine géré](active-directory-ds-disable-aadds.md) de votre annuaire.
2. Corrigez la plage d’adresses IP pour le sous-réseau
  1. Accédez à la [page Réseaux virtuels sur le portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Sélectionnez le réseau virtuel que vous envisagez d’utiliser pour les services de domaine Azure AD.
  3. Cliquez sur **Espace d’adressage** sous Paramètres
  4. Mettez à jour la plage d’adresses en cliquant sur la plage d’adresses existante et en la modifiant ou en ajoutant une plage d’adresses supplémentaire. Assurez-vous que la nouvelle plage d’adresses est dans une plage d’adresses IP privées. Enregistrez vos modifications.
  5. Cliquez sur **Sous-réseaux** dans la navigation de gauche.
  6. Cliquez sur le sous-réseau que vous souhaitez modifier dans la table.
  7. Mettez à jour la plage d’adresses et enregistrez vos modifications.
3. Suivez [le guide Prise en main des services de domaine Azure AD](active-directory-ds-getting-started.md) pour recréer votre domaine géré. Veillez à sélectionner un réseau virtuel avec une plage d’adresses IP privées.
4. Pour joindre vos machines virtuelles à votre nouveau domaine, suivez [ce guide](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Pour vérifier que l’alerte est résolue, vérifiez l’intégrité de votre domaine dans deux heures.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500 : La synchronisation ne parvient pas à se terminer depuis un certain temps.

**Message d'alerte :**

*La dernière synchronisation du domaine managé avec Azure AD a eu lieu le [date]. Les utilisateurs sont peut-être dans l’impossibilité de se connecter au domaine managé, ou les appartenances aux groupes ne sont peut-être pas synchronisées avec Azure AD.*

**Résolution :**

[Vérifiez l’intégrité de votre domaine](active-directory-ds-check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Dans certains cas, les problèmes de configuration peuvent empêcher Microsoft de synchroniser votre domaine managé. Si vous êtes en mesure de résoudre les alertes, patienter deux heures et vérifiez si la synchronisation est terminée.


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501 : Il n’y a pas eu de sauvegarde depuis un certain temps.

**Message d'alerte :**

*La dernière sauvegarde du domaine managé a eu lieu le [date].*

**Résolution :**

[Vérifiez l’intégrité de votre domaine](active-directory-ds-check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Dans certains cas, les problèmes de configuration peuvent empêcher Microsoft de synchroniser votre domaine managé. Si vous êtes en mesure de résoudre les alertes, patienter deux heures et vérifiez si la synchronisation est terminée.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503 : Suspension en raison de l’abonnement désactivé

**Message d'alerte :**

*Le domaine managé est suspendu, car l’abonnement Azure associé au domaine n’est pas actif.*

**Résolution :**

Pour restaurer votre service, [renouvelez l’abonnement Azure](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable) associé à votre domaine managé.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504 : Suspension en raison d’une configuration non valide

**Message d'alerte :**

*Le domaine managé est suspendu en raison d’une configuration non valide. Le service n’a pas pu gérer, corriger ou mettre à jour les contrôleurs du domaine managé depuis un certain temps.*

**Résolution :**

[Vérifiez l’intégrité de votre domaine](active-directory-ds-check-health.md) : recherchez les alertes susceptibles d’indiquer des problèmes dans la configuration de votre domaine managé. Si vous pouvez résoudre une de ces alertes, faites-le. Ensuite, contactez le support pour réactiver votre abonnement.

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
