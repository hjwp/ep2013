==================
 TDD from scratch
==================

.. Hi everyone, I'm Andrea Crotti, I work for a web startup in London and we
   built a SAAS platform for idea management.

   I decided to give this talk because TDD really change the way I worked and made my life better, so I wanted to share the joy with you.
   So I hope at end of this talk I will have convinced some of you to try it out if you haven't already.


**If it's not tested, it's broken**

Andrea Crotti (@andreacrotti), Harry Percival (@hjwp)

Slides: https://github.com/AndreaCrotti/ep2013/tdd

Book: http://wwww.obeythetestinggoat.com

.. image:: img/wazoku.png
   :height: 70

.. image:: img/goat_book_cover.gif
   :height: 70
 
We are hiring!
==============

.. image:: img/we_want_you.jpg
    :width: 800

Outline
=======

- Why bother?
- Unit tests
- Pure functions vs side effects
- Fighting side effects

  + Dependency injection
  + Patching

- TDD cycle
- Test coverage

Dynamic language
================

.. As we all know Python is a great language, but it's really dynamic
   and it gives you only runtime errors.
.. You can and should use some great tools like Pylint to get a much
.. better static analysis but it's still far from what you can get
.. from a less dynamic language.
.. So more than enough rope to hang yourself!


Python is **awesome**, but

.. TODO: add a unicode skull or something similar instead

- duck typing
- no checked exceptions
- no compilation errors


.. image:: img/noose.jpg
   :align: center

Typo
====
.. Here for example I have this function that under some rare
.. condition would fail
.. Can you see any problem with it?

.. Probably not because it's very hard to spot.

.. rst-class:: build

::
   
    Traceback (most recent call last):
      File "rare_cond.py", line 21, in <module>
        smart_function(42)
      File "rare_cond.py", line 7, in smart_function
        report_error(argO)
    NameError: global name 'argO' is not defined

    
.. literalinclude:: code/rare_cond.py
    :pyobject: smart_function


Fail
====

.. image:: img/testing-goat.jpg
   :scale: 150%
    
Misunderstanding
================

.. Here is another example from my personal experience, I actually got
.. bitten many times by this.  What happens if you pass by mistake a
.. string instead of a list of strings to a function that takes an
.. iterable?

.. Well a string is also an iterable so it will just interpret the string
.. as a list of characters, which is most likely not what we want.

.. rst-class:: build


::

    ['W', 'O', 'R', 'D', '1']

::

    >>> uppercase_words("word1")

::

    def uppercase_words(words):
        """Take a list of words and upper case them
        """
        return [w.upper() for w in words]


Does not fails, but still clearly **wrong**

Fail
====

.. image:: img/testing-goat.jpg
   :scale: 150%


.. TODO: should I move the unit test slides to after the change of
   perspective maybe?

Unit test
=========

.. Well for me the consequence the only way to sleep at night
.. given all these possible sources of disasters just behind the
.. corner is to really be methodic about testing.

.. And the good news though is that Python is really awesome for writing
.. tests.

.. In this talk I will only concentrate on unit testing even if the
.. same principles apply for other things.

.. A unit test is small, isolated, testing a very small part of the
.. code.  When a test fail you would know the 5-lines range of code
.. that generates the problem.

.. It's not a unit test if something else that it's not the specific
.. part of the code has to work

.. The fast is really important actually, because for TDD you really
.. need to run your tests continuosly, possibly before every commit,
   and if you
.. start to have slow tests it will get too annoying very quickly.

- small
- isolated
- localized
- without external dependencies
- **fast**

Not a unit test
===============
.. TODO: see if this should be there at all, maybe it's not so
.. necessary

.. Here is an example of what not a unit test is, the following code
.. should test a reporting function, but before doing that it needs to
.. put some values into a database.

.. literalinclude:: code/not_unit.py

.. Andrea, if you like, I can come in here and say a few words about 
.. the difference between unit tests, integration  tests, functional
.. tests, what you can use each one for.


Change of perspective
=====================

.. when you start to write your code with unit tests in mind, there is
.. a big change of perspective.  The question is not anymore *how do I
.. get this working*, but *how do I test it*?  And it turns out that
.. this question is much more valuable, because in the end you want to
.. ship something that you can prove to be working.

.. But programmers are lazy, so what is the easiest thing that you can
.. possibly test.

.. Pure functions are the easiest thing to test, because they can be
.. be simply described as a table of input-output.

**How can I prove it works**

.. image:: img/lazy.jpg

- What is the easiest thing to test?


Testing pure functions
======================

.. this is the possibly most trivial example of something we can test.
.. We have a mysum and mysubstract functions, and to test them we.

.. We can even combine the two operations using some mathematical properties
.. that we know should hold.

.. literalinclude:: code/calc_1.py
    :lines: 1-16


Side effects
============

.. unfortunately life is not so simple, and we can't only work with
.. pure functions, otherwise our program would not be able to do
.. anything useful.

.. We also have side effects, where a side effect happens whenever a
.. function in addition to returning a value, also modifies some state
.. or has an observable interaction with calling functions.

.. For example, a function might modify a global or static variable,
.. modify one of its arguments, raise an exception, write data to a
.. display or file, read data, or call other side-effecting functions.

**In addition to returning a value, it also modifies some state or has an observable interaction with calling functions or the outside world**


.. literalinclude:: code/funcs.py
    :pyobject: silly_function

::

     >>> funcs.silly_function(1)
     3
     >>> funcs.silly_function(1)
     4

Depends on the global state -> side effect -> **hard to test**

.. TODO: where can I put this information somewhere?
..

More side effects
=================

.. This is another more meaningful example of what a side effect can be.
.. This time the return value of the function depends on the function asctime,
.. which itself depends on a global external variable that is time.
.. So how can we unit test this?

.. literalinclude:: code/dep_inj.py
    :lines: 1-6

**How do I test this??**


Dependency injection
====================

.. Dependency injection is a software design pattern that allows the
   removal of hard-coded dependencies and makes it possible to change
   them, whether at run-time or compile-time.[1]

.. One possible way to solve this problem is to use dependency injection.
.. Dependency injection is a technique that allows you to inject

.. the problem with dependency injection is that it requires you
.. to change the production code just to make it more easy to test,
.. and not really adding any value to it.

.. literalinclude:: code/dep_inj.py
    :pyobject: ReportDep
    
.. literalinclude:: code/dep_inj.py
    :pyobject: test_report


**UGLY** let's leave it to the Java guys.

Any other solution?
===================

library.py:

.. literalinclude:: code/library.py

prog.py:

.. literalinclude:: code/prog.py


Mocking
=======

.. luckily we don't need to do all this by ourselves, I just wanted
.. to show that there is nothing particularly magic in this
.. library but it 

.. image:: img/mocking.jpg
   :align: center

- Automate this fiddling process
- Every time you mock you do one step away from the real system
- **Functional core imperative shell**


Mock methods
============

Mock the behaviour of an object that we don't want to run.

.. literalinclude:: code/patching/simple.py
   :pyobject: MyObject

.. literalinclude:: code/patching/test_simple.py
   :pyobject: TestSimple

Patching
========

lib.py:

.. literalinclude:: code/patching/lib.py

test_lib.py:

.. literalinclude:: code/patching/test_lib.py
   :pyobject: TestLib


Cycle
=====

.. Write the test:
.. Being able to write the tests before writing the code means that we
.. really need to understand the requirement well, and we force
.. ourselves to take some time thinking about them, before we get
.. cracking writing some code.


.. Make it pass minimally:
.. After we wrote a test, write the simplest thing that can make it
.. pass, and not more

.. show some examples of why these things can be bad (passing wrong types,
.. raising things from anywhere and so on)

.. rst-class:: build

- Add a test
- Watch it fail
- Make it pass
- Refactor (if possible)
- Add a test


Make it fail
============

.. this second step is understimated but it's very important, because
.. it removes the possibility that the test you're writing would not
.. be always passing for a programming error, and thus completely useless

.. The reason is that there is nothing wrong than having tests with
.. simple bugs that are always passing, because in this way you would
.. never check that the bug is in the *empty* since there is a passing
.. test for that.

- the test should fail if there is a bug

.. literalinclude:: code/passing_test.py


Refactoring example
===================

.. literalinclude:: code/refactor/refactor.py
   :pyobject: long_crappy_function

.. how can I actually test this function, it takes no arguments and it
.. needs to access to the filesystem, mysql and manipulate a list

.. the great thing about python is that we can still do but it's much
.. harder


Coverage
========

http://nedbatchelder.com/code/coverage/

- set a tracer function
- runs a script
- keep track of all the lines executed
- show a report

As simple as:

*nosetests show_cov.py --with-cov --cov-report=html*

Coverage
========

.. Something very interesting happened the other day while I was looking at this code again.
.. I wrote this code long time ago using Python2 and now I wanted to run everything with Python3, and noticed that one of the lines was not getting executed even if all the tests where running.
.. Only then I remembered that in Python3 the division is float by default, but the great thing is that I remembered that just by looking at the test coverage.

.. literalinclude:: code/show_cov.py
   :pyobject: smart_division


My setup
========

- tests/unit, tests/integration, tests/performance...
- *./run-tests.sh* runs all the unit test
- *./run-tests.sh full* runs also the integration tests
- *./coverage.sh* produces coverage report
- .git/hooks/pre-commit -> $PROJECT/run-tests.sh


Selenium testing
================

- "functional" aka "acceptance" aka "E2E" aka "full stack" aka "black box" aka "big" tests
- come a long way since days of v.1. webdriver works v. well
- firefox / chrome / ie / phantomjs / android / ios
- only real way of testing javascript / 3rd-part client-side plugins
- in our (very particular) case, *these are the only tests that find unexpected bugs*
- http://www.PythonAnywhere.com/ - full test suite takes 6 hours.
- ways of alleviating the problem -- not every use case needs a selenium/browser test.
- lots more info in my book!  http://wwww.obeythetestinggoat.com
- Demo!


Conclusion
==========

- tests are good
- tests are documentation
- testing is easy
- testing alleviates the fear of change
- testable code is *better code*

.. image:: img/happy.jpg
   :width: 300


Questions?
==========

Twitter: @andreacrotti
Slides: https://github.com/AndreaCrotti/ep2013

.. image:: img/wazoku.png
   :height: 70

.. _functional_core_imperative_shell: https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell
.. _tip: http://lists.idyll.org/listinfo/testing-in-python
.. _nose: https://nose.readthedocs.org/en/latest/
.. _mock: http://www.voidspace.org.uk/python/mock/
.. _coverage: http://nedbatchelder.com/code/coverage/
.. _func_imp: https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell

- tip_
- nose_
- mock_
- coverage_
- functional_core_imperative_shell_

..
   (that last is also available in the shape of two PyCon talks,
   `Fast Test, Slow Test
   <https://www.youtube.com/watch?v=RAxiiRPHS9k>`_ 
   and
   `Boundaries
   <https://www.youtube.com/watch?v=eOYal8elnZk>`_
   although you should absolutely subscribe to DAS as well. Consider it a taster.
   )


