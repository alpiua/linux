jpegoptim
=========

пережать изображения в папке (запускать в той папке, в которой нужно пережать)
 find -type f -name "*.jpg" -exec jpegoptim --strip-all --max=90 {} \;