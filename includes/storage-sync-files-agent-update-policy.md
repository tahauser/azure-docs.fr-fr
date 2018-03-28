L’agent Azure File Sync est mis à jour régulièrement pour ajouter de nouvelles fonctionnalités et pour résoudre les problèmes. Nous vous recommandons de configurer Microsoft Update pour obtenir les mises à jour de l’agent Azure File Sync lorsqu’elles sont disponibles.

#### <a name="major-vs-minor-agent-versions"></a>Versions d’agent majeures et mineures
* Les versions majeures de l’agent contiennent souvent de nouvelles fonctionnalités, et la première partie du numéro de version est constituée d’un nombre croissant. Par exemple, *2.\*.\**
* Les versions mineures de l’agent sont également appelées « correctifs », et sont publiées plus fréquemment que les versions majeures. Elles contiennent souvent des correctifs de bogues et des améliorations mineures, mais pas de nouvelles fonctionnalités. Par exemple, *\*.3.\**

#### <a name="upgrade-paths"></a>Chemins de mise à jour
Il existe trois méthodes testées et approuvées pour installer les mises à jour de l’agent Azure File Sync. Ces chemins de mise à jour fonctionnent pour les versions majeures et mineures.
1. **(Recommandé) Configurez Microsoft Update pour télécharger et installer automatiquement les mises à jour de l’agent.**  
    Il est recommandé d’installer toutes les mises à jour Azure File Sync pour bénéficier des derniers correctifs de l’agent du serveur. Microsoft Update rend ce processus transparent, en téléchargeant et en installant automatiquement les mises à jour.
2. **Appliquez un correctif à un agent Azure File Sync existant à l’aide d’un fichier de correctif Microsoft Update, ou d’un exécutable .msp. Le dernier package de mise à jour Azure File Sync peut être téléchargé à partir du [catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    L’exécution d’un exécutable .msp permet de mettre à jour votre installation d’Azure File Sync avec la même méthode que celle utilisée automatiquement par Microsoft Update dans le chemin de mise à jour précédent. L’application d’un correctif Microsoft Update effectue la mise à jour sur place d’une installation Azure File Sync.
3. **Téléchargez le programme d’installation de l’agent Azure File Sync le plus récent à partir du [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Le téléchargement du programme d’installation est un package Microsoft Installer ou un fichier exécutable .msi.**  
    Pour mettre à niveau une installation existante de l’agent Azure File Sync, désinstallez l’ancienne version, puis installez la version la plus récente à partir du programme d’installation téléchargé. L’inscription du serveur, les groupes de synchronisation et tous les autres paramètres sont gérés par le programme d’installation d’Azure File Sync.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Cycle de vie de l’agent et garanties liées à la gestion des changements
Azure File Sync est un service cloud qui permet l’introduction continue de nouvelles fonctionnalités. Cela signifie qu’une version de l’agent Azure File Sync n’est prise en charge que pour une durée limitée. Pour faciliter le déploiement, appliquez les règles suivantes pour être sûr de disposer de suffisamment de temps et d’informations pour les mises à jour ou mises à niveau lors de la gestion des modifications :

- Les versions majeures et mineures de l’agent sont prises en charge au moins six mois après leur publication initiale.
- Nous garantissons une période de chevauchement de trois mois minimum entre chaque version majeure d’agent. 
- Des avertissements sont émis pour les serveurs inscrits qui utilisent un agent sur le point d’expirer au moins trois mois avant son expiration. Vous pouvez vérifier si un serveur inscrit utilise une version antérieure de l’agent dans la section relative aux serveurs inscrits d’un service de synchronisation de stockage.
- La durée de vie d’une version mineure de l’agent est liée à la version majeure à laquelle elle est associée. Par exemple, lorsque la version 3.0 de l’agent est publiée, les versions 2.\* sont toutes définies pour expirer ensemble.

> [!Note]
> Si vous installez une version de l’agent sur le point d’expirer, un avertissement d’expiration s’affiche, mais ne vous empêche pas de l’installer. En revanche, l’installation et la connexion à une version de l’agent ayant expiré ne sont pas prises en charge et seront bloquées.