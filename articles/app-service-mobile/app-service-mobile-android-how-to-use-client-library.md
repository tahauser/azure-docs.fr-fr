---
title: "Comment utiliser le Kit de développement logiciel (SDK) Azure Mobile Apps pour Android | Microsoft Docs"
description: "Comment utiliser le Kit de développement logiciel (SDK) Azure Mobile Apps pour Android"
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: glenga
ms.openlocfilehash: ac5cbb51a5ed340a6cbf2eeefa41feb337d28fb9
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Comment utiliser le Kit de développement logiciel (SDK) Azure Mobile Apps pour Android

Ce guide vous montre comment utiliser le Kit de développement logiciel (SDK) du client Android pour Mobile Apps afin d’implémenter des scénarios courants tels que :

* Interrogation des données (insertion, mise à jour et suppression).
* Authentification.
* Gestion des erreurs.
* Personnalisation du client.

Ce guide est axé sur le kit de développement logiciel Android côté client.  Pour plus d’informations sur les Kits de développement logiciel (SDK) côté serveur pour Mobile Apps, consultez les articles [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps][10] ou [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps][11].

## <a name="reference-documentation"></a>Documentation de référence

La [référence de l’API Javadocs][12] pour la bibliothèque cliente Android se trouve sur GitHub.

## <a name="supported-platforms"></a>Plateformes prises en charge

Le Kit de développement logiciel (SDK) Azure Mobile Apps pour Android prend en charge les niveaux d’API compris entre 19 et 24 (de KitKat à Nougat) pour les facteurs de forme tablette et téléphone.  L’authentification utilise en particulier une approche courante d’infrastructure web pour recueillir des informations d’identification.  L’authentification de flux serveur ne fonctionne pas avec les appareils compacts comme les montres.

## <a name="setup-and-prerequisites"></a>Configuration et conditions préalables

Terminez le [didacticiel de démarrage rapide Mobile Apps](app-service-mobile-android-get-started.md) .  Cette tâche garantit que toutes les conditions préalables au développement d’Azure Mobile Apps ont été remplies.  Le didacticiel de démarrage rapide vous aide également à configurer et à créer votre premier backend Mobile App.

Si vous décidez de ne pas suivre le didacticiel de démarrage rapide, effectuez les tâches suivantes :

* [créer un backend Mobile Apps][13] à utiliser avec votre application Android ;
* dans Android Studio, [mettre à jour les fichiers de construction Gradle](#gradle-build);
* [activer les autorisations Internet](#enable-internet).

### <a name="gradle-build"></a>Mise à jour du fichier de construction Gradle

Modifiez les deux fichiers **build.gradle** :

1. ajoutez ce code au fichier de niveau *projet* **build.gradle** à l’intérieur de la balise *buildscript* :

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Ajoutez ce code au fichier de niveau *Module app* **build.gradle** à l’intérieur de la balise *dependencies* :

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    La version la plus récente est la 3.4.0. Les versions prises en charge sont répertoriées [sur Bintray][14].

### <a name="enable-internet"></a>activer les autorisations Internet.

Pour accéder à Azure, l’autorisation INTERNET doit être activée sur votre application. Si ce n’est pas déjà fait, ajoutez la ligne de code suivante à votre fichier **AndroidManifest.xml** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Créer une connexion cliente

Azure Mobile Apps propose quatre fonctions à votre application mobile :

* Accès aux données et synchronisation hors connexion avec un service Azure Mobile Apps.
* Appel des API personnalisées écrites avec le Kit de développement logiciel (SDK) de serveur Azure Mobile Apps.
* Authentification avec l’authentification et l’autorisation Azure App Service.
* Inscription de notifications Push avec Notification Hubs.

Pour chacune de ces fonctions, vous devez créer un objet `MobileServiceClient` au préalable.  Seul un objet `MobileServiceClient` doit être créé au sein de votre client mobile (autrement dit, il doit être un modèle de Singleton).  Pour créer un objet `MobileServiceClient` :

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` est soit une chaîne, soit un objet d’URL qui pointe vers votre serveur principal mobile.  Si vous utilisez Azure App Service pour héberger votre serveur principal mobile, veillez à utiliser la version `https://` sécurisée de l’URL.

Le client nécessite aussi un accès à l’activité ou au contexte (le paramètre `this` dans l’exemple).  La construction de MobileServiceClient doit se produire dans la méthode `onCreate()` de l’activité référencée dans le fichier `AndroidManifest.xml`.

Nous vous recommandons d’extraire la communication du serveur dans sa propre classe (modèle singleton).  Dans ce cas, vous devez transmettre l’activité dans le constructeur pour configurer correctement le service.  Par exemple :

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Vous pouvez maintenant appeler `AzureServiceAdapter.Initialize(this);` dans la méthode `onCreate()` de votre activité principale.  Toutes les autres méthodes nécessitant un accès au client utilisent `AzureServiceAdapter.getInstance();` pour obtenir une référence à l’adaptateur de services.

## <a name="data-operations"></a>Opérations de données

L’objectif principal du Kit de développement logiciel (SDK) Azure Mobile Apps consiste à fournir l’accès aux données stockées dans SQL Azure sur le serveur principal de l’application mobile.  Vous pouvez accéder à ces données à l’aide de classes fortement typées (recommandé) ou de requêtes non typées (déconseillé).  La majeure partie de cette section s’intéresse à l’utilisation des classes fortement typées.

### <a name="define-client-data-classes"></a>Définir des classes de données client

Pour accéder aux données à partir des tables SQL Azure, définissez des classes de données client qui correspondent aux tables du backend Mobile Apps. Les exemples de cette rubrique reposent sur une table nommée **MyDataTable**, qui contient les colonnes suivantes :

* id
* text
* terminé

L’objet côté client typé correspondant se trouve dans un fichier appelé **MyDataTable.java** :

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Incluez des méthodes getter et setter pour chaque champ que vous ajoutez.  Si votre table SQL Azure contient davantage de colonnes, vous devez ajouter les champs correspondants à cette classe.  Pour exemple, si l’objet de transfert de données (DTO, data transfer object) comporte une colonne d’entiers Priority, vous pouvez ajouter ce champ, ainsi que ses méthodes getter et setter :

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Pour savoir comment créer des tables supplémentaires dans votre backend Mobile Apps, consultez [Définir un contrôleur de table][15] (backend .NET) ou [Définir des tables à l’aide d’un schéma dynamique][16] (backend Node.js).

Une table de serveur principal Azure Mobile Apps définit cinq champs spéciaux, dont quatre sont disponibles pour les clients :

* `String id` : l’ID global unique pour l’enregistrement.  Nous vous recommandons de faire de l’ID la représentation de chaîne d’un objet [UUID][17].
* `DateTimeOffset updatedAt` : la date/l’heure de la dernière mise à jour.  Le champ updatedAt est défini par le serveur et ne doit jamais être défini par votre code client.
* `DateTimeOffset createdAt` : la date/l’heure de création de l’objet.  Le champ createdAt est défini par le serveur et ne doit jamais être défini par votre code client.
* `byte[] version` : généralement représentée sous forme de chaîne, la version est également définie par le serveur.
* `boolean deleted` : indique que l’enregistrement a été supprimé mais pas encore vidé.  N’utilisez pas `deleted` en tant que propriété dans votre classe.

Le champ `id` est obligatoire.  Les champs `updatedAt` et `version` sont utilisés pour la synchronisation hors connexion (pour la synchronisation incrémentielle et la résolution des conflits, respectivement).  Le champ `createdAt` est un champ de référence et n’est pas utilisé par le client.  Les noms sont des noms « à travers le câble » des propriétés et ne sont pas réglables.  Toutefois, vous pouvez créer un mappage entre votre objet et les noms « à travers le câble » à l’aide de la bibliothèque [gson][3].  Par exemple :

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getUpdatedAt() { return mCreatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected DateTimeOffset setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Créer une référence de table

Pour accéder à une table, vous devez d’abord créer un objet [MobileServiceTable][8] en appelant la méthode **getTable** sur le [MobileServiceClient][9].  Cette méthode comporte deux surcharges :

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Dans le code suivant, **mClient** est une référence à votre objet MobileServiceClient.  La première surcharge est utilisée quand le nom de la classe et le nom de la table sont identiques. Elle est reprise dans le didacticiel de démarrage rapide :

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

La deuxième surcharge est utilisée quand le nom de la table est différent du nom de la classe : le premier paramètre correspond au nom de la table.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Interroger une table de serveur principal

Commencez par obtenir une référence de table.  Ensuite, exécutez une requête sur la référence de table.  Une requête est une combinaison des éléments suivants :

* Une [clause de filtre](#filtering) `.where()`.
* Une [clause de classement](#sorting) `.orderBy()`.
* Une [clause de sélection de champ](#selection) `.select()`.
* Un élément `.skip()` et `.top()` pour les [résultats paginés](#paging).

Les clauses doivent être présentées dans l’ordre indiqué ci-dessus.

### <a name="filter"></a> Filtrage des résultats

La forme générale d’une requête est :

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

L’exemple précédent renvoie tous les résultats (jusqu’à la taille de page maximale définie par le serveur).  La méthode `.execute()` exécute la requête sur le serveur principal.  La requête est convertie en une requête [OData v3][19] avant sa transmission au serveur principal Mobile Apps.  À la réception, le serveur principal Mobile Apps convertit la requête en une instruction SQL avant de l’exécuter sur l’instance SQL Azure.  Étant donné que l’activité réseau prend du temps, la méthode `.execute()` renvoie un élément [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrer les données renvoyées

L’exécution de la requête suivante retourne tous les éléments de la table **ToDoItem** dont le paramètre **complete** est égal à **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** est la référence à la table des services mobiles que vous venez de créer.

Définissez un filtre en appelant la méthode **where** sur la référence de table. La méthode **where** est suivie d’une méthode **field**, suivie d’une méthode qui spécifie le prédicat logique. Méthodes de prédicat possibles : **eq** (égal à), **ne** (différent de), **gt** (supérieur à), **ge** (supérieur ou égal à), **lt** (inférieur à), **le** (inférieur ou égal à). Ces méthodes vous permettent de comparer les champs de nombre et de chaîne à des valeurs spécifiques.

Vous pouvez activer des filtres sur les dates. Les méthodes suivantes vous permettent de comparer le champ de date complet ou des parties de la date : **year**, **month**, **day**, **hour**, **minute** et **second**. L’exemple suivant ajoute un filtre pour les éléments dont la date d’échéance *due date* est égale à 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Les méthodes suivantes prennent en charge des filtres complexes sur les champs de chaîne : **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**, and **length**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne *text* commence par « PRI0 ».

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Les méthodes d’opérateur suivantes sont prises en charge sur les champs numériques : **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling**, and **round**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne **duration** est un nombre pair.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Vous pouvez combiner les prédicats avec les méthodes logiques **and**, **or** et **not**. L’exemple suivant combine deux des exemples précédents.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Regrouper et imbriquer des opérateurs logiques :

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Pour obtenir plus de détails et des exemples de filtres, consultez la page [Exploring the richness of the Mobile Services Android client query model][20](Exploration de la richesse du modèle de requête client Android Mobile Services).

### <a name="sorting"></a>Trier les données renvoyées

Le code qui suit renvoie tous les éléments de la table **ToDoItem** triés par ordre croissant sur le champ *text* . *mToDoTable* est la référence à la table de back-end créée précédemment :

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Le premier paramètre de la méthode **orderBy** est une chaîne équivalente au nom du champ sur lequel effectuer le tri. Le second paramètre utilise l’énumération **QueryOrder** pour spécifier si le tri doit être croissant ou décroissant.  Si vous filtrez avec la méthode ***where***, la méthode ***where*** doit être appelée avant la méthode ***orderBy***.

### <a name="selection"></a>Sélectionner des colonnes spécifiques

Le code qui suit illustre comment renvoyer tous les éléments d’une table **ToDoItems**, mais uniquement en affichant les champs **complete** et **text**. **mToDoTable** est la référence à la table de back-end créée précédemment.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Les paramètres associés à la fonction select sont les noms de chaîne des colonnes de table à renvoyer.  La méthode **select** doit suivre des méthodes telles que **where** et **orderBy**. Elle peut être suivie de méthodes de pagination telles que **skip** et **top**.

### <a name="paging"></a>Renvoyer les données de pages

Les données sont **TOUJOURS** renvoyées dans les pages.  Le nombre maximal d’enregistrements renvoyés est défini par le serveur.  Si le client demande plus d’enregistrements, le serveur renvoie le nombre maximal d’enregistrements.  Par défaut, la taille de page maximale sur le serveur est de 50 enregistrements.

Le premier exemple présente comment sélectionner les cinq premiers éléments d'une table. Cette requête renvoie les éléments d’une table **ToDoItem**. **mToDoTable** est la référence à la table de back-end créée précédemment :

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Voici maintenant une requête qui ignore les cinq premiers éléments, puis renvoie les cinq suivants :

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Si vous souhaitez obtenir tous les enregistrements dans une table, implémentez le code pour effectuer une itération sur toutes les pages :

```java
List<MyDataModel> results = new List<MyDataModel>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Une demande pour tous les enregistrements à l’aide de cette méthode crée un minimum de deux demandes à destination du serveur principal Mobile Apps.

> [!TIP]
> Le choix de la taille de page appropriée est un équilibre entre l’utilisation de la mémoire pendant l’exécution de la demande, l’utilisation de la bande passante et le délai de réception de toutes les données.  La valeur par défaut (50 enregistrements) convient à tous les appareils.  Si vous travaillez exclusivement sur des appareils avec une grande capacité de mémoire, augmentez cette valeur jusqu’à 500.  Nous avons constaté que l’augmentation de la taille de page au-delà de 500 enregistrements engendre des retards inacceptables et d’importants problèmes de mémoire.

### <a name="chaining"></a>Procédure de concaténation de méthodes de requête

Les méthodes utilisées dans les requêtes de tables de backend peuvent être concaténées. La concaténation des méthodes de requêtes vous permet de sélectionner des colonnes spécifiques de lignes filtrées, qui sont triées et paginées. Vous pouvez créer des filtres logiques complexes.  Chaque méthode de requête retourne un objet de requête. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode **execute** . Par exemple :

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Les méthodes de requête concaténées doivent être classées comme suit :

1. Filtrage des méthodes (**where**).
2. Tri des méthodes (**orderBy**).
3. Sélection des méthodes (**select**).
4. pagination des méthodes (**skip** et **top**).

## <a name="binding"></a>Lier des données à l’interface utilisateur

La liaison des données nécessite trois composants :

* la source de données ;
* la mise en page à l’écran ;
* l’adaptateur qui lie ces deux éléments.

Dans notre exemple de code, nous renvoyons les données de la table SQL Azure Mobile Apps **ToDoItem** dans un tableau. Cette activité est un cas de figure très courant pour les applications de données.  Les requêtes de base de données renvoient souvent une suite de lignes que le client récupère dans une liste ou un tableau. Dans cet exemple, le tableau est la source des données.  Ce code spécifie une mise en page à l'écran qui définit la façon dont les données sont affichées sur l'appareil.  Les deux sont liés par un adaptateur, qui, dans ce code, est une extension de la classe **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="layout"></a>Définir la mise en page

La mise en page est définie par plusieurs éléments de code XML. En nous basant sur une mise en page existante, le code qui suit représente la vue **ListView** que nous souhaitons remplir avec les données du serveur.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Dans le code précédent, l'attribut *listitem* spécifie l'ID de la mise en page de chaque ligne dans la liste. Ce code spécifie une case à cocher ainsi que le texte associé, et il est instancié une fois pour chaque élément dans la liste. Cette mise en page n’affiche pas le champ **id** ; une mise en page plus complexe spécifierait d’autres champs dans l’affichage. Le code se trouve dans le fichier **row_list_to_do.xml**.

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Définir l’adaptateur
Comme la source de données de notre vue est un tableau **ToDoItem**, nous créons une sous-classe de notre adaptateur à partir de la classe **ArrayAdapter&lt;ToDoItem&gt;**. Cette sous-classe produit une vue pour chaque élément **ToDoItem** utilisant la mise en page **row_list_to_do**.  Dans notre code, nous définissons la classe suivante, qui est une extension de la classe **ArrayAdapter&lt;E&gt;** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Ignorez la méthode **getView** de l'adaptateur. Par exemple :

```
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Nous créons une instance de cette classe dans notre activité, comme suit :

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Le deuxième paramètre du constructeur ToDoItemAdapter est une référence à la mise en page. Nous pouvons maintenant instancier l’élément **ListView** et attribuer l’adaptateur à **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Utiliser l’adaptateur pour effectuer la liaison à l’interface utilisateur

Vous êtes désormais prêt à utiliser la liaison des données. Le code suivant montre comment obtenir les éléments du tableau et remplit l’adaptateur local en utilisant les éléments renvoyés.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Appelez l’adaptateur chaque fois que vous modifiez le tableau **ToDoItem** . Comme les modifications se font enregistrement par enregistrement, vous ne gérez qu’une seule ligne, et non une collection. Lorsque vous insérez un élément, appelez la méthode **add** de l'adaptateur. Et lorsque vous supprimez un élément, appelez la méthode **remove**.

Vous trouverez un exemple complet dans le [projet de démarrage rapide Android][21].

## <a name="inserting"></a>Insérer des données dans le serveur principal

Créez une instance de la classe *ToDoItem* et définissez ses propriétés.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Utilisez ensuite **insert()** pour insérer un objet :

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

L’entité renvoyée correspond aux données insérées dans la table du serveur principal, incluant l’ID et toutes les autres valeurs (telles que les champs `createdAt`, `updatedAt` et `version`) définies sur le serveur principal.

Les tables Mobile Apps nécessitent une colonne de clé primaire nommée **id**. Cette colonne doit être une chaîne. La valeur par défaut de la colonne ID est un GUID.  Vous pouvez fournir d’autres valeurs uniques, telles que des adresses de messagerie ou des noms d’utilisateurs. Lorsqu’aucune valeur d’ID de chaîne n’est fournie pour un enregistrement inséré, le backend génère une nouvelle valeur GUID.

Les valeurs d’ID de chaîne offrent les avantages suivants :

* Les ID peuvent être générés sans effectuer d’aller-retour vers la base de données.
* Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
* Les valeurs d’ID s’intègrent mieux à la logique d’une application.

Les valeurs d’ID de chaîne sont **obligatoires** pour la prise en charge de la synchronisation hors connexion.  Vous ne pouvez pas modifier un ID une fois stocké dans la base de données principale.

## <a name="updating"></a>Mettre à jour des données dans une application mobile

Pour mettre à jour les données d’une table, transmettez le nouvel objet à la méthode **update()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Dans cet exemple, *l’élément* est une référence à une ligne dans la table *ToDoItem*, qui a été modifiée.  La ligne avec le même **id** est mise à jour.

## <a name="deleting"></a>Supprimer des données dans une application mobile

Le code suivant montre comment supprimer les données d’une table en spécifiant l’objet de données.

```java
mToDoTable
    .delete(item);
```

Vous pouvez également supprimer un élément en spécifiant le champ **id** de la ligne à supprimer.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Rechercher un élément spécifique par ID

Recherchez un élément avec un champ **id** spécifique avec la méthode **lookUp()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Procédure : utilisation de données non typées

Le modèle de programmation non typé vous offre un contrôle total de la sérialisation JSON.  Il existe certains scénarios courants où vous pouvez utiliser un modèle de programmation non typé. Par exemple, si la table de votre backend contient un grand nombre de colonnes et que vous n’avez besoin de faire référence qu’à quelques-unes d’entre elles.  Avec le modèle typé, vous devez définir toutes les colonnes du serveur principal Mobile Apps dans votre classe de données.  La plupart des appels d'API permettant d'accéder aux données sont similaires à ceux des appels de programmation typés. La principale différence est que, dans le modèle non typé, vous appelez des méthodes dans l'objet **MobileServiceJsonTable** plutôt que dans l'objet **MobileServiceTable**.

### <a name="json_instance"></a>Créer une instance de table non typée

Comme pour le modèle typé, vous commencez par obtenir une référence de table, mais il s'agit dans ce cas d'un objet **MobileServicesJsonTable** . Obtenez la référence en appelant la méthode **getTable** sur une instance du client :

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Une fois que vous avez créé une instance de l’objet **MobileServiceJsonTable**, celui-ci a pratiquement la même API disponible qu’avec le modèle de programmation typé. Dans certains cas, les méthodes utilisent un paramètre non typé au lieu d’un paramètre typé.

### <a name="json_insert"></a>Insérer une table non typée
Le code suivant vous explique comment effectuer une insertion. La première étape consiste à créer un objet [JsonObject][1], qui fait partie de la bibliothèque [gson][3].

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ensuite, utilisez **insert()** pour insérer l’objet non typé dans la table.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Si vous avez besoin d’obtenir l’ID de l’objet inséré, utilisez la méthode **getAsJsonPrimitive()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Supprimer d’une table non typée
Le code qui suit montre comment supprimer une instance, dans ce cas la même instance de l'objet **JsonObject** créé dans l'exemple *insert* précédent. Le code est identique à la casse typée, mais la méthode a une signature différente puisqu’elle fait référence à un objet **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Vous pouvez aussi directement supprimer une instance avec son ID :

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Renvoyer toutes les lignes d’une table non typée
Le code suivant montre comment récupérer toute une table. Étant donné que vous utilisez un tableau JSON, vous pouvez extraire uniquement certaines colonnes de la table, de manière sélective.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Les mêmes méthodes de filtrage et de pagination disponibles pour le modèle typé le sont également pour le modèle non typé.

## <a name="offline-sync"></a>Implémenter la synchronisation hors connexion

Le Kit de développement logiciel (SDK) Azure Mobile Apps Client implémente également la synchronisation hors connexion des données à l’aide d’une base de données SQLite pour stocker une copie des données du serveur en local.  Les opérations effectuées sur une table en mode hors connexion ne nécessitent pas de connectivité mobile pour fonctionner.  La synchronisation hors connexion contribue à la résilience et aux performances au détriment d’une logique plus complexe pour la résolution des conflits.  Le Kit de développement logiciel (SDK) Azure Mobile Apps Client implémente les fonctionnalités suivantes :

* Synchronisation incrémentielle : seuls les enregistrements nouveaux et mis à jour sont téléchargés, ce qui permet d’économiser de la bande passante et de la mémoire.
* Accès concurrentiel optimiste : les opérations sont supposées réussir.  La résolution des conflits est différée jusqu’à ce que des mises à jour soient effectuées sur le serveur.
* Résolution des conflits : le Kit de développement logiciel (SDK) détecte les modifications conflictuelles ayant été apportées au niveau du serveur et fournit des hooks pour avertir l’utilisateur.
* Suppression réversible : les enregistrements supprimés sont marqués comme supprimés, ce qui permet à d’autres appareils de mettre à jour leur cache hors connexion.

### <a name="initialize-offline-sync"></a>Initialiser la synchronisation hors connexion

Chaque table hors connexion doit être définie dans le cache hors connexion avant toute utilisation.  En règle générale, la définition de table s’effectue immédiatement après la création du client :

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Obtenir une référence à la table de cache hors connexion

Pour une table en ligne, vous utilisez `.getTable()`.  Pour une table hors connexion, utilisez `.getSyncTable()` :

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Toutes les méthodes disponibles pour les tables en ligne (y compris le filtrage, le tri, la pagination, l’insertion de données, la mise à jour de données et la suppression de données) fonctionnent de la même façon sur les tables hors connexion.

### <a name="synchronize-the-local-offline-cache"></a>Synchroniser le cache hors connexion local

La synchronisation est sous le contrôle de votre application.  Voici un exemple de méthode de synchronisation :

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Si un nom de requête est fourni à la méthode `.pull(query, queryname)`, la synchronisation incrémentielle est utilisée pour renvoyer uniquement les enregistrements qui ont été créés ou modifiés depuis la dernière extraction complète.

### <a name="handle-conflicts-during-offline-synchronization"></a>Gérer les conflits lors de la synchronisation hors connexion

Si un conflit se produit pendant une opération `.push()`, une exception `MobileServiceConflictException` est levée.   L’élément émis par le serveur est intégré à l’exception et peut être récupéré par `.getItem()` sur l’exception.  Ajustez l’opération push en appelant les éléments suivants sur l’objet MobileServiceSyncContext :

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Une fois tous les conflits marqués comme vous le souhaitez, appelez `.push()` pour résoudre tous les conflits.

## <a name="custom-api"></a>Appeler une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

À partir d’un client Android, vous appelez la méthode **invokeApi** pour appeler le point de terminaison de l’API personnalisée. L’exemple suivant montre comment appeler un point de terminaison d’API nommé **completeAll**, qui retourne une classe de collection nommée **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

La méthode **invokeApi** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles. Les autres versions de la méthode **invokeApi** vous permettent éventuellement d’envoyer un objet dans le corps de la demande, de spécifier la méthode HTTP et d’envoyer des paramètres de requête avec la demande. Des versions non typées de la méthode **invokeApi** sont également fournies.

## <a name="authentication"></a>Ajouter l’authentification à votre application

Les didacticiels décrivent déjà en détail l’ajout de ces fonctionnalités.

App Service prend en charge [l’authentification des utilisateurs d’applications](app-service-mobile-android-get-started-users.md) via divers fournisseurs d’identité externes : Facebook, Google, compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour implémenter des règles d’autorisation dans votre serveur principal.

Deux flux d’authentification sont pris en charge : un flux **serveur** et un flux **client**. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface Web des fournisseurs d’identité.  Aucun SDK supplémentaires n’est requis pour implémenter l’authentification de flux serveur. L’authentification de flux serveur ne fournit pas une intégration approfondie dans l’appareil mobile et elle est uniquement recommandée dans des scénarios de preuve de concept.

Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) proposés par le fournisseur d’identité.  Par exemple, vous pouvez intégrer le SDK Facebook dans votre application mobile.  Le client mobile permute dans l’application Facebook et confirme votre ouverture de session avant de rebasculer vers votre application mobile.

Quatre étapes sont nécessaires pour activer l'authentification dans votre application :

* inscription de votre application pour l’authentification auprès d’un fournisseur d’identité ;
* configuration de votre backend App Service ;
* restriction des autorisations de table aux utilisateurs authentifiés uniquement sur le backend App Service ;
* ajout de code d’authentification à votre application.

Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser le SID d’un utilisateur authentifié pour modifier des demandes.  Pour plus d’informations, consultez la rubrique [Prise en main de l’authentification] et les instructions pour le Kit de développement logiciel (SDK) Server.

### <a name="caching"></a>Authentification : flux serveur

Le code suivant démarre un processus de connexion du flux serveur à l’aide du fournisseur Google.  Une configuration supplémentaire est nécessaire en raison des exigences de sécurité pour le fournisseur Google :

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

En outre, ajoutez la méthode suivante à la classe d’activité principale :

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

L’élément `GOOGLE_LOGIN_REQUEST_CODE` défini dans votre activité principale est utilisé pour la méthode `login()` et dans la méthode `onActivityResult()`.  Vous pouvez choisir n’importe quel nombre unique, tant que le même nombre est utilisé dans les méthodes `login()` et `onActivityResult()`.  Si vous extrayez le code client dans un adaptateur de services (comme indiqué précédemment), vous devez appeler les méthodes appropriées sur l’adaptateur de services.

Vous devez également configurer le projet pour customtabs.  Commencez par spécifier une URL de redirection.  Ajoutez l’extrait de code suivant à `AndroidManifest.xml` :

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Ajoutez **redirectUriScheme** au fichier `build.gradle` de votre application :

```text
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Enfin, ajoutez `com.android.support:customtabs:23.0.1` à la liste des dépendances dans le fichier `build.gradle` :

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Obtenez l’ID de l’utilisateur connecté à partir d’un **MobileServiceUser** à l’aide de la méthode **getUserId**. Pour obtenir un exemple de la manière d’utiliser Futures pour appeler les API de connexion asynchrones, consultez la rubrique [Prise en main de l’authentification].

> [!WARNING]
> Le schéma d’URL mentionné respecte la casse.  Assurez-vous que toutes les occurrences de `{url_scheme_of_you_app}` respectent la casse.

### <a name="caching"></a>Mettre en cache des jetons d’authentification

Pour cela, vous devez stocker localement l'ID utilisateur et le jeton d'authentification sur l'appareil. Au prochain démarrage de l'application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer la procédure d'ouverture de session et rafraîchir le client avec ces données. Mais ces données sont sensibles et elles doivent être stockées sous forme chiffrée au cas où le téléphone serait volé.  Vous pouvez obtenir un exemple complet illustrant la mise en cache des jetons d’authentification dans la section [Mise en cache de jetons d’authentification][7].

Lorsque vous tentez d’utiliser un jeton qui a expiré, vous recevez un message *401 - Connexion non autorisée* . Vous pouvez gérer les erreurs d’authentification à l’aide de filtres.  Les filtres interceptent les requêtes adressées au backend App Service. Le code de filtre teste la réponse pour une erreur 401, déclenche le processus de connexion, puis reprend la demande qui a généré l’erreur 401.

### <a name="refresh"></a>Utiliser des jetons d’actualisation

Le jeton renvoyé par l’authentification et l’autorisation Azure App Service a une durée de vie définie d’une heure.  Passé ce délai, vous devez de nouveau authentifier l’utilisateur.  Si vous utilisez un jeton de longue durée reçu via l’authentification de flux client, vous pouvez procéder à la nouvelle authentification avec l’authentification et l’autorisation Azure App Service à l’aide du même jeton.  Un autre jeton Azure App Service est généré avec une nouvelle durée de vie.

Vous pouvez également inscrire le fournisseur pour qu’il utilise des jetons d’actualisation.  Un jeton d’actualisation n’est pas toujours disponible.  Une configuration supplémentaire est nécessaire :

* Pour **Azure Active Directory**, configurez une clé secrète client pour l’application Azure Active Directory.  Spécifiez la clé secrète client dans Azure App Service lors de la configuration de l’authentification Azure Active Directory.  Lorsque vous appelez `.login()`, transmettez `response_type=code id_token` en tant que paramètre :

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pour **Google**, transmettez `access_type=offline` en tant que paramètre :

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Pour un **compte Microsoft**, sélectionnez la portée `wl.offline_access`.

Pour actualiser un jeton, appelez `.refreshUser()` :

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Nous vous recommandons de créer un filtre qui détecte une réponse 401 à partir du serveur et tente d’actualiser le jeton utilisateur.

## <a name="log-in-with-client-flow-authentication"></a>Se connecter avec l’authentification de flux client

Le processus général de connexion avec l’authentification de flux client se déroule comme suit :

* Configurez l’authentification et l’autorisation Azure App Service comme vous le feriez pour l’authentification de flux client.
* Intégrez le Kit de développement logiciel (SDK) du fournisseur d’authentification pour que l’authentification puisse générer un jeton d’accès.
* Appelez la méthode `.login()` comme suit :

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Remplacez la méthode `onSuccess()` par le code que vous souhaitez utiliser sur une connexion réussie.  La chaîne `{provider}` est un fournisseur valide : **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount** ou **twitter**.  Si vous avez implémenté l’authentification personnalisée, vous pouvez également utiliser la balise de fournisseur d’authentification personnalisée.

### <a name="adal"></a>Authentifier des utilisateurs avec la bibliothèque ADAL (Active Directory Authentication Library)

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. L’utilisation d’une connexion par flux de client est souvent préférable à l’utilisation des méthodes `loginAsync()` , car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le serveur d’applications mobiles back-end pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory][22]. Bien que cette étape soit facultative, veillez à inscrire une application cliente native.
2. Installez la bibliothèque ADAL en modifiant votre fichier build.gradle pour inclure les définitions suivantes :

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Ajoutez le code suivant à votre application, en procédant aux remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez approvisionné votre application. Le format doit être https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du serveur principal de votre application mobile. Vous pouvez obtenir l’ID client sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.
* Remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié depuis l’application cliente native.
* Remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison */.auth/login/done* de votre site, en utilisant le modèle HTTPS. Cette valeur doit être semblable à *https://contoso.azurewebsites.net/.auth/login/done*.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Ajuster la communication client-serveur

La connexion client est normalement une connexion HTTP de base utilisant la bibliothèque HTTP sous-jacente fournie avec le Kit de développement logiciel (SDK) Android.  Vous voulez peut-être la modifier pour plusieurs raisons :

* Vous souhaitez utiliser une autre bibliothèque HTTP pour régler les délais d’expiration.
* Vous souhaitez fournir une barre de progression.
* Vous souhaitez ajouter un en-tête personnalisé pour prendre en charge la fonctionnalité de gestion des API.
* Vous souhaitez intercepter une réponse indiquant un échec pour pouvoir implémenter la procédure de nouvelle authentification.
* Vous souhaitez enregistrer les demandes du serveur principal sur un service analytique.

### <a name="using-an-alternate-http-library"></a>Utilisation d’une autre bibliothèque HTTP

Appelez la méthode `.setAndroidHttpClientFactory()` juste après avoir créé votre référence client.  Par exemple, pour définir le délai d’expiration de connexion sur 60 secondes (au lieu de la valeur par défaut de 10 secondes) :

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implémenter un filtre de progression

Vous pouvez implémenter une interception de chaque demande en implémentant un élément `ServiceFilter`.  Par exemple, le code suivant met à jour une barre de progression créée au préalable :

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    pubic void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Vous pouvez attacher ce filtre au client comme suit :

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Personnaliser des en-têtes de demande

Utilisez l’élément `ServiceFilter` suivant et attachez le filtre comme vous le feriez pour l’élément `ProgressFilter` :

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Configurer la sérialisation automatique

Vous pouvez spécifier une stratégie de conversion qui s’applique à chaque colonne à l’aide de l’API [gson][3]. La bibliothèque cliente Android utilise [gson][3] en arrière-plan pour sérialiser les objets Java en données JSON, qui sont ensuite envoyées à Azure App Service.  Le code suivant utilise la méthode **setFieldNamingStrategy()** pour définir la stratégie. Cet exemple supprimera le premier caractère (un « m »), puis mettra le caractère suivant en minuscule, ce pour tous les noms de champ. Par exemple, il changera « mId » en « id ».  Implémentez une stratégie de conversion pour réduire le besoin d’annotations `SerializedName()` sur la plupart des champs.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStategy)
);
```

Ce code doit être exécuté avant de créer une référence de client mobile à l’aide de **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Prise en main de l’authentification]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
