---
title: "Installer Azure AD Connect à l’aide d’une base de données ADSync existante | Microsoft Docs"
description: "Cette rubrique décrit comment utiliser une base de données ADSync existante."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: d65e12350e6302b0f95091f48f71cdc7d4610e2c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Installer Azure AD Connect à l’aide d’une base de données ADSync existante
Azure AD Connect nécessite une base de données SQL Server pour stocker les données. Vous pouvez utiliser la Base de données locale (LocalDB) par défaut de SQL Server 2012 Express installée avec Azure AD Connect ou utiliser votre propre version complète de SQL. Auparavant, quand vous installiez Azure AD Connect, une nouvelle base de données nommée ADSync était toujours créée. Avec Azure AD Connect version 1.1.613.0 (ou ultérieure), vous pouvez installer Azure AD Connect en le pointant sur une base de données ADSync existante.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Avantages offerts par l’utilisation d’une base de données ADSync existante
Quand vous pointez sur une base de données ADSync existante :

- Sauf pour les informations d’identification, la configuration de la synchronisation dans la base de données ADSync (notamment les règles de synchronisation personnalisées, les connecteurs, le filtrage et la configuration des fonctionnalités facultatives) est automatiquement récupérée et utilisée pendant l’installation. Les informations d’identification utilisées par Azure AD Connect pour synchroniser les modifications avec AD local et Azure AD sont chiffrées et accessibles uniquement par le serveur Azure AD Connect précédent.
- Toutes les données d’identité (associées au métaverse et aux espaces du connecteur) et les cookies de synchronisation stockés dans la base de données ADSync sont également récupérés. Le serveur Azure AD Connect nouvellement installé peut continuer à synchroniser à partir de là où le serveur Azure AD Connect précédent s’est arrêté, sans avoir à effectuer une synchronisation complète.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scénarios où l’utilisation de la base de données ADSync existante est avantageuse
Ces avantages sont utiles dans les scénarios suivants :


- Vous avez un déploiement existant d’Azure AD Connect. Votre serveur Azure AD Connect existant ne fonctionne plus, mais le serveur SQL contenant la base de données ADSync fonctionne toujours. Vous pouvez installer un nouveau serveur Azure AD Connect et le faire pointer sur la base de données ADSync existante. 
- Vous avez un déploiement existant d’Azure AD Connect. Votre serveur SQL contenant la base de données ADSync ne fonctionne plus. Toutefois, vous disposez d’une sauvegarde récente de la base de données. Vous pouvez tout d’abord restaurer la base de données ADSync sur un nouveau serveur SQL. Après cela, vous pouvez installer un nouveau serveur Azure AD Connect et le faire pointer sur la base de données ADSync restaurée.
- Vous avez un déploiement existant d’Azure AD Connect qui utilise Base de données locale. En raison de la limite de 10 Go imposée par Base de données locale, vous souhaitez migrer vers la version complète de SQL. Vous pouvez sauvegarder la base de données ADSync à partir de Base de données locale et la restaurer sur un serveur SQL. Après cela, vous pouvez réinstaller un nouveau serveur Azure AD Connect et le faire pointer sur la base de données ADSync restaurée.
- Vous essayez de configurer un serveur intermédiaire et souhaitez vous assurer que sa configuration correspond à celle du serveur actif actuel. Vous pouvez sauvegarder la base de données ADSync et la restaurer sur un autre serveur SQL. Après cela, vous pouvez réinstaller un nouveau serveur Azure AD Connect et le faire pointer sur la base de données ADSync restaurée.

## <a name="prerequisite-information"></a>Informations relatives aux prérequis

Remarques importantes avant de continuer :

- Veillez à examiner les prérequis pour l’installation d’Azure AD Connect dans Matériel et prérequis, ainsi que les comptes et les autorisations requises pour l’installation d’Azure AD Connect. Les autorisations requises pour l’installation d’Azure AD Connect à l’aide du mode « Utiliser une base de données existante » sont identiques à celles d’une installation « Personnalisée ».
- Le déploiement d’Azure AD Connect parallèlement à une base de données ADSync existante est uniquement pris en charge avec la version complète de SQL. Il n’est pas pris en charge avec Base de données locale SQL Express. Si vous souhaitez utiliser une base de données ADSync existante de Base de données locale, vous devez tout d’abord sauvegarder la base de données ADSync (Base de données locale) et la restaurer vers la version complète de SQL. Vous pourrez alors déployer Azure AD Connect parallèlement à la base de données restaurée à l’aide de cette méthode.
- La version d’Azure AD Connect utilisée pour l’installation doit remplir les critères suivants :
    - Version 1.1.613.0 ou ultérieure, ET
    - Identique ou ultérieure à la dernière version d’Azure AD Connect utilisée avec la base de données ADSync. Si la version d’Azure AD Connect utilisée pour l’installation est ultérieure à celle utilisée en dernier avec la base de données ADSync, une synchronisation complète peut être requise.  Une synchronisation complète est nécessaire s’il y a des modifications de règle de synchronisation ou de schéma entre les deux versions. 
- La base de données ADSync utilisée doit contenir un état de synchronisation relativement récent. La dernière activité de synchronisation avec la base de données ADSync existante doit avoir eu lieu durant les trois dernières semaines.
- Quand vous installez Azure AD Connect à l’aide du mode « Utiliser une base de données existante », la méthode d’authentification configurée sur le serveur Azure AD Connect précédent n’est pas conservée. De plus, vous ne pouvez pas configurer la méthode de connexion pendant l’installation. Vous pouvez uniquement configurer la méthode de connexion une fois l’installation terminée.
- Vous ne pouvez avoir plusieurs serveurs Azure AD Connect qui partagent la même base de données ADSync. Le mode « Utiliser une base de données existante » vous permet de réutiliser une base de données ADSync existante avec un nouveau serveur Azure AD Connect. Il ne prend pas en charge le partage.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Étapes d’installation d’Azure AD Connect avec le mode « Utiliser une base de données existante »
1.  Téléchargez le programme d’installation d’Azure AD Connect (AzureADConnect.MSI) sur le serveur Windows. Double-cliquez sur le programme d’installation d’Azure AD Connect pour démarrer l’installation d’Azure AD Connect.
2.  Une fois l’installation du fichier MSI terminée, l’Assistant Azure AD Connect démarre le programme d’installation en mode Express. Fermez la fenêtre en cliquant sur l’icône Quitter.
![Bienvenue](media/active-directory-aadconnect-existing-database/db1.png)
3.  Démarrez une nouvelle invite de commandes ou session PowerShell. Accédez au dossier <drive>\program files\Microsoft Azure AD Connect. Exécutez la commande .\AzureADConnect.exe /useexistingdatabase pour démarrer l’Assistant Azure AD Connect en mode d’installation « Utiliser une base de données existante ».
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  L’écran d’accueil d’Azure AD Connect s’affiche. Après avoir accepté les termes du contrat de licence et la déclaration de confidentialité, cliquez sur **Continuer**.
![Bienvenue](media/active-directory-aadconnect-existing-database/db3.png)
5.  Dans l’écran **Installer les composants nécessaires**, l’option **Utiliser un SQL Server existant** est activée. Spécifiez le nom du serveur SQL qui héberge la base de données ADSync. Si l’instance du moteur SQL utilisée pour héberger la base de données ADSync n’est pas l’instance par défaut sur le serveur SQL, vous devez spécifier le nom de l’instance du moteur SQL. De plus, si l’exploration SQL n’est pas activée, vous devez également spécifier le numéro de port de l’instance du moteur SQL. Par exemple :         
![Bienvenue](media/active-directory-aadconnect-existing-database/db4.png)           

6.  Dans l’écran **Se connecter à Azure AD**, vous devez fournir les informations d’identification d’un administrateur général de votre annuaire Azure AD. Nous vous recommandons d’utiliser un compte du domaine onmicrosoft.com par défaut. Ce compte est uniquement utilisé pour créer un compte de service dans Azure AD et n’est plus utilisé une fois l’assistant terminé.
![Connexion](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  Dans l’écran **Connexion de vos annuaires**, une icône de croix rouge est affichée en regard de la forêt AD existante configurée pour la synchronisation d’annuaire. Pour synchroniser les modifications à partir d’une forêt AD locale, un compte de domaine AD DS est nécessaire. L’Assistant Azure AD Connect ne peut pas récupérer les informations d’identification du compte AD DS stockées dans la base de données ADSync, car elles sont chiffrées et peuvent uniquement être déchiffrées par le serveur Azure AD Connect précédent. Cliquez sur **Modifier les informations d’identification** pour spécifier le compte AD DS pour la forêt AD.
![Directories](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  Dans la boîte de dialogue contextuelle, vous pouvez (i) entrer les informations d’identification d’un administrateur d’entreprise et laisser Azure AD Connect créer le compte AD DS pour vous, ou (ii) créer vous-même le compte AD DS et fournir ses informations d’identification à Azure AD Connect. Une fois que vous avez sélectionné une option et fourni les informations d’identification nécessaires, cliquez sur **OK** pour fermer la boîte de dialogue contextuelle.
![Bienvenue](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Une fois les informations d’identification fournies, la croix rouge est remplacée par une coche verte. Cliquez sur **Suivant**.
![Bienvenue](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. Dans l’écran **Prêt à configurer**, cliquez sur **Installer**.
![Bienvenue](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Une fois l’installation terminée, le serveur Azure AD Connect est automatiquement activé pour le Mode de préproduction. Nous vous recommandons de vérifier la présence de modifications inattendues dans la configuration du serveur et les exportations en attente avant de désactiver le Mode de préproduction. 

## <a name="next-steps"></a>Étapes suivantes

- Azure AD Connect étant installé, vous pouvez passer à [Vérification de l’installation et affectation des licences](active-directory-aadconnect-whats-next.md).
- Pour en savoir plus sur ces fonctionnalités, activées lors de l’installation, consultez les pages [Azure AD Connect Sync : Prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) et [Utilisation d’Azure AD Connect Health pour la synchronisation](../connect-health/active-directory-aadconnect-health-sync.md).
- Pour en savoir plus sur ces sujets courants, consultez l’article [Planificateur Azure AD Connect Sync](active-directory-aadconnectsync-feature-scheduler.md).
- En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
