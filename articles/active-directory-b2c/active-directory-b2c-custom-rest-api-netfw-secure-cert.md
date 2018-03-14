---
title: "Azure Active Directory B2C : sécuriser votre service RESTful à l’aide de certificats clients"
description: "Sécuriser vos échanges de revendications d’API REST dans votre instance Azure AD B2C à l’aide de certificats clients"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 582aadd35821779e307ac285804e3b7fe5c24abd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Sécuriser votre service RESTful à l’aide de certificats clients
Dans un article connexe, vous [créez un service RESTful](active-directory-b2c-custom-rest-api-netfw.md) qui interagit avec Azure Active Directory B2C (Azure AD B2C).

Dans cet article, vous allez apprendre à restreindre l’accès à votre application web Azure (API RESTful) à l’aide d’un certificat client. Ce mécanisme est appelé authentification mutuelle TLS ou bien *authentification par certificat client*. Seuls les services qui ont des certificats appropriés, tels qu’Azure AD B2C, peuvent accéder à votre service.

>[!NOTE]
>Vous pouvez également sécuriser votre service RESTful [à l’aide de l’authentification de base HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Toutefois, l’authentification de base HTTP est considérée comme moins sécurisée qu’un certificat client. Notre recommandation est de sécuriser le service RESTful à l’aide de l’authentification par certificat client comme décrit dans cet article.

Cet article explique comment :
* Configurer votre application Web pour utiliser l’authentification par certificat client
* Télécharger le certificat sur les clés de stratégie de Azure AD B2C
* Configurer votre stratégie personnalisée pour utiliser le certificat client

## <a name="prerequisites"></a>Prérequis
* Suivre les étapes dans l’article [Intégrer les échanges de revendications de l’API REST](active-directory-b2c-custom-rest-api-netfw.md).
* Obtenir un certificat valide (fichier .pfx avec une clé privée).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Étape 1 : Configurer une application web pour l’authentification par certificat client
Pour configurer **Azure App Service** afin d’exiger des certificats clients, définissez le paramètre de site de l’application web `clientCertEnabled` sur *true*. Pour ce faire, vous devez utiliser l’API REST. Le paramètre est disponible via l’expérience de gestion dans le portail Azure. Pour localiser le paramètre, dans le menu **Paramètres** de votre application RESTful, sous **Outils de développement**, sélectionnez **Explorateur de ressources**.

>[!NOTE]
>Assurez-vous que votre plan Azure App Service est Standard ou supérieur. Pour plus d’informations, consultez la rubrique [Présentation détaillée des plans Azure App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Utilisez [Azure Resource Explorer (préversion)](https://resources.azure.com) pour définir la propriété **clientCertEnabled** sur la valeur *true*, comme l’illustre l’image suivante :

![Définition de la propriété clientCertEnabled par le biais d’Azure Resource Explorer](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Pour plus d’informations sur la définition de la propriété **clientCertEnabled**, consultez [Configurer l’authentification mutuelle TLS pour des applications web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>Vous pouvez également faciliter l’élaboration de l’appel de l’API REST à l’aide de l’outil [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Étape 2 : Télécharger votre certificat sur les clés de stratégie de Azure AD B2C
Après avoir défini `clientCertEnabled` sur *true*, la communication avec votre API RESTful nécessite un certificat client. Pour obtenir, charger et stocker le certificat client dans votre locataire Azure AD B2C, effectuez les opérations suivantes : 
1. Dans votre locataire Azure AD B2C, sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**.

2. Pour afficher les clés disponibles dans votre locataire, sélectionnez **Clés de stratégie**.

3. Sélectionnez **Ajouter**.  
    La fenêtre **Créer une clé** s’ouvre.

4. Dans la zone **Options**, sélectionnez **Charger**.

5. Dans la zone **Nom**, tapez **B2cRestClientCertificate**.  
    Le préfixe *B2C_1A_* est ajouté automatiquement.

6. Dans la zone **Chargement de fichier**, sélectionnez le fichier .pfx de votre certificat avec une clé privée.

7. Dans la zone **Mot de passe**, entrez le mot de passe du certificat.

    ![Télécharger la clé de stratégie](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Sélectionnez **Créer**.

8. Pour afficher les clés qui sont disponibles dans votre locataire et confirmer que vous avez créé la clé `B2C_1A_B2cRestClientCertificate`, sélectionnez **Clés de stratégie**.

## <a name="step-3-change-the-technical-profile"></a>Étape 3 : Changer le profil technique
Pour prendre en charge l’authentification par certificat client dans votre stratégie personnalisée, changez le profil technique en effectuant les opérations suivantes :

1. Dans votre répertoire de travail, ouvrez le fichier de stratégie d’extension *TrustFrameworkExtensions.xml*.

2. Recherchez le nœud `<TechnicalProfile>` qui inclut `Id="REST-API-SignUp"`.

3. Recherchez l’élément `<Metadata>`.

4. Définissez *AuthenticationType* sur *ClientCertificate*, comme suit :

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Immédiatement après l’élément `<Metadata>` fermant, ajoutez l’extrait de code XML suivant : 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Une fois l’extrait de code ajouté, votre profil technique doit se présenter comme le code XML suivant :

    ![Définir les éléments XML de l’authentification ClientCertificate (Certificat client)](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Étape 4 : Charger la stratégie sur votre locataire

1. Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), puis sélectionnez **Azure AD B2C**.

2. Sélectionnez **Infrastructure d’expérience d’identité**.

3. Sélectionnez **Toutes les stratégies**.

4. Sélectionnez **Charger la stratégie**.

5. Activez la case à cocher **Remplacer la stratégie si elle existe**.

6. Chargez le fichier *TrustFrameworkExtensions.xml*, puis vérifiez que sa validation réussit.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Étape 5 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1. Ouvrez **Paramètres Azure AD B2C**, puis sélectionnez **Infrastructure d’expérience d’identité**.

    >[!NOTE]
    >L’option Exécuter maintenant nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2. Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée, puis sélectionnez **Exécuter maintenant**.

3. Testez le processus en tapant **Test** dans la zone **Prénom**.  
    Azure AD B2C affiche un message d’erreur en haut de la fenêtre.    

    ![Tester l’API d’identité](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Dans la zone **Prénom**, tapez un nom (différent de « Test »).  
    Azure AD B2C inscrit l’utilisateur, puis envoie un numéro de fidélité à votre application. Notez le numéro dans cet exemple JWT :

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Si vous recevez le message d’erreur *Le nom n’est pas valide, fournissez un nom valide*, cela signifie qu’Azure AD B2C a pu appeler votre service RESTful pendant qu’il présentait le certificat client. L’étape suivante consiste à valider le certificat.

## <a name="step-6-add-certificate-validation"></a>Étape 6 : Ajouter une validation de certificat
Le certificat client envoyé par Azure AD B2C à votre service RESTful ne fait pas l’objet d’une validation de la part de la plateforme Azure Web Apps, hormis vérifier si le certificat existe. La validation de ce certificat est la responsabilité de l'application Web. 

Dans cette section, vous ajoutez un exemple de code ASP.NET qui valide les propriétés du certificat pour l’authentification.

> [!NOTE]
>Pour plus d’informations sur la configuration d’Azure App Service pour l’authentification par certificat client, consultez [Configurer l’authentification mutuelle TLS pour des applications web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Ajouter des paramètres d’application au fichier web.config de votre projet
Dans le projet Visual Studio que vous avez créé, ajoutez les paramètres d’application suivants au fichier *web.config* après l’élément `appSettings` :

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Remplacez les valeurs **Nom du sujet**, **Nom de l’émetteur** et **Empreinte du certificat** du certificat par les valeurs de votre certificat.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Ajouter la fonction IsValidClientCertificate
Ouvrez le fichier *Controllers\IdentityController.cs*, puis ajoutez la fonction suivante à la classe de contrôleur `Identity` : 

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Dans l’exemple de code précédent, nous n’acceptons le certificat comme étant valide que si toutes les conditions suivantes sont remplies :
* Le certificat n’a pas expiré et est actif à l’heure actuelle sur le serveur.
* Le nom `Subject` du certificat est égal à la valeur de paramètre d’application `ClientCertificate:Subject`.
* Le nom `Issuer` du certificat est égal à la valeur de paramètre d’application `ClientCertificate:Issuer`.
* Le `thumbprint` du certificat est égal à la valeur de paramètre d’application `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>En fonction de la sensibilité de votre service, vous devrez peut-être ajouter plus de validations. Par exemple, vous pouvez être amené à tester la chaîne d’approbation vers une autorité racine fiable, la validation du nom de l’organisation émettrice et autres.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Appeler la fonction IsValidClientCertificate
Ouvrez le fichier *Controllers\IdentityController.cs*, puis, au début de la fonction `SignUp()`, ajoutez l’extrait de code suivant : 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Une fois l’extrait de code ajouté, votre contrôleur `Identity` doit se présenter comme le code suivant :

![Ajout d’un code de validation de certificat](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Étape 7 : Publier votre projet sur Azure et le tester
1. Dans**l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.API**, puis sélectionnez **Publier**.

2. Répétez l’étape 6 et retestez votre stratégie personnalisée avec la validation du certificat. Essayez d’exécuter la stratégie et assurez-vous que tout fonctionne après avoir ajouté la validation.

3. Dans votre fichier *web.config*, définissez `ClientCertificate:Subject` sur **invalid**. Réexécutez la stratégie ; vous devriez alors voir un message d’erreur.

4. Rétablissez la valeur **valid**, puis vérifiez que la stratégie peut appeler votre API REST.

Si vous avez des problèmes avec cette étape, consultez [Collecter les journaux à l’aide d’Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Facultatif) Télécharger les fichiers de stratégie et le code complets
* Une fois que vous avez [pris en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md), nous vous recommandons de créer votre scénario à l’aide de vos propres fichiers de stratégie personnalisée. Des [exemples de fichiers de stratégie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert) sont à votre disposition pour référence.
* Vous pouvez télécharger le code complet dans [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
