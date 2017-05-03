# Magento with nanobox

## Installation instructions/logs

*Important note*: Install on linux requires re-mounting app directory with `-o nolock` from docker-machine's 'global zone' to avoid flocking issues with the install.

#### Create and cd into work directory
```
mkdir ~/src/nanobox/my-magento
cd ~/src/nanobox/my-magento
```

#### Extract magento to current directory
```
tar -zxf ~/Downloads/Magento-CE-2.1.6*.tar.gz
```

#### Set aside data for network directories
```
mkdir nanobox
#### cp -r app/etc nanobox/etc
cp -r pub/media nanobox/media
cp -r pub/static nanobox/static
```

#### Ensure using this repo's boxfile (or similar)
```
vim boxfile.yml
```

#### Install magento
```
nanobox run
composer install
php bin/magento setup:install \
  --admin-firstname=test \
  --admin-lastname=user \
  --admin-email=lintong@pagodabox.com \
  --admin-user=admin \
  --admin-password=supersecret1234 \
  --base-url=http://magento-test.nanoapp.io/ \
  --backend-frontname=adminTest123 \
  --currency=USD \
  --timezone=America/Denver \
  --use-rewrites=1 \
  --db-host=${DATA_DB_HOST} \
  --db-name=gonano \
  --db-user=${DATA_DB_USER} \
  --db-password=${DATA_DB_PASS} \
  --cleanup-database \
  --session-save=db
exit
```

#### Run and develop locally (while in `nanobox run`)
```sh
php-server # will start apache and fastCGI. your application should be accessible at the container's ip:8080.
```

#### Link local work directory to app on nanobox and deploy
```
nanobox remote add my-magento
nanobox deploy
```

#### Tunnel to db for seeding
```
nanobox tunnel data.db
```

#### In another tab, seed the database. (use password from dashboard->app components->data.db->connect)
```
mysql -h 127.0.0.1 -u nanobox -p$MY_MAGENTO_DB_PASS gonano < gonano.sql
```

#### Console in to your magento app and reindex (if needed) (run from your linked local work directory)
```
nanobox console web.main
php bin/magento indexer:reindex
```

### DISCLAIMER
While I intend on following all of magento best practices, I am in no way a magento (nor php) developer and may make mistakes. I make no guarantee as to the condition of this guide.
