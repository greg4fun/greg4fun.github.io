.. title: Django Display choices defined in models in template
.. slug: django-display-choices-defined-in-models-in-template
.. date: 2014/11/02 14:14:56
.. tags: 
.. link: 
.. description: 
.. type: text

example:
Having model:

.. code-block:: python

    GENDER= (
        (1 , 'Male'),
        (0 , 'Female'),
    )

    class Profile(UserenaBaseProfile):
        name = models.CharField(max_length=100,null=True,blank=True)
        surname = models.CharField(max_length=100,null=True,blank=True)
        sex = models.NullBooleanField(blank=True,null=True,choices=GENDER)
        born = models.DateTimeField(blank=True,null=True)

        def __unicode__(self):
            return str(self.name)

we can display Male or Female choice value in template by:


.. code-block:: python

    {{profile.get_sex_display}}
