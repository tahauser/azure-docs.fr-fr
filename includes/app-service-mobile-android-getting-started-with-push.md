1. Dans votre projet **d’application**, ouvrez le fichier `AndroidManifest.xml`. Ajoutez le code suivant après la balise de début `application` :

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Ouvrez le fichier `ToDoActivity.java`, puis apportez les modifications suivantes :

    - Ajoutez l’instruction d’importation :

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Modifiez la définition de `MobileServiceClient` en remplaçant **private** par **private static**, afin qu’elle ressemble à ceci :

        ```java
        private static MobileServiceClient mClient;
        ```

    - Ajoutez la méthode `registerPush` :

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Mettez à jour la méthode **onCreate** de la classe `ToDoActivity`. Veillez à ajouter ce code après l’instanciation de `MobileServiceClient`.

        ```java
        registerPush();
        ```

3. Ajoutez une nouvelle classe pour gérer les notifications. Dans l’Explorateur de projets, ouvrez les nœuds **app** > **java** > **your-project-namespace**, puis cliquez avec le bouton droit sur le nœud de nom de package. Cliquez sur **Nouveau**, puis sur **Classe Java**. Dans Nom, entrez `ToDoMessagingService`, puis cliquez sur OK. Remplacez ensuite la déclaration de classe par :

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Ajoutez une autre classe pour gérer les mises à jour de jeton. Créez une classe Java `ToDoInstanceIdService` et remplacez la déclaration de classe par :

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

L'application est mise à jour et prend en charge les notifications Push.
