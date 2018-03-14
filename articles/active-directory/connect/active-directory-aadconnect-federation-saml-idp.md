---
title: "Azure AD Connect : utiliser un fournisseur d’identité SAML 2.0 pour l’authentification unique | Documents Microsoft"
description: "Cette rubrique décrit l’utilisation d’un IdP compatible SAML 2.0 pour l’authentification unique."
services: active-directory
author: billmath
manager: mtillman
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 46c65e0efdc91b70c5d0d2afdf83d7205efc8057
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2018
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Utiliser un fournisseur d’identité (IdP) SAML 2.0 pour l’authentification unique

Cette rubrique contient des informations sur l’utilisation d’un profil SP-Lite compatible SAML 2.0 basé sur un fournisseur d’identité en tant que Service STS (Security Token Service) / fournisseur d’identité préféré. Cela est utile lorsque vous avez déjà un répertoire d’utilisateurs et un Store de mots de passe local accessibles à l’aide de SAML 2.0. Ce répertoire d’utilisateurs existant peut être utilisé pour l’authentification à Office 365 et d’autres ressources sécurisées par AD Azure. Le profil SP-Lite SAML 2.0 est basé sur la norme d’identité fédérée Security Assertion Markup Language (SAML) largement utilisée pour fournir une structure d’échange d’authentification et d’attribut.

>[!NOTE]
>Pour obtenir la liste des IdP tiers testés pour une utilisation avec Azure AD, consultez la [liste de compatibilité de fédération Azure AD](active-directory-aadconnect-federation-compatibility.md)

Microsoft prend en charge cette expérience d’authentification comme l’intégration d’un service cloud Microsoft, par exemple Office 365, avec votre profil SAML 2.0 correctement configuré basé sur les fournisseurs d’identité. Les fournisseurs d’identité SAML 2.0 sont des produits tiers, par conséquent, Microsoft n’offre aucun support technique sur les meilleures pratiques de déploiement, configuration, dépannage les concernant. Une fois correctement configurée, la configuration correcte de l’intégration au fournisseur d’identité SAML 2.0 peut être testée à l’aide de l’outil Analyseur de connectivité Microsoft décrit plus en détail ci-dessous. Pour plus d’informations sur votre fournisseur d’identité basé sur un profil SP-Lite SAML 2.0, demandez à l’organisation qui l’a fournie.

>[!IMPORTANT]
>Seul un ensemble limité de clients est disponible dans ce scénario d’authentification avec les fournisseurs d’identité SAML 2.0, cela inclut :

>- Clients basés sur le Web tels que Outlook Web Access et Sharepoint Online
- Clients de messagerie riches qui utilisent l’authentification de base et une méthode d’accès Exchange prise en charge comme IMAP, POP, Active Sync, MAPI, etc. (le point de terminaison Enhanced Client Protocol est requis pour le déploiement), y compris :
    - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (différentes versions d’iOS)
    - Différents appareils Google Android
    - Windows Phone 7, Windows Phone 7.8 et Windows Phone 8.0
    - Client de messagerie Windows 8 et client de messagerie Windows 8.1
    - Client de messagerie Windows 10

Tous les autres clients ne sont pas disponibles dans ce scénario d’authentification avec votre fournisseur d’identité SAML 2.0. Par exemple, le client de bureau Lync 2010 n’est pas en mesure de vous connecter au service avec votre fournisseur d’identité SAML 2.0 configuré pour l’authentification unique.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Exigences du protocole SAML 2.0 Azure AD
Cette rubrique recense les exigences détaillées du protocole et de la mise en forme des messages que votre fournisseur d’identité SAML 2.0 doit mettre en œuvre pour établir la fédération avec Azure AD et activer l’authentification à un ou plusieurs services de cloud de Microsoft (par exemple, Office 365). La partie utilisatrice de SAML 2.0 (SP-STS) pour un service de cloud Microsoft utilisée dans ce scénario est Azure AD.

Il est recommandé de vérifier que les messages de sortie de votre fournisseur d’identité SAML 2.0 sont aussi semblables aux suivis d’exemples fournis que possible. En outre, utilisez les valeurs d’attribut spécifiques à partir des métadonnées Azure AD lorsque cela est possible. Une fois que vous êtes satisfait de vos messages de sortie, vous pouvez tester l’Analyseur de connectivité Microsoft comme décrit ci-dessous.

Les métadonnées Azure AD peuvent être téléchargées à partir de cette URL : [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Pour les clients en Chine qui utilisent l’instance spécifique à la Chine d’Office 365, le point de terminaison de fédération suivant doit être utilisé : [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Exigences du protocole SAML
Cette section présente en détail comment les paires de messages de requête et de réponse sont regroupées pour vous aider à mettre correctement en forme vos messages.

Azure AD peut être configuré pour fonctionner avec les fournisseurs d’identité qui utilisent le profil SP-Lite SAML 2.0 avec certaines exigences spécifiques indiquées ci-dessous. En utilisant les exemples de messages de requête et de réponse SAML ainsi que les tests automatisés et manuels, vous pouvez atteindre l’interopérabilité avec Azure AD.

## <a name="signature-block-requirements"></a>Exigences des blocs de signature
Dans le message de réponse SAML, le nœud Signature contient des informations sur la signature numérique du message proprement dit. Ce bloc de signature comporte les exigences suivantes :

1. Le nœud d’assertion doit être signé
2.  L’algorithme RSA-sha1 doit être utilisé comme DigestMethod. Les autres algorithmes de signature numérique ne sont pas acceptés.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Vous pouvez aussi signer le document XML. 
4.  L’algorithme de transformation doit correspondre aux valeurs dans l’exemple suivant :`<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  L’algorithme SignatureMethod doit correspondre à l’exemple suivant :`<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Liaisons prises en charge
Les liaisons représentent les paramètres des communications relatives au transport requises. Les exigences suivantes s’appliquent aux liaisons

1. HTTPS est le transport requis.
2.  Azure AD exige HTTP POST pour la soumission de jeton au cours de la connexion
3.  Azure AD utilise HTTP POST pour la demande d’authentification auprès du fournisseur d’identité et REDIRECT pour le message de fermeture de session pour le fournisseur d’identité.

## <a name="required-attributes"></a>Attributs requis
Cette table présente les exigences des attributs spécifiques dans le message SAML 2.0.
 
|Attribut|Description|
| ----- | ----- |
|NameID|La valeur de cette assertion doit être la même que ImmutableID de l’utilisateur Azure AD. Elle peut avoir jusqu'à 64 caractères alphanumériques. Tous les caractères HTML non sécurisés doivent être encodés, par exemple un caractère « + » est affiché comme «.2B ».|
|IDPEmail|Le Nom principal utilisateur (UPN) est répertorié dans la réponse SAML sous la forme d’un élément portant le nom IDPEmail. Il s’agit du UserPrincipalName (UPN) de l’utilisateur dans Azure AD/Office 365. L’UPN est au format de l’adresse de messagerie. Valeur UPN dans Windows Office 365 (Azure Active Directory).|
|Émetteur|Cela est nécessaire pour être un URI du fournisseur d’identité. Vous ne devez pas réutiliser l’émetteur des exemples de messages. Si vous avez plusieurs domaines de premier niveau dans vos clients Azure AD, l’émetteur doit correspondre au paramètre URI spécifié, configuré par domaine.|

>[!IMPORTANT]
>Actuellement, Azure AD prend en charge les URI au Format NameID suivant pour SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>Exemple de messages de requête et de réponse SAML
Une paire de messages de requête et de réponse est affichée pour l’échange de messages d’authentification.
Il s’agit d’un exemple de message de requête envoyé à partir d’Azure AD à un exemple de fournisseur d’identité SAML 2.0. L’exemple de fournisseur d’identité SAML 2.0 est Active Directory Federation Services (AD FS) configuré pour utiliser le protocole SAML-P. Les tests d’interopérabilité ont également été effectués avec d’autres fournisseurs d’identité SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Il s’agit d’un exemple de message de réponse envoyé depuis l’exemple de fournisseur d’identité SAML 2.0 vers Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurer votre fournisseur d’identité compatible SAML 2.0
Cette rubrique contient des instructions sur comment configurer votre fournisseur d’identité SAML 2.0 pour établir la fédération avec Azure AD et permettre l’accès d’authentification unique à un ou plusieurs services cloud Microsoft (par exemple, Office 365) à l’aide du protocole SAML 2.0. La partie utilisatrice de SAML 2.0 pour un service de cloud Microsoft utilisée dans ce scénario est Azure AD.

## <a name="add-azure-ad-metadata"></a>Ajouter les métadonnées Azure AD
Votre fournisseur d’identité SAML 2.0 doit se conformer aux informations sur la partie utilisatrice AD Azure. Azure AD publie des métadonnées sur https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Il est recommandé de toujours importer les dernières métadonnées Azure AD lors de la configuration de votre fournisseur d’identité SAML 2.0. Notez qu’Azure AD ne lit pas les métadonnées du fournisseur d’identité.

## <a name="add-azure-ad-as-a-relying-party"></a>Ajouter Azure AD en tant que partie utilisatrice
Vous devez activer la communication entre votre fournisseur d’identité SAML 2.0 et Azure AD. Cette configuration dépend de votre fournisseur d’identité spécifique, vous devez vous référer à la documentation correspondante. Vous pouvez généralement définir l’ID de la partie utilisatrice identique à l’entityID à partir des métadonnées Azure AD.

>[!NOTE]
>Vérifiez que l’horloge du serveur de votre fournisseur d’identité SAML 2.0 est synchronisée à une heure source précise. Une heure inexacte peut entraîner l’échec des connexions fédérées.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installez Windows PowerShell pour l’authentification avec le fournisseur d’identité SAML 2.0
Après avoir configuré votre fournisseur d’identité SAML 2.0 pour une utilisation avec l’authentification Azure AD, l’étape suivante consiste à télécharger et installer le Module Azure Active Directory pour Windows PowerShell. Une fois installé, utilisez ces applets de commande pour configurer vos domaines Azure AD en tant que domaines fédérés.

Le Module Azure Active Directory pour Windows PowerShell est téléchargé pour la gestion des données de votre organisation dans Azure AD. Ce module installe un ensemble d’applets de commande Windows PowerShell ; exécutez ces applets de commande pour configurer l’accès d’authentification unique à Azure AD et à tous les services cloud auxquels vous êtes abonné. Pour obtenir des instructions sur la façon de télécharger et d’installer les applets de commande, consultez [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Configurer une approbation entre votre fournisseur d’identité SAML et Azure AD
Avant de configurer la fédération sur un domaine Azure AD, un domaine personnalisé doit être configuré. Vous ne pouvez pas fédérer le domaine par défaut fourni par Microsoft. Le domaine par défaut de Microsoft se termine par « onmicrosoft.com ».
Exécutez une série d’applets de commande dans l’interface de ligne de commandes de Windows PowerShell pour ajouter ou convertir des domaines pour l’authentification unique.

Chaque domaine Azure Active Directory que vous souhaitez fédérer à l’aide de votre fournisseur d’identité SAML 2.0 doit être ajouté en tant que domaine d’authentification unique ou converti en un seul domaine d’authentification d’un domaine standard. L’ajout ou la conversion d’un domaine configure une approbation entre votre fournisseur d’identité SAML 2.0 et Azure AD.

La procédure suivante vous guide tout au long de la conversion d’un domaine standard existant en domaine fédéré à l’aide de SP-Lite SAML 2.0. Notez que votre domaine peut rencontrer une indisponibilité qui impacte les utilisateurs pendant au moins 2 heures après avoir effectué cette étape.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configuration d’un domaine dans votre répertoire Azure AD pour la fédération


1. Connectez-vous à votre répertoire Azure AD en tant qu’administrateur client : Connect-MsolService.
2.  Configurez votre domaine Office 365 souhaité pour utiliser la fédération avec SAML 2.0 :`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Vous pouvez obtenir la chaîne codée en base 64 du certificat de signature à partir de votre fichier de métadonnées IDP. Un exemple de cet emplacement a été spécifié mais peut varier légèrement en fonction de votre mise en œuvre.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Pour plus d’informations sur « Set-MsolDomainAuthentication », consultez : [http://technet.microsoft.com/library/dn194112.aspx](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Vous devez exécutez utiliser « $ecpUrl = « https://WS2012R2-0.contoso.com/PAOS » » uniquement si vous définissez une extension ECP pour votre fournisseur d’identité. Les clients Exchange Online, à l’exception d’Outlook Web Application (OWA), s’appuient sur un point de terminaison actif basé sur POST. Si votre STS SAML 2.0 met en œuvre un point de terminaison actif similaire à la mise en œuvre d’ECP de Shibboleth, il est possible pour ces clients riches d’interagir avec le service Exchange Online.

Une fois la fédération configurée, vous pouvez basculer vers « non fédéré » (ou « géré »), toutefois ce changement peut prendre jusqu'à deux heures et nécessite l’attribution de nouveaux mots de passe aléatoires pour l’authentification basée sur le cloud pour chaque utilisateur. Basculer vers « géré » peut être nécessaire dans certains scénarios pour réinitialiser une erreur dans vos paramètres. Pour plus d’informations sur la conversion de domaine, consultez : [http://msdn.microsoft.com/library/windowsazure/dn194122.aspx](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Approvisionner les principaux d’utilisateur à Azure AD / Office 365
Avant d’authentifier vos utilisateurs à Office 365, vous devez approvisionner Azure AD avec des principaux d’utilisateur qui correspondent à l’assertion de la revendication SAML 2.0. Si ces principaux d’utilisateur ne sont pas connus dans Azure AD à l’avance, ils ne peuvent pas être utilisés pour la connexion fédérée. Azure AD Connect ou Windows PowerShell peut être utilisé pour approvisionner les principaux d’utilisateur.

Azure AD Connect peut être utilisé pour approvisionner les principaux vers vos domaines dans votre Azure AD Directory à partir d’Active Directory local. Pour obtenir plus d’informations, consultez [Intégrer vos répertoires locaux avec Azure Active Directory](active-directory-aadconnect.md).

Windows PowerShell peut également être utilisé pour automatiser l’ajout de nouveaux utilisateurs à Azure AD et synchroniser les modifications apportées à partir du répertoire local. Pour utiliser les applets de commande Windows PowerShell, vous devez télécharger les [Modules Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Cette procédure indique comment ajouter un utilisateur unique à Azure AD.


1. Connectez-vous à votre Azure AD Directory en tant qu’administrateur client : Connect-MsolService.
2.  Créer un nouveau principal d’utilisateur : ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Pour plus d’informations sur la vérification de « New-MsolUser », [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>La valeur « UserPrinciplName » doit correspondre à la valeur que vous envoyez pour « IDPEmail » dans votre revendication SAML 2.0 et la valeur « ImmutableID » doit correspondre à la valeur envoyée dans votre assertion « NameID ».

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Vérifiez l’authentification unique avec votre fournisseur d’identité SAML 2.0
En tant qu’administrateur, avant de vérifier et gérer l’authentification unique (également appelée fédération des identités), passez en revue les informations et suivez les étapes dans les articles ci-dessous pour configurer l’authentification unique avec votre fournisseur d’identité basé sur SP-Lite SAML 2.0 :


1.  Vous avez consulté les exigences du protocole SAML 2.0 Azure AD
2.  Vous avez configuré votre fournisseur d’identité SAML 2.0
3.  Installez Windows PowerShell pour l’authentification unique avec le fournisseur d’identité SAML 2.0
4.  Configurez une approbation entre votre fournisseur d’identité SAML 2.0 et Azure AD
5.  Configurez un principal d’utilisateur test connu sur Azure Active Directory (Office 365) par le biais de Windows PowerShell ou Azure AD Connect.
6.  Configurez la synchronisation de répertoires à l’aide de [Azure AD Connect](active-directory-aadconnect.md).

Après avoir configuré l’authentification unique avec votre fournisseur d’identité basé sur SP-Lite SAML 2.0, vous devez vérifier qu’elle fonctionne correctement.

>[!NOTE]
>Si vous avez converti un domaine, plutôt que d’en ajouter un, l’authentification unique peut prendre jusqu'à 24 heures.
Avant de vérifier l’authentification unique, vous devez terminer la configuration de la synchronisation Active Directory, synchroniser vos répertoires et activer vos utilisateurs synchronisés.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Utilisez l’outil pour vérifier que l’authentification unique a été correctement configurée
Afin de vérifier la configuration correcte de l’authentification unique, vous pouvez exécuter la procédure suivante pour confirmer la possible connexion au service clous avec vos informations d’identification.

Microsoft propose un outil que vous pouvez utiliser pour tester votre fournisseur d’identité basé sur SAML 2.0. Avant d’exécuter l’outil de test, vous devez avoir configuré un client Azure AD pour procéder à une fédération avec votre fournisseur d’identité.

>[!NOTE]
>L’Analyseur de connectivité requiert Internet Explorer 10 ou version ultérieure.



1. Téléchargez l’Analyseur de connectivité à partir de [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Cliquez sur Installer maintenant pour commencer à télécharger et installer l’outil.
3.  Sélectionnez « Je ne peux pas configurer la fédération avec Office 365, Azure ou d’autres services qui utilisent Azure Active Directory ».
4.  Une fois l’outil téléchargé et en cours d’exécution, la fenêtre Diagnostics de connectivité s’affiche. L’outil vous guide pour tester votre connexion de fédération.
5.  L’Analyseur de connectivité ouvre votre fournisseur d’identité SAML 2.0 pour vous permettre de vous connecter, entrez les informations d’identification pour le principal d’utilisateur que vous testez : ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  Dans la fenêtre de connexion des tests de la fédération, entrez un nom de compte et un mot de passe pour le client Azure AD configuré pour être fédéré avec votre fournisseur d’identité SAML 2.0. L’outil tente de se connecter à l’aide de ces informations d’identification et les résultats détaillés de tests effectués lors de la tentative de connexion sont fournis en tant que sortie.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Cette fenêtre affiche un résultat d’échec du test. Cliquez sur Vérifier les résultats détaillés pour afficher des informations sur les résultats de chaque test effectué. Vous pouvez également enregistrer les résultats sur le disque afin de les partager.
 
>[!NOTE]
>L’Analyseur de connectivité teste également la Fédération Active à l’aide des protocoles basés sur WS* et ECP/PAOS. Si vous ne les utilisez pas, vous pouvez ignorer l’erreur suivante : tester le flux de connexion active à l’aide du point de terminaison de la Fédération active de votre fournisseur d’identité.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Vérifiez manuellement que l’authentification unique a été correctement configurée
La vérification manuelle comprend des étapes supplémentaires que vous pouvez suivre pour vous assurer que votre fournisseur d’identité SAML 2.0 fonctionne correctement dans de nombreux scénarios.
Pour vérifier que l’authentification unique a été correctement configurée, procédez comme suit :


1. Sur un ordinateur joint au domaine, connectez-vous à votre service cloud à l’aide du même nom d’ouverture de session que vous utilisez pour vos informations d’identification d’entreprise.
2.  Cliquez dans la zone de mot de passe. Si l’authentification unique est configurée, la zone de mot de passe est grisée et le message suivant s’affiche : « Vous devez maintenant vous connecter à <your company>. »
3.  Cliquez sur Se connecter sur le lien <your company>. Si vous êtes en mesure de vous connecter, l’authentification unique a été configurée.

## <a name="next-steps"></a>Étapes suivantes


- [Gestion des services AD FS (Active Directory Federation Services) et personnalisation avec Azure AD Connect](active-directory-aadconnect-federation-management.md)
- [Liste de compatibilité de fédération Azure AD](active-directory-aadconnect-federation-compatibility.md)
- [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
