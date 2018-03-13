---
title: "Azure AD Connect : Activation de l’écriture différée des appareils | Microsoft Docs"
description: "Ce document explique comment activer l’écriture différée des appareils à l’aide d’Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: billmath
ms.openlocfilehash: fddbbeda50764ade149e8a8f370bf7341da01736
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect : Activation de l’écriture différée des appareils
> [!NOTE]
> L’écriture différée sur appareil nécessite un abonnement Azure AD Premium.
>
>

La documentation suivante fournit des informations sur l’activation de la fonctionnalité d’écriture différée des appareils dans Azure AD Connect. L’écriture différée des appareils est utilisée dans les scénarios suivants :

* Activer l’accès conditionnel basé sur les appareils pour les applications protégées ADFS (2012 R2 ou version ultérieure) (approbations de la partie de confiance).

Cela fournit une sécurité supplémentaire et l’assurance que l’accès aux applications est accordé uniquement aux appareils de confiance. Pour plus d’informations sur l’accès conditionnel, consultez [Gestion des risques avec accès conditionnel](../active-directory-conditional-access-azure-portal.md) et [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Les appareils doivent se trouver dans la même forêt que les utilisateurs. Étant donné que les appareils doivent être réécrits dans une seule forêt, cette fonctionnalité ne prend pas en charge un déploiement à plusieurs forêts d’utilisateurs pour l’instant.</li>
> <li>Vous ne pouvez ajouter qu’un seul objet de configuration d’enregistrement d’appareil à la forêt Active Directory locale. Cette fonctionnalité n’est pas compatible avec une topologie dans laquelle le domaine Active Directory local est synchronisé à plusieurs annuaires Azure AD.</li>
>

## <a name="part-1-install-azure-ad-connect"></a>1re partie : Installer Azure AD Connect
1. Installez Azure AD Connect à l’aide de paramètres personnalisés ou Express. Microsoft recommande de commencer par synchroniser correctement tous les utilisateurs et groupes avant d’activer l’écriture différée des appareils.

## <a name="part-2-prepare-active-directory"></a>Partie 2 : Préparer Active Directory
Utilisez les étapes suivantes pour préparer l’utilisation de l’écriture différée des appareils.

1. À partir de l’ordinateur sur lequel est installé Azure AD Connect, lancez PowerShell en mode élevé.
2. Si le module Active Directory PowerShell n’est PAS installé, installez les outils d’administration de serveur distant qui contiennent le module AD PowerShell ainsi que dsacls.exe qui est requis pour l’exécution du script. Exécutez la commande suivante :

   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Si le module Azure Active Directory PowerShell n’est PAS installé, téléchargez-le et installez-le à partir du [Module Azure Active Directory pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Ce composant a une dépendance à l’assistant de connexion, qui est installé avec Azure AD Connect.  
4. Avec les informations d’identification d’administrateur d’entreprise, exécutez les commandes suivantes, puis quittez PowerShell.

   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

Les informations d’identification d’administrateur d’entreprise sont requises, car des modifications à l’espace de noms de configuration sont nécessaires. Un administrateur de domaine ne dispose pas des autorisations suffisantes.

![Powershell pour l’activation de l’écriture différée des appareils](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)  

Description :

* Si elles n’existent pas, des conteneurs et des objets sont créés et configurés sous CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn].
* Si elles existent, des conteneurs et des objets sont créés et configurés sous CN=RegisteredDevices,[domain-dn]. Les objets d’appareil seront créés dans ce conteneur.
* Définit les autorisations nécessaires sur le compte Azure AD Connector pour gérer des appareils sur votre Active Directory.
* Ne doit s’exécuter que sur une seule forêt, même si Azure AD Connect est installé sur plusieurs forêts.

Paramètres :

* DomainName : domaine Active Directory où les objets d’appareil seront créés. Remarque : tous les appareils pour une forêt Active Directory donnée seront créés dans un domaine unique.
* AdConnectorAccount : compte Active Directory qui servira à Azure AD Connect pour gérer des objets dans le répertoire. C'est le compte utilisé par la synchronisation Azure AD Connect pour se connecter à AD. Si vous avez procédé à l'installation à l'aide de paramètres Express, c'est le compte précédé de MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>3ème partie : Activer l’écriture différée des appareils dans Azure AD Connect
Utilisez la procédure suivante pour activer l’écriture différée des appareils dans Azure AD Connect.

1. Réexécutez l’Assistant d’installation. Sélectionnez **Personnalisation des options de synchronisation** à partir de la page Tâches supplémentaires et cliquez sur **Suivant**.
   ![Installation personnalisée - Personnaliser les options de synchronisation](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. Sur la page Fonctionnalités facultatives, l’écriture différée des appareils ne sera plus grisée. Notez que si les étapes de préparation d’Azure AD Connect ne sont pas terminées, l’écriture différée des appareils sera grisée sur la page Fonctionnalités facultatives. Cochez la case correspondant à l’écriture différée des appareils, puis cliquez sur **Suivant**. Si la case à cocher est toujours décochée, consultez la [section de résolution des problèmes](#the-writeback-checkbox-is-still-disabled).
   ![Installation personnalisée - Fonctionnalités facultatives de l’écriture différée des appareils](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. Dans la page de l’écriture différée, vous verrez le domaine fourni en tant que forêt d’écriture différée d’appareil par défaut.
   ![Installation personnalisée - Forêt cible de l’écriture différée des appareils](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. Terminez l’installation de l’Assistant sans autre modification de la configuration. Si nécessaire, consultez [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
5. Si vous avez activé le [filtrage](active-directory-aadconnectsync-configure-filtering.md) dans Azure AD Connect, vérifiez que le conteneur nouvellement créé, CN=RegisteredDevices est inclus dans votre étendue.

## <a name="part-4-verify-devices-are-synchronized-to-active-directory"></a>Partie 4 : Vérifier la synchronisation des appareils avec Active Directory
L’écriture différée des appareils doit désormais fonctionner correctement. Sachez que la réécriture des objets d’appareil dans AD peut prendre jusqu’à 3 heures. Pour vérifier que vos appareils sont correctement synchronisés, procédez comme suit après la fin de la synchronisation :

1. Lancez le Centre d’administration Active Directory.
2. Développez RegisteredDevices au sein du domaine configuré en [Partie 2](#part-2-prepare-active-directory).  

   ![Active Directory - Appareils inscrits au Centre d’administration](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)  
   
3. Les appareils enregistrés actuels sont répertoriés à cet emplacement.  

   ![Active Directory - Liste des appareils inscrits au Centre d’administration](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)  

## <a name="enable-conditional-access"></a>Activer l’accès conditionnel
   Des instructions détaillées pour activer ce scénario sont disponibles dans [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](../active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="the-writeback-checkbox-is-still-disabled"></a>La case à cocher de l'écriture différée est toujours désactivée.
Si la case à cocher pour l'écriture différée des appareils n'est pas activée alors que vous avez suivi les étapes ci-dessus, la procédure suivante vous guidera dans ce que l'Assistant d'installation vérifie avant l'activation de la case.

Commençons par le début :

* Assurez-vous qu'au moins une forêt a Windows Server 2012R2. Le type d'objet d'appareil doit être présent.
* Si l'Assistant d'installation est déjà en cours d'exécution, les modifications ne seront pas détectées. Dans ce cas, terminez l'Assistant installation et exécutez-le à nouveau.
* Assurez-vous que le compte que vous fournissez dans le script d'initialisation est bien l'utilisateur utilisé par le connecteur Active Directory. Pour ce faire, procédez comme suit :
  * Dans le menu Démarrer, ouvrez **Service de synchronisation**.
  * Ouvrez l’onglet **Connecteurs** .
  * Trouvez le connecteur de type services de domaine Active Directory et sélectionnez-le.
  * Sous **Actions**, sélectionnez **Propriétés**.
  * Accédez à **Se connecter à la forêt Active Directory**. Vérifiez que le nom de domaine et le nom d’utilisateur spécifiés sur cet écran correspondent au compte fourni pour le script.  
  
    ![Compte de connecteur dans Sync Service Manager](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Vérifiez la configuration dans Active Directory :

* Vérifiez que le Service d’inscription de l’appareil se trouve à l’emplacement ci-dessous (CN = DeviceRegistrationService, CN = Services d’inscription de périphérique, CN = Inscription de l’appareil, CN = Services, CN = Configuration) dans le contexte d’appellation de configuration.

![Résoudre les problèmes, DeviceRegistrationService dans l’espace de noms de configuration](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Vérifiez qu'il n'y a qu'un seul objet de configuration en recherchant l'espace de noms de configuration. S'il en existe plusieurs, supprimez le doublon.

![Résoudre les problèmes, rechercher les objets en double](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Sur l'objet Service d'inscription de l'appareil, assurez-vous que l'attribut msDS-DeviceLocation est présent et a une valeur. Recherchez cet emplacement et assurez-vous qu'il est présent avec l'attribut objectType msDS-DeviceContainer.

![Résoudre les problèmes, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Résoudre les problèmes, classe d’objet RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Vérifiez que le compte utilisé par le connecteur Active Directory dispose des autorisations requise sur le conteneur d’appareils inscrits trouvé à l’étape précédente. Voici les autorisations attendues sur ce conteneur :

![Résoudre les problèmes, vérifier les autorisations du conteneur](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Vérifiez que le compte Active Directory dispose des autorisations sur CN = Inscription de l’appareil, CN = Services, CN = Objet de Configuration.

![Résoudre les problèmes, vérifier les autorisations de la configuration de l’inscription des appareils](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informations supplémentaires
* [Gestion des risques avec accès conditionnel](../active-directory-conditional-access-azure-portal.md)
* [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
