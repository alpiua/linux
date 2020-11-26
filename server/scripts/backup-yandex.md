backup-yandex
=============
```bash
# # # # # # # # # # ОБЩИЕ НАСТРОЙКИ # # # # # # # # # #

# Название проекта, используется в логах и именах архивов.
# Имя сайта
PROJECT='site'

#!/bin/bash
MYSQL_SERVER='localhost'

# Юзер, под которым будем делать бекап доступных баз, руту mysql обычно доступны все БД, отдельному пользователю обычно доступна БД конкретного проекта
MYSQL_USER='$PROJECT'

# Пароль пользователя базы данных (Пароль от рута сервера и от рута mysql разные не путайте)
MYSQL_PASSWORD='pass'
    
# Директория для временного хранения бекапов, которые удаляются после отправки на Яндекс.Диск
BACKUP_DIR='/home/$PROJECT/backup'

# Максимальное количество хранимых на Яндекс.Диске бекапов (0 - хранить все бекапы):

MAX_DAY_BACKUPS='6'
MAX_WEEKS_BACKUPS='4'

# Дата, используется в именах архивов
DATE=`date '+%Y-%m-%d'`
WEEK_DAY=`date '+%w'`

# Конфиг для отправки почты
MSMTP=/home/$PROJECT/config/.msmtprc
    
# Директории для архивации (указываются через пробел), которые будут помещены в единый архив и отправлены на Яндекс.Диск
DIRS='/home/$PROJECT/www /etc/{nginx,my.cnf.d,php-fpm.d} /home/$PROJECT/config'

# Yandex.Disk токен (как получить - см. на neblog.info)
TOKEN='token'

# Имя лог-файла, хранится в директории, указанной в $BACKUP_DIR
LOGFILE='backup.log'
        
# E-mail для отправки результата выполнения скрипта. Оставьте пустым, если отправлять результаты не требуется.
sendLog='info@site.com'
                
# Отправлять только ошибки (true). Укажите false, если нужно отправлять логи при любом результате выполнения скрипта.
sendLogErrorsOnly='true'
                
        
# # # # # # # # # # КОНЕЦ НАСТРОЕК # # # # # # # # # # # # #
# # # # # # # # ДАЛЬШЕ НИЧЕГО НЕ МЕНЯЕМ! # # # # # # # # # #
  
function mailing()
{
    if [ ! $sendLog = '' ];then
        if [ "$sendLogErrorsOnly" == true ];
        then
            if echo "$1" | grep -q 'error'
            then
                echo -e "Subject: "Backup error"\nFrom: "$sendLog"\nTo: "$sendLog"\nReply-to: "$sendLog"\nContent-Type: text/plain; charset=\"UTF-8\"\n\n"$1 $2"" | msmtp  -C $MSMTP --debug $sendLog
            fi
        else
                echo -e "Subject: "Backup info"\nFrom: "$sendLog"\nTo: "$sendLog"\nReply-to: "$sendLog"\nContent-Type: text/plain; charset=\"UTF-8\"\n\n"$1 $2"" | msmtp  -C $MSMTP --debug $sendLog
        fi
    fi
}

function logger()
{
    echo "["`date "+%Y-%m-%d %H:%M:%S"`"] File $BACKUP_DIR: $1" >> $BACKUP_DIR/$LOGFILE
}

function parseJson()
{
    local output
    regex="(\"$1\":[\"]?)([^\",\}]+)([\"]?)"
    [[ $2 =~ $regex ]] && output=${BASH_REMATCH[2]}
    echo $output
}
    
function checkError()
{
    echo $(parseJson 'error' "$1")
}

function getUploadUrl()
{
    json_out=`curl -s -H "Authorization: OAuth $TOKEN" https://cloud-api.yandex.net:443/v1/disk/resources/upload/?path=app:/$FOLDER/$backupName&overwrite=true`
    json_error=$(checkError "$json_out")
    if [[ $json_error != '' ]];
    then
        logger "$PROJECT - Yandex.Disk error: $json_error"
        mailing "$PROJECT - Yandex.Disk backup error" "ERROR copy file $FILENAME. Yandex.Disk error: $json_error"
#    echo ''
    else
        output=$(parseJson 'href' $json_out)
        echo $output
    fi
}

function uploadFile
{
    local json_out
    local uploadUrl
    local json_error
    uploadUrl=$(getUploadUrl)
    if [[ $uploadUrl != '' ]];
    then
    echo $UploadUrl
        json_out=`curl -s -T $1 -H "Authorization: OAuth $TOKEN" $uploadUrl`
        json_error=$(checkError "$json_out")
    if [[ $json_error != '' ]];
    then
        logger "$PROJECT - Yandex.Disk error: $json_error"
        mailing "$PROJECT - Yandex.Disk backup error" "ERROR copy file $FILENAME. Yandex.Disk error: $json_error"


    else
        logger "$PROJECT - Copying file to Yandex.Disk success"
        mailing "$PROJECT - Yandex.Disk backup success" "SUCCESS copy file $FILENAME"

    fi
    else
        echo 'Some errors occured. Check log file for detail'
    fi
}

function backups_list() {
    # Ищем в директории приложения все файлы бекапов и выводим их названия:
    curl -s -H "Authorization: OAuth $TOKEN" "https://cloud-api.yandex.net:443/v1/disk/resources?path=app:/$FOLDER/&sort=created&limit=100" | tr "{},[]" "\n" | grep "name\":[[:graph:]]*.tar.gz" | cut -d: -f 2 | tr -d '"'
}

function backups_count() {
    local bkps=$(backups_list | wc -l)
    # Если мы бекапим и файлы, и БД, то на 1 бекап у нас приходится 2 файла. Поэтому количество бекапов = количество файлов / 2:
    expr $bkps / 2
}

function remove_old_backups() {
    bkps=$(backups_count)
    old_bkps=$((bkps - MAX_BACKUPS))
    if [ "$old_bkps" -gt "0" ];then
        logger "Удаляем старые бекапы с Яндекс.Диска"
        # Цикл удаления старых бекапов:
        # Выполняем удаление первого в списке файла 2*old_bkps раз
        for i in `eval echo {1..$((old_bkps * 2))}`; do
            curl -X DELETE -s -H "Authorization: OAuth $TOKEN" "https://cloud-api.yandex.net:443/v1/disk/resources?path=app:/$FOLDER/$(backups_list | awk '(NR == 1)')&permanently=true"
        done
    fi
}

logger "--- $PROJECT START BACKUP $DATE ---"
logger "Удаляем архивы с диска"
find $BACKUP_DIR -type f -name "*.gz" -exec rm '{}' \;

if [ $WEEK_DAY -eq 1 ]
then
  FOLDER="Week"
  echo "sunday"
   logger "Week backup!"
   MAX_BACKUPS=$MAX_WEEKS_BACKUPS
else
  FOLDER="Day"
  echo "not Sunday"
  logger "Common day backup"
  MAX_BACKUPS=$MAX_DAY_BACKUPS
fi

logger "Выгружаем дампы баз"
mkdir $BACKUP_DIR/$DATE
#for i in `mysql -h $MYSQL_SERVER -u $MYSQL_USER -p$MYSQL_PASSWORD -e'show databases;' | grep -v information_schema | grep -v Database`;
#    do mysqldump -h $MYSQL_SERVER -u $MYSQL_USER -p$MYSQL_PASSWORD $i > $BACKUP_DIR/$DATE/$i.sql;
#done
mysqldump -h $MYSQL_SERVER -u $MYSQL_USER -p$MYSQL_PASSWORD main > $BACKUP_DIR/$DATE/$PROJECT.sql

logger "Создаем архив mysql $BACKUP_DIR/$DATE-mysql-$PROJECT.tar.gz"
tar -czf $BACKUP_DIR/$DATE-mysql-$PROJECT.tar.gz $BACKUP_DIR/$DATE
rm -rf $BACKUP_DIR/$DATE

logger "Создаем архив каталогов $BACKUP_DIR/$DATE-files-$PROJECT.tar.gz"
tar -czf $BACKUP_DIR/$DATE-files-$PROJECT.tar.gz $DIRS

FILENAME=$DATE-mysql-$PROJECT.tar.gz
logger "Выгружаем на Яндекс.Диск архив mysql $BACKUP_DIR/$DATE-mysql-$PROJECT.tar.gz"
backupName=$DATE-mysql-$PROJECT.tar.gz
uploadFile $BACKUP_DIR/$DATE-mysql-$PROJECT.tar.gz

FILENAME=$DATE-files-$PROJECT.tar.gz
logger "Выгружаем на Яндекс.Диск архив с файлами $BACKUP_DIR/$DATE-files-$PROJECT.tar.gz"
backupName=$DATE-files-$PROJECT.tar.gz
uploadFile $BACKUP_DIR/$DATE-files-$PROJECT.tar.gz

#logger "Удаляем архивы с диска"
#find $BACKUP_DIR -type f -name "*.gz" -exec rm '{}' \;

# Удаляем старые бекапы с Яндекс.Диска (если MAX_BACKUPS > 0)
if [ $MAX_BACKUPS -gt 0 ];then remove_old_backups; fi


logger "Завершение скрипта бекапа"
```

### настройки .msmtprc
```
defaults
tls on
tls_certcheck off
tls_starttls on
#tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile /home/site/logs/msmtp.log

account default
host smtp.mail.ru
port 587
protocol smtp
auth on
from info@site.com
user info@site.com
password PASSWRD
```