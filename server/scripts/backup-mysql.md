backup-mysql
============
Backup mysql databases on the remote servers to network share

```
#!/bin/bash
CURRENT_DATE=$(date +"%d-%m-%Y-%H%M%S")
SERVER=$(hostname)
INSTANCEHOST=$(</path/to/hosts.txt)
INSTANCEPORT="3306"
MYSQLDUMPFLAGS0="--routines --extended-insert --add-drop-table --force --create-options --master-data=2"
MYSQLDUMPFLAGS1="--no-data --routines --extended-insert --add-drop-table --force --create-options --master-data=2"
MYSQLDUMPFLAGS2="--routines --extended-insert --add-drop-table --force --no-create-info --master-data=2"
MYSQLDUMPFLAGS3="--single-transaction --no-data --no-create-info --routines --skip-triggers"
MYSQLDUMPUSER="backup"о
MYSQLDUMPPASSWD=""
MYSQLUSER="backup"
MYSQLPASSWD=""
BACKUP_PREFIXPATH="/mnt/networkshare"
DUMPER="`which mysqldump`"
MYSQLCLNT="`which mysql`"

if [ ! $# == 1 ]; then
  echo "Usage: $0 backup|restore"
  exit
fi

function backup()
{

mount /mnt/qnap/unix
MOUNT_CHECK=$?

let WAIT_FOR_MYSQL=0

while [ $MOUNT_CHECK ] && [ $(pidof mysql) ]
do
  sleep 60
  [ $WAIT_FOR_MYSQL -gt 3600 ] && echo "MYSQL SHARE BLOCKED AT ${SERVERNAME}. MYSQL IS ALREADY ACTIVE" | mailx -s "Backup script failed" it-unixsys@fozzy.ua && exit 1
  let WAIT_FOR_MYSQL+=60
  (( $WAIT_FOR_MYSQL % 600 )) || echo "$(date +"%H%M%S") Stil waiting..."  
done

echo "Starting backup"

for i in $INSTANCEHOST; do
  if [ -d $BACKUP_PREFIXPATH ]; then
      cd $BACKUP_PREFIXPATH
  else
      echo "Where's network share?"
      exit 1
  fi
BACKUP_NAME="$i-DB"
BACKUP_PATH="./$i/$CURRENT_DATE"
mkdir -p $BACKUP_PATH || echo "WTF with the rights?!"
      MYLIST=$(${MYSQLCLNT} --host=$i --port=${INSTANCEPORT} --user=$MYSQLUSER --password=$MYSQLPASSWD -s -r -e "SHOW DATABASES;" |egrep -v ^'information_schema|performance_schema|mysql|sys|Database') || echo "Something went wrong, sorry."
      for CURRENTDB in $MYLIST; do
            echo "Dumping Database ${CURRENTDB}..."
            ${DUMPER} --host=$i --port=${INSTANCEPORT} --user=$MYSQLDUMPUSER --password=$MYSQLDUMPPASSWD ${MYSQLDUMPFLAGS0} --databases ${CURRENTDB} > $BACKUP_PATH/${BACKUP_NAME}-${CURRENTDB}.sql || exit 1
            ${DUMPER} --host=$i --port=${INSTANCEPORT} --user=$MYSQLDUMPUSER --password=$MYSQLDUMPPASSWD ${MYSQLDUMPFLAGS1} --databases ${CURRENTDB} > $BACKUP_PATH/${BACKUP_NAME}-${CURRENTDB}-schema.sql || exit 1
            ${DUMPER} --host=$i --port=${INSTANCEPORT} --user=$MYSQLDUMPUSER --password=$MYSQLDUMPPASSWD ${MYSQLDUMPFLAGS2} --databases ${CURRENTDB} > $BACKUP_PATH/${BACKUP_NAME}-${CURRENTDB}-dataonly.sql || exit 1
            ${DUMPER} --host=$i --port=${INSTANCEPORT} --user=$MYSQLDUMPUSER --password=$MYSQLDUMPPASSWD ${MYSQLDUMPFLAGS3} --databases ${CURRENTDB} > $BACKUP_PATH/${BACKUP_NAME}-${CURRENTDB}-routines.sql || exit 1
      done

BACKUP_REMOVEDIR="./$i" && \
  if [ -d $BACKUP_REMOVEDIR ]; then
                  cd $BACKUP_REMOVEDIR/ && \
                  rm -rf $(ls -1t |tail -n +3)
  else
      echo "Where are directories to remove ?!"
      exit 1
  fi
done
unset INSTANCEHOST
echo "Backup session has finished successfully at `date`" && cd ~/; umount /mnt/qnap/unix || umount -f /mnt/qnap/unix

}

function restore()
{
echo "This one isn't implemented yet."
}
```