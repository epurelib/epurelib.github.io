#### `.join()` method

```python hl_lines="3" title="model.py"
def join(
        self, 
        model: Model, 
        on_clause: str, 
        join_type: str = "LEFT", 
        alias: str = ""
    ) -> JoinResource:
```

`.join()` is a method of <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a>, that joins model(s) by specific `on_clause` and `join_type`

- `model` parameter takes <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a> object that you want to join

- `on_clause` specifies by which id (e.g. data_id) tables will be joined

- `join_type` is the type of join, by default it is set to LEFT join

- `alias` is experimental feature that might be added in future

```python hl_lines="3" title="model.py"
def read(self, *args, **kwargs)
```

#### Advanced example with two joins

Let's examine a case when we want to join three models using .join() method :thinking:. 

!!! example "Shipping company with two joins"

    So, for example we have a shipping parcels company: we will create classes `TestShippmentOffice`, `TestCustomer` and `TestOrder`. 

    And we would like to join these three models in one Resource and read from it using _smart_ query

    So we will first define `TestShippmentOffice`:

    ```python hl_lines="1 2 5 16 26 37 40 43 48 51 62"
    
    from epure.generics import NotNull # (1)!
    from epure import escript, epure
    from uuid import UUID

    @epure()
    class TestShippmentOffice:
        adress:NotNull[str]

        def __init__(self, adress) -> None:
            self.adress = adress
    ```

    1. Check out about Epure Generics more <a href="">here</a>

    Then create and save instances of it:

    ??? info "Creating and saving `TestShippmentOffice` instances"
        ```py
        office1_id = TestShippmentOffice("Washington str.").save()
        office2_id = TestShippmentOffice("Elm str.").save()
        office3_id = TestShippmentOffice("Kole str.").save()
        ```

    We will then define our main class `TestCustomer` ... 

    ```py
    @epure()
    class TestCustomer:
        name:str
        country:str

        def __init__(self, name:str, country:str) -> None:
            self.name = name
            self.country = country

    ```
    ... with its smart query `read_from_join_resource`

    We will take Model and Domain of `TestCustomer`, get Models of other two classes

    Model can be taken either through `.model()` method of Domain object or by taking attribute of `domain` object through `.` (dot) notation and snakecase name of class:
    ```py
        @classmethod
        @escript
        def read_from_join_resource(cls):
            model = cls.md # (1)!
            domain = cls.dom # (2)!

            test_order_model = domain.test_order # (3)!

            # test_office_model = domain.test_shippment_office
            # or
            test_office_model = domain.model(TestShippmentOffice) #(4)!

    ```

    1. Here we are accessing the md: <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a> object of <span style="color:#00A550;">TestCustomer</span> class. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">here</a>
    2. Here we are accessing dom: <a href="https://epurelib.github.io/0.1/learn/domain_model/#domain">Domain</a> object of <span style="color:#00A550;">TestCustomer</span> class. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#domain">here</a>
    3. This way we get Model object of class <span style="color:#00A550;">TestOrder</span> by accessing dom object. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">here</a>
    4. This `model()` method of Model might be more convinient for you if you have class instance in reach. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model-method">here</a>

    Then we can just join these three models based on `data_id` attribute:

    ```py   
            first_join = model.join(test_order_model, model.data_id == test_order_model.test_customer_id) 

            second_join = first_join.join(test_office_model, test_order_model.office_id == test_office_model.data_id) 

    ```
    After joining these three modelds, we will have a JoinResource as a result, and we can easily read from it in three ways:

    - by passing our custom header in read which will return set(s) of 3 objects that are joined by `data_id` and their adress is "Washington str.".

    - by reading without header, and on condtiton that returned `TestCustomer` object will have "Japan" as country.

    - reading without header and condition will return all existing set(s) of three objects.

    ```py

            res_header = join_res.read(\
                [test_office_model.adress, model.name,\
                model, test_order_model, model.country],\ 
                test_office_model.adress == "Washington str.") # header

            res_no_header = join_res.read(\
                model.country == "Japan") # no header

            res_empty = join_res.read() # no header, no on_clause

            return res_header
    ```

    ??? info "Creating and saving `TestCustomer` instances"
        ```py
        nico_id = TestCustomer("Nicolas", "Argentina").save()
        victor_id = TestCustomer("Victor", "USA").save()
        tom_id = TestCustomer("Tom", "Japan").save()
        john_id = TestCustomer("John", "Laos").save()
        mike_id = TestCustomer("Mike", "Monaco").save()
        bob_id = TestCustomer("Bob", "Netherlands").save()
        ```

    Defining `TestOrder` class:
    ```py
    @epure()
    class TestOrder:
        test_customer_id:UUID
        order_date:str 
        office_id:UUID

        def __init__(self, test_customer_id, order_date, office_id) -> None:
            self.test_customer_id = test_customer_id
            self.order_date = order_date
            self.office_id = office_id

    ```
    ??? info "Creating and saving `TestOrder` instances"
        ```py

        TestOrder(nico_id, "2022-03-15", office1_id).save()
        TestOrder(victor_id, "2022-03-10", office2_id).save()
        TestOrder(nico_id, "2022-03-15", office1_id).save()
        TestOrder(tom_id, "2022-03-30", office1_id).save()
        TestOrder(john_id, "2022-01-15", office3_id).save()
        TestOrder(mike_id, "2022-12-10", office2_id).save()
        TestOrder(nico_id, "2022-08-04", office1_id).save()
        TestOrder(bob_id, "2022-09-15", office3_id).save()
        TestOrder(bob_id, "2022-05-11", office1_id).save()
        ```

<!--- # COMMENT

Lets look at our `#!python classmethod` a bit closer:

??? info "Looking closer at `test_two_joins`"
    ```python linenums="1"
    ...
    @classmethod
    @escript
    def test_two_joins(cls):
        md = cls.md # (1)!
        dom = cls.dom # (2)!

        test_order_md = dom.test_order # (3)!

        # test_office_md = dom.test_shippment_office
        # or
        test_office_md = cls.model(TestShippmentOffice) #(4)!
        
        join_res = md.join(test_order_md,\
        md.data_id == test_order_md.test_customer_id) 

        join_res.join(test_office_md,\
        test_order_md.office_id == test_office_md.data_id) 

        res_header = join_res.read(\
            [test_office_md.adress, md.name,\
            md, test_order_md, md.country],\ 
            test_office_md.adress == "Washington str.") # header

        res_no_header = join_res.read(\
            test_office_md.adress == "Washington str.") # no header

        res_empty = join_res.read() # no header, no on_clause

        return res_header
    ...
    ```

    1. Here we are accessing the md: <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a> object of <span style="color:#00A550;">TestCustomer</span> class. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">here</a>
    2. Here we are accessing dom: <a href="https://epurelib.github.io/0.1/learn/domain_model/#domain">Domain</a> object of <span style="color:#00A550;">TestCustomer</span> class. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#domain">here</a>
    3. This way we get Model object of class <span style="color:#00A550;">TestOrder</span> by accessing dom object. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">here</a>
    4. This `model()` method of Model might be more convinient for you if you have class instance in reach. Read more about it <a href="https://epurelib.github.io/0.1/learn/domain_model/#model-method">here</a>

    At line `#!python 14-15`:

    ```python
    
    ```

    ```python
    join_res = md.join(test_order_md, md.data_id == test_order_md.test_customer_id) 
    ```

    Because TestOrder has `test_customer_id` field, we can use it as `on_clause` to join it to our TestCustomer <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a>

    At line `#!python 17-18`:

    ```python
    join_res.join(test_office_md,\
        test_order_md.office_id == test_office_md.data_id) 
    ```

    After we joined two Models, we can additionally join another <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a> TestOffice using `office_id`

    At line `#!python 20-23`:

    ```python
    res_header = join_res.read(\
        [test_office_md.adress, md.name,\
        md, test_order_md, md.country],\ 
        test_office_md.adress == "Washington str.") # header
    ```

    <a href="https://epurelib.github.io/0.1/learn/epure_read/#read">read()</a> here takes:

    - `header` as `[test_office_md.adress, md.name, md, test_order_md, md.country]`, in this case we want to retrive address, name, the full Model itself, TestOrder Model, country and from these values classes will be initialized. 
    
    - `on_clause` as `test_office_md.adress == "Washington str."`, this means that only objects with this adress will be only retrived from db
        
    and it will return list of list with classes `[[TestShippmentOffice, TestCustomer, TestOrder], ...]` that were joined on `test_customer_id` and TestOffice object has `adress = "Washington str."`

    Read about `read()` more <a href="https://epurelib.github.io/0.1/learn/epure_read/#read">here</a>

    At line `#!python 25-26`:

    ```python
    res_no_header = join_res.read(\
            test_office_md.adress == "Washington str.")
    ```
    
    Because no `header` is passed, `header` will be set by default as `[TestShippmentOffice, TestCustomer, TestOrder]` i.e. all models that were joined. 

    At line `#!python 28`:

    ```python
    res_empty = join_res.read() # no header, no on_clause
    ```

    Because neither `header`, nor `on_clause` were passed: 
    
    - `header` will be set by default as `[TestShippmentOffice, TestCustomer, TestOrder]` i.e. all models that were joined.

    - `on_clause` will be set by default as `#!python None`, and it will just return all objects that were saved for these 3 classes.

# COMMENT -->

Calling `test_two_joins` method will result in list of lists, each sublist will contain three objects of each type:

``` python
TestCustomer.test_two_joins() # ->
    # '[[resource.test_join_resource.TestShippmentOffice object, 
    #      pyt1.tests.resource.test_join_resource.TestCustomer object, 
    #      resource.test_join_resource.TestOrder object], ...]'
```