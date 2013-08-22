===========================
Alembic migrations in Flask
===========================

This is an example of setting up a Flask_ app to use alembic_ for
database schema migrations. It uses Flask-Script_ to handle command-line
arguments. It works because alembic is called from a flask-script
command, which wraps it in an `app context`_, so there is a database
connection available at all times.

.. _Flask: http://flask.pocoo.org/
.. _alembic: https://alembic.readthedocs.org/
.. _Flask-Script: http://flask-script.readthedocs.org/
.. _app context: http://flask.pocoo.org/docs/appcontext/

Create an `instance folder`_ and write a ``settings.py`` file inside::

    import os.path
    instance_path = os.path.dirname(__file__)
    SQLALCHEMY_DATABASE_URI = 'sqlite:///%s/db.sqlite' % instance_path

.. _instance folder: http://flask.pocoo.org/docs/config/#instance-folders

Initialize an empty database::

    $ ./myapp syncdb

Generate a migration script after making changes to the models::

    $ ./myapp.py alembic revision --autogenerate -m foo.baz

Run all pending migrations::

    $ ./myapp.py alembic upgrade head

You can also define some more commands to save on typing::

    @manager.command
    def revision(message=None):
        if message is None:
            message = raw_input('revision name: ')
        return alembic(['revision', '--autogenerate', '-m', message])

    @manager.command
    def upgrade(revision='head'):
        return alembic(['upgrade', revision])

    @manager.command
    def downgrade(revision):
        return alembic(['downgrade', revision])
