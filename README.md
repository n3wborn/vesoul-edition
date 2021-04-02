# Vesoul Edition

This repo is meant to give a "Docker ready" version of a project I'm working on.
The last is linked to this one as a git submodule which must be "installed" too for
all this to work.

So, to be able to work on this you have to :

1. [Download](https://github.com/n3wborn/vesoul-edition#download-repo-and-submodule) [Repo](https://github.com/n3wborn/vesoul-edition) and [Submodule](https://github.com/n3wborn/vesoul)
2. [Install Dependencies](https://github.com/n3wborn/vesoul-edition#install-dependencies)
3. [Prepare Docker](https://github.com/n3wborn/vesoul-edition#prepare-docker)
4. [Create Database and Fixtures](https://github.com/n3wborn/vesoul-edition#create-database-and-fixtures)
5. [Tests](https://github.com/n3wborn/vesoul-edition#tests)

## [Download Repo and Submodule](https://github.com/n3wborn/vesoul-edition#download-repo-and-submodule)

```bash
# download this project
git clone https://github.com/n3wborn/vesoul-edition.git

# move into it's directory
cd vesoul-edition

# init vesoul submodule
git submodule init

# and download it's content
git submodule update

# move into vesoul directory to be in the right place for what's coming next
cd vesoul
```

So now you have the sources, you have to install dependencies.

**These steps are only needed the very first time you install this project.**

Once done, everything will be done via docker-compose.


## [Install Dependencies](https://github.com/n3wborn/vesoul-edition#install-dependencies)

**Note**:

When you see *$PWD*, replace it with the *current directory*.
So if you're working from /home/user/dev/vesoul-edition/vesoul, you will just
have to replace $PWD with /home/user/dev/vesoul-edition/vesoul.

(If you're feeling lucky, type $PWD and hit tab, if you have a *good* shell it
will do the job for you ;) )


## [Composer](https://github.com/n3wborn/vesoul-edition#composer)

There's many ways to play with Composer using Docker but I would suggest this :

```bash
# from vesoul-edition/vesoul directory:
docker run --rm -ti -v "$PWD:/app" --user $(id -u):$(id -g) composer install
```

Doing this, you wont have problems with rights, every directory/file will be owned
by your user, which avoid many problems.
I also configured Apache the same way. So, the container will run with your user.
Better for dev, better for sec.


## [Yarn](https://github.com/n3wborn/vesoul-edition#yarn)

We now can do the same with Yarn.

```bash
# From vesoul-edition/vesoul directory
docker run --rm --name yarn -u "node" -m "300M" --memory-swap "500M" -v $PWD:/usr/src/app -w /usr/src/app node:14-buster-slim yarn install
```

You can see memory/swap related options. These were required to be as high (?)
for me but maybe you can need a bit less. I would suggest to not change this but
if you know what you're doing...


## [Prepare Docker](https://github.com/n3wborn/vesoul-edition#prepare-docker)

Now, all this is set up, edit Symfony .env file (or .env.local) so that web
server and database can talk to each other and then :

```bash
# build the whole thing and start containers
docker-compose build && docker-compose up
```

First time you do this, you will probably see error messages from docker logs
saying that database isn't created, so let's do it.


## [Create Database and Fixtures](https://github.com/n3wborn/vesoul-edition#create-database-and-fixtures)

Disclaimer : there's a smarter way to do this described below this "basic" one.

To do this, we will enter in our www container and create database, make
migrations and load fixtures (if you want them of course).

```bash
# from vesoul-edition :
docker-compose exec www bash

# from inside your container :
cd vesoul
php bin/console doctrine:database:create
php bin/console make:migration
php bin/console doctrine:migrations:migrate
php bin/console doctrine:fixtures:load
exit
```

You don't want to repeat all this each time, and don't want to.

If you look at composer.json you'll see 2 scripts, prepare and prepare-test.
Thanks to this, it will be easier to prepare dev and test environment.

So, just use **prepare** and **prepare-test** composer scripts :

```bash
# prepare dev environment
docker-compose exec www sh -c cd vesoul; composer prepare

# or test one
docker-compose exec www sh -c cd vesoul; composer prepare-test
```

**Don't forget that you have to configure Symfony environment**.

You can replace DATABASE_URL and MAILER_URL into .env and .env.test if you want.
But you can also override it using .env.local and .env.test.local.
Moreover, these ones are not committed (Symfony add them in .gitignore).

## [Tests](https://github.com/n3wborn/vesoul-edition#tests)

To launch tests :

```bash
# prepare dev environment
docker-compose exec www sh -c cd vesoul; composer prepare

# execute testsuite
docker-compose exec www sh -c "cd vesoul; php bin/phpunit"
```


Well, now you should be ready to go ! Feel free to report anything wrong, or good !


## [Links](https://github.com/n3wborn/vesoul-edition#links)

[Atlassian : git-submodules](https://www.atlassian.com/git/tutorials/git-submodule)

[Get-started with Docker](https://www.docker.com/get-started)

[Play with Docker](https://www.docker.com/play-with-docker)

[Composer Doc](https://getcomposer.org/doc/)

[Yarn Doc](https://classic.yarnpkg.com/en/docs)

[Symfony](https://symfony.com/doc/current/index.html)

[Symfony Fast-Track](https://symfony.com/doc/current/the-fast-track/en/index.html)

Happy coding :)

