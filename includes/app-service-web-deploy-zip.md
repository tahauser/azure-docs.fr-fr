## <a name="deploy-uploaded-zip-file"></a>Déployer le fichier ZIP chargé

Dans l’interface Cloud Shell, déployez le fichier ZIP chargé dans votre application web à l’aide de la commande [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Assurez-vous de remplacer *\<app_name>* par le nom de votre application web.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Cette commande déploie les fichiers et répertoires du fichier ZIP vers votre dossier d’applications App Service par défaut (`\home\site\wwwroot`), puis redémarre l’application. Si un processus de génération personnalisé supplémentaire est configuré, il est également exécuté.
