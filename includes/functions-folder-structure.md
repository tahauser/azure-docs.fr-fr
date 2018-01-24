
Le code de toutes les fonctions dans une application de fonction spécifique se trouve dans un dossier racine qui contient un fichier de configuration d’hôte, et un ou plusieurs sous-dossiers. Chaque sous-dossier contient le code d’une fonction distincte, comme dans l’exemple suivant :

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Le fichier host.json contient des configurations spécifiques du runtime et se trouve dans le dossier racine de l’application de fonction. Pour plus d’informations sur les paramètres disponibles, consultez les [informations de référence sur le fichier host.json](../articles/azure-functions/functions-host-json.md).

Chaque fonction a un dossier contenant un ou plusieurs fichiers de code, la configuration de function.json et d’autres dépendances.

