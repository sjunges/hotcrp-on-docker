# HotCRP on Docker

1. clone this repository
  ```
    git clone https://github.com/sjunges/hotcrp-on-docker
    cd hotcrp-on-docker
  ```
2. clone hotcrp repository into app directory
  ```
    git clone https://github.com/kohler/hotcrp app
  ```
3. Rename `.env.default` to `.env` and set up the SMTP connection info (We detail how to setup gmail below)
  ```
    cp .env.default .env
    nano .env
  ```
5. Start docker compose
  ```
  docker compose up -d
  ```
5. Initialize the database (or use a backup)
  ```
  docker-compose exec mysql  /bin/sh -c "echo 'ok\nhotcrp\nhotcrppwd\nn\nY\n' | sh /srv/www/api/lib/createdb.sh --user=root --password=root"
  ```
6. Copy the default hotcrp configuration
  ```
  cp hotcrp-options.php app/conf/options.php
  ```
7. Open `localhost:9001`


## More options

* If you want to print the deadline in another timezone (default is HST), edit `docker/php/php.conf` (need to restart) (list of php timezones: https://www.php.net/manual/en/timezones.php)
* To change the port, you can edit `docker-compose.yaml` (need to restart)
* You can change more options in the hotcrp config file: `app/conf/options.php` (no need to restart)
* You can change or add custom php.ini options in `docker/php/php.conf` (need to restart)
* You can change or add custom nginx options in `docker/nginx/default.conf` (need to restart)


## Backups and Restore

Perform a backup:
```
docker-compose exec -T mysql mysqldump -uhotcrp -photcrppwd hotcrp > backup.sql
```

To restore:
```
docker-compose exec -T mysql mysql -uhotcrp -photcrppwd hotcrp < backup.sql
```


## Update Hotcrp
You can update your hotcrp installation just by running `git pull` inside the app folder. See the hotcrp readme for more info.


## Open bash terminal inside a one of the containers

#### Webserver
```
docker-compose exec php /bin/bash
```

#### Database
```
docker-compose exec mysql /bin/bash
```
particularly useful if you want to run mysql cli `mysql -proot`
