tips
========

### Remove ^M from the end of line

:e ++ff=do:e ++ff=dos 

The :e ++ff=dos command tells Vim to read the file again, forcing  dos file format. Vim will remove CRLF and LF-only line endings, leaving  only the text of each line in the buffer.

then 

:set ff=unix 
and finally 
:wq 

:s/^M$//

(Press Ctrl+V Ctrl+M to insert that ^M.)



**Как пользоваться vim**  
http://najomi.org/vim


**DATABASE QUERY**  
https://habamax.github.io/2019/09/02/use-vim-dadbod-to-query-databases.html


**vim-git**
https://itnan.ru/post.php?c=1&p=261783






Вставить  символ в начало большого количества подряд идущих строк:

Вариант 1

Use Ctrl+v to select the first column of text in the lines you want to comment.

Then hit 'I' and type the text you want to insert.

Then hit 'Esc', wait 1 second and the inserted text will appear on every line.

Вариант 2
This replaces the beginning of each line with "//":


:%s!^!//!

This replaces the beginning of each selected line (use visual mode to select) with "//":


:'<,'>s!^!//! 

---
What if you’ve forgot to give sudo when you’ve opened the /etc/group file as shown below? In this case, instead of coming out of the file (and loosing all your changes) and executing the vim command with sudo, you can do the following.

$ vim /etc/group




Использование стиля “подсветил — посмотрел — выполнил” совместно с визуальным режимом оказалось очень удобной практикой. Такое комбинирование стилей выделения используется при решении задач типа “в данной функции переименовать переменную foo в bar” и подобных. Такая (и подобные) задачи решаются последовательностью действий:

Подсвечиваем foo командой *

Переходим в режим визуального выделения и выделяем текущую функцию

Отдаем команду замены :’<,’>s//bar/g


Символы ’<,’>, означающие начало и конец текущего выделенного блока, и определяющие диапазон применения команды :s, Vim подставляет автоматически при отдаче любой команды из режима визуального выделения. Также опущен первый аргумент команды :s, т. к. набирать его нет необходимости — когда он опущен, Vim использует в качестве этого аргумента содержимое регистра текущего поиска. То есть именно то, что подсвечено желтым.

:g//t$ — скопировать строки, содержащие подсвеченное значение, в конец файла. Если, например, надо быстро понять, как глобальная переменная (sic! — а что делать, в legacy-коде они встречаются) используется в модуле.

:g//d — удалить строки, содержащие подсвеченное значение
:g!//d — удалить стрки, НЕ содержащие подсвеченного значения

Заменить каждое вхождение нескольких пустых строк на одну пустую строку (чтобы между параграфами стал одинаковый промежуток в одну линию):
:v/./,/./-j



Раздвинуть подряд идущие строки (обратное предыдущему действие, каждая строка станет параграфом)
Нужно при форматировании текста под 76 символов, из формата, как его сохраняет Word, когда каждый абзац становится строкой в текстовом файле.
:'<,'>s/$/\r/g


