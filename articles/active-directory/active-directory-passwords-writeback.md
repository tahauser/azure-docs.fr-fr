---
title: "Réinitialisation de mot de passe en libre-service Azure AD avec réécriture du mot de passe | Microsoft Docs"
description: "Utilisation d’Azure AD et d’Azure AD Connect pour l’écriture différée des mots de passe dans un annuaire local"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b4a14d3c79f93988eeac1525da09cf70dc2de634
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="password-writeback-overview"></a>Vue d’ensemble de la réécriture du mot de passe

L’écriture différée des mots de passe vous permet de configurer Azure Active Directory (Azure AD) pour réécrire les mots de passe dans votre environnement Active Directory local. Cela vous évite d’avoir à configurer et gérer une solution de réinitialisation des mots de passe libre-service (SSPR) locale complexe et offre à vos utilisateurs un moyen pratique, via le cloud, de réinitialiser leurs mots de passe locaux, où qu’ils se trouvent. L’écriture différée des mots de passe est un composant [Azure Active Directory Connect](./connect/active-directory-aadconnect.md). Elle peut être activée et utilisée par les abonnés actifs des [éditions Azure Active Directory](active-directory-whatis.md) Premium.

La réécriture du mot de passe fournit les fonctionnalités suivantes :

* **Retour d’informations immédiat** : l’écriture différée des mots de passe est une opération synchrone. Vos utilisateurs sont informés immédiatement si leur mot de passe ne respecte pas la stratégie définie ou s’il n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
* **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs ayant recours à des services de fédération Active Directory (AD FS) ou autres technologies de fédération** : avec l’écriture différée des mots de passe, tant que les comptes d’utilisateur fédérés sont synchronisés dans votre client Azure AD, ils sont en mesure de gérer leurs mots de passe Active Directory locaux à partir du cloud.
* **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs ayant recours à la** [synchronisation de hachage de mot de passe](./connect/active-directory-aadconnectsync-implement-password-synchronization.md) : lorsque le service de réinitialisation des mots de passe détecte qu’un compte d’utilisateur synchronisé est activé pour la synchronisation de hachage de mot de passe, nous réinitialisons simultanément le mot de passe local de ce compte et le mot de passe du cloud.
* **Prise en charge de la modification des mots de passe à partir du panneau d’accès et d’Office 365** : lorsque des utilisateurs fédérés ou synchronisés par mot de passe modifient leurs mots de passe (ceux-ci ayant ou non expiré), nous réécrivons ces mots de passe dans votre environnement Active Directory local.
* **Prise en charge de l’écriture différée des mots de passe lorsqu’un administrateur les réinitialise depuis le portail Azure** : chaque fois qu’un administrateur réinitialise le mot de passe d’un utilisateur dans le [portail Azure](https://portal.azure.com), dès lors que cet utilisateur est fédéré ou qu’il dispose de la synchronisation de mot de passe, nous définissons également le mot de passe sélectionné par l’administrateur dans l’annuaire Active Directory local. Actuellement, cette fonctionnalité n’est pas prise en charge dans le portail d’administration Office.
* **Application de vos stratégies de mot de passe Active Directory locales** : lorsqu’un utilisateur réinitialise son mot de passe, nous nous assurons qu’il répond à votre stratégie Active Directory locale avant de le valider dans l’annuaire. Ainsi, nous vérifions l’historique, la complexité, l’âge, les filtres de mot de passe et toute autre restriction de mot de passe éventuellement définie dans l’annuaire Active Directory local.
* **Absence de règles de pare-feu entrantes** : l’écriture différée de mot de passe utilise un relais Microsoft Azure Service Bus comme canal de communication sous-jacent. Vous n’êtes pas obligé d’ouvrir de ports entrants sur le pare-feu pour que cette fonctionnalité soit opérationnelle.
* **Aucune prise en charge pour les comptes d’utilisateur intégrés à des groupes protégés dans l’annuaire Active Directory local** : pour plus d’informations sur les groupes protégés, voir [Comptes et groupes protégés dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Fonctionnement de la réécriture du mot de passe

Lorsqu’un utilisateur fédéré ou disposant de la synchronisation du hachage de mot de passe réinitialise ou modifie son mot de passe dans le cloud, les événements suivants ont lieu :

1. Nous vérifions le type de mot de passe de l’utilisateur. Si nous constatons que le mot de passe est géré en local :
   * Nous vérifions que le service d’écriture différée fonctionne et est en cours d’exécution. Si tel est le cas, nous laissons l’utilisateur poursuivre.
   * Si le service d’écriture différée n’est pas disponible, nous indiquons à l’utilisateur que son mot de passe ne peut pas être réinitialisé pour l’instant.
2. Ensuite, l’utilisateur transite par les portails d’authentification appropriés et atteint la page de **réinitialisation du mot de passe**.
3. L’utilisateur sélectionne un nouveau mot de passe et le confirme.
4. Lorsqu’il clique sur **Envoyer**, nous chiffrons le mot de passe en texte brut avec une clé de contenu qui a été créée durant le processus de configuration d’écriture différée.
5. Après avoir chiffré le mot de passe, nous l’incluons dans une charge utile qui est envoyée via un canal HTTPS à votre relais Service Bus spécifique au client (que nous configurons également pour vous lors du processus d’installation de la réécriture). Ce relais est protégé par un mot de passe généré de manière aléatoire, connu uniquement de votre installation locale.
6. Une fois que le message a atteint le Service Bus, le point de terminaison de réinitialisation de mot de passe sort automatiquement du mode veille et découvre qu’une demande de réinitialisation est en attente.
7. Ce service recherche alors l’utilisateur concerné à l’aide de l’attribut d’ancrage de cloud. Pour que cette recherche aboutisse :

    * L’objet utilisateur doit exister dans l’espace connecteur Active Directory.
    * L’objet utilisateur doit être lié à l’objet métaverse (MV) correspondant.
    * L’objet utilisateur doit être lié à l’objet connecteur Azure Active Directory correspondant.
    * Le lien de l’objet connecteur Active Directory au métaverse doit avoir pour règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` sur le lien. <br> <br>
    Lorsque l’appel provient du cloud, le moteur de synchronisation utilise l’attribut **cloudAnchor** pour rechercher l’objet CS (Connector Space) Azure Active Directory. Il suit ensuite le lien vers l’objet MV, puis le lien vers l’objet Active Directory. Étant donné qu’il peut exister plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le lien `Microsoft.InfromADUserAccountEnabled.xxx` pour choisir celui qui convient.

    > [!Note]
    > Conformément à cette logique, Azure AD Connect doit être en mesure de communiquer avec l’émulateur de contrôleur de domaine principal (PDC) pour que l’écriture différée du mot de passe fonctionne. Si vous avez besoin d’activer cette option manuellement, vous pouvez connecter Azure AD Connect à l’émulateur PDC. Cliquez avec le bouton droit sur les **propriétés** du connecteur de synchronisation Active Directory, puis choisissez de **configurer les partitions d’annuaire**. À partir de là, recherchez la section correspondant aux **paramètres de connexion du contrôleur de domaine** et cochez la case intitulée **only use preferred domain controllers** (utiliser uniquement les contrôleurs de domaine préférés). Même si le contrôleur de domaine préféré n’est pas un émulateur PDC, Azure AD Connect cherche à se connecter au contrôleur de domaine principal pour l’écriture différée du mot de passe.

8. Une fois le compte d’utilisateur trouvé, nous tentons de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée.
9. Si l’opération de définition du mot de passe réussit, nous signalons à l’utilisateur que son mot de passe a été modifié.
    > [!NOTE]
    > Si le mot de passe de l’utilisateur est synchronisé à Azure AD à l’aide de la synchronisation de mot de passe, il se peut que la stratégie de mot de passe locale soit plus faible que la stratégie de mot de passe cloud. Dans ce cas, nous appliquons toujours la stratégie locale, quelle qu’elle soit, et nous utilisons la synchronisation du hachage de mot de passe pour synchroniser le hachage de ce mot de passe. Cela garantit que votre stratégie locale est appliquée dans le cloud, que vous utilisiez ou non la synchronisation ou la fédération de mot de passe pour fournir une authentification unique.

10. Si l’opération de définition du mot de passe échoue, nous renvoyons une erreur à l’utilisateur et le laissons réessayer. L’opération peut échouer pour les raisons suivantes :
    * Le service était arrêté.
    * Le mot de passe sélectionné ne répondait pas aux stratégies de l’organisation.
    * Nous n’avons pas pu trouver l’utilisateur dans l’annuaire Active Directory local.

    Nous avons un message spécifique pour la plupart de ces cas de figure et nous indiquons à l’utilisateur la marche à suivre pour résoudre le problème.

## <a name="configure-password-writeback"></a>Configuration de l’écriture différée du mot de passe

Nous vous recommandons d’utiliser la fonctionnalité de mise à jour automatique [d’Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) si vous souhaitez utiliser la réécriture du mot de passe.

Aucune prise en charge de DirSync et d’Azure AD Sync n’est actuellement possible pour activer l’écriture différée de mot de passe. Pour plus d’informations et faciliter votre transition, voir [Mise à niveau depuis DirSync et Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

Les étapes suivantes partent du principe que vous avez déjà configuré Azure AD Connect dans votre environnement à l’aide des paramètres [Express](./connect/active-directory-aadconnect-get-started-express.md) ou [Personnalisé](./connect/active-directory-aadconnect-get-started-custom.md).

1. Pour configurer et activer l’écriture différée des mots de passe, connectez-vous à votre serveur Azure AD Connect et démarrez l’Assistant de configuration **Azure AD Connect**.
2. Sur la page d’**accueil**, sélectionnez **Configurer**.
3. Sur la page **Tâches supplémentaires**, sélectionnez **Personnalisation des options de synchronisation**, puis cliquez sur **Suivant**.
4. Sur la page **Connexion à Azure AD**, entrez des informations d’identification d’administrateur général et sélectionnez **Suivant**.
5. Sur les pages **Connexion des annuaires** et **Filtrage par domaine/unité d’organisation**, sélectionnez **Suivant**.
6. Sur la page **Fonctionnalités facultatives**, cochez la case située à côté de l’option **Écriture différée de mot de passe**, puis sélectionnez **Suivant**.
   ![Activation de l’écriture différée du mot de passe dans Azure AD Connect][Writeback]
7. Sur la page **Prêt à configurer**, sélectionnez **Configurer** et attendez la fin du processus.
8. Lorsque la configuration prend fin, sélectionnez **Quitter**.

Pour apprendre à connaître les tâches de dépannage courantes associées à l’écriture différée des mots de passe, voir la section [Résoudre les problèmes de réécriture du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) dans l’article portant sur la résolution des problèmes.

## <a name="active-directory-permissions"></a>Autorisations Active Directory

Pour rester dans le cadre de la réinitialisation des mots de passe en libre-service, vous devez définir les éléments suivants dans le compte spécifié (utilitaire Azure AD Connect) :

* **Réinitialiser le mot de passe** 
* **Modifier le mot de passe** 
* **Autorisations en écriture** sur `lockoutTime`  
* **Autorisations en écriture** sur `pwdLastSet`
* **Droits étendus** sur l’un ou l’autre des éléments suivants :
   * L’objet racine de *chaque domaine* dans cette forêt
   * Les unités d’organisation utilisateur (UO) que vous souhaitez utiliser pour rester dans le cadre de la réinitialisation des mots de passe en libre-service

Si vous ignorez de quel compte il s’agit exactement, ouvrez l’interface utilisateur de configuration d’Azure Active Directory Connect, puis sélectionnez l’option **View current configuration** (Afficher la configuration actuelle). Le compte auquel vous devez ajouter l’autorisation est répertorié sous **Synchronized Directories** (Annuaires synchronisés).

Si vous définissez ces autorisations, le compte de service de l’agent de gestion de chaque forêt peut gérer les mots de passe pour les comptes d’utilisateur de cette forêt. 

> [!IMPORTANT]
> Sans ces autorisations, même si l’écriture différée semble configurée correctement, les utilisateurs rencontreront des erreurs en essayant de gérer leurs mots de passe locaux à partir du cloud.
>

> [!NOTE]
> La réplication de ces autorisations pour tous les objets de l’annuaire peut durer une heure ou plus.
>

Afin de configurer les autorisations appropriées pour l’écriture différée de mot de passe, procédez comme suit :

1. Ouvrez Utilisateurs et ordinateurs Active Directory avec un compte disposant des autorisations d’administration de domaine appropriées.
2. Dans le menu **Affichage**, assurez-vous que l’option **Fonctionnalités avancées** est activée.
3. Dans le volet gauche, cliquez avec le bouton droit sur l’objet représentant la racine du domaine, puis sélectionnez **Propriétés** > **Sécurité** > **Avancée**.
4. Dans l’onglet **Autorisations**, sélectionnez **Ajouter**.
5. Sélectionnez le compte auquel les autorisations sont appliquées (à partir de la configuration Azure AD Connect).
6. Dans la liste déroulante **S’applique à**, sélectionnez les objets de type **utilisateur descendant**.
7. Sous **Autorisations**, cochez les cases correspondant aux éléments suivants :
    * **Réinitialiser le mot de passe**
    * **Modifier le mot de passe**
    * **Écrire lockoutTime**
    * **Écrire pwdLastSet**
8. Cliquez sur **Appliquer/OK** pour appliquer les changements et fermer les boîtes de dialogue ouvertes.

## <a name="licensing-requirements-for-password-writeback"></a>Conditions de licence pour la réécriture du mot de passe

Pour plus d’informations concernant les licences, voir [Licences requises pour la réécriture du mot de passe](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou les sites suivants :

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Entreprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Modes d’authentification locale pris en charge pour l’écriture différée des mots de passe

L’écriture différée des mots de passe est prise en charge pour les types de mots de passe utilisateur suivants :

* **Utilisateurs cloud uniquement** : l’écriture différée des mots de passe ne s’applique pas dans cette situation, faute de mot de passe local.
* **Utilisateurs dont le mode passe est synchronisé** : l’écriture différée des mots de passe est prise en charge.
* **Utilisateurs fédérés** : l’écriture différée des mots de passe est prise en charge.
* **Utilisateurs à authentification directe** : l’écriture différée des mots de passe est prise en charge.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Opérations de l’utilisateur et de l’administrateur prises en charge pour l’écriture différée des mots de passe

Les mots de passe sont réécrits dans les situations suivantes :

* **Opérations de l’utilisateur final prises en charge**
  * Toute modification volontaire en libre-service du mot de passe de l’utilisateur final
  * Toute modification forcée en libre-service du mot de passe de l’utilisateur final (par exemple, expiration du mot de passe)
  * Toute réinitialisation du mot de passe en libre-service de l’utilisateur final émanant du [portail de réinitialisation des mots de passe](https://passwordreset.microsoftonline.com)
* **Opérations de l’administrateur prises en charge**
  * Toute modification volontaire en libre-service du mot de passe de l’administrateur
  * Toute modification forcée du mot de passe en libre-service de l’administrateur (par exemple, expiration du mot de passe)
  * Toute réinitialisation du mot de passe en libre-service de l’administrateur émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Opérations de l’utilisateur et de l’administrateur non prises en charge pour l’écriture différée des mots de passe

Les mots de passe ne sont *pas* réécrits dans les situations suivantes :

* **Opérations de l’utilisateur final non prises en charge**
  * Tout utilisateur final réinitialisant son mot de passe à l’aide de PowerShell (version 1 ou version 2) ou l’API Azure AD Graph
* **Opérations de l’administrateur non prises en charge**
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail de gestion Office](https://portal.office.com)
  * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur à l’aide de PowerShell (version 1 ou version 2) ou l’API Azure AD Graph

Nous travaillons à supprimer ces contraintes, mais nous n’avons pas encore de calendrier de publication.

## <a name="password-writeback-security-model"></a>Modèle de sécurité de la réécriture du mot de passe

La réécriture du mot de passe est un service hautement sécurisé. Pour garantir la protection de vos informations, nous activons un modèle de sécurité à quatre niveaux, décrit ci-dessous :

* **Relais de Service Bus propre au client**
  * Lorsque vous configurez le service, nous définissons un relais Service Bus spécifique au client et protégé par un mot de passe fort généré de manière aléatoire, auquel Microsoft n’a jamais accès.
* **Clé de chiffrement de mot de passe forte et verrouillée**
  * Une fois le relais Service Bus créé, nous créons une paire de clés symétriques fortes qui nous permet de chiffrer le mot de passe lorsqu’il arrive sur le réseau. Cette clé réside uniquement dans le magasin de secrets de votre entreprise dans le cloud, lequel est fortement verrouillé et audité, comme n’importe quel mot de passe de l’annuaire.
* **Norme TLS (Transport Layer Security)**
  1. Lorsqu’une opération de réinitialisation ou de modification de mot de passe a lieu dans le cloud, nous prenons le mot de passe et nous le chiffrons avec votre clé publique.
  2. Nous insérons cela dans un message HTTPS envoyé à votre relais Service Bus via un canal chiffré, à l’aide de certificats SSL Microsoft.
  3. Une fois le message arrivé dans Service Bus, votre agent local sort du mode veille et s’authentifie auprès de Service Bus en utilisant le mot de passe fort précédemment généré.
  4. L’agent local récupère le message chiffré et le déchiffre à l’aide de la clé privée que nous avons générée.
  5. L’agent local tente ensuite de définir le mot de passe via l’API SetPassword AD DS. C’est cette étape qui nous permet d’appliquer votre stratégie de mot de passe Active Directory local (complexité, âge, historique et filtres) dans le cloud.
* **Stratégies d’expiration de message** 
  * Si étant donné que votre service local est arrêté, le message reste dans Service Bus, il expire et est supprimé après quelques minutes. Le délai d’expiration et la suppression du message renforcent ainsi la sécurité.

### <a name="password-writeback-encryption-details"></a>Informations détaillées sur le chiffrement de la réécriture du mot de passe

Lorsqu’un utilisateur soumet une réinitialisation de mot de passe, cette demande transite via plusieurs étapes de chiffrement avant son arrivée dans votre environnement local. Ces étapes de chiffrement garantissent une sécurité et une fiabilité maximales. En voici le détail :

* **Étape 1 : Chiffrement du mot de passe avec la clé RSA de 2 048 bits** : lorsqu’un utilisateur envoie un mot de passe en vue de son écriture différée au niveau local, celui-ci est préalablement chiffré avec une clé RSA de 2 048 bits.
* **Étape 2 : Chiffrement de niveau package avec AES-GCM** : la méthode AES-GCM permet de chiffrer la totalité du package (mot de passe et métadonnées requises). Ce chiffrement empêche quiconque ayant un accès direct au canal ServiceBus sous-jacent de visualiser ou compromettre le contenu.
* **Étape 3 : Toutes les communications passent par une connexion TLS/SSL** : toutes les communications avec ServiceBus transitent par un canal SSL/TLS. Ce chiffrement protège le contenu contre les tiers non autorisés.
* **Substitution de clé automatique tous les six mois** : tous les six mois, ou chaque fois que l’écriture différée de mot de passe est désactivée puis réactivée sur Azure AD Connect. Nous substituons automatiquement toutes les clés pour garantir une sécurité optimale du service.

### <a name="password-writeback-bandwidth-usage"></a>Utilisation de la bande passante de la réécriture du mot de passe

L’écriture différée des mots de passe est un service à bande passante faible qui renvoie les demandes à l’agent local, uniquement dans les circonstances suivantes :

* Deux messages sont envoyés lors de l’activation ou de la désactivation de la fonctionnalité via Azure AD Connect.
* Un message est envoyé une fois toutes les cinq minutes comme une pulsation du service tant que le service est en cours d’exécution.
* Deux messages sont envoyés à chaque fois qu’un nouveau mot de passe est proposé :
    * Le premier message est une requête visant à effectuer l’opération.
    * Le deuxième message contient le résultat de l’opération et est envoyé dans les circonstances suivantes :
        * Chaque fois qu’un nouveau mot de passe est soumis pendant une réinitialisation de mot de passe libre-service par l’utilisateur.
        * Chaque fois qu’un nouveau mot de passe est soumis pendant une opération de modification de mot de passe par l’utilisateur.
        * Chaque fois qu’un nouveau mot de passe est soumis au cours d’une réinitialisation des mots de passe des utilisateurs réalisée par un administrateur (depuis les portails d’administration Azure uniquement).

#### <a name="message-size-and-bandwidth-considerations"></a>Considérations relatives à la taille des messages et à la bande passante

La taille de chaque message décrit précédemment est généralement inférieure à 1 Ko. Même sous des charges extrêmes, le service d’écriture différée des mots de passe lui-même utilise quelques kilobits par seconde de bande passante. Étant donné que chaque message est envoyé en temps réel, uniquement lorsqu’une opération de mise à jour du mot de passe le requiert, et étant donné que la taille du message est très limitée, l’utilisation de la bande passante de la fonctionnalité d’écriture différée est effectivement trop petite pour produire un impact mesurable.

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Activation de la réécriture du mot de passe dans Azure AD Connect"
