---

copyright:
  years: 2015, 2018
lastupdated: "2018-05-22"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Utilisation de la réplication

La réplication utilise l'un de vos plannings d'instantané pour copier automatiquement les instantanés vers un volume de destination dans un centre de données distant. Les copies peuvent être récupérées sur le site distant en cas de données endommagées ou de catastrophe.

Les répliques vous permettent :

- d'effectuer une restauration rapide après des échecs sur site et d'autres incidents en basculant vers le volume de destination,
- de basculer vers un point de cohérence spécifique dans la copie de réplication de données.

Avant de pouvoir exécuter une réplication, vous devez créer un planning d'instantané. Lors du basculement, vous "actionnez le commutateur" à partir du volume de stockage de votre centre de données principal vers le volume de destination de votre centre de données distant. Par exemple, votre centre de données principal est Londres et votre centre de données secondaire est Amsterdam. Dans le cas d'un événement d'échec, vous basculez vers Amsterdam, en vous connectant au volume qui est désormais devenu principal à partir d'une instance de calcul à Amsterdam. Une fois que votre volume à Londres est réparé, un instantané est pris du volume à Amsterdam afin d'effectuer une reprise par restauration à Londres et de revenir au volume principal restauré à partir d'une instance de calcul à Londres.


## Comment puis-je déterminer le centre de données distant pour mon volume de stockage répliqué ?

Les centres de données d'{{site.data.keyword.BluSoftlayer_full}} ont été associés par paires sous forme de combinaisons principales et distantes au niveau mondial.
Consultez le Tableau 1 pour obtenir la liste complète de la disponibilité et des cibles de réplication des centres de données. 

<table style="width: 80.0%;">
	<caption style="text-align: left;"><p>Tableau 1 - Ce tableau présente la liste complète des centres de données avec des fonctionnalités améliorées dans chaque région. Chaque région est une colonne distincte. Certaines villes, comme Dallas, San José, Washington, Amsterdam, Francfort, Londres et Sydney possèdent plusieurs centres de données.</p>
		<p>&#42; Les centres de données dans la région US 1 NE disposent PAS d'un stockage amélioré. Les hôtes dans les centres de données avec des fonctionnalités de stockage amélioré <strong>ne peuvent pas</strong> lancer de réplication avec des cibles de réplique dans les centres de données US 1.</p>
</caption>
	<thead>
		<tr>
			<th>US 1 &#42;</th>
			<th>US 2</th>
			<th>Amérique latine</th>
			<th>Canada</th>
			<th>Europe</th>
			<th>Asie-Pacifique</th>
			<th>Australie</t>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>DAL01<br />
				DAL05<br />
				DAL06<br />
				HOU02<br />
				SJC01<br />
				WDC01<br />
				<br />
				<br />
				<br />
				<br />
				<br />
			</td>
			<td>SJC03<br />
			       SJC04<br />
			       WDC04<br />
			       WDC06<br />
			       WDC07<br />
			       DAL09<br />
				DAL10<br />
				DAL12<br />
				DAL13<br />
				<br /><br />
			</td>
			<td>MEX01<br />
				SAO01<br /><br /><br /><br /><br /><br /><br /><br /><br /><br />
			</td>
			<td>TOR01<br />
				MON01<br /><br /><br /><br /><br /><br /><br /><br /><br /><br />
			</td>
			<td>
				AMS01<br />
				AMS03<br />
				FRA02<br />
				FRA04<br />
				FRA05<br />
				LON02<br />
				LON04<br />
				LON06<br />
				OSL01<br />
				PAR01<br />
				MIL01<br />
			</td>
			<td>HKG02<br />
				TOK02<br />
				SNG01<br />
				SEO01<br />
                                CHE01<br />
				<br />
				<br />
				<br />
				<br />
				<br />
				<br />
			</td>
			<td>
				SYD01<br />
				SYD04<br />
				MEL01<br />
				<br /><br /><br /><br /><br /><br /><br /><br />
			</td>
		</tr>
	</tbody>
</table>

## Comment puis-je créer une réplication initiale ?

Les réplications fonctionnent selon un planning d'instantané. Un espace d'instantané et un planning d'instantané doivent d'abord être définis pour le volume source avant que la réplication puisse avoir lieu. Vous recevrez des invites vous informant que vous devez acheter de l'espace ou qu'un planning doit être défini, si vous essayez de configurer la réplication et que l'une ou l'autre de ces conditions n'est pas remplie au préalable. Les réplications sont gérées sous **Stockage**, **{{site.data.keyword.blockstorageshort}}** dans le portail [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}.

1. Cliquez sur votre volume de stockage.
2. Cliquez sur **Réplique**, puis sur **Acheter une réplication**.
Sélectionnez le planning d'instantané existant que vous souhaitez que votre réplication suive. La liste contient tous vos plannings d'instantané actifs. <br />
  **Remarque :** Vous ne pouvez sélectionner qu'un planning, même si vous disposez d'un mélange d'heures, de jours et de semaines. Tous les instantanés capturés depuis le dernier cycle de réplication seront répliqués, quel que soit leur planning d'origine.<br />
  **Remarque :** Si aucun instantané n'est configuré, vous serez invité à effectuer cette opération avant de commander une réplication. Pour plus de détails, voir [Utilisation d'instantanés](snapshots.html).
3. Cliquez sur la flèche déroulante **Emplacement** et sélectionnez le centre de données qui sera votre site de réplication des données.
4. Cliquez sur **Continuer**.
5. Saisissez un **code promotionnel** le cas échéant, puis cliquez sur **Recalculer**. Les autres zones de la boîte de dialogue contiennent les valeurs par défaut.
6. Cochez la case **J'ai lu et j'accepte l'intégralité du Contrat cadre de service...** et cliquez sur **Valider la commande**.


## Comment puis-je éditer une réplication existante ?

Vous pouvez éditer votre planning de réplication et modifier votre espace de réplication à partir de l'onglet **Principal** ou **Réplique** sous **Stockage**, **{{site.data.keyword.blockstorageshort}}** dans le portail [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}.



## Comment puis-je éditer un planning de réplication ?

En fait, vous modifiez un planning d'instantané car votre planning de réplication se fonde sur un planning d'instantané existant. Pour modifier le planning de réplication, par exemple d'Horaire en Hebdomadaire, vous devez annuler le planning de réplication et en configurer un nouveau.

La modification du planning peut s'effectuer sur l'onglet Principal ou Réplique.

1. Cliquez sur **Actions** à partir de l'onglet **Principal** ou **Réplique**.
2. Sélectionnez **Modifier le planning d'instantané**.
3. Regardez dans le cadre **Instantané** sous **Planning** pour déterminer le planning que vous utilisez pour la réplication. Modifiez le planning utilisé pour la réplication. Par exemple, si votre planning de réplication est **Quotidien**, vous pouvez modifier l'heure de la journée à laquelle la réplication doit avoir lieu.
4. Cliquez sur **Enregistrer**.


## Comment puis-je modifier l'espace de réplication ?

Votre espace d'instantané principal et votre espace de réplication doivent être identiques. Si vous modifiez l'espace sur l'onglet **Principal** ou **Réplique**, un espace est automatiquement ajouté à vos centres de données source et cible. N'oubliez pas que l'augmentation de l'espace d'instantané déclenchera une mise à jour immédiate de la réplication.

1. Cliquez sur **Actions** à partir de l'onglet **Principal** ou **Réplique**.
2. Sélectionnez **Ajouter de l'espace d'instantané supplémentaire**.
3. Sélectionnez la taille de stockage dans la liste et cliquez sur **Continuer**.
4. Saisissez un **code promotionnel** le cas échéant, puis cliquez sur **Recalculer**. Les autres zones de la boîte de dialogue contiennent les valeurs par défaut.
5. Cochez la case **J'ai lu et j'accepte l'intégralité du Contrat cadre de service...** et cliquez sur **Valider la commande**.


## Comment puis-je voir mes volumes de réplique dans la liste de volumes ?

Vous pouvez afficher vos volumes de réplication sur la page {{site.data.keyword.blockstorageshort}} sous **Stockage > {{site.data.keyword.blockstorageshort}}**. Le **Nom LUN** contient le nom du volume principal suivi de REP. Le **Type** est Endurance ou Performance – Réplique. L'**Adresse cible** est Sans objet car le volume de réplique n'est pas monté sur le centre de données de réplique, et le **Statut** indique Inactif.



## Comment puis-je afficher les détails d'un volume répliqué sur le centre de données de réplique ?

Vous pouvez afficher les détails du volume de réplique sur l'onglet **Réplique** sous **Stockage**, **{{site.data.keyword.blockstorageshort}}**. Une autre option consiste à sélectionner le volume de réplique à partir de la page **{{site.data.keyword.blockstorageshort}}** et à cliquer sur l'onglet **Réplique**.



## Comment puis-je spécifier les autorisations de l'hôte avant de basculer vers le centre de données secondaire ?

Les hôtes autorisés et les volumes doivent se trouver dans le même centre de données. Vous ne pouvez pas avoir un volume de réplique à Londres et l'hôte à Amsterdam ; ils doivent se trouver tous les deux à Londres ou à Amsterdam.

1. Cliquez sur votre volume source ou cible à partir de la page **{{site.data.keyword.blockstorageshort}}**.
2. Cliquez sur **Réplique**.
3. Faites défiler l'écran vers le bas jusqu'au cadre **Autoriser les hôtes** et cliquez sur **Autoriser les hôtes** à droite. 
4. Mettez en évidence l'hôte qui doit être autorisé pour les réplications. Pour sélectionner plusieurs hôtes, utilisez la touche CTRL et cliquez sur les hôtes applicables.
5. Cliquez sur **Soumettre**. Si vous n'avez pas d'hôtes, la boîte de dialogue vous donne la possibilité d'acheter des ressources de calcul dans le même centre de données. 


## Comment puis-je accroître mon espace d'instantané dans mon centre de données de réplique lorsque j'augmente l'espace dans mon centre de données principal ?

Les tailles des volumes de stockage principal et de réplique doivent être identiques. Il n'est pas possible que l'un soit plus grand que l'autre. Lorsque vous augmentez l'espace d'instantané pour votre volume principal, l'espace de réplique augmente automatiquement. N'oubliez pas que l'augmentation de l'espace d'instantané déclenchera une mise à jour immédiate de la réplication. L'augmentation des deux volumes s'affichera sous forme de lignes d'article sur votre facture et sera calculée au prorata, si nécessaire.

Cliquez [ici](snapshots.html) pour savoir comment augmenter votre espace d'instantané.



## Comment puis-je lancer un basculement à partir d'un volume vers sa réplique ?

Dans le cas d'un événement d'échec, vous pouvez lancer un **basculement** vers votre volume cible. Le volume cible devient actif. Le dernier instantané répliqué avec succès est activé et le volume est alors disponible pour le montage. Toutes les données écrites sur le volume source depuis le dernier cycle de réplication seront perdues. N'oubliez pas que lorsqu'un basculement a démarré, la relation de réplication est actionnée. Votre volume cible devient votre volume source, et le volume source précédent devient votre cible, comme indiqué par le **Nom LUN** suivi de **REP**.

Les basculements sont lancés sous **Stockage**, **{{site.data.keyword.blockstorageshort}}** dans le portail [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}.

**Avant d'exécuter cette procédure, il est recommandé de déconnecter le volume. Sinon, vous risquez une altération et une perte des données.**

1. Cliquez sur votre numéro d'unité logique actif ("source").
2. Cliquez sur **Réplique**, puis sur le lien **Actions** dans l'angle supérieur droit.
3. Sélectionnez Basculement.
   Vous devez voir s'afficher un message dans la partie supérieure de la page, indiquant que le basculement est en cours. En outre, une icône apparaît en regard de votre volume sur le service **{{site.data.keyword.blockstorageshort}}**, indiquant qu'une transaction est active. Survolez l'icône pour afficher une boîte de dialogue présentant la transaction. L'icône disparaîtra une fois la transaction terminée. Pendant le processus de basculement, les actions liées à la configuration sont en lecture seule. Vous ne pouvez pas modifier de planning d'instantané, d'espace d'instantané, etc. L'événement est consigné dans l'historique des réplications.
   Un autre message vous informe lorsque votre volume cible est opérationnel. Le nom LUN de votre volume source d'origine sera suivi de REP et son statut sera Inactif.
4. Cliquez sur **Tout afficher ({{site.data.keyword.blockstorageshort}})**.
5. Cliquez sur votre numéro d'unité logique actif (anciennement votre volume cible). Ce volume a désormais le statut **Actif**.
6. Montez et connectez votre volume de stockage à l'hôte. Cliquez [ici](provisioning-block_storage.html) pour obtenir des instructions.


## Comment puis-je lancer une reprise par restauration à partir d'un volume vers sa réplique ?

Lorsque votre volume source d'origine a été réparé, vous pouvez lancer une reprise par restauration contrôlée vers votre volume source d'origine. Dans une reprise par restauration contrôlée,

- le volume source actif est mis hors ligne ;
- un instantané est pris ;
- le cycle de réplication prend fin ;
- l'instantané de données qui vient d'être pris est activé ;
- et le volume source est activé pour le montage.

N'oubliez pas que lorsqu'une reprise par restauration a démarré, la relation de réplication est à nouveau actionnée. Votre volume source est restauré en tant que volume source, et votre volume cible redevient le volume cible, comme indiqué par le **Nom LUN** suivi de **REP**.

Les reprises par restauration sont lancées sous **Stockage**, **{{site.data.keyword.blockstorageshort}}** dans le portail [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}.

1. Cliquez sur votre numéro d'unité logique Endurance actif ("cible").
2. Cliquez sur **Réplique**, puis sur **Actions** dans l'angle supérieur droit.
3. Sélectionnez **Reprise par restauration**. Vous devez voir s'afficher un message dans la partie supérieure de la page, indiquant le basculement est en cours. En outre, une icône apparaît en regard de votre volume sur le service **{{site.data.keyword.blockstorageshort}}**, indiquant qu'une transaction est active. Survolez l'icône pour afficher une boîte de dialogue présentant la transaction. L'icône disparaît une fois la transaction terminée. Pendant le processus de basculement, les actions liées à la configuration sont en lecture seule. Vous ne pouvez pas modifier de planning d'instantané, d'espace d'instantané, etc. L'événement est consigné dans l'historique des réplications. Un autre message vous informe lorsque votre volume source est opérationnel. Votre volume cible a le statut Inactif.
4. Dans l'angle supérieur droit, cliquez sur le lien **Afficher tous les stockages par blocs**.
5. Cliquez sur votre numéro d'unité logique Endurance actif (source). Ce volume a le statut **Actif**.
6. Montez et connectez votre volume de stockage à l'hôte. Cliquez [ici](provisioning-block_storage.html) pour obtenir des instructions.


## Comment puis-je afficher l'historique de mes réplications ?

L'historique des réplications s'affiche dans le **Journal d'audit** sur l'onglet **Compte** sous **Gérer**. Les volumes principal et de réplique affichent un historique des réplications identique, qui inclut les éléments suivants :

- Type de réplication (basculement ou reprise par restauration)
- Date/heure de début
- Instantané utilisé pour la réplication
- Taille de la réplication
- Date/heure de fin


## Comment puis-je annuler une réplication existante ?

Vous pouvez annuler la réplication immédiatement ou à la date anniversaire, ce qui met fin à la facturation. La réplication peut être annulée à partir de l'onglet **Principal** ou **Réplique**.

1. Cliquez sur le volume dans la page **{{site.data.keyword.blockstorageshort}}**.
2. Cliquez sur **Actions** à partir de l'onglet **Principal** ou **Réplique**.
3. Sélectionnez **Annuler la réplique**.
4. Sélectionnez le moment choisi pour l'annulation, à savoir **Immédiatement** ou **Date anniversaire**, puis cliquez sur **Continuer**.
5. Cochez la case **Je comprends les risques de perte de données liés à cette annulation** et cliquez sur **Annuler la réplique**.


## Comment puis-je annuler la réplication lorsque le volume principal est annulé ?

Lorsqu'un volume principal est annulé, le planning de réplication et le volume dans le centre de données de réplique sont supprimés. Les répliques sont annulées à partir de la page {{site.data.keyword.blockstorageshort}}.

 1. Mettez en évidence votre volume sur la page **{{site.data.keyword.blockstorageshort}}**.
 2. Cliquez sur **Actions** et sélectionnez **Annuler stockage par bloc**.
 3. Sélectionnez le moment choisi pour l'annulation, à savoir **Immédiatement** ou **Date anniversaire**, puis cliquez sur **Continuer**.
 4. Cochez la case **Je comprends les risques de perte de données liés à cette annulation** et cliquez sur **Annuler**.
