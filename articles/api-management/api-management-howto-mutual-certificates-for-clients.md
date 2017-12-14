---
title: "Sécuriser les API avec l’authentification de certificat client dans la gestion des API - Gestion des API Azure | Microsoft Docs"
description: "Apprenez à sécuriser l’accès aux API à l’aide des certificats clients"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Comment sécuriser les API à l'aide d'une authentification par certificat client dans la Gestion des API

La Gestion des API permet de sécuriser l'accès aux API (par ex. client à gestion des API) en utilisant des certificats client. Actuellement, vous pouvez vérifier l’empreinte numérique d’un certificat client par rapport à une valeur souhaitée. Vous pouvez également vérifier l’empreinte numérique par rapport aux certificats existants téléchargés dans la gestion des API.  

Pour plus d’informations sur la sécurisation de l’accès au service principal d’une API à l’aide de certificats clients (par exemple, gestion des API vers service principal), consultez [Comment sécuriser les services principaux à l’aide d’une authentification par certificat client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates).

## <a name="checking-the-expiration-date"></a>Vérification de la date d’expiration

Les stratégies ci-dessous peuvent être configurées pour vérifier si le certificat a expiré :

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Vérification de l’émetteur et du sujet

Les stratégies suivantes peuvent être configurées pour vérifier l’émetteur et le sujet d’un certificat client :

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Vérification de l’empreinte numérique

Les stratégies suivantes peuvent être configurées pour vérifier l’empreinte d’un certificat client :

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Vérification d’une empreinte par rapport aux certificats téléchargés dans la gestion des API

L’exemple suivant montre comment vérifier l’empreinte d’un certificat client par rapport aux certificats téléchargés dans la gestion des API : 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Étape suivante

*  [Comment sécuriser les services principaux à l'aide d'une authentification par certificat client](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Comment télécharger des certificats](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

