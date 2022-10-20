# NextCloud

## Migrating Server

1- Pare o nextcloud na máquina original e coloque-o em modo Manutenção.
Espere de 6 a 8 minutos até o sincronismo concluir.

sudo -u www-data php occ maintenance:mode --on

2- Copie toda a estrutura do Nextcloud:

rsync -Aavx nextcloud/ nextcloud-dirbkp_`date +"%Y%m%d"`/

3- Exporte o banco de dados:

mysqldump --single-transaction -h [server] -u [username] -p[password] [db_name] > nextcloud-sqlbkp_`date +"%Y%m%d"`.bak

mysqldump --single-transaction --default-character-set=utf8mb4 -h [server] -u [username] -p[password] [db_name] > nextcloud-sqlbkp_`date +"%Y%m%d"`.bak

4- Restaure as pastas no novo endereço:

rsync -vaAxhHt nextcloud-dirbkp/ nextcloud/

5- Restaure o banco de dados:

MySQL is the recommended database engine. To restore MySQL:

mysql -h [server] -u [username] -p[password] -e "DROP DATABASE nextcloud"
mysql -h [server] -u [username] -p[password] -e "CREATE DATABASE nextcloud"
If you use UTF8 with multibyte support (e.g. for emojis in filenames), use:

mysql -h [server] -u [username] -p[password] -e "DROP DATABASE nextcloud"
mysql -h [server] -u [username] -p[password] -e "CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci"

6- Conclua a restauração:

mysql -h [server] -u [username] -p[password] [db_name] < nextcloud-sqlbkp.bak

7- Conferir se um novo Data-Fingerprint em /config/config.php foi gerado. Se não, rode:
maintenance:data-fingerprint

Change /data permissions set as root user: sudo -i

chown www-data:www-data

find /mnt/temp/data -type f -print0 | xargs -0 chmod 0640

find /mnt/temp/data -type d -print0 | xargs -0 chmod 0750
