---
title: "Codes d’erreur des rapports d’activité des connexions dans le portail Azure Active Directory | Microsoft Docs"
description: "Informations de référence des codes d’erreur des rapports d’activité des connexions."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e5d477aa74d3d0588e33363888d84e860d4b2f8
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Codes d’erreur des rapports d’activité des connexions dans le portail Azure Active Directory

Les informations indiquées dans le rapport sur les connexions des utilisateurs vous permettent de trouver des réponses aux questions suivantes :

- Qui s’est connecté à l’aide d’Azure Active Directory ?
- À quelles applications l’utilisateur s’est-il connecté ?
- Quelles connexions ont échoué ? Le cas échéant, pourquoi ?

Cette rubrique répertorie les codes d’erreur et les descriptions associées. 

## <a name="how-can-i-display-failed-sign-ins"></a>Comment afficher les connexions en échec ? 

La zone **[Connexions](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** de la section **Activité** **d’Azure Active** constitue le premier point d’entrée pour toutes les activités de connexion.


![Activité de connexion](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Activité de connexion")

Dans votre rapport de connexions, vous pouvez afficher toutes les connexions en échec en sélectionnant **Échec** pour **État de la connexion**.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Activité de connexion")

Cliquez sur un élément de la liste affichée pour ouvrir le panneau **Détails de l’activité : connexions**. Cette vue vous fournit toutes les informations qui sont suivies par Azure Active Directory sur les connexions, notamment le **code d’erreur de connexion** et une **raison de l’échec**.

![Activité de connexion](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Activité de connexion")


Outre le portail Azure, vous pouvez utiliser [l’API de création de rapports](active-directory-reporting-api-getting-started-azure-portal.md) pour accéder aux données des connexions.


La section suivante vous fournit une vue d’ensemble complète de toutes les erreurs possibles, ainsi que les descriptions associées. 

## <a name="error-codes"></a>Codes d’erreur

|Error|Description|
|---|---|
|50001|Le principal du service nommé X est introuvable dans le locataire nommé Y. Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire. Il se peut également que le principal de la ressource soit introuvable dans le répertoire ou non valide.|
|50008|L’assertion SAML est manquante ou configurée de façon incorrecte dans le jeton.|
|50011|L’adresse de réponse est manquante, configurée de façon incorrecte ou bien elle ne correspond pas aux adresses de réponse configurées pour l’application.|
|50012|Un utilisateur a signalé une fraude lors de l’authentification multifacteur.|
|50053|Le compte est verrouillé, car l’utilisateur a essayé de se connecter un trop grand nombre de fois avec un ID d’utilisateur ou un mot de passe incorrects.|
|50054|Un ancien mot de passe est utilisé pour l’authentification.|
|50055|Mot de passe non valide, mot de passe arrivé à expiration entré.|
|50057|Le compte d’utilisateur est désactivé.|
|50058|Aucune information sur l’identité de l’utilisateur ne se trouve dans les informations d’identification indiquées ; l’utilisateur est introuvable dans le locataire ; une demande de connexion en mode silencieux a été envoyée, mais aucun utilisateur n’est connecté ou encore le service n’a pas pu authentifier l’utilisateur.|
|50074|L’utilisateur n’a pas réussi la vérification de l’authentification multifacteur.|
|50079|L’utilisateur doit s’inscrire pour l’authentification du second facteur.|
|50089|La validation du jeton du flux a échoué en raison de l’expiration du jeton flux.|
|50097|L’appareil n'est pas authentifié.|
|50105|L’utilisateur connecté n’est pas affecté à un rôle pour cette application.|
|50126|Nom d’utilisateur ou mot de passe non valides, ou nom d’utilisateur ou mot de passe locaux non valides.|
|50131|Utilisation dans différentes erreurs d’accès conditionnel. Par exemple, état d’appareil Windows incorrect, demande bloquée en raison d’une activité suspecte, d’une stratégie d’accès et de décisions de stratégie de sécurité.|
|50133|La session n’est pas valide en raison de l’expiration ou de la modification récente du mot de passe.|
|50144|Le mot de passe Active Directory de l’utilisateur est arrivé à expiration.|
|65 001|L’application X n’est pas autorisée à accéder à l’application Y, ou l’autorisation a été révoquée. Ou l’utilisateur ou l’administrateur n’ont pas accepté d’utiliser l’application avec ID X. Envoyez une demande d’autorisation interactive pour cet utilisateur et cette ressource. Ou l’utilisateur ou l’administrateur n’ont pas accepté d’utiliser l’application avec ID X. Envoyez une demande d’autorisation à l’administrateur du locataire pour agir au nom de l’application Y pour la ressource Z.|
|65005|La liste d’accès aux ressources requise par l’application ne contient pas d’applications détectables par la ressource ; l’application cliente a demandé un accès à la ressource qui n’était pas spécifié dans sa liste d’accès aux ressources requise ; le service Graph a retourné une demande incorrecte ou la ressource est introuvable.|
|70001|L’application nommée X est introuvable dans le locataire nommé Y. Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être envoyé votre demande d’authentification au locataire incorrect.|
|80001|L’Agent d’authentification ne peut pas se connecter à Active Directory.|
|80002|La demande de validation du mot de passe de l’Agent d’authentification est arrivée à expiration.|
|80003|Réponse non valide reçue par l’Agent d’authentification.|
|80004|Nom d’utilisateur principal (UPN) incorrect utilisé dans la demande de connexion.|
|80005|Agent d’authentification : une erreur s’est produite.|
|80007|L’Agent d’authentification ne peut pas valider le mot de passe.|
|80010|L’Agent d’authentification ne peut pas déchiffrer le mot de passe.|
|80011|L’Agent d’authentification n’a pas pu récupérer la clé de déchiffrement.|
|81001|Le ticket Kerberos de l’utilisateur est trop volumineux.|
|81002|Impossible de valider le ticket Kerberos de l’utilisateur.|
|81003|Impossible de valider le ticket Kerberos de l’utilisateur.|
|81004|Échec de l’authentification Kerberos.|
|81008|Impossible de valider le ticket Kerberos de l’utilisateur.|
|81009|Impossible de valider le ticket Kerberos de l’utilisateur.|
|81010|Échec de l’authentification unique transparente, car le ticket Kerberos de l’utilisateur est arrivé à expiration ou n’est pas valide.|
|81011|L’objet utilisateur basé sur les informations est introuvable dans le ticket Kerberos de l’utilisateur.|
|81012|L’utilisateur qui tente de se connecter à Azure AD est différent de l’utilisateur connecté à l’appareil.|
|81013|Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur.|
|90014|Utilisation dans différents cas lorsqu’un champ attendu est absent des informations d’identification.|
|90093|Graphique retourné avec un code d’erreur interdit pour la demande.|



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, voir [Rapports d’activité des connexions dans le portail Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
