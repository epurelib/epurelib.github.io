## Serialization with `.to_dict()` or `.to_json()`

In order to serialize Epure object we need to use `.to_dict()` or `.to_json()`

Lets look at example:

!!! example "{==Example with nested Epure values==}"
    ```python
    from epure import epure, Elist, Eset

    @epure()
    class SomeRandEpure:
        someint:int = 777
        somecomplexval:complex = 3 + 4j

    @epure()
    class SomeEpure:
        str_val:str = "keen"
        int_val:int = 80
        someRandEpureVal:SomeRandEpure = SomeRandEpure()

    @epure()
    class ExampleCls:
        someEpureVal:SomeEpure = SomeEpure()
        some_val:str = "To the moon!"

    ex1 = ExampleCls()
    ex2 = ExampleCls()

    @epure()
    class ToDictEx:
        elist_val:Elist[ExampleCls] = Elist[ExampleCls]([ex1, ex2])
        eset_val:Eset[ExampleCls]= Eset[ExampleCls]([ex1, ex2])
        epure_val:SomeEpure = SomeEpure()
        str_val:str = "In Tech we trust"
        int_val:int = 424
        complex_val:complex = 3 + 4j
        generic_list:List[str] = ["cat", "dog", "yak"]
        UPCASE_VAL:str = "Some UPcase val"


    to_dict_ex_inst = ToDictEx()
    ```

Now when we created necessary classes we can serialize epure object to a dict:

### 1. `.to_dict()`
```python title="edata.py" hl_lines="4 5 6"
def to_dict(
        self,
        full=False, # (1)!
        lambda_func: Callable 
            = lambda field_name, field_value, field_type, parent_value, rec_depth: # (2)!
            rec_depth < 1 or isinstance(type(parent_value), ECollectionMetacls),
        _rec_depth=0, # (3)!
    ) -> Dict[str, Any]:
```

1. `full` is parameter that you can pass as `#!python True` if you want object to be serialized fully, to its end. Check out example <a href="https://epurelib.github.io/0.1/learn/serialization_deserialization/serialization/#14-using-to_dict-with-full-true">here</a>
2. `#!python lambda_func` is explained more below in note section
3. `#!python _rec_depth` is purely private field for `#!python .to_dict()`. Please avoid using it.

Let's look more closely to the parameters for `#!python .to_dict()`:

??? info "`to_dict()` parameters"

    - `#!python full`

        This `#!python bool` parameter specifies that you want to serialize Epure object fully, all its children. Example here.

    - `#!python lambda_func`

        <!-- This `#!python lamda` parameter is used to specify what fields you want to __exclude__ from resulting dictionary.  -->
        
        <!-- By default it set to serialize children of object for first level or if value is Elist or Eset. -->

        This `#!python lambda` parameter is used to specify what fields of Epure object you want to serialize as `#!python dict` or `#!python UUID` in resulting dictionary. 
        
        By default `lambda_func` is set to serialize fields of object's first level that are of type <a>`Epure`</a>, <a href="https://epurelib.github.io/0.1/learn/elist_eset/#elist">`Elist`</a> or <a href="https://epurelib.github.io/0.1/learn/elist_eset/#eset">`Eset`</a> to <a>`UUID`</a> value. If you specify them explicitly in `lambda_func` they will be serialized as `#!python dict`.

        If you want to use your own custom `#!python lambda` function and pass it to `#!python .to_dict()` - it __must__ follow some requirements:

        - It __must__ return `#!python bool` value

        - It __must__ have __only__ __this__ one signature with __these__ 5 arguments (field_name, field_value, 
                        field_type, parent_value, rec_depth) e.g.:

            !!! failure "Wrong lambda_func"
                ```python hl_lines="2"

                .to_dict(
                    lambda_func = lambda my_condition, another_spec:
                        another_spec += 1
                )
                
                ```

            !!! success "Right lambda_func"
                ```python hl_lines="2 3"

                .to_dict(
                    lambda_func = lambda field_name, field_value, 
                                    field_type, parent_value, rec_depth:
                                    
                        field_type == Elist or field_name != "epure_val"
                )
                
                ```

            <!-- ```python hl_lines="2 4" -->

            <!-- .to_dict( -->
                <!-- lambda_func = lambda field_name, field_value, field_type, parent_value, rec_depth, args: -->
                    <!-- ... -->
                    <!-- your specific fields you want to exclude using these 4 arguments -->
                    <!-- ... -->
            <!-- ) -->
            
            <!-- ``` -->

            <!-- Using `#!python lambda` function with different signature or different return type __will__ __not__ work with `#!python to_dict()` -->

            !!! warning 
                Using `#!python lambda` function with different signature or different return type __will__ __not__ work with `#!python .to_dict()`

        Let's look at parameters by which you can modify `lambda_func`:
        
        - `field_name` - name of field
        - `field_value` - value that is assigned to the field
        - `parent_value` - parent value of that field
        - `rec_depth` - the depth of serialization of Epure type fields of this object to dict, starts count from 0, i.e. "ground" level of object attrs.
        
            For example: if `#!python rec_depth == 0` it will serialize objects of type Epure for first base level only 
            
            And `#!python rec_depth < 2` will serialize children of object (of type Epure) and children of children (that are of type Epure as well)

        !!! example 
            More examples for using custom lambdas <a href="https://epurelib.github.io/0.1/learn/serialization_deserialization/serialization/#13-passing-custom-lambda-function-to-to_dict">here</a>

<!-- !!! note
    `#!python to_dict()` method takes `#!python lambda` (lambda_func) function as parameter in order for you to specify what field 
    you want to __exclude__ from resulting dictionary

    If you want to use your custom lambda function that you pass to `.to_dict()`: it __must__ return `#!python bool` value and have these 4 parameters without them `#!python .to_dict()` __will__ __not__ __work__ even if you dont use them (might change in future)

    parameters by which you can modify lambda function is:
    
    - `field_name` - name of field that will be excluded
    - `field_value` - field with such value will be excluded
    - `parent_value` - field with such parent value will be exluded
    - `rec_depth` - the depth of serialization for this object (for example: if `#!python rec_depth == 0` it will serialize epure object's members for first level __only__)

    By default lambda function is set to only serialize children of first level or if parent of value is <a href="">`Elist`</a> or <a href="">`Eset`</a>

    More examples for using custom lambdas <a>here</a> -->

!!! warning
    `#!python to_dict()` will serialize __only__ fields that were __defined__ in class, assigned new members of class __won't__ __be__ __serialized__
    
    <!-- See example <a href= >here</a> -->

    ??? example
        ```python

        to_dict_ex_inst.not_declared_in_cls_val = "42 42 42"

        res = to_dict_ex_inst.to_dict()

        res["not_declared_in_cls_val"] # -> KeyError('not_declared_in_cls_val')

        ```

Lets look at four case scenarious with `#!python .to_dict()`

#### 1.1 `#!python .to_dict()` when object is saved

Now if we save object and we want to serialize it, we will get dict where epure instances will be represented by UUID `#!python data_id` identifiers

```python
to_dict_ex_inst.save() # (1)!
```

1. Check out more about `#!python .save()` method <a href="">here</a>

And then result will be:

??? success "Result when object is saved"

    ```python

    to_dict_ex_inst.to_dict() # -> 
        "{
            "data_id": "12e3d625-64bf-479b-927e-27f69cfc4872",
            "elist_val": [
                {
                    "data_id": "96067115-e35a-4654-910c-8746596e77d7",
                    "someEpureVal": "b68c45a7-0268-4709-ae9b-818fa75d2976",
                    "some_val": "To the moon!",
                },
                {
                    "data_id": "9fc82b6d-a251-4de4-9313-19d0bd33a4b4",
                    "someEpureVal": "b68c45a7-0268-4709-ae9b-818fa75d2976",
                    "some_val": "To the moon!",
                },
            ],
            "eset_val": [
                {
                    "data_id": "96067115-e35a-4654-910c-8746596e77d7",
                    "someEpureVal": "b68c45a7-0268-4709-ae9b-818fa75d2976",
                    "some_val": "To the moon!",
                },
                {
                    "data_id": "9fc82b6d-a251-4de4-9313-19d0bd33a4b4",
                    "someEpureVal": "b68c45a7-0268-4709-ae9b-818fa75d2976",
                    "some_val": "To the moon!",
                },
            ],
            "epure_val": {
                "data_id": "0f07af91-9e11-45b4-99bd-ae91921be11d",
                "str_val": "keen",
                "int_val": 80,
                "someRandEpureVal": "8bf57d49-17d6-4168-9cca-d22536710673",
            },
            "str_val": "In Tech we trust",
            "int_val": 424,
            "complex_val": 3 + 4j,
            "generic_list": ["cat", "dog", "yak"],
            "UPCASE_VAL": "Some UPcase val",
        }"
    ```

<!-- ##### to_dict() when new field assigned and it that was __not__ declared in Epurized class -->


#### 1.2 `#!python .to_dict()` when object is not saved

!!! info
    When we serialize object that was not saved, fields that were not included in lambda function criteria - will be __`#!python None`__ in resulting dict.

??? success "Result when object is not saved"
    ```python
    to_dict_ex_inst.to_dict() # -> 
        "{
            "elist_val": [
                {"someEpureVal": None, "some_val": "To the moon!"},
                {"someEpureVal": None, "some_val": "To the moon!"},
            ],
            "eset_val": [
                {"someEpureVal": None, "some_val": "To the moon!"},
                {"someEpureVal": None, "some_val": "To the moon!"},
            ],
            "epure_val": {
                "str_val": "keen", 
                "int_val": 80, 
                "someRandEpureVal": None
            },
            "str_val": "In Tech we trust",
            "int_val": 424,
            "complex_val": 3 + 4j,
            "generic_list": ["cat", "dog", "yak"],
            "UPCASE_VAL": "Some UPcase val",
        }"
    ```



#### 1.3 passing custom `#!python lambda` function to `#!python .to_dict()`

So for example we want to get serialized `#!python dict` where:

- All children of object of type <a href="https://epurelib.github.io/0.1/learn/elist_eset/#elist">`Elist`</a> to be serialized __not__ as `#!python dict`, but as `UUID`.

- First and second level children of object of type `Epure` to be serialized as `#!python dict` and __not__ `UUID`.

```python hl_lines="3"
result = to_dict_ex_inst.to_dict(lambda_func = 
        lambda field_name, field_value, field_type, parent_value, rec_depth:
            field_type != Elist and rec_depth <= 1
)
```

The result will be:

??? success "Result"

    ```python hl_lines="4 12 22"

    result # -> 
    "{
        "data_id": "2681b947-e7e7-4ac5-ad02-5cdaf6145f08",   # 1 level child
        "elist_val": "fb3bc534-e54d-4a01-8d0b-3d527c67ae44", # 1 level child
        "eset_val": [                                        # 1 level child
            {
                "data_id": "9dd8d28b-c660-4fa1-a5bd-11636e4d3b45", # 2 level child
                "someEpureVal": {                                   # 2 level child
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955", # 3 level child
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                },
                "some_val": "To the moon!", # 2 level child
            },
            {
                "data_id": "3ab7589d-1a0f-4da9-b66a-3af2a1183902", # 2 level child
                "someEpureVal": {                                  # 2 level child
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955", # 3 level child
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                },
                "some_val": "To the moon!", # 2 level child
            },
        ],
        "epure_val": {                                          # 1 level child
            "data_id": "08be3fe4-c4a7-407e-a447-0f9b7b4e57bc",  # 2 level child
            "str_val": "keen",                                  # 2 level child
            "int_val": 80,                                      # 2 level child
            "someRandEpureVal": {                               # 2 level child
                "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc", # 3 level child
                "someint": 777,
                "somecomplexval": 3 + 4j,
            },
        },
        "str_val": "In Tech we trust",  # 1 level child
        "int_val": 424,                 # 1 level child
        "complex_val": 3 + 4j,          # 1 level child
        "generic_list": ["cat", "dog", "yak"],  # 1 level child
        "UPCASE_VAL": "Some UPcase val",    # 1 level child
    }"

    ```

As you can see elist_val became UUID, and children until 3 level of type Epure became dict

!!! note
    Please note that `full` parameter has higher priority than `lambda_function` parameter. If `full` is True - then every Epure, Elist or Eset value will be serialized to `#!python dict` no matter what was specified in `#!python lambda` function.


#### 1.4 using `#!python .to_dict()` with `#!python full = True`

Parameter `full` can be seen as a sort of short-cut to fully serialize whole object to `#!python dict`.

Because of `full` parameter higher priority than `lambda_func`, these expressions

```python
result = to_dict_ex_inst.to_dict(full = True)
```

and

```python hl_lines="2"
result = to_dict_ex_inst.to_dict(
    full = True, 
    lambda_func = lambda field_name, field_value,\ 
        field_type, parent_value, rec_depth:
                ...
)
```

Will give same result:
??? success "Result"
    ```python

    result #->
    "{
        "data_id": "2681b947-e7e7-4ac5-ad02-5cdaf6145f08",
        "elist_val": [
            {
                "data_id": "b98e0c51-ed27-49b1-a94b-619dc4c084e1",
                "someEpureVal": {
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955",
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": {
                        "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                        "someint": 777,
                        "somecomplexval": 3 + 4j,
                    },
                },
                "some_val": "To the moon!",
            },
            {
                "data_id": "63ae35e8-671c-4e91-823b-9cc8dfcc0c83",
                "someEpureVal": {
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955",
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": {
                        "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                        "someint": 777,
                        "somecomplexval": 3 + 4j,
                    },
                },
                "some_val": "To the moon!",
            },
        ],
        "eset_val": [
            {
                "data_id": "9dd8d28b-c660-4fa1-a5bd-11636e4d3b45",
                "someEpureVal": {
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955",
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": {
                        "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                        "someint": 777,
                        "somecomplexval": 3 + 4j,
                    },
                },
                "some_val": "To the moon!",
            },
            {
                "data_id": "3ab7589d-1a0f-4da9-b66a-3af2a1183902",
                "someEpureVal": {
                    "data_id": "e1d0fa2f-fa6a-4c7a-bd6c-0566b651f955",
                    "str_val": "keen",
                    "int_val": 80,
                    "someRandEpureVal": {
                        "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                        "someint": 777,
                        "somecomplexval": 3 + 4j,
                    },
                },
                "some_val": "To the moon!",
            },
        ],
        "epure_val": {
            "data_id": "08be3fe4-c4a7-407e-a447-0f9b7b4e57bc",
            "str_val": "keen",
            "int_val": 80,
            "someRandEpureVal": {
                "data_id": "8ae6f23e-108b-41c3-a3ae-282adb0e77cc",
                "someint": 777,
                "somecomplexval": 3 + 4j,
            },
        },
        "str_val": "In Tech we trust",
        "int_val": 424,
        "complex_val": 3 + 4j,
        "generic_list": ["cat", "dog", "yak"],
        "UPCASE_VAL": "Some UPcase val",
    }"

    ```




### 2. `to_json()`

<!-- !!! example -->
```python title="edata.py"
def to_json(
        self,
        full=False,
        lambda_func: Callable = lambda field_name, field_value, field_type, parent_value, rec_depth: rec_depth
        < 1
        or isinstance(type(parent_value), ECollectionMetacls),
        _rec_depth=0,
        encoder: Callable = jsonpickle.encode,
    ) -> str:
```

`to_json()` can be treated just as an wrapper for `.to_dict()`, that takes json serializer function by additional `encoder` argument and returns already serialized json string. By default encoder is <a href="https://jsonpickle.github.io/">jsonpickle</a> encoder