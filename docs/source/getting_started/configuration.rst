==============================
Django `sage_language` Package
==============================

Overview
========
The `sage_language` package is designed to handle language localization through cookies in a Django application. It provides middleware to manage locale switching based on user preferences stored in cookies and integrates with Django's built-in internationalization (i18n) framework.

This documentation covers how to install, configure, and use `sage_language` in your Django project.

Installation
============

To install the `sage_language` package, run the following command:

.. code-block:: bash

    pip install sage_language

Configuration
=============

After installing the `sage_language` package, configure your Django project by modifying the `settings.py` file.

1. **Add `sage_language` to Installed Apps**

   In your `settings.py`, add `sage_language` to the `INSTALLED_APPS` list:

   .. code-block:: python

      INSTALLED_APPS = [
          # other apps...
          'sage_language',
      ]

2. **Add `CookieLocaleMiddleware` to Middleware**

   The `CookieLocaleMiddleware` must be added to the `MIDDLEWARE` setting. Importantly, it should always be added **after** the `SessionMiddleware`:

   .. code-block:: python

      MIDDLEWARE = [
          # Django's session middleware must come before CookieLocaleMiddleware
          'django.contrib.sessions.middleware.SessionMiddleware',
          # other middleware...
          'sage_language.middlewares.cookie.CookieLocaleMiddleware',
      ]

3. **Configure Language Settings**

   In addition to adding the middleware, you need to configure various internationalization (i18n) and localization (l10n) settings in `settings.py`. Below are the necessary configurations:

   - **SAGE_LANGUAGE_COOKIE_NAME**: Set the name of the cookie used to switch the language.

     .. code-block:: python

        SAGE_LANGUAGE_COOKIE_NAME = "your_preferred_cookie_name"

   - **USE_I18N**: This should be set to `True` to enable Django’s internationalization system.

     .. code-block:: python

        USE_I18N = True

   - **USE_L10N**: This should also be set to `True` to activate localization.

     .. code-block:: python

        USE_L10N = True

   - **USE_TZ**: Set this to `True` to activate timezone support.

     .. code-block:: python

        USE_TZ = True

   - **LANGUAGES**: Define the list of languages available in your application. This should be a list of two-tuples, where the first element is the language code and the second is the language name.

     .. code-block:: python

        LANGUAGES = [
            ('en', 'English'),
            ('fr', 'French'),
            # other languages...
        ]

   - **LANGUAGE_CODE**: Set the default language code of your application.

     .. code-block:: python

        LANGUAGE_CODE = 'en'

4. **Add `SetLanguageView` to URL Patterns**

   To allow users to change languages through a URL, you need to define the `SetLanguageView` in your `urls.py`:

   .. code-block:: python

      from sage_language.views import SetLanguageView

      urlpatterns = [
          path('set-language/', SetLanguageView.as_view(), name='set_language'),
      ]

5. **Include i18n URL Patterns**

   To enable Django's internationalization routing, include the `i18n` URL configuration in your `urls.py`:

   .. code-block:: python

      from django.conf.urls.i18n import i18n_patterns
      from django.urls import path, include
      from django.contrib import admin

      from sage_language.views import SetLanguageView
      from yourapp.views import YourView  # replace with your view

      urlpatterns = [
          path('set-language/', SetLanguageView.as_view(), name='set_language'),
          path("i18n/", include("django.conf.urls.i18n")),
      ]

      # Define the patterns for multilingual views
      urlpatterns += i18n_patterns(
          path('admin/', admin.site.urls),
          path('', YourView.as_view(), name='your-view-that-wants-to-be-multilingual'),
          prefix_default_language=False,
      )

Usage
=====

Once you have configured your settings, the `sage_language` package will automatically handle language switching via cookies. The user’s language preference will be stored in the cookie specified by `SAGE_LANGUAGE_COOKIE_NAME`. This will enable your application to serve content in the selected language across sessions.

Ensure that your views and templates support Django’s translation framework by wrapping text with the translation functions provided by Django (e.g., `gettext` or `ugettext` for older versions).

More information about Django’s translation framework can be found in the official Django documentation: https://docs.djangoproject.com/en/stable/topics/i18n/translation/.

Further Reading
===============

For more detailed information about `sage_language`, please consult the package’s GitHub repository or official documentation (if available).

- Django i18n Documentation: https://docs.djangoproject.com/en/stable/topics/i18n/
- Django Middleware: https://docs.djangoproject.com/en/stable/topics/http/middleware/
