# Introducing sham

- date: 2013-12-01 11:56
- tags: python, mock, testing, sham

----

Some people, when their cats throw up in the middle of the night,
go back to sleep. Some people write their own test double library.
I am the latter person.

I just pushed my [initial commit][1] for [`sham`][2], a test double
library I started making because I have issues with the popular
[Mock][3].

Now, I love what Mock *does*.  After [Adam Tauno Williams][4]' 
[presentation][5] on it at the [Grand Rapids Python Users Group][6],
I started using it for [octothorpe][7], replacing some custom-built
test doubles that recorded their behavior.  Mock let me magically
buid those instead, making for a cleaner test suite.

But the API makes me chafe.  My chief beef is this:

````python
>>> m = mock.Mock()
>>> m(True)
<Mock name='mock()' id='4300958352'>
>>> m.assert_called_once_with(True)
>>> m.assert_called_once_with(False)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File ".../mock.py", line 846, in assert_called_once_with
    return self.assert_called_with(*args, **kwargs)
  File ".../mock.py", line 835, in assert_called_with
    raise AssertionError(msg)
AssertionError: Expected call: mock(False)
Actual call: mock(True)
````

At first blush, nothing may seem particularly wrong here, right?
But Mock has magic behavior, which, while useful, can cause some
subtle problems to appear.

````python
>>> m.assert_sky_is_pink()
````

This assertion should not have passed.  Clearly, the sky isn't pink,
but that's not our core problem—our core problem is actually that
`assert_sky_is_pink` is not a valid `assert` method.  But our `Mock`
did what a good `Mock` should do: it spawned itself a brand new
sub-`Mock` called `assert_sky_is_pink` and called *that* instead.

Now, obviously, I don't go around asserting the sky is pink.  But I
don't always keep Mock's API in my head, and as a result once thought
it had a method called `assert_called_once`.  It doesn't, but *my
tests did not fail* when I attempted to make that assertion.  I began
to think that perhaps assertion methods should be kept off the `Mock`
itself—moved to the module, perhaps—so that there was no ambiguity.

Of course, the logical response to this line of reasoning is that
I'm not doing unit testing properly.  I should have noticed my assertion
did not fail, because I'm supposed to make tests that fail first, then
code until they pass.  I get that.  But I'm not always testing first.
In real life, I'm often testing after the fact.  (And why am I calling
a method that doesn't exist?  Well, there's another problem here, and
that is that Mock's API sometimes doesn't make a lot of sense.  I'm
looking at you, `side_effect`.)

`sham` is my attempt to make a compelling replacement for Mock.  I'm
starting clean, I'm experimenting a bit with some useful functionality
like keeping a log.  Right now, all it does is log calls and attribute
gets, but I intend to add more functionality, such as returned values,
side effects, and [replays][8].

````python
>>> s = sham.Sham()
>>> s(True)
>>> sham.assertCallCount(s, 1)
>>> sham.assertCallCount(s, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "sham/__init__.py", line 112, in assertCallCount
    assert count == log_count, '%d == %d' % (count, log_count)
AssertionError: 2 == 1
ham.assertCalledWith(s, True)
>>> sham.assertCalledWith(s, False)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "sham/__init__.py", line 123, in assertCalledWith
    raise AssertionError(repr(match_entry))
AssertionError: <CallLogEntry False, >
>>> s.foo
<sham.Sham object at 0x1004aafd0>
>>> sham.getLog(s)
[(1385900294.091232, <CallLogEntry True, >),
 (1385900331.47395, <GetAttrLogEntry 'foo'>)]
>>> sham.filterLog(s, sham.CallLogEntry)
[(1385900294.091232, <CallLogEntry True, >)]
````

Astute observers will notice this is not very [PEP 8][9].  My primary
reasoning for this is that [`unittest`][10] is also not very PEP 8.
Shifting back and forth between styles in test assertions causes
me mental friction I'd rather not have to deal with, especially
when I've already found I have to unlearn a little bit of Mock's
method call style to properly use sham.  I think it will be worth it in
the end, though.

`sham` is being developed [at GitHub][11].

[1]: https://github.com/zigg/sham/commit/d814d2d9fe48b4e31e06863bb443bcfbe54b0635
[2]: /code/sham/
[3]: http://www.voidspace.org.uk/python/mock/
[4]: http://www.whitemiceconsulting.com/
[5]: http://www.meetup.com/grpython/events/132340202/
[6]: http://www.meetup.com/grpython/
[7]: /code/octothorpe/
[8]: http://martinfowler.com/articles/mocksArentStubs.html#UsingEasymock
[9]: http://www.python.org/dev/peps/pep-0008/
[10]: http://docs.python.org/2/library/unittest.html
[11]: https://github.com/zigg/sham

