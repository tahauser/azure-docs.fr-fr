<!--author=alkohli last changed: 01/23/18-->

Si le conteneur de volumes a des volumes associés, commencez par mettre ces volumes hors connexion. Suivez les étapes de la [Mise hors connexion d’un volume](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Une fois les volumes hors connexion, vous pouvez les supprimer. Lorsque le conteneur de volumes n’est associé à aucun volume, supprimez-le. Pour supprimer un conteneur de volumes, procédez comme suit.

#### <a name="to-delete-a-volume-container"></a>Pour supprimer un conteneur de volumes
1. Accédez à votre service StorSimple Device Manager et cliquez sur **Appareils**. Sélectionnez l’appareil et cliquez dessus, puis accédez à **Paramètres > Gérer > Conteneurs de volumes**.

    ![Panneau Conteneurs de volumes](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Dans la liste tabulaire des conteneurs de volumes, sélectionnez le conteneur de volumes à supprimer, cliquez avec le bouton droit sur **...**, puis sélectionnez **Supprimer**.

    ![Supprimer un conteneur de volumes](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Si un conteneur de volumes n’est associé à aucun volume, il peut être supprimé. Lorsque vous êtes invité à confirmer l’opération, vérifiez et cochez l’option indiquant l’impact de la suppression du conteneur de volumes. Cliquez sur **Supprimer**, puis supprimez le conteneur de volumes.

    ![Confirmer la suppression](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

La liste des conteneurs de volumes est mise à jour pour refléter la suppression du conteneur de volumes.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


