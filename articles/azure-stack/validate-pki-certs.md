---
title: "Valider des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs"
description: "Explique comment valider les certificats pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Valider des certificats PKI Azure Stack
L’outil de vérification de certificat Azure Stack décrit dans cet article est fourni par l’OEM inclus dans le fichier deploymentdata.json afin de vérifier que les [certificats PKI générés](azure-stack-get-pki-certs.md) conviennent pour le prédéploiement. Les certificats doivent être validés avec suffisamment de temps pour tester et obtenir les certificats réémis si nécessaire. 

L’outil de vérification de certificat (Certchecker) effectue les vérifications suivantes :

- **Lecture PFX**. Vérifie si le fichier PFX est valide, si mot de passe est correct et vous avertit si les informations publiques ne sont pas protégées par un mot de passe. 
- **Algorithme de signature**. Vérifie que l’algorithme de signature n’est pas SHA1. 
- **Clé privée**. Vérifie que la clé privée est présente et qu’elle est exportée avec l’attribut Ordinateur Local. 
- **Chaîne de certificats**. Vérifie que la chaîne de certificats est intacte, y compris pour les certificats auto-signés. 
- **Noms DNS**. Vérifie que le réseau SAN contient les noms DNS appropriés pour chaque point de terminaison ou si un caractère générique de prise en charge est présent. 
- **Syntaxe de la clé**. Vérifie que la syntaxe de la clé contient une signature numérique et un chiffrement de clé, et que la syntaxe avancée de la clé contient l’authentification du serveur et l’authentification du client. 
- **Taille de la clé**. Vérifie que la taille de clé est d’au moins 2 048 octets. 
- **Ordre de la chaîne**. Vérifie que l’ordre des autres certificats qui constituent la chaîne est correct. 
- **Autres certificats**. Assurez-vous qu’aucun autre certificat n’a été packagé dans le PFX, à part le certificat feuille pertinent et sa chaîne. 
- **Aucun profil**. Vérifie qu’un nouvel utilisateur peut charger les données PFX sans profil utilisateur chargé, en simulant le comportement des comptes de service administré de groupe pendant la maintenance du certificat.   

> [!IMPORTANT]
> Le fichier PFX du certificat PKI et le mot de passe doivent être considérés comme des informations sensibles.

## <a name="prerequisites"></a>Prérequis
Votre système doit respecter la configuration requise suivante pour permettre la validation des certificats PKI pour le déploiement Azure Stack :
- CertChecker (dans PartnerToolKit sous \utils\certchecker)
- Certificats SSL exportés conformément aux [instructions de préparation](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 ou Windows Server 2016

## <a name="perform-certificate-validation"></a>Valider les certificats
Suivez ces étapes pour préparer et valider les certificats PKI Azure Stack : 

1. Extrayez le contenu de <partnerToolkit>\utils\certchecker vers un nouveau répertoire (par exemple, **c:\certchecker**).

2. Ouvrez PowerShell en tant qu’administrateur et modifiez le répertoire du dossier certchecker :

  ```powershell
  cd c:\certchecker
  ```
 
3. Créez une structure de répertoires pour les certificats en exécutant les commandes PowerShell suivantes :

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Si le fournisseur d’identité du déploiement Azure Stack est Azure AD, supprimez les répertoires **ADFS** et **Graph**. 

4. Placez vos certificats dans les répertoires appropriés créés à l’étape précédente, par exemple : 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - Etc. 

5. Copiez **deploymentdata.json** dans le répertoire **c:\certchecker**.

6. Dans la fenêtre PowerShell, exécutez les commandes suivantes : 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. La sortie doit contenir OK pour tous les certificats et tous les attributs vérifiés : 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Problèmes connus 
**Symptôme**: Certchecker se termine prématurément et vous recevez l’erreur suivante : 
> Échec

> Détails : cette commande ne peut pas être exécutée en raison de l’erreur : le nom du répertoire n’est pas valide. 

**Cause** : exécution de certchecker.ps1 à partir d’un dossier restrictif (par exemple, c:\temp ou % temp%). 

**Résolution** : déplacez l’outil certchecker dans nouveau répertoire (par exemple, C:\CertChecker). 


**Symptôme** : Certchecker émet un avertissement sur l’utilisation d’une version antérieure à 1803 (comme dans l’exemple ci-dessus à l’étape 7) :

> [!WARNING]
> Structure de certificat antérieure à 1803. La structure de dossiers pour Azure Stack 1803 et versions ultérieures est : ACSBlob, ACSQueue, ACSTable au lieu du dossier ACS. Pour plus d’informations, consultez la documentation sur le déploiement.

**Cause** : CertChecker a détecté l’utilisation d’un seul dossier ACS. Cela est approprié pour les déploiements antérieurs à la version 1803. Pour les déploiements Azure Stack 1803 et versions ultérieures, la structure de dossiers devient ACSTable, ACSQueue, ACSBlob. Certchecker a déjà été mis à jour pour prendre en charge cette fonctionnalité.

**Résolution** : si vous déployez la version 1802, aucune action n’est requise. Pour un déploiement 1803 et versions ultérieures, remplacez ACS par ACSTable, ACSQueue, ACSBlob, puis copiez les certificats ACS dans ces dossiers.

**Symptôme** : les tests sont ignorés.

**Cause** : CertChecker ignore certains tests si une dépendance n’est pas détectée :
- Les autres certificats sont ignorés si la chaîne de certificats échoue.
- Aucun profil n’est ignoré si :
  - Il existe une stratégie de sécurité qui restreint la possibilité de créer un utilisateur temporaire et d’exécuter PowerShell en tant que cet utilisateur.
  - Échec de la vérification de la clé privée.

**Résolution** : suivez les instructions des outils dans la section des détails sous l’ensemble de tests de chaque certificat.


## <a name="prepare-deployment-script-certificates"></a>Préparer les certificats de script de déploiement 
Comme dernière étape, tous les certificats que vous avez préparés doivent être placés dans les répertoires appropriés sur l’ordinateur hôte de déploiement. Sur votre hôte de déploiement, créez un dossier nommé Certificats** et placez les fichiers de certificat exportés dans les sous-dossiers correspondants spécifiés dans la section [Certificats obligatoires](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) :

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Les certificats signalés par un astérisque * sont nécessaires uniquement lorsqu’AD FS est utilisé en tant que magasin d’identités.


## <a name="next-steps"></a>étapes suivantes
[Intégration des identités au centre de données](azure-stack-integrate-identity.md)