---
title: "Azure Active Directory Identity Protection | Microsoft Docs"
description: "Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e66d033d95efccf53ea2de889b5811fe2eafb76a
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Azure Active Directory Identity Protection est une fonctionnalité de l’édition Azure AD Premium P2 qui vous permet de :

- Détecter les vulnérabilités potentielles qui affectent les identités de votre organisation

- Configurer des réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation  

- Examiner les incidents suspects et prendre les mesures appropriées pour les résoudre   


## <a name="getting-started"></a>Prise en main

Microsoft sécurise les identités basées sur le cloud depuis plus de dix ans. Avec Azure Active Directory Identity Protection, vous pouvez dans votre environnement utiliser les mêmes systèmes de protection que Microsoft pour sécuriser les identités.

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Aujourd’hui, les cybercriminels arrivent de plus en plus à exploiter les failles de fournisseurs tiers et utilisent des attaques par hameçonnage (ou « phishing ») sophistiquées toujours plus efficaces. Dès qu’un cybercriminel accède à un compte d’utilisateur, même si les privilèges de celui-ci sont faibles, il peut facilement accéder à des ressources importantes pour l’entreprise de manière latérale.

Par conséquent, vous devez :

- Protéger toutes les identités, quel que soit leur niveau de privilège

- Empêcher proactivement le détournement des identités compromises

Détecter les identités compromises n’est pas chose aisée. Azure Active Directory utilise des algorithmes de Machine Learning et des modèles heuristiques adaptatifs pour détecter les anomalies et les incidents suspects qui révèlent des identités potentiellement compromises. Grâce à ces données, Identity Protection génère des rapports et des alertes qui vous permettent d’évaluer les problèmes détectés et de prendre les mesures de correction ou d’atténuation qui s’imposent.

Mais Azure Active Directory Identity Protection est bien plus qu’un outil de surveillance et de création de rapports. Pour protéger les identités de votre organisation, vous pouvez configurer des stratégies qui répondent automatiquement aux problèmes détectés lorsqu’un niveau de risque spécifié est atteint. Outre les autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, ces stratégies peuvent automatiquement bloquer ou déclencher des mesures de correction adaptatives qui incluent des réinitialisations de mot de passe et la mise en œuvre de l’authentification multifacteur.


#### <a name="identity-protection-capabilities"></a>Fonctionnalités d’Identity Protection

**Détection des vulnérabilités et des comptes à risque :**  

* Recommandations personnalisées visant à améliorer la posture de sécurité globale en mettant en évidence les vulnérabilités
* Calcul des niveaux de risque à la connexion
* Calcul du niveau de risque des utilisateurs


**Examen des événements à risque :**

* Envoi de notifications pour les événements à risque
* Examen des événements à risque à l’aide d’informations contextuelles et pertinentes
* Workflows de base pour le suivi des investigations
* Accès rapide à des mesures de correction telles que la réinitialisation de mot de passe

**Stratégies d’accès conditionnel en fonction des risques :**

* Stratégie pour atténuer les connexions à risque en bloquant les connexions ou en imposant des demandes d’authentification multifacteur
* Stratégie pour bloquer ou sécuriser les comptes d’utilisateurs à risque
* Stratégie pour exiger que les utilisateurs s’inscrivent à l’authentification multifacteur



## <a name="identity-protection-roles"></a>Rôles de protection des identités (Identity Protection)

Pour équilibrer la charge des activités de gestion en ce qui concerne votre implémentation de la protection des identités, vous pouvez attribuer plusieurs rôles. Azure AD Identity Protection prend en charge 3 rôles d’annuaire :

| Rôle                         | Peut                          | Ne peut pas
| :--                          | ---                                |  ---   |
| Administrateur général         | Accès complet à Identity Protection, Onboard Identity Protection| |
| Administrateur de sécurité       | Accès complet à Identity Protection | Onboard Identity Protection, réinitialiser les mots de passe pour un utilisateur |
| Lecteur de sécurité              | Accès en lecture seule à Identity Protection | Onboard Identity Protection, résoudre les utilisateurs, configurer les utilisateurs, réinitialiser les mots de passe |




Pour plus d’informations, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Détection

### <a name="vulnerabilities"></a>Vulnérabilités

Azure Active Directory Identity Protection analyse votre configuration et détecte les vulnérabilités qui peuvent avoir un impact sur les identités de vos utilisateurs. Pour en savoir plus, consultez [Vulnérabilités détectées par Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Événements à risque

Azure Active Directory utilise les algorithmes de Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux identités de votre utilisateur. Le système crée un enregistrement pour chaque action suspecte détectée. Ces enregistrements sont également appelés événements à risque.  
Pour en savoir plus, consultez [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Investigation
Votre parcours dans Identity Protection commence généralement par le tableau de bord d’Identity Protection.

![Correction](./media/active-directory-identityprotection/1000.png "Correction")

Le tableau de bord vous donne accès à :

* des rapports comme **Utilisateurs associés à un indicateur de risque**, **Événements à risque** et **Vulnérabilités** ;
* des paramètres vous permettant notamment de configurer vos **stratégies de sécurité**, vos **notifications** et **l’inscription à l’authentification multifacteur**.

Il s’agit généralement de votre point de départ pour l’investigation, le processus consistant à vérifier les activités, les journaux et les autres informations pertinentes concernant un événement à risque, afin de déterminer s’il est nécessaire d’appliquer des mesures de correction ou d’atténuation, de comprendre comment l’identité a été compromise et d’identifier la manière dont l’identité compromise a été exploitée.

Vous pouvez lier vos activités d’investigation aux [notifications](active-directory-identityprotection-notifications.md) d’Azure Active Directory Protection envoyées par courrier électronique.

Les sections suivantes fournissent plus de détails, ainsi que les étapes liées à une investigation.  


## <a name="risky-sign-ins"></a>Connexions risquées

Azure Active Directory détecte les [types d’événements à risque](active-directory-reporting-risk-events.md#risk-event-types) en temps réel et hors connexion. Tous les événements à risque qui sont détectés pendant la connexion d’un utilisateur viennent alimenter un concept logique appelé « connexion risquée ». Une connexion risquée est une tentative de connexion susceptible d’émaner d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.


### <a name="sign-in-risk-level"></a>Niveau de risque d’une connexion

Un niveau de risque de connexion est une indication de la probabilité (haute, moyenne ou faible) qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur.

### <a name="mitigating-sign-in-risk-events"></a>Atténuation des événements à risque à la connexion

Une atténuation est une mesure visant à limiter la probabilité qu’un pirate exploite une identité ou un appareil compromis, mais qui ne permet pas de rétablir la sécurité de l’identité ou de l’appareil en question. Une atténuation ne résout pas les précédents événements de connexion à risque associés à l’identité ou à l’appareil.

Pour limiter automatiquement les connexions à risque, vous pouvez configurer des stratégies de sécurité. Avec ce type de stratégie, vous prenez en compte le niveau de risque de l’utilisateur ou de la connexion pour bloquer les connexions à risque ou exiger une authentification multifacteur de la part de l’utilisateur. Ces mesures peuvent empêcher un cybercriminel d’exploiter une identité volée pour causer des dommages et vous donner le temps de sécuriser l’identité.

### <a name="sign-in-risk-security-policy"></a>Stratégie de sécurité en matière de risque à la connexion
Une stratégie en matière de risque à la connexion est une stratégie d’accès conditionnel consistant à évaluer le risque associé à une connexion spécifique et qui applique des mesures d’atténuation à partir de règles et de conditions prédéfinies.

![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1014.png "Stratégie en matière de risque à la connexion")

Azure AD Identity Protection vous aide à gérer l’atténuation des connexions à risque, en vous permettant d’effectuer les opérations suivantes :

* Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1015.png "Stratégie en matière de risque à la connexion")
* Définir le niveau de risque à la connexion (Faible, Moyen ou Élevé) qui déclenche la stratégie :

    ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1016.png "Stratégie en matière de risque à la connexion")
* Définir les contrôles à appliquer lorsque la stratégie est déclenchée :  

    ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1017.png "Stratégie en matière de risque à la connexion")
* Basculer l’état de votre stratégie :

    ![Inscription à MFA](./media/active-directory-identityprotection/403.png "Inscription à MFA")
* Examiner et évaluer l’impact d’un changement avant de l’appliquer :

    ![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1018.png "Stratégie en matière de risque à la connexion")

#### <a name="what-you-need-to-know"></a>Bon à savoir
Vous pouvez configurer une stratégie de sécurité en matière de risque à la connexion pour exiger l’authentification multifacteur :

![Stratégie en matière de risque à la connexion](./media/active-directory-identityprotection/1017.png "Stratégie en matière de risque à la connexion")

Toutefois, pour des raisons de sécurité, ce paramètre s’applique uniquement aux utilisateurs qui ont déjà été inscrits pour l’authentification multifacteur. En cas d’obligation d’authentification multifacteur pour un utilisateur qui n’est pas encore inscrit pour l’authentification multifacteur, l’utilisateur est bloqué.

La meilleure pratique pour exiger une authentification multifacteur pour les connexions à risque consiste à :

1. Activer la [stratégie d’inscription à l’authentification multifacteur](#multi-factor-authentication-registration-policy) pour les utilisateurs concernés, et
2. Demander aux utilisateurs concernés de se connecter à une session ne présentant aucun risque pour s’inscrire à l’authentification MFA.

Suivre ces étapes permet de s’assurer que l’authentification multifacteur est requise pour une connexion à risque.

#### <a name="best-practices"></a>Meilleures pratiques
La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.  

Cependant, cela a pour effet d’exclure les connexions associées à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut qu’un cybercriminel soit en mesure d’exploiter une identité compromise.

Pour définir la stratégie

* Excluez les utilisateurs qui ne sont pas inscrits/ne peuvent pas s’inscrire à l’authentification multifacteur.
* Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).
* Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).
* Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux.
* Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

La stratégie en matière de risque à la connexion :

* est appliquée à l’ensemble du trafic de navigateur et des connexions utilisant une authentification moderne ;
* n’est pas appliquée aux applications utilisant des protocoles de sécurité plus anciens en désactivant le point de terminaison WS-Trust sur le fournisseur d’identité fédérée, tels qu’ADFS.

La page **Événements à risque** de la console Identity Protection répertorie tous les événements :

* auxquels cette stratégie a été appliquée ;
* pour lesquels vous pouvez consulter l’activité afin de déterminer si la mesure était appropriée ou non.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Récupération de connexion à risque](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Connexion à risque bloquée](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Expériences de connexion avec Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Pour ouvrir la boîte de dialogue de configuration connexe**:

- Dans le panneau **Azure AD Identity Protection**, dans la section **Configurer**, cliquez sur **Stratégie en matière de risque à la connexion**.

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1014.png "Stratégie en matière de risque des utilisateurs")



## <a name="users-flagged-for-risk"></a>Utilisateurs associés à un indicateur de risque

Tous les [événements à risque](active-directory-identity-protection-risk-events.md) actifs détectés par Azure Active Directory pour un utilisateur alimentent un concept logique appelé « risque de l’utilisateur ». Un utilisateur signalé comme présentant un risque indique qu’un compte d’utilisateur est susceptible d’avoir été compromis.

![Utilisateurs associés à un indicateur de risque](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Niveau de risque d’un utilisateur

Le niveau de risque d’un utilisateur est une indication (Élevé, Moyen ou Faible) de la probabilité que l’identité de l’utilisateur ait été compromise. Il est calculé en fonction des événements à risque associés à l’identité de l’utilisateur.

L’état d’un événement à risque est soit **Actif**, soit **Fermé**. Seuls les événements à risque qui sont **actifs** entrent dans le calcul du risque d’un utilisateur.

Le niveau de risque d’un utilisateur est calculé à l’aide des données suivantes :

* Événements à risque actifs ayant un impact sur l’utilisateur
* Niveau de risque de ces événements
* Application ou non de mesures de correction

![Risques des utilisateurs](./media/active-directory-identityprotection/1031.png "Risques des utilisateurs")

Vous pouvez utiliser les niveaux de risque des utilisateurs pour créer des stratégies d’accès conditionnel qui empêchent les utilisateurs à risque de se connecter ou les forcent à sécuriser leur mot de passe.

### <a name="closing-risk-events-manually"></a>Fermeture manuelle des événements à risque

Dans la plupart des cas, vous pouvez prendre des mesures de correction telles qu’une réinitialisation de mot de passe sécurisée pour fermer automatiquement les événements à risque. Toutefois, il se peut que cela ne soit pas toujours possible.  
C’est par exemple le cas quand :

* un utilisateur avec des événements à risque actifs a été supprimé ;
* une enquête révèle qu’un événement à risque signalé a été effectué par l’utilisateur légitime.

Comme les événements à risque dont l’état est défini sur **Actif** entrent dans le calcul du risque des utilisateurs, vous pouvez avoir besoin de réduire manuellement un niveau de risque en fermant manuellement les événements à risque.  
Au cours de l’investigation, vous pouvez choisir d’effectuer n’importe laquelle des actions suivantes pour modifier l’état d’un événement à risque :

![Actions](./media/active-directory-identityprotection/34.png "Actions")

* **Résoudre** : si après avoir examiné un événement à risque, vous avez pris une mesure de correction appropriée en dehors d’Identity Protection et pensez que l’événement à risque doit être considéré comme fermé, marquez l’événement comme résolu. L’état des événements à risque résolus est défini sur Fermé et ces événements n’entrent plus dans le calcul du risque d’un utilisateur.
* **Marquer comme faux positif** : dans certains cas, il se peut qu’un événement à risque soit signalé à tort en tant que tel. Vous pouvez réduire le nombre de ces événements en les marquant comme faux positifs. Vous aiderez ainsi les algorithmes d’apprentissage automatique à améliorer la classification des événements similaires par la suite. L’état des événements marqués comme faux positifs est défini sur **Fermé** et ces événements n’entrent plus dans le calcul du risque de l’utilisateur.
* **Ignorer** : si vous n’avez pris aucune mesure de correction, mais que vous souhaitez que l’événement à risque soit supprimé de la liste active, vous pouvez choisir de l’ignorer. Son état sera alors défini sur Fermé. Les événements ignorés n’entrent plus dans le calcul du risque d’un utilisateur. Cette option doit uniquement être utilisée dans des circonstances inhabituelles.
* **Réactiver** : les événements à risque qui ont été fermés manuellement (en choisissant **Résoudre**, **Marquer comme faux positif** ou **Ignore**) peuvent être réactivés. Leur statut est alors défini à nouveau sur **Actif**. Les événements à risque réactivés contribuent au calcul du niveau de risque d’un utilisateur. Les événements à risque fermés à l’aide d’une mesure de correction (telle qu’une réinitialisation de mot de passe sécurisée) ne peuvent pas être réactivés.

**Pour ouvrir la boîte de dialogue de configuration connexe**:

1. Dans le panneau **Azure AD Identity Protection**, sous **Examiner**, cliquez sur **Événements à risque**.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1002.png "Réinitialisation manuelle du mot de passe")
2. Dans la liste **Événements à risque** , cliquez sur un risque.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1003.png "Réinitialisation manuelle du mot de passe")
3. Dans le panneau Risque, cliquez avec le bouton droit sur un utilisateur.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1004.png "Réinitialisation manuelle du mot de passe")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Fermeture manuelle de tous les événements à risque pour un utilisateur
Au lieu de fermer manuellement les événements à risque pour chaque utilisateur, Azure Active Directory Identity Protection propose une méthode permettant de fermer tous les événements pour un utilisateur en un seul clic.

![Actions](./media/active-directory-identityprotection/2222.png "Actions")

Lorsque vous cliquez sur **Ignorer tous les événements**, tous les événements sont fermés et l’utilisateur concerné n’est plus exposé.

### <a name="remediating-user-risk-events"></a>Correction des événements à risque d’un utilisateur

Une correction est une mesure visant à sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis. Une mesure de correction permet de rétablir la sécurité de l’identité ou de l’appareil et de résoudre les anciens événements à risque associés à l’identité ou à l’appareil.

Pour corriger les événements à risque d’un utilisateur, vous pouvez procéder comme suit :

* Effectuez une réinitialisation de mot de passe sécurisée pour corriger manuellement les événements à risque de l’utilisateur.
* Configurez une stratégie de sécurité en matière de risque des utilisateurs pour atténuer ou corriger automatiquement les événements à risque de l’utilisateur.
* Réimager l’appareil infecté.  

#### <a name="manual-secure-password-reset"></a>Réinitialisation manuelle et sécurisée du mot de passe
Une réinitialisation de mot de passe sécurisée est une mesure de correction efficace pour de nombreux événements à risque. Lorsqu’elle est effectuée, ces événements à risque sont fermés et le niveau de risque de l’utilisateur correspondant recalculé automatiquement. Vous pouvez utiliser le tableau de bord d’Identity Protection afin de lancer une réinitialisation de mot de passe pour un utilisateur à risque.

La boîte de dialogue connexe fournit deux méthodes différentes pour réinitialiser le mot de passe :

**Réinitialiser le mot de passe** : sélectionnez **Demander à l’utilisateur de réinitialiser son mot de passe** pour permettre à l’utilisateur de récupérer lui-même son compte s’il s’est inscrit à l’authentification multifacteur. La prochaine fois que l’utilisateur se connectera, il devra résoudre une demande d’authentification multifacteur, puis sera obligé de changer le mot de passe. Cette option n’est pas disponible si le compte d’utilisateur n’est pas déjà inscrit à l’authentification multifacteur.

**Mot de passe temporaire** : sélectionnez **Générer un mot de passe temporaire** pour invalider immédiatement le mot de passe existant et créer un nouveau mot de passe temporaire pour l’utilisateur. Envoyez le nouveau mot de passe temporaire à une autre adresse de messagerie de l’utilisateur ou au responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur sera invité à changer le mot de passe lors de la connexion.

![Stratégie](./media/active-directory-identityprotection/1005.png "Stratégie")

**Pour ouvrir la boîte de dialogue de configuration connexe**:

1. Dans le panneau **Azure AD Identity Protection**, cliquez sur **Utilisateurs associés à un indicateur de risque**.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1006.png "Réinitialisation manuelle du mot de passe")
2. Dans la liste des utilisateurs, sélectionnez un utilisateur comportant au moins un risque.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1007.png "Réinitialisation manuelle du mot de passe")
3. Dans le panneau Utilisateur, cliquez sur **Réinitialiser le mot de passe**.

    ![Réinitialisation manuelle du mot de passe](./media/active-directory-identityprotection/1008.png "Réinitialisation manuelle du mot de passe")

### <a name="user-risk-security-policy"></a>Stratégie de sécurité en matière de risque des utilisateurs
Une stratégie de sécurité en matière de risque des utilisateurs est une stratégie d’accès conditionnel qui évalue le niveau de risque d’un utilisateur spécifique et applique des mesures de correction et d’atténuation en fonction de conditions et de règles prédéfinies.

![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1009.png "Stratégie en matière de risque des utilisateurs")

Azure AD Identity Protection vous aide à gérer les mesures de correction et d’atténuation pour les utilisateurs associés à un indicateur de risque, en vous permettant d’effectuer les opérations suivantes :

* Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1010.png "Stratégie en matière de risque des utilisateurs")
* Définir le niveau de risque d’un utilisateur (Faible, Moyen ou Élevé) qui déclenche la stratégie :

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1011.png "Stratégie en matière de risque des utilisateurs")
* Définir les contrôles à appliquer lorsque la stratégie est déclenchée :

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1012.png "Stratégie en matière de risque des utilisateurs")
* Basculer l’état de votre stratégie :

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/403.png "Inscription à MFA")
* Examiner et évaluer l’impact d’un changement avant de l’appliquer :

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1013.png "Stratégie en matière de risque des utilisateurs")

La sélection d’un niveau de risque **Élevé** réduit la fréquence de déclenchement d’une stratégie et minimise l’impact sur les utilisateurs.
Cependant, cela a pour effet d’exclure les utilisateurs associés à un indicateur de risque **Faible** et **Moyen**. Par conséquent, il se peut que des identités ou des appareils déjà identifiés comme potentiellement ou effectivement compromis ne soient pas sécurisés.

Pour définir la stratégie

* Excluez les utilisateurs susceptibles de générer un grand nombre de faux positifs (développeurs, analystes de sécurité).
* Excluez les utilisateurs situés dans des régions où l’activation de la stratégie n’est pas adaptée (par exemple, aucun accès au support technique).
* Utilisez un niveau de risque **Élevé** pendant le déploiement initial de la stratégie ou si vous devez minimiser la complexité pour les utilisateurs finaux.
* Utilisez un niveau de risque **Faible** si votre organisation nécessite une sécurité accrue. La sélection d’un niveau de risque **Faible** complique la connexion pour les utilisateurs, mais renforce la sécurité.

Pour la plupart des organisations, nous recommandons de configurer un niveau de risque **Moyen** afin d’établir un juste équilibre entre facilité d’utilisation et sécurité.

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Flux de récupération de compte compromis](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Flux de compte compromis bloqué](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Pour ouvrir la boîte de dialogue de configuration connexe**:

- Dans le panneau **Azure AD Identity Protection**, dans la section **Configurer**, cliquez sur **Stratégie de risque d’utilisateur**.

    ![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1009.png "Stratégie en matière de risque des utilisateurs")

### <a name="mitigating-user-risk-events"></a>Atténuation des événements à risque d’un utilisateur
Les administrateurs peuvent définir une stratégie de sécurité en matière de risque des utilisateurs pour bloquer les utilisateurs lors de la connexion selon le niveau de risque.

Le blocage d’une connexion :

* empêche la génération de nouveaux événements à risque pour l’utilisateur concerné ;
* permet aux administrateurs de corriger manuellement les événements à risques affectant l’identité de l’utilisateur pour sécuriser à nouveau cette dernière.



## <a name="multi-factor-authentication-registration-policy"></a>Stratégie d’inscription à l’authentification multifacteur
Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l’utilisation d’autres méthodes que le nom d’utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur.  
Nous vous recommandons d’exiger l’authentification multifacteur d’Azure des connexions des utilisateurs pour les raisons suivantes :

* Elle fournit une authentification renforcée avec un éventail d’options de vérification simples.
* Elle joue un rôle clé dans la préparation de votre organisation pour protéger et récupérer les comptes compromis.

![Stratégie en matière de risque des utilisateurs](./media/active-directory-identityprotection/1019.png "Stratégie en matière de risque des utilisateurs")

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)

Azure AD Identity Protection vous permet de gérer le déploiement de l’inscription à l’authentification multifacteur en configurant une stratégie qui vous permet d’effectuer les opérations suivantes :

* Définir les utilisateurs et les groupes auxquels la stratégie s’applique :

    ![Stratégie MFA](./media/active-directory-identityprotection/1020.png "Stratégie MFA")
* Définir les contrôles à appliquer lorsque la stratégie est déclenchée :  

    ![Stratégie MFA](./media/active-directory-identityprotection/1021.png "Stratégie MFA")
* Basculer l’état de votre stratégie :

    ![Stratégie MFA](./media/active-directory-identityprotection/403.png "Stratégie MFA")
* Afficher l’état d’inscription actuel :

    ![Stratégie MFA](./media/active-directory-identityprotection/1022.png "Stratégie MFA")

Pour une obtenir une vue d’ensemble de l’expérience utilisateur, consultez :

* [Processus d’inscription à l’authentification multifacteur](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Expériences de connexion avec Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Pour ouvrir la boîte de dialogue de configuration connexe**:

- Dans le panneau **Azure AD Identity Protection**, dans la section **Configurer**, cliquez sur **Inscription à l’authentification multifacteur**.

    ![Stratégie MFA](./media/active-directory-identityprotection/1019.png "Stratégie MFA")

## <a name="next-steps"></a>Étapes suivantes
* [Channel 9 : Azure AD and Identity Show: Identity Protection Preview (Émission sur Azure AD et l’identité : présentation d’Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Activer Azure Active Directory Identity Protection](active-directory-identityprotection-enable.md)

* [Vulnérabilités détectées par Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md)

* [Notifications d’Azure Active Directory Identity Protection](active-directory-identityprotection-notifications.md)

* [Manuel d’Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md)

* [Glossaire d’Azure Active Directory Identity Protection](active-directory-identityprotection-glossary.md)

* [Expériences de connexion avec Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory Identity Protection - Déblocage des utilisateurs](active-directory-identityprotection-unblock-howto.md)

* [Prise en main d’Azure Active Directory Identity Protection et de Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
