## Deserialization using `#!python .from_dict()` and `#!python .from_json()`

### 1. `.from_dict()`
```python title="edata.py"
@classmethod
def from_dict(cls, _dict:Dict[str, Any]) -> Epure
```

`.from_dict()` is a class-method of "epurized" (1) class that deserialiezes `#!python dict` to an object of this "epurized" class
{ .annotate }

1. "epurized" class is class that was decorated by `@epure()` decorator

We will use {==Example with nested Epure values==} defined in `#!python to_dict()` section. You can find it <a href="http://127.0.0.1:8000/epure-docs/learn/serialization_deserialization/#serialization-with-to_dict-or-to_json">here</a>:

#### 1.1 `.from_dict()` when object is saved

```python

to_dict_ex_inst.save()

serialized_obj = to_dict_ex_inst.to_dict()

```

Result of `to_dict()` with this object you can examine <a href="http://127.0.0.1:8000/epure-docs/learn/serialization_deserialization/#11-to_dict-when-object-is-saved">here</a>:

```python

deserialized_obj = ToDictEx.from_dict(serialized_obj)

deserialized_obj.data_id # -> "12e3d625-64bf-479b-927e-27f69cfc4872"
deserialized_obj.str_val # -> "In Tech we trust"
deserialized_obj.generic_list # -> ["cat", "dog", "yak"]
```

#### 1.2 `.from_dict()` when object is __not__ saved

!!! warning
    Using `.from_dict()` with `#!python dict` that was result of not saved serialized object, may lead to creation of Epure object with `#!python None` or `#!python {}` fields where Epure field was supposed to be

### 2. `.from_json()`

Similary to `to_json`, `.from_json()` is a wrapper for `.from_dict()` that takes json_string and additional argument of json decoder, to decode json to `#!python dict`. By default, decoder is <a href="https://jsonpickle.github.io/">jsonpickle</a> decoder

```python title="edata.py"
@classmethod
def from_json(cls, json_str:str, decoder=jsonpickle.decode) -> Epure: 
```