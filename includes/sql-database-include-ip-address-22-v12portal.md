
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Dans la liste à gauche, sélectionnez **Parcourir**. 

3. Faites défiler l’écran, puis sélectionnez **Serveurs SQL**. 
   
    ![Rechercher votre serveur Azure SQL Database sur le portail][b21-FindServerInPortal]
4. Si vous trouvez cela plus pratique, vous pouvez réduire le panneau **Parcourir**.

5. Dans la zone de texte de filtre, tapez les premières lettres du nom de votre serveur. La ligne correspondante s’affiche.

6. Sélectionnez la ligne de votre serveur. Un panneau dédié à votre serveur s’affiche.

7. Dans ce panneau, sélectionnez **Paramètres**. 

8. Sélectionnez **Pare-feu**. 
   
    ![Sélectionnez Paramètres, puis Pare-feu][b31-SettingsFirewallNavig]
9. Sélectionnez **Ajouter une adresse IP cliente**. Dans la première zone de texte, tapez un nom pour votre nouvelle règle.

10. Tapez les valeurs d’adresse IP basse et haute de la plage que vous souhaitez autoriser.
    
    * Pour des raisons pratiques, vous pouvez terminer les valeurs basses par **.0** et les valeurs hautes par **.255**.
    
    ![Ajouter une plage d’adresses IP à autoriser][b41-AddRange]
11. Sélectionnez **Enregistrer**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
