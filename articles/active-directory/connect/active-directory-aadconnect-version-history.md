---
title: 'Azure AD Connect : historique de publication des versions | Microsoft Docs'
description: Cet article répertorie toutes les versions d’Azure AD Connect et d’Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/16/2018
ms.author: billmath
ms.openlocfilehash: 8bae1140d4a3ac4762bdcbabb16851d29415a8fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect : historique de publication des versions
L’équipe Azure Active Directory (Azure AD) met régulièrement à jour Azure AD Connect avec de nouvelles fonctions et fonctionnalités. Tous les ajouts ne sont pas applicables à toutes les configurations.

Cet article est conçu pour vous aider à conserver la trace des versions qui ont été publiées, et à comprendre si vous devez ou non effectuer la mise jour vers la version la plus récente.

Voici la liste des rubriques connexes :



Rubrique |  Détails
--------- | --------- |
Étapes de mise à niveau à partir d’Azure AD Connect | Différentes méthodes pour [effectuer une mise à niveau à partir d’une version précédente vers la dernière](active-directory-aadconnect-upgrade-previous-version.md) version Azure AD Connect.
Autorisations requises | Pour plus d’informations sur les autorisations requises afin d’appliquer une mise à jour, consultez [Comptes et autorisations](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Télécharger| [Télécharger Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="117500"></a>1.1.750.0
État 22/3/2018 : publié pour la mise à niveau automatique et le téléchargement.
>[!NOTE]
>Lorsque la mise à niveau vers cette nouvelle version s’exécute, elle déclenche automatiquement une synchronisation complète et une importation intégrale pour le Connecteur Azure AD, ainsi qu’une synchronisation complète pour le Connecteur AD. Selon la taille de l’environnement Azure AD Connect, cette opération peut prendre un certain temps ; veillez à prendre les mesures nécessaires pour qu’elle ne gêne pas vos activités, ou différez la mise à niveau jusqu’au moment opportun.

>[!NOTE]
>« La fonctionnalité de mise à niveau automatique a été erronément désactivée pour certains clients ayant déployé des builds postérieures à la build 1.1.524.0. Pour vous assurer que votre instance Azure AD Connect est éligible pour la mise à niveau automatique, exécutez l’applet de commande PowerShell suivante : « Set-ADSyncAutoUpgrade -AutoupGradeState Enabled »


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problèmes résolus

* Auparavant, la cmdlet Set-ADSyncAutoUpgrade bloquait la mise à niveau automatique lorsqu’elle était dans l’état Suspendu. Ce comportement a été modifié pour éviter de bloquer la mise à niveau automatique des versions futures.

## <a name="117490"></a>1.1.749.0
État : version publiée à l’intention d’un panel de clients

>[!NOTE]
>Lorsque la mise à niveau vers cette nouvelle version s’exécute, elle déclenche automatiquement une synchronisation complète et une importation intégrale pour le Connecteur Azure AD, ainsi qu’une synchronisation complète pour le Connecteur AD. Étant donné que cette opération peut prendre un certain temps, selon la taille de votre environnement Azure AD Connect, assurez-vous que vous avez pris les mesures nécessaires pour que cette opération ne gêne pas vos activités, ou différez la mise à niveau jusqu’à ce que vous trouviez un moment opportun pour l’effectuer.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problèmes résolus
* Résolution du problème de fenêtre de synchronisation sur les tâches en arrière-plan pour la page Filtrage de partitions lors du passage à la page suivante

* Correction d’un bogue qui entraînait une violation d’accès lors de l’action personnalisée ConfigDB.

* Correction d’un bogue de récupération suite à un délai de connexion SQL

* Correction d’un bogue qui entraînait l’échec d’une vérification des prérequis pour les certificats avec caractères génériques SAN.

* Correction d’un bogue qui entraînait un incident d’exécution de miiserver.exe lors d’une exportation du Connecteur Azure AD

* Correction d’un bogue concernant les tentatives d’ouverture de session avec un mot de passe incorrect sur le contrôleur de domaine lors de l’exécution de l’Assistant Azure AD Connect dans le but de modifier la configuration.


#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Ajout de paramètres de confidentialité conformément aux exigences du Règlement général sur la protection des données (RGPD).  Le RGPD nous impose d’indiquer les types de données client qui sont partagées avec Microsoft (télémétrie, intégrité, etc.), de fournir des liens d’accès à la documentation en ligne détaillée et de permettre à nos clients de modifier leurs préférences.  Cette modification ajoute les éléments suivants :


    - notification concernant la confidentialité et le partage des données sur la nouvelle page de conditions du contrat de licence (CLUF) d’installation ;
    - notification concernant la confidentialité et le partage des données sur la page de mise à niveau ;
    - nouvelle tâche supplémentaire « Paramètres de confidentialité » permettant à l’utilisateur de modifier ses préférences.

* Télémétrie applicative : l’administrateur peut activer/désactiver cette catégorie de données à sa convenance.

* Données d’intégrité Azure AD : l’administrateur doit accéder au portail de contrôle d’intégrité pour contrôler ses paramètres d’intégrité.
   Une fois que la stratégie du service a été modifiée, les agents la lisent et la mettent en œuvre.

* Ajout d’actions de configuration d’écriture différée sur les appareils et d’une barre de progression pour l’initialisation de la page.

* Amélioration des diagnostics généraux avec un rapport HTML et une collecte de données complète dans un rapport au format ZIP/HTML.

* Amélioration de la fiabilité des mises à niveau automatiques et ajout d’une télémétrie supplémentaire pour garantir la possibilité de détermination de l’intégrité du serveur.

* Restriction des autorisations disponibles pour les comptes privilégiés sur le compte du Connecteur AD.

  * Dans le cas des nouvelles installations, l’Assistant restreindra les autorisations dont disposent les comptes privilégiés sur le compte MSOL après la création de ce dernier.

Les modifications prendront en charge les éléments suivants :
1. Installations rapides
2. Installations personnalisées avec création automatique de compte

* Modification du programme d’installation afin qu’il ne nécessite plus de privilège d’administrateur système pour une nouvelle installation d’Azure AD Connect.

* Ajout d’un nouvel utilitaire destiné à résoudre les problèmes de synchronisation pour un objet spécifique. Cet utilitaire est accessible sous l’option « Troubleshoot Object Synchronization » (Résoudre les problèmes de synchronisation d’objets) de la tâche supplémentaire de résolution des problèmes disponible dans l’Assistant Azure AD Connect. Actuellement, cet utilitaire procède aux vérifications suivantes :

  * détection d’une incompatibilité de nom d’utilisateur principal (UPN) entre l’objet utilisateur synchronisé et le compte d’utilisateur dans le locataire Azure AD ;
  * vérification si l’objet a été exclu de la synchronisation en raison d’un filtrage de domaine ;
  * vérification si l’objet a été exclu de la synchronisation en raison d’un filtrage d’unité d’organisation.

* Ajout d’un nouvel utilitaire destiné à synchroniser le hachage de mot de passe actuel stocké dans le service Active Directory local pour un compte d’utilisateur spécifique.

Cet utilitaire ne nécessite aucune modification de mot de passe. Il est accessible sous l’option « Troubleshoot Password Hash Synchronization » (Résoudre les problèmes de synchronisation de hachage de mot de passe) de la tâche supplémentaire de résolution des problèmes disponible dans l’Assistant Azure AD Connect.






## <a name="116540"></a>1.1.654.0
État : 12 décembre 2017

>[!NOTE]
>Il s’agit d’un correctif de sécurité pour Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Une amélioration a été ajoutée à Azure AD Connect version 1.1.654.0 (et ultérieure) pour s’assurer que les changements d’autorisations recommandés décrits dans la section [Verrouiller l’accès au compte AD DS](#lock) sont appliqués automatiquement quand Azure AD Connect crée le compte AD DS. 

- Lors de la configuration d’Azure AD Connect, l’administrateur qui effectue l’installation peut fournir un compte AD DS existant ou laisser Azure AD Connect créer automatiquement le compte. Les changements d’autorisations sont appliquées automatiquement au compte AD DS créé par Azure AD Connect pendant l’installation. Ils ne sont pas appliqués au compte AD DS existant fourni par l’administrateur lors de l’installation.
- Pour les clients ayant effectué une mise à niveau à partir d’une version antérieure d’Azure AD Connect vers la version 1.1.654.0 (ou ultérieure), les changements d’autorisations ne seront pas appliqués rétroactivement aux comptes AD DS existants créés avant la mise à niveau. Ils seront appliqués uniquement aux nouveaux comptes AD DS créés après la mise à niveau. Cela se produit quand vous ajoutez de nouvelles forêts Active Directory à synchroniser avec Azure AD.

>[!NOTE]
>Cette version supprime la vulnérabilité uniquement pour les nouvelles installations d’Azure AD Connect où le compte de service est créé par le processus d’installation. Pour les installations existantes, ou dans les cas où vous fournissez vous-même le compte, vous devez vous assurer que cette vulnérabilité n’existe pas.

#### <a name="lock"></a>Verrouiller l’accès au compte AD DS
Verrouillez l’accès au compte AD DS en implémentant les changements d’autorisations suivants dans l’annuaire AD local :  

*   Désactivez l’héritage sur l’objet spécifié
*   Supprimez toutes les entrées de contrôle d'accès sur l’objet spécifique, à l’exception de celles propres à SELF. Il faut conserver les autorisations par défaut intactes quand il s’agit de SELF.
*   Attribuez ces autorisations spécifiques :

type     | NOM                          | Access               | S'applique à
---------|-------------------------------|----------------------|--------------|
AUTORISER    | SYSTEM                        | Contrôle total         | Cet objet  |
AUTORISER    | Administrateurs de l’entreprise             | Contrôle total         | Cet objet  |
AUTORISER    | Admins du domaine                 | Contrôle total         | Cet objet  |
AUTORISER    | Administrateurs                | Contrôle total         | Cet objet  |
AUTORISER    | Contrôleurs de domaine d’entreprise | Lister le contenu        | Cet objet  |
AUTORISER    | Contrôleurs de domaine d’entreprise | Lire toutes les propriétés  | Cet objet  |
AUTORISER    | Contrôleurs de domaine d’entreprise | Autorisations de lecture     | Cet objet  |
AUTORISER    | Utilisateurs authentifiés           | Lister le contenu        | Cet objet  |
AUTORISER    | Utilisateurs authentifiés           | Lire toutes les propriétés  | Cet objet  |
AUTORISER    | Utilisateurs authentifiés           | Autorisations de lecture     | Cet objet  |

Pour renforcer les paramètres pour le compte AD DS, vous pouvez exécuter [ce script PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Le script PowerShell affecte les autorisations mentionnées ci-dessus au compte AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Script PowerShell pour renforcer un compte de service existant

Pour utiliser le script PowerShell afin d’appliquer ces paramètres à un compte AD DS existant (fourni par votre organisation ou créé par une précédente installation d’Azure AD Connect), téléchargez le script à partir du lien fourni ci-dessus.

##### <a name="usage"></a>Usage :

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Where 

**$ObjectDN** = compte Active Directory dont les autorisations doivent être renforcées.

**$Credential** = informations d’identification de l’administrateur qui dispose des privilèges nécessaires pour restreindre les autorisations sur le compte $ObjectDN. Il s’agit généralement de l’administrateur d’entreprise ou de domaine. Utilisez le nom de domaine complet du compte d’administrateur afin d’éviter les échecs de recherche de compte. Exemple : contoso.com\admin.

>[!NOTE] 
>$credential.UserName doit être au format nom_de_domaine_complet\nom_utilisateur. Exemple : contoso.com\admin. 

##### <a name="example"></a>Exemple :

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Cette vulnérabilité a-t-elle été exploitée pour obtenir un accès non autorisé ?

Pour voir si cette vulnérabilité a été exploitée afin de compromettre votre configuration Azure AD Connect, vous devez vérifier la date de la dernière réinitialisation du mot de passe du compte de service.  Si l’horodatage est inattendu, vous devez étudier de manière plus approfondie les circonstances de cet événement de réinitialisation de mot de passe par le biais du journal des événements.

Pour plus d’informations, consultez [Avis de sécurité Microsoft 4056318](https://technet.microsoft.com/library/security/4056318).

## <a name="116490"></a>1.1.649.0
Statut : 27 octobre 2017

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problème résolu
* Résolution d’un problème de compatibilité entre Azure AD Connect et Azure AD Connect Health Agent (pour la synchronisation). Ce problème affecte les clients qui effectuent une mise à niveau sur place d’Azure AD Connect vers la version 1.1.647.0, mais qui ont actuellement la version 3.0.127.0 de Health Agent. Après la mise à niveau, Health Agent ne peut plus envoyer les données d’intégrité sur le service de synchronisation Azure AD Connect à Azure AD Health Service. Avec ce correctif, Health Agent version 3.0.129.0 est installé lors de la mise à niveau sur place d’Azure AD Connect. Health Agent version 3.0.129.0 n’a pas de problème de compatibilité avec Azure AD Connect version 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Statut : 19 octobre 2017

> [!IMPORTANT]
> Il existe un problème de compatibilité connu entre la version 1.1.647.0 d’Azure AD Connect et la version 3.0.127.0 d’Azure AD Connect Health Agent (pour la synchronisation). Ce problème empêche Health Agent d’envoyer des données d’intégrité sur le service de synchronisation Azure AD Connect (y compris les données relatives aux erreurs de synchronisation d’objets et à l’historique des exécutions) au service de contrôle d’intégrité Azure AD. Avant de mettre à niveau manuellement votre déploiement d’Azure AD Connect vers la version 1.1.647.0, vérifiez la version d’Azure AD Connect Health Agent qui est actuellement installée sur votre serveur Azure AD Connect. Pour cela, accédez au *Panneau de configuration → Ajout/Suppression de programmes*, puis recherchez l’application *Microsoft Azure AD Connect Health Agent pour la synchronisation*. Si sa version est 3.0.127.0, il est recommandé d’attendre que la prochaine version d’Azure AD Connect soit disponible avant d’effectuer la mise à niveau. Si la version d’Health Agent n’est pas 3.0.127.0, vous pouvez procéder à la mise à niveau sur place manuellement. Notez que ce problème n’affecte pas les mises à niveau de type « swing », ni les nouvelles installations d’Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Problèmes résolus
* Résolution d’un problème concernant la tâche *Modifier la connexion utilisateur* dans l’Assistant Azure AD Connect :

  * Ce problème se produit lorsque la synchronisation de mot de passe est **activée** dans votre déploiement Azure AD Connect, et que vous tentez de définir la méthode de connexion utilisateur sur *Authentification directe*. Avant que la modification ne soit appliquée, l’Assistant affiche à tort le message suivant *Désactiver la synchronisation de mot de passe*. Toutefois, la synchronisation de mot de passe reste activée une fois la modification appliquée. Avec ce correctif, l’Assistant n’affiche plus ce message.

  * Par défaut, l’Assistant ne désactive pas la synchronisation de mot de passe lorsque vous mettez à jour la méthode de connexion utilisateur à l’aide de la tâche *Modifier la connexion utilisateur*. Cela permet d’éviter une interruption pour les clients qui souhaitent conserver la synchronisation de mot de passe, même s’ils choisissent d’activer l’authentification directe ou la fédération comme méthode principale d’authentification utilisateur.
  
  * Si vous souhaitez désactiver la synchronisation de mot de passe après avoir modifié la méthode d’authentification utilisateur, exécutez la tâche *Personnaliser la configuration de la synchronisation* dans l’Assistant. Lorsque vous accédez à la page *Fonctionnalités facultatives*, désactivez l’option *Synchronisation de mot de passe*.
  
  * Notez que le même problème se produit également si vous tentez d’activer ou de désactiver l’authentification unique transparente. Mettons que vous disposiez d’un déploiement d’Azure AD Connect existant dans lequel est activée la synchronisation de mot de passe et que la méthode d’authentification utilisateur soit déjà configurée sur *Authentification directe*. À l’aide de la tâche *Modifier la connexion utilisateur*, vous tentez de cocher ou de décocher l’option *Activer l’authentification unique transparente* alors que la méthode d’authentification utilisateur est configurée sur « Authentification directe ». Avant que la modification ne soit appliquée, l’Assistant affiche à tort le message suivant *Désactiver la synchronisation de mot de passe*. Toutefois, la synchronisation de mot de passe reste activée une fois la modification appliquée. Avec ce correctif, l’Assistant n’affiche plus ce message.

* Résolution d’un problème concernant la tâche *Modifier la connexion utilisateur* dans l’Assistant Azure AD Connect :

   * Ce problème se produit lorsque la synchronisation de mot de passe est **désactivée** dans votre déploiement Azure AD Connect, et que vous tentez de définir la méthode de connexion utilisateur sur *Authentification directe*. Lorsque la modification est appliquée, l’Assistant active à la fois l’authentification directe et la synchronisation de mot de passe. Avec ce correctif, l’Assistant n’active plus la synchronisation de mot de passe.

  * Auparavant, la synchronisation de mot de passe était un prérequis pour l’activation de l’authentification directe. Lorsque vous définissiez la méthode de connexion utilisateur sur *Authentification directe*, l’Assistant activait à la fois l’authentification directe et la synchronisation de mot de passe. Depuis peu, la synchronisation de mot de passe ne fait plus partie des prérequis. Dans la version 1.1.557.0 d’Azure AD Connect, la synchronisation de mot de passe n’était plus activée lorsque vous définissiez la méthode de connexion utilisateur sur *Authentification directe*. Toutefois, cette modification concernait uniquement les installations Azure AD Connect. Avec ce correctif, cette même modification s’applique désormais à la tâche *Modifier la connexion utilisateur*.
  
  * Notez que le même problème se produit également si vous tentez d’activer ou de désactiver l’authentification unique transparente. Mettons que vous disposiez d’un déploiement d’Azure AD Connect existant dans lequel est désactivée la synchronisation de mot de passe et que la méthode d’authentification utilisateur soit déjà configurée sur *Authentification directe*. À l’aide de la tâche *Modifier la connexion utilisateur*, vous tentez de cocher ou de décocher l’option *Activer l’authentification unique transparente* alors que la méthode d’authentification utilisateur est configurée sur « Authentification directe ». Lorsque la modification est appliquée, l’Assistant active la synchronisation de mot de passe. Avec ce correctif, l’Assistant n’active plus la synchronisation de mot de passe. 

* Résolution d’un problème qui provoquait l’échec de la mise à niveau d’Azure AD Connect avec l’erreur *Impossible de mettre à niveau le service de synchronisation*. En outre, le service de synchronisation ne peut plus démarrer et affiche l’erreur d’événement*Le service n’a pas pu démarrer, car la version de la base de données est plus récente que celle des binaires installés*. Ce problème se produit lorsque l’administrateur qui effectue la mise à niveau ne dispose pas de privilèges sysadmin pour le serveur SQL qui est utilisé par Azure AD Connect. Avec ce correctif, Azure AD Connect nécessite uniquement que l’administrateur dispose de privilèges db_owner pour la base de données ADSync pendant la mise à niveau.

* Résolution d’un problème avec la mise à niveau d’Azure AD Connect qui affecte les clients ayant activé l’option [Authentification unique transparente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Après la mise à niveau d’Azure AD Connect, l’authentification unique transparente apparaît à tort comme étant désactivée dans l’Assistant Azure AD Connect, alors qu’elle est toujours activée et entièrement fonctionnelle. Avec ce correctif, cette fonctionnalité apparaît désormais comme étant activée dans l’Assistant.

* Résolution d’un problème lié à l’affichage du message *Configurer l’ancre source* dans la page *Prêt à configurer* de l’Assistant Azure AD Connect, même lorsqu’aucune modification liée à l’ancre source n’avait été apportée.

* Lorsqu’il effectue manuellement une mise à niveau sur place d’Azure AD Connect, le client doit fournir les informations d’identification d’administrateur général du locataire Azure AD correspondant. Auparavant, la mise à niveau pouvait se poursuivre, même si les informations d’identification d’administrateur général fournies appartenaient à un autre locataire Azure AD. Même si la mise à niveau semblait se terminer correctement, certaines configurations n’étaient pas conservées après la mise à niveau. Avec ce correctif, l’Assistant ne permet plus d’effectuer la mise à niveau si les informations d’identification fournies ne correspondent pas au locataire client Azure AD.

* Suppression d’une logique redondante qui redémarrait inutilement le service Azure AD Connect Health au début d’une mise à niveau manuelle.


#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Ajout d’une logique pour simplifier les étapes de configuration d’Azure AD Connect avec Microsoft Germany Cloud. Auparavant, vous étiez invité à mettre à jour certaines clés de Registre sur le serveur Azure AD Connect pour que celui-ci fonctionne correctement avec Microsoft Germany Cloud, comme décrit dans cet article. Désormais, Azure AD Connect peut détecter automatiquement si votre locataire se trouve dans Microsoft Germany Cloud, en se basant sur les informations d’identification d’administrateur général fournies pendant l’installation.

### <a name="azure-ad-connect-sync"></a>Synchronisation d’Azure AD Connect
>[!NOTE]
> Remarque : Le service de synchronisation comprend une interface WMI qui vous permet de développer votre propre planificateur personnalisé. Cette interface est désormais dépréciée et sera supprimée des prochaines versions d’Azure AD Connect après le 30 juin 2018. Les clients qui souhaitent personnaliser le calendrier de synchronisation doivent utiliser le [Planificateur intégré (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Problèmes résolus
* Lorsque l’Assistant Azure AD Connect créait le compte de connecteur AD pour la synchronisation des modifications apportées dans l’instance locale d’Active Directory, il n’attribuait pas l’autorisation au compte pour lire les objets PublicFolder. Ce problème concernait l’installation Express et l’installation personnalisée. Ce problème a été résolu.

* Résolution d’un problème concernant la page de résolution des problèmes de l’Assistant Azure AD Connect qui ne s’affichait pas correctement pour les administrateurs utilisant Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Désormais, lorsque l’utilisateur cherche à résoudre des problèmes liés à la synchronisation des mots de passe à l’aide de la page Résolution des problèmes de l’Assistant Azure AD Connect, cette page retourne l’état spécifique au domaine.

* Auparavant, si vous tentiez d’activer la synchronisation de hachage de mot de passe, Azure AD Connect ne vérifiait pas si le compte de connecteur AD disposait des autorisations nécessaires pour synchroniser les hachages de mot de passe avec l’instance locale d’AD. Désormais, l’Assistant Azure AD Connect vérifie et vous avertit si le compte de connecteur AD ne dispose pas d’autorisations suffisantes.

### <a name="ad-fs-management"></a>Gestion AD FS
#### <a name="fixed-issue"></a>Problème résolu
* Résolution d’un problème lié à l’utilisation de la fonctionnalité [msDS-ConsistencyGuid en tant que sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor). Ce problème affectait les clients ayant sélectionné *Fédération avec AD FS* comme méthode d’authentification utilisateur. Lorsque vous exécutiez la tâche *Configurer l’ancre source* dans l’Assistant, Azure AD Connect se mettait à utiliser *ms-DS-ConsistencyGuid en tant qu’attribut de source pour immutableId. Avec ce correctif, Azure AD Connect tente désormais de mettre à jour les règles de revendication pour ImmutableId dans AD FS. Auparavant, cette étape échouait, car Azure AD Connect ne disposait pas des informations d’identification administrateur nécessaires pour configurer AD FS. Avec ce correctif, Azure AD Connect invite désormais l’utilisateur à entrer les informations d’identification d’administrateur pour AD FS lorsque vous exécutez la tâche *Configurer l’ancre source*.



## <a name="116140"></a>1.1.614.0
Statut : 5 septembre 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Problèmes connus
* Il existe un problème connu qui provoque l’échec de la mise à niveau d’Azure AD Connect avec l’erreur *Impossible de mettre à niveau le service de synchronisation*. En outre, le service de synchronisation ne peut plus démarrer et affiche l’erreur d’événement*Le service n’a pas pu démarrer, car la version de la base de données est plus récente que celle des binaires installés*. Ce problème se produit lorsque l’administrateur qui effectue la mise à niveau ne dispose pas de privilèges sysadmin pour le serveur SQL qui est utilisé par Azure AD Connect. Les autorisations dbo ne sont pas suffisantes.

* Il existe un problème connu avec la mise à niveau d’Azure AD Connect qui affecte les clients ayant activé l’[authentification unique transparente](active-directory-aadconnect-sso.md). Après la mise à niveau d’Azure AD Connect, la fonctionnalité apparaît comme étant désactivée dans l’Assistant, même si elle reste activée. Un correctif pour ce problème sera fourni dans une version ultérieure. Vous pouvez résoudre ce problème d’affichage manuellement en activant l’authentification unique transparente dans l’Assistant.

#### <a name="fixed-issues"></a>Problèmes résolus
* Correction d’un problème qui empêchait Azure AD Connect de mettre à jour les règles de revendications dans les services ADFS locaux tout en activant la fonctionnalité [msDS-ConsistencyGuid en tant que Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor). Le problème se produit si vous essayez d’activer la fonctionnalité pour un déploiement Azure AD Connect existant pour lequel les services ADFS sont configurés en tant que méthode de connexion. Le problème se produit parce que l’Assistant n’invite pas à indiquer les informations d’identification ADFS avant d’essayer de mettre à jour les règles de revendications dans ADFS.
* Correction d’un problème qui provoque l’échec de l’installation d’Azure AD Connect si l’authentification NTLM est désactivée dans la forêt AD locale. Ce problème est dû au fait que l’Assistant Azure AD Connect ne fournit pas les informations d’identification complètes lors de la création des contextes de sécurité nécessaires pour l’authentification Kerberos. Cela provoque l’échec de l’authentification Kerberos et l’utilisation de l’authentification NTLM par l’Assistant Azure AD Connect.

### <a name="azure-ad-connect-sync"></a>Synchronisation d’Azure AD Connect
#### <a name="fixed-issues"></a>Problèmes résolus
* Correction d’un problème qui empêche la création d’une règle de synchronisation si l’attribut Tag n’est pas renseigné.
* Correction d’un problème qui contraint Azure AD Connect à se connecter à AD local pour la synchronisation du mot de passe à l’aide de NTLM même si Kerberos est disponible. Ce problème se produit si la topologie AD locale contient un ou plusieurs contrôleurs de domaine qui ont été restaurés à partir d’une sauvegarde.
* Correction d’un problème qui provoque l’exécution inutile d’étapes de synchronisation complète après la mise à niveau. En général, l’exécution d’étapes de synchronisation complète est nécessaire après la mise à niveau en cas de modifications des règles de synchronisation par défaut. Ce problème est dû à une erreur dans la logique de détection des modifications, qui détecte incorrectement une modification quand elle rencontre une expression de règle de synchronisation avec des caractères de saut de ligne. Les caractères de saut de ligne sont insérés dans une expression de règle de synchronisation pour améliorer la lisibilité.
* Correction d’un problème qui peut entraîner le fonctionnement incorrect du serveur Azure AD Connect après la mise à niveau automatique. Ce problème affecte les serveurs Azure AD Connect avec la version 1.1.443.0 (ou antérieure). Pour plus d’informations sur ce problème, consultez l’article [Azure AD Connect is not working correctly after an automatic upgrade (Azure AD Connect ne fonctionne pas correctement après une mise à niveau automatique)](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Correction d’un problème qui peut entraîner une nouvelle tentative de mise à niveau automatique toutes les cinq minutes quand des erreurs sont rencontrées. Avec le correctif, la mise à niveau automatique effectue une nouvelle tentative avec interruption exponentielle quand des erreurs sont rencontrées.
* Correction d’un problème où l’événement de synchronisation du mot de passe 611 est affiché de manière incorrecte dans les journaux des événements d’application Windows avec la mention **Informationnel** au lieu de **Erreur**. L’événement 611 est généré chaque fois que la synchronisation du mot de passe rencontre un problème. 
* Correction d’un problème dans l’Assistant Azure AD Connect qui autorise l’activation de la fonctionnalité d’écriture différée de groupe sans sélectionner d’unité d’organisation requise pour l’écriture différée de groupe.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Ajout d’une tâche de résolution des problèmes à l’Assistant Azure AD Connect sous Tâches supplémentaires. Les clients peuvent utiliser cette tâche pour résoudre les problèmes liés à la synchronisation du mot de passe et pour recueillir des diagnostics généraux. À l’avenir, la tâche de résolution des problèmes sera étendue pour inclure d’autres problèmes liés à la synchronisation d’annuaires.
* Azure AD Connect prend désormais en charge un nouveau mode d’installation nommé **Utiliser une base de données existante**. Ce mode d’installation permet aux clients d’installer Azure AD Connect en spécifiant une base de données ADSync existante. Pour plus d’informations sur cette fonctionnalité, consultez l’article [Utiliser une base de données existante](active-directory-aadconnect-existing-database.md).
* Pour améliorer la sécurité, Azure AD Connect utilise désormais par défaut TLS 1.2 pour se connecter à Azure AD pour la synchronisation d’annuaires. Auparavant, la valeur par défaut était TLS 1.0.
* Quand l’agent de synchronisation des mots de passe Azure AD Connect démarre, il tente de se connecter au point de terminaison connu Azure AD pour effectuer la synchronisation du mot de passe. Une fois la connexion établie, il est redirigé vers un point de terminaison propre à la région. Auparavant, l’agent de synchronisation des mots de passe mettait en cache le point de terminaison propre à la région jusqu’à ce qu’il soit redémarré. Désormais, l’agent efface le cache et effectue une nouvelle tentative avec le point de terminaison connu s’il rencontre un problème de connexion avec le point de terminaison propre à la région. Ce changement garantit que la synchronisation du mot de passe peut basculer vers un autre point de terminaison propre à la région quand le point de terminaison propre à la région mis en cache n’est plus disponible.
* Pour synchroniser les modifications à partir d’une forêt AD locale, un compte de domaine AD DS est nécessaire. Vous pouvez (i) créer vous-même le compte AD DS et fournir ses informations d’identification à Azure AD Connect, ou (ii) entrer les informations d’identification d’un administrateur d’entreprise et laisser Azure AD Connect créer le compte AD DS pour vous. Auparavant, l’option (i) était l’option par défaut dans l’Assistant Azure AD Connect. Désormais, il s’agit de l’option (ii).

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Ajout de la prise en charge de Microsoft Azure Government Cloud et Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Gestion AD FS
#### <a name="fixed-issues"></a>Problèmes résolus
* L’applet de commande Initialize-ADSyncNGCKeysWriteBack dans le module Powershell de préparation AD plaçait incorrectement le conteneur d’inscription d’appareil dans la liste de contrôle d’accès, et héritait par conséquent uniquement des autorisations existantes.  Une mise à jour a été appliquée afin que le compte de service de synchronisation dispose des autorisations appropriées.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* La tâche AAD Connect Vérifier la connexion AD FS a été mise à jour afin de vérifier les connexions par rapport à Microsoft Online, et pas simplement la récupération de jeton à partir des services de fédération Active Directory (AD FS).
* Quand vous configurez une nouvelle batterie AD FS à l’aide d’AAD connect, la page invitant à fournir des informations d’identification AD FS a été déplacée et apparaît maintenant avant que l’utilisateur soit invité à spécifier les serveurs AD FS et WAP.  Cela permet à AAD Connect de vérifier que le compte spécifié dispose des autorisations appropriées.
* La mise à niveau d’AAD Connect n’échoue plus si la mise à jour de l’approbation AAD AD FS échoue.  Si cela se produit, un message d’avertissement approprié est affiché, et l’utilisateur doit réinitialiser l’approbation par le biais de la tâche supplémentaire AAD Connect.

### <a name="seamless-single-sign-on"></a>Authentification unique transparente
#### <a name="fixed-issues"></a>Problèmes résolus
* Correction d’un problème qui fait en sorte que l’Assistant Azure AD Connect retourne une erreur si vous essayez d’activer l’[authentification unique transparente](active-directory-aadconnect-sso.md). Le message d’erreur est *« Échec de la configuration de l’agent d’authentification Microsoft Azure AD Connect. »* Ce problème affecte les clients existants qui avaient manuellement mis à niveau la préversion des agents d’authentification pour l’[authentification directe](active-directory-aadconnect-sso.md) d’après les étapes décrites dans cet [article](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
État : 23 juillet 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problème résolu

* Correction d’un problème qui provoquait la suppression de la règle de synchronisation prête à l’emploi « Out to AD - user ImmutableId » :

  * Le problème se produit lors de la mise à niveau d’Azure AD Connect, ou lorsque l’option de tâche de *mise à jour de la configuration de synchronisation* de l’assistant Azure AD Connect est utilisée pour mettre à jour la configuration de la synchronisation Azure AD Connect.
  
  * Cette règle de synchronisation s’applique aux clients qui ont activé la fonctionnalité [msDS-ConsistencyGuid en tant que sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Cette fonctionnalité a été introduite dans la version 1.1.524.0 et les versions ultérieures. Lorsque la règle de synchronisation est supprimée, Azure AD Connect ne peut plus remplir l’attribut ms-DS-ConsistencyGuid local avec la valeur de l’attribut ObjectGuid. Cela n’empêche pas la configuration de nouveaux utilisateurs dans Azure AD.
  
  * Le correctif garantit que la règle de synchronisation ne sera plus supprimée pendant la mise à niveau, ou au cours de la modification de la configuration, tant que la fonctionnalité est activée. Il garantit également que la règle de synchronisation est ajoutée après la mise à niveau vers cette version d’Azure AD Connect pour les clients existants ayant été affectés par ce problème.

* Correction d’un problème qui oblige les règles de synchronisation prédéfinies à avoir une valeur de précédence inférieure à 100 :

  * En général, les valeurs de précédence 0 - 99 sont réservées aux règles de synchronisation personnalisées. Au cours de la mise à niveau, les valeurs de précédence des règles de synchronisation prêtes à l’emploi sont mises à jour pour prendre en compte les modifications apportées aux règles de synchronisation. En raison de ce problème, les règles de synchronisation prêtes à l’emploi peuvent se voir attribuer une valeur de précédence inférieure à 100.
  
  * Le correctif empêche l’apparition du problème au cours d’une mise à niveau. Toutefois, il ne restaure pas les valeurs de précédence pour les clients existants ayant été affectés par le problème. Un correctif distinct sera fourni ultérieurement afin de faciliter la restauration.

* Correction d’un problème durant lequel [l’écran Filtrage par domaine ou unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) de l’Assistant Azure AD Connect affiche l’option *Synchroniser tous les domaines et toutes les unités d’organisation* comme étant sélectionnée, même si le filtrage basé sur une unité d’organisation est activé.

*   Correction d’un problème qui entraînait l’affichage d’une erreur par l’[écran Configurer des partitions d’annuaire](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) de Synchronization Service Manager lorsque l’utilisateur cliquait sur le bouton *Actualiser*. Le message d’erreur est le suivant : *« An error was encountered while refreshing domains: Unable to cast object of type ‘System.Collections.ArrayList’ to type ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject »* (Une erreur s’est produite lors de l’actualisation des domaines : impossible de convertir l’objet de type 'System.Collections.ArrayList' en type 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject). L’erreur se produit lorsque le nouveau domaine Active Directory a été ajouté à une forêt Active Directory existante et que vous tentez de mettre à jour Azure AD Connect à l’aide du bouton Actualiser.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* La[fonctionnalité de mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) a été développée pour prendre en charge des clients présentant les configurations suivantes :
  * Vous avez activé la fonctionnalité Écriture différée des appareils.
  * Vous avez activé la fonctionnalité Écriture différée de groupe.
  * L’installation n’est pas une configuration rapide ou une mise à niveau DirSync.
  * Vous avez plus de 100 000 objets dans le métaverse.
  * Vous vous connectez à plusieurs forêts. L’installation rapide se connecte à une seule forêt.
  * Le compte de connecteur AD n’est plus le compte MSOL_ par défaut.
  * Le serveur est défini sur le mode de transit.
  * Vous avez activé la fonctionnalité Écriture différée d’utilisateur.
  
  >[!NOTE]
  >L’extension de la portée de la fonctionnalité de mise à niveau automatique affecte les clients utilisant Azure AD Connect build 1.1.105.0 et les versions ultérieures. Si vous ne voulez pas que votre serveur Azure AD Connect soit mis à niveau automatiquement, vous devez exécuter la cmdlet suivante sur votre serveur Azure AD Connect : `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Pour en savoir plus sur l’activation/la désactivation de la mise à niveau automatique, reportez-vous à l’article [Azure AD Connect : Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
État : ne sera pas mis en production. Les modifications apportées à ce build sont incluses dans la version 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problème résolu

* Correction d’un problème qui provoquait la suppression de la règle de synchronisation prête à l’emploi « Out to AD - user ImmutableId » lors de la mise à jour de la configuration de filtrage basé sur une unité d’organisation. Cette règle de synchronisation est requise pour la fonctionnalité [msDS-ConsistencyGuid en tant que sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Correction d’un problème durant lequel [l’écran Filtrage par domaine ou unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) de l’Assistant Azure AD Connect affiche l’option *Synchroniser tous les domaines et toutes les unités d’organisation* comme étant sélectionnée, même si le filtrage basé sur une unité d’organisation est activé.

*   Correction d’un problème qui entraînait l’affichage d’une erreur par l’[écran Configurer des partitions d’annuaire](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) de Synchronization Service Manager lorsque l’utilisateur cliquait sur le bouton *Actualiser*. Le message d’erreur est le suivant : *« An error was encountered while refreshing domains: Unable to cast object of type ‘System.Collections.ArrayList’ to type ‘Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject »* (Une erreur s’est produite lors de l’actualisation des domaines : impossible de convertir l’objet de type 'System.Collections.ArrayList' en type 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject). L’erreur se produit lorsque le nouveau domaine Active Directory a été ajouté à une forêt Active Directory existante et que vous tentez de mettre à jour Azure AD Connect à l’aide du bouton Actualiser.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* La[fonctionnalité de mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) a été développée pour prendre en charge des clients présentant les configurations suivantes :
  * Vous avez activé la fonctionnalité Écriture différée des appareils.
  * Vous avez activé la fonctionnalité Écriture différée de groupe.
  * L’installation n’est pas une configuration rapide ou une mise à niveau DirSync.
  * Vous avez plus de 100 000 objets dans le métaverse.
  * Vous vous connectez à plusieurs forêts. L’installation rapide se connecte à une seule forêt.
  * Le compte de connecteur AD n’est plus le compte MSOL_ par défaut.
  * Le serveur est défini sur le mode de transit.
  * Vous avez activé la fonctionnalité Écriture différée d’utilisateur.
  
  >[!NOTE]
  >L’extension de la portée de la fonctionnalité de mise à niveau automatique affecte les clients utilisant Azure AD Connect build 1.1.105.0 et les versions ultérieures. Si vous ne voulez pas que votre serveur Azure AD Connect soit mis à niveau automatiquement, vous devez exécuter la cmdlet suivante sur votre serveur Azure AD Connect : `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Pour en savoir plus sur l’activation/la désactivation de la mise à niveau automatique, reportez-vous à l’article [Azure AD Connect : Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
État : juillet 2017

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problème résolu
* Correction d’un problème lié à la cmdlet Initialize-ADSyncDomainJoinedComputerSync, qui modifiait le domaine vérifié configuré sur l’objet de point de connexion de service existant, même si ce domaine était toujours valide. Ce problème se produit lorsque votre locataire Azure AD dispose de plusieurs domaines vérifiés qui peuvent être utilisés pour configurer le point de connexion de service.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* La réécriture du mot de passe est désormais disponible en version préliminaire avec le cloud Microsoft Azure Government et Microsoft Cloud Allemagne. Pour en savoir plus sur la prise en charge d’Azure AD Connect pour les différentes instances de service, reportez-vous à l’article [Azure AD Connect : Considérations spéciales relatives aux instances](active-directory-aadconnect-instances.md).

* La cmdlet Initialize-ADSyncDomainJoinedComputerSync dispose désormais d’un nouveau paramètre facultatif, nommé AzureADDomain. Ce paramètre vous permet d’indiquer quel domaine vérifié doit être utilisé pour configurer le point de connexion de service.

### <a name="pass-through-authentication"></a>Authentification directe

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Le nom de l’agent requis pour l’authentification directe n’est plus *Connecteur Microsoft Azure AD Application Proxy*, mais *Agent d’authentification Microsoft Azure AD Connect*.

* L’activation de l’authentification directe ne permet plus la synchronisation du hachage de mot de passe par défaut.


## <a name="115530"></a>1.1.553.0
État : juin 2017

> [!IMPORTANT]
> Des modifications de schéma et de règle de synchronisation ont été introduites dans cette build. Le service de synchronisation Azure AD Connect déclenchera des étapes d’importation et de synchronisation complètes après la mise à niveau. Ces modifications sont décrites en détail ci-dessous. Consultez l’article [Comment différer la synchronisation complète après la mise à niveau](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade) pour reporter temporairement les étapes d’importation et de synchronisation complètes après la mise à niveau.
>
>

### <a name="azure-ad-connect-sync"></a>Synchronisation d’Azure AD Connect

#### <a name="known-issue"></a>Problème connu
* Il existe un problème qui affecte les clients utilisant le[filtrage basé sur une unité d’organisation](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) avec la synchronisation Azure AD Connect. Lorsque vous accédez à la [page Domaine et Filtrage par unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’assistant Azure AD Connect, le comportement suivant est attendu :
  * Si le filtrage basé sur une unité d’organisation est activé, l’option **Synchroniser les domaines et les unités d’organisation sélectionnés** est sélectionnée.
  * Dans le cas contraire, l’option **Synchroniser tous les domaines et toutes les unités d’organisation** est sélectionnée.

Le problème qui se pose est le suivant : l’option **Synchroniser tous les domaines et toutes les unités d’organisation** est toujours sélectionnée lorsque vous exécutez l’Assistant.  Cela se produit même si le filtrage basé sur une unité d’organisation a été configuré précédemment. Avant d’enregistrer les modifications apportées à la configuration AAD Connect, assurez-vous que **l’option Synchroniser les domaines et les unités d’organisation sélectionnés est sélectionnée** et vérifiez que toutes les unités d’organisation qui doivent être synchronisées sont activées à nouveau. Dans le cas contraire, le filtrage basé sur une unité d’organisation sera désactivé.

#### <a name="fixed-issues"></a>Problèmes résolus

* Correction d’un problème lié à la réécriture du mot de passe, qui permet à un administrateur Azure Active Directory de réinitialiser le mot de passe d’un compte d’utilisateur privilégié Active Directory local. Le problème se produit lorsqu’Azure AD Connect se voit accorder l’autorisation de réinitialiser le mot de passe à la place du compte privilégié. Le problème est résolu dans cette version d’Azure AD Connect, car l’administrateur d’Azure AD n’est plus autorisé à réinitialiser le mot de passe d’un compte d’utilisateur privilégié AD local, sauf s’il est le propriétaire de ce compte. Pour en savoir plus, voir [Avis de sécurité Microsoft 4033453](https://technet.microsoft.com/library/security/4033453).

* Correction d’un problème lié à la fonctionnalité [msDS-ConsistencyGuid en tant que sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor), à cause duquel Azure AD Connect ne peut écrire de données en différé dans un attribut msDS-ConsistencyGuid local. Le problème se produit lorsqu’il y a plusieurs forêts AD locales ajoutées à Azure AD Connect et que *l’option Les identités utilisateurs existent sur plusieurs annuaires* est sélectionnée. Lorsqu’une configuration de ce type est utilisée, les règles de synchronisation résultantes ne remplissent pas l’attribut sourceAnchorBinary dans le métaverse. L’attribut sourceAnchorBinary est utilisé en tant qu’attribut source pour l’attribut msDS-ConsistencyGuid. Par conséquent, l’écriture différée vers l’attribut ms-DSConsistencyGuid n’a pas lieu. Afin de résoudre le problème, les règles de synchronisation suivantes ont été mises à jour pour faire en sorte que l’attribut sourceAnchorBinary du métaverse soit toujours rempli :
  * In from AD - InetOrgPerson AccountEnabled.xml
  * In from AD - InetOrgPerson Common.xml
  * In from AD - User AccountEnabled.xml
  * In from AD - User Common.xml
  * In from AD - User Join SOAInAAD.xml

* Auparavant, même si la fonctionnalité [msDS-ConsistencyGuid en tant que sourceAnchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) était désactivée, la règle de synchronisation « Out to AD – User ImmutableId » était encore ajoutée à Azure AD Connect. L’effet est sans gravité et n’entraîne pas l’écriture différée de l’attribut msDS-ConsistencyGuid. Pour éviter toute confusion, nous avons ajouté une logique pour vous assurer que la règle de synchronisation n’est ajoutée que lorsque la fonctionnalité est activée.

* Correction d’un problème qui provoquait l’échec de la synchronisation du hachage de mot de passe avec l’événement d’erreur 611. Ce problème se produit après qu’un ou plusieurs contrôleurs de domaine ont été supprimés de l’instance AD locale. À la fin de chaque cycle de synchronisation de mot de passe, le cookie de synchronisation émis par l’instance AD locale contient les ID d’appel des contrôleurs de domaine supprimés, avec 0 comme valeur USN (numéro de séquence de mise à jour). Le Gestionnaire de synchronisation des mots de passe ne parvient pas à conserver le cookie de synchronisation contenant la valeur USN de 0 et échoue avec l’événement d’erreur 611. Lors du prochain cycle de synchronisation, le Gestionnaire de synchronisation des mots de passe réutilise le dernier cookie de synchronisation persistant qui ne contient aucune valeur USN égale à 0. Cela entraîne la resynchronisation des mêmes modifications apportées aux mots de passe. Grâce à ce correctif, le Gestionnaire de synchronisation des mots de passe conserve correctement le cookie de synchronisation.

* Auparavant, même si la mise à niveau automatique était désactivée à l’aide de la cmdlet Set-ADSyncAutoUpgrade, le processus de mise à niveau automatique continuait de vérifier régulièrement les mises à niveau et s’appuyait sur le programme d’installation téléchargé pour respecter la désactivation. Grâce à ce correctif, le processus de mise à niveau automatique ne vérifie plus les mises à niveau régulièrement. Le correctif est appliqué automatiquement lorsque le programme d’installation de la mise à niveau de cette version d’Azure AD Connect est exécuté une fois.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Auparavant, la fonctionnalité [msDS-ConsistencyGuid en tant que Source Anchor](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) n’était disponible que pour les nouveaux déploiements. Désormais, elle est disponible pour les déploiements existants. Plus précisément :
  * Pour accéder à cette fonctionnalité, démarrez l’assistant Azure AD Connect, puis choisissez l’option *Mettre à jour sourceAnchor*.
  * Cette option n’est visible que pour les déploiements existants qui utilisent objectGuid comme attribut sourceAnchor.
  * Lorsque vous configurez l’option, l’assistant vérifie l’état de l’attribut msDS-ConsistencyGuid dans votre annuaire Active Directory local. Si l’attribut n’est configuré sur aucun objet utilisateur dans l’annuaire, l’assistant utilise msDS-ConsistencyGuid en tant qu’attribut sourceAnchor. Si l’attribut est configuré sur un ou plusieurs objets utilisateur dans l’annuaire, l’assistant détermine que l’attribut est utilisé par d’autres applications, qu’il n’est pas approprié en tant qu’attribut sourceAnchor et n’autorise pas le processus de modification de sourceAnchor à se poursuivre. Si vous êtes certain qu’attribut n’est pas utilisé par des applications existantes, contactez le Support technique pour plus d’informations sur la manière de supprimer l’erreur.

* Pour l’attribut **userCertificate** sur les objets périphériques, Azure AD Connect recherche désormais des valeurs de certificats requises pour [connecter des appareils joints au domaine à Azure AD pour Windows 10 ](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) et exclut le reste avant de se synchroniser avec Azure AD. La règle de synchronisation prête à l’emploi « Out to AAD - Device Join SOAInAD » a été mise à jour pour permettre ce comportement.

* Azure AD Connect prend désormais en charge l’écriture différée de l’attribut **cloudPublicDelegates** d’Exchange Online vers un attribut AD local **publicDelegates**. Cela permet à une boîte aux lettres Exchange Online d’obtenir des droits SendOnBehalfTo sur les boîtes aux lettres Exchange locales des utilisateurs. Une nouvelle règle de synchronisation prête à l’emploi « Out to AD - User Exchange hybride PublicDelegates writeback » a été ajoutée pour prendre en charge cette fonctionnalité. Cette règle n’est ajoutée à Azure AD Connect que lorsque la fonctionnalité Exchange hybride est activée.

*   Azure AD Connect prend désormais en charge la synchronisation de l’attribut **altRecipient** d’Azure AD. Afin de prendre en charge cette modification, les règles de synchronisation prêtes à l’emploi suivantes ont été mises à jour pour inclure le flux d’attributs requis :
  * Entrant depuis AD – Utilisateur Exchange
  * Out to AAD - User ExchangeOnline
  
* L’attribut **cloudSOAExchMailbox** du métaverse indique si un utilisateur donné possède une boîte aux lettres Exchange Online ou non. Sa définition a été mise à jour pour inclure les autres types RecipientDisplayTypes d’Exchange Online en tant que boîtes aux lettres de salle de conférence et d’équipement. Pour activer cette modification, la définition de l’attribut cloudSOAExchMailbox, qui se trouve sous la règle de synchronisation prête à l’emploi « In from AAD - User Exchange Hybrid », a été mise à jour à partir de :

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... vers ce qui suit :

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Ajout de l’ensemble de fonctions X509Certificate2-compatible pour la création d’expressions de règle de synchronisation pour gérer les valeurs de certificat dans l’attribut userCertificate :

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Sélectionnez|
    |CertKeyAlgorithmParams|CertHashString|Where|
    |||With|

* Les modifications de schéma suivantes ont été introduites pour permettre aux clients de créer des règles de synchronisation personnalisées afin de suivre les paramètres sAMAccountName, domainNetBios et domainFQDN pour les objets de groupe, ainsi que le paramètre distinguishedName pour les objets utilisateur :

  * Les attributs suivants ont été ajoutés au schéma MV :
    * Groupe : AccountName
    * Groupe : domainNetBios
    * Groupe : domainFQDN
    * Personne : distinguishedName

  * Les attributs suivants ont été ajoutés au schéma de connecteur Azure AD :
    * Groupe : OnPremisesSamAccountName
    * Groupe : NetbiosName
    * Groupe : DnsDomainName
    * Utilisateur : OnPremisesDistinguishedName

* Le script de la cmdlet ADSyncDomainJoinedComputerSync dispose d’un nouveau paramètre facultatif, nommé AzureEnvironment. Ce paramètre est utilisé pour spécifier la région dans laquelle est hébergé le locataire Azure Active Directory correspondant. Les valeurs valides incluent :
  * AzureCloud (par défaut)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Éditeur de règle de synchronisation mis à jour afin d’utiliser la valeur Joindre (et non plus Approvisionner) en tant que valeur par défaut du type de lien lors de la création de règle de synchronisation.

### <a name="ad-fs-management"></a>Gestion AD FS.

#### <a name="issues-fixed"></a>Problèmes résolus

* Les URL suivantes correspondent à de nouveaux points de terminaison WS-Federation introduits par Azure afin d’améliorer la résilience contre les pannes de courant. Elles seront ajoutées à la configuration de réponse de la partie de confiance AD FS :
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Correction d’un problème qui forçait ADFS à générer une valeur de revendication incorrecte pour IssuerID. Le problème se produit s’il existe plusieurs domaines vérifiés dans le locataire Azure AD et que le suffixe de domaine de l’attribut userPrincipalName utilisé pour générer la revendication IssuerID présente une profondeur de 3 niveaux minimum (par exemple, johndoe@us.contoso.com). Le problème est résolu, grâce à la mise à jour de l’expression régulière utilisée par les règles de revendication.

#### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités
* Auparavant, la fonctionnalité de gestion des certificats ADFS fournie par Azure AD Connect ne pouvait être utilisée qu’avec des batteries ADFS gérées via Azure AD Connect. Désormais, vous pouvez utiliser la fonctionnalité avec les batteries ADFS qui ne sont pas gérées à l’aide d’Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Publication : mai 2017

> [!IMPORTANT]
> Des modifications de schéma et de règle de synchronisation ont été introduites dans cette build. Le service de synchronisation Azure AD Connect déclenchera des étapes d’importation complète et de synchronisation complète après la mise à niveau. Ces modifications sont décrites en détail ci-dessous.
>
>

**Problèmes résolus :**

Synchronisation d’Azure AD Connect

* Résolution d’un problème provoquant une mise à niveau automatique sur le serveur Azure AD Connect, même si le client a désactivé la fonctionnalité à l’aide de l’applet de commande Set-ADSyncAutoUpgrade. Avec ce correctif, le processus de mise à niveau automatique sur le serveur continue de vérifier régulièrement la disponibilité des mises à niveau, mais le programme d’installation téléchargé respecte la configuration de la mise à niveau automatique.
* Durant la mise à niveau sur place de DirSync, Azure AD Connect crée un compte de service Azure AD que le connecteur Azure AD peut utiliser pour la synchronisation avec Azure AD. Une fois le compte créé, Azure AD Connect s’authentifie auprès d’Azure AD en utilisant le compte. Parfois, l’authentification échoue en raison de problèmes temporaires, ce qui entraîne un échec de la mise à niveau sur place de DirSync avec l’erreur *« An error has occurred executing Configure AAD Sync task: AADSTS50034: To sign into this application, the account must be added to the xxx.onmicrosoft.com directory. »* (une erreur s’est produite l’exécution de la tâche Configurer AAD Sync : AADSTS50034 : Pour que vous puissiez vous connecter à cette application, le compte doit être ajouté dans l’annuaire xxx.onmicrosoft.com). Pour améliorer la résilience de la mise à niveau de DirSync, Azure AD Connect retente désormais l’étape d’authentification.
* Un problème dans la build 443 avait pour effet que la mise à niveau sur place de DirSync réussissait, mais que les profils d’exécution requis pour la synchronisation d’annuaires n’étaient pas créés. Une logique de réparation est incluse dans cette build d’Azure AD Connect. Quand le client met à niveau vers cette build, Azure AD Connect détecte les profils d’exécution manquants et les crée.
* Correction d’un problème entraînant l’échec du démarrage du processus de synchronisation de mot de passe avec l’ID d’événement 6900 et l’erreur *« Un élément avec la même clé a déjà été ajouté »*. Ce problème se produit si vous mettez à jour la configuration du filtrage de l’unité d’organisation afin d’inclure la partition de configuration Active Directory. Pour résoudre ce problème, le processus de synchronisation de mot de passe synchronise désormais les changements de mot de passe uniquement à partir de partitions de domaine Active Directory. Les partitions autres que de domaine, telles que les partitions de configuration sont ignorées.
* Lors d’une installation rapide, Azure AD Connect crée un compte AD DS local que le connecteur Active Directory utilise pour communiquer avec l’AD DS local. Auparavant, le compte était créé avec l’indicateur PASSWD_NOTREQD défini sur l’attribut de contrôle de compte d’utilisateur (user-Account-Control), et un mot de passe aléatoire était défini sur le compte. Désormais, Azure AD Connect supprime explicitement l’indicateur PASSWD_NOTREQD une fois le mot de passe défini sur le compte.
* Correction d’un problème qui entraînait l’échec de la mise à niveau de DirSync avec l’erreur *« a deadlock occurred in sql server which trying to acquire an application lock »* (un blocage s’est produit dans sql server qui tente d’acquérir un verrou d’application) lorsque l’attribut mailNickname figure dans le schéma AD local, mais n’est pas limité à la classe d’objets utilisateur AD.
* Correction d’un problème qui avait pour effet de désactiver automatiquement la fonctionnalité d’écriture différée d’appareil quand un administrateur mettait à jour la configuration de la synchronisation Azure AD Connect à l’aide de l’Assistant Azure AD Connect. Ce problème résultait du fait que l’Assistant vérifiait les conditions préalables pour la configuration existante de l’écriture différée d’appareil dans l’AD local et que cette vérification échouait. La solution consiste à ignorer la vérification si l’écriture différée d’appareil est déjà activée.
* Pour configurer le filtrage de l’unité d’organisation, vous pouvez utiliser l’Assistant Azure AD Connect ou Synchronization Service Manager. Auparavant, si vous utilisiez l’Assistant Azure AD Connect pour configurer le filtrage de l’unité d’organisation, les nouvelles unités d’organisation créées par la suite étaient incluses dans la synchronisation d’annuaires. Si vous ne souhaitiez pas que les nouvelles unités d’organisation soient incluses, vous deviez configurer le filtrage de l’unité d’organisation à l’aide de Synchronization Service Manager. Désormais, vous pouvez obtenir le même comportement en utilisant l’Assistant Azure AD Connect.
* Correction du problème qui avait pour effet que des procédures stockées requises par Azure AD Connect étaient créées sous le schéma de l’administrateur effectuant l’installation, plutôt que sous le schéma dbo.
* Résolution du problème qui avait pour effet que l’attribut TrackingId retourné par Azure AD était omis dans les journaux des événements du serveur AAD Connect. Le problème se produisait quand Azure AD Connect recevait un message de redirection d’Azure AD et qu’Azure AD Connect ne parvenait pas à se connecter au point de terminaison fourni. Le TrackingId est utilisé par les ingénieurs du Support pour établir une corrélation avec les journaux côté service lors du dépannage.
* Quand Azure AD Connect recevait une erreur LargeObject d’Azure AD, Azure AD Connect générait un événement avec l’EventID 6941 et le message *« L’objet approvisionné est trop volumineux. Réduisez le nombre de valeurs d’attribut sur cet objet. »* En même temps, Azure AD Connect générait un événement trompeur avec l’EventID 6900 et le message *« Microsoft.Online.Coexistence.ProvisionRetryException : Impossible de communiquer avec le service Windows Azure Active Directory. »* Pour réduire les risques de confusion, Azure AD Connect ne génère plus cet événement lors de la réception de l’erreur LargeObject.
* Résolution d’un problème qui avait pour effet que Synchronization Service Manager cessait de répondre en cas de tentative de mise à jour de la configuration d’un connecteur LDAP générique.

**Nouvelles fonctionnalités/améliorations :**

Synchronisation d’Azure AD Connect
* Changements de règle de synchronisation - les changements de règle de synchronisation suivants ont été implémentés :
  * Mise à jour de la règle de synchronisation par défaut définie pour ne pas exporter les attributs **userCertificate** et **userSMIMECertificate** si ceux-ci comportent plus de 15 valeurs.
  * Les attributs AD **employeeID** et **msExchBypassModerationLink** sont désormais inclus dans l’ensemble de règles de synchronisation par défaut.
  * L’attribut AD **photo** a été supprimé de l’ensemble de règles de synchronisation par défaut.
  * Ajout de **preferredDataLocation** au schéma Metaverse et au schéma du connecteur AAD. Les clients désireux de mettre à jour les attributs dans Azure AD peuvent implémenter des règles de synchronisation personnalisées à cette fin. 
  * Ajout de **userType** au schéma Metaverse et au schéma du connecteur AAD. Les clients désireux de mettre à jour les attributs dans Azure AD peuvent implémenter des règles de synchronisation personnalisées à cette fin.

* Azure AD Connect permet désormais l’utilisation de l’attribut ConsistencyGuid en tant qu’attribut sourceAnchor pour les objets Active Directory locaux. En outre, Azure AD Connect remplit l’attribut ConsistencyGuid avec la valeur de l’attribut objectGuid s’il est vide. Cette fonctionnalité s’applique uniquement au nouveau déploiement. Pour en savoir plus sur cette fonctionnalité, voir la section de l’article [Principes de conception Azure AD Connect - Utilisation de msDS-ConsistencyGuid en tant que sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Une nouvelle applet de commande de dépannage Invoke-ADSyncDiagnostics a été ajoutée pour faciliter le diagnostic des problèmes de synchronisation de hachage de mot de passe. Pour en savoir sur l’utilisation de la cmdlet, reportez-vous à l’article [Résolution des problèmes de synchronisation de mot de passe avec Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-synchronization).
* Azure AD Connect prend désormais en charge la synchronisation d’objets Dossier public à extension messagerie à partir d’un AD local sur Azure AD. Vous pouvez activer la fonctionnalité à l’aide de l’Assistant Azure AD Connect accessible sous Fonctionnalités facultatives. Pour en savoir plus sur cette fonctionnalité, consultez l’article relatif à la [prise en charge du blocage du périmètre basé sur l’annuaire par Office 365 pour les dossiers publics activés par courrier en local](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect nécessite la synchronisation d’un compte AD DS à partir de l’instance AD locale. Auparavant, lorsque vous installiez Azure AD Connect à l’aide du mode Express, vous pouviez fournir les informations d’identification d’un compte d’administrateur d’entreprise, et Azure AD Connect créait le compte AD DS requis. Toutefois, vous deviez fournir le compte AD DS pour une installation personnalisée et pour l’ajout de forêts à un déploiement existant. Désormais, vous pouvez également fournir les informations d’identification d’un compte d’administrateur d’entreprise au cours d’une installation personnalisée et laisser Azure AD Connect créer le compte AD DS requis.
* Azure AD Connect prend désormais en charge SQL AOA. Vous devez activer SQL AOA avant d’installer Azure AD Connect. Pendant l’installation, Azure AD Connect détecte si l’instance SQL spécifiée est ou non activée pour SQL AOA. Si SQL AOA est activé, Azure AD Connect détermine si SQL AOA est configuré pour utiliser une réplication synchrone ou asynchrone. Lorsque vous configurez l’écouteur de groupe de disponibilité, il est recommandé de définir la propriété RegisterAllProvidersIP sur 0. En effet, Azure AD Connect utilise actuellement SQL Native Client pour se connecter à SQL, et SQL Native Client ne prend pas en charge l’utilisation de la propriété MultiSubNetFailover.
* Si vous utilisez une base de données locale pour votre serveur Azure AD Connect et si avez atteint la limite de taille de 10 Go, le service de synchronisation ne démarre plus. Auparavant, vous deviez effectuer l’opération ShrinkDatabase sur la base de données locale pour récupérer un espace de base de données suffisant pour permettre le démarrage du service de synchronisation. Ensuite, vous pouviez utiliser Synchronization Service Manager pour supprimer l’historique d’exécution afin de récupérer davantage d’espace de base de données. Désormais, vous pouvez utiliser l’applet de commande Start-ADSyncPurgeRunHistory pour purger des données de l’historique d’exécution de la base de données locale afin de récupérer de l’espace de base de données. En outre, cette applet de commande prend en charge un mode hors connexion (en spécifiant le paramètre - offline) qui peut être utilisé lorsque le service de synchronisation ne s’exécute pas. Remarque : le mode hors connexion est utilisable uniquement si le service de synchronisation n’est pas en cours d’exécution et si la base de données utilisée est la base de données locale.
* Pour réduire la quantité d’espace de stockage requis, Azure AD Connect compresse désormais les détails des erreurs de synchronisation avant de les stocker dans la base de données locale et les bases de données SQL. Lors de la mise à niveau vers cette version à partir d’une version antérieure d’Azure AD Connect, Azure AD Connect effectue une compression unique sur les détails d’erreur de synchronisation existants.
* Précédemment, après mise à jour de la configuration du filtrage de l’unité d’organisation, vous deviez exécuter manuellement une importation complète pour être certain que les objets existants soient correctement inclus ou exclus dans la synchronisation d’annuaires. Désormais, Azure AD Connect déclenche automatiquement une importation complète lors du cycle de synchronisation suivant. De plus, l’importation complète s’applique uniquement aux connecteurs AD affectés par la mise à jour. Remarque : cette amélioration s’applique uniquement aux mises à jour du filtrage de l’unité d’organisation effectuées à l’aide de l’Assistant Azure AD Connect. Elle ne s’applique pas à une mise à jour du filtrage de l’unité d’organisation effectuée à l’aide de Synchronization Service Manager.
* Auparavant, le filtrage de groupe prenait en charge uniquement les objets Utilisateurs, Groupes et Contacts. Désormais, le filtrage de groupe prend également en charge les objets Ordinateur.
* Auparavant, vous pouviez supprimer les données de l’espace connecteur sans désactiver le planificateur de synchronisation Azure AD Connect. Désormais, Synchronization Service Manager bloque la suppression des données de l’espace connecteur s’il détecte que le planificateur est activé. De plus, un avertissement est renvoyé pour informer les utilisateurs concernant une perte potentielle de données en cas de suppression des données de l’espace connecteur.
* Auparavant, vous deviez désactiver la transcription PowerShell pour que l’Assistant Azure AD Connect s’exécute correctement. Ce problème est partiellement résolu. Vous pouvez activer la transcription PowerShell si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration de la synchronisation. Vous devez désactiver la transcription PowerShell si vous utilisez l’Assistant Azure AD Connect pour gérer la configuration ADFS.



## <a name="114860"></a>1.1.486.0
Publication : avril 2017

**Problèmes résolus :**
* Résolution du problème d’échec de l’installation d’Azure AD Connect sur une version localisée de Windows Server.

## <a name="114840"></a>1.1.484.0
Publication : avril 2017

**Problèmes connus :**

* Cette version d’Azure AD Connect ne s’installe pas correctement si les conditions suivantes sont réunies :
   1. Vous mettez à niveau DirSync sur place ou vous effectuez une nouvelle installation d’Azure AD Connect.
   2. Vous utilisez une version localisée de Windows Server dans laquelle le nom du groupe d’administrateurs intégré sur le serveur n’est pas « Administrateurs ».
   3. Vous utilisez la base de données locale SQL Server 2012 Express par défaut, installée avec Azure AD Connect, au lieu de fournir votre propre base de données SQL complète.

**Problèmes résolus :**

Synchronisation d’Azure AD Connect
* Résolution d’un problème amenant le planificateur de synchronisation à ignorer la totalité de l’étape de synchronisation s’il manque le profil d’exécution d’un ou de plusieurs connecteurs pour cette étape de synchronisation. Par exemple, vous avez ajouté manuellement un connecteur à l’aide de Synchronization Service Manager sans créer de profil d’exécution Importation d’écart associé. Ce correctif garantit que le planificateur de synchronisation continue à exécuter le profil Importation d’écart pour les autres connecteurs.
* Résolution d’un problème amenant le service de synchronisation à arrêter immédiatement le traitement d’un profil d’exécution en cas de problème avec l’une des étapes de l’exécution. Ce correctif garantit que le service de synchronisation ignore cette étape d’exécution et continue à traiter la suite. Par exemple, vous disposez d’un profil d’exécution Importation d’écart pour votre connecteur AD avec plusieurs étapes d’exécution (une pour chaque domaine AD local). Le service de synchronisation exécutera le profil Importation d’écart avec les autres domaines AD même si l’un d’entre eux présente des problèmes de connectivité réseau.
* Résolution d’un problème amenant la mise à jour du connecteur Azure AD à être ignorée lors de la mise à niveau automatique.
* Résolution d’un problème amenant Azure AD Connect à déterminer de manière incorrecte si le serveur est un contrôleur de domaine lors de l’installation, ce qui provoque l’échec de la mise à niveau de DirSync.
* Résolution d’un problème amenant la mise à niveau DirSync sur place à ne pas créer de profil d’exécution pour le connecteur Azure AD.
* Résolution d’un problème amenant l’interface utilisateur Synchronization Service Manager à ne pas répondre lorsque vous essayez de configurer le connecteur LDAP générique.

Gestion AD FS.
* Résolution d’un problème d’échec de l’Assistant Azure AD Connect si le nœud principal AD FS a été déplacé vers un autre serveur.

Desktop SSO
* Résolution d’un problème au sein de l’Assistant Azure AD Connect amenant l’écran de connexion à vous empêcher d’activer la fonctionnalité Desktop SSO si vous avez choisi la synchronisation de mot de passe comme option de connexion lors de la nouvelle installation.

**Nouvelles fonctionnalités/améliorations :**

Synchronisation d’Azure AD Connect
* Azure AD Connect Sync prend désormais en charge l’utilisation du compte de service virtuel, du compte de service géré et du compte de service géré de groupe en tant que comptes de service. Cela s’applique à la nouvelle installation d’Azure AD Connect uniquement. Lorsque vous installez Azure AD Connect :
    * Par défaut, l’Assistant Azure AD Connect crée un compte de service virtuel et l’utilise en tant que compte de service.
    * Si vous effectuez votre installation sur un contrôleur de domaine, Azure AD Connect revient au comportement précédent : il crée un compte d’utilisateur de domaine et l’utilise à la place du compte de service.
    * Vous pouvez substituer le comportement par défaut en fournissant l’un des éléments suivants :
      * Un compte de service géré de groupe
      * Un compte de service géré
      * Un compte d’utilisateur de domaine
      * Un compte d’utilisateur local
* Auparavant, si vous effectuiez une mise à niveau vers une nouvelle version d’Azure AD Connect contenant une mise à jour des connecteurs ou des modifications des règles de synchronisation, Azure AD Connect déclenchait un cycle de synchronisation complet. À présent, Azure AD Connect déclenche l’étape Importation complète uniquement pour les connecteurs avec mise à jour, et l’étape Synchronisation complète uniquement pour les connecteurs avec modifications des règles de synchronisation.
* Auparavant, le seuil de suppression de l’exportation s’appliquait uniquement aux exportations déclenchées par le planificateur de synchronisation. À présent, la fonctionnalité inclut aussi les exportations déclenchées manuellement par le client à l’aide de Synchronization Service Manager.
* Sur votre locataire Azure AD, une configuration de service indique si la fonctionnalité de synchronisation de mot de passe est activée pour votre locataire. Auparavant, les problèmes de configuration du service par Azure AD Connect étaient courants lorsque vous disposiez d’un serveur intermédiaire actif. À présent, Azure AD Connect tente d’assurer la cohérence entre la configuration de service et votre serveur Azure AD Connect actif uniquement.
* L’Assistant Azure AD Connect détecte désormais si AD local n’a pas activé la Corbeille AD et envoie un avertissement.
* Auparavant, l’exportation vers Azure AD expirait et échouait si la taille combinée des objets du lot dépassait le seuil donné. À présent, le service de synchronisation tente de renvoyer les objets dans des lots distincts et plus petits en cas de problème.
* L’application Gestion des clés du service de synchronisation a été supprimée du menu Démarrer de Windows. La gestion de la clé de chiffrement sera toujours prise en charge par l’interface de ligne de commande à l’aide de miiskmu.exe. Pour plus d’informations sur la gestion de la clé de chiffrement, reportez-vous à l’article [Abandon de la clé de chiffrement Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Auparavant, si vous modifiiez le mot de passe du compte de service Azure AD Connect Sync, le service de synchronisation n’était pas en mesure de démarrer correctement. Vous deviez alors abandonner la clé de chiffrement et réinitialiser le mot de passe du compte de service Azure AD Connect Sync. À présent, cela n’est plus nécessaire.

Desktop SSO

* L’Assistant Azure AD Connect ne requiert plus l’ouverture du port 9090 sur le réseau lors de la configuration de l’authentification directe et de Desktop SSO. Seul le port 443 est requis. 

## <a name="114430"></a>1.1.443.0
Publication : mars 2017

**Problèmes résolus :**

Synchronisation d’Azure AD Connect
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect si le nom complet du connecteur Azure AD ne contenait pas le domaine onmicrosoft.com initialement affecté au client Azure AD.
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect lors de la connexion à une base de données SQL lorsque le mot de passe du compte de service de synchronisation contenait des caractères spéciaux tels que l’apostrophe, les deux-points ou l’espace.
* Correction d’un problème qui provoquait l’erreur « la dimage a une ancre qui est différente de celle de l’image » sur un serveur Azure AD Connect en mode intermédiaire après avoir exclu temporairement an objet Active Directory local de la synchronisation et l’avoir inclus à nouveau pour la synchronisation.
* Correction d’un problème qui provoque l’erreur « l’objet se trouve par nom de domaine est un fantôme » sur un serveur Azure AD Connect en mode, intermédiaire une fois que vous avez exclu temporairement sur les sites Active Directory à partir de la synchronisation de l’objet et inclus à nouveau pour la synchronisation.

Gestion AD FS.
* Correction d’un problème qui empêchait l’assistant Azure AD Connect de mettre à jour la configuration AD FS et définir les bonnes revendications sur l’approbation de partie de confiance après configuration de l’ID de connexion secondaire.
* Correction d’un problème qui empêchait l’assistant Azure AD Connect de traiter correctement les serveurs AD FS dont les comptes de service sont configurés à l’aide du format d’userPrincipalName au lieu du format sAMAccountName.

Authentification directe
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect si l’authentification directe était activée mais que l’inscription de son connecteur échouait.
* Résolution du problème qui provoquait le contournement des vérifications de validation par l’assistant Azure AD Connect sur la méthode de connexion sélectionnée lorsque la fonctionnalité d’authentification unique de bureau était activée.

Réinitialisation de mot de passe
* Résolution d’un problème qui peut entraîner l’absence de reconnexion du serveur Azure AAD Connect si la connexion a été arrêtée par un pare-feu ou un proxy.

**Nouvelles fonctionnalités/améliorations :**

Synchronisation d’Azure AD Connect
* L’applet de commande Get-ADSyncScheduler renvoie désormais une nouvelle propriété booléenne nommée SyncCycleInProgress. Si la valeur renvoyée est true, cela signifie qu’il existe un cycle de synchronisation planifié en cours.
* Le dossier de destination pour les journaux d’installation et de configuration d’Azure Connect AD a été déplacé de %localappdata%\AADConnect à %programdata%\AADConnect pour améliorer l’accessibilité aux fichiers journaux.

Gestion AD FS.
* Prise en charge supplémentaire de la mise à jour du certificat SSL de batterie de serveurs AD FS.
* Prise en charge ajoutée pour la gestion de serveurs AD FS 2016.
* Vous pouvez maintenant spécifier un gMSA (compte de service géré de groupe) existant lors de l’installation d’AD FS.
* Vous pouvez maintenant configurer SHA-256 comme algorithme de hachage de signature pour l’approbation de partie de confiance Azure AD.

Réinitialisation de mot de passe
* Introduction d’améliorations pour permettre au produit de fonctionner dans les environnements incluant des règles de pare-feu plus strictes.
* Amélioration de la fiabilité de la connexion à Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Publication : décembre 2016

**Problème résolu :**

* Résolution du problème d’absence de la règle de revendication issuerid pour Active Directory Federation Services (AD FS) dans la version.

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Publication : décembre 2016

**Problème connu :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure Active Directory (Azure AD). Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problème résolu :**

* Si le port 9090 n’est pas ouvert pour les connexions sortantes, l’installation ou la mise à niveau d’Azure AD Connect échoue.

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Publication : décembre 2016

**Problèmes connus :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure AD. Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).
* Le port 9090 doit être ouvert en sortie pour achever l’installation.

**Nouvelles fonctionnalités :**

* Authentification directe (version préliminaire).

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Publication : novembre 2016

**Problème connu :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure AD. Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problèmes résolus :**

* Parfois, l’installation d’Azure AD Connect échoue, car il est impossible de créer un compte de service local dont le mot de passe est conforme au niveau de complexité spécifié par la stratégie de mot de passe de l’organisation.
* Correction d’un problème dans lequel les règles de jointure ne sont pas réévaluées lorsqu’un objet dans l’espace du connecteur devient simultanément hors de portée pour une règle de jointure et dans la portée d’une autre. Cela peut se produire si vous avez deux ou plusieurs règles de jointure dont les conditions de jointure s’excluent mutuellement.
* Correction d’un problème dans lequel les règles de synchronisation entrante (à partir d’Azure AD) qui ne contiennent pas de règles de jointure ne sont pas traitées si elles ont des valeurs de priorité plus faibles que celles contenant des règles de jointure.

**Améliorations :**

* Ajout de la prise en charge de l’installation d’Azure AD Connect sur Windows Server 2016 standard ou version ultérieure.
* Ajout de la prise en charge de l’utilisation de SQL Server 2016 comme base de données distante pour Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Publication : août 2016

**Problèmes résolus :**

* Les modifications apportées à l’intervalle de synchronisation n’ont pas lieu avant la fin du prochain cycle de synchronisation.
* L’Assistant Azure AD Connect n’accepte pas de compte Azure AD dont le nom d’utilisateur commence par un trait de soulignement (\_).
* L’Assistant Azure AD Connect ne parvient pas à authentifier le compte Azure AD fourni si le mot de passe du compte contient un trop grand nombre de caractères spéciaux. Un message d’erreur du type « Impossible de valider les informations d’identification. Une erreur inattendue s’est produite. » est renvoyé.
* La désinstallation du serveur intermédiaire désactive la synchronisation de mot de passe dans le client Azure AD et provoque l’échec de la synchronisation de mot de passe avec le serveur actif.
* La synchronisation du mot de passe échoue dans de rares cas lorsqu’aucun hachage de mot de passe n’est stocké sur l’utilisateur.
* Lorsque le serveur Azure AD Connect est activé pour le mode intermédiaire, l’écriture différée de mot de passe n’est pas temporairement désactivée.
* L’assistant Azure AD Connect n’affiche pas la configuration réelle de synchronisation de mot de passe et d’écriture différée de mot de passe lorsque le serveur est en mode intermédiaire. Il les affiche toujours comme étant désactivées.
* Les modifications apportées à la synchronisation de mot de passe et à l’écriture différée de mot de passe ne sont pas conservées par l’Assistant Azure AD lorsque le serveur est en mode intermédiaire.

**Améliorations :**

* Mise à jour de l’applet de commande Start-ADSyncSyncCycle pour indiquer si elle est en mesure ou non de démarrer correctement un nouveau cycle de synchronisation.
* Ajout de l’applet de commande Stop-ADSyncSyncCycle pour terminer le cycle de synchronisation et l’opération en cours.
* Mise à jour de l’applet de commande Stop-ADSyncScheduler pour terminer le cycle de synchronisation et l’opération en cours.
* Lors de la configuration des [extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md) dans l’Assistant Azure AD Connect, l’attribut Azure AD de type « chaîne Teletex » peut maintenant être sélectionné.

## <a name="111890"></a>1.1.189.0
Publication : juin 2016

**Problèmes résolus et améliorations :**

* Vous pouvez maintenant installer Azure AD Connect sur un serveur compatible FIPS.
  * Pour la synchronisation du mot de passe, consultez [Synchronisation de mot de passe et FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Correction d’un problème à cause duquel un nom NetBIOS ne pouvait pas être résolu pour le nom de domaine complet dans le connecteur Active Directory.

## <a name="111800"></a>1.1.180.0
Publication : mai 2016

**Nouvelles fonctionnalités :**

* Vous avertit et vous permet de vérifier les domaines si vous ne l’avez pas fait avant d’exécuter Azure AD Connect.
* Ajout de la prise en charge de [Microsoft Cloud Allemagne](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Ajout de la prise en charge de la toute dernière infrastructure [cloud Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) avec de nouvelles exigences d’URL.

**Problèmes résolus et améliorations :**

* Ajout d’un filtrage à l’éditeur de règles de synchronisation pour faciliter la recherche de règles de synchronisation.
* Amélioration des performances lors de la suppression d’un espace connecteur.
* Résolution d’un problème lorsqu’un même objet a été supprimé et ajouté dans la même exécution (appelé supprimer/ajouter).
* Une règle de synchronisation désactivée ne réactive plus les attributs et objets inclus lors d’une mise à niveau ou d’une actualisation de schéma d’annuaire.

## <a name="111300"></a>1.1.130.0
Publication : avril 2016

**Nouvelles fonctionnalités :**

* Ajout de la prise en charge des attributs à valeurs multiples dans les [extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).
* Ajout de la prise en charge de variantes de configuration supplémentaires pour que la [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) soit considérée comme éligible à la mise à niveau.
* Ajout d’applets de commande pour le [planificateur personnalisé](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Publication : mars 2016

**Problèmes résolus :**

* Nous nous sommes assurés que l’installation Express ne peut pas être utilisée sur Windows Server 2008 (version antérieure à R2), car la synchronisation du mot de passe n’est pas prise en charge sur ce système d’exploitation.
* La mise à niveau à partir de DirSync avec une configuration de filtre personnalisée n’a pas fonctionné comme prévu.
* Lors de la mise à niveau vers une version plus récente et sans aucune modification de la configuration, une importation/synchronisation complète ne doit pas être planifiée.

## <a name="111100"></a>1.1.110.0
Publication : février 2016

**Problèmes résolus :**

* La mise à niveau à partir de versions antérieures ne fonctionne pas si l’installation ne se trouve pas dans le dossier C:\Program Files par défaut.
* Si vous installez et désélectionnez **Démarrer le processus de synchronisation** à la fin de l’Assistant d’installation, la réexécution de ce dernier n’active pas le planificateur.
* Le planificateur ne fonctionne pas comme prévu sur les serveurs où le format de date et d’heure n’est pas en-US. Il empêche également `Get-ADSyncScheduler` de retourner les heures correctes.
* Si vous avez installé une version antérieure d’Azure AD Connect avec AD FS comme option de connexion et mise à niveau, vous ne pouvez pas réexécuter l’Assistant d’installation.

## <a name="111050"></a>1.1.105.0
Publication : février 2016

**Nouvelles fonctionnalités :**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) pour les clients de la configuration rapide.
* Prise en charge pour l’administrateur général par le biais d’Azure Multi-Factor Authentication et de Privileged Identity Management dans l’Assistant d’installation.
  * Vous devrez configurer votre proxy pour qu’il autorise également le trafic vers https://secure.aadcdn.microsoftonline-p.com si vous utilisez Multi-Factor Authentication.
  * Vous devrez ajouter https://secure.aadcdn.microsoftonline-p.com à votre liste de sites de confiance pour que Multi-Factor Authentication fonctionne correctement.
* Autorisez la modification de la méthode de connexion de l’utilisateur après l’installation initiale.
* Activez l’option [Filtrage par domaine et unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’Assistant d’installation. Cela permet également la connexion à des forêts dans lesquelles tous les domaines ne sont pas disponibles.
* [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) est intégré dans le moteur de synchronisation.

**Fonctionnalités promues de version préliminaire à disponibilité générale :**

* [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md).
* [Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nouvelles fonctionnalités préliminaires :**

* Le nouvel intervalle de cycle de synchronisation par défaut est de 30 minutes. (trois heures pour toutes les versions antérieures). Ajoute la prise en charge pour modifier le comportement du [planificateur](active-directory-aadconnectsync-feature-scheduler.md) .

**Problèmes résolus :**

* La page de vérification des domaines DNS n’a pas reconnu tous les domaines.
* Demande d’informations d’identification de l’administrateur de domaine lors de la configuration AD FS.
* Les comptes AD locaux ne sont pas reconnus par l’Assistant d’installation lorsqu’ils sont situés dans un domaine avec une autre arborescence DNS que celle du domaine racine.

## <a name="1091310"></a>1.0.9131.0
Publication : décembre 2015

**Problèmes résolus :**

* La synchronisation de mot de passe peut ne pas fonctionner lorsque vous modifiez les mots de passe dans Active Directory Domain Services (AD DS), mais elle fonctionne lorsque vous définissez un mot de passe.
* Lorsque vous avez un serveur proxy, l’authentification à Azure AD peut échouer durant le processus d’installation, ou si la mise à niveau est annulée sur la page de configuration.
* La mise à jour à partir d’une version antérieure d’Azure AD Connect avec une instance SQL Server complète échoue si vous n’êtes pas administrateur système SQL Server (SA).
* La mise à jour à partir d’une version antérieure d’Azure AD Connect avec un serveur SQL Server distant affiche l’erreur « Impossible d’accéder à la base de données SQL ADSync ».

## <a name="1091250"></a>1.0.9125.0
Publication : novembre 2015

**Nouvelles fonctionnalités :**

* Reconfiguration d’AD FS pour l’approbation Azure AD.
* Actualisation du schéma Active Directory et nouvelle génération de règles de synchronisation.
* Désactivation d'une règle de synchronisation.
* Définition d’« AuthoritativeNull » comme nouvelle chaîne littérale dans une règle de synchronisation.

**Nouvelles fonctionnalités préliminaires :**

* [Azure AD Connect Health pour la synchronisation](../connect-health/active-directory-aadconnect-health-sync.md).
* Prise en charge de la synchronisation de mot de passe pour les [services de domaine Azure AD](../active-directory-passwords-update-your-own-password.md) .

**Nouveau scénario pris en charge :**

* Prise en charge de plusieurs organisations Exchange locales Pour plus d’informations, consultez la rubrique [Déploiements hybrides à forêts Active Directory multiples](https://technet.microsoft.com/library/jj873754.aspx).

**Problèmes résolus :**

* Problèmes liés à la synchronisation de mot de passe :
  * Le mot de passe d'un objet inclus dans l'étendue de la synchronisation alors qu'il ne l'était pas ne sera pas synchronisé. Cela est valable pour l’unité d’organisation et le filtrage des attributs.
  * La sélection d'une nouvelle unité d'organisation à inclure dans la synchronisation ne requiert pas une synchronisation de mot de passe complète.
  * Lorsqu'un utilisateur désactivé est activé, son mot de passe n'est pas synchronisée.
  * La file d'attente des nouvelles tentatives de mot de passe est infinie et la limite de 5 000 objets supprimés a été supprimée.
* Impossible de se connecter à Active Directory avec le niveau fonctionnel de forêt Windows Server 2016.
* Impossible de modifier le groupe utilisé pour le filtrage de groupes après l’installation initiale.
* Aucun profil utilisateur n’est plus créé sur le serveur Azure AD Connect pour chaque utilisateur effectuant une modification de mot de passe avec l’écriture différée du mot de passe activée.
* Impossible d’utiliser des entiers longs dans des règles de synchronisation.
* La case à cocher « Écriture différée des appareils » reste désactivée s’il existe des contrôleurs de domaine inaccessibles.

## <a name="1086670"></a>1.0.8667.0
Publication : août 2015

**Nouvelles fonctionnalités :**

* L’Assistant Installation d’Azure AD Connect est maintenant localisé dans toutes les langues de Windows Server.
* Prise en charge du déverrouillage de compte lors de l’utilisation de la gestion des mots de passe d’Azure AD.

**Problèmes résolus :**

* L’Assistant Installation d’Azure AD Connect se bloque si un autre utilisateur continue l’installation à la place de la personne qui a commencé l’installation.
* Si une désinstallation précédente d’Azure AD Connect ne parvient pas à désinstaller correctement Azure AD Connect Sync, il n’est pas possible de le réinstaller.
* Impossible d’installer Azure AD Connect en utilisant l’installation rapide si l’utilisateur n’est pas dans le domaine racine de la forêt, ou si une version non anglaise d’Active Directory est utilisée.
* Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur trompeur « Échec de la validation du schéma » est affiché.
* Si le compte utilisé sur le connecteur Active Directory est modifié en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
* L’installation d’Azure AD Connect sur un contrôleur de domaine échoue parfois.
* Impossible d’activer et de désactiver le « Mode de préproduction » si des attributs d’extension ont été ajoutés.
* L’écriture différée de mot de passe échoue dans certaines configurations en raison d’un mot de passe incorrect sur le connecteur Active Directory.
* Impossible de mettre à niveau DirSync si un nom unique (DN) est utilisé lors du filtrage des attributs.
* Utilisation du processeur excessive lors de la réinitialisation du mot de passe.

**Fonctionnalités préliminaires supprimées :**

* La fonctionnalité préliminaire [Écriture différée d’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) a été temporairement supprimée suite aux commentaires des clients de la version préliminaire. Nous la rajouterons une fois que nous aurons traité ces commentaires.

## <a name="1086410"></a>1.0.8641.0
Publication : juin 2015

**Version initiale d’Azure AD Connect.**

Changement de nom d’Azure AD Sync en Azure AD Connect.

**Nouvelles fonctionnalités :**

* Installation à l’aide de la [configuration rapide](active-directory-aadconnect-get-started-express.md)
* [Configuration d’AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* [Mise à niveau à partir de DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Introduction du [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode)

**Nouvelles fonctionnalités préliminaires :**

* [Écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Écriture différée de groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md)
* [Extensions d’annuaire](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Publication : mai 2015

**Nouvelle condition requise**

* Azure AD Sync requiert maintenant que .NET Framework version 4.5.1 soit installé.

**Problèmes résolus :**

* L’écriture différée de mot de passe à partir d’Azure AD échoue en raison d’une erreur de connectivité Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Publication : avril 2015

**Problèmes résolus et améliorations :**

* Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et qu’il existe plusieurs domaines dans la forêt.
* Les performances des opérations d’importation ont été améliorées pour le connecteur Azure Active Directory.
* Lorsqu’un groupe dépassait la limite d’appartenance (par défaut, la limite est définie à 50 000 objets), le groupe était supprimé dans Azure Active Directory. Désormais, le groupe n’est pas supprimé. Une erreur est générée et les nouveaux changements d’appartenance ne sont pas exportés.
* Un nouvel objet ne peut pas être configuré si une suppression intermédiaire avec le même nom de domaine est déjà présente dans l’espace du connecteur.
* Certains objets sont marqués pour synchronisation lors d’une synchronisation différentielle même si aucune modification intermédiaire n’est apportée à l’objet.
* Forcer une synchronisation de mot de passe supprime également la liste des contrôleurs de domaine favoris.
* CSExportAnalyzer a des problèmes avec certains états des objets.

**Nouvelles fonctionnalités :**

* Une jonction peut maintenant connecter au type d’objet « ANY » dans le métaverse.

## <a name="104850222"></a>1.0.485.0222
Publication : février 2015

**Améliorations :**

* Performances de l’importation améliorées.

**Problèmes résolus :**

* La synchronisation de mot de passe prend en compte l’attribut cloudFiltered utilisé par le filtrage des attributs. Les objets filtrés ne sont plus dans la portée pour la synchronisation de mot de passe.
* Dans de rares situations où la topologie avait un grand nombre de contrôleurs de domaine, la synchronisation de mot de passe ne fonctionnait pas.
* « Arrêt serveur » lors de l’importation à partir du connecteur Azure AD après l’activation de la gestion des appareils dans Azure AD/Intune.
* La jonction d’entités de sécurité externes à partir de plusieurs domaines dans la même forêt provoque une erreur de jonction ambiguë.

## <a name="104751202"></a>1.0.475.1202
Publication : décembre 2014

**Nouvelles fonctionnalités :**

* Il est maintenant possible d’effectuer la synchronisation de mot de passe avec le filtrage basé sur les attributs. Pour plus d’informations, consultez [Synchronisation de mot de passe avec filtrage](active-directory-aadconnectsync-configure-filtering.md).
* L’attribut msDS-ExternalDirectoryObjectID est écrit en différé dans Active Directory. Cette fonctionnalité prend en charge les applications Office 365. Elle utilise OAuth2 pour accéder aux boîtes aux lettres en ligne et en local dans un déploiement d’Exchange hybride.

**Problèmes de mise à niveau résolus :**

* Une version plus récente de l’Assistant Connexion est disponible sur le serveur.
* Un chemin d’installation personnalisé était utilisé pour installer Azure AD Sync.
* Un critère de jonction personnalisée non valide bloque la mise à niveau.

**Autres correctifs :**

* Résolution du problème des modèles pour Office Professionnel Plus.
* Résolution des problèmes d’installation provoqué par des noms d’utilisateurs commençant par un tiret.
* Résolution du problème de perte de la valeur de sourceAnchor lors d’une deuxième exécution de l’Assistant Installation.
* Résolution du traçage ETW pour la synchronisation de mot de passe.

## <a name="104701023"></a>1.0.470.1023
Publication : octobre 2014

**Nouvelles fonctionnalités :**

* Synchronisation du mot de passe à partir de plusieurs configurations Active Directory locales vers Azure AD.
* Interface utilisateur du programme d’installation localisée dans toutes les langues de Windows Server.

**Mise à niveau à partir d’Azure Active Directory Sync 1.0 GA**

Si vous avez déjà installé Azure AD Sync, vous devez effectuer une étape supplémentaire dans le cas où vous avez modifié les règles de synchronisation prédéfinies. Une fois que vous avez mis à niveau vers la version 1.0.470.1023, les règles de synchronisation que vous avez modifiées sont dupliquées. Pour chaque règle de synchronisation modifiée, procédez comme suit :

1.  Recherchez la règle de synchronisation que vous avez modifiée et notez les modifications.
* Supprimez la règle de synchronisation.
* Recherchez la nouvelle règle de synchronisation créée par Azure AD Sync et réappliquez les modifications.

**Autorisation pour le compte Active Directory**

Le compte Active Directory doit disposer d’autorisations supplémentaires pour permettre la lecture des hachages de mot de passe à partir d’Active Directory. Les autorisations à accorder sont nommées « Réplication des changements de répertoire » et « Réplication de toutes les modifications de l’annuaire ». Les deux autorisations sont nécessaires pour permettre la lecture des hachages de mot de passe.

## <a name="104190911"></a>1.0.419.0911
Publication : septembre 2014

**Version initiale d’Azure AD Sync.**

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
