
## Model

In Epure, Model class represents table and your decorated by @epure() class from db perspective.

Model of a class can be accessed through the `.md` field

!!! warning "Accessing `.dom`, `.md` fields and `.model()` method"

    `.dom`, `.md` fields and `.model()` method can be only accessed in method of "epurized" class, decorated by @escript function. Read more about @escript <a href="https://epurelib.github.io/0.1/learn/escript_decorator/#magic-escript-decorator">here</a>

You can get ColumnProxy objects by accessing fields of Model

Through ColumnProxy object you then can create all kinds of queries. Read more about examples and methods of ColumnProxy that are used with `#!python @escript` decorator <a href="https://epurelib.github.io/0.1/learn/column_proxy_methods/">here</a>

??? example "Getting ColumnProxy field from Model"

    ```python hl_lines="13"
    from epure import escript, epure

    @epure
    class MyEpureCls:
        int_field:int 
    ...

    ...
    @escript
    def method_to_get_model(self):
        my_epure_cls_model = self.md # This will return Model of MyEpureCls

        my_epure_cls_model.int_field # This will return ColumnProxy of int_field field
    ...
    ```

### `.model()` method

`.model()` is a simple and easy way to get the Model object of class using instance of this class e.g.:

??? example "Getting Model object with `.model()` method"

    The more convinient way if you have instance of Class

    ```python hl_lines="10"
    from epure import escript, epure

    @epure
    class MyEpureCls:
    ...

    ...
    @escript
    def method_to_get_model(self):
        my_epure_cls_model = self.model(MyEpureCls) # This will return Model object
    ...
    ```


## Domain

Domain comes from the idea of "DDD" or Domain Driven Design, in Epure, Domain represents object of DataBase and can be accessed through the `.dom` field

!!! warning "Accessing `.dom`, `.md` fields and `.model()` method"

    `.dom`, `.md` fields and `.model()` method can be only accessed in method of "epurized" class, decorated by @escript function. Read more about @escript <a href="https://epurelib.github.io/0.1/learn/escript_decorator/#magic-escript-decorator">here</a>

You can dynamically interact with Domain object by getting Models from it.

??? example "Getting Model object from Domain"

    The more convinient way if you have instance of Class

    ```python hl_lines="10"
    from epure import escript, epure

    @epure
    class MyEpureCls:
    ...

    ...
    @escript
    def method_to_get_model(self):
        my_epure_cls_model = self.model(MyEpureCls) # This will return Model object
    ...
    ```

    Or if you know the table name of Model which you want to get

    ```python hl_lines="5"
    ...
    @escript
    def method_using_dom_field(self):
        dom_object = self.dom # This will return Domain object
        my_epure_cls_model = self.dom.my_epure_cls
    ...
    ```

