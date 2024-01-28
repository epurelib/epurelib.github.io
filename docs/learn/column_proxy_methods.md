<!-- ## Supported ColumnProxy methods with @escript decorator -->

Listed below methods are supported in decorated with `#!python @escript` methods of "epurized" class such as those described in `#!python @escript` section <a href="https://epurelib.github.io/0.1/learn/escript_decorator/#magic-escript-decorator">here</a>

Methods below demonstrate how operators in Epure will be translated in SQL and the class used in examples will be declared here:

```python
    @epure
    class MyEpureCls:
        my_str_field:str
        my_int_field:int
```

!!! warning "Operators Precedence" 
    Precedence of these operators are same as in SQL and brackets __does not__ affect precedence (at least for now).

<!-- '==': '=',
'!=': '<>',
'<': '<',
'<=': '<=',
'>': '>',
'>=': '>=',
'not': 'NOT',
'not in': 'NOT IN',
'and': 'AND',
# 'all': 'ALL',
# 'any': 'ANY',
# 'in range':'BETWEEN',
'in': 'IN',
'like': 'LIKE',
'or': 'OR',
# 'some':'SOME',
'is': 'IS',
'is not': 'IS NOT' -->

### 1) Equality operator "`==`"
    
In Epure: `#!python ==` -> In sql: `#!sql =`

??? example "Example for `#!python ==` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field == "Honesty is the best policy"
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_str_field = "Honesty is the best policy"
    ```


### 2) Inequality operator "`#!python !=`"
    
In Epure: `#!python !=` -> In sql: `#!sql <>`

??? example "Example for `#!python !=` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field != "Honesty is the best policy"
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_str_field <> "Honesty is the best policy"
    ```

### 3) More and more or equals operator "`#!python >`" and "`#!python >=`"
    
In Epure: `#!python >` -> In sql: `#!sql >`

In Epure: `#!python >=` -> In sql: `#!sql >=`

??? example "Example for `#!python >` and `#!python >=` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_int_field > "42"
        self.md.my_int_field >= "80"
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_int_field > 42

    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_int_field >= 80
    ```

### 4) Less and less or equals operator "`#!python <`" and "`#!python <=`"
    
In Epure: `#!python <` -> In sql: `#!sql <`

In Epure: `#!python <=` -> In sql: `#!sql <=`

??? example "Example for `#!python <` and `#!python <=` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_int_field < "42"
        self.md.my_int_field <= "80"
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_int_field < 42

    SELECT * 
    FROM public.my_epure_cls 
    WHERE my_int_field <= 80
    ```

### 5) Not operator "`#!python not`"
    
In Epure: `#!python not` -> In sql: `#!sql NOT`

??? example "Example for `#!python not` operator"
    ```python
    @escript
    def my_decorated_method():
        not self.md.my_int_field > 66
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls 
    WHERE NOT my_int_field > 66
    ```

### 6) Not in operator "`#!python not in`"
    
In Epure: `#!python not in` -> In sql: `#!sql NOT IN`

??? example "Example for `#!python not in` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field not in ("dog", "goes", "woof")
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field NOT IN ("dog", "goes", "woof")
    ```

### 7) And operator "`#!python and`"

In Epure: `#!python and` -> In sql: `#!sql AND`

??? example "Example for `#!python and` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field == "Milky Galaxy" and self.md.my_int_field != 90
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field = "Milky Galaxy" AND my_int_field <> 90
    ```


### 8) All operator "`#!python .all()`" (In development)

### 9) Any operator "`#!python .any()`" (In development)

### 10) Between operator "`#!python .between()`" (In development)

### 11) In operator "`#!python in`"
    
In Epure: `#!python in` -> In sql: `#!sql IN`

??? example "Example for `#!python in` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field in ("dog", "goes", "woof")
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field IN ("dog", "goes", "woof")
    ```

### 12) Like operator "`#!python .like()`"
    
In Epure: `#!python .like()` -> In sql: `#!sql LIKE`

??? example "Example for `#!python .like()` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field.like("%R")
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field LIKE "%R"
    ```

### 13) Or operator "`#!python or`"

In Epure: `#!python or` -> In sql: `#!sql OR`

??? example "Example for `#!python or` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field == "Milky Galaxy" or self.md.my_int_field != 90
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field = "Milky Galaxy" OR my_int_field <> 90
    ```

### 14) Some operator "`#!python .some()`" (In development)

### 15) Is operator "`#!python is`"
    
In Epure: `#!python is` -> In sql: `#!sql IS`

??? example "Example for `#!python is` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field is None
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field IS NULL
    ```

### 15) Is not operator "`#!python is not`"
    
In Epure: `#!python is not` -> In sql: `#!sql IS NOT`

??? example "Example for `#!python is not` operator"
    ```python
    @escript
    def my_decorated_method():
        self.md.my_str_field is not None
    ```
    Will be in SQL:
    ```sql
    SELECT * 
    FROM public.my_epure_cls
    WHERE my_str_field IS NOT NULL
    ```