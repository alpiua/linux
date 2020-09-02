virtualenv
==========

Альтернативы virtualenv

pip -t
1. добавить ./.pip в переменную окружения PYTHONPATH,
2. установить пакеты локально с помощью pip install -t .pip,
3. запускать python из папки проекта.

https://habr.com/ru/post/261263/

```
pipenv
cd yourproject
pipenv --python 3.7
pipenv install Flask gunicorn
pipenv shell | exit
```

https://habr.com/ru/post/413009/

poetry  
https://github.com/python-poetry/poetry