---
title: 'Azure Active Directory B2C : Définitions et exemples de l’API de rapports d’utilisation | Microsoft Docs'
description: Guide et exemples d’obtention de rapports sur les utilisateurs, les authentifications et les authentifications multifacteurs des locataires Azure AD B2C
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 3470f45683ea508dfe338c313a5307eb6c1164d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Accès aux rapports d’utilisation dans Azure AD B2C via l’API de création de rapports

Azure Active Directory B2C (Azure AD B2C) fournit une authentification basée sur la connexion des utilisateurs et de l’authentification multifacteur d’Azure. L’authentification est fournie pour les utilisateurs finals de votre famille d’applications à travers les fournisseurs d’identité. Quand vous connaissez le nombre d’utilisateurs inscrits dans le locataire, les fournisseurs qu’ils ont utilisés pour s’inscrire et le nombre d’authentifications par type, vous pouvez répondre à des questions comme celles-ci :
* Combien d’utilisateurs de chaque type de fournisseur d’identité (par exemple un compte Microsoft ou LinkedIn) se sont inscrits au cours des 10 derniers jours ?
* Combien d’authentifications utilisant l’authentification multifacteur ont réussi au cours du mois dernier ?
* Combien d’authentifications basées sur la connexion ont été effectuées ce mois-ci ? Par jour ? Par application ?
* Comment puis-je estimer le coût mensuel prévu de l’activité de mon locataire Azure AD B2C ?

Cet article se concentre sur les rapports liés à l’activité de facturation, qui est basée sur le nombre d’utilisateurs, les authentifications facturables basées sur la connexion et les authentifications multifacteurs.


## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer les étapes décrites dans [Prérequis pour accéder aux API de création de rapports d’Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Créez une application, obtenez une clé secrète pour celle-ci et accordez-lui des autorisations d’accès aux rapports de votre client Azure AD B2C. Des exemples *Script Bash* et *Script Python* sont également fournis ici. 

## <a name="powershell-script"></a>Script PowerShell
Ce script montre la création de quatre rapports d’utilisation avec le paramètre `TimeStamp` et le filtre `ApplicationId`.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Définitions de rapport d’utilisation
* **tenantUserCount** : le nombre d’utilisateurs dans le locataire par type de fournisseur d’identité, par jour, au cours des 30 derniers jours. (Facultatif : un filtre `TimeStamp` fournit le nombre d’utilisateurs à partir d’une date spécifiée jusqu’à la date du jour.) Le rapport fournit les éléments suivants :
  * **TotalUserCount** : nombre de tous les objets utilisateur.
  * **OtherUserCount** : nombre d’utilisateurs d’Azure Active Directory (pas les utilisateurs d’Azure AD B2C).
  * **LocalUserCount** : nombre de comptes utilisateur Azure AD B2C créés avec les informations d’identification locales du locataire Azure AD B2C.

* **AlternateIdUserCount** : nombre d’utilisateurs d’Azure AD B2C inscrits auprès de fournisseurs d’identité externes (par exemple Facebook, un compte Microsoft ou un autre locataire Azure Active Directory, également appelé `OrgId`).

* **b2cAuthenticationCountSummary** : récapitulatif du nombre quotidien d’authentifications facturables au cours des 30 derniers jours, par jour et par type de flux d’authentification.

* **b2cAuthenticationCount** : nombre d’authentifications dans une période de temps définie. La période par défaut est celle des 30 derniers jours.  (Facultatif : les paramètres `TimeStamp` de début et de fin définissent une période spécifique.) La sortie inclut `StartTimeStamp` (première date d’activité de ce locataire ) et `EndTimeStamp` (dernière mise à jour).

* **b2cMfaRequestCountSummary** : récapitulatif du nombre quotidien d’authentifications multifacteurs, par jour et par type (SMS ou vocales).


## <a name="limitations"></a>Limites
Les données du décompte d’utilisateurs sont actualisées toutes les 24 à 48 heures. Les authentifications sont mises à jour plusieurs fois par jour. Quand vous utilisez le filtre `ApplicationId`, une réponse de rapport vide peut être due à une des conditions suivantes :
  * L’ID d’application n’existe pas dans le locataire. Assurez-vous qu’il est correct.
  * L’ID d’application existe, mais aucune donnée n’a été trouvée dans la période du rapport. Passez en revue vos paramètres de date/heure.


## <a name="next-steps"></a>Étapes suivantes
### <a name="monthly-bill-estimates-for-azure-ad"></a>Estimations de la facturation mensuelle pour Azure AD
Lorsque vous utilisez également [la tarification Azure AD B2C la plus récente disponible](https://azure.microsoft.com/pricing/details/active-directory-b2c/), vous pouvez estimer la consommation Azure quotidienne, hebdomadaire et mensuelle.  Une estimation est particulièrement utile quand vous prévoyez des changements de comportement du locataire qui peuvent avoir un impact sur le coût global. Vous pouvez examiner les coûts réels dans votre [abonnement Azure lié](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Options pour les autres formats de sortie
Le code suivant montre des exemples de l’envoi de la sortie vers JSON, vers une liste de valeurs de noms et vers XML :
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
