## Definitions

| Test Type        | Description    |
|------------------|----------------|
| Unit test        | A test of specific functionality, whose execution does not reach outside of the current CPU process. |
| Integration test | A test of underlying functionality, which tests multiple connected pieces oflogic.  These may and typically do reach outside of the current CPU process. |


## Objective

The purpose of automated testing is to create an objective, repeatable set of logic which verifies
that the logic being tested performs as expected in all reasonable scenarios.  By having such a
process in place, it allows the codebase to grow while ensuring that existing functionality is not
broken.  Moreover, being automated ensures that little human effort or intervention is required to
make such a verification, which both increases confidence in the codebase and frees up developer
time that would otherwise be required to perform said testing manually.

Initially, our focus will be on unit testing.  Integration testing will be introduced, as needed.

It is highly likely that the amount of test code written will exceed that of the code being tested.
This is normal and expected.


## Guidelines


### Overview

Below are some high-level guidelines that will outline the approaches we will use to ensure that our
codebase is highly testable and, as a byproduct, highly reliable.


### Coverage

All tests must obtain 100% *test* coverage; *not* 100% code coverage.  These terms are not
interchangeable.  For example, take the following method, which needs to be tested.  (All examples
are pseudocode.)


```
bool someMethod(bool a, bool b) {
    if (a && b) {
        return true;
    }
    return false;
}
```


We could write a test, like this:


```
void performTests() {
    expect(someMethod(true, true) == true);
    expect(someMethod(true, false) == false);
}
```


These two tests would pass and they would cover the logic in `someMethod` with 100% *code* coverage.
That is, all pieces of logic in `someMethod` were technically executed.  However, if, in the future, `someMethod` were changed to the following, the tests would still pass, even though the `someMethod`
method is now broken.


```
bool someMethod(bool a, bool b) {
    if (b) {
        return true;
    }
    return false;
}
```


This is because the tests did not cover all scenarios.  It did not test the effect of the first
argument, `a`.  If we change the test method to the following, instead, it would detect that the
method has been broken.


```
void performTests() {
    expect(someMethod(true, true) == true);
    expect(someMethod(true, false) == false);
    expect(someMethod(false, false) == false);
    expect(someMethod(false, true) == false);
}
```


If you test this against the original version of `someMethod`, it will succeed.  This test now has
100% *test* coverage against the `someMethod` method.

Some may argue that this method does *not* have 100% test coverage because we haven't tested for
exception handling or what would happen if the method were called repeatedly or whether it's thread
safe.  There will always be a scenario for which a test has not been written.  This is where
reasonability must be applied.  Ask yourself, have I tested that the logic has been exercised in the
way(s) that it was intended to be used?  If the answer is "yes" then you're likely at a reasonable
point in your testing.  For example, `someMethod` does not have any exception handling, so it would
likely be excessive to test for such (or, rather, the lack thereof).  One exception would be if the
method intentionally must not handle exceptions, in which case such a test would be warranted.  This
is where you must use your best judgment to make a reasonable and defensible decision on whether or
not to test.

As a side note, you'll notice that even with `someMethod` being small and straightforward, the amount
of code needed to test it still exceeded that of the original.

To facilitate unit testing of otherwise-private members, it may be necessary to make said members
more accessible than they normally would be.  Some testing frameworks have "visible for testing"
attributes to facilitate this, others do not.  The specific approach used will be addressed on a
framework-by-framework basis.


### Mocking/Faking

As mentioned above, one core tenant of unit testing is that the tests do not reach outside of their
own process.  For example, if a method being tested calls a database, said calls should not be
allowed to occur.  This can be accomplished by mocking, or removing the ability of "offending" logic
to operate as it usually would, and replacing the underlying functionality with something that an
automated test *can* control within its own process.  In doing this, dependencies are removed,
enabling both greater test reliability and portability.


### Componentization

It is likely that your usual coding style will need to be adapted to facilitate making the code you
write testable; typically, making it more componentized.  This is because everything you write must
be self contained.  That is, you cannot unintentionally intermingle dependencies in a given set of
logic without adversely affecting the complexity of tests you ultimately must write for it.

For example, consider the following.


```
bool isOkToSendNotifications() {
    var dbServerTime = getTimeFromDatabaseServer();
    if (dbServerTime.hour < 8) {
        return false;
    }
    if (dbServerTime.hour > 10) {
        return false;
    }
    return true;
}
```


Granted this is a bit contrived.  Notice that this method makes an external call to the database.
Regardless of our approach, this aspect will need to be handled with mocking, which is outside the
scope of this example.

If we were to write a test for this method, it might look something like this.


```
void performTests() {
    // Do something to mock db server time to return 7 ...
    expect(isOkToSendNotifications() == false);

    // Do something to mock db server time to return 8 ...
    expect(isOkToSendNotifications() == true);

    // Do something to mock db server time to return 10 ...
    expect(isOkToSendNotifications() == true);

    // Do something to mock db server time to return 11 ...
    expect(isOkToSendNotifications() == false);
}
```


For each call to `isOkToSendNotifications`, we must mock away the database call to return the time
we actually want to test.  Depending on what's required to mock away the external call, this could be
cumbersome and, in any case, clutters the intent of the test itself.  Remember, for our current tests,
we're *not* testing `getTimeFromDatabaseServer` (that would be tested separately), we're just having
to accommodate it to test `isOkToSendNotifications`.

Now consider if we changed our logic to look like this.


```
bool isOkToSendNotificationsAtHour(int hour) {
    if (hour < 8) {
        return false;
    }
    if (hour > 10) {
        return false;
    }
    return true;
}
```


We've removed the external call that gets the database server time, replacing it with a parameter
that provides the current hour.  Now, we can change our tests to look like this.


```
void performTests() {
    expect(isOkToSendNotifications(7) == false);
    expect(isOkToSendNotifications(8) == true);
    expect(isOkToSendNotifications(10) == true);
    expect(isOkToSendNotifications(11) == false);
}
```


We no longer need to accommodate `getTimeFromDatabaseServer`.  We can simply test the intended
method in complete isolation.
