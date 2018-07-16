sudo apt-get update
sudo apt-get install python3
python3-pip nginx git

sudo apt-get update
sudo pip3 install virtualenv
git clone 

cd travqdjango

virtualenv venv
source venv/bin/activate
pip3 install django
pip3 install gunicorn
cd travqdjango
sudo vim settings.py


# Inside settings.py modify these lines allowed host public IP address I for INSERT


ALLOWED_HOSTS = ['<host ip>']

# add the line below to the bottom of the file

STATIC_ROOT = os.path.join(BASE_DIR, "static/")


cd .. 
python manage.py collectstatic
gunicorn --bind 0.0.0.0:8000 travqdemo.wsgi:application

ctrl+c

sudo vim /etc/systemd/system/gunicorn.service

i

[Unit]
Description=gunicorn daemon
After=network.target
[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/travqdjango
ExecStart=/home/ubuntu/travqdjango/venv/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/travqdjango/travqdjango.sock travqdjango.wsgi:application
[Install]
WantedBy=multi-user.target

ESC :wq

sudo systemctl daemon-reload
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
sudo vim /etc/nginx/sites-available/travqdjango

i

server {
  listen 80;
  server_name _travqdjango;
  location = /favicon.ico { access_log off; log_not_found off; }
  location /static/ {
      root /home/ubuntu/travqdjango;
  }
  location / {
      include proxy_params;
      proxy_pass http://unix:/home/ubuntu/travqdjango/travqdjango.sock;
  }
}

ESC :wq

sudo ln -s /etc/nginx/sites-available/travqdjango /etc/nginx/sites-enabled
sudo nginx -t
sudo rm /etc/nginx/sites-enabled/default
sudo service nginx restart
