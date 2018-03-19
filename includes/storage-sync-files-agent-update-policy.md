L’agent Azure File Sync est mis à jour régulièrement pour ajouter de nouvelles fonctionnalités et pour résoudre les problèmes. Nous vous recommandons de configurer Microsoft Update pour obtenir les mises à jour de l’agent Azure File Sync lorsqu’elles sont disponibles. Nous sommes conscients que certaines organisations souhaitent contrôler et tester les mises à jour de manière stricte.

Pour les déploiements utilisant des versions antérieures de l’agent Azure File Sync :

- Le service de synchronisation de stockage autorise la version principale précédente pendant trois mois après la publication initiale de la nouvelle version principale. Par exemple, le service de synchronisation de stockage prend en charge la version 1.\* pendant trois mois après la publication de la version 2\*.
- Au bout de ces trois mois, le service de synchronisation de stockage ne synchronise plus les serveurs inscrits utilisant la version expirée avec leurs groupes de synchronisation.
- Pendant les trois mois d’utilisation de la version principale précédente, seule la version principale actuelle (et nouvelle) reçoit les correctifs de bogues.

> [!Note]  
> Si votre version d’Azure File Sync expire pendant ces trois mois, vous êtes notifiés via une notification toast dans le portail Azure.
