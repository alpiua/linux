archivers
=========


    tar -P --transform='s@/my/path/

`--transform` is a simple string replacement parameter. It will strip the path of the files from the archive so the tarball's root becomes the current directory when extracting. Note that you can't use -C option to change directory as you'll lose benefits of find: all files of the directory would be included.

`-P` tells tar to use absolute paths, so it doesn't trigger the warning "Removing leading `/' from member names". Leading '/' with be removed by --transform anyway.