## More on `#!python @epure()` decorator

`#!python @epure()` is a class decorator, that adds class Epure as an aditional parent to your class, making your class "epurized".

Your "epurized" class may serve you as an controller for working with your DB.

???+ warning
    Note that you need to use __type hints__ for class attributes in order to save class to DB. Class attribute without a type-hint will not be saved in DB.

## Storing Epure in another table and domain 

### Storing Epure in another table

By default when class is "epurized" than its table will be stored in table named after name of the class.

Though you can override it passing name of domain to `#!python @epure` function e.g.:

!!! example "Storing Epure in another table"
    ```python
    @epure("AnotherTable")
    class AnotherClass:
        my_str:str
        ...
    ```

This will save class AnotherClass in table named "another_table"

### Storing Epure in another domain

Another option you can use is storing your table in another domain or "scheme"

This is also done by passing str with name of path, but with additional dot notation:

!!! example "Storing Epure in another domain"
    ```python
    @epure("my_other_domain.my_other_table")
    class SomeOtherClass:
        my_str:str
        ...
    ```

So this will be stored in scheme named "my_other_domain" and in table named "my_other_table".

## Class with mulitple inheritance and `#!python @epure` decorator

When "epurized" class inherits another parent class, fields of this parent class will be inherited and stored in DB by "epurized" class

Let's look at example:

!!! example "Example with class inheritance"

    ```python
    class FirstParent:
        first_parent_complex:complex
        first_parent_uuid: UUID

    class SecondParent:
        second_parent_float:float
        second_parent_bool:bool

    @epure()
    class Child(FirstParent, SecondParent):
        child_field_str:str
        child_obj:object

    ```

In result there will be created a Table "child" with its parents inhereted fields of FirstParent: `first_parent_complex, first_parent_uuid` and of SecondParent: `second_parent_float, second_parent_bool`

