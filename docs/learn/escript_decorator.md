## Magic `#!python @escript` decorator

`#!python @escript` decorator is a method-decorator, that is used for decorating methods of "epurized" (1) class
{ .annotate }

1. "epurized" class is class that was decorated by `@epure()` decorator

`#!python @escript` decorator magicly convert your queries into strings

??? note "`#!python @escript` decorator with `#!python @classmethod` decorator"
    ```python
    ...
    @classmethod
    @escript
    def your_method():
    ...
    ```

!!! info
    Only in method decorated by @escript, can be used instances of <a href="">self.dom</a>, <a href="">self.md</a> and method <a href="">self.model</a>

Lets see an example:

???+ example "Retrieving cats with more than one tail using `#!python @escript`"
    For example you have a `#!python class Cat`:
    ```python
    @epure()
    class Cat:
        paws_cntr:int
        name:str
        tail_cntr:int
        likes_catnip:bool
    ```
    And you want to get all the stored cats:

    - with more than one tail (lets assume there are some cat-alien species)

    - and those cats who don't like catnip (a real weird cat-aliens)

    Then we will define a method for `#!python class Cat` decorated by `#!python @escript`:

    ```python hl_lines="6"
    ...
    @classmethod
    @escript
    def get_all_cats_with_two_tails(cat):
        
        my_query = cat.md.tail_cntr > 1 and cat.md.likes_catnip == False
        
        res = cat.resource.read(my_query)
        
        res # [<Cat object at 0x000001FFFC6800D0>, <Cat object at 0x000001FCFC6800D7>, ...]

    ...
    ```

    ??? info "Let's look closely to my_query:"

        ```python
        ...
        my_query = cat.md.tail_cntr > 1 and cat.md.likes_catnip == False
        ...
        ```
        First, because method is a classmethod, in cat variable will be assigned class.

        Then we are taking `.md` <a href="">Model</a> of Cat class and taking <a href="">Column</a> `tail_cntr` and comparing it to more than 1

    In resulting list we will get cats with more than one tail and those who doesnt like catnip. Like expected!

    ??? tip "What will be result SQL of this query"
        ```sql 
        SELECT * 
        FROM public.cat 
        WHERE cat.tail_cntr > 1 AND cat.likes_catnip = False
        ```

All the avalible methods (e.g. `==`, `like`, `is_not`, `_is` etc),  for `self.md` can be found in <a href="">Column Proxy methods section</a>




