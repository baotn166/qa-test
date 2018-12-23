# Answers for DevOps Engineer tests
## Establishing a Development environment
### Preriquisites
These applications must be installed before running the project
- Git (see [Git installation guideline](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))
  > __Note:__ If you are using Windows, make sure that you install Git Bash
- Docker
  - For [Linux](https://docs.docker.com/install/linux/docker-ce/centos/)
  - For [MacOS](https://docs.docker.com/docker-for-mac/install/)
  - For [Windows](https://docs.docker.com/docker-for-windows/install/)

### Setup environment
- This project is using Laravel framework so that we will use Laradock to setup a development environment
- Ensure that you are in the project root directory. You can check this by running the command
- At the project root directory, run this command
```bash
$ git submodule add https://github.com/Laradock/laradock.git
$ cd laradock
```
- Change directory to `laradock` folder and rename `env.example` to `.env`
```bash
$ cd laradock
$ cp env.example .env
```
- Create your laradock containers
```bash
docker-compose up -d nginx mysql
```
> __Note:__ More containers are available, find them in the docs or the docker-compose.yml file.
- Go to your workspace
```bash
$ docker-compose exec workspace bash
```
- Install and configure laravel
```bash
$ root@workspace:/var/www# composer install
$ root@workspace:/var/www# cp .env.example .env
$ root@workspace:/var/www# php artisan key:generate
$ root@workspace:/var/www# php artisan migrate --seed
$ root@workspace:/var/www# exit
$ cd ..
$ sudo chmod -R 777 storage bootstrap/cache
```
- Change the `.env` in the project directory like this
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```
- Save the file and check your website at `http://localhost`

## Define a Build process

> Can you plan a build?

Steps to build the project:
```bash
$ rm -rf dist
$ cd laradock
$ docker-compose exec workspace composer install
$ cd ..
$ mkdir -p dist
$ cp -R app bootstrap public routes storage vendor .env dist
$ cd dist
$ chmod 777 storage bootstrap/cache
$ cd ..
$ tar -zcf todo-app-build.tgz build/
```

> Can you make this package has small and/or fast to download, as well as efficient on the loading time

Sure thing! We can optimize autoloader, configuration loading, route loading by these commands:

```bash
$ cd laradock
$ docker-compose exec workspace composer install --optimize-autoloader --no-dev
$ docker-compose exec workspace php artisan config:cache
$ docker-compose exec workspace php artisan route:cache
```

## Define a Release process

> Our app has some tests set, unit, feature and e2e. Can you set up a tool that runs them? 

Sure thing! Please check `.travis.yml` file for that or you can check the status of CI on travis.org