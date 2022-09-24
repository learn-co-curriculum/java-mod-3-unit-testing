# Unit Testing

## Learning Goals

- Introduce the concept of testing.
- Explain unit testing.
- Create unit tests in Java.

## Introduction to Testing

Testing is a fundamental step in software development. We want to test the code
we write before deploying it and letting users interact with it. Through
testing, we can aim to eliminate **bugs** (or issues within the code) and ensure
edge cases and exceptions are handled in an appropriate manner.

So far, we have been testing manually through the use of print statements. For
example, in our labs, we compile our code and then run it by providing the
program some input. We then wait to see what the program prints out and how the
input was handled. If the output was not what we were expecting, we go back and
try to address the issue. We can also now use the debugger tool to help us track
down any bugs due to unexpected behavior.

This presents two issues:

1. We needed to manually run our program in order to determine if it still
   worked as we intended.
2. We needed to manually check the program's output in order to make that
   determination.

But how else could we test our code then?

## Unit Testing in Java

**Unit testing** is a specific kind of testing that focuses on validating the
functionality of a single component of the system. It's also focused on making
sure that all possible permutations of the input (valid or invalid) can be
handled successfully, even though that might sometimes mean throwing an
exception or returning an error message.

Even though unit testing alone cannot guarantee that a system will work
without errors or work as expected by the users of the system (more on that
distinction later), it is the foundational work that ensures that each
individual, small, specific piece of functionality works properly.

Overall, unit testing focuses on:

- Testing a small, specific piece of functionality.
- Covering all permutations of input parameters (valid and invalid).
- Is deterministic, as in, calling the same method with the same input multiple
  times in a row should produce the same output.

Unit testing will also address the issues that are found in manual testing
that we described above with the print statements:

1. Unit tests can be run automatically as part of the code lifecycle. The exact
   mechanisms to do this are out of scope for this module, but the ability to
   integrate unit testing into the code's flow to production is critical.
2. The actual result of a unit test isn't subject to anyone looking at the code.
   The unit test framework itself can report pass/fail and the code pipeline
   can react accordingly.

To help us create unit tests, we will be making use of an open-source framework
called **JUnit**. JUnit is a unit testing framework for Java which is used for
writing and running tests. It comes with annotations to identity testing
methods, assertions to test expected results, and test runners to quickly run
individual tests.

Let's consider a method that reverses a `String`:

```java
public class StringUtils {
    public String reverse(String toReverse) {
        return toReverse;
    }
}
```

This basic implementation does not actually reverse the `String` that is passed
in, so it currently does not work. Let's go ahead and write a test for it
though - we will discuss the process of writing unit tests before the
corresponding functionality is actually implemented in a later section about
"Test Driven Development".

## Creating Unit Tests in IntelliJ

In IntelliJ, right click anywhere inside the method and select the "Generate" -
we can also use the Cmd-N/Ctrl-N keyboard shortcut to bring up the
same menu:

![generate-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/generate-test.png)

From there, go ahead and select "Test". The first time you do this, IntelliJ
will give you a dialog window to let you set up unit testing in your project:

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

We now have a test class that looks like this:

```java
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class StringUtilsTest {
    
    StringUtils stringUtils;

    @BeforeEach
    void setUp() {
        stringUtils = new StringUtils();
    }

    @AfterEach
    void tearDown() {
    }

    @Test
    void reverse() {
    }
}
```

## Writing the Unit Test

Sometimes when we are writing multiple tests, we find that we have to do
a little prep work before we can even test the method or functionality. An
example could be defining the objects by calling their constructors. This is
where we could condense that set-up work by creating a `setUp()` method. To
force the `setUp()` method to run before each test, we have to add the
annotation, `@BeforeEach`, right before we define the method. Now the `setUp()`
method will be called every time before each test runs. In the above test class
we created, we can see that we have our `setUp()` method defined as we described
along with the construction of a `StringUtils` object. We are assuming each test
in this class may need a `StringUtils` object to be instantiated prior to
testing.

Similarly, we may also have some clean-up work to do after testing. If we find
we have to perform the same clean-up steps after every test, then we can also
take those lines of code and move it to a `tearDown()` method. To force the
`tearDown()` method to run after each test, we have to add the annotation,
`@AfterEach`, right before we define the method. Now the `tearDown()` method
will be called after every time a test runs.

The last annotation we see in our `StringUtilsTest` class is the `@Test`
annotation. This annotation signals that the following method is a _test_
method. This will tell the JUnit framework to treat this as a test case and
run as a unit test.

Let's go ahead and add logic to our test method:

```java
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class StringUtilsTest {

    StringUtils stringUtils;

    @BeforeEach
    void setUp() {
        stringUtils = new StringUtils();
    }

    @AfterEach
    void tearDown() {
    }

    @Test
    void reverse() {
        String testString = "sample";
        String reversed = stringUtils.reverse(testString);
        assertEquals("elpmas", reversed);
    }
}
```

In the test `reverse()` method, we create a `String` object that we will use
for testing. We can check to see if the `StringUtils::reverse()` method returns a `String`
value that we expect by using an **assertion**.

```java
assertEquals("elpmas", reversed);
```

`assertEquals()` is an assertion method, which is a kind of method that unit
test frameworks, like JUnit, provide in order for us to be able to test expected
values against actual values. When we need such a test, we should check the
[JUnit documentation](https://junit.org/junit5/docs/5.7.2/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html)
to see which assertion methods it supports for the
particular use case.

In this case, we want to see if the `String` object that was returned was indeed
the reverse of the `String` object we provided to the method we're trying to
test.

## Running the Unit Tests

Now that our unit test is set up properly, we can choose to run a single test by
pressing the play button next to the test `reverse()` method:

![run-single-test.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-tests/reverse-run-test.png)

Or we can choose to run all the tests in a specific class by clicking the
double play button next to the `StringUtilsTest` class definition. Note that in
our current example, we only have one test defined. If we had multiple tests
defined in this class though, it would run all of them by running the
`StringUtilsTest`:

![run-all-tests.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-tests/stringutils-run-tests.png)

Either way, we will see the following error for our unit test, since we have
not actually implemented the reverse functionality:

![unit-test-error.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-testing/unit-test-error.png)

This indicates that we expected to see the value `elpmas`, but instead saw the
value `sample`.

## Capturing More Test Cases

So now let's implement a silly version of the `reverse()` method to show why
unit testing needs to be thorough:

```java
    public String reverse(String toReverse) {
        return "elpmas";
    }
```

This is obviously not a good implementation since it always returns the same
value, but since it returns exactly the value our unit test expects, our unit
test will now pass.

An easy to fix this is to make sure we add one more test to our existing method:

```java
    @Test
    void reserve() {
        String testString = "sample";
        String reversed = stringUtils.reverse(testString);
        assertEquals("elpmas", reversed);
        reversed = stringUtils.reverse(reversed);
        assertEquals("sample", reversed);
    }
```

Here, we use the same string value again to make sure it can get reversed back
to the original.

Let's now come up with a better implementation of our `reverse` method, which is
based on reversing the order of a `byte` array that we create from the original
`String` object:

```java
public class StringUtils {
   public String reverse(String toReverse) {
      char[] characters = toReverse.toCharArray();
      char[] reversedCharacters = new char[characters.length];

      for (int index = 0; index < characters.length; index++) {
         reversedCharacters[characters.length - 1 - index] = characters[index];
      }

      return new String(reversedCharacters);
   }
}
```

Run the unit test now. It should all pass!

It may seem like we're done, but we're not - what if we tried to reverse a
`String` object that hadn't been initialized, i.e. a `null` string? Our current
code would throw a `NullPointerException` because we don't check the input
string before we attempt to convert it to a `char` array.

Instead of simply fixing that issue in our code, we are going to _first_ add a
testing method to test that specific case, validate that it currently fails, and
then fix it with code (more on this later):

```java
    @Test
    void reverseNull() {
        String testString = null;
        String reversed = stringUtils.reverse(testString);
        assertNull(reversed);
    }
```

Running our entire test class now will tell us that our previous test still
passes, but that our new test does not pass:

![one-pass-one-fail.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/unit-tests/one-pass-one-fail.png)

Now let's implement the fix:

```java
public class StringUtils {
   public String reverse(String toReverse) {
      if (toReverse == null) {
         return null;
      }

      char[] characters = toReverse.toCharArray();
      char[] reversedCharacters = new char[characters.length];

      for (int index = 0; index < characters.length; index++) {
         reversedCharacters[characters.length - 1 - index] = characters[index];
      }

      return new String(reversedCharacters);
   }
}
```

Now both tests pass.

This example highlights a very important aspect of unit testing: when
discovering new test cases to test, like we did with the case of reversing a
`String` with a `null` value, we must _add_ to the existing test, not just
replace them. That's because our unit tests play a very significant role in
another form of testing called **regression testing** as well.

Regression testing is the idea that as we make changes to the code, we must
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
collection of tests, or **test suite**, we wouldn't have caught the fact that
now our method only works when the string being reversed is `null`.

In order to ensure that it works for _all_ known cases, we have to make sure
that our test suite continues to test all the cases, even the ones that we think
we already have covered.

## References

[JUnit documentation](https://junit.org/junit5/docs/5.7.2/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html)