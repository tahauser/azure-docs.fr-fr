---
title: "Comment utiliser PowerShell pour créer des certificats X.509 | Microsoft Docs"
description: "Comment utiliser PowerShell pour créer des certificats X.509 localement et activer la sécurité X.509 dans votre IoT Hub Azure dans un environnement simulé."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Scripts PowerShell permettant de gérer les certificats X.509 signés par une autorité de certification

Pour mettre en place une sécurité basée sur un certificat X.509 dans le IoT Hub, vous devez commencer par une [chaîne d’approbation X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), qui inclut le certificat racine, ainsi que tous les certificats intermédiaires jusqu’au certificat feuille. Ce guide *explicatif* présente des exemples de scripts PowerShell qui utilisent [OpenSSL](https://www.openssl.org/) pour créer et signer les certificats X.509. Nous vous recommandons d’utiliser ce guide à des fins d’expérimentation uniquement, étant donné que plusieurs de ces étapes se produisent en réalité pendant la fabrication. Vous pouvez utiliser ces certificats pour simuler la sécurité dans votre IoT Hub Azure à l’aide de *l’authentification par certificat X.509*. Les étapes décrites dans ce guide créent des certificats localement sur votre ordinateur Windows. 

## <a name="prerequisites"></a>Prérequis
Ce didacticiel suppose que vous avez acquis les fichiers binaires OpenSSL. Vous pouvez soit
    - télécharger le code source OpenSSL et générer les fichiers binaires sur votre ordinateur, ou 
    - télécharger et installer des [fichiers binaires OpenSSL tiers](https://wiki.openssl.org/index.php/Binaries), par exemple, à partir de [ce projet sur SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Créer des certificats X.509
Les étapes suivantes expliquent comment créer des certificats racines X.509 localement. 

1. Ouvrez une fenêtre PowerShell en tant qu’*administrateur*. 
2. Accédez à votre répertoire de travail. Exécutez le script suivant pour définir les variables globales. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Exécutez le script suivant pour copier les fichiers binaires OpenSSL dans votre répertoire de travail et définir les variables d’environnement :

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Exécutez ensuite le script suivant pour rechercher si un certificat portant le *nom d’objet* spécifié est déjà installé, et si OpenSSL est correctement configuré sur votre ordinateur :
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Si tout est correctement configuré, vous devez voir un message de réussite. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Créer la chaîne d’approbation X.509
Créez une chaîne d’approbation avec une autorité de certification racine, comme « CN = Azure IoT Root CA » utilisée dans cet exemple, en exécutant le script PowerShell suivant. Ce script met également à jour le magasin de certificats de votre système d’exploitation Windows, et crée des fichiers de certificat dans votre répertoire de travail. 
    1. Le script suivant génère une fonction PowerShell pour créer un certificat auto-signé, pour un *nom d’objet* et une autorité de signature donnés. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. La fonction PowerShell suivante crée des certificats X.509 intermédiaires à l’aide de la fonction précédente, ainsi que des fichiers binaires OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. La fonction PowerShell suivante crée la chaîne d’approbation X.509. Reportez-vous à [Chaînes d’approbation](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) pour en savoir plus.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Ce script crée un fichier nommé *RootCA.cer* dans votre répertoire de travail. 
    4. Enfin, utilisez les fonctions PowerShell ci-dessus pour créer la chaîne d’approbation X.509, en exécutant la commande `New-CACertChain` dans votre fenêtre PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Preuve de possession de votre certificat d’autorité de certification X.509

Ce script effectue le flux de *preuve de possession* de votre certificat X.509. 

Dans la fenêtre PowerShell sur votre bureau, exécutez le code suivant :
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Ce code crée un certificat portant le nom d’objet donné, signé par l’autorité de certification, sous forme de fichier nommé *VerifyCert4.cer* dans votre répertoire de travail. Ce fichier de certificat aidera à valider auprès de votre IoT Hub que vous disposez de l’autorisation de signature (autrement dit, de la clé privée) de cette autorité de certification.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Créer le certificat X.509 feuille pour votre appareil

Cette section montre que vous pouvez utiliser un script PowerShell qui crée un certificat feuille pour un appareil ainsi que la chaîne d’approbation correspondante. 

Dans la fenêtre PowerShell de votre ordinateur local, exécutez le script suivant pour créer un certificat X.509 signé par une autorité de certification pour cet appareil :

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Exécutez ensuite `New-CADevice "<yourTestDevice>"` dans votre fenêtre PowerShell, en utilisant le nom convivial que vous avez utilisé pour créer votre appareil. Lorsqu’il vous est demandé de saisir le mot de passe de la clé privée de l’autorité de certification, entrez « 123 ». Cette opération crée un fichier _<yourTestDevice>.pfx_ dans votre répertoire de travail.

## <a name="clean-up-certificates"></a>Nettoyer les certificats

Dans la barre de démarrage ou dans l’application **Paramètres**, recherchez et sélectionnez **Manage computer certificates** (Gérer les certificats d’ordinateur). Supprimez tous les certificats délivrés par **Azure IoT CA TestOnly**\*. Ces certificats doivent se trouver aux trois emplacements suivants : 

* Certificats - Ordinateur local > Personnel > Certificats
* Certificats - Ordinateur local > Autorités de certification racines de confiance > Certificats
* Certificats - Ordinateur local > Autorités de certification intermédiaires > Certificats

   ![Supprimer les certificats Azure IoT CA TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)