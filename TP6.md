**Quere Theotime 3ICS**

# Table des matières

[Exercice 1. Adressage IP (rappels)](#Anch1)

[Exercice 2. Préparation de l’environnement](#Anch2)

[Exercice 3. Installation du serveur DHCP](#Anch3)

[Exercice 4. Donner un accès à Internet au client](#Anch4)

[Exercice 5. Installation du serveur DNS](#Anch5)

[Exercice 6. Configuration du serveur DNS pour la zone tpadmin.local](#Anch6)

# Exercice 1. Adressage IP (rappels) <a id='Anch1'></a>
Vous administrez le réseau interne 172.16.0.0/23 d’une entreprise, et devez gérer un parc de 254 machines réparties en 7 sous-réseaux. La répartition des machines est la suivante :
- Sous-réseau 1 : 38 machines
- Sous-réseau 2 : 33 machines
- Sous-réseau 3 : 52 machines
- Sous-réseau 4 : 35 machines
- Sous-réseau 5 : 34 machines
- Sous-réseau 6 : 37 machines
- Sous-réseau 7 : 25 machines 
</br>
Donnez, pour chaque sous-réseau, l’adresse de sous-réseau, l’adresse de broadcast (multidiffusion) ainsi que les adresses de la première et dernière machine configurées (précisez si vous utilisez du VLSM ou pas).

</br>
On utilise pas de VLSM , car c'est faisable sans </br>

Convertion en binaire de l'adresse :</br>
172.16.0.0/23 -> 10101100 00010000 00000000 00000000</br>
Masque :         11111111 11111111 11111110 00000000</br>
 
Adresse du sous réseau : 172.16.0.</br></br>

Il faut faire 7 sous réseau dans ce reseau on choisit des sous reseau en /26 ( on peut mettre 8 /26 dans un / 23)</br></br>

On pourrat mettre dans chaque sous réseau au maximum : 64 adresses hotes

Les adresses des sous réseaux sont les suivantes : </br></br>

- Sous-réseau 3 : 52 machines
10101100 00010000 00000000 00000000 -> 172.16.0.0   /26 </br>
Broadcast : 172.16.0.63 </br>
Premiere : 172.16.0.1 </br>
Derniere : 172.16.0.62 </br>

- Sous-réseau 1 : 38 machines
10101100 00010000 00000000 01000000 -> 172.16.0.64  /26 </br>
Broadcast : 172.16.0.127 </br>
Premiere : 172.16.0.65 </br>
Derniere : 172.16.0.126 </br>

- Sous-réseau 6 : 37 machines
10101100 00010000 00000000 10000000 -> 172.16.0.128 /26 </br>
Broadcast : 172.16.0.191 </br>
Premiere : 172.16.0.129 </br>
Derniere : 172.16.0.190 </br>

- Sous-réseau 4 : 35 machines
10101100 00010000 00000000 11000000 -> 172.16.0.192 /26 </br>
Broadcast : 172.16.0.255 </br>
Premiere : 172.16.0.193 </br>
Derniere : 172.16.0.254 </br>

- Sous-réseau 5 : 34 machines
10101100 00010000 00000001 00000000 -> 172.16.1.0   /26 </br>
Broadcast : 172.16.1.63 </br>
Premiere : 172.16.1.1 </br>
Derniere : 172.16.1.62 </br>

- Sous-réseau 2 : 33 machines
10101100 00010000 00000001 01000000 -> 172.16.1.64  /26 </br>
Broadcast : 172.16.1.127 </br>
Premiere : 172.16.1.65 </br>
Derniere : 172.16.1.126 </br>

- Sous-réseau 7 : 25 machines 
10101100 00010000 00000001 10 000000 -> 172.16.1.128 /27 </br>
Broadcast : 172.16.1.63 </br>
Premiere : 172.16.1.0 </br>
Derniere : 172.16.1.62 </br>

# Exercice 2. Préparation de l’environnement <a id='Anch2'></a>
Dans ce TP nous allons mettre en place un réseau rudimentaire constitué de seulement deux machines :
un serveur et un client :
- le serveur a une connexion Internet, notamment pour télécharger les paquets nécessaires à l’installation
des serveurs, et sert de passerelle au client ;
- les deux machines appartiennent à un réseau local ;
- le client a accès à Internet uniquement via le serveur ; il dispose d’une interface réseau qui recevra son
adresse IP du serveur DHCP. Dans un premier temps, cette interface sera activée mais débranchée.
## 1. VM éteintes, utilisez les outils de configuration de VirtualBox pour mettre en place l’environnement décrit ci-dessus.

![](/TP6/IMG_1.png)

![](/TP6/IMG_2.png)

## 2. Démarrez le serveur et vérifiez que les interfaces réseau sont bien présentes. A quoi correspond l’interface appelée lo ?

```console
User@localhost:~$ ip a
```

![](/TP6/IMG_3.png)

Lo est l'interface de loopback. Celle sur laquelle on met habituellement l'adresse de localhost.

## 3.  Dans les versions récentes, Ubuntu installe d’office le paquet cloud-init lors de la configuration du système. Ce paquet permet la configuration et le déploiement de machines dans le cloud via un script au démarrage. Nous ne nous en servirons pas et sa présence interfère avec certains services (en particulier le changement de nom d’hôte) ; par ailleurs, vos machines démarreront plus rapidement.
Désinstallez complètement ce paquet (il faudra penser à le faire également sur le client
ensuite.)

```console
User@localhost:~$ sudo apt purge cloud-init -y
User@localhost:~$ sudo rm -rf /etc/cloud && sudo -rm -rf /var/lib/cloud/
```
On reboot :

```console
User@localhost:~$ init 6
```

## 4. Les deux machines serveur et client se trouveront sur le domaine tpadmin.local. A l’aide de la commande hostnamectl renommez le serveur (le changement doit persister après redémarrage, donc cherchez les bonnes options dans le manuel !). On peut afficher le nom et le domaine d’une machine avec les commandes hostname et/ou dnsdomainname ou en affichant le contenu du fichier /etc/hostname.

Avant les changements : 

Hostname : Localhost
DnsDomainName : Rien

On change l'hostname :

```console
User@localhost:~$ sudo hostnamectl set-hostname TheotimeqServeur
```

On redemare pour voir si c'est bien persistant : 

```console
User@localhost:~$ init6
User@localhost:~$ hostnamectl
```

![](/TP6/IMG_4.png)

# Exercice 3. Installation du serveur DHCP <a id='Anch3'></a>
Un serveur DHCP permet aux ordinateurs clients d’obtenir automatiquement une configuration réseau
(adresse IP, serveur DNS, passerelle par défaut…), pour une durée déterminée. Ainsi, dans notre cas, l’interfaces réseau de client doit être configurée automatiquement par serveur. Le réseau local tpadmin.local
a pour adresse 192.168.100.0/24 (on aurait pu choisir une autre adresse ; attention, 192.168.1.0/24 est
souvent réservée, par exemple par votre FAI).

## 1. Sur le serveur, installez le paquet isc-dhcp-server. La commande systemctl status isc-dhcp-server devrait vous indiquer que le serveur n’a pas réussi à démarrer, ce qui est normal puisqu’il n’est pas encore configuré (en particulier, il n’a pas encore d’adresses IP à distribuer).

On install le paquet isc-dhcp-server

```console
sudo atp-get install isc-dhcp-server
```

## 2. Un serveur DHCP a besoin d’une IP statique. Attribuez de manière permanente l’adresse IP 192.168.100.1 à l’interface réseau du réseau interne. Vérifiez que la configuration est correcte.

On install le serveur dhcp puis on mets une ip statique que la bonne interface réseau.
```console
User@localhost:~$ sudo atp-get install isc-dhcp-server
User@localhost:~$ ip addr add 192.168.100.1/24 dev ens224
``` 

![](/TP6/IMG_5.png)

## 3. La configuration du serveur DHCP se fait via le fichier /etc/dhcp/dhcpd.conf. Faites une sauvegarde du fichier existant sous le nom dhcpd.conf.bak puis éditez le fichier dhcpd.conf avec les informations suivantes :

On fait une sauvegarde du fichier existant : 

```console
User@localhost:~$ cp /etc/ghcp/dhcp.conf /etc/ghcp/dhcp.conf.bak
``` 

On edite le fichier avec les informations demandé

```console
User@localhost:~$ sudo nano /etc/ghcp/dhcp.conf
``` 

![](/TP6/IMG_6.png)

A quoi correspondent les deux premières lignes ?

La ligne default-lease-time sert à setup le temps de validité d'une adresse ip. L'adresse ip donnée au client est temporaire , il doit la renouveler avant expiration de ce delais.

La ligne max-lease-time sert à setup le temps maximum de pret d'une adresse ip.

Les valeurs indiquées sur ces deux lignes sont faibles, afin que l’on puisse voir constituer quelques logs durant ce TP. Dans un environnement de production, elles sont beaucoup plus élevées !

## 4. Editez le fichier /etc/default/isc-dhcp-server afin de spécifier l’interface sur laquelle le serveur doit écouter.

On edite le fichier /etc/default.ics-dhcp-server 

```console
User@localhost:~$ sudo nano /etc/default.ics-dhcp-server 
```

![](/TP6/IMG_7.png)

## 5. Validez votre fichier de configuration avec la commande dhcpd -t puis redémarrez le serveur DHCP (avec la commande systemctl restart isc-dhcp-server) et vérifiez qu’il est actif.


```console
User@localhost:~$ sudo dhcpd -t
```

on redemare le service:

```console
User@localhost:~$ sudo systemctl restart isc-dhcp-server
```

On regarde si il y as des erreurs
```console
User@localhost:~$ tail /var/log/syslog
```

![](/TP6/IMG_8.png)

On retrouve la meme erreur avec :
```console
User@localhost:~$ systemctl status isc-dhcp-server
```

Peut etre que notre interface ens224 est down
```console
User@localhost:~$ ip link set dev ens224 up
```

Après correction , le service démare : 

![](/TP6/IMG_9.png)

## 6. Notre serveur DHCP est configuré ! Passons désormais au client. Si vous avez suivi le sujet du TP 1, le client a été créé en clonant la machine virtuelle du serveur. Par conséquent, son nom d’hôte est toujours serveur. Vérifiez que la carte réseau du client est débranchée, puis démarrez le client (il est possible qu’il mette un certain temps à démarrer : ceci est dû à l’absence de connexion Internet).
Comme pour le serveur, désinstallez ensuite cloud-init, puis modifiez le nom de la machine (elle doit
s’appeler client.tpadmin.local).

On desinstal clou init :
```console
User@localhost:~$ sudo apt purge cloud-init -y
User@localhost:~$ sudo rm -rf /etc/cloud && sudo -rm -rf /var/lib/cloud/
```

On change le nom de l'hostname :
```console
User@localhost:~$ sudo hostnamectl set-hostname TheotimeqClient
```

On empeche la latence au demarage si pas internet :
```console
User@localhost:~$ sudo nano /etc/netplan/00-installer-config.yaml
```

![](/TP6/IMG_10.png)

## 7. La commande tail -f /var/log/syslog affiche de manière continue les dernières lignes du fichier de log du système (dès qu’une nouvelle ligne est écrite à la fin du fichier, elle est affichée à l’écran).

On affiche les logs sur le server : 

```console
User@localhost:~$ sudo tail -f /var/log/syslog
```

![](/TP6/IMG_11.png)

Les messages : 

DHCPDISCOVER from 00:50:56:89:7d:5d (TheotimeqClient) via ens224
Ce message sert à detecter les serveurs DHCP disponible

DHCPOFFER on 192.168.100.101 to 00:50:56:89:7d:5d (TheotimeqClient) via ens224
Ce message est la reponse à un paquet DHCPDISCOVER

DHCPREQUEST for 192.168.100.101 from 00:50:56:89:7d:5d (TheotimeqClient) via ens224
Ce message contient l'adresse ip du serveur et l'adresse ip fourni au client . Il à pour but d'informer les autres serveurs DHCP de ne pas donner suite car le client à recu son IP .

DHCPACK on 192.168.100.100 to 00:50:56:89:7d:5d (TheotimeqClient) via ens224
Ce message est emit par le serveur mentioné par le DHCPREQUEST en guise de réponse.

On verifie que le client recoit bien un ip de la plage specifié précedémment  :

L'ip du client est : 192.168.100.101
C'est bien un IP de la range 192.168.100.100 | 192.168.100.240

## 8. Que contient le fichier /var/lib/dhcp/dhcpd.leases sur le serveur, et qu’afficle la commande dhcp-lease-list ?

On regarde le fichier : 
```console
User@localhost:~$ sudo nano /var/lib/dhcp/dhcpd.leases
```

Ce fichier stock la liste des clients connus , leur ip ...

![](/TP6/IMG_12.png)

```console
User@localhost:~$ sudo dhcp-lease-list
```

![](/TP6/IMG_13.png)

La commande montre la liste des client connus , leur ip , leur mac , leur date de connection..

## 9. Vérifiez que les deux machines peuvent communiquer via leur adresse IP, à l’aide de la commande ping.

On ping le client depuis le server : 
```console
User@localhost:~$ sudo nano ping 192.172.100.101
```
![](/TP6/IMG_14.png)

## 10. Modifiez la configuration du serveur pour que l’interface réseau du client reçoive l’IP statique 192.168.100.20 :

On modifie la configuration du server : 

`
deny unknown-clients; #empêche l'attribution d'une adresse IP à une
#station dont l'adresse MAC est inconnue du serveur
host client {
hardware ethernet 00:50:56:89:7d:5b; #remplacer par l'adresse MAC
fixed-address 192.168.100.20;
}`

On reedite le fichier conf : 

```console
User@localhost:~$ sudo nano /etc/gh cp/dhcp.conf
```

![](/TP6/IMG_15.png)

On vas sur le client et on regarde notre nouvelle adress ip :
```console
User@localhost:~$ dhclient -v
User@localhost:~$ ip a
```

Le ping foncitonne : 

![](/TP6/IMG_16.png)

On vois la nouvelle adresse ip :
![](/TP6/IMG_17.jpg)

# Exercice 4. Donner un accès à Internet au client <a id='Anch4'></a>

## 1. La première chose à faire est d’autoriser l’IP forwarding sur le serveur (désactivé par défaut, étant donné que la plupart des utilisateurs n’en ont pas besoin). Pour cela, il suffit de décommenter la ligne net.ipv4.ip_forward=1 dans le fichier /etc/sysctl.conf. Pour que les changements soient pris en compte immédiatement, il faut saisir la commande sudo sysctl -p /etc/sysctl.conf.

On edite le fichier avec nano

```console
User@localhost:~$ sudo nano /etc/sysctl.conf
```

On reload la config 
```console
User@localhost:~$ sudo sysctl -p /etc/sysctl.conf
```

On verifie que ca à bien marché
```console
User@localhost:~$ sudo sysctl net.ipv4.ip_forward 
```

![](/TP6/IMG_18.png)

## 2. Ensuite, il faut autoriser la traduction d’adresse source (masquerading) en ajoutant la règle iptables suivante :

On autorise la traduction d'adresse : 

```console
User@localhost:~$ sudo iptables --table nat --append POSTROUTING --out-interface ens192 -j MASQUERADE
```

![](/TP6/IMG_19.png)

On ping 1.1.1.1 :

![](/TP6/IMG_200.jpg)

# Exercice 5. Installation du serveur DNS <a id='Anch5'></a>

## 1. Sur le serveur, commencez par installer bind9, puis assurez-vous que le service est bien actif.

```console
User@localhost:~$ sudo apt-get install bind9
User@localhost:~$ sudo service named start
```

On verifie avec 

```console
User@localhost:~$ sudo service named status
```

![](/TP6/IMG_20.png)

## 2. A ce stade, Bind n’est pas configuré et ne fait donc pas grand chose. L’une des manières les simples de le configurer est d’en faire un serveur cache : il ne fait rien à part mettre en cache les réponses de serveurs externes à qui il transmet la requête de résolution de nom.

```console
User@localhost:~$ sudo nano /etc/bind/named.conf.options
```

![](/TP6/IMG_21.png)


## 3. Sur le client, retentez un ping sur www.google.fr. Cette fois ça devrait marcher ! On valide ainsi la configuration du DHCP effectuée précédemment, puisque c’est grâce à elle que le client a trouvé son serveur DNS.

![](/TP6/IMG_202.jpg)

## 4. Sur le client, installez le navigateur en mode texte lynx et essayez de surfer sur fr.wikipedia.org (bienvenue dans le passé...)

```console
User@localhost:~$ sudo apt install lynx
```

![](/TP6/IMG_201.jpg)

# Exercice 6. Configuration du serveur DNS pour la zone tpadmin.local <a id='Anch6'></a>

## 1. Modifiez le fichier /etc/bind/named.conf.local et ajoutez les lignes suivantes :

On modifie le fichier :

```console
User@localhost:~$ sudo nano /etc/bind/named.conf.local 
```

![](/TP6/IMG_22.png)

## 2. Créez une copie appelée db.tpadmin.local du fichier db.local. Ce fichier est un fichier configuration

On fait la copie : 

```console
User@localhost:~$ sudo cp /etc/bind/db.local /etc/bind/db.tpadmin.local
```

On edite le fichier : 

```console
sudo nano /etc/bind/db.tpadmin.local
```

![](/TP6/IMG_23.png)

## 3. Maintenant que nous avons configuré notre fichier de zone, il reste à configurer le fichier de zone inverse,

On edite le fichier : 

```console
sudo nano /etc/bind/named.conf.local
```

![](/TP6/IMG_24.png)

CReation du fichier db :

![](/TP6/IMG_203.jpg)

## 4. Utilisez les utilitaires named-checkconf et named-checkzone pour valider vos fichiers de configuration :

`
$ named-checkconf named.conf.local
$ named-checkzone tpadmin.local /etc/bind/db.tpadmin.local
$ named-checkzone 100.168.192.in-addr.arpa /etc/bind/db.192.168.100
`

![](/TP6/IMG_204.jpg)

![](/TP6/IMG_205.jpg)

Modifiez le fichier /etc/systemd/resolved.conf et décommentez la section DNS.

## 5. Redémarrer le serveur Bind9. Vous devriez maintenant être en mesure de « pinguer »les différentes machines du réseau.

On ping une autre machine sur le réseau : 

![](/TP6/IMG_206.jpg)




