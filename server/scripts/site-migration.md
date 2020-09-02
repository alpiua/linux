site-migration
==============
require root
```bash
# LOCAL_SERVER
LOCALDIR=/home/www

SITE1=
DBN1=
DBU1=
DBP1=

#REMOTE_SERVER
REMOTEDIR=/home/user
CHUSER=user
HOST=1.1.1.1
PORT=22
USER=root

#SITE_MOVEMENT
[[ ! -d ${LOCALDIR}/sql ]] || mkdir ${LOCALDIR}/sql

mysqldump --add-drop-table --single-transaction -u ${DBU1} -p${DBP1} ${DBN1} > ${LOCALDIR}/sql/${DBN1}.sql

rsync -av --delete-during -compress-level=9 --chown=${CHUSER}:${CHUSER} -e "ssh -p ${PORT}" ${LOCALDIR}/{$SITE1} ${USER}@${HOST}:${REMOTEDIR}/www/${SITE1}

rsync -av -compress-level=9 -e "ssh -p ${PORT}" ${LOCALDIR}/sql/ ${USER}${HOST}:${REMOTEDIR}/sql/
rsync -av -compress-level=9 -e "ssh -p ${PORT}" /etc/ ${USER}@${HOST}:${REMOTEDIR}/etc/
```
