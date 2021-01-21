# README.md

## Install'

**To be able to run the project with docker-compose, you have to install front/back dependencies first.
It must be done only once, then docker-compose will do the job.**

Note: $PWD = current directory

### Composer
 
There's many ways to install php dependencies using docker. I would recommend this one.

**From vesoul directory** :

```bash
docker run --rm -ti -v "$PWD:/app" --user $(id -u):$(id -g) composer install
```

### Yarn

```bash
docker run --rm --name yarn -u "node" -m "300M" --memory-swap "500M" -v "$PWD:/usr/src/app" -w /usr/src/app node yarn install
```

### Docker-compose

Now, adapt Symfony .env file so that web server and database can talk to each other and then :

**From vesoul-edition directory**

```
docker-compose build && docker-compose up
```

### Symfony

Now, it's time to create database, make migrations and load fixtures if needed. Inside vesoul-edition folder 

```bash
docker-compose exec www bash
```

Now you have the server's shell, type:

```bash
cd vesoul
php bin/console doctrine:database:create
php bin/console make:migration
php bin/console doctrine:migrations:migrate
php bin/console doctrine:fixtures:load
exit
```

And you're ready to go.
