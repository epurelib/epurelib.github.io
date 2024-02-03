## Supported types for type-hinting class attributes

For type-hinting class attributes Epure supports:

- most of primitive types of `#!python python` 
- type: `#!python UUID` 
- other "epurized" classes and <a href="https://epurelib.github.io/0.1/learn/elist_eset/#elist">Elist</a> and <a href="https://epurelib.github.io/0.1/learn/elist_eset/#eset">Eset</a> Generic types

!!! info "Supported types"
    ```python
    from uuid import UUID
    from epure import epure, Elist, Eset

    @epure()
    class SomeEpureCls:
        ...

    @epure()
    class AllSupportedTypes:
        my_int: int
        my_str: str
        my_complex: complex
        my_float: float
        my_bytes: bytes
        my_bool: bool
        my_UUID: UUID
        my_Epure: SomeEpureCls
        my_elist: Elist[str]
        my_eset: Eset[bytes]
        my_object: object
    ```

!!! question "`#!js JSON` and `#!py object` type"
    class atribute with type `#!py object` will be saved as `#!js JSON` to DB

    __So__ if you have a object and you dont know which type to use - just choose `#!py object` type 🙂