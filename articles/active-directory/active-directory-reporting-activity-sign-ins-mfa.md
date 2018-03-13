---
title: "Référence pour la génération de rapports d’authentification multifacteur dans le portail Azure | Microsoft Docs"
description: "Informations de référence pour la génération de rapports d’authentification multifacteur dans le portail Azure"
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
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: cb4ef0e361cb3495a03e5a7ed49d9a84a54dbdc3
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Référence pour la génération de rapports d’authentification multifacteur dans le portail Azure

Grâce à la [fonction de création de rapports Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) dans le [portail Azure](https://portal.azure.com), vous pouvez obtenir les informations dont vous avez besoin pour évaluer l’état de votre environnement.

Les [rapports d’activité de connexion](active-directory-reporting-activity-sign-ins.md) vous fournissent des informations sur l’utilisation des applications gérées et des activités de connexion des utilisateurs, y compris des informations sur l’utilisation de l’authentification multifacteur (MFA). 

Les données d’authentification multifacteur vous donnent un aperçu du fonctionnement de l’authentification multifacteur dans votre organisation. Elles vous permettent de répondre aux questions telles que : 

- La connexion a-t-elle été protégée avec l’authentification multifacteur ? 

- Comment l’utilisateur a-t-il effectué l’authentification multifacteur ? 

- Pourquoi l’utilisateur était-il incapable d’effectuer l’authentification multifacteur ?  

En regroupant toutes les données de connexion, vous pouvez mieux comprendre l’expérience de l’authentification multifacteur au sein de votre organisation. Ces données vous permettent de répondre aux questions telles que : 

- Combien d’utilisateurs sont-ils invités à utiliser l’authentification multifacteur ?  

- Combien d’utilisateurs n’ont pas pu résoudre le défi de l’authentification multifacteur ? 

- Quels sont les problèmes courants d’authentication multifacteur auxquels les utilisateurs sont confrontés ? 


Ces données sont disponibles via le portail Azure et l’[API de génération de rapports](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Structure des données


Les rapports d’activité de connexion pour l’authentification multifacteur vous permettent d’accéder aux informations suivantes :

**Authentification multifacteur requise :** spécifie si la MFA est nécessaire pour la connexion ou non. L’authentification multifacteur peut être nécessaire en raison de l’authentification multifacteur par utilisateur, de l’accès conditionnel ou d’autres raisons. Les valeurs possibles sont `Yes` ou `No`.

**Méthode d’authentification multifacteur :** la méthode d’authentification que l’utilisateur a utilisé pour effectuer l’authentification multifacteur. Les valeurs possibles sont les suivantes : 

- SMS 

- Notification sur l’application mobile 

- Appel téléphonique (téléphone d’authentification) 

- Code de vérification de l’application mobile 

- Appel téléphonique (téléphone du bureau) 

- Appel téléphonique (téléphone d’authentification alternatif) 

**Détail d’authentification MFA :** version nettoyée du numéro de téléphone, par exemple : + X XXXXXXXX64. 

**Résultat de l’authentification multifacteur :** plus d’informations indiquant si l’authentification multifacteur a été réussie ou refusée :

- Si l’authentification multifacteur a été réussie, cette colonne fournit plus d’informations sur la façon dont l’authentification multifacteur a été satisfaite. 

- Si l’authentification multifacteur a été refusée, cette colonne fournit la raison de refus. Les valeurs possibles sont `Satisfied` ou `Denied`. 

La section suivante répertorie les valeurs de chaîne possibles pour le champ de résultat de l’authentification multifacteur.

## <a name="status-strings"></a>Chaînes d’état

Cette section répertorie les valeurs possibles pour la chaîne d’état de résultat de l’authentification multifacteur.

### <a name="satisfied-status-strings"></a>Chaînes d’état satisfait


- Azure Multi-Factor Authentication

    - achevée dans le cloud 

    - a expiré en raison des politiques configurées pour l’abonné 

    - inscription demandée 

    - satisfaite par une revendication dans le jeton 

    - satisfaite par une revendication dans le jeton 

    - satisfaite par une revendication dans le jeton 

    - satisfaite par une revendication dans le jeton 

    - satisfaite par une revendication fournie par un fournisseur externe 

    - satisfaite par l’authentification forte 

    - ignorée, car le flux testé était un flux d’ouverture de session de répartiteur de service Windows 

    - ignorée, car le flux testé était un flux d’ouverture de session de répartiteur de service Windows 

    - ignorée en raison de mot de passe 

    - ignorée en raison d’emplacement 

    - ignorée en raison de l’appareil enregistré 
    
    - ignorée en raison de l’appareil mémorisé 

    - réussie 

- Redirigé vers un fournisseur externe pour l’authentification multifacteur 

 
### <a name="denied-status-strings"></a>Chaînes d’état refusé

Azure Multi-Factor Authentication refusée ; 

- authentification en cours 

- double tentative d’authentification 

- code erroné entré beaucoup de fois 

- authentification invalide 

- code de vérification de l’application mobile incorrect 

- configuration incorrecte 

- appel téléphonique passé à la messagerie vocale 

- numéro de téléphone de format invalide 

- erreur de service 

- téléphone de l’utilisateur injoignable 

- Impossible d’envoyer la notification d’application mobile à l’appareil 

- Impossible d’envoyer la notification d’application mobile 

- refus de l’authentification par l’utilisateur 

- l’utilisateur n’a pas répondu à la notification d’application mobile 

- l’utilisateur ne dispose pas de toutes les méthodes de vérification enregistrés 

- l’utilisateur a entré un code erroné 

- l’utilisateur a entré un code PIN erroné 

- l’utilisateur a raccroché l’appel téléphonique sans réussir l’authentification 

- l’utilisateur est bloqué 

- l’utilisateur n’a jamais entré le code de vérification 

- utilisateur non trouvé 
 
- code de vérification déjà utilisé une fois 



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la page [Génération de rapports Azure Active Directory](active-directory-reporting-azure-portal.md).




























