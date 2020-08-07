# Ganbreeder

[Ganbreeder](https://ganbreeder.app) is a collaborative art tool for discovering images. Images are 'bred' by having children, mixing with other images and being shared via their URL. This is an experiment in using breeding + sharing as methods of exploring high complexity spaces. GAN's are simply the engine enabling this. Ganbreeder is very similar to, and named after, **Picbreeder.**

Ganbreeder uses [these](https://tfhub.dev/deepmind/biggan-128/2) models (BigGAn).

This code was made in a weekend and hasn't been cleaned up or documented yet. There are also improvements to make to scalability.

**Retaken to give the best of install** ..as it's a real hard wordk to understand how to ..coz not enough keys/steps described

## How to use
tentative d'install sous WSL2 (un linux sous windows10) qui possède aussi les drivers gpu ..
mais qui bug lors des pip installs alors que sous powershell c ok (car anaconda ok).

### Prerequisites
c gros mais y aurait bien un docker à installer !!?
* Install Python 3 + pip (for the GAN server) (sous win10: pip a bien ts les requirements)
* Install NodeJS + npm (for the frontend) (installé en 10s avec pip sous powershel .. !! 
 * pip install nodejs puis pip install nodejs npm (module de plus...)
* Install a PostgreSQL server - (par défaut il semble installé dans un docker, ts les fichiers du requirements  servent au server sous docker)
** lequel s'est bine installé avec pip ( pip install postgres )

### requirements.txt recopié:
sous poweshell (win10) j'ai tout installé avec pip :
pip install tensorflow .. pip install tensorflow_hub .. flask .. flask_cors
en bref il suffirait d'avoir le fichier dans son dossier: pip install -r requirements.txt 
qui contient:
Flask==1.0.2   Flask-Cors==3.0.7   tensorflow==1.12.0   tensorflow_hub==0.2.0   scipy==1.1.0   Pillow==5.3.0
je n'ai pas indiqué ces versions mais les dernières à jour ! .. (7aout2020)

### Problèmes rencontrés et résolus .. mais mal notés
* pip upgrade : utiliser simplement : easy_install pip
* docker-compose down pour arrêter et .. rebuild:  docker-compose build puis redémarre: 
### Launch the GAN server
```bash
cd gan_server
# Install dependencies
pip install -r requirements.txt
# And go...
python server.py
```
Your GAN server is available at http://localhost:5000/

### Configure the frontend
For quick hacking, if you have Docker at your disposal, you can spawn a PostgreSQL database like so:
```bash
docker run -p 5432:5432 --name ganbreederpostgres -e POSTGRES_PASSWORD=ganbreederpostgres -d postgres
```
With that simple scenario, the database and user would be `postgres` and the password would be `ganbreederpostgres`

Copy the file `server/example_secrets.js` to `secrets.js` and modify it to fit your environment.

### Launch the frontend
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

Make sure that [docker](https://docs.docker.com/install/) and [docker-compose](https://docs.docker.com/compose/install/) are installed.

Start the containers:
```bash
docker-compose up
```
Your frontend is available at http://localhost:8888/, backend at http://localhost:5000/.
Initial backend setup can take few minutes.

If this is the first time you are running the project you might want to generate some random images:
```bash
docker-compose exec server node make_randoms.js
```
Restart only frontend server (to avoid backend initialization wait):
```bash
docker-compose restart server
```

pour memoire: *It is also inspired by an earlier project of mine [Facebook Graffiti](http://www.joelsimon.net/facebook-graffiti.html) which demonstrated the creative capacity of crowds.*
