files
=======
myFile = open("new.txt", "w+")
myFile.close()

**File Opening Modes**

    w: Opens a file for writing and creates a new file if it doesn't yet exist. In the case that the file does exist, it overwrites it.
    w+: Opens a file for writing but also for reading and creating it if it doesn't exist. If a file already exists, it overwrites it.
    r: Opens a file for reading only.
    rb: Opens a file for reading in Binary format.
    wb: Opens a file for writing in Binary format.
    wb+: Opens a file for writing and reading in Binary format.
    a: Opens a file for appending at the end of the file.
    +: In general, this character is used along side r, w, or a and means both writing and reading.


**rename**  

    from os import rename
    rename('oldname','newname')
    
    from shutil import move
    move('/path/1', 'path/2')

**remove**

    from os import remove
    remove('file')

**zip**

    from zipfile import ZipFile
    zf = ZipFile('path_to_file/file.zip', 'r')
    zf.extractall('path_to_extract_folder')
    zf.close()