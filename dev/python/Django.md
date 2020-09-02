Django
======

sudo pip install --upgrade pip
sudo pip install virtualenv
virtualenv djangoenv
source ~/djangoenv/bin/activate
pip install mezzanine
cd ~
mezzanine-project newproject
cd newproject
python manage.py createdb
ALLOW_HOSTS
python runserver 0.0.0.0:8000
