### Collection
> Collection has two sub interface: List and Set. They are acting differently on functionality.
### List
- List is ordered, and can contain duplicated objects, Can be sorted by calling Arrays.sort(<List>), List.contains()
calls overriden equals method to compare objects.
### HashSet, HashTable, HashMap
- Set is non-ordered, and cannot contain duplicated objects. Set.contains calls overriden equals and hashCode methods to
compare objects. 
- hashCode is called when adding object to the HashSet, it generates key for the value, hashCode will affect the
  performance of the HashSet. 
- equal objects must have same hashCode, but same hashCode cannot guarantee equal objects.
## String
### convert a string with zone info to a LocalDateTime
There is a string represented date `2022-07-25T00:00:00.000Z`, to convert this string into the
`java.time.LocalDateTime`, `Z` in the end means time zone info, it stands for `UTC`. This code can convert it to the
date. A `DateTimeFormatter` is needed to specify the format of the date time.
```
String timeString = "2022-07-25T00:00:00.000Z";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ss.SSSz");
ZonedDateTime zonedDateTime = ZonedDateTime.parse(timeString, formatter);
LocalDateTime localDateTime = zonedDateTime.toLocalDateTime();
```
### Import a trust ca to java keystore
To import a root ca to java keystore, the first thing is to locate the keystore used by the jre. It should be
`$JAVA_HOME/lib/security/cacerts`.
With the ca cert, use keytool to import it 
```
keytool -import -trustcacerts -alias <alias_name> -file <file_name> -keystore $JAVA_HOME/lib/security/cacerts -storepass
changeit -noprompt
```
Once it's successful, run this command to verify the ca imported
```
keytool -list -v -keystore $JAVA_HOME/lib/security/cacerts
```

