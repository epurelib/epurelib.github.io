
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

### Using `#!py read()` with `#!py **kwargs` parameters

`#!py **kwargs` with `#!py read()` is convinient when you:

- know attribute by which you want to get set of objects

- have `data_id` (`#!python UUID`) of specific object

Then you can use `.read()` method that takes key-word arguments and allows this ready to hand approach of getting objects:

!!! example "Using `#!py read()` with `#!py **kwargs`"

    We will take `data_id` (`UUID`) from saved object:

    ```python hl_lines="4 9"

    article_one_data_id = article_one.data_id # -> UUID4 # (1)!
    ```
    Passing `UUID` as `data_id` kwarg will return `#!py object` with that specific `UUID`:
    ```py

    # reading by unique data_id of article will return one object
    my_articles = obj1.table.read(data_id=article_one_data_id)[0] # -> [<Article object at 0x0...>]

    ```
    Or if you want to retrieve saved instances by some attribute of `#!py object`:
    ```py

    # multiple attrs of article
    my_articles = Article.resource.read(str_attr="Why Epure is the best ORM?", times_published=3) # -> [[<Article object at 0x0...,>, ...]]

    ```

    1. data_id is a unique UUID object identifier that Epure uses to discriminate different objects