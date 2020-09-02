_system
=======

let g:pymode_python = 'python3'

:sudo update-alternatives --config editor
sudo update-alternatives --set vim /usr/bin/vim.nox-py2
sudo update-alternatives --set vim /usr/bin/vim.gtk3



== Работа с кодировкой ==
e ++enc=<имя кодировки>         Редактирование файла в ??? кодировке
w ++enc=<имя кодировки>         Сохранить файл в новой кодировке
set fileencodings=utf-8,koi8-r  Список автоматически определяемых
                                  кодировок в порядке убывания
                                  приоритета
