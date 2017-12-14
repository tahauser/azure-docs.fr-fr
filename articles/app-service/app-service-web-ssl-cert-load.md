---
title: "Utiliser un certificat SSL chargé dans votre code d’application dans Azure App Service | Microsoft Docs"
description: 
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utiliser un certificat SSL dans votre code d’application dans Azure App Service

Cette procédure vous explique comment utiliser l’un des certificats SSL que vous avez chargés ou importés sur votre application App Service dans votre code d’application. Un exemple de cas d’utilisation est l’accès de votre application à un service externe qui nécessite une authentification de certificat. 

Cette approche de l’utilisation de certificats SSL dans votre code se sert de la fonctionnalité SSL dans App Service, ce qui implique que votre application soit au niveau **De base** ou plus. Une alternative consiste à inclure le fichier de certificat dans le répertoire de votre application et à le charger directement (consultez [Alternative : charger un certificat en tant que fichier](#file)). Toutefois, cette solution ne vous permet pas de masquer la clé privée du certificat à partir du code de l’application ou du développeur. En outre, si votre code d’application se trouve dans un référentiel open source, conserver un certificat avec une clé privée dans le référentiel n’est pas une option.

Lorsque vous laissez App Service gérer vos certificats SSL, vous pouvez conserver les certificats et votre code d’application séparément et protéger vos données sensibles.

## <a name="prerequisites"></a>Composants requis

Pour suivre cette procédure :

- [Création d’une application App Service](/azure/app-service/)
- [Mappage d’un nom DNS personnalisé à une application web](app-service-web-tutorial-custom-domain.md)
- [Chargement d’un certificat SSL](app-service-web-tutorial-custom-ssl.md) ou [importation d’un App Service Certificate](web-sites-purchase-ssl-web-site.md) dans votre application web


## <a name="load-your-certificates"></a>Charger vos certificats

Pour utiliser un certificat chargé ou importé sur App Service, commencez par le rendre accessible à votre code d’application. Pour ce faire, utilisez le paramètre d’application `WEBSITE_LOAD_CERTIFICATES`.

Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez la page de votre application web.

Dans le volet de navigation gauche, cliquez sur **Certificats SSL**.

![Certificat chargé](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Tous vos certificats SSL importés et chargés pour cette application web sont affichés ici avec leurs empreintes. Copiez l’empreinte du certificat à utiliser.

Dans le volet de navigation gauche, cliquez sur **Paramètres de l’application**.

Ajoutez une application nommée `WEBSITE_LOAD_CERTIFICATES` et définissez sa valeur sur l’empreinte du certificat. Pour que plusieurs certificats soient accessibles, utilisez des valeurs d’empreinte séparées par des virgules. Pour que tous les certificats soient accessibles, définissez la valeur sur `*`. 

![Configurer les paramètres de l’application](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Lorsque vous avez terminé, cliquez sur **Enregistrer**.

Le certificat configuré est maintenant prêt à être utilisé par votre code.

## <a name="use-certificate-in-c-code"></a>Utiliser un certificat dans le code C#

Une fois que votre certificat est accessible, vous y accéder dans le code C# via l’empreinte du certificat. Le code suivant charge un certificat avec l’empreinte `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>Alternative : charger un certificat en tant que fichier

Cette section montre comment charger un fichier de certificat se trouvant dans le répertoire de votre application. 

L’exemple C# suivant charge un certificat nommé `mycert.pfx` à partir du répertoire `certs` du référentiel de votre application.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

