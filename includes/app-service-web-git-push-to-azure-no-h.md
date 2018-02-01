Dans la fenêtre du terminal local, ajoutez un référentiel distant Azure dans votre référentiel Git local. Remplacez _&lt;paste\_copied\_url\_here>_ par l’URL du Git distant que vous avez enregistrée à la section [Créer une app web](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Lorsque vous êtes invité à saisir un mot de passe, veillez à utiliser celui que vous avez créé dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user), et non pas celui vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :
