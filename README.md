# Django Appointment 
## Features ✨

1. Customizable time slots, lead time, and finish time.
2. Competent handling of appointment conflicts and availability.
3. Seamless integration with the Django admin interface for appointment management.
4. Custom admin interface for managing appointment/staff member editing, creation, availability, and conflicts.
5. User-friendly interface for viewing available time slots and scheduling appointments.
6. Email notifications for appointment scheduling and reminders:
   - Instant email notifications to clients upon scheduling an appointment.
   - Automated email reminders sent 24 hours before the appointment (requires Django Q).
   - ICS file attachment for calendar synchronization.
7. Integration with Django Q for efficient task scheduling and email sending.

## Key features introduced in previous versions.

- For more information, please refer to
  this [documentation](https://github.com/adamspd/django-appointment/tree/main/docs/history).

## Added Features and Bug Fixes in version 3.x.x

See the [release notes](https://github.com/adamspd/django-appointment/releases/tag/v3.8.0).
For older version,
see their [release notes](https://github.com/adamspd/django-appointment/tree/main/docs/release_notes).

## Quick Start 🚀

⚠️ **Haven't tried the demo yet?** Go [here](https://django-appt.adamspierredavid.com/) first - it'll make everything clearer.

1. Install Django-Appointment in your project using pip:

   ```bash
   pip install django-appointment
   ```

2. Add "appointment" to your `INSTALLED_APPS` setting like so:

   ```python
   INSTALLED_APPS = [
       # other apps
       'appointment',
   ]
   ```

3. Then, incorporate the appointment URLconf in your project's `urls.py`:

   ```python
   from django.urls import path, include
   
   urlpatterns = [
       # other urls
       path('appointment/', include('appointment.urls')),
   ]
   ```
4. In your Django's `settings.py`, append the following:

   ```python
   AUTH_USER_MODEL = 'models.UserModel'  # Optional if you use Django's user model
   ```

   For instance, if you employ a custom user model called `UserClient` in an app named `client`, you would add it like:

   ```python
   AUTH_USER_MODEL = 'client.UserClient'
   ```

   But if you're using the default Django user model (like most of us), there's no need to add this line since Django
   automatically sets it to:

   ```python
   AUTH_USER_MODEL = 'auth.User'
   ```

   Ensure your `create_user` function includes the following arguments, even if they are not all used (in case you're
   using a custom user model with your own logic for creating users):

   ```python
   def create_user(first_name, email, username, last_name=None, **extra_fields):
       pass
   ```

   This function will create a passwordless user.
   After doing so, a link to set the password will be sent to the user's email upon completing the appointment request.

   Another variable that is worth configuring is the website's name in your `settings.py`:

   ```python
   APPOINTMENT_WEBSITE_NAME = 'Chocolates'
   ```

   It will be used in the footer of the emails sent to clients upon scheduling an appointment:

   ```html
   <p>® 2023 {{ APPOINTMENT_WEBSITE_NAME }}. All Rights Reserved.</p>
   ```

5. Next would be to create the migrations and run them by doing `python manage.py makemigrations appointment` and right
   after, run `python manage.py migrate` to create the appointment models.

6. Launch the development server and navigate to http://127.0.0.1:8000/admin/ to create appointments, manage
   configurations, and handle appointment conflicts (the Admin app must be enabled).
7. You must create at least one service before using the application on the admin page. If your service is free, input 0
   as the price. If your service is paid, input the price in the price field. You may also provide a description for
   your service.
8. Visit http://127.0.0.1:8000/appointment/request/<service_id>/ to view the available time slots and schedule an
   appointment.

## Email and Django Q Configuration 📧

### Email Configuration 📧

Proper email configuration is crucial for the sending email and for the appointment reminder functionality.
Add the following to your `settings.py`:

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'your_smtp_host'
EMAIL_PORT = 587 # or 465 for EMAIL_USE_SSL
EMAIL_USE_TLS = True # or EMAIL_USE_SSL = True
EMAIL_HOST_USER = 'your_email@example.com'
EMAIL_HOST_PASSWORD = 'your_email_password'

# Optional: Set your website name for email footer
APPOINTMENT_WEBSITE_NAME = 'Your Website Name'
```

> **Note:** Make sure to use environment variables or a secure method to store sensitive information like email passwords in production.


### Django Q Configuration 📧

Django-Appointment uses Django Q for sending email reminders.
This feature is optional but recommended for better performance and user experience.

#### Setting up Django Q

1. Install Django Q:
   ```bash
   pip install django_q2
   ```

2. Add 'django_q' to INSTALLED_APPS in your settings.py:
   ```python
   INSTALLED_APPS = [
       # ...
       'django_q',
   ]
   ```

3. Configure Django Q in your settings.py:
   ```python
   Q_CLUSTER = {
       'name': 'django-appointment',
       'workers': 4,
       'timeout': 90,
       'retry': 120,
       'queue_limit': 50,
       'bulk': 10,
       'orm': 'default',
   }
   USE_DJANGO_Q_FOR_EMAILS = True  # Use Django Q for sending ALL emails
   ```

4. Start the Django Q cluster:
   ```bash
   python manage.py qcluster
   ```

> **Note:** If you choose not to use Django Q, email reminders will not be sent, but the rest of the application will
> function normally.


## Template Configuration 📝

If you're using a base.html template, you must include the following blocks in your template:

```html
{% block customCSS %}
{% endblock %}

{% block title %}
{% endblock %}

{% block description %}
{% endblock %}

{% block body %}
{% endblock %}

{% block customJS %}
{% endblock %}
```

These blocks are essential for the proper functioning of the application:
- `customCSS` and `customJS` allow the application to inject the necessary styles and scripts.
- `body` is where the main content of each page will be rendered.
- `title` and `description` are used for SEO and are recommended but not strictly required.

At minimum, the blocks for CSS, body, and JS are required for the application to work properly. 
jQuery is also required to be included in the template.

See an example of a base.html template [here](https://github.com/adamspd/django-appointment/blob/main/appointment/templates/base_templates/base.html).


## Customization 🔧

1. In your Django project's `settings.py`, you can override the default values for the appointment scheduler.
   More information regarding available configurations can be found in
   the [documentation](https://github.com/adamspd/django-appointment/tree/main/docs/README.md#configuration).
2. Modify these values as needed for your application, and the app will adapt to the new settings.
3. For further customization, you can extend the provided models, views, and templates or create your own.

## Internationalization 🌍

Django-Appointment includes built-in support for multiple languages with localized date formats and translations.

**Currently supported languages:**
- English (en) - Default
- French (fr) - Complete translation

**Features:**
- 🗣️ UI translations for 2 languages (more welcome!)
- 📅 Localized date formats for 35+ languages
- 🌐 Automatic language detection
- 📝 RTL language support

**Quick setup:**
```python
# settings.py
USE_I18N = True
USE_L10N = True
LANGUAGES = [('en', 'English'), ('fr', 'French')]
```

**Want to add your language or translate database content?**
👉 **[See the full Internationalization Guide](INTERNATIONALIZATION.md)**

## Docker Support 🐳

Django-Appointment now supports Docker, making it easier to set up, develop, and test.

### Getting Started with Docker for Development or Local Testing

Using Django-Appointment with Docker is primarily intended for **development purposes** or **local testing**.
This means you'll need to ___clone the project from the GitHub repository___ to get started.

> **Note:** These Docker instructions are for development and testing. For production deployment, please refer to
> Django's official deployment documentation and best practices.

Here's how you can set it up:

1. **Clone the Repository**: Clone the Django-Appointment repository to your local machine:

   ```bash
   git clone https://github.com/adamspd/django-appointment.git
   ```

   or using SSH:
   ```bash
   git clone git@github.com:adamspd/django-appointment.git
   ```

   then go to the project's directory:
   ```bash
   cd django-appointment
   ```

2. **Prepare an .env File**: Create an `.env` file in the root directory of your project with your configuration
   settings.
   You should include your email host user and password for Django's email functionality (if you want it to work):

   ```plaintext
   # Main admin user, can have several others, but you need to change the settings.py configuration then
   ADMIN_NAME="Super Admin"
   ADMIN_EMAIL=super.admin@example.com
   
   # If you don't change these 3 below, docker-compose (or localhost) will fail sending emails
   EMAIL_HOST_USER=no-reply@example.com
   EMAIL_HOST_PASSWORD=youcantguessme
   EMAIL_HOST=smtp.example.com
   
   # default one (can leave it as is)
   EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
   
   # if using TLS on your mail server this is ok, else, use 465 for SSL
   EMAIL_PORT=587
   EMAIL_USE_TLS=True
   
   # On localhost, you must install django-q2, with docker-compose, it's already installed
   USE_DJANGO_Q=True
   ```

   > **Note:** The `.env` file is used to store sensitive information and should not be committed to version control.

