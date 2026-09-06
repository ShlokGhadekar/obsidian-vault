### Basics
```java
String s = "hello";
char ch = s.charAt(1); //e
s.substring(start, end); //end is exclusive
s1.equals(s2) //never use ==
char[] arr = s.toCharArray();
String[] arr = s.split(" ");
s.split("\\s+"); // split by multiple spaces
"hello".indexOf('l'); // index of first occurance 2, -1 if not found

s1.compareTo(s2); //useful for lexicographical comparison
//0     → equal abc abc
//< 0   → s1 comes before s2 abc abd
//> 0   → s1 comes after s2 abd abc


```






### StringBuilder
```java
StringBuilder sb = new StringBuilder(); //initialise
sb.append("helo"); //adding
sb.insert(2,"l"); //inserting at position
sb.delete(5, 11); //end exclusive deletion
sb.deleteCharAt(1);
sb.setCharAt(2, "b"); //replaces character
char ch = sb.charAt(2);
int n = sb.length(); //length
sb.reverse() //reverse entire sb
String result = sb.toString();
String s = sb.substring(1, 4);
int index = sb.indexOf("abc");
sb.setLength(3); // hello -> hel
```

