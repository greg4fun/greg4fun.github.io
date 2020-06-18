.. title: Update field value with another field value within same table
.. slug: update-field-value-with-another-field-value-within-same-table
.. date: 2014/06/15 16:29:28
.. tags: draft
.. link: 
.. description: 
.. type: text

update food_food set total_carbs = IF(total_carbs is NULL or total_carbs='',sugars,total_carbs) ;
