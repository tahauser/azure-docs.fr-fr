Lorsque l’hôte Functions s’exécute localement, il écrit des journaux dans le chemin d’accès suivant :

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Sous Windows, `<DefaultTempDirectory>` est la première valeur trouvée pour les variables d’environnement TMP, TEMP, USERPROFILE ou le répertoire Windows.
Sur Mac OS ou Linux, `<DefaultTempDirectory>` est la variable d’environnement TMPDIR.

[!Note]
Lorsque l’hôte Functions démarre, il remplace la structure de fichiers existants dans le répertoire.