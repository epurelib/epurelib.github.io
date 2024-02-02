
### `#!python .read()`

Retriving data from resource (db) is done using `#!python .read()` method

```python
def read(self, *args, **kwargs) -> Any
```

Read is called from resource of table property (table property is called from object of epure)

Read takes either key-word arguments or result of creating query with `#!python @escript` decorator (read abput `#!python @escript` <a href="https://epurelib.github.io/latest/learn/escript_decorator/#magic-escript-decorator">here</a>):

???+ example "Using `#!python .read()`"
    ```python
    from epure import epure, escript

    @epure()
    class MyEpure:
        str_field:str

        @classmethod
        @escript
        def my_query_creator(cls):
            query = cls.md.str_field == "a key-word arg"

    ...
    # creating and saving instances
    ...

    ```
    Now to retrive your instances you can go either with:
    
    Key-word approach
    ```python
    MyEpure.resource.read(str_field="a key-word arg") # [[<MyEpure obj>, <MyEpure obj>]]
    ```
    Or `#!python @escript` decorator approach
    ```
    query = MyEpure.my_query_creator()
    MyEpure.resource.read(query) # [[<MyEpure obj>, <MyEpure obj>]]

    ```

