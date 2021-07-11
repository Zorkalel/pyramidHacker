# WriteUPS auth.log
Team : Sabotay Society
______________________
*PAS DE GRAMMAIRE* 
______________________

## Analyse et Conclusion
### ETAPE 1
**IP ATTACKER** : `::ffff:192.168.10.5`( IPv4 avec notation IPv6)

Pour commencer l'attaquant essaie de se connecter mais je pourrais dire de maniere *test* c'est a dire qu'il cherchait a trouver le nombre de tentative de faux mot de passe le server **SSH** supporte. Voyons voir :
```
Apr 11 09:38:29 thunt sshd[8162]: Failed password for www-data from 192.168.10.5 port 40066 ssh2
Apr 11 09:38:30 thunt sshd[8162]: Failed password for www-data from 192.168.10.5 port 40066 ssh2
Apr 11 09:38:30 thunt sshd[8162]: Connection closed by authenticating user www-data 192.168.10.5 port 40066 [preauth]
```
Nous pouvons voir que la tentative a été lancé via l'utilisateur **www-data** et sur le port 4066 ( mais tardons pas trop sur le port car ca peut changer).

Ensuite on voit le service CRON( qui permet aux utilisateurs des systèmes Unix d'exécuter automatiquement des scripts, des commandes ou des logiciels à une date et une heure spécifiées à l'avance, ou selon un cycle défini à l'avance ) a prit la main avec un utilisateur root.

### ETAPE 2
Continuons plus bas qu'on remarque une erreur `FAILES PASSWORD FOR www-data`, et c'est tellement beaucoup et sur un meme utilisateur que ca semble etre du bruforcage sur le *server ssh avec le utilisateur www-data dont l'IP destinataire est 192.168.10.5*. Voyons voir un petit peu des requetes car on peut pas tout mettre :
```
Apr 11 09:39:37 thunt sshd[8232]: Failed password for www-data from 192.168.10.5 port 40084 ssh2
Apr 11 09:39:37 thunt sshd[8234]: Failed password for www-data from 192.168.10.5 port 40088 ssh2
Apr 11 09:39:37 thunt sshd[8237]: Failed password for www-data from 192.168.10.5 port 40094 ssh2
Apr 11 09:39:37 thunt sshd[8229]: Failed password for www-data from 192.168.10.5 port 40078 ssh2
Apr 11 09:39:37 thunt sshd[8235]: Failed password for www-data from 192.168.10.5 port 40090 ssh2
```
Apres plusieurs tentatives de connexion sans succes enfin le systeme arrive a prendre la main sur ce qui se passe et repond avec une reponse `maximum authentication attempts exceeded for www-data, Too many authentication failures`, qui signifie que la limite d'essaie pour cet utilisateur est arrivé mais l'attaquant ne s'est pas arreté et après quelques requetes de plus il a trouvé le bon mot de passe et  est arrivé a se connecter avec succes au server SSH avec une session entant qu'utilisateur de l'application web (www-data). Voyons voir :
```
Apr 11 09:41:19 thunt sshd[8260]: Accepted password for www-data from 192.168.10.5 port 40112 ssh2
Apr 11 09:41:19 thunt sshd[8260]: pam_unix(sshd:session): session opened for user www-data by (uid=0)
Apr 11 09:41:19 thunt systemd-logind[737]: New session 12 of user www-data.
```

