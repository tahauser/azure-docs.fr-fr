## <a name="test"></a>Tester la fonction

Utilisez cURL pour tester la fonction déployée sur un ordinateur Mac ou Linux ou un interpréteur de commandes (Bash) sur Windows. Exécutez la commande cURL suivante, en remplaçant l’espace réservé `<app_name>` par le nom de votre Function App. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Si vous n’avez pas de cURL disponible dans votre ligne de commande, entrez la même URL dans la barre d’adresse de votre navigateur web. Remplacez à nouveau l’`<app_name>` espace réservé par le nom de votre application de fonction, et ajoutez la chaîne de requête `&name=<yourname>` à l’URL avant d’exécuter la requête. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Réponse de la fonction affichée dans un navigateur.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
