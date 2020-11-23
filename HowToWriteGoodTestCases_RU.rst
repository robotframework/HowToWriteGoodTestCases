.. default-role:: code

==================================================
Как писать автотесты используя Robot Framework
==================================================

.. contents:: Содержание:
   :local:
   :depth: 2


Введение
============

- Это руководство дает обобщенные советы по написания наборо тестов с использованием Robot
  Framework.

  - Детальное описание взаимодействия с тестируемыми системами выходит за рамки настоящего руководства.

- Важнейшим принципом написания тестов является его максимальная понятность для людей, знакомых с предметной областью.

  - Обычно такие тесты еще и легко поддерживать.

- За дополнительными сведениями по этой теме вы можете обратится к следующим замечательным источникам:

  - `Robot Framework Dos and Don'ts`__ презентация в основе которой это руководство.
  - Статья Дейла Эмери `Writing Maintainable Automated Acceptance Tests`__.
  - Запись в блоге Андреаса Эбберт-Кароум `How to Structure a Scalable And Maintainable Acceptance Test Suite`__.

__ http://www.slideshare.net/pekkaklarck/robot-framework-dos-and-donts
__ http://dhemery.com/pdf/writing_maintainable_automated_acceptance_tests.pdf
__ http://blog.codecentric.de/en/2010/07/how-to-structure-a-scalable-and-maintainable-acceptance-test-suite


Выбор имен
==========

Названия наборов тестов
-----------------------

- Названия наборов тестов должны полно описывать содержание.

- Имена создаются автоматически на основе названий файлов или каталогов:

  - Расширения файлов отбрасываются.
  - Символы подчеркивания преобразуются в пробелы.
  - Если имя в нижнем регистре, то слова пишутся заглавными буквами.

- Имя может быть длинным, но слишком длинные имена могут не походит под ограничения файловой системы.

- В случае необходимости имя набора тестов может быть перезаписано из командной строки с использованием опции `--name`.

Примеры:

- `login_tests.robot` -> `Login Tests`
- `IP_v4_and_v6` -> `IP v4 and v6`


Названия тестовых сценариев
----------------------------

- Имена тестов должны такими же описательными, как и названия наборов тестов.

- Если набор тестов содержить много похожих тестов и имеет подходяещее название, то название входящих в него тестов можно сделать короче.

- Имя теста отображается в точности так, как вы его зададите в файле тестового сценария.

Например, если у вас есть тесты, связанные с проверкой невалидного входа в систему в файле `невалидный_вход.robot`, то это будут подходящие имена:

.. code:: robotframework

  *** Test Cases ***
  Пустой пароль
  Пустое имя пользователя
  Пустой пароль и имя пользователя
  Неверное имя пользователя
  Неверный пароль
  Неверный пароль и имя пользователя

А эти имена будут слегка длинными:

.. code:: robotframework

  *** Test Cases ***
  Вход с пустым поролем не должен быть успешным
  Вход с пустым именем пользователя не должен быть успешным
  Вход с пустым именем пользователя и паролем не должен быть успешным
  Вход с неверным поролем не должен быть успешным
  Вход с неверным именем пользователя не должен быть успешным
  Вход с невернымм именем пользователя и паролем не должен быть успешным


Названия ключевых слов
----------------------

- Название ключевого слова должно описывать выполняемое действие и быть ясными.

- Навзание должно описывать, **что** делает это ключевое слов, а не то, **как** оно это делает.

- Может иметь разные уровни абстракции (например, `Ввод текста` или `Администратор входит в систему`).

- Нет четкого правил, которое бы определяло должны ли быть заглавными первые буквы во всех словах (title casing) или же заглавной должна быть только первая буква первого слова.

  - Title casing обычно используется, если название короткое (например. `Ввод текста`).
  - Заглавная буква в первом слове обычно работает лучше, если название похоже на законченное предложение (например, `Администратор входит в систему`). Обычно это ключевые слова более высокого уровня.

Правильно:

.. code:: robotframework

  *** Keywords ***
  Login With Valid Credentials

Не правильно:

.. code:: robotframework

  *** Keywords ***
  Input Valid Username And Valid Password And Click Login Button


Выбор имен для процедур подготовки и завершения тестов
----------------------------------------------------------------

- Старайтесь использовать имена, которые описывают то, что делает это ключевое слово.

  - По возможности, используйте уже существующие ключевый слова.

- Более обобщенные названия приемлемы, если эти процедуры содержать несвязанные шаги.

  - Перечисление шагов в названии приводит к дублированию и проблемам с поддержкой
    (например, `Войти в систему, добавить пользователя, активировать оповещение и проверить баланс`).

  - Часто бывает, что луше использовать более общее описание (например,  `Инициализировать систему`).

- Подходящим может быть встроенное ключевое слово `Run Keywords`__ , если в процедуре используются готовые ключевые слова более низкого уровня.

  - Этот способ не подходит для повторного использования, поэтому лучше использовать его, если эта процедура будет использоваться только в одном тесте.

- Всякий, кто работет с этим тестом, должен понимать, что эти процедуры делают.

Правильно:

.. code:: robotframework

  *** Settings ***
  Suite Setup     Инициализировать систему

Правльно (если используется только в одном месте):

.. code:: robotframework

  *** Settings ***
  Suite Setup     Run Keywords
  ...             Войти в систему    AND
  ...             Добавить пользователя   AND
  ...             Активировать оповещение    AND
  ...             Проверить баланс

Не правильно:

.. code:: robotframework

    *** Settings ***
    Suite Setup     Войти в систему, добавить пользователя, активировать оповещение и проверить баланс

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Run%20Keywords


Documentation
=============

Test suite documentation
------------------------

- Often a good idea to add overall documentation to test case files.

- Should contain background information, why tests are created, notes about
  execution environment, etc.

- Do not just repeat test suite name.

  - Better to have no documentation if it is not really needed.

- Do not include too much details about test cases.

  - Tests should be clear enough to understand alone.
  - Duplicate information is waste and maintenance problem.

- Documentation can contain links to more information.

- Consider using test suite metadata if you need to document information
  represented as name-value pairs (e.g. `Version: 1.0` or `OS: Linux`).

- Documentation and metadata of the top level suite can be set from the
  command line using `--doc` and `--metadata` options, respectively.

Good:

.. code:: robotframework

  *** Settings ***
  Documentation    Tests to verify that account withdrawals succeed and
  ...              fail correctly depending from users account balance
  ...              and account type dependent rules.
  ...              See http://internal.example.com/docs/abs.pdf
  Metadata         Version    0.1

Bad (especially if suite is named well like `account_withdrawal.robot`):

.. code:: robotframework

  *** Settings ***
  Documentation    Tests Account Withdrawal.


Test case documentation
-----------------------

- Test normally does not need documentation.

  - Name and possible documentation of the parent suite and test's own name
    should give enough background information.
  - Test case structure should be clear enough without documentation or other
    comments.

- Tags are generally more flexible and provide more functionality (statistics,
  include/exclude, etc.) than documentation.

- Sometimes test documentation is useful. No need to be afraid to use it.

Good:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      [Tags]    Iteration-3    Smoke
      Open Login Page
      Input Username    ${VALID USERNAME}
      Input Password    ${VALID PASSWORD}
      Submit Credentials
      Welcome Page Should Be Open

Bad:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      [Documentation]    Opens a browser to login url, inputs valid username
      ...                and password and checks that the welcome page is open.
      ...                This is a smoke test. Created in iteration 3.
      Open Browser    ${URL}    ${BROWSER}
      Input Text    field1    ${UN11}
      Input Text    field2    ${PW11}
      Click Button    button_12
      Title Should Be    Welcome Page


User keyword documentation
--------------------------

- Not needed if keyword is relatively simple.

  - Good keyword, argument names and clear structure should be enough.

- Important usage is documenting arguments and return values.

- Shown in resource file documentation generated with Libdoc__ and editors
  such as RIDE__ can show it in keyword completion and elsewhere.

__ http://robotframework.org/robotframework/#built-in-tools
__ https://github.com/robotframework/RIDE


Test suite structure
====================

- Tests in a suite should be related to each other.

  - Common setup and/or teardown is often a good indicator.

- Should not have too many tests (max 10) in one file unless they are
  `data-driven tests`_.

- Tests should be independent. Initialization using setup/teardown.

- Sometimes dependencies between tests cannot be avoided.

  - For example, it can take too much time to initialize all tests separately.
  - Never have long chains of dependent tests.
  - Consider verifying the status of the previous test using the built-in
    `${PREV TEST STATUS}` variable.


Test case structure
===================

- Test case should be easy to understand.

- One test case should be testing one thing.

  - *Things* can be small (part of a single feature) or large (end-to-end).

- Select suitable abstraction level.

  - Use abstraction level consistently (single level of abstraction principle).
  - Do not include unnecessary details on the test case level.

- Two kinds of test cases:

  - `Workflow tests`_
  - `Data-driven tests`_


Workflow tests
--------------

- Generally have these phases:

  - Precondition (optional, often in setup)
  - Action (do something to the system)
  - Verification (validate results, mandatory)
  - Cleanup (optional, always in teardown to make sure it is executed)

- Keywords describe what a test does.

  - Use clear keyword names and suitable abstraction level.
  - Should contain enough information to run manually.
  - Should never need documentation or commenting to explain what the test does.

- Different tests can have different abstraction levels.

  - Tests for a detailed functionality are more precise.
  - End-to-end tests can be on very high level.
  - One test should use only one abstraction level

- Different styles:

  - More technical tests for lower level details and integration tests.
  - "Executable specifications" act as requirements.
  - Use domain language.
  - Everyone (including customer and/or product owner) should always understand.

- No complex logic on the test case level.

  - No for loops or if/else constructs.
  - Use variable assignments with care.
  - Test cases should not look like scripts!

- Max 10 steps, preferably less.

Example using "normal" keyword-driven style:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      Open Browser To Login Page
      Input Username    demo
      Input Password    mode
      Submit Credentials
      Welcome Page Should Be Open

Example using higher level "gherkin" style:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
      Given browser is opened to login page
      When user "demo" logs in with password "mode"
      Then welcome page should be open

See the `web demo project <https://github.com/robotframework/WebDemo/>`_
for executable versions of the above examples.

Data-driven tests
-----------------

- One high-level keyword per test.

  - Different arguments create different tests.
  - One test can run the same keyword multiple times to validate multiple
    related variations

- If the keyword is implemented as a user keyword, it typically contains
  a similar workflow as `workflow tests`_.

  - Unless needed elsewhere, it is a good idea to create it in the same file
    as tests using it.

- Recommended to use the *test template* functionality.

  - No need to repeat the keyword multiple times.
  - Easier to test multiple variations in one test.

- Possible, and recommended, to name column headings

- If a really big number of tests is needed, consider generating them based
  on an external model.

Example:

.. code:: robotframework

  *** Settings ***
  Test Template         Login with invalid credentials should fail

  *** Test Cases ***    USERNAME             PASSWORD
  Invalid Username      invalid              ${VALID PASSWORD}
  Invalid Password      ${VALID USERNAME}    invalid
  Invalid Both          invalid              invalid
  Empty Username        ${EMPTY}             ${VALID PASSWORD}
  Empty Password        ${VALID USERNAME}    ${EMPTY}
  Empty Both            ${EMPTY}             ${EMPTY}

  *** Keywords ***
  Login with invalid credentials should fail
      [Arguments]    ${username}    ${password}
      Input Username    ${username}
      Input Password    ${password}
      Submit Credentials
      Error Page Should Be Open

The `web demo project`_ contains an executable version of this example too.


User keywords
=============

- Should be easy to understand.

  - Same rules as with workflow tests.

- Different abstraction levels.

- Can contain some programming logic (for loops, if/else).

  - Especially on lower level keywords.
  - Complex logic in libraries rather than in user keywords.


Variables
=========

- Encapsulate long and/or complicated values.

- Pass information from them command line using the `--variable` option.

- Pass information between keywords.


Variable naming
---------------

- Clear but not too long names.

- Can use comments in variable table to document them more.

- Use case consistently:

  - Lower case with local variables only available inside a certain scope.
  - Upper case with others (global, suite or test level).
  - Both space and underscore can be used as a word separator.

- Recommended to also list variables that are set dynamically in the variable
  table.

  - Set typically using BuiltIn keyword `Set Suite Variable`__.
  - The initial value should explain where/how the real value is set.

Example:

.. code:: robotframework

  *** Settings ***
  Suite Setup       Set Active User

  *** Variables ***
  # Default system address. Override when tested agains other instances.
  ${SERVER URL}     http://sre-12.example.com/
  ${USER}           Actual value set dynamically at suite setup

  *** Keywords ***
  Set Active User
      ${USER} =    Get Current User    ${SERVER URL}
      Set Suite Variable    ${USER}

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Set%20Suite%20Variable


Passing and returning values
----------------------------

- Common approach is to return values from keywords, assign them to variables
  and then pass them as arguments to other keywords.

  - Clear and easy to follow approach.
  - Allows creating independent keywords and facilitates re-use.
  - Looks like programming and thus not so good on the test case level.

- Alternative approach is storing information in a library or using the BuiltIn
  `Set Test Variable`__ keyword.

  - Avoid programming style on the test case level.
  - Can be more complex to follow and make reusing keywords harder.

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Set%20Test%20Variable

Good:

.. code:: robotframework

  *** Test Cases ***
  Withdraw From Account
      Withdraw From Account    $50
      Withdraw Should Have Succeeded

  *** Keywords ***
  Withdraw From Account
      [Arguments]    ${amount}
      ${STATUS} =    Withdraw From User Account    ${USER}    ${amount}
      Set Test Variable    ${STATUS}

  Withdraw Should Have Succeeded
      Should Be Equal    ${STATUS}   SUCCESS

Not so good:

.. code:: robotframework

  *** Test Cases ***
  Withdraw From Account
      ${status} =    Withdraw From Account    $50
      Withdraw Should Have Succeeded    ${status}

  *** Keywords ***
  Withdraw From Account
      [Arguments]    ${amount}
      ${status} =    Withdraw From User Account    ${USER}    ${amount}
      [Return]    ${status}

  Withdraw Should Have Succeeded
      [Arguments]    ${status}
      Should Be Equal     ${status}    SUCCESS


Avoid sleeping
==============

- Sleeping is a very fragile way to synchronize tests.

- Safety margins cause too long sleeps on average.

- Instead of sleeps, use keyword that polls has a certain action occurred.

  - Keyword names often starts with `Wait ...`.
  - Should have a maximum time to wait.
  - Possible to wrap other keywords inside the BuiltIn keyword
    `Wait Until Keyword Succeeds`__.

- Sometimes sleeping is the easiest solution.

  - Always use with care.
  - Never use in user keywords that are used often by tests or other keywords.

- Can be useful in debugging to stop execution.

  - `Dialogs library`__ often works better.

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Wait%20Until%20Keyword%20Succeeds
__ http://robotframework.org/robotframework/latest/libraries/Dialogs.html
