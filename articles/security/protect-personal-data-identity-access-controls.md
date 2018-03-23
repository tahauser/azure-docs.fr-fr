---
title: Protéger les données personnelles avec des contrôles d’accès et d’identité Azure | Microsoft Docs
description: Les contrôles d’accès et d’identité Azure vous aident à protéger des données personnelles et peuvent être utiles pour se conformer au Règlement général sur la protection des données (RGPD).
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: fd3beb57cda4993f922fb935263b0e962b38da6a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory et Multi-Factor Authentication : protéger les données personnelles avec des contrôles d’accès et d’identité

Cet article fournit des informations et des procédures que vous pouvez utiliser pour protéger des données personnelles à l’aide des services et fonctionnalités de sécurité Azure Active Directory et Multi-Factor Authentication. Les informations contenues dans cet article peuvent soutenir vos efforts de conformité avec le Règlement général sur la protection des données (RGPD).

## <a name="scenario"></a>Scénario

Une importante compagnie de croisière, dont le siège se situe aux États-Unis, étend ses opérations afin de proposer des circuits dans les Mers Méditerranée, Adriatique et Baltique, ainsi que dans les îles Britanniques. Pour soutenir cet effort, elle a fait l’acquisition de plusieurs lignes de croisière plus petites basées en Italie, en Allemagne, au Danemark et au Royaume-Uni. 

La compagnie utilise Microsoft Azure pour stocker les données d’entreprise dans le cloud. Ces dernières incluent des informations d’identification personnelle telles que les noms, les adresses, les numéros de téléphone et les informations de carte de crédit de sa base de clients globale. Elles incluent également des informations de ressources humaines traditionnelles telles que les adresses, les numéros de téléphone, les numéros d’identification fiscale, etc. des employés de toutes les agences de la compagnie. La ligne de croisière gère également une volumineuse base de données des membres du programme de fidélité et de récompense qui inclut des informations personnelles pour effectuer le suivi des relations avec les clients actuels et anciens.

Les employés de la compagnie accèdent au réseau à partir de ses agences distantes et les agents de voyage répartis dans le monde entier ont accès à certaines ressources de l’entreprise.

## <a name="problem-statement"></a>Définition du problème

La compagnie doit protéger la confidentialité des données personnelles de ses clients et employés contre les attaquants cherchant à utiliser des identités usurpées pour obtenir un accès. Elle doit également s’assurer que l’accès à des données personnelles par des utilisateurs légitimes se limite strictement aux personnes qui en ont besoin pour accomplir leur travail.

## <a name="company-goal"></a>Objectif de l’entreprise

L’objectif de la compagnie est de garantir que l’accès aux données personnelles est strictement contrôlé. Il est essentiel que les identités des utilisateurs ayant accès aux données personnelles soient protégées par une forte authentification. Une stratégie de [privilège minimum] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) doit s’appliquer afin que les utilisateurs légitimes aient uniquement le niveau d’accès dont ils ont besoin, et pas plus.

## <a name="solutions"></a>solutions

Microsoft Azure fournit des outils de gestion des identités et des accès pour aider les entreprises à contrôler qui a accès aux ressources qui contiennent des données personnelles.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) gère les identités et contrôle l’accès à Azure, ainsi qu’à d’autres ressources, données et applications locales et cloud. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) permet aux administrateurs Azure de réduire le nombre de personnes qui ont accès à certaines informations telles que des données personnelles. Il leur permet de découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources, ainsi que d’attribuer des droits d’administration temporaires et juste-à-temps (JIT) à des utilisateurs éligibles. Il fournit également des informations sur les personnes dotées de privilèges Administrateur AAD.

Les activités liées à l’utilisation de AAD PIM sont les suivantes :

- Activation de Privileged Identity Management pour votre annuaire

- Utilisation du tableau de bord d’administration Privileged Identity Management pour voir les informations importantes en un coup de œil

- Gestion des identités privilégiées (administrateurs) en ajoutant ou supprimant des administrateurs permanents ou éligibles dans chaque rôle

- Configuration des paramètres d’activation de rôle

- Activation des rôles

- Révision des activités de rôle

#### <a name="how-do-i-enable-aad-pim"></a>Comment activer AAD PIM ?

Pour commencer à utiliser PIM pour votre annuaire, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur général de votre annuaire.

2. Si votre organisation possède plusieurs répertoires, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure. Sélectionnez le répertoire où vous allez utiliser Azure AD Privileged Identity Management.

3. Sélectionnez **Autres services** et utilisez la zone de texte **Filtre** pour rechercher Azure AD Privileged Identity Management.

4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

Une fois Azure AD Privileged Identity Management configuré, le panneau de navigation s’affiche chaque fois que vous ouvrez l’application.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Pour plus d’informations et pour obtenir des instructions sur la mise en route avec AAD PIM, consultez [Commencer à utiliser Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Contrôle d’accès en fonction du rôle Azure

Le [contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) aide les administrateurs Azure à gérer l’accès aux ressources Azure en activant l’octroi d’accès en fonction du rôle attribué à l’utilisateur. Vous pouvez répartir les tâches au sein de votre équipe et accorder aux utilisateurs, groupes et applications uniquement les accès nécessaires pour accomplir leur travail.

L’accès en fonction du rôle peut être accordé aux utilisateurs à l’aide du portail Azure, des outils en ligne de commande Azure ou des API de gestion Azure.

Pour plus d’informations sur Azure RBAC, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Comment gérer le contrôle d’accès en fonction du rôle Azure avec PowerShell ?

Vous pouvez utiliser les applets de commande PowerShell pour gérer Azure RBAC, notamment pour accomplir les tâches de gestion suivantes :

- Répertorier les rôles

- Voir quel utilisateur a des autorisations d’accès

- Accorder l'accès

- Suppression d'accès

- Créer un rôle personnalisé

- Actions Get pour un fournisseur de ressources

- Modifier un rôle personnalisé

- Supprimer un rôle personnalisé

- Répertorier les rôles personnalisés

Pour obtenir des instructions sur la gestion d’Azure RBAC avec PowerShell, consultez [Gérer l’accès en fonction du rôle avec Azure Powershell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) est une solution de vérification en deux étapes qui contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur d’un processus d’authentification simple. Il fournit une authentification forte au moyen de diverses méthodes de vérification, notamment les appels téléphoniques, l’envoi de SMS ou la vérification sur l’application mobile.

Pour déployer MFA dans le cloud Azure, vous devez d’abord l’activer, puis activer une vérification en deux étapes pour les utilisateurs.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Comment permettre à Azure d’utiliser MFA ?

Si vos utilisateurs disposent de licences comprenant Azure Multi-Factor Authentication, vous devez simplement configurer Azure MFA par utilisateur ou par groupe. 

![Utilisateurs prenant en charge MFA](media/protect-personal-data-identity-access-controls/enable-mfa.png)

Si vous ne disposez pas de licences, vous devez effectuer une série d’étapes pour déterminer le type de déploiement le mieux adapté à votre scénario. Vous pouvez commencer par lire l’article intitulé [Choisir la solution Azure Multi-Factor Authentication adaptée à vos besoins](../multi-factor-authentication/multi-factor-authentication-get-started.md). Si vous décidez de poursuivre dans cette voie, vous devez créer un serveur Multi-Factor Authentication. Pour commencer, effectuez les étapes suivantes :

1. Sélectionnez **Active Directory** dans le portail Azure (en étant connecté en tant qu’administrateur).

2. Sélectionnez **Serveur MFA**

3. Spécifiez une valeur de délai d’attente. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. Cliquez sur **Enregistrer**.

Dans cette fenêtre, vous pouvez aussi télécharger le serveur MFA. Pour obtenir des détails supplémentaires sur le dimensionnement et la planification der votre déploiement, lisez l’article [Bien démarrer avec le serveur Azure Multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

Pour obtenir des instructions sur la gestion du fournisseur d’authentification multifacteur, consultez [Prise en main du fournisseur Azure Multi-Factor Auth.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Comment activer la vérification en deux étapes pour les utilisateurs ?

Vous pouvez appliquer la vérification en deux étapes pour toutes les connexions, ou créer des règles d’accès conditionnel, ce qui permet d’exiger la vérification en deux étapes quand des conditions spécifiques s’appliquent.

L’activation de l’authentification multifacteur Azure en modifiant les états des utilisateurs est l’approche traditionnelle pour exiger une vérification en deux étapes. Tous les utilisateurs que vous activez doivent satisfaire à la même exigence, qui consiste à lancer la vérification en deux étapes chaque fois qu’ils se connectent. L’activation d’un utilisateur remplace toutes les stratégies d’accès conditionnel appliquées à cet utilisateur.

L’activation de l’authentification multifacteur Azure avec une stratégie d’accès conditionnel est une approche plus souple pour exiger une vérification en deux étapes. Vous pouvez créer des stratégies d’accès conditionnel qui s’appliquent à des groupes et à des utilisateurs individuels. Vous pouvez appliquer davantage de restrictions aux groupes à haut risque par rapport aux groupes à faible risque, ou exiger la vérification en deux étapes uniquement pour les applications cloud à haut risque tout en ignorant celles à faible risque. Toutefois, l’accès conditionnel est une fonctionnalité payante d’Azure Active Directory.

Pour activer MFA en modifiant l’état de l’utilisateur, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Accédez à **Azure Active Directory \> Utilisateurs et groupes \> Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
4. Recherchez l’utilisateur que vous souhaitez activer pour l’authentification multifacteur Azure. Vous devrez peut-être modifier l'affichage en haut de la page.
5. Cochez la case en regard du nom de l’utilisateur.
6. À droite, sous les étapes rapides, choisissez **Activer**.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. Confirmez votre sélection dans la fenêtre contextuelle qui s’ouvre.  Les utilisateurs pour lesquels MFA a été activé sont invités à s’inscrire la prochaine fois qu’ils se connectent.

Pour activer Azure MFA avec une stratégie d’accès conditionnel, procédez comme suit :

1. Connectez-vous au portail Azure en tant qu’administrateur.

2. Accédez à **Azure Active Directory \> Accès conditionnel**.

3. Sélectionnez **Nouvelle stratégie**.

4. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**. Utilisez les onglets **Inclure** et **Exclure** pour choisir les utilisateurs et groupes gérés par la stratégie.

5. Sous **Affectations**, sélectionnez **Applications cloud.** Choisissez d’**inclure toutes les applications cloud**.
6.  Sous **Contrôles d’accès**, sélectionnez **Accorder**. Choisissez **Exiger une authentification multifacteur**.
7.  Définissez l’option **Activer la stratégie** sur **On** (activée) puis sélectionnez **Enregistrer**.

Pour plus d’informations sur la façon de configurer les paramètres Azure MFA pour configurer des alertes de fraude, créer un contournement à usage unique, utiliser des messages vocaux personnalisés, configurer la mise en cache, spécifier les adresses IP approuvées, créer des mots de passe d’application, activer la mémorisation de MFA pour les appareils auxquels les utilisateurs font confiance et sélectionner des méthodes de vérification, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Étapes suivantes

- [Sécurisation de l’accès privilégié dans Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Forum aux questions sur Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Résolution des problèmes de contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
