# Ganbreeder
[Ganbreeder](https://ganbreeder.app) is a collaborative art tool for discovering images. Images are 'bred' by having children, mixing with other images and being shared via their URL. This is an experiment in using breeding + sharing as methods of exploring high complexity spaces. GAN's are simply the engine enabling this. Ganbreeder is very similar to, and named after, **Picbreeder.**
Ganbreeder uses [these](https://tfhub.dev/deepmind/biggan-128/2) models (BigGAn).

**Retaken to give the best of install** ..as it's a real hard wordk to understand how to ..coz not enough keys/steps described
Pour l'instant le **docker (sous powershell) fait bien démarrer la base de données**, mais le calcul ne trouve pas les bons ports.
Ensuite le localhost est refusé. Du coup, pas de frontend ni backend.
Mais j'avais chgé les ports (5000-> 5566 et 8888 -> 8877) et ça semblait donner une image (que je n'ai pu ressortir) -23082020

## How to use
tentative d'install sous WSL2 (un linux sous windows10) qui possède aussi les drivers gpu ..
mais qui bug lors des pip installs alors que sous powershell c ok (car anaconda ok).

### Prerequisites
 * docker à installer facilement sous win10PRO, ... il contient le docker-compose aussi (pas sous linux).
* Install Python 3 + pip (for the GAN server) (sous win10: pip a bien ts les requirements)
* Install NodeJS + npm (for the frontend) (installé en 10s avec pip sous powershel .. !! ---> voir comment nodejs démarre une page ?
* pip install nodejs puis pip install nodejs npm (module de plus...)
* Install a PostgreSQL server - (par défaut il semble installé dans un docker, ts les fichiers du requirements  servent au server sous docker)
** lequel s'est bien installé avec pip ( pip install postgres )

### requirements.txt recopié:
sous poweshell (win10) j'ai tout installé avec pip :
pip install tensorflow .. pip install tensorflow_hub .. flask .. flask_cors
en bref il suffirait d'avoir le fichier dans son dossier: pip install -r requirements.txt 
qui contient:
Flask==1.0.2   Flask-Cors==3.0.7   tensorflow==1.12.0   tensorflow_hub==0.2.0   scipy==1.1.0   Pillow==5.3.0
je n'ai pas indiqué ces versions mais les dernières à jour ! .. (7aout2020)
* seulemnt ensuite c'est sous docker-compose que ça va chercher le requirements.txt et s'installer (au moment du docker-compose build)

### Start it !
* 1 commencer (peut-être) dans le rép \server powershell: docker-compose build
* 1b on démarre un essai avec  dans le rép \server powershell: docker-compose up 
* 2 - ensuite ouverture d'une seconde console.. powershel avec docker-compose exec server node make_randoms.js
* et **qd installé** : on peut reprendre avec docker-compose restart server
* il va chercher à partir du fichier /server/save_results.js un serveur AWS (amazon ??) S3 .. ?? --> vérifier cette partie dite publique !
* et pour le coup, une image est récupérée sur le serveur https://s3.amazonaws.com/ganbreederpublic ! depuis server.js (voir la modif (enlever du gitignore)
* **stopper** avec   -   docker-compose down ce fichier est accessible et contient 
* aussi docker-compose up -d    starts the containers in the background and leaves them running.

```bash 
/ganbreederpublic contient: 
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<Name>ganbreederpublic</Name>
<Prefix/>
<Marker/>
<MaxKeys>1000</MaxKeys>
<IsTruncated>true</IsTruncated>
<Contents>
<Key>data/0001d1905cb3e3acfcde.json</Key>
<LastModified>2020-02-04T00:38:50.000Z</LastModified>
<ETag>"384ed348a6758a87bc03330ab9860fcd"</ETag>
<Size>49342</Size>
<StorageClass>STANDARD</StorageClass>
</Contents>
... plusieurs fois le paquet <contents> puis se ferme avec </ListBucketResult>
```



### Problèmes rencontrés et résolus .. mais mal notés
* pip upgrade : utiliser simplement : easy_install pip
* docker-compose down pour arrêter et .. rebuild:  docker-compose build puis redémarre: 
* aussi dans le docker-compose.YAML ..   # changer  les ports écrit en double - "5432:5432"  en "5432" (vu sur un forum ...
* all done .. *donc le programme a bien tourné ..mais l'image n'est pas là !? --> vérif si en répertoire ...\ganbreeder\server\public\img ? y a des images là.*

###vu le gitignore.. et cette adresse: https://github.com/AkoZ/ganbreeder/commit/ff833751e87f16ca43701cf924c8ed4f348e4b2e#diff-bc37d034bad564583790a46f19d807abfe519c5671395fd494d8cce506c42947 qui permet de voir tous les fichiers modifiés (enlèvement et/ou ajout et en tout cas enlève la partie S3 !.. ;)

### sous linux: Launch the GAN server
```bash
cd gan_server
# Install dependencies
pip install -r requirements.txt
# And go...
python server.py
```
Your GAN server is available at http://localhost:5000/ 
*et 127.0.0.1 est l'adresse de protocole Internet (IP) de bouclage, également appelée «localhost».*
* si le localhost donne une err-connection_refused: I have changed everything from .localhost to .local and everything works as normal.*

### Erreurs recontrées sour powershell (j)
...\ganbreeder\gan_server> python server.py
```bash
Traceback (most recent call last):
  File "server.py", line 6, in <module>
    import tensorflow as tf
  File "\Anaconda3\lib\site-packages\tensorflow\__init__.py", line 28, in <module>
    from tensorflow.python import pywrap_tensorflow  # pylint: disable=unused-import
  File "\Anaconda3\lib\site-packages\tensorflow\python\__init__.py", line 47, in <module>
    import numpy as np
  File "\Anaconda3\lib\site-packages\numpy\__init__.py", line 140, in <module>
    from . import _distributor_init
  File "\Anaconda3\lib\site-packages\numpy\_distributor_init.py", **line 34**, in <module>
    from . **import _mklinit**
ImportError: DLL load failed: Le module spécifié est introuvable. trouver quel dll ?
```

* Configure the frontend
For quick hacking, with Docker, you can spawn a PostgreSQL database like so:
ici vérif si on doit laisser le double port 5432:5432 ? et si rien fnctionne tenter avec QUE 5432
```bash
docker run -p 5432:5432 --name ganbreederpostgres -e POSTGRES_PASSWORD=ganbreederpostgres -d postgres
```
With that simple scenario, the database and user would be `postgres` and the password would be `ganbreederpostgres`
Copy the file `server/example_secrets.js` to `secrets.js` and modify it to fit your environment.

* Launch the frontend
```bash
cd server
npm install
# Create the database structure
node_modules/knex/bin/cli.js migrate:latest
# Generate the first images
node make_randoms.js
# Generate a cache of image keys for the front page (do it every time you want to update the front page)
node updatecache.js
# And go...
node server.js
```
Your frontend is available at http://localhost:8888/

### docker-compose setup

Make sure that [docker](https://docs.docker.com/install/) 
and [docker-compose](https://docs.docker.com/compose/install/) are installed.
**Start the containers:**
```bash
docker-compose up
```
Your frontend is available at http://localhost:8888/,
backend at http://localhost:5000/.
Initial backend setup can take few minutes.

If this is the first time you are running the project you might want to generate some random images:
```bash
docker-compose exec server node make_randoms.js
```
Restart only frontend server (to avoid backend initialization wait):
```bash
docker-compose restart server
```

pour memoire: 
* There are also improvements to make to scalability.
* It is also inspired by an earlier project [Facebook Graffiti](http://www.joelsimon.net/facebook-graffiti.html) which demonstrated the creative capacity of crowds.*
