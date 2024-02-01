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
    Only in method decorated by @escript, can be used instances of <a href="https://epurelib.github.io/0.1/learn/domain_model/#domain">self.dom</a>, <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">self.md</a> and method <a href="">self.model</a>

Lets see an example:

### Alien Cats example 👽🐈

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

    - with more than one tail (lets assume there are some cat-alien species 👽)

    - and those cats who don't like catnip (a real weird cat-aliens)

    Then we will define a :magic_wand: method :magic_wand: for `#!python class Cat` decorated by `#!python @escript`:

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

        Then we are taking `.md` <a href="https://epurelib.github.io/0.1/learn/domain_model/#model">Model</a> of Cat class and taking <a href="https://epurelib.github.io/0.1/learn/column_proxy_methods/">Column</a> `tail_cntr` and comparing it to more than 1

    In resulting list we will get cats with more than one tail and those who doesnt like catnip. Like expected!

    ??? tip "What will be result SQL of this query"
        ```sql 
        SELECT * 
        FROM public.cat 
        WHERE cat.tail_cntr > 1 AND cat.likes_catnip = False
        ```

!!! info "All avalible for `self.md` methods"
    All the avalible methods (e.g. `==`, `like`, `is_not`, `_is` etc),  for `self.md` can be found in <a href="https://epurelib.github.io/0.1/learn/column_proxy_methods/">Column Proxy methods section</a>

### More Advanced Alien Cats 👽🐈 example with `#!py for`

So if we want to create something more complex, lets use `#!py for` statement:

???+ example "Creating advanced _smart_ query with `#!py for`"

    So as in previous example you have a `#!python class Cat`:
    ```python
    @epure()
    class Cat:
        paws_cntr:int
        name:str
        tail_cntr:int
        likes_catnip:bool
    ```

    But this time you want to get `Cat`(s) that:

    - has a tail (i.e `tail_cntr` is not `#!sql NULL``)
    - has a name and name starts with letter `'C'`
    - has less than 5 paws or equally 8 paws (more like an octocat :simple-github:)
    - likes catnip 🍀

    So we will define :magic_wand: _magic_ :magic_wand: method for `Cat` class:

    ```python linenums="1"
    ...
    @classmethod
    @escript
    def get_cats_using_for(cat):
        
        has_tail_query = cat.md.tail_cntr is not None

        name_query = cat.md.name is not None and cat.md.name.like("C%")

        legs_query = cat.md.paws_cntr < 5 or cat.md.paws_cntr == 8

        catnip_query = cat.md.likes_catnip == True

        ...
    ```
    After we created our queries, imagine: we want all to this conditions to meet and be `#!py True` at the same time. 
    
    But what do we do? 🤔
    
    Create a long-long query, that is unreadable and to much hassle to edit? Nah ❌. 
    
    I suggest to use `#!py for` loop ✔️. 
    
    This way, we can put them in a `#!py list` and iterate over it adding `#!py and` to each condition, sticking it all together
    ```py linenums="15" hl_lines="6"
        ...
        queries_list = [name_query, legs_query, catnip_query]

        result_query = has_tail_query

        for query in queries_list:
           result_query = result_query and query
        
        res = cat.resource.read(result_query)
        
        res # [<Cat object at 0x000001FFFC6800D0>, <Cat object at 0x000001FCFC6800D7>, ...]
        
    ```
    ??? note "line 18 `result_query = has_tail_query` explanation"
        This assignemnt made mainly made because we need to start somewhere and we cant use empty string ("") as a start.

    This way we will get a long _smart_ query without any hassle and code confusion.