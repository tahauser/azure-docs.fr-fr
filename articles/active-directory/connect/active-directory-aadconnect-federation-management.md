---
title: "Azure AD Connect - Gestion et personnalisation d’AD FS | Microsoft Docs"
description: "Gestion d’AD FS avec Azure AD Connect et personnalisation de la connexion de l’utilisateur à AD FS avec Azure AD Connect et PowerShell."
keywords: "AD FS, ADFS, gestion AD FS, AAD Connect, Connect, connexion, personnalisation d’AD FS, réparer l’approbation, O365, fédération, partie de confiance"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 49acea5c08a10ba3b60d0db5f05e30d573f5e507
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Gérer et personnaliser Active Directory Federation Services à l’aide d’Azure AD Connect
Cet article décrit comment gérer et personnaliser Active Directory Federation Services (ADFS) à l’aide d’Azure Active Directory (Azure AD) Connect. Il indique également d’autres tâches courantes liées à AD FS que vous devrez peut-être effectuer pour terminer la configuration d’une batterie de serveurs AD FS.

| Rubrique | Sujet traité |
|:--- |:--- |
| **Gérer AD FS** | |
| [Réparation de l’approbation](#repairthetrust) |Réparation de l’approbation de fédération avec Office 365. |
| [Fédération avec Azure AD à l’aide d’un ID de connexion de substitution](#alternateid) | Configurer la fédération à l’aide d’un ID de connexion de substitution  |
| [Ajout d’un serveur AD FS](#addadfsserver) |Extension d’une batterie de serveurs AD FS à l’aide d’un serveur AD FS supplémentaire. |
| [Ajouter un serveur de proxy d’application web AD FS](#addwapserver) |Extension d’une batterie de serveurs AD FS à l’aide d’un serveur de proxy d’application web. |
| [Ajout d’un domaine fédéré](#addfeddomain) |Ajout d’un domaine fédéré. |
| [Mettre à jour le certificat SSL](active-directory-aadconnectfed-ssl-update.md)| Mise à jour du certificat SSL pour une batterie de serveurs AD FS. |
| **Personnaliser AD FS** | |
| [Ajout d’une illustration ou d’un logo de société personnalisé](#customlogo) |Personnalisation de la page de connexion AD FS à l’aide d’une illustration ou d’un logo de société. |
| [Ajout d’une description de connexion](#addsignindescription) |Ajout d’une description de la page de connexion. |
| [Modification des règles de revendication AD FS](#modclaims) |Modification des revendications AD FS pour différents scénarios de fédération. |

## <a name="manage-ad-fs"></a>Gérer AD FS
Vous pouvez effectuer différentes tâches associées à AD FS dans Azure AD Connect avec une intervention minime de l’utilisateur à l’aide de l’Assistant Azure AD Connect. Une fois que vous avez terminé l’installation d’Azure AD Connect à l’aide de l’Assistant, vous pouvez à nouveau exécuter l’Assistant pour effectuer des tâches supplémentaires.

## <a name="repairthetrust"></a>Réparation de l’approbation 
Vous pouvez utiliser Azure AD Connect pour vérifier l’état actuel des services AD FS et Azure AD et prendre des mesures appropriées pour réparer l’approbation. Pour réparer l’approbation des services Azure AD et AD FS, procédez comme suit :

1. Sélectionnez **Réparer la confiance AAD et ADFS** dans la liste des tâches disponibles.
   ![Réparer la confiance AAD et ADFS](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. Dans la page **Connexion à Azure AD**, saisissez vos informations d’identification d’administrateur global d’Azure AD, puis cliquez sur **Suivant**.
   ![Connexion à Azure AD](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. Dans la page **Informations d’identification d’accès à distance** , indiquez les informations d’identification de l’administrateur de domaine.

   ![Informations d’identification d’accès à distance](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    Lorsque vous cliquez sur **Suivant**, Azure AD Connect vérifie l’intégrité du certificat et affiche les éventuels problèmes.

    ![État des certificats](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    La page **Prêt à configurer** affiche la liste des actions qui seront effectuées afin de réparer l’approbation.

    ![Prêt à configurer](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. Cliquez sur **Installer** pour réparer l’approbation.

> [!NOTE]
> Azure AD Connect peut seulement réparer ou modifier les certificats auto-signés. Azure AD Connect ne permet pas de réparer les certificats tiers.

## <a name="alternateid"></a>Fédération avec Azure AD via AlternateID 
Il est recommandé que le nom d’utilisateur principal (UPN) local et le nom d’utilisateur principal cloud soient identiques. Si l’UPN local utilise un domaine non routable (par ex. Contoso.local) ou ne peut pas être modifié en raison des dépendances d’application locales, nous vous recommandons de configurer un ID de connexion de substitution. Un ID de connexion de substitution permet de configurer une expérience de connexion où les utilisateurs peuvent se connecter avec un attribut autre que leur UPN, comme leur adresse e-mail. Le choix de nom d’utilisateur principal dans Azure AD Connect est par défaut l’attribut userPrincipalName dans Active Directory. Si vous choisissez n’importe quel autre attribut pour le nom d’utilisateur principal et fédérez avec AD FS, Azure AD Connect configurera AD FS pour l’ID de connexion de substitution. Vous trouverez ci-dessous un exemple de choix d’un autre attribut pour le nom d’utilisateur principal :

![Sélection d’un attribut d’ID de substitution](media/active-directory-aadconnect-federation-management/attributeselection.png)

La configuration d’un ID de connexion de substitution pour AD FS comprend deux étapes principales :
1. **Configuration du jeu de revendications d’émission** : Les règles de revendication d’émission dans la partie de confiance Azure AD sont modifiées pour utiliser l’attribut UserPrincipalName sélectionné en tant qu’ID de substitution de l’utilisateur.
2. **Activation d’un ID de connexion de substitution dans la configuration d’AD FS** : La configuration AD FS est mise à jour afin que AD FS permette de rechercher des utilisateurs dans les forêts appropriées à l’aide de l’ID de substitution. Cette configuration est prise en charge pour AD FS sur Windows Server 2012 R2 (avec KB2919355) ou version ultérieure. Si les serveurs AD FS sont sous 2012 R2, Azure AD Connect vérifie la présence de la base de connaissances requise. Si la base de connaissances n’est pas détectée, un avertissement s’affiche après la configuration, comme indiqué ci-dessous :

    ![Avertissement d’absence de base de connaissances sur 2012 R2](media/active-directory-aadconnect-federation-management/kbwarning.png)

    Pour corriger la configuration en cas de bases de connaissances manquantes, installez la mise à jour [KB2919355](http://go.microsoft.com/fwlink/?LinkID=396590) requise, puis réparez l’approbation à l’aide de [Réparer l’approbation AAD et AD FS](#repairthetrust).

> [!NOTE]
> Pour plus d’informations sur ID de substitution et les étapes de configuration manuelle, lisez [Configuration d’un ID de connexion de substitution](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Ajout d’un serveur AD FS 

> [!NOTE]
> Pour ajouter un serveur AD FS, Azure AD Connect requiert le certificat PFX. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

1. Sélectionnez **Déploiement d’un serveur de fédération supplémentaire**, puis cliquez sur **Suivant**.

   ![Serveur de fédération supplémentaire](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. Dans la page **Connexion à Azure AD**, saisissez vos informations d’identification d’administrateur global d’Azure AD, puis cliquez sur **Suivant**.

   ![Se connecter à Azure AD](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. Indiquez les informations d’identification de l’administrateur de domaine.

   ![Informations d’identification de l’administrateur de domaine](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect vous demande le mot de passe du fichier PFX que vous avez fourni lors de la configuration de votre nouvelle batterie de serveurs AD FS avec Azure AD Connect. Cliquez sur **Saisie du mot de passe** pour fournir le mot de passe du fichier PFX.

   ![Mot de passe du certificat](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![Spécifiez le certificat SSL](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. Dans la page **Serveurs AD FS** , entrez le nom ou l’adresse IP du serveur à ajouter à la batterie de serveurs AD FS.

   ![Serveurs AD FS](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. Cliquez sur **Suivant** et parcourez la page **Configurer** finale. Une fois qu’Azure AD Connect a fini d’ajouter les serveurs à la batterie de serveurs AD FS, vous avez la possibilité de vérifier la connectivité.

   ![Prêt à configurer](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![Installation terminée](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Ajout d’un serveur WAP AD FS 

> [!NOTE]
> Pour ajouter un serveur de proxy d’application web AD FS, Azure AD Connect requiert le certificat PFX. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

1. Sélectionnez **Déployer le proxy d’application web** dans la liste des tâches disponibles.

   ![Déployer le proxy d’application web](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Indiquez les informations d’identification de l’administrateur global Azure.

   ![Se connecter à Azure AD](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. Dans la page **Spécifiez le certificat SSL** , indiquez le mot de passe du fichier PFX que vous avez fourni lors de la configuration de la batterie de serveurs AD FS avec Azure AD Connect.
   ![Mot de passe du certificat](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![Spécifiez le certificat SSL](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. Ajoutez le serveur à ajouter en tant que serveur de proxy d’application web. Étant donné que le serveur de proxy d’application web peut être joint ou non au domaine, l’Assistant vous demande les informations d’identification administratives du serveur en cours d’ajout.

   ![Informations d’identification du serveur d’administration](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. Dans la page **Informations d’identification de confiance du proxy** , indiquez les informations d’identification de l’administrateur pour configurer l’approbation du proxy et accéder au serveur principal dans la batterie de serveurs AD FS.

   ![Informations d’identification de confiance du proxy](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. Dans la page **Prêt à configurer** , l’Assistant affiche la liste des actions qui seront effectuées.

   ![Prêt à configurer](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. Cliquez sur **Installer** pour terminer la configuration. Une fois la configuration terminée, l’Assistant vous permet de vérifier la connectivité aux serveurs. Cliquez sur **Vérifier** pour vérifier la connectivité.

   ![Installation terminée](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Ajout d’un domaine fédéré 

Il est facile d’ajouter un domaine à fédérer avec Azure AD à l’aide d’Azure AD Connect. Azure AD Connect ajoute le domaine de la fédération et modifie les règles de revendication pour identifier correctement l’émetteur lorsque plusieurs domaines sont fédérés avec Azure AD.

1. Pour ajouter un domaine fédéré, sélectionnez la tâche **Ajout d’un domaine Azure AD supplémentaire**.

   ![Domaine Azure AD supplémentaire](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. Dans la page suivante de l’Assistant, indiquez les informations d’identification de l’administrateur global d’Azure AD.

   ![Se connecter à Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. Dans la page **Informations d’identification d’accès à distance** , indiquez les informations d’identification de l’administrateur de domaine.

   ![Informations d’identification d’accès à distance](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. Dans la page suivante, l’Assistant affiche une liste de domaines Azure AD dans lesquels vous pouvez fédérer votre annuaire local. Sélectionnez le domaine dans la liste.

   ![Domaine Azure AD](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    Après avoir choisi le domaine, l’Assistant présente des informations utiles concernant les autres actions qu’il va effectuer et l’impact de la configuration. Dans certains cas, si vous sélectionnez un domaine qui n’est pas encore vérifié dans Azure AD, l’Assistant affiche des informations pour vous aider à vérifier le domaine. Pour plus d’informations, consultez [Ajouter un nom de domaine personnalisé à Azure Active Directory](../active-directory-domains-add-azure-portal.md) .

5. Cliquez sur **Suivant**. La page **Prêt à configurer** affiche la liste des actions qui seront effectuées par Azure AD Connect. Cliquez sur **Installer** pour terminer la configuration.

   ![Prêt à configurer](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

> [!NOTE]
> Les utilisateurs du domaine fédéré ajouté doivent être synchronisés pour pouvoir se connecter à Azure AD.

## <a name="ad-fs-customization"></a>Personnalisation d’AD FS
Les sections suivantes fournissent des informations concernant certaines tâches courantes que vous devrez peut-être effectuer pour personnaliser votre page de connexion AD FS.

## <a name="customlogo"></a>Ajout d’une illustration ou d’un logo de société personnalisé 
Pour changer le logo de la société affiché sur la page **Connexion**, utilisez l’applet de commande et la syntaxe Windows PowerShell suivantes.

> [!NOTE]
> Les dimensions recommandées pour le logo sont 260 x 35 à 260 PPP avec une taille de fichier maximale de 35 Ko.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Le paramètre *TargetName* est obligatoire. Le thème par défaut publié avec AD FS est nommé Par défaut.

## <a name="addsignindescription"></a>Ajout d’une description de connexion 
Pour ajouter une description de la **page de connexion**, utilisez l’applet de commande et la syntaxe Windows PowerShell suivantes.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Modification des règles de revendication AD FS 
AD FS prend en charge un langage complet, qui permet de créer des règles de revendication personnalisées. Pour plus d’informations, consultez [Rôle du langage de règle de revendication](https://technet.microsoft.com/library/dd807118.aspx).

Les sections suivantes décrivent comment écrire des règles personnalisées pour certains scénarios se rapportant à la fédération des services Azure AD et AD FS.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID non modifiable à condition que la valeur soit présente dans l’attribut
Azure AD Connect vous permet de spécifier un attribut à utiliser comme ancre source, lorsque les objets sont synchronisés avec Azure AD. Si la valeur de l’attribut personnalisé n’est pas vide, vous souhaiterez peut-être émettre une revendication d’ID non modifiable.

Par exemple, vous pouvez sélectionner **ms-ds-consistencyguid** comme attribut de l’ancre source et émettre **ms-ds-consistencyguid** comme **ImmutableID**, si l’attribut a une valeur. Si l’attribut n’a pas de valeur, émettez l’ID non modifiable **objectGuid** . Vous pouvez construire le jeu de règles de revendication personnalisées, comme indiqué dans la section suivante.

**Règle 1 : attributs de la requête**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Dans cette règle, vous interrogez les valeurs de **ms-ds-consistencyguid** et **objectGuid** de l’utilisateur à partir d’Active Directory. Remplacez le nom du magasin par un nom de magasin approprié dans votre déploiement AD FS. Remplacez également le type de revendication par un type approprié à votre fédération, comme défini pour **objectGuid** et **ms-ds-consistencyguid**.

Par ailleurs, utiliser **add** à la place de **issue** évite d’ajouter un problème à la sortie de l’entité et permet d’utiliser les valeurs en tant que valeurs intermédiaires. Vous allez émettre la revendication dans une règle ultérieure, après avoir établi la valeur à utiliser comme ID non modifiable.

**Règle 2 : vérifier si ms-ds-consistencyguid existe pour l’utilisateur**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Cette règle définit un indicateur temporaire **idflag** dont la valeur est **useguid** si aucun ID **ms-ds-concistencyguid** n’est renseigné pour l’utilisateur. Il y a une logique à cela : AD FS n’autorise pas les revendications vides. Ainsi, lorsque vous ajoutez les revendications http://contoso.com/ws/2016/02/identity/claims/objectguid et http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid dans la règle 1, vous vous retrouvez avec une revendication **msdsconsistencyguid** uniquement si la valeur est indiquée pour l’utilisateur. Si elle n’est pas indiquée, AD FS voit que sa valeur sera vide et le supprime immédiatement. Tous les objets auront un **objectGuid**. Donc, cette revendication sera toujours là après l’exécution de la règle 1.

**Règle 3 : émettre ms-ds-consistencyguid comme ID non modifiable s’il est présent**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Il s’agit d’un contrôle **Exist** implicite. Si la valeur de la revendication existe, alors émettez-la en tant qu’ID non modifiable. L’exemple précédent utilise le **nameidentifier** de revendication. Vous devez le remplacer par un type de revendication approprié pour l’ID non modifiable dans votre environnement.

**Règle 4 : émettre objectGuid comme ID non modifiable si ms-ds-consistencyGuid n’est pas présent**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Dans cette règle, vous vérifiez simplement l’indicateur temporaire **idflag**. Vous décidez s’il faut émettre la revendication en fonction de sa valeur.

> [!NOTE]
> L’ordre de ces règles est important.

### <a name="sso-with-a-subdomain-upn"></a>Authentification unique avec un UPN de sous-domaine
Vous pouvez ajouter plusieurs domaines à fédérer à l’aide d’Azure AD Connect, comme indiqué dans [Ajout d’un domaine fédéré](active-directory-aadconnect-federation-management.md#addfeddomain). Vous devez modifier la revendication de nom d’utilisateur principal (UPN) pour faire correspondre l’ID de l’émetteur au domaine racine et non au sous-domaine, car le domaine racine fédéré englobe également l’enfant.

Par défaut, la règle de revendication pour l’ID de l’émetteur est définie telle quelle :

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Revendication de l’ID d’émetteur par défaut](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

La règle par défaut prend simplement le suffixe UPN et l’utilise dans la revendication de l’ID de l’émetteur. Par exemple, John est un utilisateur de sub.contoso.com et contoso.com est fédéré à Azure AD. John entre john@sub.contoso.com en tant que nom d’utilisateur lors de la connexion à Azure AD. La règle de revendication de l’ID de l’émetteur par défaut dans AD FS le traite alors de la manière suivante :

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valeur de revendication :**  http://sub.contoso.com/adfs/services/trust/

Pour n’avoir que le domaine racine dans la valeur de revendication de l’émetteur, modifiez la règle de revendication de la manière suivante :

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md).
