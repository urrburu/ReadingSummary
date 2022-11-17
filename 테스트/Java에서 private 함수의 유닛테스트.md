# Unit Test Private Methods in Java

Last modified: October 26, 2022

We’re finally running a Black Friday launch. All Courses are **30% off** until **December 2nd:**

## 1. Overview[](https://www.baeldung.com/java-unit-test-private-methods#overview)

In this tutorial, we'll briefly explain why testing private methods directly is generally not a good idea. Then we'll demonstrate how to test private methods in Java if it's necessary.

## 2. Why We Shouldn't Test Private Methods[](https://www.baeldung.com/java-unit-test-private-methods#why-we-shouldnt-test-private-methods)

**As a rule, the unit tests we write should only check our public methods contracts.** Private methods are implementation details that the callers of our public methods aren't aware of. Furthermore, changing our implementation details shouldn't lead us to change our tests.

Generally speaking, urging to test a private method highlights one of the following problems:

-   We have dead code in our private method.
-   Our private method is too complex and should belong to another class.
-   Our method wasn't meant to be private in the first place.

Hence, when we feel like we need to test a private method, what we should really do is fix the underlying design problem instead.

## 3. An Example: Remove Dead Code From a Private Method[](https://www.baeldung.com/java-unit-test-private-methods#an-example-remove-dead-code-from-a-private-method)

Let's showcase a quick example of that.

We're going to write a private method that will return the double of an _Integer_. For _null_ values, we want to return _null_:

```java
private static Integer doubleInteger(Integer input) {
    if (input == null) {
        return null;
    }
    return 2 * input;
}
```

Now, let's write our public method. It'll be the only entry point from outside the class.

This method receives an _Integer_ as an input. It validates that this _Integer_ isn't _null;_ otherwise, it throws an [_IllegalArgumentException_](https://www.baeldung.com/java-illegalargumentexception-or-nullpointerexception). After that, it calls the private method to return twice the _Integer_‘s value:

```java
public static Integer validateAndDouble(Integer input) {
    if (input == null) {
        throw new IllegalArgumentException("input should not be null");
    }
    return doubleInteger(input);
}
```

Let's follow our good practice and test our public method contract.

First, let's write a test that ensures that an [_IllegalArgumentException_](https://www.baeldung.com/java-illegalargumentexception-or-nullpointerexception) is thrown if the input is _null_:

```java
@Test
void givenNull_WhenValidateAndDouble_ThenThrows() {
    assertThrows(IllegalArgumentException.class, () -> validateAndDouble(null));
}
```

Now let's check that a non-null _Integer_ is correctly doubled:

```java
@Test
void givenANonNullInteger_WhenValidateAndDouble_ThenDoublesIt() {
    assertEquals(4, validateAndDouble(2));
}
```

Let's have a look at the [coverage reported by the JaCoCo plugin](https://www.baeldung.com/jacoco):

[![Code coverage of our methods](https://www.baeldung.com/wp-content/uploads/2022/06/public-and-private-method-code-coverage.png)](https://www.baeldung.com/wp-content/uploads/2022/06/public-and-private-method-code-coverage.png)As we can see, the null check inside our private method isn't covered by our unit tests. Should we test it then?

The answer is no. It's important to understand that our private method doesn't exist in a vacuum. It'll only be called after the data is validated in our public method. **Thus, the null check in our private method will never be reached; it's dead code and should be removed.**

## 4. How to Test Private Methods in Java[](https://www.baeldung.com/java-unit-test-private-methods#how-to-test-private-methods-in-java)

Assuming we're not discouraged, let's explain how to test our private method concretely.

To test it, **it would be helpful if our private method had another visibility**. The good news is that **we'll be able to simulate that with [reflection](https://www.baeldung.com/java-reflection)**.

Our encapsulating class is called _Utils_. The idea is to access the private method called _doubleInteger,_ which accepts an _Integer_ as a parameter. Then we'll modify its visibility to be accessible from outside the _Utils_ class. Let's see how we can do that:

```java
private Method getDoubleIntegerMethod() throws NoSuchMethodException {
    Method method = Utils.class.getDeclaredMethod("doubleInteger", Integer.class);
    method.setAccessible(true);
    return method;
}
```

Now we're able to use this method. Let's write a test to ensure that, given a _null_ object, our private method returns _null_. We'll need to apply the method to a parameter that will be _null_:

```java
@Test
void givenNull_WhenDoubleInteger_ThenNull() throws InvocationTargetException, IllegalAccessException, NoSuchMethodException {
    assertEquals(null, getDoubleIntegerMethod().invoke(null, new Integer[] { null }));
}
```

Let's explain a bit more about the usage of the [_invoke_](https://www.baeldung.com/java-method-reflection) method. The first argument is the object on which we apply the method. As _doubleInteger_ is static, we passed in a _null_. The second argument is an array of parameters. In this case, we had only one parameter, and it was _null_.

Finally, let's demonstrate how we can also test the case of a non-null input:

```java
@Test
void givenANonNullInteger_WhenDoubleInteger_ThenDoubleIt() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
    assertEquals(74, getDoubleIntegerMethod().invoke(null, 37));
}
```

## 5. Conclusion[](https://www.baeldung.com/java-unit-test-private-methods#conclusion)

In this article, we learned why testing private methods is generally not a good idea. Then we demonstrated how to use reflection to test a private method in Java.

As always, the code is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-reflection-2).