---
title: "Protocole SAML de déconnexion unique Azure | Microsoft Docs"
description: "Cet article décrit le protocole SAML de déconnexion unique dans Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: c77bf15d69a4c7749567f53df96c91a1d329a466
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="single-sign-out-saml-protocol"></a>Protocole SAML de déconnexion unique
Azure Active Directory (Azure AD) prend en charge le profil de déconnexion unique du navigateur web SAML 2.0. Pour que la déconnexion unique fonctionne correctement, l’élément **LogoutURL** de l’application doit être explicitement inscrit auprès d’Azure AD pendant l’inscription de l’application. Azure AD utilise l’élément LogoutURL pour rediriger les utilisateurs une fois qu’ils sont déconnectés.

Ce schéma illustre le workflow du processus de déconnexion unique Azure AD.

![Workflow de déconnexion unique](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Le service cloud envoie un message `LogoutRequest` à Azure AD pour indiquer qu’une session a été arrêtée. L’extrait suivant illustre un exemple d’élément `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
L’élément `LogoutRequest` envoyé à Azure AD requiert les attributs suivants :

* `ID` : identifie la demande de déconnexion. La valeur `ID` ne doit pas commencer par un chiffre. La méthode classique consiste à ajouter **id** à la représentation sous forme de chaîne d’un GUID.
* `Version` : définissez la valeur de cet élément sur **2.0**. Cette valeur est obligatoire.
* `IssueInstant` : chaîne `DateTime` associée à une valeur UTC et ayant le [format aller-retour (« o »)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure Active Directory attend une valeur de ce type, mais ne l’applique pas.

### <a name="issuer"></a>Émetteur
L’élément `Issuer` dans `LogoutRequest` doit correspondre exactement à l’un des **ServicePrincipalNames** du service cloud dans Azure AD. En règle générale, il est défini sur **l’URI ID d’application** spécifié au moment de l’inscription de l’application.

### <a name="nameid"></a>NameID
La valeur de l’élément `NameID` doit correspondre exactement à la valeur `NameID` de l’utilisateur déconnecté.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD envoie une `LogoutResponse` en réponse à un élément `LogoutRequest`. L’extrait suivant illustre un exemple d’élément `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD définit les valeurs `ID`, `Version` et `IssueInstant` dans l’élément `LogoutResponse`. Il définit également l’élément `InResponseTo` sur la valeur de l’attribut `ID` de l’élément `LogoutRequest` qui a obtenu la réponse.

### <a name="issuer"></a>Émetteur
Azure AD définit cette valeur sur `https://login.microsoftonline.com/<TenantIdGUID>/`, où <TenantIdGUID> correspond à l’ID client du client Azure AD.

Pour évaluer la valeur de l’élément `Issuer` , utilisez la valeur de **l’URI ID d’application** spécifiée lors de l’inscription de l’application.

### <a name="status"></a>État
Azure AD utilise l’élément `StatusCode` dans l’élément `Status` pour indiquer la réussite ou l’échec de la déconnexion. En cas d’échec de la tentative de déconnexion, l’élément `StatusCode` peut également contenir des messages d’erreur personnalisés.