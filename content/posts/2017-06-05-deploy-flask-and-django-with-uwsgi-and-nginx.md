---
layout: post
title: "使用nginx+uwsgi部署Flask和Django"
date: 2017-06-05 22:22:22
category: 部署
tags:
  - Django
  - Flask
  - 技术
  - Nginx
  - uWSGI
---

# 使用nginx+uwsgi部署Flask和Django

## Flask

将nginx目录下的`uwsgi_params`文件复制一份到工程目录下。可根据需求对齐进行修改。

```shell
cp /etc/nginx/uwsgi_params /path/to/your/project/root/uwsgi_params
```

在工程目录下新建app的uwsgi配置文件，如下。修改其中`chdir`、`socket`、`daemonize`、`pidfile`、`touch-reload`、`wsgi-file`、`callable`选项。其中`touch-reload`是指通过touch命令touch一个文件从而实现重启服务，可以按喜好设置，这里设置为配置文件。

```ini
# myapp_uwsgi_config.ini file
[uwsgi]
# the base directory (full path)
chdir           = /path/to/your/project/root
# the virtualenv (full path)
#home            = /path/to/virtualenv

# process-related settings
# master
master          = true
# maximum number of worker processes
processes       = 4
threads         = 2
# the socket (use the full path to be safe)
socket          = /var/run/uwsgi/sock/myapp.sock
# ... with appropriate permissions - may be needed
chmod-socket    = 666
# clear environment on exit
vacuum          = true
# background the process & log
daemonize = /var/log/uwsgi/myapp.log

pidfile = /var/run/uwsgi/myapp.pid

touch-reload    = /path/to/this/config/file

wsgi-file       = myapp.py
callable        = app

wsgi-disable-file-wrapper = true
```

在工程目录下新建nginx.conf配置文件，如下。修改其中的端口号`listen`、``uwsgi_pass`和`include`选项。

```nginx
# configuration of the server
server {
    # the port your site will be served on
    listen      5001;
    # the domain name it will serve for
    server_name example.com; # substitute your machine's IP address or FQDN
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to taste

    location / {
        uwsgi_pass  unix:///var/run/uwsgi/sock/myapp.sock;
        include     /path/to/your/project/root/uwsgi_params; # the uwsgi_params file you installed
    }
}
```

将nginx.conf配置文件链接到nginx的sites-enabled目录下。（如果没有这个目录，则创建一个，然后将其添加到nginx的搜索路径）

```shell
ln -s /path/to/your/project/root/nginx.conf /etc/nginx/sites-enabled/myapp_nginx.conf
```

使用uwsgi拉起flask服务

```shell
uwsgi --ini myapp_uwsgi_config.ini
```

重新载入或者重启nginx服务器

```shell
service nginx reload;  # or service nginx restart
```

至此设置完毕，可以进入`<your server>:5001`看看是否正常。

## Django

将nginx目录下的`uwsgi_params`文件复制一份到工程目录下。可根据需求对齐进行修改。

```shell
cp /etc/nginx/uwsgi_params /path/to/your/project/root/uwsgi_params
```

在工程目录下新建app的uwsgi配置文件，如下。修改其中`chdir`、`module`、`socket`、`daemonize`、`pidfile`、`touch-reload`选项。其中的`module`是指工程根目录下与工程同名目录即工程设置目录下的`wsgi.py`文件，这里设置为能通过module路径访问的方式。其中`touch-reload`是指通过touch命令touch一个文件从而实现重启服务，可以按喜好设置，这里设置为配置文件。

```ini
# myapp_uwsgi_config.ini file
[uwsgi]

# Django-related settings
# the base directory (full path)
chdir           = /part/to/your/project/root
# Django's wsgi file
module          = myapp.wsgi
# the virtualenv (full path)
#home            = /path/to/virtualenv

# process-related settings
# master
master          = true
# maximum number of worker processes
processes       = 10
# the socket (use the full path to be safe
socket          = /part/to/your/project/root/myapp.sock
# ... with appropriate permissions - may be needed
chmod-socket    = 666
# clear environment on exit
vacuum          = true

# background the process & log
daemonize = /var/log/uwsgi/myapp.log

pidfile = /run/uwsgi/myapp-master.pid

touch-reload    = /path/to/this/config/file
```

在工程目录下新建nginx.conf配置文件，如下。修改其中的端口号`listen`、``uwsgi_pass`和`include`选项。

```nginx
# myapp_nginx.conf

# the upstream component nginx needs to connect to
upstream django {
    server unix:///part/to/your/project/root/myapp.sock; # for a file socket
    # server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}
# configuration of the server
server {
    # the port your site will be served on
    listen      8001;
    # the domain name it will serve for
    server_name .example.com; # substitute your machine's IP address or FQDN
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /part/to/your/project/root/media;  # your Django project's media files - amend as required
    }

    location /static {
        alias /part/to/your/project/root/static; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     /part/to/your/project/root/uwsgi_params; # the uwsgi_params file you installed
    }
}
```

将nginx.conf配置文件链接到nginx的sites-enabled目录下。（如果没有这个目录，则创建一个，然后将其添加到nginx的搜索路径）

```shell
ln -s /path/to/your/project/root/nginx.conf /etc/nginx/sites-enabled/myapp_nginx.conf
```

使用uwsgi拉起django服务

```shell
uwsgi --ini myapp_uwsgi_config.ini
```

重新载入或者重启nginx服务器

```shell
service nginx reload;  # or service nginx restart
```

至此设置完毕，可以进入`<your server>:8001`看看是否正常。
