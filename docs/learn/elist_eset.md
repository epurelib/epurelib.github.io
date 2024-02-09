## Elist and Eset: shared logic

<a>`Elist`</a> and <a>`Eset`</a> are <a href="https://docs.python.org/3/library/stdtypes.html#generic-alias-type">Generic</a> strict-type-based Alias classes and are created with subscripting the <a>`Elist`</a> or <a>`Eset`</a> class with the argument of python primitive `#!python type` or <a href="">Epure</a> `#!python type` 

e.g.

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

    <a>`Elist`</a> and <a>`Eset`</a> are __strict-type-based__ classes which means that they can contain __only__ instances of their subscripted class:

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

    Because <a>`Elist`</a> and <a>`Eset`</a> are Generic Alias type, when subscripted - Elist class will be stored in `.__origin__` field of created from subscription class:

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

Elist is a very usefull collection based on `#!python list` when:

 - you need to store small amounts of data
 - to have consistent and numerated order of stored items
 - used by one user at same time

??? info "The way Elist is stored in DB"

    Elist is created and stored in DB (by default in ecollections scheme) in form of table with name: `"elist__"` + "{name of your type}" e.g. `"elist__str"`, `"elist__my_epure_cls"`

    ??? example "Example of the way Elist is stored"
        ```python
        Elist[str] # This will be stored in "elist__str"
        ```

Contrary for Eset - you dont need to call `.load()` method for Elist.

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

    res[0].pop(0) # knowledge
    ```

### Elist methods:

(*If none description provided, then methods's return type and signature does not differ from default python `#!python list` methods)

    Elist.load(self, *args, **kwargs)
        """Loads all items of Elist contents like Epure, Elist, Eset 
         values from DataPromises"""

    Elist.ids(self)
        """Returns data_id's from all stored items in Elist if Elist was saved"""

    Elist.save(self)
        """Saves Elist to DB"""

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

Eset is our interpretation of Many-To-Many field. 

Eset is convinient when you:

- potentially want to store big amounts of data
- do not care about the order of stored items 
- can be used by many people simultaneously


!!! info "Loading Eset"

    When Eset is retrived from DB - Eset is empty by default, so it needs be loaded by `.load()` method of Eset.

    <!-- Because Eset can store big chunks of data, `.load()` is useful because you can `.load()` parts of data you stored based on your specified condition -->

    ??? example "Using `.load()`"
        ```python hl_lines="13 17"
        @epure()
        class MyEpureCls:
            my_eset = Eset[str](["up", "down"])

        my_inst = MyEpureCls()

        my_inst.save()

        data_id = my_inst.data_id

        res = MyEpureCls.resource.read(data_id=data_id)

        res[0].my_eset # {}

        res[0].my_eset.load()

        res[0].my_eset # {"up", "down"}
        ```


??? info "The way Eset is stored in DB"
    In DB Eset is stored by default in ecollection scheme in two ways:

    1) When Eset is a bound field of "epurized" (1) class, collection will be saved in table: `"eset__"` + "{name of your field}" e.g. `"my_epure_cls__my_eset"`
    { .annotate }

    1. "epurized" class is class that was decorated by `@epure()` decorator

    ??? example "Example of bounded Eset to epurized class"
        ```python
        @epure()
        class MyEpureCls:
            myEset:Eset[str] = Eset[str](["puss", "in", "boots"]) # this will be stored as "my_epure_cls__myeset"
        ```
        

    (in cases when type is primitive like `str` or `int`, it will be stored in number 2) way)
        

    2) When Eset is not bounded to any class, it is stored in a way like elist: `"eset__"` + "{name of your type}" e.g. `"eset__int"`, `"eset__new_epure"`

    ??? example "Example of not bounded to class Eset"
        ```python
        class MyBasicCls:
            my_eset_epure:Eset[MyEpureCls] # this will be stored as "eset__my_epure_cls"
        ```

        or

        ```python
        my_eset_epure:Eset[str] # this will be stored as "eset__str"
        ```

???+ example "Example of creating Eset, saving, deleting its contents"

    ```python
    from epure import Elist, Eset, epure

    @epure()
    class SomeEpureEset:
        my_eset_field:Eset[str] = Eset[str](["knowledge", "is", "power"])

    my_inst = SomeEpureEset()

    my_inst.save() # this will triger recursive save for
                   # all esets, elists and its internal epure vals to save()

    data_id = my_inst.data_id

    res = SomeEpureEset.resource.read(data_id = data_id)

    res[0].my_eset_field.load()

    res[0].remove("knowledge")
    ```

### Eset methods:

(*If none description provided, then methods's return type and signature does not differ from default python `#!python set` methods)

    Eset.load()
        """Loads all items of Eset contents like Epure, Elist, Eset 
         values from DataPromises"""
    
    Eset.ids()
        """Returns data_id's from all stored items in Elist if Elist was saved"""

    Eset.save()
        """Saves Elist to DB"""
    
    Eset.add(item)

    Eset.clear()

    Eset.copy() (In progress)

    Eset.difference() (In progress)

    Eset.difference_update() (In progress)

    Eset.discard(val)

    Eset.intersection() (In progress)

    Eset.intersection_update() (In progress)

    Eset.isdisjoint() (In progress)

    Eset.issubset() (In progress)

    Eset.issuperset() (In progress)

    Eset.pop()

    Eset.remove(val)

    Eset.symmetric_difference() (In progress)

    Eset.symmetric_difference_update() (In progress)
    
    Eset.union() (In progress)

    Eset.update(_set)