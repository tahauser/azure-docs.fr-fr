## <a name="rest"></a>Déployer en utilisant des API REST 
 
Vous pouvez utiliser les [API REST du service de déploiement](https://github.com/projectkudu/kudu/wiki/REST-API) pour déployer le fichier .zip sur votre application dans Azure. Envoyez simplement une demande POST à https://<nom_application>.scm.azurewebsites.net/api/zipdeploy. La demande POST doit contenir le fichier .zip dans le corps du message. Les informations d’identification de déploiement pour votre application sont fournies dans la demande avec l’authentification de base HTTP. Pour plus d’informations, consultez les [informations de référence des envois (push) de fichiers .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

L’exemple suivant utilise l’outil cURL pour envoyer une demande qui contient le fichier .zip. Vous pouvez exécuter cURL à partir du terminal sur un ordinateur Mac ou Linux, ou en utilisant Bash sur Windows. Remplacez l’espace réservé `<zip_file_path>` par le chemin vers l’emplacement du fichier .zip de votre projet. Remplacez également `<app_name>` par le nom unique de votre application.

Remplacez l’espace réservé `<deployment_user>` par le nom d’utilisateur de vos informations d’identification de déploiement. Quand vous y êtes invité par cURL, tapez le mot de passe. Pour découvrir comment définir les informations d’identification de déploiement pour votre application, consultez [Définir et réinitialiser les informations d’identification de niveau utilisateur](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Cette demande déclenche le déploiement par envoi (push) à partir du fichier .zip chargé. Vous pouvez examiner les déploiements en cours et passés en utilisant le point de terminaison https://<nom_application>.scm.azurewebsites.net/api/deployments, comme le montre l’exemple cURL suivant. Ici encore, remplacez `<app_name>` par le nom de votre application et `<deployment_user>` par le nom d’utilisateur de vos informations d’identification de déploiement.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```