# WriteUPS Vsftpd.log
Team : Sabotay Society
______________________
*PAS DE GRAMMAIRE* 
______________________

## Analyse et Conclusion
Au début  sur la connexion ftp je remarque une connexion depuis une adresse tel que `::ffff:127.0.0.1`, et il parait que cet adresse est genre du IPv4 avec une notation IPv6, pour pas trop blablater voyons un peu la différence via l'exemple suivant :
### ETAPE 1
```
::1 est une adresse IPv6

::ffff:127.0.0.1 est une IPv4 avec notation IPv6
```
Continuons que cet connexion depuis IPv4 avec notation IPv6 paraissait etre une connexion anonyme vers ftp avec le nom d'utilisateur  *anonymous*. Cet connexion conseguie 3 échecs au niveau du système authentification, et au 4 et 5 essaie avec un nom d'utilisateur *anon* et mot de passe *?* ainsi qu'un nom d'utilisateur *anon* et mot de passe *ls* et faut dire que tous ces essaient ont été effectué depuis l'IPv4 avec notation IPv6.

### ETAPE 2
Cette étape la illustre qu'on recoit diverse sorte de connexion impliquant a la fois diverses processus mais cette fois-ci depuis un client inconnu `::ffff:192.168.10.5`, presque la meme notation IPv4 avec notation IPv6 mais la différence est que celui-la il fixe pas depuis la connexion locale mais bien distant( en plus claire il se pourrait que cette connexion a ete inicie vie une autre hote). Cette connexion, des son arrivee depuis le server ftp essaie avec de s'authentifier avec nom d'utilisateur *anon* et mot de passe *IEUser@*( selon certaine recherche, cette mot de passe par defaut pour la connexion anonyme), et il va continuer par s'authentifier avec le nom d'utilsateur *anon* ainsi qu'un mot de passe *?*.

### ETAPE 3
Apres s'etre connecte au server ftp apres plusieurs tentatives de connexion, il va telecharger 2 fichiers a la racine du server ftp et aussi il parait que ces fihchiers sont des fichiers .bak ( c'est a dire des fichiers de sauvegardes), voyons voir :

1. */www-data.bak* : Ce fichier a premiere vue on pourrait dir que c'est un fichier de l'utilisateur web par defaut d'un server linux, ce fichier pourrait contenir tous les infos sur le server avec un privilege reduit pour le web 

2.*/coupons_2013.md.bak* : Ce fichier, bon je pourrais dire que c'est un fichier qui donne une reduction ou une avance a atteindre quelque chose si on s'accorde avec le terme coupon et puis ca se pourrait aussi qu'il contient une liste car il est sous format .md ( qui est un fichier markdown, dont laplupart s'en sert pour illustrer quelque texte de maniere d'un rapport)