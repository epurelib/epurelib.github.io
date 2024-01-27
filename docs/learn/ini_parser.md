In Epure this section for parsing Ini files appeared because other python libraries that exist are somewhat inconvinient and complicated.

Having a ini file - we can just get it fields using . (dot) notation.

???+ example "Parsing Ini File"

    ```ini title="example.ini"
    db_host = localhost

    [general]
    db_port = 5432

    [public]

    [section1]
    db_port = 323

    [section1.section2]
    db_user = user

    [section1.section2.section3]
    pi_begins = 3.14159265359

    [epure.best.app.forever]
    epure_is_the_best = true
    ```

    We can easily parse it:

    ```python 
    example_ini = IniFile('./example.ini')

    db_host = example_ini.db_host # 'localhost'

    db_port = example_ini.general.db_port # 5432

    db_user = example_ini.section1.section2.db_user # "user"
    an_db_user = example_ini.section1.db_port # 323

    pi_begins = example_ini.section1.section2.section3.pi_begins # 3.14159265359

    epure_is_the_best = example_ini.epure.best.app.forever.epure_is_the_best # True

    db_host = example_ini.not_exist_prop # None

    example_ini = IniFile('not_exist_config.ini') # non-existent ini file will raise an error
    db_host = example_ini.db_host
    assert db_host == None
    ```