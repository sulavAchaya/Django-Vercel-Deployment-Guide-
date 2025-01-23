
# How to Host Django Project Without any Error in Vercel.

**Key points To be Considered before Hosting**

- Vercel Dosen't Support **dbsqlite** so we need to use **postgres database**

- vercel uses **serveless function** so we need something that helps us in hosting our assets (CSS, Javascript and media files).


## How and which service of   postgres Database to use ?
- postgres service is provided by various platform for free, even vercel itself provides one database for us in free plan which we can use
- Railway also provide us a free plan (5$ plan which we can use)

 [Link to the Railway](https://railway.app/)

# How to use vercel postgres ?

**Step1**

Navigate to vercel Dashboard and then Storage create new postgres database you will get all the required data there 


**Step2**

Now In Your Project **Settings.py** add the data inplace of sqlite database in the follwing way

```sh
  DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'name', # Replace this
        'USER': 'default',
        'PASSWORD': '***********', # Replace this
        'HOST': 'aaaaaaaaaaaaaaaaaaaaa', # Replace this
        'PORT': '0000', # Replace this
        'OPTIONS': {
            'sslmode': 'require', 
        },
    }
}
```
**Step3**

Run Makemigrations and migrate, to migrate all the database here

```bash
python manage.py makemigrations

```

```bash
python manage.py migrate

```
# How to use Railway postgres ?

**Step1**

Navigate to [Railway](https://railway.app/) and then create account and new project (+ project ) and then Deploy postgres , wait for sometimes the database will be created

**Step2**

click on the project created and go the variables you will get all the data required.

**Step3**

Now In Your Project **Settings.py** add the data inplace of sqlite database in the follwing way

```sh
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'aaaaaa', # Replace this
        'USER': 'user',  # Replace this
        'PASSWORD': '************', # Replace this
        'HOST': 'sjfhajdfadfaj.net', # Replace this
        'PORT': '000000', # Replace this
    }
}

```
**Step4**

Run Makemigrations and migrate, to migrate all the database here

```bash
python manage.py makemigrations

```

```bash
python manage.py migrate

```

**Now Our Postgres database has been connected to the django project, now let's install whitenoise to serve our static files**

```bash

pip install whitenoise

```

Edit your settings.py file and add WhiteNoise to the MIDDLEWARE list, above all other middleware apart from Django’s SecurityMiddleware:

```bash

MIDDLEWARE = [
    # ...
    "django.middleware.security.SecurityMiddleware",
    "whitenoise.middleware.WhiteNoiseMiddleware",
    # ...
]

```

now add this to store the static files in whitenoise

```bash

STATICFILES_STORAGE = "whitenoise.storage.CompressedManifestStaticFilesStorage"

```

Now run **collectstatic** once

```bash

python manage.py collectstatic

```

**Now its time to configure fort the media files**

for the media files we will be using [cloudinary](https://cloudinary.com/) 

**Step1**

Create account in [cloudinary](https://cloudinary.com/) and login.


**Step2**

Install cloudinary in Your Project via :

```bash

pip install cloudinary

```


**Step3**

add cloudinary in Installed app inside **settings.py** in following way:

```bash

INSTALLED_APPS = [
    ..........,
    'cloudinary_storage',
    'django.contrib.staticfiles',
    'cloudinary',
    
]

```

**Step4**

Now add the cloudinary configuration to the **Settings.py** In following way 

```bash
# Cloudinary configuration

CLOUDINARY_STORAGE = {
    'CLOUD_NAME': 'name', #Replace this
    'API_KEY': '1234567890', # Replace this
    'API_SECRET': 'asdfghjkladklfaskjfaskjasfafd' # Repalce this
}

DEFAULT_FILE_STORAGE = 'cloudinary_storage.storage.MediaCloudinaryStorage'

```

you will get this data in your cloudinary account

# Remember Your template, staticfiles and media files must be properly configured 

**Eg**

```bash 
import os  # Import this at the top 

#template Directory 

'DIRS': [os.path.join(BASE_DIR, 'templates')],


# Static configuration

STATIC_URL = 'static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles_build')
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

#media configuration

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

```

**Aslo in urls.py**

Import ths at top 
```bash
from django.conf import settings
from django.conf.urls.static import static

```
and add this at the end after your ']'

```bash
+ static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

```

# Now we have done all the required things that is needed by vercel to host a project now lets configure and create file for vercel 


**1.**

Create a file name "build_files.sh" in your root directory (where you have manage.py file) and paste the below code.


```bash 
pip install -r requirements.txt
python manage.py collectstatic --noinput


```

**2.**

Now Create another file called "runtime.txt" in same directory and specify the python that you want to use to run the code (Remember the version must also be supported by the vercel)

```bash
python-3.9

```

**3.**

 create next file named "vercel.json" and configure the project like given below (You can simply copy paste and add/remove according to your project)

 ```bash
 {
  "builds": [
    {
      "src": "project_name/wsgi.py", 
      "use": "@vercel/python",
      "config": {
        "maxLambdaSize": "15mb",
        "runtime": "python3.9"
      }
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "project_name/wsgi.py"
    }
  ]
}

```

Remeber to Repalce project_name with your Project Name(The one which has settings.py file)

**4.**

Now in YOur 'wsgi.py' file (this wil be in same directory as settings.py) add the below line, This is because vercel search for app and vercel don't understand application directly

```bash
app = application

```

add this at the end of the code of wsgi.py 


**5.**
now run pip freeze command to create all the requirements that has been installed while working with the projects.

```bash 
pip freeze > requirements.txt

```
****
# We are done now  setting all the file for hosting  project in vercel.  

# Hosting can be Done in Two different Way 
**Method 1. Using Github Repository** 

**Step1.**

Create Repo in your github and push all the code their.

**Step2.**

Navigate to [Vercel](https://vercel.com/) and create your account you can directly create account with Github or link Github later on.

**Step3.**
Navigate to **Add New** and then **Project**, Then Import Your Repository then Give the Project name, Leave the Framwork to "Others" (or select Others if there is any other framwork) specify Base Directory if you have other directory as a base if not leave as it is for (./) 

I am telling you to leave for (./) for a project that has this kind of structure 

```sh

your_project/
├── app/
│   ├── migrations/
│   │   ├── __init__.py
│   │   └── ...
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
├── project/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
├── static/
│   ├── css/
│   ├── js/
│   └── images/
├── templates/
│   ├── base.html
│   └── ...
├── venv/
│   ├── bin/
│   ├── include/
│   ├── lib/
│   └── ...
├── build_files.sh
├── manage.py
├── requirements.txt
├── runtime.txt
└── vercel.json

```

**Step3.**

Now Click on Deploy **Your Project will be Deployed**

******


****
****Method 2: Using the command via code editor*****

(I will be taking referance as vscode as its famous but is same for all code editor)

**Step1:**

Install the Vercel CLI to deploy from your command line:

```bash
npm install -g vercel

```

**Step2.**

Log in to Vercel Via following command and follow the instruction.

```bash
vercel login

```

**Step3.**

 Now deploy your project using the Vercel CLI

 ```bash 
 vercel --prod

 ```

 # Your Project will be sucessfully hosted to the vercel you can check it out by logging into the [Vercel](https://vercel.com/), with same account that you have hosted the project Into  and also Remeber to change ALLOWED_HOSTS (If Not change it and re run the step3 (vercel --prod)) 


 **Remember to mark Star to a Repo, which will motivate me to create more documentation like this.**

 ***

 **Contribution to Documentation are Warmly welcomed**


# Want to hire me or stuck into a problem ?
**Contact me here ↓**

**[linkedin](https://www.linkedin.com/in/sulav-acharya-6a8009234?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app)**

**[Email](codingsuyog@gmail.com)**


# Remember to mention me (@suyog_coding) if you use any part of my work
