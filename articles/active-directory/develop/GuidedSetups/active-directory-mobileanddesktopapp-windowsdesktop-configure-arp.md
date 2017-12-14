---
title: "Prise en main d’Azure AD v2 avec les applications de bureau Windows - Configuration | Microsoft Docs"
description: "Cet article explique comment une application de bureau Windows .NET (XAML) peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison Azure Active Directory v2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 922fd0e24334eb45995b66dfaaa49d3d4f835e9a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Ajouter les informations d’inscription de l’application à votre application
Dans cette étape, vous devez ajouter l’ID d’application à votre projet.

1.  Ouvrez `App.xaml.cs` et remplacez la ligne contenant `ClientId` par :

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Étapes suivantes

[!INCLUDE  [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]
