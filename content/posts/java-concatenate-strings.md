---
title: "How to Concatenate Strings in Java like a Pro"
date: 2023-02-18T01:00:00-05:00
tags: ["java", "intermediate", "string", "stringbuilder"]
summary: "String concatenation in Java can be initially tricky, especially if you come from other programming languages."
---

String concatenation in Java can be initially tricky, especially if you come from other programming languages. When Java was first implemented, the decision was made to render the `String` class immutable. So what if you have to concatenate two or more strings? I remember being instructed to rely on the `StringBuilder` class like so:

```java
StringBuilder sb = new StringBuilder();
sb.append("Gandalf");
sb.append(" ");
sb.append("The");
sb.append(" ");
sb.append("Grey");

String gandalf = sb.toString();
```

Prior to Java 8, this was more efficient. However, now the compiler handles that for you by making `+` operations into `StringBuilder` operations at compile time.

However, this implementation is very naïve.

If this code...

```java
String firstName = "Frodo";
String lastName = "Baggins";
String fullName = firstName + " " + lastName;
```

...translates to this equivalent at compile time...

```java
String firstName = "Frodo";
String lastName = "Baggins";
StringBuilder sb = new StringBuilder();
sb.append(firstName);
sb.append(" ");
sb.append(lastName);
String fullName = sb.toString();
```

...what happens during a loop?

Unfortunately, this code...

```java
List<String> hobbits = List.of("Frodo", "Samwise", "Merry", "Pippin");
String greetAllHobbits = "";
for (String hobbit : hobbits) {
  greetAllHobbits += "Greetings, " + hobbit + "!\n";
}
```

...translates to roughly this equivalent at compile time:
```java
List<String> hobbits = List.of("Frodo", "Samwise", "Merry", "Pippin");
String greetAllHobbits = "";
for (String hobbit : hobbits) {
  StringBuilder sb = new StringBuilder(greetAllHobbits);
  sb.append("Greetings, ");
  sb.append(hobbit);
  sb.append("!\n");
  greetAllHobbits = sb.toString();
}
```

A separate `StringBuilder` instance is created during each iteration, which can defeat the purpose of being efficient!

In the above case, you'll need to create a `StringBuilder` outside the loop so you aren't creating an unnecessary new object for each iteration.

```java
List<String> hobbits = List.of("Frodo", "Samwise", "Merry", "Pippin");
StringBuilder sb = new StringBuilder();
for (String hobbit : hobbits) {
  sb.append("Greetings, ");
  sb.append(hobbit);
  sb.append("!\n");
}
String greetAllHobbits = sb.toString();
```

In conclusion:
- Use `+` when concatenating ad hoc
- Use `StringBuilder` when concatenating with a loop