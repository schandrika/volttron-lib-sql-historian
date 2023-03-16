.. _SQLHistorian-Library:

SQLHistorian Library
====================

volttron-lib-sql-historian library provides a SQLHistorian class that extends the
`BaseHistorian library <https://pypi.org/project/volttron-lib-base-historian/>`_ and could serves as a base class for any
historian that stores its data in an RDBMS based data store.  It inherits from the
`BaseHistorian class <https://github.com/eclipse-volttron/volttron-lib-base-historian/blob/main/src/historian/base/base_historian.py#:~:text=class%20BaseHistorian>`_ and provides all the necessary validation and processing
logic needed by a historian and abstracts only the actual database specific SQL queries to be implemented by an
inheriting historian class.

An historian that extends from SQLHistorian need to create only a single class that inherits the
`DbDriver class <https://github.com/eclipse-volttron/volttron-lib-sql-historian/blob/main/src/historian/sql/basedb.py#:~:text=class%20DbDriver>`_
and save it in a module named "historian.{database_type}.{database_type}functs.py" where database_type is the type of
the database as configured in the historian's configuration file. For example, below shows a minimal configuration
for SQLiteHistorian that store data in a sqlite database.

.. code-block::

    {
        "connection": {
            "type": "sqlite",
            "params": {
                "database": "data/historian.sqlite"
            }
        }
    }

The SQLiteHistorian contains a single module named sqlitefuncts.py in the package historian.sqlite. This module
contains the class that inherit the `DbDriver class <https://github.com/eclipse-volttron/volttron-lib-sql-historian/blob/main/src/historian/sql/basedb.py#:~:text=class%20DbDriver>`_

SQLHistorian assume that the data is store in two tables
1. A table to store topics and its metadata. Default table name is "topics"
2. A table to store the actual data. Default table name is "data"


.. _SQL-Historian-Configurations:

Optional Configuration
----------------------

The table names can be configured using the following configuration parameters.

.. code-block::

    "tables_def": {
        "table_prefix": "volttron",
        "data_table": "data_table",
        "topics_table": "topics_table"
    }

If a *table_prefix* is configured, the resultant table name would be <table_prefix>.<default/configured table_name>. A
*table_prefix* configuration is useful when multiple historians' store data in the same database.

Below example, shows a configuration for a historian that store data in a MySQL data.

.. code-block::

    {
        "connection": {
            "type": "mysql",
            "params": {
                "host": "localhost",
                "port": 3306,
                "database": "test_historian",
                "user": "historian",
                "passwd": "historian"
            }
        },
        "tables_def": {
            "table_prefix": "volttron",
            "data_table": "data_table",
            "topics_table": "topics_table"
        }
    }

A class that implements DBDriver should implement all the abstract methods defined in the DBDriver class, where each
method provides the database specific SQL query for a specific database operation such as setting up the historian
tables, insert a topic into topics table, update metadata for a topic, insert data for a topic etc.

Additional Configurations
-------------------------

Since SQLHistorian extends from the BaseHistorian, all the configurations supported by the BaseHistorian are also
available to any class that extends from the SQLHistorian. Please refer to :ref:`Base Historian Configurations <Base-Historian-Configurations>`

