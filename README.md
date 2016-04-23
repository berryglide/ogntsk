# ogntsk

##Résumé
Tasks file for live @ OGN (live.glidernet.org)

Repository destiné à recevoir les fichiers pour customiser le live OGN pour le terrain d'Issoudun (LFEK) via le lien du type :

http://live.glidernet.org/#c=46.8883333,2.04&z=9&o=1&b=48.5,45.0,0.20,3.5&l=a&w=0&p=2&t=http://[github_task_file_url]

où le paramètre
- c=46.8883333,2.04 indique les coordonnées LFEK
- t=http://[github_task_file_url] indique l'url du fichier décrivant l'épreuve du jour

Pour les autres paramètres, Cf. https://github.com/glidernet/ogn-live#task-file-format

## Procédure détaillée

Exemple de circuit : AAT 002Start Issoudun - 011Amboise aero R=25Km - 019Aubigny aero R=15Km - 005Finish18

Résultats à obtenir :
un fichier texte (extension ".txt") contenant les informations du circuit et une liste d'identifiant Flarm à filtrer, au format JSON comme ci-dessous, accessible via une URL sur un serveur implémenant CORS.

{"tasks":
	[
		{	"name":"epreuve 1",
			"color":"FFFFFF",
			"legs":[[46.95,2.007],[5000],[47.34,0.94],[25000],[47.48,2.39],[15000],[46.89,2.04],[1000]],
			"wlist":["DD504B","DD838A","DD98E0","DDAB3A","DDD42F","DDD445","DDD44D","DDD87E","DDD883","DDD88A","DDD89A","DDD89F"]
		}
	]
}


###Etapes :

####1 Creer le fichier ".txt"
Repartir d'un fichier existant en le copiant et passer à l'étape 2.
Ou bien ouvrir un éditeur de texte (notepad++ ou autre), puis créer un fichier (.txt) avec un nom correspondant à l'épreuve du jour et copier-coller l'exemple ci-dessus.


####2 Définir le circuit
Modifier les informations en fonction de l'épreuve du jour et en respectant le format JSON (http://www.json.org/) :
* name : le nom du circuit, par exemple n° de l'épreuve.
* legs : tableau des coordonnées des point de virages et des zones d'observations pour chaque point, y-compris départ et arrivée, on a 2 éléments entre crochets :
  * [lat,long] : coordonnées en décimal du point de virage, se reporter au fichier "ACI2016_turnpoints_decimal.csv" pour trouver les coordonnées en décimal.
  * [rayon] : rayon de la zone d'observation en mètres (en cas d'AST utiliser 500)
Les éléments entre crochets sont séparés par des virgules ","
Ajouter autant d'élements que nécessaire.
L'ensemble des éléments est lui-même entre crochets.
* wlist : liste des identifiants FLARM auxquels on veut restreindre la visualisation. Cette liste est optionnelle.
  * Si présente, seuls les Flarms identifiés dans la liste seront affichés.
  * Si absente, tous les Flarm "visibles" seront affichés.
  * Cf. plus bas pour obtenir la liste des identifiants Flarm.


####3 Uploader le fichier sur un serveur
Le fichier doit pourvoir être téléchargé par la page OGN.
Il faut donc le téléchager sur un serveur. Et il faut également que ce serveur implémente la spécification CORS (http://enable-cors.org/)
Pour respecter ces conditions on utilise un entrepôt GitHub. Un compte GitHub a été créé avec le compte scoring@berryglide.net
* Se connecter sur GitHub https://github.com/
* Ouvrir le repository "cdf2016"
* Uploader le fichier texte créé précédemment
* Obtenir l'url du fichier : clic droit sur le bouton "Raw" et copier l'adresse du lien 
  * exemple : https://github.com/gpornin/ogntsk/raw/master/lfek_tsktst_nowlist.txt
  * optionel : on peut en plus utiliser le service https://goo.gl/ pour raccourcir l'url


####4 Fabriquer le lien OGN final
L'url finale est composée de parties principales :

* 1ere partie url OGN : http://live.glidernet.org/#c=46.8883333,2.04&z=9&o=1&b=49.0,44.0,-1.0,4.0&l=a&w=0&p=2
avec les paramètres suivants
  * c : coordonnées du centre de la carte affichée
  * z : niveau du zoom (de 0=plus détaillé à 21=monde entier)
  * o : =1 ==> affiche les aéronefs hors ligne
  * b : coordonnées des limites de la zone de vol
  * l : couches d'affichages (v:wind, p:pressure, z:airspaces, a:airports, r:recievers)
  * w : =0 ==> cache la fenêtre de warning
  * p : longueur de la trace (default: 5 min, p=2: 10 min, p=3 all points)
Plus de détail : https://github.com/glidernet/ogn-live#task-file-format
		
* 2e partie url du fichier de l'épreuve au format JSON
Récupérer l'url du fichier sous GitHub, par exemple : https://github.com/berryglide/ogntsk/raw/master/lfek_sample.txt
Ajouter cette url dans le paramètre "&t=" en fin d'url OGN.

On obtient finalement une url du type :
http://live.glidernet.org/#c=46.8883333,2.04&z=9&o=1&b=49.0,44.0,-1.0,4.0&l=a&w=0&p=2&t=https://github.com/berryglide/ogntsk/raw/master/lfek_sample.txt



###ANNEXE : Récupération des identifiants Flarm :
Les identifiants uniques des Flarm sont de la forme "DDxxxx" (ou plus rarement n° ICAO de l'aéronef)
Pour obtenir les identifiants des Flarm, le mieux est d'avoir un fichier IGC issus des Flarm.
L'identifiant est visible
* dans les fichiers IGC, à la ligne identifiée par "LFLAhhmmssID" où hhmmss varient en fonction de l'heure, ex : LFLA111955ID 2 DDD44C
* dans les rapports de test Flarm : https://flarm.com/support/tools-software/flarm-range-analyzer/
