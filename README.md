# Unit Testing

## Learning Goals

- Explain unit testing
- Create unit tests in Java

## Introduction

Unit testing is a specific kind of testing that focused on validating the
functionality of a single component of the system you are building. It's also
focused on making sure that all possible permutations of the input (valid or
invalid) can be handled successfully, even that might sometimes mean throwing an
exception or returning an error message.

Even though unit testing alone cannot guarantee that a system will a) work
without errors and b) work as expected by the users of the system (more on that
distinction later), it is the foundational work that ensures that each
individual, small, specific piece of functionality works properly.

Overall, unit testing is focused on:

- Testing small, specific piece of functionality
- Cover all permutations of input parameters (valid and invalid)
- Ensure the output is always the same when the input is the same
- No side effects -> calling the same method with the same input multiple times
  in a row should produce the same output

## Unit Testing in Java

Let's consider a method that reverses the string it's given as a parameter:

```java
public class StringUtils {
    public String reserve(String toReverse) {
        return toReverse;
    }
}
```

This basic implementation does not actually reverse the string that is passed
in, so it currently does not work. Let's go ahead and write a testing for it
though - we will discuss the process of writing unit tests before the
corresponding functionality is actually implemented in a later section about
"Test Driven Development".

In IntelliJ, right click anywhere inside of your method and select the
"Generate" - you can also use the Cmd-N/Ctrl-N keyboard shortcut to bring up the
same menu:

![generate-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/generate-test.png)

When you select "Test" for the first time, IntelliJ will give you a dialog
window to let you set up unit testing in your project:

![set-up-unit-testing.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/set-up-unit-testing.png)

If IntelliJ tells you that it can't find the unit testing library, click on the
"Fix" button to bring up the next dialog box:

![fix-unit-testing.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/fix-unit-testing.png)

Select to download jUnit locally:

![download-junit.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/download-junit.png)

After IntelliJ downloads the jUnit library, it will get you back to the dialog
box to create your unit test:

![create-unit-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/create-unit-test.png)

Select to create the `setUp()` and `tearDown()` methods and select the method
you would like IntelliJ to create a unit test for:

![select-method-to-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/select-method-to-test.png)

You will now have a test class that looks like this:

```java
import static org.junit.jupiter.api.Assertions.*;

class StringUtilsTest {

    @org.junit.jupiter.api.BeforeEach
    void setUp() {
        stringUtils = new StringUtils();
    }

    @org.junit.jupiter.api.AfterEach
    void tearDown() {
    }

    @org.junit.jupiter.api.Test
    void reserve() {
    }
}
```

The `setUp()` method is a method that is called before every time each test is
run, and allows you to prepare objects that might be necessary for each one of
your tests. By getting these objects ready in this method, you don't have to do
it for every test method that you create. As we will see, you might end up with
many test methods for a single class or even a single method.

The `tearDown()` method is called after every time each test is run, and allows
you to clean things up after each test.

Let's go ahead and add logic to our test method:

```java
import static org.junit.jupiter.api.Assertions.*;

class StringUtilsTest {

    StringUtils stringUtils;

    @org.junit.jupiter.api.BeforeEach
    void setUp() {
        stringUtils = new StringUtils();
    }

    @org.junit.jupiter.api.AfterEach
    void tearDown() {
    }

    @org.junit.jupiter.api.Test
    void reserve() {
        String testString = "sample";
        String reversed = stringUtils.reverse(testString);
        assertEquals("elpmas", reversed);
    }
}
```

We've declared a global `stringUtils` variable, so we can initialize it in the
`setUp()` method and use it in our `reserve()` test method . In the `reserve()`
test method, we are create a `String` object we will use for testing and using a
jUnit method to test whether the return value is equal to what we expected:

```java
        assertEquals("elpmas", reversed);
```

`assertEquals()` is an "assertion" method, which is a kind of method that unit
test frameworks like jUnit provide in order for us to be able to test expected
values against actual values. When you need such a test, you should check the
jUnit documentation to see which assertion methods it supports for your use
case.

In this case, we want to see if the `String` object that was returned was indeed
the reverse of the `String` object we provided to the method we're trying to
test.

When we've tested our code before, we're used the "print out" method of testing,
meaning that we would run our program, see what output it produced and visually
check if that output matched our expectations. This presents two issues that
unit testing allows us to address:

1. We needed to manually run our program in order to determine if it still
   worked as we intended
2. We needed to manually check the program's output in order to make that
   determination

With unit testing, we address both issues:

1. Unit tests can be run automatically as part of your code lifecycle. The exact
   mechanisms to do this are out of scope for this module, but the ability to
   integrate unit testing into your code's flow to production is critical.
2. Thanks to assertions, the actual result of a unit test isn't subject to
   anyone looking at the code. The unit test framework itself (in this case,
   jUnit) can report pass/fail and the code pipeline can react accordingly.

Now that our unit test is set up properly, we can choose to run a single test:

![run-single-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/run-single-test.png)

Or you can choose to run all the tests in a specific class:

![run-all-tests.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/run-all-tests.png)

Either way, you will see the following error for our unit test, since we have
not actually implemented the reverse functionality:

![unit-test-error.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/unit-test-error.png)

This indicates that we expected to see the value `elpmas`, but instead saw the
value `sample`.

So now let's implement a silly version of the `reverse` method to show why unit
testing needs to be thorough:

```java
    public String reverse(String toReverse) {
        return "elpmas";
    }
```

This is obviously not a good implementation since it always returns the same
value, but since it returns exactly the value our unit test expects, our unit
test will now pass.

An easy to fix this is to make sure to add one more test to our existing method:

```java
    @org.junit.jupiter.api.Test
    void reserve() {
        String testString = "sample";
        String reversed = stringUtils.reverse(testString);
        assertEquals("elpmas", reversed);
        reversed = stringUtils.reverse(reversed);
        assertEquals("sample", reversed);
    }
```

Here we use the same string value again to make sure it can get reversed back to
the original.

Let's now come up with a better implementation of our `reverse` method, which is
based on reversing the order of a `byte` array that we create from the original
`String` object:

```java
    public String reverse(String toReverse) {
        byte[] stringBytes = toReverse.getBytes();
        byte[] reversedBytes = new byte[stringBytes.length];

        for (int i = 0; i < stringBytes.length; i++) {
            reversedBytes[stringBytes.length - 1 - i] = stringBytes[i];
        }
        return new String(reversedBytes);
    }
```

Run your unit test now and it should all pass.

It may seem like we're done, but we're not - what if we tried to reverse a
`String` object that hadn't been initialized, i.e. a `null` string? Our current
code would throw a `NullPointerException` because we don't check the input
string before we attempt to convert it to a `byte` array.

Instead of simply fixing that issue in our code, we are going to _first_ add a
testing method to test that specific case, validate that it currently fails, and
then fix it with code (more on this when we discuss Test Driven Development -
TDD):

```java
    @org.junit.jupiter.api.Test
    void reserveNull() {
        String testString = null;
        String reversed = stringUtils.reverse(testString);
        assertNull(reversed);
    }
```

Running our entire test suite now will tell us that our previous test still
passes, but that our new test does not pass:

![one-pass-one-fail.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/one-pass-one-fail.png)

Now let's implement the fix:

```java
    public String reverse(String toReverse) {
        if (toReverse == null) {
            return null;
        }

        byte[] stringBytes = toReverse.getBytes();
        byte[] reversedBytes = new byte[stringBytes.length];

        for (int i = 0; i < stringBytes.length; i++) {
            reversedBytes[stringBytes.length - 1 - i] = stringBytes[i];
        }
        return new String(reversedBytes);
    }
```

Now both tests pass.

This example highlights a very important aspect of unit testing: when
discovering new test cases to test, like we did with the case of reversing a
`null` string, we must _add_ to the existing test, not just replace them. That's
because our unit tests play a very important role in "regression" testing as
well.

Regression testing is the idea that as I make changes to my code, I must
continue to ensure that things that used to work are still going to work. For
example, consider the following change we could have to the `reserve()` method
in order to ensure that the new test case passed:

```java
    public String reverse(String toReverse) {
        return null;
    }
```

Sure, that's a silly example, but the fact is that it would allow the
`reverseNull()` test case to pass. So if that had been the only test case in our
test suite, we wouldn't have caught the fact that now our method only works when
the string being reversed is null.

In order to ensure that it works for _all_ known cases, we have to make sure
that our test suite continues to test all the cases, even the ones that we think
we already have covered.
