
backup-gmail
======

```sh
#!/bin/sh

##  Backup to Google Drive. Author: Oleksii Pylypchuk 
##  github: https://github.com/alpi-ua/backup-site/
##  
##  This script is designed to upload daily backup of the wordpress site to the google drive
##  In case of fail email is sending to admin via mailx with log attached
##  You should download https://github.com/odeke-em/drive and specify backup.log, email for using this script
##
##  The crontab record is
##  10 5 * * * /path/to/script/backup-wordpress.sh > /path/to/backup.log 2>&1
##  Make sure the owner of the backup.log is the same who is running cron job

DATE_FORMAT='+[%d-%m-%Y] %H:%M:%S'

USER=username

site=site.com
workdir=/home/${USER}/backup
sitedir=/home/${USER}/www

mysqldb=dbname
mysqluser=username
mysqlpass=MYSQLPASS

backuplog=/home/${USER}/logs/${site}.backup.log
email=alert@email.com

backuplog=/home/${USER}/logs/backup_${site}.log
email=swirl7@gmail.com

files="$workdir"/"$site"/"$site"_files.zip
images="$workdir"/"$site"/"$site"_images.zip
database="$workdir"/"$site"/"$site"_database.zip

FREE_SPACE=$(df -k --output=avail "$PWD" | tail -n1)
SITE_SIZE=$(du -s $sitedir | cut -f 1)

### FUNCTIONS
function log() {
echo "$(date '+[%d-%m-%Y] %H:%M:%S') | $1" >> $backuplog
}

function upload() {
log "Uploading $1"
"$workdir"/drive push -ignore-conflict -ignore-name-clashes -quiet $1 > /dev/null
returncode=$?
if [ $returncode -ne 0 ]
  then
    log "-- Error occured. Upload unsuccessful. Return code is "$returncode""
    echo "Error occured while uploading "$1" to google" | mail -s "Error while uploading "$site" backup" -a "$backuplog" "$email"
  else
    log "|_task complete"
  fi
}

function checkjob() {
returncode=$?
if [ $returncode -ne 0 ]
  then
    log "-- Failed to create a "$2" backup. Return code is "$returncode""
    echo "Error backuping "$site" "$2". Program returned code $returncode" | mail -s "Error backuping "$site" "$2"" -a "$backuplog" "$email"
    rm -rf "$3"
  else
    log "|_ New $2 backup created. Old backup deleted if any" && rm -rf "$3".old
fi
}

function movebackup() {
[ -f "$1" ] && log "Moving old "$2" backup" && mv "$1" "$1".old
}

### SCRIPT STARTS
[ -f ${backuplog} ] || touch ${backuplog}
[ -d ${workdir}/${site} ] || mkdir ${workdir}/${site}

[ ${FREE_SPACE} -lt ${SITE_SIZE} ] && rm -rf "$workdir"/"$site/*" && log "Not enough free space. Trying to delete old backups"

[[ $(find "$workdir"/"$site" -type f -mtime -1 | grep "." ) ]] || echo "Backup created more than 24h ago" | mail -s "BACKUP "$site" OUTDATED" "$email"

log "Starting..."

movebackup "$images" "images"
movebackup "$files" "files"
movebackup "$database" "database"

log "Backuping database"
mysqldump -u "$mysqluser" -p"$mysqlpass" "$mysqldb" > "$workdir"/"$site"/"$site".sql

cd "$workdir"/"$site" ; zip -rq "$database" "$site".sql
checkjob "$?" "database" "$database" && rm -rf "$site".sql

log "Archiving files"
cd "$sitedir" ; zip -x=cache/smarty/\* -x=img/p/\* -x=upload/\* -x=var/cache/\* -rq "$files" .
checkjob "$?" "files" "$files"

log "Archiving images"
cd "$sitedir" ; zip -rq "$images" img/p/
checkjob "$?" "images" "$images"

log "Starting upload to the google disk"
[ -f "$files" ] && upload "$files" || log "No files to upload, cancelling"
[ -f "$images" ] && upload "$images" || log "No images to upload, cancelling"
[ -f "$database" ] && upload "$database" || log "No database to upload, cancelling"

echo "=================================================================" >> $backuplog

### IN CASE WE ARE UNDER ROOT
chown -R ${USER}:${USER} ${workdir}
```