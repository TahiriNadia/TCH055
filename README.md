# TCH055


# INF1070 Hiver 2020 - Travail pratique 2

**Ce travail peut être fait en équipe de deux.**

Ce travail porte principalement sur la découverte, l'installation, l'administration et l'utilisation sommaire de [Odoo](https://www.odoo.com/). Odoo est un progiciel de gestion intégré (PGI) ou *Enterprise Resource Planning ([ERP](https://en.wikipedia.org/wiki/Enterprise_resource_planning))*, 
open source. Il comporte plusieurs applications pour de nombreux processus dans 
une organisation ou une entreprise. La procédure d'[installation de odoo](https://www.odoo.com/documentation/13.0/setup/install.html#setup-install-source) est disponible 
 dans la documentation ([https://www.odoo.com/documentation/user/13.0/index.html](https://www.odoo.com/documentation/user/13.0/index.html)). 
 Cet énoncé explique la procédure à suivre pour installer Odoo à partir du code source. Le 
 but est de s'initier et d'expérimenter certaines tâches d'un administrateur système.
  **Dans le cadre de ce TP, il est strictement interdit d'installer Odoo avec un docker 
    ou un  gestionnaire de paquets effectuant toutes manipulations**. 
 
## Instructions générales

- Ce travail permet d'expérimenter quelques tâches effectuées  habituellement par un administrateur système. L'exercice consiste à installer et configurer une application Web avec les prérequis nécessaires. 
  Pour cela, on demande de procéder selon les instructions données dans le présent 
  document.

- Pour faciliter la reproduction des étapes importantes, on vous demande de créer un fichier ­`rapport-tp2.md` 
  selon le modèle fourni avec cet énoncé et d'y mettre les différentes commandes, 
   avec les résultats obtenus ainsi que les réponses à chaque question posée.

- Il faudra également mettre les extraits des fichiers de configuration modifiés dans le rapport `rapport-tp2.md`.


- Le fichier `solution-tp2.sh` devra contenir  les commandes 
  exécutées dans le cadre de ce TP. Dans une certaine mesure, ce fichier fait office 
  d'un programme Shell qu'on pourrait exécuter pour faire automatiquement toutes les activités d'installation. 

- Pour les captures d'écran, privilégiez le format PNG (.png) pour éviter des problèmes d'affichage 
  dans le rapport au format Markdown.

- Certaines commandes données dans l'énoncé peuvent nécessiter l'utilisation de `sudo` pour les exécuter. Notez que si l'on se connecte en tant que root (ex: sur un `container` docker, il n'est pas nécessaire d'utiliser `sudo`).
  
## Environnement de travail - Configuration initiale

Vous avez le choix de travailler avec l'un des deux environnements de travail expliqués ci-après. Dans cette section on vous indique la configuration du système d'exploitation à utiliser pour ce travail.

###  Container docker - Ubuntu 18.04

**Cet environnement est celui à privilégier.**

- Un [`container`](https://www.docker.com/resources/what-container) est une unité standard contenant l'installation minimale (librairies, paquets, configuration, etc.) nécessaire pour faire fonctionner une application. Dans ce TP, un `container` docker pourra être utilisé comme une machine Linux (terminal seulement - pas d'environnement graphique).
- Pour travailler avec cet environnement, il faut avoir [installé Docker](https://docs.docker.com/install/). (Pour les 
  anciennes versions de Windows ou Mac (https://docs.docker.com/toolbox/overview/](https://docs.docker.com/toolbox/overview/).)
  - **Windows** [https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/) (Si vieille version Windows 
    [https://docs.docker.com/toolbox/overview/](https://docs.docker.com/toolbox/overview/))
  - **Mac OS** [https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)
  - **Linux Ubuntu** [https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  - **Linux Fedora** [https://docs.docker.com/install/linux/docker-ce/fedora/](https://docs.docker.com/install/linux/docker-ce/fedora/) 

*Notez que si vous ne voulez/pouvez pas installer Docker sur votre système d'exploitation Mac OS ou Windows, vous pouvez aussi installer Docker sur votre machine virtuelle Linux pour profiter des avantages expliqués ci-dessous.*

- Dans ce dossier, on vous donne un [Dockerfile](Dockerfile) (pour info: [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)) contenant la configuration initiale (Ubuntu 18.04). L'idée n'est pas d'écrire ou de manipuler l'environnement docker à proprement parler, mais de travailler avec un `container` docker comme si l'on était connecté sur un terminal Linux.

- L'avantage de travailler avec des `containers` est qu'on peut rapidement recommencer l'exercice autant de fois qu'on le veut en partant exactement de la même configuration. On peut aussi changer facilement de système d'exploitation pour son application (ex.: tester une autre distribution Linux).

- Dans le cadre de ce TP, on vous donne un script `login.sh` qui va exécuter les commandes 
docker nécessaires jusqu'à vous connecter sur un terminal du `container`. Pour cela, en utilisant un terminal, vous devez vous placer dans le dossier contenant les fichiers `Dockerfile` et `login.sh` puis exécuter la commande suivante.

> ~~~csh
> $ ./login.sh
> ~~~ 
>
> - La première fois, il faut avoir une connexion Internet  parce que le système va procéder au téléchargement des paquets, et à la création de l'image, et du `container` docker.
> - Les autres fois (pour vous connecter à votre docker Linux), la connexion Internet n'est plus requise (sauf si vous voulez installer quelque chose avec le gestionnaire de paquets, évidemment).
> - Si tout se passe bien, le dossier courant sera monté dans le `container` sous `/inf1070`. 
Ce dossier peut-être utilisé pour l'échange des fichiers entre le `container` et la 
machine locale.
> - Vous serez connecté sur le `container` avec le compte suivant.
>>
>> ~~~csh
>> Nom d'utilisateur: etudiant
>> Mot de passe: INF1070pass
>> ~~~
>> Le mot de passe va servir essentiellement pour les commandes `sudo`.

- Le gestionnaire de paquets utilisé dans cet énoncé est `apt` parce qu'on se base 
  sur la distribution Ubuntu.

- Rappelons qu'avant de procéder à l'installation des paquets, il faut mettre à jour 
  les informations en tapant la commande suivante.

~~~csh
$ sudo apt update
~~~

### Machine virtuelle ou autre - Ubuntu 18.04

- Si vous ne pouvez pas travailler avec un `container` docker,  vous pouvez utiliser 
  une machine virtuelle ou une installation classique.
- Pour cela, il faut partir d'une installation *fraiche" de Ubuntu 18.04 (configuration 
  de base) dont la liste des paquets doit-être le plus proche possible de celle fournie  dans le fichier [`listePackages.txt`](listePackages.txt). 

- Créez un dossier `/inf1070`.

## Identification du système d'exploitation

- Commencez par mettre les informations d'identification du système d'exploitation dans le fichier ­`rapport-tp2.md`. Pour cela, exécutez la commande suivante pour les obtenir (pour plus d'info voir [https://www.cyberciti.biz/faq/how-to-check-os-version-in-linux-command-line/](https://www.cyberciti.biz/faq/how-to-check-os-version-in-linux-command-line/)). 

~~~csh
$ lsb_release  -a
~~~
> - Qu'est-ce qu'on peut identifier plus précisément avec les informations fournies?

- En utilisant la commande suivante, enregistrez la liste des paquets installés initialement sur la machine :
 
 ~~~csh
 $ sudo apt list --installed > paquetsInit.txt
 ~~~

- En utilisant les commandes `tail` et `wc` ainsi que le fichier `paquetsInit.txt`, dites dans le rapport, combien de paquets sont installés initialement. 

 > Vous pouvez utiliser la commande suivante :
 >
 >~~~csh
 > $ tail -n +1 paquetsInit.txt | wc -l
 >~~~
 > 
 >Si la première ligne du fichier `paquetsInit.txt` est  du genre `En train de lister 
  ...`, alors utilisez la commande suivante.
 >
 >~~~csh
 > $ tail -n +2 paqutages0.txt | wc -l
 >~~~

## Python - Installation et configuration

- Vérifier si python3 et pip3 sont déja installés et les versions éventuelles

~~~csh
$ python3 --version
$ pip3 --version
~~~

> - `pip` ou `pip3` est un gestionnaire de paquets pour le language Python.
> - Pour chacune des commandes, dites quelle version exacte est installée.
> - En utilisant la commande `grep` et `wc` dites combien de paquets contiennet le 
mot `python`.

- Installez python 3 en observant bien ce qui se passe pour répondre aux questions suivantes.

> - Est-ce qu'il y a d'autres paquets qui s'installent ? Pourquoi ?
> - Est-ce qu'il y a des paquets qui étaient déjà installés mais que le gestionnaire 
a proposé de mettre à jour ?

~~~csh
$ sudo apt install python3 python3-pip
~~~ 

- Apres l'installation, vérifiez à nouveau la version Python3 installée pour s'assurer 
  que vous avez bien python 3.6 (ou version supérieure) comme version par défaut.  Pour cela exécuter la commande suivante :

~~~csh
$ python3 --version
$ pip3 --version
~~~

> - Quelles sont les versions exactes installées ?
> - Si ce n'est pas python 3 (actuellement, la version par défaut sur ubuntu 18.04 est 
3.6) ou une version supérieure, alors exécuter la commande suivante  pour assigner python 3.6 comme version par défaut.

> ~~~csh
> $ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 10
> ~~~

- Produire la liste des paquets installés à ce stade :

~~~csh
$ apt list --installed > paquetsPython.txt
~~~
> - En utilisant les commandes `tail` et `wc` comme expliqué précédement, dites le nombre total de paquets qui sont installés maintenant.
> - Dites combien de paquets ont le le mot `python` dans leur nom ?

## PostgreSQL - Installation et configuration

A titre d'information, [postgreSql](https://www.postgresql.org/) est un système de gestion de base de données (SGBD). Dans ce TP, nous allons l'utiliser pour installer [Odoo](https://www.odoo.com/).

- Installation 

~~~csh
$ sudo apt install postgresql postgresql-client
~~~

> - Si tout se passe bien, un utilisaeur système `postgres` sera créé
> - Vérifiez qu'on a bien un utilisateur `postgres`
> ~~~csh
> $ id postgres
> ~~~
>  Cela devrait afficher les informations de l'utilisateur `postgres` que vous devez inclure dans votre rapport.

- Démarer le serveur postgresql

~~~csh
$ sudo service  postgresql start
~~~

- Créer un utilisateur `odoo` pour la base de données 

~~~csh
$ sudo -u postgres createuser -s odoo
~~~ 

- Créer une base de données `odoo`, et en donner la propriété à l'utilisateur `odoo` :

~~~csh
$ sudo -u postgres createdb -E utf8 -l fr_CA.utf8 -T template0 -O odoo odoo
~~~ 

- Modifier le mot de passe de l'utilisateur `odoo` par `passodoo` :

~~~csh
$ sudo -u postgres psql --command="ALTER USER odoo WITH PASSWORD 'passodoo'"
~~~

- Produire la liste des paquets installés à ce stade, et dire combien il y en a au total, maintenant.

~~~csh
$ sudo apt list --installed > paquetsPostgreSql.txt
~~~

## Odoo - Installation et configuration

### Installation

- Prérequis - installer les requis suivants :

~~~csh
$ sudo apt install python3-dev libxml2-dev libxslt1-dev libldap2-dev libsasl2-dev 
~~~

- Installer `nodejs` et `npm` et `wkhtmltopdf` :

~~~csh
$ sudo apt install wkhtmltopdf nodejs npm 
~~~

- Installer `rtlcss` avec le gestionnaire des paquets `npm` pour la prise en charge des langues de droite à gauche :

~~~csh
$ sudo npm install -g rtlcss
~~~

- Copier le code source odoo, et le mettre dans le dossier d'installation :

~~~csh
$ sudo cp -r /inf1070/odoo /opt/
~~~

- Installer des librairies python spécifiques (peut nécessiter une connexion Internet).
  - Allez dans le  dossier `odoo` contenant le code source. C'est le dossier qui contient le 
    fichier `requirements.txt`
  - Tapez les commandes suivantes pour installer les librairies Python :
  ~~~csh
  $ sudo pip3 install setuptools wheel
  $ sudo pip3 install -r requirements.txt
  $ sudo pip3 install -U pip
  $ sudo pip3 install Pypdf2
  ~~~

- Produire la liste des paquets installés à ce stade, et dire combien il y en a, au total, maintenant.

~~~csh
$ apt list --installed > paquetsPrerequis.txt
~~~

### Configuration et démarrage

- Création du dossier pour les données.

~~~csh
$ sudo mkdir /var/odoo
$ sudo chmod  -R  755 /var/odoo
~~~

- Configurez le fichier [`odoo.conf`](odoo.conf) fourni en spécifiant les valeurs des paramètres suivants :
  - **addons_path**: chemin du sous-dossier addons
  - **data_dir**: Répertoire des données
  - **database**: Nom de la base de données
  - **db_user**: Nom d'utilisateur de la base de données
  - **db_password**: Mot de passe de l'utilisateur base de données
  > Au besoin, on trouvera plus d'information sur [https://www.odoo.com/documentation/13.0/reference/cmdline.html#configuration-file](https://www.odoo.com/documentation/13.0/reference/cmdline.html#configuration-file)
- Copiez le fichier `odoo.conf` das le dossier d'installation de odoo.
- Installez la base de données de odoo en tapant la commande suivante dans le dossier d'instalation 
de odoo

~~~csh
$ sudo python3 odoo-bin -c odoo.conf -i base --stop-after-init
~~~

> - Attention, ceci va arrêter le serveur après installation de la base de données (création des tables dans la base de données)
> - On peut aussi utiliser directement la commande suivante :
>
> ~~~csh
>$ sudo python3 odoo-bin --addons-path=addons --data-dir=/var/odoo -d odoo -r odoo -w passodoo \
>   -i base --stop-after-init --db_host localhost
> ~~~

- Démarrer le serveur odoo :

~~~csh
$ sudo python3 odoo-bin -c odoo.conf 
~~~

> On peut aussi utiliser directement la commande suivante :

> ~~~csh
>$ sudo python3 odoo-bin --addons-path=addons --data-dir=/var/odoo -d odoo -r odoo -w passodoo \
>    --db_host localhost
> ~~~

- Pour faciliter le demarrage, créer un script shell `startodoo.sh` contenant la commande de démarrage et placez-le dans le dossier d'installation de Odoo.

## Odoo - Applications et Utilisation 

- Avec un fureteur (Chrome), allez sur le site (http://localhost:8065/)
> Si vous avez bien installé et démarré Odoo, et que ça ne fonctionne pas, essayez un autre numéro de port compris entre 8065 et 8069.
> - Pour faire des captures d'écran à mettre dans le rapport, Connectez vous à postgres et vérifiez que plusieurs tables sont bien créées dans la base de données `odoo`. 
> ~~~csh
> $ sudo -u postgres psql
> ~~~
>
> - Pour voir la liste des bases de données (BD)
> ~~~csh
> postgres=# \l
> ~~~
>> - Faire une capture d'écran du terminal avec la lliste des BD.
>
> - Voir la liste des utilisateurs postgreSQL :
> ~~~csh
> postgres=# \du
> ~~~
>> - Faire une capture d'écran de la liste des utilisateurs.
>
> - Se connecter à la base de données `odoo` :
> ~~~csh
> postgres=# \c odoo
> ~~~
>
> - Voir si plusieurs (plus de 2) des tables ont bien été créées :
> ~~~csh
> postgres=# \dt
> ~~~
> - Faire une capture d'écran de la liste des tables (cartaines tables peuvent ne 
pas apparaitre dans la capture) :
>
> > S'il n'y a pas assez de tables alors recommencez l'étape d'installation de la base 
de données de Odoo :

> - Pour quitter postgreSQL, tapez la commande suivante
> ~~~csh
> postgres=# \q
> ~~~

- Sur l'interface Web, connectez-vous avec le nom d'utilisateur `admin`, et le mot de 
  passe `admin`.

> Dans un contexte professionnel il faudrait utiliser un mot de passe plus solide. Il est 
aussi fortement déconseillé d'avoir un mot de passe identique au nom d'utilisateur.

- Configurer l'interface Web de la page d'accueil de votre application et activer  les modules suivants :
   - développement des pages Web
   - gestion des évenements
   - E-Learning
   - CRM
   - Tous les autres modules gratuits

- En utilisant le module de gestion des évenements, créez une conference sur COVID-19 
  qui aura lieu le 2020-06-30.

- En utilisant le module de création de sites Web, créez une page Web, et rédigez un 
  petit article sur COVID-19 avec un titre de votre choix.
-En consultant aussi  [https://www.odoo.com/documentation/13.0/setup/install.html#source-install](https://www.odoo.com/documentation/13.0/setup/install.html#source-install), 
dites :
> - Qu'est-ce que qui se passe si l'on arrête le service postgres ?
> - Pourquoi a-t-on installé le langage Python et pas Java, par exemple ?
> - Pourquoi a-t-on installé postGresql et pas Mysql ou un autre SGBD ?
> - Quelle différence faites-vous entre les comptes utilisateurs suivants qui ont 
été utilisés, et dans quelles circonstances devrait-on les utiliser ?
>> - root
>> - etudiant
>> - odoo
>> - admin 

## Sauvegarde et Restauration


### Base de données

Les personnes curieuses trouveront plus d'information sur [https://www.postgresql.org/docs/10/backup-dump.html](https://www.postgresql.org/docs/10/backup-dump.html).

- Après avoir rédigé tous les rapports, et effectué toutes les configurations, effectuez 
une sauvegarde (backup) de la base de données.

~~~csh
$ sudo -u postgres pg_dump odoo > odoo_bd.bak
~~~

### Fichiers de données et de configuration

- En utilisant la commande `tar` (au au besoin gzip), créez un fichier `odoo.tar.gz` 
du répertoire des données `/var/odoo`

- Copiez le fichier `odoo.conf` (que vous devez remettre).

## Modalités de remise

Votre travail doit être remis au plus tard le **26 avril 2020 à 23h55** par l'intermédiaire de la plateforme [Moodle](https://www.moodle.uqam.ca/). Vous  devez remettre un seul fichier (une archive ZIP) nommé exactement `tp2.zip`, et contenant tous les fichiers suivants:

- `solution-tp2.sh`
- `rapport-tp2.md` (avec des liens vers tous les autres fichiers)
- `paquetsInit.txt`
- `paquetsPython.txt`
- `paquetsPostgreSql.txt`
- `paquetsPrerequis.text`
- `odoo.conf`
- `startodoo.sh`
- `odoo_bd.bak`
- `odoo.tar.gz`
- Les captures d'écrans (.png) des pages de votre site Web suivantes (une capture par page) : 
> - Accueil et article COVID-19,
> - Évenement,
> - Page d'installation des applications 
> - Page des rapports sur odoo
- Toutes les captures d'écrans doivent-être affichées dans `rapport-tp2.md`
- Autres fichiers pertinents éventuels (optionnel)


> Pour les fichiers créés dans le `container` obtenu avec le Dockerfile fourni, on peut 
 les mettre dans le dossier /inf1070 qui a été monté à la création, et on les trouvera 
 dans le dossier où se trouve le script `login.sh`.

<!--
 ~~Une pénalité de **2 points** par heure de retard sera appliquée.~~ Une pénalité 
   eventuelle, en conformité avec les directives de l'université dans le contexte de COVID-19 
   sera appliquée pour les remises hors délai.
-->

**Aucune remise par courriel ne sera acceptée**, peu importe le motif. Plus précisément, si vous envoyez votre travail par courriel, il sera considéré comme non remis. Il est donc de votre responsabilité de vous assurer d'être capable de faire une remise à temps par l'intermédiaire de Moodle.

Si vous êtes en équipe de deux, un seul des deux étudiants doit effectuer la remise sur son compte Moodle.

## Le format Markdown

VOUS DEVEZ écrire votre rapport (rapportTP2.md) dans le format Markdown. La documentation est dispoible sur Internet, par exemple [https://en.support.wordpress.com/markdown-quick-reference/](https://en.support.wordpress.com/markdown-quick-reference/)

## Barème de correction

Le travail est corrigé sur 100 points selon le barème suivant :

- **12.5** points: Les fichiers contenant les listes de paquets et les configurations.
- **5** points: Les captures d'écrans.
- **10** points: contenu de **solution-tp2.sh**
- **62.5** points: le contenu de **rapport-tp2.md**
> * **2.5** points: Identification du système d'exploitation
> * **15** points: Python - Installation et configuration
> * **10** points: PostgreSQL - Installation et configuration
> * **12.5** points: Odoo - Installation et Configuration 
> * **15.0** points: Odoo - Applications et Utilisation
> * **5.0** points:  Sauvegarde et Restauration
> * **2.5** points: Prérequis Linux
- **10** points: Appréciation globale et possibilité de reproduire l'expérience avec les informations 
et fichiers fournis.
