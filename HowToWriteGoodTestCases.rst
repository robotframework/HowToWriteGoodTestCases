.. default-role:: code

============================
How to write good test cases
============================

General guidelines for writing good test cases using `Robot Framework
<http://robotframework.org>`_.

.. contents:: Table of contents:
   :local:
   :depth: 2


Introduction
============

- These are high-level guidelines for writing test cases. How to actually
  interact with the system under test is out of the scope of this document.
- Most important guideline is keeping test cases as easy to understand as
  possible for people familiar with the domain.
- For more information about this subject, you may want to take a look at
  these great resources:

  - `Dos and Don'ts`__ slides
  - `Writing Maintainable Automated Acceptance Tests`__ article by Dale H. Emery
  - `How to Structure a Scalable And Maintainable Acceptance Test Suite`__
    blog post by Andreas Ebbert-Karroum

__ http://www.slideshare.net/pekkaklarck/robot-framework-dos-and-donts
__ http://cwd.dhemery.com/2009/11/wmaat
__ http://blog.codecentric.de/en/2010/07/how-to-structure-a-scalable-and-maintainable-acceptance-test-suite


Naming
======

Test suite names
----------------

- Suite names should be as describing as possible.
- Names can be relatively long, but over 40 characters starts to be too much.
- Remember that suite names are created automatically from file/directory names:

  - Extensions are stripped
  - Underscores are converted to spaces
  - If name is all lower case, words are capitalized
  - Examples:

    - `login_tests.robot` -> `Login Tests`
    - `IP_v4_and_v6` -> `IP v4 and v6`


Test case names
---------------

- Test names should be describing similarly as suite names.
- If a suite contains many similar tests, and the suite itself is well named,
  test names can be shorter.
- Name is exactly what you you specify in the test case file without any
  conversion.

For example, if we have tests related to invalid login in file
`invalid_login_should_fail.robot`, these would be OK test case names:

.. code:: robotframework

  *** Test Cases ***
  Empty Password
  Empty Username
  Empty Username And Password
  Invalid Username
  Invalid Password
  Invalid Username And Password

These names would be somewhat long:

.. code:: robotframework

  *** Test Cases ***
  Login With Empty Password Should Fail
  Login With Empty Username Should Fail
  Login With Empty Username And Password Should Fail
  Login With Invalid Username Should Fail
  Login With Invalid Password Should Fail
  Login With Invalid Username And Invalid Password Should Fail


Keyword names
-------------

- Also keyword names should be describing and clear.
- Should explain what the keyword does, not how it does it.
- Very different abstraction levels (e.g. `Input Text` or `Administrator
  logs into system`).
- There is no clear guideline should a keyword be title cased (e.g. `My Example
  Keyword`) or should only the first letter is capitalized (e.g. `My example
  keyword`). The former is often used when the keyword name is short, but
  the latter typically works better with keywords that are like sentences.

Good:

.. code:: robotframework

  *** Keywords ***
  Login With Valid Credentials

Bad:

.. code:: robotframework

  *** Keywords ***
  Input Valid Username And Valid Password And Click Login Button


Naming setup and teardown
-------------------------

- Try to use name that describes what is done

  - Possibly use an existing keyword.

- More abstract names acceptable if a setup/teardown contain unrelated steps.

  - Listing steps in name is duplication and a maintenance problem
    (e.g. `Login to system, add user, activate alarms and check balance`)

  - Often better to use something generic (e.g. `Initialize system`)

- BuiltIn keyword `Run Keywords`__ can work well if you have keywords for all
  lower level steps ready.

  - Not reusable so best used when certain setup/teardown scenario is needed only once.

- Everyone working with these tests should always understand what a setup/teardown does.

Good:

.. code:: robotframework

  *** Settings ***
  Suite Setup     Initialize System

Good (if only used once):

.. code:: robotframework

  *** Settings ***
  Suite Setup     Run Keywords
  ...             Login To System    AND
  ...             Add User           AND
  ...             Activate Alarms    AND
  ...             Check Balance

Bad:

.. code:: robotframework

    *** Settings ***
    Suite Setup     Login To System, Add User, Activate Alarms And Check Balance

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Run%20Keywords


Documentation
=============

Test suite documentation
------------------------

- Often a good idea to add overall documentation to test case files.
- Should contain background information, why tests are created, notes about
  execution environment, etc.
- Do not just repeat test suite name.

  - Better not to have documentation all if it is not really needed.

- Do not include too much details about test cases.

  - Tests should be clear enough to understand alone.
  - Duplicate information is waste and maintenance problem.

- Documentation can contain links to more information.
- Consider using test suite metadata if you need to document information
  represented as name-value pairs (e.g. `Version: 1.0` or `OS: Linux`.)

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

- Test normally do not need documentation.

  - Name and possible documentation of the parent suite and tests own name
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
    [Tags]  Iteration-3  Smoke
    Open Login Page
    Input Username    ${VALID USERNAME}
    Input Password    ${VALID PASSWORD}
    Submit Credentials
    Welcome Page Should Be Open
    [Teardown]   Close Browser

Bad:

.. code:: robotframework

  *** Test Cases ***
  Valid Login
    [Documentation]    Opens a browser to login url, inputs username
    ...                and password and checks the welcome page is open.
    ...                This is a smoke test. Created in iteration 3.
    Open Browser   ${URL}  ${BROWSER}
    Input Text     field1  ${UN11}
    Input Text     field2  ${PW11}
    Click Button   button_12
    Title Should Be  Welcome Page
    [Teardown]     Close Browser


User keyword documentation
--------------------------

- Not needed if keyword is relatively simple. Good keyword and argument names
  and clear structure should be enough.

- Important usage is documenting arguments and return values.

- Shown in resource file documentation generated with Libdoc__ and editors
  such as RIDE__ can show it in keyword completion and elsewhere.

__ http://robotframework.org/robotframework/#built-in-tools
__ https://github.com/robotframework/RIDE


Test suite structure
====================

- Tests in a suite should be related to each others.

  - Common setup and/or teardown is often a good indicator.

- Should not have too many tests (max 10) in one suite unless they are
  `data-driven tests`_.

- Tests should be independent. Initialization using setup/teardown.

- Sometimes dependencies between tests cannot be avoided.

  - For example, it can take too much time to initialize all tests separately.
  - Never have long chains of dependent tests.
  - Consider verifying the status of the previous test using
    `${PREV TEST STATUS}` variable.


Test case structure
===================

- Test case should be easy to understand.

- One test case should be testing one thing.

  - *Things* can be small (part of single feature) or large (end-to-end).

- Select suitable abstraction level.

  - Use abstraction level consistently (single level of abstraction principle).
  - Only include information that is relevant for the test case.

- Two kinds of test cases:

  - Workflow tests
  - Data-driven tests


Workflow tests
--------------

- Generally has these phases:

  - Preconditions (optional, often in setup)
  - Action (do something to the system)
  - Verification (must have one!)
  - Cleanup (optional, always in teardown to make sure it is executed)

- Keywords describe what a test does.

  - Use clear keyword names and suitable abstraction level.
  - Should contain enough information to run manually.
  - Should never need documentation or commenting to explain what the test does.

- Different tests can have different abstraction levels.

  - Tests for a detailed functionality are more precise.
  - End-to-end tests can be on very high level.
  - One test should use only one abstraction level

- Different styles

  - More technical tests for lower level details and integration tests
  - "Executable specifications" act as requirements
  - Use domain language
  - Everyone (including customer/product owner) should always understand

- No complex logic on test case level

  - No for loops or if/else constructs
  - Use variable assignments with care
  - Test cases should not look like scripts!

- Max 10 steps, preferably less

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

Data-driven tests
-----------------

- One high-level keyword per test

  - Different arguments create different tests
  - The keyword often contains similar workflow as workflow tests
  - Unless the keyword is needed elsewhere, it is a good idea to have it in
    the same file as tests using it.

- Recommended to use *Test Template* functionality.

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


User keywords
-------------

- Should be easy to understand

  - Same rules as with workflow tests

- Different abstraction levels

- Can contain some programming logic (for loops, if/else)

  - Especially on lower level keywords
  - Complex logic in libraries rather than in user keywords


Variables
=========

- Encapsulate long and/or complicated values
- Pass information from command line
- Pass information between keywords


Variable naming
---------------

- Clear but not too long names

- Can use comments in variable table to document them more

- Use case consistently

  - Lower case with local variables only available inside a certain scope
  - Upper case with others (global, suite or test level)
  - Both space and underscore can be used as word separator

- Recommended to list also variables that are set dynamically in the variable
  table

  - Set typically using `Set Suite Variable`__ keyword
  - The initial value should explain where/how the real value is set

Example:

.. code:: robotframework

  *** Settings ***
  Suite Setup      Set Active User

  *** Variables ***
  # Default system address. Override when tested agains other instances.
  ${SERVER URL}        http://sre-12.example.com/
  ${USER}              Actual value set dynamically at suite setup

  *** Keywords ***
  Set Active User
      ${USER} =    Get Current User    ${SERVER URL}
      Set Suite Variable    ${USER}

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Set%20Suite%20Variable


Passing and returning values
----------------------------

- Common approach is to return values from keywords, assign them to variables
  and then pass them as arguments to other keywords

  - Clear and easy to follow approach
  - Looks like programming and thus not so good on test case level

- Alternative approach is storing information in a library or using `Set Test
  Variable`__ keyword

  - No need to have any programming style on test case level
  - Can be more complex to follow and make reusing keywords harder
  - Avoid below test case level

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

OK:

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

- Sleeping is very fragile

- Safety margins cause too long sleeps on average

- Instead of sleep, use keyword that polls has certain action occurred

  - Should have a maximum time to wait
  - Keyword names often starts with `Wait ...`
  - Possible to wrap other keywords inside `Wait Until Keyword Succeeds`__

- Sometimes sleeping is easiest solution

  - Always use with care
  - Never use in user keywords that are used often by tests or other keywords

- Can be useful in debugging to stop execution, but `Dialogs library`__ often
  works better for that purpose

__ http://robotframework.org/robotframework/latest/libraries/BuiltIn.html#Wait%20Until%20Keyword%20Succeeds
__ http://robotframework.org/robotframework/latest/libraries/Dialogs.html
