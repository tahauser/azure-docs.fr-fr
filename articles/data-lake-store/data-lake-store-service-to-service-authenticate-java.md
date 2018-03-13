---
title: "Authentification de service à service : Java avec Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment procéder à une authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory avec Java"
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
ms.openlocfilehash: e537d8a6ea53bf4366168727de8ef95b96281d5b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-java"></a>Authentification de service à service auprès de Data Lake Store à l’aide de Java
> [!div class="op_single_selector"]
> * [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Dans cet article, vous allez apprendre à utiliser le Kit de développement logiciel (SDK) Java pour effectuer une authentification de service à service auprès d’Azure Data Lake Store. L’authentification des utilisateurs finals auprès de Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java n’est pas prise en charge.

## <a name="prerequisites"></a>Conditions préalables
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Créez une application « web » Azure Active Directory**. Vous devez avoir suivi la procédure [Authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de build et de projet. Bien qu’il soit possible de créer sans utiliser un système de build comme Maven ou Gradle, ces systèmes facilitent considérablement la gestion des dépendances.

* (Facultatif) Et un environnement IDE comme [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaire.

## <a name="service-to-service-authentication"></a>Authentification de service à service
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
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Utilisez l’extrait de code ci-après dans votre application Java afin d’obtenir un jeton pour l’application web Active Directory que vous avez créée précédemment à l’aide de l’une des sous-classes de `AccessTokenProvider` (l’exemple ci-après utilise `ClientCredsTokenProvider`). Le fournisseur de jetons met en cache les informations d’identification utilisées pour obtenir le jeton en mémoire, et renouvelle automatiquement le jeton s’il est sur le point d’expirer. Il est possible de créer vos propres sous-classes de `AccessTokenProvider` afin que les jetons soient obtenus par votre code client. Toutefois, pour le moment, utilisons simplement le jeton fourni dans le Kit de développement logiciel (SDK).

    Remplacez **FILL-IN-HERE** (à remplir) par les valeurs réelles de l’application web Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Le SDK Data Lake Store propose des méthodes pratiques pour gérer les jetons de sécurité nécessaires pour communiquer avec le compte Data Lake Store. Toutefois, le Kit de développement (SDK) n’impose pas d’utiliser ces seules méthodes. Vous pouvez utiliser tout autre moyen pour obtenir le jeton, par exemple à l’aide du [SDK Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java), ou de votre propre code personnalisé.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser l’authentification des utilisateurs finals auprès d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java. Vous pouvez à présent consulter les articles ci-après, qui expliquent comment utiliser le Kit de développement logiciel (SDK) Java pour travailler avec Azure Data Lake Store.

* [Opérations de gestion du système de fichiers sur Data Lake Store à l’aide du kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)


