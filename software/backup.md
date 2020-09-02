backup
======
## Bacula

<https://www.bacula.org/>

## Borg
The main goal of Borg is to provide an efficient and secure way to backup data. The data deduplication technique used makes Borg suitable for daily backups since only changes are stored. The authenticated encryption technique makes it suitable for backups to not fully trusted targets.

<https://borgbackup.readthedocs.io/en/stable/>

## rdiff-backup
Rdiff-backup backs up one directory to another, possibly over a network. The target directory ends up a copy of the source directory, but extra reverse diffs are stored in a special subdirectory of that target directory, so you can still recover files lost some time ago.  
<https://rdiff-backup.net/>

## rsnapshot
rsnapshot is a filesystem snapshot utility based on rsync. rsnapshot makes it easy to make periodic snapshots of local machines, and remote machines over ssh. The code makes extensive use of hard links whenever possible, to greatly reduce the disk space required.


## fsbackup

Cистема инкрементального резервного копирования и синхронизации ФС.

<http://www.opennet.ru/dev/fsbackup/>

## SHELL

### IBM Docs, ssh keychain usage and more
<https://www.ibm.com/developerworks/ru/library/l-backup/index.html?ca=drs-ru-0113>

### sophisticated bash script
<https://github.com/chuguniy/bash-data-compression>

### simple backup script
<https://habr.com/ru/post/51966/>

### s3-bash
<https://code.google.com/archive/p/s3-bash/>