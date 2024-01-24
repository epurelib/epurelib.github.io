<a>`Elist`</a> and <a>`Eset`</a> are <a href="https://docs.python.org/3/library/stdtypes.html#generic-alias-type">Generic</a> strict-type-based Alias classes and are created with subscripting the <a>`Elist`</a> or <a>`Eset`</a> class with the argument of python primitive `#!python type` or <a href="">Epure</a> `#!python type` e.g.

??? example "Example of creating Elist or Eset by subscriptiting type"
    ```python
    from epure import Elist, Eset

    Elist[str]

    Eset[int]
    ```

    or

    ```python
    from epure import Elist, Eset, epure

    @epure()
    class MyEpure:
        ...

    Elist[MyEpure]
    Eset[MyEpure]

    @epure()
    class MyOtherEpure:
        my_elist_field:Elist[MyEpure]
        my_eset_field:Eset[MyEpure]
    ```

!!! note "Elist and Eset are strict-type-based"

    <a>`Elist`</a> and <a>`Eset`</a> are __strict-type-based__ classes which means that they can contain __only__ instances of their subscribed class:

    ??? example "Example of Eset and Elist strict-type-based nature"

        ```python

        @epure()
        class RightElist:
            some_elist:Elist[str] = Elist[str](["You", "are", "wizard", "Harry"])

        str_elist = RightElist()
        ```

        Now if we would want to add some other type, for example `#!python int` to our `str_elist`:

        !!! failure "Adding instance of type other than `#!python str` type will raise an error"
            ```python
            str_elist.append(42) # TypeError("value '42' of type '<class 'int'>' 
                                 # is not same type as 
                                 # Elist 'ecollections.elist__str' 
                                 # type of '<class 'str'>'")
            ```

!!! info "Checking type of Elist or Eset"

    <a>`Elist`</a> and <a>`Eset`</a> classes constructor is defined in `ECollectionMetacls`, so to check if class is `Elist` or `Eset`, it is best to use `isinstance()` function for their type

    ??? example "Example of checking Elist or Eset type"
        ```python
        isinstance(Elist[str], ECollectionMetacls) # True
        ```

        ```python
        
        ```

    Because <a>`Elist`</a> and <a>`Eset`</a> are Generic Alias type, when subscripted - their class will be stored in `.__origin__` field of subscripted class:

    ??? example "Check type with `__origin__` field of Elist or Eset"

        ```python 
        from epure import Elist

        my_elist = Elist[str]

        type(my_elist) == Elist # False

        type(my_elist.__origin__) == Elist # True
        ```


<!-- !!! warning "Warning: Nested Generic Types"
    Elist and Eset does not support nested Generics Types
    ??? failure "Example of not supported subscription"
        ```python
        Elist[list[str]]
        ``` -->





## Elist

Elist is a very usefull collection based on `#!python list` when you need to store small amounts of data in DB with consistent order of items.

Elist inherits some of `#!python list` type methods, but they may differ from python default `#!python list` methods: these methods are explained below.

!!! warning "Warning for multi-user usage"

    In systems where is more than one user will interact with one <a>`Elist`</a> - <a>`Elist`</a> __may__ lose consistency for its order of stored items. 
    
    If you want more multiuser-friendly alternative - your best choise is <a href="">Eset</a>.

???+ example "Example of creating Elist, saving, deleting its contents"

    ```python
    from epure import Elist, Eset, epure

    @epure()
    class SomeEpureElist:
        my_elist_field:Elist[str] = Elist[str](["knowledge", "is", "power"])

    my_inst = SomeEpureElist()

    my_inst.save() # this will triger recursive save for
                   # all esets, elists and its internal epure vals to save()

    data_id = my_inst.data_id

    res = SomeEpureElist.resource.read(data_id = data_id)

    res[0].my_elist_field[2] # power

    res[0].pop(2)
    ```

### Elist methods:

(*If none description provided, then methods's return type and signature does not differ from default python `#!python list` methods)

    Elist.load(self, *args, **kwargs)
        "Loads all items of Elist contents like Epure, Elist, Eset 
         values from DataPromises"

    Elist.ids(self)
        "Returns data_id's from all items of Elist"

    Elist.append(item)

    Elist.extend(Elist) (In progress)

    Elist.insert(index, item)

    Elist.remove(value)

    Elist.pop(index)

    Elist.clear()

    Elist.index(item[, start[, end]])

    Elist.count(item) (In progress)

    Elist.sort(*, key=None, reverse=False) (In progress)

    Elist.reverse() (In progress)

    Elist.copy() (In progress)


## Eset

