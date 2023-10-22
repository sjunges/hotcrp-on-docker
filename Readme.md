# HotCRP on Docker

This repository contains scripts and a guide to install docker containers for a hotcrp installation. It is baed on https://github.com/Bramas/hotcrp-docker-compose/ with only documentation updates so far. 

##
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
7. Open `localhost:9001` in a webbrowser and add your account.
8. At the bottom of the page, send account information to your account. You should receive an email. If not, check the log of the mail server:
```
docker compose exec smtp /bin/ls /var/log/maillog
```

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

### Mail server

#### Checking mail log
```
docker compose exec smtp /bin/ls /var/log/maillog 
```

### Phpserver
```
docker-compose exec php /bin/bash
```

### Database
```
docker-compose exec mysql /bin/bash
```
particularly useful if you want to run mysql cli `mysql -proot`

## On setting up SMTP

### Google Mail
At the time of writing, google mail accepts external SMTP usage. However, this does not work with the standard password. Instead, you have to make an app password. 

Other settings:
```
Mandatory: Server address of the SMTP server to use.
SMTP_SERVER=smtp.gmail.com

# Optional: (Default value: 587) Port address of the SMTP server to use.
SMTP_PORT=587

# Mandatory: Username to authenticate with.
SMTP_USERNAME=<YOURACCOUNT>@gmail.com

# Mandatory: Password of the SMTP user.
SMTP_PASSWORD=<YOUR APP PASSOWRD>

# Mandatory: Server hostname for the Postfix container. Emails will appear to come from the hostname's domain.
SERVER_HOSTNAME=gmail.com
```
