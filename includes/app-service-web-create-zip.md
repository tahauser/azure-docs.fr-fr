## <a name="create-a-project-zip-file"></a>Créer un fichier de projet ZIP

Créez une archive ZIP de l’ensemble des contenus de votre projet. La commande suivante utilise l’outil par défaut dans votre terminal :

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Par la suite, vous chargez ce fichier ZIP dans Azure et le déployer dans App Service.