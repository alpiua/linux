snippets
==

### pv - the progress bar
pv mydump.sql.gz | gunzip | mysql -u root -p basename

### inode economy while moving files
*creates hardlinks*

cp -rl ./source ./destination && rm -rf ./source

### permissions
find . -type d -exec chmod 755 -- {} + 
find . -type f -exec chmod 644 -- {} + 

### creating directories from file
cat foo.txt | xargs -I % sh -c 'echo %; mkdir %'

### copying over network

tar -c file | ssh server.com "cd && tar -x"

rsync -avv --delete-during -compress-level=9 -e "ssh -p remote_ssh_port" user@host:/dir/to/foobar_src foobar_dst/

zip -r - ./cfg/ | ssh user@server.com "cd ~; cat > cfg.zip"