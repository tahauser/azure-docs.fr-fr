## <a name="rest"></a>Déployer un fichier ZIP avec l’API REST 

Vous pouvez utiliser les [API REST du service de déploiement](https://github.com/projectkudu/kudu/wiki/REST-API) pour déployer le fichier .zip sur votre application dans Azure. Pour déployer, envoyez une demande POST à https://<nom_application>.scm.azurewebsites.net/api/zipdeploy. La demande POST doit contenir le fichier .zip dans le corps du message. Les informations d’identification de déploiement pour votre application sont fournies dans la demande avec l’authentification de base HTTP. Pour plus d’informations, consultez les [informations de référence des envois (push) de fichiers .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Pour l’authentification HTTP BASIC, vous avez besoin de vos informations d’identification de déploiement App Service. Pour découvrir comment définir les informations d’identification de votre déploiement, consultez [Définir et réinitialiser les informations d’identification de niveau utilisateur](../articles/app-service/app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>Avec cURL

L’exemple suivant utilise l’outil cURL pour déployer un fichier .zip. Remplacez les espaces réservés `<username>`, `<password>`, `<zip_file_path>` et `<app_name>`. Quand vous y êtes invité par cURL, tapez le mot de passe.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Cette demande déclenche le déploiement par envoi (push) à partir du fichier .zip chargé. Vous pouvez examiner les déploiements en cours et passés en utilisant le point de terminaison https://<nom_application>.scm.azurewebsites.net/api/deployments, comme le montre l’exemple cURL suivant. Ici encore, remplacez `<app_name>` par le nom de votre application et `<deployment_user>` par le nom d’utilisateur de vos informations d’identification de déploiement.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Avec PowerShell

L’exemple suivant utilise [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) pour envoyer une demande qui contient le fichier .zip. Remplacez les espaces réservés `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` et `<app_name>`.

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

Cette demande déclenche le déploiement par envoi (push) à partir du fichier .zip chargé. Pour passer en revue les déploiements en cours et passés, exécutez les commandes suivantes. Là encore, remplacez l’espace réservé `<app_name>`.

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
