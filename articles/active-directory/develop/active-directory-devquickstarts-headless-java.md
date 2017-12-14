---
title: "Bien démarrer avec la ligne de commande Azure AD Java | Microsoft Docs"
description: "Comment créer une application de ligne de commande Java qui connecte les utilisateurs pour l’accès à une API."
services: active-directory
documentationcenter: java
author: navyasric
manager: mtillman
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2330ccf734944a8a563f9031a9d51902255c30d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Utilisation d’une application en ligne de commande Java pour accéder à une API avec Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD simplifie l’externalisation de la gestion des identités de votre application web en fournissant une authentification unique avec seulement quelques lignes de code.  Dans les applications web Java, vous pouvez y parvenir en utilisant l’implémentation Microsoft d’ADAL4J communautaire.

  Ici, nous allons utiliser ADAL4J pour :

* connecter l’utilisateur à l’application à l’aide d’Azure AD comme fournisseur d’identité ;
* afficher des informations sur l’utilisateur ;
* déconnecter l’utilisateur de l’application.

Pour ce faire, vous devez :

1. inscrire une application auprès d’Azure AD ;
2. Configurez votre application pour utiliser la bibliothèque ADAL4J.
3. Utilisez la bibliothèque ADAL4J pour émettre des demandes de connexion et de déconnexion dans Azure AD.
4. afficher les données relatives à l’utilisateur.

Pour commencer, téléchargez [la structure de l’application](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Vous aurez également besoin d’un client Azure AD dans lequel inscrire votre application.  Si ce n’est pas déjà fait, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Inscrire une application auprès d’Azure AD
Pour autoriser l’authentification des utilisateurs par votre application, vous devez tout d’abord inscrire une nouvelle application dans votre client.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Autres services** dans le volet de navigation gauche et choisissez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sur **Ajouter**.
5. Suivez les invites et créez une **Application Web et/ou API Web**.
  * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
  * L’ **URL de connexion** est l’URL de base de votre application.  La valeur par défaut de la structure est `http://localhost:8080/adal4jsample/`.
6. Une fois l’inscription terminée, AAD affecte un ID d’application unique à votre application.  Copiez cette valeur dans l’onglet de l’application, car vous en aurez besoin dans les sections suivantes.
7. À partir de la page **Paramètres** -> **Propriétés** de votre application, mettez à jour l’URI ID d’application. Un **URI ID d’application** est un identificateur unique pour votre application.  L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple `http://localhost:8080/adal4jsample/`.

Une fois dans le portail de votre application, créez une **clé** dans la page **Paramètres** pour votre application et notez-la quelque part.  Vous en aurez besoin rapidement.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Configurer votre application pour utiliser la bibliothèque ADAL4J et la configuration requise à l’aide de Maven
Ici, nous allons configurer ADAL4J pour utiliser le protocole d’authentification OpenID Connect.  ADAL4J sera utilisée pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

* Dans le répertoire racine de votre projet, ouvrez/créez `pom.xml`, recherchez `// TODO: provide dependencies for Maven` et remplacez cette portion par les éléments suivants :

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Créer le fichier PublicClient Java
Comme indiqué ci-dessus, nous utiliserons l’API Graph pour obtenir les données relatives à l’utilisateur connecté. Afin de nous faciliter la tâche, nous devons créer un fichier pour représenter un **objet répertoire** et un fichier individuel pour représenter **l’utilisateur** afin que le modèle OO de Java puisse être utilisé.

* Créez un fichier appelé `DirectoryObject.java` que nous utiliserons pour stocker les données de base sur n’importe quel objet répertoire (n’hésitez pas à l’utiliser ultérieurement pour toutes vos autres requêtes Graph éventuelles). Vous pouvez couper/coller ces éléments ci-dessous :

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Compiler et exécuter l’exemple
Revenez à votre répertoire racine et exécutez la commande suivante pour générer l’exemple que vous venez de créer à l’aide de `maven`. L’opération utilisera le fichier `pom.xml` que vous avez écrit pour vos dépendances.

`$ mvn package`

Vous devez maintenant disposer d’un fichier `adal4jsample.war` dans votre répertoire `/targets`. Vous pouvez le déployer dans votre conteneur Tomcat et ouvrir l’URL 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> Il est très facile de déployer un fichier WAR avec les derniers serveurs Tomcat. Accédez simplement à `http://localhost:8080/manager/` et suivez les instructions pour charger votre fichier adal4jsample.war. Il se déploiera automatiquement pour vous avec le point de terminaison correct.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Vous disposez désormais d’une application Java fonctionnelle capable d’authentifier les utilisateurs, d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et d’obtenir des informations de base concernant l’utilisateur.  Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

