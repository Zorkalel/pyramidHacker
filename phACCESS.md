# WriteUPS access.log
Team : Sabotay Society
______________________
*PAS DE GRAMMAIRE* 
______________________

## Analyse et Conclusion
### ETAPE 1
*IP ATTACKER* : `::ffff:192.168.10.5`( IPv4 avec notation IPv6)

Pour commencer, je dirais qu'au file de mon eenquete j'ai pu constater qu'il que l'attaquant commence par un *requete GET* ( requete de demande) vers un fichier .bak, voici dont la requete complete :
```
/nice ports,/Trinity.txt.bak
```
ca parait etre un fichier .bak ( un fichier de sauvegarde tres important), et souvent fois ces fichiers il faut jeter un coup d'oeil malgré ca semble un peu etre de format texte mais il faudrait y voir.

### ETAPE 2
Ensuite dans les lignes qui suivent je dois dire qu'une suite de requete GET aussi a ete lance mais cette fois il parait que c'est avec **NMAP** ( un mapper reseau, en gros cet outil permet de faire de  l'analyse de port, services, versions et plus loin ca peut meme aider a trouver des vulnerabilites si toute fois il y en a), voyons voir un peu :
```
::ffff:192.168.10.5 - - [11/Apr/2021:09:08:35 +0000] "PROPFIND / HTTP/1.1" 200 1924 "-" "Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)"
```
Dans cette requete le module NSE ( Nmap script engine, juste des petits programmes qui aident nmap a etre efficace) du nom <a href="https://nmap.org/nsedoc/scripts/http-webdav-scan.html" >WebDav</a>, permet de faire du WebScanning( analyse web, genre recherche de repertoire, fichiers, ..), faut dire que je l'ai decouvert juste parceque j'ai que le terme *PROPFIND* etait trop repeter et du coup j'ai appris que c'est une option de cette outil.

### ETAPE 3
Continuons plus bas on remarque une forte demande vers le systeme d'authentification , il parait que l'attaquant tentais de trouver le bon couple pour se connecter a un compte d'utilisateur que se soit root ou utilisateur a privilege requis et on peut le voir avec tous les requetes effectues et utilisait l'outil **Hydra** pour l'effectuer et faut dire que c'est un puissant outil de bruteforcage par dictionnaire ( ca signifie faire plusieurs tentatives jusqu'a en trouver le bon mais ceci avec une liste de mot specifiques, *le plus courant est le `rockyou.txt`*) Voyons voir :
```
::ffff:192.168.10.5 - - [11/Apr/2021:09:16:29 +0000] "POST /rest/user/login HTTP/1.0" 401 26 "-" "Mozilla/5.0 (Hydra)"
::ffff:192.168.10.5 - - [11/Apr/2021:09:16:31 +0000] "POST /rest/user/login HTTP/1.0" 200 831 "-" "Mozilla/5.0 (Hydra)"
```
### ETAPE 4
Plus bas on trouvera qu'il y avait aussi de sqli ( Sql Injection, en terme simple c'est une attaque qui permet d'extraire tous ce qui ce trouve dans une base de donnée{ que ce soit *email, password, adresse carte bancaire*, tous}) et l'outil la plus pratique et plus rapide est le fameux **SQLMAP** qui possede presque tous les types d'injections sql en forme de payload( charge utile, ca signifie une petite sequence de chaine tout pret pour effectuer ce que l'on veut) pret a l'emploi. Notre attaquant l'a utilisé. Voyons voir :
```
::ffff:192.168.10.5 - - [11/Apr/2021:09:31:04 +0000] "GET /rest/products/search?q=qwert')) UNION SELECT id, email, password, '4', '5', '6', '7', '8', '9' FROM Users-- HTTP/1.1" 200 - "-" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"
::ffff:192.168.10.5 - - [11/Apr/2021:09:32:51 +0000] "GET /rest/products/search?q=qwert')) UNION SELECT id, email, password, '4', '5', '6', '7', '8', '9' FROM Users-- HTTP/1.1" 200 3742 "-" "curl/7.74.0"
```
On peut voir que l'attaquant a trouver le parametre vulnerable (**/rest/products/search?q=**) et l'a aussi exploiter et si j'avais a schematiser ce qu'il a trouver je le faire de la maniere suivante : 


###### TABLE Users
| id| email | password |
|---|-------|----------|


en forme simple, ceux que l'attaquant a demandé a été sous cette forme et attention a la derniere requete effectué car il a été realisé avec l'outil *Curl* (Client Url), l'usage courant de cette outil est d'effectuer des requestes genre GET ou POST mais via la ligne de commande ou terminal il supporte un bon nombre d'autres mais si vous voulez cherchez-le. Voyons voir :
```
::ffff:192.168.10.5 - - [11/Apr/2021:09:32:51 +0000] "GET /rest/products/search?q=qwert%27))%20UNION%20SELECT%20id,%20email,%20password,%20%274%27,%20%275%27,%20%276%27,%20%277%27,%20%278%27,%20%279%27%20FROM%20Users-- HTTP/1.1" 200 3742 "-" "curl/7.74.0"
```
### ETAPE 5
Et finallement on a *Feroxbuster* l'outil pour forcer l'accesibilite vers les repertoires et autant faire de l'enumeration avancée et ceci dit cet outil a trouver un bon nombre de ressource important pour l'attaquant. Voyons voir :
```
/a54372a1404141fe8842ae5c029a00e3
/3e72ead66df04ca5bff7c9b741883cfbd3044c03e5114f7589804da12c36e5bafa6807b272cf4288ae1316f157b1fab2
/api
/administartion
/login
/admin
/backup
/promotion
/ftp
```
ensuite on a quelque fichiers interresant dont nous discutons en details dans les autres fichiers mais ce sont des fichiers de sauvegardes tres important, restez avec nous et lisez les suivants pour en savoir plus sur eux.
```
/ftp/www-data.bak
/ftp/coupons_2013.md.bak
``
