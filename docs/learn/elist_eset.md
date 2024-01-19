<a>`Elist`</a> and <a>`Eset`</a> are <a href="https://docs.python.org/3/library/stdtypes.html#generic-alias-type">Generic</a> Alias classes and are created with subscripting the <a>`Elist`</a> or <a>`Eset`</a> class with the argument of python primitive `#!python type` or <a href="">Epure</a> `#!python type` e.g.

??? example "Example of creating Elist or Eset by subscriptiting type"
    ```python
    Elist[str]

    Eset[int]
    ```

    or

    ```python
    @epure()
    class MyEpure:
        ...

    Elist[MyEpure]

    Eset[MyEpure]
    ```

!!! info "Checking type of Elist or Eset"

    <a>`Elist`</a> and <a>`Eset`</a> classes constructor is defined in `ECollectionMetacls`, so to check if class is `Elist` or `Eset`, it is best to use `isinstance()` function

    ??? example "Example of checking Elist or Eset type"
        ```python
        isinstance(Elist[str], ECollectionMetacls) # True
        ```

        ```python
        
        ```

!!! warning "Warning: Nested Generic Types"
    Elist and Eset does not support nested Generics Types
    ??? failure "Example of not supported subscription"
        ```python
        Elist[list[str]]
        ```


## Elist


## Eset

