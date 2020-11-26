backup-yandex
=============
```
#!/bin/bash
MYSQL_SERVER='localhost'

# Юзер, под которым будем делать бекап доступных баз, руту mysql обычно доступны все БД, отдельному пользователю обычно доступна БД конкретного проекта
MYSQL_USER='user'

# Пароль пользователя базы данных (Пароль от рута сервера и от рута mysql разные не путайте)
MYSQL_PASSWORD='pass'

# # # # # # # # # # ОБЩИЕ НАСТРОЙКИ # # # # # # # # # #
    
# Директория для временного хранения бекапов, которые удаляются после отправки на Яндекс.Диск
BACKUP_DIR='/home/site/backup'

# Название проекта, используется в логах и именах архивов
PROJECT='site.com'

# Максимальное количество хранимых на Яндекс.Диске бекапов (0 - хранить все бекапы):

MAX_DAY_BACKUPS='6'
MAX_WEEKS_BACKUPS='4'

# Дата, используется в именах архивов
DATE=`date '+%Y-%m-%d'`
WEEK_DAY=`date '+%w'`
    
# Директории для архивации (указываются через пробел), которые будут помещены в единый архив и отправлены на Яндекс.Диск
DIRS='/home/site/www /etc/{nginx,my.cnf.d,php-fpm.d} /home/photodom/config'

# Yandex.Disk токен (как получить - см. на neblog.info)
TOKEN='AQAAAAAxkMylAAV9e_XJrkPBc0cegm1h4GK_buw'

# Имя лог-файла, хранится в директории, указанной в $BACKUP_DIR
LOGFILE='backup.log'
        
# E-mail для отправки результата выполнения скрипта. Оставьте пустым, если отправлять результаты не требуется.
sendLog='info@photodom.kz'
                
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
                echo -e "Subject: "Backup error"\nFrom: "info@photodom.kz"\nTo: "info@photodom.kz"\nReply-to: "info@photodom.kz"\nContent-Type: text/plain; charset=\"UTF-8\"\n\n"$1 $2"" | msmtp  -C /home/photodom/config/.msmtprc --debug info@photodom.kz
            fi
        else
                echo -e "Subject: "Backup info"\nFrom: "info@photodom.kz"\nTo: "info@photodom.kz"\nReply-to: "info@photodom.kz"\nContent-Type: text/plain; charset=\"UTF-8\"\n\n"$1 $2"" | msmtp  -C /home/photodom/config/.msmtprc --debug info@photodom.kz
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
```