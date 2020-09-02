config
======

:color <name>             выбор цветовой схемы. цветвые схемы:
                            /usr/local/share/vim/vim72/colors/*.vim

:set number              включить нумерацию строк
:set nonumber            отключить нумерацию строк

:set [no]wildmenu          При авто-дополнении в командной строке над  ней выводятся возможные варианты
:set list                  Отображать табуляцию и переводы строк

set tabstop=#             для табуляции используется # пробелов
set shiftwidth=#          в командах отступа используется # пробелов 
set [no]expandtab         заменять ли табуляцию на соответствующее число пробелов

:set dictionary=dict	Установить словарь

:ab mail mail@provider.org	Определить mail как сокращение от mail@provider.org


Отступы

:set autoindent	Включить автоматическую расстановку отступов
:set smartindent	Включить “умную” расстановку отступов
:set shiftwidth=4	Установить отступ равный 4 пробелам

:syntax on	Включить подсветку
:syntax off	Выключить подсветку
:set syntax=perl	Установить режим подсветки


== Перенос строк ==
:set wrap                 разрешить word wrap (по умолчанию)
:set nowrap               запретить word wrap


== Печать ==
:ha[rdcopy]                   распечатать документ
:set printoptions=duplex:off  отключить двустороннюю печать
