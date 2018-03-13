---
title: "Synchronisation Azure AD Connect : configurer un emplacement de données par défaut pour les fonctionnalités multigéographiques dans Office 365 | Microsoft Docs"
description: "Explique comment placer vos ressources utilisateur Office 365 près de l’utilisateur avec la synchronisation Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronisation Azure AD Connect : configurer un emplacement de données par défaut pour les ressources Office 365
L’objectif de cette rubrique est de vous expliquer comment configurer l’attribut PreferredDataLocation dans Azure AD Connect Sync. Quand un client utilise les fonctionnalités multigéographiques dans Office 365, cet attribut est utilisé pour désigner l’emplacement géographique des données Office 365 de l’utilisateur. Les termes **région** et **zone géographique** sont utilisés de manière interchangeable.

> [!IMPORTANT]
> Les fonctionnalités multigéographiques sont actuellement en préversion. Si vous souhaitez participer au programme d’évaluation, contactez votre représentant Microsoft.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>Activer la synchronisation de l’attribut PreferredDataLocation
Par défaut, les ressources Office 365 des utilisateurs se trouvent dans la même zone géographique que le locataire Azure AD. Par exemple, si votre client est situé en Amérique du Nord, les boîtes aux lettres Exchange des utilisateurs sont également situées en Amérique du Nord. Pour une organisation multinationale, cela n’est peut-être pas optimal. L’attribut PreferredDataLocation permet de définir la zone géographique de l’utilisateur.

En définissant cet attribut, il est possible de mettre les ressources Office 365 de l’utilisateur, par exemple la boîte aux lettres et OneDrive, dans la même zone géographique que l’utilisateur, tout en conservant un seul locataire pour l’organisation.

> [!IMPORTANT]
> Pour être éligible aux fonctionnalités multigéographiques, vous devez disposer d’au moins 5 000 licences dans votre abonnement Office 365.
>
>

Vous trouverez la liste de toutes les zones géographiques pour Office 365 dans la section [Où se trouvent vos données](https://aka.ms/datamaps).

Voici les zones géographiques Office 365 disponibles pour les fonctionnalités multigéographiques :

| Zone géographique | Valeur de PreferredDataLocation |
| --- | --- |
| Asie-Pacifique | APC |
| Australie | AUS |
| Canada | CAN |
| Union européenne | EUR |
| Inde | IND |
| Japon | JPN |
| Corée du Sud | KOR |
| Royaume-Uni | GBR |
| États-Unis | NAM |

* Si une zone géographique, par exemple l’Amérique du Sud, n’apparaît pas dans ce tableau, c’est qu’elle n’est pas utilisable pour les fonctionnalités multigéographiques.
* Les zones géographiques Inde et Corée du Sud ne sont accessibles qu’aux clients dont l’adresse de facturation et la licence achetée correspondent à ces zones géographiques.
* Toutes les charges de travail Office 365 ne prennent pas en charge l’utilisation du paramètre de zone géographique des utilisateurs.

Azure AD Connect prend en charge la synchronisation de l’attribut **PreferredDataLocation** pour les objets **Utilisateur** dans la version 1.1.524.0 et versions ultérieures. Plus spécifiquement, les modifications introduites sont les suivantes :

* Le schéma du type d’objet **Utilisateur** dans le connecteur Azure AD est étendu pour inclure l’attribut PreferredDataLocation qui est de type chaîne à une seule valeur.
* Le schéma du type d’objet **Personne** dans le Metaverse est étendu pour inclure l’attribut PreferredDataLocation, qui est de type chaîne et n’a qu’une seule valeur.

Par défaut, l’attribut PreferredDataLocation n’est pas activé pour la synchronisation. Cette fonctionnalité étant conçue pour les grandes organisations, tout le monde ne peut pas en tirer parti. Vous devez également identifier un attribut destiné à contenir la zone géographique Office 365 pour vos utilisateurs, car il n’existe aucun attribut PreferredDataLocation dans Active Directory en local. Cet attribut varie d’une organisation à l’autre.

> [!IMPORTANT]
> Actuellement, Azure AD permet que l’attribut PreferredDataLocation sur des objets utilisateur personnalisés et des objets utilisateur cloud soit directement configuré à l’aide d’Azure AD PowerShell. Une fois que vous avez activé la synchronisation de l’attribut PreferredDataLocation, vous devez cesser d’utiliser Azure AD PowerShell pour configurer l’attribut sur des **objets utilisateur synchronisés**, car Azure AD Connect les remplace sur la base des valeurs d’attribut source dans l’Active Directory local.

> [!IMPORTANT]
> Depuis le 1er septembre 2017, Azure AD ne permet plus la configuration de l’attribut PreferredDataLocation sur des **objets utilisateur synchronisés** directement à l’aide d’Azure AD PowerShell. Pour configurer l’attribut PreferredLocation sur des objets utilisateur synchronisés, vous devez utiliser Azure AD Connect.

Avant d’activer la synchronisation de l’attribut PreferredDataLocation, vous devez :

* Déterminer l’attribut de l’Active Directory local à utiliser en tant qu’attribut source. Il doit être de type **chaîne à valeur unique**. Dans les étapes ci-dessous, un des extensionAttributes est utilisé.
* Si vous avez déjà configuré l’attribut PreferredDataLocation sur des objets utilisateur synchronisés existants dans Azure AD à l’aide d’Azure AD PowerShell, vous devez **rétroporter** les valeurs d’attribut vers les objets utilisateur correspondants dans l’Active Directory local.

    > [!IMPORTANT]
    > Si vous ne rétroportez pas les valeurs d’attribut pour les objets utilisateur correspondants dans l’Active Directory local, Azure AD Connect supprime les valeurs d’attribut existantes dans Azure AD lorsque la synchronisation de l’attribut PreferredDataLocation est activée.

* Nous vous recommandons de configurer maintenant l’attribut source sur au moins deux objets utilisateur AD locaux qui pourront être utilisés à des fins de vérification ultérieure.

Les étapes d’activation de la synchronisation de l’attribut PreferredDataLocation peuvent se résumer comme suit :

1. Désactiver le planificateur de synchronisation et vérifier qu'aucune synchronisation n’est en cours
2. Ajouter l’attribut source au schéma du connecteur ADDS
3. Ajouter PreferredDataLocation au schéma du connecteur Azure AD
4. Créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
5. Créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
6. Exécuter un cycle de synchronisation complète
7. Activer le planificateur de synchronisation
8. Vérifier le résultat

> [!NOTE]
> Le reste de cette section décrit ces étapes en détail. Ils sont décrits dans le cadre d’un déploiement d’Azure AD avec une topologie de forêt unique et sans règles de synchronisation personnalisées. Si vous avez une topologie à forêts multiples, des règles de synchronisation personnalisées configurées ou un serveur intermédiaire, vous devez ajuster les étapes en conséquence.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Étape 1 : désactiver le planificateur de synchronisation et vérifier qu’aucune synchronisation n’est en cours
Veillez à ce qu’aucune synchronisation ne se produise quand vous êtes en train de mettre à jour des règles de synchronisation afin d’éviter l’exportation de modifications inattendues vers Azure AD. Pour désactiver le planificateur de synchronisation intégré :

1. Lancez une session PowerShell sur le serveur Azure AD Connect.
2. Désactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Lancez **Synchronization Service Manager** en accédant au menu **DÉMARRER** > **Service de synchronisation**.
4. Accédez à l’onglet **Opérations** et confirmez qu’aucune opération n’est à l’état *En cours*.

![Synchronization Service Manager - Vérifier qu’aucune opération n’est en cours](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>Étape 2 : Ajouter l’attribut source au schéma du connecteur ADDS
Certains attributs AD ne sont pas importés dans l’espace du connecteur AD local. Si vous avez choisi d’utiliser un attribut non synchronisé par défaut, vous devez l’importer. Pour ajouter l’attribut source à la liste des attributs importés :

1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
2. Cliquez avec le bouton droit sur le **Connecteur Active Directory local**, puis sélectionnez **Propriétés**.
3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
4. Assurez-vous que l’attribut source que vous avez sélectionné est activé dans la liste d’attributs. Si vous ne voyez pas votre attribut, cochez la case « Afficher tout ».
5. Cliquez sur **OK** pour enregistrer.

![Ajoutez l’attribut source au schéma du connecteur AD local](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Étape 3 : Ajouter l’attribut PreferredDataLocation au schéma du connecteur Azure AD
Par défaut, l’attribut PreferredDataLocation n’est pas importé dans l’espace Azure AD Connect. Pour ajouter l’attribut PreferredDataLocation à la liste des attributs importés :

1. Accédez à l’onglet **Connecteurs** dans Synchronization Service Manager.
2. Cliquez avec le bouton droit sur le **connecteur Azure AD**, puis sélectionnez **Propriétés**.
3. Dans la boîte de dialogue contextuelle, accédez à l’onglet **Sélectionner les attributs**.
4. Sélectionnez l’attribut preferredDataLocation dans la liste d’attributs.
5. Cliquez sur **OK** pour enregistrer.

![Ajouter un attribut source au schéma du connecteur Azure AD](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Étape 4 : créer une règle de synchronisation de trafic entrant pour transmettre la valeur de l’attribut à partir de l’Active Directory local
La règle de synchronisation de trafic entrant permet de transmettre la valeur de l’attribut de l’attribut source de l’Active Directory local au Metaverse :

1. Lancez **Synchronization Rules Editor** dans le menu **DÉMARRER** > **Éditeur de règles de synchronisation**.
2. Définissez le filtre de recherche **Direction** sur **Entrant**.
3. Cliquez sur **Ajouter une nouvelle règle** pour créer une règle de trafic entrant.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | --- | --- | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, *« Entrant depuis AD – Utilisateur PreferredDataLocation”* |
    | DESCRIPTION | *Fournissez une description personnalisée* |  |
    | Système connecté | *Sélectionnez le connecteur AD local* |  |
    | Type d’objet système connecté | **Utilisateur** |  |
    | Type d’objet Metaverse | **Person** |  |
    | Type de lien | **Join** |  |
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. Ne sélectionnez pas de valeur utilisée par une autre règle de synchronisation. |

5. Conservez le **filtre d’étendue** vide pour inclure tous les objets. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.
6. Accédez à l’onglet **Transformation**, puis implémentez la règle de transformation suivante :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    |Directement | PreferredDataLocation | Sélectionnez l’attribut source | Désactivé | Mettre à jour |

7. Cliquez sur **Ajouter** pour créer la règle de trafic entrant.

![Créer une règle de synchronisation de trafic entrant](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Étape 5 : créer une règle de synchronisation de trafic sortant pour transmettre la valeur de l’attribut à Azure AD
La règle de synchronisation de trafic sortant permet de transmettre la valeur de l’attribut du Metaverse à l’attribut PreferredDataLocation dans Azure AD :

1. Accédez à l’**Éditeur de règles de synchronisation**.
2. Définissez le filtre de recherche **Direction** sur **Sortante**.
3. Cliquez sur le bouton **Ajouter une nouvelle règle**.
4. Sous l’onglet **Description**, définissez la configuration suivante :

    | Attribut | Valeur | Détails |
    | ----- | ------ | --- |
    | NOM | *Donnez-lui un nom* | Par exemple, « Sortant vers AAD – Utilisateur PreferredDataLocation » |
    | DESCRIPTION | *Fournissez une description* ||
    | Système connecté | *Sélectionnez le connecteur AAD* ||
    | Type d’objet système connecté | Utilisateur ||
    | Type d’objet métaverse | **Person** ||
    | Type de lien | **Join** ||
    | Precedence | *Choisissez une valeur comprise entre 1 et 99* | Les valeurs de 1 à 99 sont réservées aux règles de synchronisation personnalisées. Ne sélectionnez pas de valeur utilisée par une autre règle de synchronisation. |

5. Accédez à l’onglet **Filtre d’étendue**, puis ajoutez un **groupe de filtre d’étendue unique avec deux clauses** :

    | Attribut | Operator | Valeur |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utilisateur |
    | cloudMastered | NOTEQUAL | True |

    Le filtre d’étendue détermine les objets Active Directory auxquels cette règle de synchronisation de trafic sortant s’applique. Dans cet exemple, nous utilisons le filtre d’étendue de la règle de synchronisation OOB « Sortant vers AAD – Utilisateur Identity ». Il empêche que la règle de synchronisation soit appliquée aux objets utilisateur non synchronisés à partir de l’Active Directory local. Il se peut que vous deviez modifier le filtre d’étendue en fonction de votre déploiement Azure AD Connect.

6. Accédez à l’onglet **Transformation**, puis implémentez la règle de transformation suivante :

    | Type de flux | Attribut cible | Source | Appliquer une seule fois | Type de fusion |
    | --- | --- | --- | --- | --- |
    | Directement | PreferredDataLocation | PreferredDataLocation | Désactivé | Mettre à jour |

7. Fermez **Ajouter** pour créer la règle de trafic sortant.

![Créer une règle de synchronisation de trafic sortant](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Étape 6 : exécuter un cycle de synchronisation complète
En règle générale, un cycle de synchronisation complète est nécessaire parce que nous avons ajouté de nouveaux attributs à l’AD et au schéma du connecteur Azure AD, et introduit des règles de synchronisation personnalisées. Il est recommandé de vérifier les modifications avant de les exporter vers Azure AD. Vous pouvez procéder comme suit pour vérifier les modifications tandis que vous exécutez manuellement les étapes d’un cycle de synchronisation complète.

1. Exécutez l’étape d’**importation complète** sur le **Connecteur Active Directory local** :

   1. Accédez à l’onglet **Opérations** dans Synchronization Service Manager.
   2. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.
   3. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.
   4. Attendez que l’opération soit terminée.

    > [!NOTE]
    > Vous pouvez ignorer l’importation complète sur le connecteur AD local si l’attribut source est déjà inclus dans la liste des attributs importés. En d’autres termes, vous n’avez pas dû apporter de modification au cours de l’[Étape 2  : ajouter l’attribut source au schéma du connecteur AD local](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Exécutez l’étape d’**importation complète** sur le **Connecteur Azure AD** :

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle, sélectionnez **Importation complète**, puis cliquez sur **OK**.
   3. Attendez que l’opération soit terminée.

3. Vérifiez le changement de la règle de synchronisation sur un objet utilisateur existant :

L’attribut source de l’Active Directory local et l’attribut PreferredDataLocation d’Azure AD ont été importés dans leur espace de connecteur respectif. Avant de passer à l’étape de synchronisation complète, il est recommandé d’afficher un **Aperçu** d’un objet utilisateur existant dans l’espace du connecteur AD local. L’attribut source de l’objet que vous avez choisi doit avoir une valeur définie. Un bon **Aperçu** avec l’attribut PreferredDataLocation renseigné dans le Metaverse est un bon indicateur que vous avez correctement configuré les règles de synchronisation. Pour plus d’informations sur la façon de générer un **Aperçu**, voir la section [Vérifier la modification](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Exécutez l’étape **Synchronisation complète** sur le **Connecteur AD local** :

   1. Cliquez avec le bouton droit sur le **Connecteur AD local**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle, sélectionnez **Synchronisation complète**, puis cliquez sur **OK**.
   3. Attendez que l’opération soit terminée.

5. Vérifiez les **Exportations en attente** vers Azure AD :

   1. Cliquez avec le bouton droit sur le connecteur **Azure AD** et sélectionnez **Rechercher dans l’espace connecteur**.
   2. Dans la fenêtre contextuelle Rechercher dans l’espace connecteur :

      1. Définissez l’**Étendue** sur **En attente d’exportation**.
      2. Activez les trois cases à cocher : **Ajouter, Modifier et Supprimer**.
      3. Cliquez sur le bouton **Rechercher** pour obtenir la liste d’objets contenant des modifications à exporter. Pour examiner les modifications apportées à un objet donné, double-cliquez sur celui-ci.
      4. Vérifiez que les modifications sont celles auxquelles vous vous attendiez.

6. Exécutez l’étape **Exporter** du **Connecteur Azure AD**.

   1. Cliquez avec le bouton droit sur le **Connecteur Azure AD**, puis sélectionnez **Exécuter...**.
   2. Dans la boîte de dialogue contextuelle Exécuter le connecteur, sélectionnez **Exporter**, puis cliquez sur **OK**.
   3. Patientez jusqu’à ce l’exportation vers Azure AD soit terminée.

> [!NOTE]
> Vous pouvez remarquer que les étapes n’incluent pas la synchronisation complète sur le connecteur Azure AD et l’exportation sur le connecteur Azure AD. Ces étapes ne sont pas obligatoires car les valeurs d’attribut sont transmises uniquement de l’Active Directory local à Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Étape 7 : réactiver le planificateur de synchronisation
Réactivez le planificateur de synchronisation intégré :

1. Lancez une session PowerShell.
2. Réactivez la synchronisation planifiée en exécutant l’applet de commande : `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Étape 8 : Vérifier le résultat
Il est maintenant temps de vérifier la configuration et de l’activer pour vos utilisateurs.

1. Ajoutez la zone géographique à l’attribut sélectionné sur un utilisateur. Vous trouverez la liste des zones géographiques disponibles dans [ce tableau](#enable-synchronization-of-preferreddatalocation).  
![Attribut AD ajouté à un utilisateur](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Attendez que l’attribut soit synchronisé avec Azure AD.
3. À l’aide d’Exchange Online PowerShell, vérifiez que la région de boîte aux lettres a été correctement définie.  
![Région de boîte aux lettres définie sur un utilisateur dans Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Si votre locataire a été marqué comme étant en mesure d’utiliser cette fonctionnalité, la boîte aux lettres est déplacée dans la bonne zone géographique. Vous pouvez le vérifier en examinant le nom du serveur où se trouve la boîte aux lettres.
4. Pour vérifier l’efficacité de ce paramétrage sur de nombreuses boîtes aux lettres, utilisez le script disponible dans la [galerie Technet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Ce script liste également les préfixes de serveur de tous les centres de données Office 365, ainsi que la zone géographique correspondante. Vous pouvez l’utiliser à titre de référence à l’étape précédente pour vérifier l’emplacement de la boîte aux lettres.

## <a name="next-steps"></a>Étapes suivantes

**Découvrez en plus sur les fonctionnalités multigéographiques dans Office 365 :**

* Sessions multigéographiques dans Ignite : https://aka.ms/MultiGeoIgnite
* Fonctionnalités multigéographiques dans OneDrive : https://aka.ms/OneDriveMultiGeo
* Fonctionnalités multigéographiques dans SharePoint Online : https://aka.ms/SharePointMultiGeo

**Découvrez en plus sur le modèle de configuration dans le moteur de synchronisation :**

* En savoir plus sur le modèle de configuration dans [Comprendre l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* En savoir plus sur le langage d’expression dans [Comprendre les expressions d’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
