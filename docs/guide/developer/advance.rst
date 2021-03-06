.. _developer_guide:

Advance Guide
=============

StateField
----------
``StateField`` is a model field defined in ``django-river``, which prepare everything for you. Whenever you put this field into a model object, the model converts into a workflow model. The model have some workflow specialized properties, methods and relations.

Arguments
^^^^^^^^^


This is paragraph text *before* the table.

+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Arguments          | Description                                                                                                                                                                                        |
+====================+====================================================================================================================================================================================================+
| state_model        | You can change your state model. State model in ``django-river`` is used as default. It is recomended to use default one.                                                                          |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| reverse_identifier | Approvements have generic foreign key to your model objects. If you want to filter like ``Approvements.objects.filter(my_model__field1='value1')``, you should give that argument as ``my_model``. |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

This is paragraph text *after* the table.



The Model Injections
^^^^^^^^^^^^^^^^^^^^
``django-river`` injects some properties, relations and methods into your model class. This means, ther is gonna be special workflow properties, relations and methods in your model objects. Assume that your model class is ``Ticket``; When you have a ticket object, you can access the injections like ``ticket.any_injection_below`` or ``ticket.any_injection_below.all()`` or ``ticket.any_injection_below()``.


Properties:
"""""""""""

+------------------------------+--------------------------+-------------------------------------------------------------------+
| Name                         | Return Type              | Description                                                       |
+==============================+==========================+===================================================================+
| ``on_initial_state:``        | ``boolean``              | Defines your model object is on initial states or not.            |
+------------------------------+--------------------------+-------------------------------------------------------------------+
| ``on_final_state:``          | ``boolean``              | Defines your model object is on final states or not.              |
+------------------------------+--------------------------+-------------------------------------------------------------------+
| ``on_initial_approvements:`` | ``Approvement Queryset`` | Returns initial approvement objects.                              |
+------------------------------+--------------------------+-------------------------------------------------------------------+
| ``on_final_approvements:``   | ``Approvement Queryset`` | Returns final approvement objects.                                |
+------------------------------+--------------------------+-------------------------------------------------------------------+
| ``next_approvements:``       | ``Approvement Queryset`` | Returns next approvements objects according to the current state. |
+------------------------------+--------------------------+-------------------------------------------------------------------+



Methods:
""""""""

+--------------+-------------+-----------------------------------------------------------+--------------------------------------------------------+
| Name         | Return Type | Parameters                                                | Description                                            |
+==============+=============+===========================================================+========================================================+
| ``approve:`` | ``boolean`` | ``user``(must): Approved user, ``next_state``(not always) | Defines your model object is on initial states or not. |
+--------------+-------------+-----------------------------------------------------------+--------------------------------------------------------+
| ``reject:``  | ``boolean`` | ``user``(must): Approved user, ``next_state``(not always) | Defines your model object is on final states or not.   |
+--------------+-------------+-----------------------------------------------------------+--------------------------------------------------------+


Relations:
""""""""

+------------------------+---------------------+----------------------------+
| Name                   | Type                | Description                |
+========================+=====================+============================+
| ``approvements:``      | ``GenericRelation`` | To Approvement model.      |
+------------------------+---------------------+----------------------------+
| ``approvement_tract:`` | ``ForeignKey``      | To Approvement Track model |
+------------------------+---------------------+----------------------------+


Signals
-------

``pre_transition``: it is fired before any transition occured.

+-------------------+---------------------------------------+
| **Args**          | **Description**                       |
+===================+=======================================+
| workflow_object   | Your object on transition             |
+-------------------+---------------------------------------+
| field             | Field which you registered object for |
+-------------------+---------------------------------------+
| source_state      | Transition source state object        |
+-------------------+---------------------------------------+
| destination_state | Transition destination state object   |
+-------------------+---------------------------------------+
| appovement        | Approvement object                    |
+-------------------+---------------------------------------+

``post_transition``: it is fired before any transition occured.

+-------------------+---------------------------------------+
| **Args**          | **Description**                       |
+===================+=======================================+
| workflow_object   | Your object on transition             |
+-------------------+---------------------------------------+
| field             | Field which you registered object for |
+-------------------+---------------------------------------+
| source_state      | Transition source state object        |
+-------------------+---------------------------------------+
| destination_state | Transition destination state object   |
+-------------------+---------------------------------------+
| appovement        | Approvement object                    |
+-------------------+---------------------------------------+


``pre_approved``: it is fired before any approvement occured. Transition does not have to be occured.

+-----------------+---------------------------------------+
| **Args**        | **Description**                       |
+=================+=======================================+
| workflow_object | Your object approved                  |
+-----------------+---------------------------------------+
| field           | Field which you registered object for |
+-----------------+---------------------------------------+
| appovement      | Approvement object                    |
+-----------------+---------------------------------------+
| track           | Approvement track object              |
+-----------------+---------------------------------------+

``post_approved``: it is fired before any approvement occured. Transition does not have to be occured.

+-----------------+---------------------------------------+
| **Args**        | **Description**                       |
+=================+=======================================+
| workflow_object | Your object approved                  |
+-----------------+---------------------------------------+
| field           | Field which you registered object for |
+-----------------+---------------------------------------+
| appovement      | Approvement object                    |
+-----------------+---------------------------------------+
| track           | Approvement track object              |
+-----------------+---------------------------------------+

``pre_final``: it is fired before any workflow is completed.

+-----------------+---------------------------------------+
| **Args**        | **Description**                       |
+=================+=======================================+
| workflow_object | Your object on final                  |
+-----------------+---------------------------------------+
| field           | Field which you registered object for |
+-----------------+---------------------------------------+

``post_final``: it is fired before any workflow is completed.

+-----------------+---------------------------------------+
| **Args**        | **Description**                       |
+=================+=======================================+
| workflow_object | Your object on final                  |
+-----------------+---------------------------------------+
| field           | Field which you registered object for |
+-----------------+---------------------------------------+





Handlers
--------
Handlers are different from `django-river`. These are for spesific object, spesific source_state, spesific destination_state etc. It is fired when the condition is matched.

PreCompletedHandler:
^^^^^^^^^^^^^^^^^^^^
Before an object is on final state, if the condition is match; means object is suitable, it is fired;

.. code-block:: python

    from river.handlers.completed import PreCompletedHandler

    def handler(my_object,field,*args,**kwargs):
	    do_something_with(object,field)

    PreCompletedHandler.register(handler,my_object,'my_state_field')
	
	


``register`` method parameter**

+-----------------+---------------------------------------+----------+
| **Args**        | **Description**                       |          |
+=================+=======================================+==========+
| workflow_object | Your object                           | Required |
+-----------------+---------------------------------------+----------+
| field           | Field which you registered object for | Required |
+-----------------+---------------------------------------+----------+

PostCompletedHandler:
^^^^^^^^^^^^^^^^^^^^^
After an object is on final state, if the condition is match; means object is suitable, it is fired;


.. code-block:: python

    from river.handlers.completed import PostCompletedHandler

    def handler(my_object,field,*args,**kwargs):
        do_something_with(object,field)
    
    PostCompletedHandler.register(handler,my_object,'my_state_field')


**`register` method parameter**

+-----------------+---------------------------------------+----------+
| **Args**        | **Description**                       |          |
+=================+=======================================+==========+
| workflow_object | Your object                           | Required |
+-----------------+---------------------------------------+----------+
| field           | Field which you registered object for | Required |
+-----------------+---------------------------------------+----------+

PreTransitionHandler:
^^^^^^^^^^^^^^^^^^^^^
Before any transition occurred, if the condition is match; means object, source_state,destination state are suitable, it is fired;

.. code-block:: python

    from river.handlers.transition import PreTransitionHandler

    def handler(my_object,field,*args,**kwargs):
        do_something_with(object,field)

    PreTransitionHandler.register(handler,my_object,'my_state_field')


**`register` method parameter**

+------------------+---------------------------------------+----------+
| **Args**         | **Description**                       |          |
+==================+=======================================+==========+
| workflow_object  | Your object                           | Required |
+------------------+---------------------------------------+----------+
| field            | Field which you registered object for | Required |
+------------------+---------------------------------------+----------+
| source_state     | Source state of the tranition         | Optional |
+------------------+---------------------------------------+----------+
| desination_satte | Destinatio state of the tranition     | Optional |
+------------------+---------------------------------------+----------+

PostTransitionHandler:
^^^^^^^^^^^^^^^^^^^^^^
After any transition occurred, if the condition is match; means object, source_state,destination state are suitable, it is fired;

.. code-block:: python

    from river.handlers.transition import PostTransitionHandler
    
    def handler(my_object,field,*args,**kwargs):
        do_something_with(object,field)

    PostTransitionHandler.register(handler,my_object,'my_state_field')


**`register` method parameter**

+------------------+---------------------------------------+----------+
| **Args**         | **Description**                       |          |
+==================+=======================================+==========+
| workflow_object  | Your object                           | Required |
+------------------+---------------------------------------+----------+
| field            | Field which you registered object for | Required |
+------------------+---------------------------------------+----------+
| source_state     | Source state of the tranition         | Optional |
+------------------+---------------------------------------+----------+
| desination_satte | Destinatio state of the tranition     | Optional |
+------------------+---------------------------------------+----------+
