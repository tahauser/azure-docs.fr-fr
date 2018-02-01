---
title: "Configurer un accès conditionnel local dans Azure Active Directory | Microsoft Docs"
description: "Guide pas à pas visant à activer l’accès conditionnel pour des applications locales à l’aide des services de fédération Active Directory (AD FS) dans Windows Server 2012 R2."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 466cd564f08b07d443db7cb1de59c5778682ed73
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>Configuration d’un accès conditionnel en local à l’aide du service Azure Active Directory Device Registration
Quand vous demandez aux utilisateurs de joindre leurs appareils à l’espace de travail du service Azure Active Directory Device Registration, leurs appareils peuvent être marqués comme étant connus de votre organisation. Vous trouverez ci-dessous un guide pas à pas visant à activer l’accès conditionnel pour des applications locales à l’aide des services de fédération Active Directory (AD FS) dans Windows Server 2012 R2.

> [!NOTE]
> Une licence Office 365 ou Azure AD Premium est nécessaire pour utiliser des appareils enregistrés dans les stratégies d’accès conditionnel du service Azure Active Directory Device Registration. Ces stratégies se composent notamment de celles qui sont appliquées par AD FS dans les ressources locales.
> 
> Pour plus d’informations sur les scénarios d’accès conditionnel pour les ressources locales, consultez [Rejoindre un espace de travail à partir d’un appareil pour bénéficier d’une authentification unique et d’une authentification transparente à deux facteurs dans les applications de l’entreprise](https://technet.microsoft.com/library/dn280945.aspx).

Ces fonctionnalités sont accessibles aux clients qui achètent une licence Azure Active Directory Premium.

## <a name="supported-devices"></a>Appareils pris en charge
* Appareils Windows 7 joints à un domaine
* Appareils Windows 8.1 personnels et joints à un domaine
* iOS 6 et versions ultérieures pour le navigateur Safari
* Android 4.0 ou versions ultérieures, téléphones Samsung GS3 ou versions ultérieures, Samsung Galaxy Note 2 ou tablettes plus récentes

## <a name="scenario-prerequisites"></a>Configuration requise pour le scénario
* Abonnement à Office 365 ou Azure Active Directory Premium
* Un client Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou versions ultérieures)
* Schéma mis à jour dans Windows Server 2012 R2
* Une licence Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, configuré pour l’authentification unique à Azure AD
* Proxy d’application web Windows Server 2012 R2 
* Microsoft Azure Active Directory Connect (Azure AD Connect) [(Télécharger Azure AD Connect)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Domaine vérifié

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version
* Les stratégies d’accès conditionnel basées sur les appareils nécessitent la réécriture d’objets d’appareil dans Active Directory depuis Azure Active Directory. La réécriture d’objets d’appareil dans Active Directory peut prendre jusqu’à trois heures.
* Les appareils iOS 7 demandent systématiquement à l’utilisateur de sélectionner un certificat pendant l’authentification des certificats clients.
* Certaines versions d’iOS8 antérieures à iOS 8.3 ne fonctionnent pas.

## <a name="scenario-assumptions"></a>Hypothèses de scénario
Ce scénario suppose que vous disposez d’un environnement hybride comprenant un locataire Azure AD et une instance Active Directory locale. Ces locataires doivent être connectés avec Azure AD Connect, un domaine vérifié et AD FS pour l’authentification unique. Aidez-vous de la liste de vérification suivante pour configurer votre environnement selon les spécifications.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Liste de vérification : prérequis pour un scénario d’accès conditionnel
Connectez votre locataire Azure AD avec votre instance Active Directory locale.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurer le service Azure Active Directory Device Registration
Aidez-vous de ce guide pour déployer et configurer le service Azure Active Directory Device Registration pour votre organisation.

Ce guide part du principe que vous avez configuré Windows Server Active Directory et souscrit un abonnement à Microsoft Azure Active Directory. Reportez-vous aux prérequis décrits précédemment.

Pour déployer le service Azure Active Directory Device Registration avec votre locataire Azure Active Directory, effectuez les tâches de la liste de vérification suivante dans l’ordre. Quand un lien de référence vous mène à une rubrique conceptuelle, revenez à cette liste de vérification de façon à effectuer les tâches restantes. Certaines tâches comportent une étape de validation de scénario pour vous aider à vérifier si l’étape a bien été effectuée.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>1ère partie : Activer le service Azure Active Directory Device Registration
Suivez les étapes de la liste de vérification pour activer et configurer le service Azure Active Directory Device Registration.

| Tâche | Informations de référence | 
| --- | --- |
| Activez le service Device Registration dans votre locataire Azure Active Directory pour autoriser les appareils à rejoindre l’espace de travail. Par défaut, Azure Multi-Factor Authentication n’est pas activé pour le service. Cependant, nous vous recommandons d’utiliser l’authentification multifacteur pendant l’inscription d’un appareil. Avant d’activer l’authentification multifacteur dans le service d’inscripton Active Directory, vérifiez que les services AD FS sont configurés pour un fournisseur d’authentification multifacteur. |[Activer le service Azure Active Directory Device Registration](active-directory-device-registration-get-started.md)| 
|Les appareils détectent votre service Azure Active Directory Device Registration en recherchant des enregistrements DNS connus. Configurez le DNS de votre entreprise de sorte que les appareils puissent détecter le service Azure Active Directory Device Registration. |[Configurer la découverte du service Azure Active Directory Device Registration](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2ème partie : Déployer et configurer Windows Server 2012 R2 Active Directory Federation Services et configurer une relation de fédération avec Azure AD

| Tâche | Informations de référence |
| --- | --- |
| Déployez Active Directory Domain Services avec les extensions de schéma Windows Server 2012 R2. Vous n'avez pas besoin de mettre à niveau vos contrôleurs de domaine vers Windows Server 2012 R2. Seule la mise à niveau des schémas est nécessaire. |[Mettre à niveau le schéma Active Directory Domain Services](#upgrade-your-active-directory-domain-services-schema) |
| Les appareils détectent votre service Azure Active Directory Device Registration en recherchant des enregistrements DNS connus. Configurez le DNS de votre entreprise de sorte que les appareils puissent détecter le service Azure Active Directory Device Registration. |[Préparation de votre Active Directory à la prise en charge d'appareils](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3ème partie : Activer l'écriture différée des appareils dans Azure AD
| Tâche | Informations de référence |
| --- | --- |
| Terminez la 2ème partie de l’activation de l’écriture différée des appareils dans Azure AD Connect. Une fois cette tâche terminée, revenez à ce guide. |[Activation de l’écriture différée des appareils dans Azure AD Connect](#upgrade-your-active-directory-domain-services-schema) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Facultatif] 4ème partie : Activer l’authentification multifacteur
Nous vous recommandons vivement de configurer l’une des différentes options d’authentification multifacteur. Si vous voulez imposer l’authentification multifacteur, consultez [Choisissez la solution de sécurité Azure Multi-Factor Authentication qui vous convient](../multi-factor-authentication/multi-factor-authentication-get-started.md). Cette rubrique inclut une description de chaque solution et des liens pour vous aider à configurer la solution de votre choix.

## <a name="part-5-verification"></a>5ème partie : Vérification
Le déploiement est maintenant terminé ; vous pouvez tester certains scénarios. Utilisez les liens ci-dessous pour tester le service et vous familiariser avec ses fonctionnalités.

| Tâche | Informations de référence |
| --- | --- |
| Joignez des appareils à votre espace de travail à l’aide du service Azure Active Directory Device Registration. Vous pouvez joindre des appareils iOS, Windows et Android. |[Joindre des appareils à votre espace de travail à l’aide du service Azure Active Directory Device Registration](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Affichez les appareils inscrits dans le portail d’administration, puis procédez à leur activation ou désactivation. Dans cette tâche, vous affichez des appareils inscrits via le portail d’administration. |[Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-device-registration-get-started.md) |
| Vérifiez que les objets d'appareil sont réécrits depuis Azure Active Directory vers Windows Server Active Directory. |[Vérifier que les appareils inscrits sont réécrits dans Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Maintenant que les utilisateurs peuvent inscrire leurs appareils, vous pouvez créer des stratégies d'accès à l'application dans AD FS qui autorisent uniquement les appareils inscrits. Cette tâche consiste à créer une règle d’accès à l’application et un message personnalisé d’accès refusé. |[Créer une stratégie d’accès à une application et un message personnalisé de refus d’accès](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Intégration d’Azure Active Directory au répertoire Active Directory local

**Consultez les pages suivantes :**

- [Intégrer des répertoires locaux à Azure Active Directory](./connect/active-directory-aadconnect.md) (informations conceptuelles).

- [Installation personnalisée d’Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (instructions d’installation).


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Mettre à niveau le schéma des services de domaine Active Directory
> [!NOTE]
> Une fois le schéma Active Directory mis à niveau, le processus ne peut plus être inversé. Nous vous recommandons dans un premier temps d’effectuer la mise à niveau dans un environnement de test.
> 

1. Connectez-vous à votre contrôleur de domaine avec un compte qui détient des droits d’administrateur d’entreprise et d’administrateur de schéma.
2. Copiez le répertoire et les sous-répertoires **[média]\support\adprep** sur l’un de vos contrôleurs de domaine Active Directory (**[média]** correspondant au chemin du média d’installation Windows Server 2012 R2).
4. À partir d’une invite de commandes, accédez au répertoire **adprep**, puis exécutez **adprep.exe /forestprep**. Suivez les instructions à l’écran pour terminer la mise à niveau du schéma.

## <a name="prepare-your-active-directory-to-support-devices"></a>Préparation de votre Active Directory à la prise en charge d'appareils
> [!NOTE]
> Il s’agit d’une opération ponctuelle que vous devez effectuer pour préparer votre forêt Active Directory à la prise en charge d’appareils. Pour effectuer cette procédure, vous devez être connecté avec des autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma Windows Server 2012 R2.
> 


### <a name="prepare-your-active-directory-forest"></a>Préparer votre forêt Active Directory
1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell, puis tapez **Initialize-ADDeviceRegistration**. 
2. Quand vous êtes invité à renseigner le **ServiceAccountName**, entrez le nom du compte de service que vous avez sélectionné pour AD FS. S’il s’agit d’un compte gMSA, entrez le nom au format **domaine\nomcompte$**. Pour un compte de domaine, utilisez le format **domaine\nomcompte**.

### <a name="enable-device-authentication-in-ad-fs"></a>Activer l’authentification d’appareils dans AD FS
1. Sur votre serveur de fédération, ouvrez la console de gestion AD FS et accédez à **AD FS** > **Stratégies d’authentification**.
2. Dans le volet **Actions**, sélectionnez **Modifier l’authentification principale globale**.
3. Cochez **Activer l’authentification des appareils**, puis sélectionnez **OK**.
4. Par défaut, AD FS supprime régulièrement les appareils non utilisés d’Active Directory. Désactivez cette tâche quand vous utilisez le service Azure Active Directory Device Registration pour permettre la gestion des appareils dans Azure.

### <a name="disable-unused-device-cleanup"></a>Désactiver le nettoyage des appareils non utilisés
Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell, puis tapez **Set-AdfsDeviceRegistration -MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Préparation d'Azure AD Connect pour l'écriture différée des appareils
Terminez la partie 1 : Préparer Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Joindre des appareils à votre espace de travail à l’aide du service Azure Active Directory Device Registration

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Joindre un appareil iOS à l’aide du service Azure Active Directory Device Registration
Pour les appareils iOS, le service Azure Active Directory Device Registration utilise le processus d’inscription de profil « Over-the-Air ». Ce processus démarre dès que l’utilisateur se connecte à l’URL d’inscription de profil avec Safari. Le format de l’URL est le suivant :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Dans ce cas, `yourdomainname` correspond au nom de domaine que vous avez configuré avec Azure Active Directory. Par exemple, si votre nom de domaine est contoso.com, l’URL se présente comme suit :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Il existe de nombreux moyens de communiquer cette URL à vos utilisateurs. Par exemple, l’une des méthodes que nous recommandons consiste à publier cette URL dans un message personnalisé de refus d’accès à l’application dans AD FS. Cette question est abordée dans la prochaine section : [Créer une stratégie d’accès à une application et un message personnalisé de refus d’accès](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Joindre un appareil Windows 8.1 à l’aide du service Azure Active Directory Device Registration
1. Sur votre appareil Windows 8.1, sélectionnez **Paramètres du PC** > **Réseau** > **Lieu de travail**.
2. Entrez votre nom d’utilisateur au format UPN (par exemple, **dan@contoso.com**).
3. Sélectionnez **Joindre**.
4. Quand vous y êtes invité, connectez-vous avec vos informations d’identification. L’appareil est à présent joint.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Joindre un appareil Windows 7 à l’aide du service Azure Active Directory Device Registration
Pour inscrire des appareils joints à un domaine Windows 7, vous devez déployer le [package logiciel d’inscription d’appareils](https://www.microsoft.com/download/details.aspx?id=53554).

Vous trouverez des instructions sur l’utilisation du package sur la page [Packages Windows Installer pour les ordinateurs autres que Windows 10](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Vérifier que les appareils inscrits sont réécrits dans Active Directory
Vous pouvez utiliser LDP.exe et Modification ADSI pour afficher les objets d’appareil et ainsi vérifier qu’ils ont bien été réécrits dans votre service Active Directory. Ces deux outils sont disponibles dans les outils d’administrateur Active Directory.

Par défaut, les objets d’appareil réécrits à partir d’Azure Active Directory sont placés dans le même domaine que votre batterie de serveurs AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Créer une stratégie d’accès à une application et un message personnalisé de refus d’accès
Considérez le scénario suivant : vous créez une approbation de partie de confiance d’application dans AD FS et configurez une règle d’autorisation d’émission autorisant uniquement les appareils enregistrés. À présent, seuls les appareils inscrits sont autorisés à accéder à l’application. 

Pour permettre à vos utilisateurs d’accéder facilement à l’application, vous configurez un message personnalisé de refus d’accès qui inclut des instructions leur indiquant comment joindre leur appareil. Vos utilisateurs peuvent dès lors enregistrer de manière transparente leurs appareils et ainsi accéder à une application.

Les étapes suivantes vous expliquent comment implémenter ce scénario.

> [!NOTE]
> Cette section part du principe que vous avez déjà configuré une approbation de partie de confiance pour votre application dans AD FS.
> 

1. Ouvrez l’outil MMC AD FS et sélectionnez **AD FS** > **Relations d’approbation** > **Approbations de partie de confiance**.
2. Recherchez l’application à laquelle s’applique la nouvelle règle d’accès. Cliquez avec le bouton droit sur l’application, puis sélectionnez **Modifier les règles de revendication**.
3. Sélectionnez l’onglet **Règles d’autorisation d’émission**, puis sélectionnez **Ajouter une règle**.
4. Dans la liste déroulante **Modèle de règle de revendication**, sélectionnez **Autoriser ou refuser l’accès des utilisateurs en fonction d’une revendication entrante**. Sélectionnez ensuite **Suivant**.
5. Dans le champ **Nom de la règle de revendication**, tapez **Autoriser l’accès aux appareils inscrits**.
6. Dans la liste déroulante **Type de revendication entrante**, sélectionnez **Est un utilisateur inscrit**.
7. Dans le champ **Valeur de revendication entrante**, tapez **true**.
8. Sélectionnez la case d'option **Autoriser l'accès aux utilisateurs avec cette revendication entrante** .
9. Sélectionnez **Terminer**, puis **Appliquer**.
10. Supprimez les règles moins strictes que celle que vous avez créées. Par exemple, supprimez la règle par défaut **Autoriser l’accès à tous les utilisateurs**.

Votre application est à présent configurée pour autoriser l’accès uniquement quand une personne utilise un appareil qu’elle a inscrit et qu’elle a joint à l’espace de travail. Pour des stratégies d’accès plus avancées, consultez [Gérer les risques avec une authentification multifacteur supplémentaire pour les applications sensibles](https://technet.microsoft.com/library/dn280949.aspx).

Configurez ensuite un message d’erreur personnalisé pour votre application. Ce message informe les utilisateurs qu’ils doivent joindre leur appareil à l’espace de travail avant de pouvoir accéder à l’application. Vous pouvez créer un message personnalisé de refus d’accès à l’application en utilisant du code HTML personnalisé et PowerShell.

Sur votre serveur de fédération, ouvrez une fenêtre de commande PowerShell, puis tapez la commande suivante. Remplacez certaines parties de la commande par des éléments propres à votre système.

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Vous devez inscrire votre appareil avant d'accéder à cette application.

**Si vous utilisez un appareil iOS, cliquez sur ce lien pour joindre votre appareil**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Joindre cet appareil iOS à votre espace de travail.

Si vous utilisez un appareil Windows 8.1, vous pouvez joindre votre appareil en sélectionnant **Paramètres du PC**> **Réseau** > **Espace de travail**.

Dans les commandes précédentes, **relying party trust name** correspond au nom d’objet Approbation de partie de confiance de votre application dans AD FS.
Et **yourdomain.com** correspond au nom de domaine que vous avez configuré avec Azure Active Directory (par exemple, contoso.com).
Veillez à supprimer les sauts de ligne éventuels dans le contenu HTML que vous transmettez à l’applet de commande **Set-AdfsRelyingPartyWebContent**.

Désormais, quand les utilisateurs accèderont à votre application à partir d’un appareil qui n’est pas enregistré auprès d’Azure Active Directory Device Registration Service, ils verront s’afficher une erreur.

