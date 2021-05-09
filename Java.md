### Collection
> Collection has two sub interface: List and Set. They are acting differently on functionality.
#### List
- List is ordered, and can contain duplicated objects, Can be sorted by calling Arrays.sort(<List>), List.contains()
calls overriden equals method to compare objects.
#### HashSet, HashTable, HashMap
- Set is non-ordered, and cannot contain duplicated objects. Set.contains calls overriden equals and hashCode methods to
compare objects. 
- hashCode is called when adding object to the HashSet, it generates key for the value, hashCode will affect the
  performance of the HashSet. 
- equal objects must have same hashCode, but same hashCode cannot guarantee equal objects.

