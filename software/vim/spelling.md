spelling
========
Проверка орфографии

    mkdir -p ~/.vim/spell
    cd ~/.vim/spell
    wget http://ftp.vim.org/vim/runtime/spell/ru.koi8-r.sug
    wget http://ftp.vim.org/vim/runtime/spell/ru.koi8-r.spl
    wget http://ftp.vim.org/vim/runtime/spell/en.ascii.sug
    wget http://ftp.vim.org/vim/runtime/spell/en.ascii.spl


:set spell spelllang=ru,en       включить проверку орфографии
:set nospell                     выключить проверку орфографии
]s                               следующее слово с ошибкой
[s                               предыдущее слово с ошибкой
z=                               замена слова на альтернативу из списка
zg                               good word
zw                               wrong word
zG                               ignore word
