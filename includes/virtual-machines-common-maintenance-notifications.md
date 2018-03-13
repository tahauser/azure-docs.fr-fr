
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Afficher les machines virtuelles sur lesquelles une maintenance est planifiée dans le portail

Après la planification d’une vague d’opérations de maintenance et l’envoi de notifications, vous pouvez observer la liste des machines virtuelles impactées par la maintenance à venir. 

Vous pouvez utiliser le portail Azure et rechercher les machines virtuelles sur lesquelles une maintenance est planifiée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans la barre de navigation de gauche, cliquez sur **Machines virtuelles**.

3. Dans le volet Machines virtuelles, cliquez sur le bouton **Colonnes** pour ouvrir la liste des colonnes disponibles.

4. Sélectionnez et ajoutez les colonnes suivantes :

   **Maintenance** : affiche l’état de maintenance de la machine virtuelle. Les valeurs potentielles sont les suivantes :
      
      | Valeur | Description |
      |-------|-------------|
      | Démarrer maintenant | La machine virtuelle étant dans la fenêtre de maintenance libre-service, vous pouvez lancer la maintenance vous-même. Consultez la procédure ci-dessous pour démarrer la maintenance sur votre machine virtuelle. | 
      | Planifiée | Une maintenance est planifiée sur la machine virtuelle, mais aucune option de lancement ne vous est proposée. Pour déterminer la fenêtre de maintenance, sélectionnez la fenêtre Planifiée automatiquement dans cet affichage ou cliquez sur la machine virtuelle. | 
      | Completed | Vous avez lancé et terminé la maintenance sur votre machine virtuelle. | 
      | Ignoré| Vous avez choisi de lancer la maintenance, mais sans succès. Vous ne pourrez pas utiliser l’option de maintenance libre-service. Votre machine virtuelle devra être redémarrée par Azure pendant la phase de maintenance planifiée. | 

   **Maintenance - Proactive** : affiche la fenêtre de temps dans laquelle vous pouvez démarrer vous-même la maintenance de vos machines virtuelles.
   
   **Maintenance planifiée** : affiche la fenêtre de temps dans laquelle Azure redémarre votre machine virtuelle pour terminer la maintenance. 




## <a name="notification-and-alerts-in-the-portal"></a>Notification et alertes dans le portail

Azure communique une planification de maintenance planifiée en envoyant un e-mail au propriétaire et au groupe de copropriétaires de l’abonnement. Vous pouvez ajouter des destinataires et des chaînes à cette communication en créant des alertes de journal d’activité Azure. Pour plus d’informations, consultez [Surveiller l’activité d’abonnement avec le journal d’activité Azure] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Surveiller**. 
3. Dans le volet **Surveiller - Journal d’activité**, sélectionnez **Alertes**.
4. Dans le volet **Surveiller - Alertes**, cliquez sur **+ Ajouter une alerte de journal d’activité**.
5. Renseignez la page **Ajouter une alerte de journal d’activité** et veillez à définir les éléments suivants dans **Critères** : **Type** : Maintenance, **État** : Tout les (ne choisissez pas l’état Actif ou Résolu), **Niveau** : Tout.
    
Pour découvrir plus en détail comment configurer des alertes de journal d’activité, consultez [Créer des alertes de journal d’activité](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Démarrer la maintenance sur votre machine virtuelle à partir du portail

L’affichage détaillé de la machine virtuelle contient davantage d’informations relatives à la maintenance.  
En haut de l’affichage détaillé, un nouveau ruban de notification est ajouté si votre machine virtuelle fait l’objet d’une vague d’opérations de maintenance planifiées. En outre, une nouvelle option permet de démarrer la maintenance dès que possible. 


Cliquez sur la notification de maintenance pour afficher la page de maintenance avec plus de détails sur la maintenance planifiée. À partir de là, vous pouvez **démarrer la maintenance** sur votre machine virtuelle.

Une fois la maintenance démarrée, votre machine virtuelle est redémarrée, puis après quelques minutes, l’état de la maintenance est mis à jour pour refléter le résultat.

Si vous avez raté la fenêtre de démarrage de la maintenance, vous pouvez toujours afficher la fenêtre quand Azure redémarre votre machine virtuelle. 
