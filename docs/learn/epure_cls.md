## More on `#!python @epure()` decorator

`#!python @epure()` is a class decorator, that adds class Epure as an aditional parent to your class, making your class "epurized".

Your "epurized" class may serve you as an controller for working with your DB.

???+ warning
    Note that you need to use __type hints__ for class attributes in order to save class to DB. Class attribute without a type-hint will not be saved in DB.

## Supported types for type-hinting class attributes

For type-hinting class attributes Epure supports:

- most of primitive types of `#!python python` 
- type: `#!python UUID` 
- other "epurized" classes and <a>Elist</a> and <a>Eset</a> Generic types

!!! info "Supported types"
    ```python
    from uuid import UUID
    from epure import @epure, Elist, Eset

    @epure()
    class SomeEpureCls:
        ...

    @epure()
    class AllSupportedTypes:
        my_int:int
        my_str:str
        my_complex:complex
        my_float:float
        my_bytes:bytes
        my_bool:bool
        my_UUID:UUID
        my_Epure:SomeEpureCls
        my_elist: Elist[str]
        my_eset: Eset[bytes]
    ```

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
