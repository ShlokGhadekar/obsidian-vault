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

###