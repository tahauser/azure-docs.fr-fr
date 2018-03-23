---
title: 'Azure Active Directory Domain Services : Résolution des problèmes de la configuration du protocole LDAP sécurisé | Microsoft Docs'
description: Résolution des problèmes du protocole LDAP sécurisé pour Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: 38885caacfb98f76627854a782aa56d8a609aad9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services : Résolution des problèmes de la configuration du protocole LDAP sécurisé

Cet article donne la résolution des problèmes couramment rencontrés lors de la [configuration du protocole LDAP sécurisé](active-directory-ds-admin-guide-configure-secure-ldap.md) pour Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101 : Configuration du Groupe de sécurité réseau du protocole LDAP sécurisé

**Message d’alerte :**

*Le protocole LDAP sécurisé sur Internet est activé pour le domaine managé. Toutefois, l’accès au port 636 n’est pas verrouillé à l’aide d’un Groupe de sécurité réseau (NSG). Cela peut exposer les comptes d’utilisateurs du domaine managé à des attaques de mots de passe par force brute.*

### <a name="secure-ldap-port"></a>Port LDAP sécurisé

Lorsque le protocole LDAP sécurisé est activé, nous recommandons de créer des règles supplémentaires pour réserver l’accès LDAPS entrant à certaines adresses IP. Ces règles protègent votre domaine contre les attaques par force brute qui pourraient constituer une menace de sécurité. Le port 636 autorise l’accès au domaine managé. Voici comment mettre à jour votre NSG de façon à autoriser l’accès au protocole LDAP sécurisé :

1. Accédez à l’onglet [Groupes de sécurité réseau](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) sur le Portail Azure.
2. Choisissez le NSG associé à votre domaine dans la table.
3. Cliquez sur **Règles de sécurité du trafic entrant**.
4. Créez la règle du port 636.
   1. Cliquez sur **Ajouter** dans la barre de navigation en haut de la page.
   2. Choisissez **Adresses IP** pour la source.
   3. Spécifiez les Plages de ports sources de cette règle.
   4. Entrez « 636 » comme Plages de ports de destination.
   5. Le protocole est **TCP**.
   6. Donnez à la règle un nom, une description et une priorité adaptés. La priorité de cette règle doit être supérieure à celle de votre règle « Tout refuser », le cas échéant.
   7. Cliquez sur **OK**.
5. Vérifiez que votre règle a bien été créée.
6. Consultez l’intégrité de votre domaine après deux heures pour vérifier que vous avez correctement suivi les étapes.

> [!TIP]
> Le port 636 n’est pas la seule règle nécessaire au bon fonctionnement d’Azure AD Domain Services. Pour en savoir plus, consultez les articles traitant des [recommandations en matière de mise en réseau](active-directory-ds-networking.md) ou de la [résolution des problèmes de configuration liés au groupe de sécurité réseau](active-directory-ds-troubleshoot-nsg.md).
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502 : expiration du certificat LDAP sécurisé

**Message d'alerte :**

*Le certificat LDAP sécurisé pour le domaine managé va expirer le XX.*

**Résolution :**

Créez un certificat LDAP sécurisé en suivant les étapes décrites dans l’article [Configurer le protocole LDAPS](active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
