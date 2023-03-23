# Tutorial: Deploying a Django App with Gunicorn and Nginx on Ubuntu

This tutorial will guide you through the process of deploying a Django app using Gunicorn and Nginx on Ubuntu. This is a popular configuration for deploying web applications because it offers better performance and scalability compared to using Django's built-in server.

## Prerequisites

Before following this tutorial, ensure that you have completed the following prerequisites:

- A Django app that is ready for deployment
- An AWS account
- Familiarity with setting up Django for production on AWS. If you need help with this step, check out this tutorial: [Setting up Django for Production on AWS](https://github.com/cgarcia156/Tutorial-Setting-up-Django-for-Production-on-AWS). It provides a step-by-step guide on how to configure Django's settings for production, create a `requirements.txt` file, and launch an Ubuntu instance on AWS.


## Step 1: Update and Upgrade Packages

We need to ensure that our server has the latest packages installed.

```
sudo apt-get update
sudo apt-get upgrade
```


## Step 2: Create a Virtual Environment

Next, we need to create a virtual environment to isolate our app's dependencies.

```
sudo apt-get install python3-venv
python3 -m venv env
source env/bin/activate
```


## Step 3: Install Django

We can now install the dependencies required by our Django app.

```
pip install django
```


## Step 4: Clone Your Project

Clone your project repository to your server using Git.

```
git clone https://github.com/yourusername/yourproject.git
cd yourproject
```

## Step 5: Install Dependencies

We can now install the dependencies required by our Django app. Navigate to your project's root directory and install the requirements from the `requirements.txt` file.

```
pip install -r requirements.txt
```

This command will install all the packages listed in the `requirements.txt` file. Make sure you have a `requirements.txt` file in the root directory of your project with all the necessary dependencies.


## Step 6: Install Nginx and Gunicorn

We need to install Nginx and Gunicorn to serve our Django app.

```
sudo apt-get install -y nginx
pip install gunicorn
```


## Step 7: Configure Gunicorn with Supervisor

We will use Supervisor to manage our Gunicorn process.

```
sudo apt-get install supervisor
cd /etc/supervisor/conf.d/
sudo touch gunicorn.conf
sudo nano gunicorn.conf
```

Here is an example `gunicorn.conf` file:

```
[program:gunicorn]
directory=/home/ubuntu/Main/mysite
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/Main/mysite/mysite/app.sock mysite.wsgi:application  
autostart=true
autorestart=true
stderr_logfile=/var/log/gunicorn/gunicorn.err.log
stdout_logfile=/var/log/gunicorn/gunicorn.out.log

[group:guni]
programs:gunicorn
```

In the `directory` field, replace `/home/ubuntu/Main/mysite` with the path to your Django project root directory

Ensure that the `app.sock` file is created in the same directory as your Django project's `wsgi.py` file

Create the log directory:

```
sudo mkdir /var/log/gunicorn
```

Reload Supervisor configuration:

```
sudo supervisorctl reread
```

Check that the gunicorn process is available:

```
sudo supervisorctl update
```

Check the status of the process:

```
sudo supervisorctl status
```

## Step 8: Configure Nginx

We need to configure Nginx to forward requests to our Gunicorn process.

```
cd /etc/nginx/
sudo nano nginx.conf
```

Change the `user` to `root` to avoid permission errors.

```
user root;
```

Navigate to the `sites-available` directory and create a new configuration file:

```
cd sites-available
sudo touch django.conf
sudo nano django.conf
```

Here is an example `django.conf` file, edit the paths and replace `server_name` with your IP:

```
server{

        listen 80;
        server_name 54.176.66.65;


        location / {

                include proxy_params;
                proxy_pass http://unix:/home/ubuntu/Main/mysite/mysite/app.sock;

        }

        location /static/ {
                alias /home/ubuntu/Main/mysite/static/;
        }

}

```

Test your Nginx configuration:

```
sudo nginx -t
```

Create a symbolic link to enable your project's Nginx configuration:

```
sudo ln django.conf /etc/nginx/sites-enabled
```

Restart Nginx:

```
sudo service nginx restart
```

## Step 9: Test Your Application

Visit your public IP address in your web browser. You should see your Django app running.

## Conclusion

Congratulations! You have successfully deployed a Django app with Gunicorn and Nginx on Ubuntu. This configuration offers better performance and scalability compared to using Django's built-in server.




