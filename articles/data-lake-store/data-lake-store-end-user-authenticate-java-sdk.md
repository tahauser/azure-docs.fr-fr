---
title: "Authentification des utilisateurs finals : Java avec Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment authentifier les utilisateurs finals auprès de Data Lake Store à l’aide d’Azure Active Directory avec Java."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b1fbf3c1e0e5715ff7effcdac45ff03f2b55f9ee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>Authentification des utilisateurs finals auprès de Data Lake Store avec Java
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Dans cet article, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Java pour authentifier les utilisateurs finals auprès d’Azure Data Lake Store. Pour plus d’informations sur l’authentification de service à service auprès de Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java, consultez l’article [Authentification de service à service auprès de Data Lake Store à l’aide de Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Conditions préalables
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « native » Azure Active Directory**. Vous devez avoir suivi la procédure [Authentification d’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de build et de projet. Bien qu’il soit possible de créer sans utiliser un système de build comme Maven ou Gradle, ces systèmes facilitent considérablement la gestion des dépendances.

* (Facultatif) Et un environnement IDE comme [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaire.

## <a name="end-user-authentication"></a>Authentification de l’utilisateur final
1. Créez un projet Maven en utilisant [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) à partir de la ligne de commande ou à l’aide d’un environnement de développement intégré (IDE). Vous trouverez [ici](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html) des instructions sur la création d’un projet Java à l’aide d’IntelliJ. Vous trouverez [ici](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm) des instructions sur la création d’un projet à l’aide d’Eclipse.

2. Ajoutez les dépendances suivantes à votre fichier Maven **pom.xml**. Ajoutez l’extrait de code suivant avant la balise **\</project>** :
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La première dépendance consiste à utiliser le SDK Data Lake Store (`azure-data-lake-store-sdk`) à partir du référentiel maven. La seconde dépendance consiste à spécifier le framework de journalisation (`slf4j-nop`) à utiliser pour cette application. Le SDK Data Lake Store utilise la façade de journalisation [slf4j](http://www.slf4j.org/), ce qui vous permet de choisir parmi plusieurs frameworks de journalisation populaires tels que log4j, la journalisation Java, Logback, etc., ou aucune journalisation. Pour cet exemple, nous désactivons la journalisation et, par conséquent, nous utilisons la liaison **slf4j-nop**. Vous trouverez [ici](http://www.slf4j.org/manual.html#projectDep) des informations pour utiliser d’autres options de journalisation.

3. Ajoutez les instructions import ci-après à votre application.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Utilisez l’extrait de code ci-après dans votre application Java afin d’obtenir un jeton pour l’application native Active Directory que vous avez créée précédemment à l’aide de l’élément `DeviceCodeTokenProvider`. Remplacez **FILL-IN-HERE** (à remplir) par les valeurs réelles de l’application native Azure Active Directory.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Le SDK Data Lake Store propose des méthodes pratiques pour gérer les jetons de sécurité nécessaires pour communiquer avec le compte Data Lake Store. Toutefois, le Kit de développement (SDK) n’impose pas d’utiliser ces seules méthodes. Vous pouvez utiliser tout autre moyen pour obtenir le jeton, par exemple à l’aide du [SDK Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java), ou de votre propre code personnalisé.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser l’authentification des utilisateurs finals auprès d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser le Kit de développement logiciel (SDK) Java pour travailler avec Azure Data Lake Store.

* [Opérations de gestion du système de fichiers sur Data Lake Store à l’aide du kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)


