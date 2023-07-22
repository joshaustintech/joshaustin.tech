---
title: "Those sneaky NullPointerExceptions!"
date: 2019-06-18T00:00:00-05:00
tags: ["java", "junit", "spring", "beginner"]
summary: "Defensive drivers anticipate accidents and bad drivers. This lowers the chances of getting into accidents."
cover:
    image: "images/undraw_electric_car_b7hl.png"
    alt: "drawing of a Tesla car stopped to let a man walk by and admire the car"
---

There is a form of driving called "defensive driving". Defensive drivers anticipate accidents and bad drivers. This lowers the chances of getting into accidents. It has saved me from several bad accidents.

Similarly, we can avoid the scary `NullPointerException` in Java if we know how to anticipate it.

For example, what is wrong with this?

```java
if (aString.equals("yes")) { /* do stuff */ }
```

It's assuming that `aString` has been initialized. If any string is not initialized, it will be `null`! Calling `.equals()` on a `null` object is not going to end well. This happens more often than you might expect. It is better to write this:

```java
if ("yes".equals(aString)) { /* do stuff */ } 
```

I edited a POJO recently that was meant to represent a deserialized JSON object. The business requirements said I needed to remove any leading or trailing whitespace. Thankfully, the `String.trim()` method can do this for me in a single line! I placed it in the setter like this:

```java
public class Person {
  private String firstName;

  public String getFirstName() { return this.firstName; }

  public void setFirstName(String firstName) {
    this.firstName = firstName.trim();
  }
}
```

Seems innocent enough. 😉

Imagine my frustration when converting a JSON to this object was throwing a `com.fasterxml.jackson.databind.JsonMappingException` with a not-so-helpful explanation of "N/A". What was happening???

Apparently, if `firstName` is null in JSON, `setFirstName(firstName)` was calling `trim()` on a `null` object!

What I need to do is check to make sure that the String parameter in `setFirstName(firstName)` is not null. There's more than one way to check, but I chose a ternary operator:

```java
public class Person {
  private String firstName;

  public String getFirstName() { return this.firstName; }

  public void setFirstName(String firstName) {
    this.firstName = (firstName == null) ? null : firstName.trim();
  }
}
```

How about you? Have you run into the dreaded `NullPointerException` recently? How did you fix it?