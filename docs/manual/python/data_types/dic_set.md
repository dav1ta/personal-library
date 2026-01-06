dict vs set
what is hashable

An object is hashable if it has a hash value which never changes during its lifetime (it needs a hash() method), and can be compared to other objects (it needs an eq() method). Hashable objects which compare equal must have the same hash value. [...]

User-defined types are hashable by default because their hash code is their id() and the eq() method inherited from the object class simply compares the object ids. If an object implements a custom eq() which takes into account its internal state, it will be hashable only if its hash() always returns the same hash code. In practice, this requires that eq() and hash() only take into account instance attributes that never change during the life of the object.
missing keys with setdefault

d.get(k, default) my_dict.setdefault(key, []).append(new_value)
missing with missinng

o subclass dict or any other mapping type and add a missing method. Both solutions are covered next.

The missing method is only called by getitem (i.e., for the d[k] operator). The presence of a missing method has no effect on the behavior of other methods that look up keys, such as get or contains (which implements the in operator). This is why the default_factory of defaultdict works only with getitem, as noted in the warning at the end of the previous section.
subclass builtin

A better way to create a user-defined mapping type is to subclass collections.UserDict instead of dict (as we’ll do in Example 3-8). Here we subclass dict just to show that missing is supported by the built-in dict.getitem method
dict variations

    collections.OrderedDict
    collections.ChainMap
    ChainMap(locals(), globals(), vars(builtins))
    collections.Counter

custom mapping

    collections.UserDict
    typing.TypedDict The collections.UserDict class behaves like a dict, but it is slower because it is implemented in Python, not in C. We’ll cover it in more detail next

Set Theory

Set elements must be hashable. The set type is not hashable, so you can’t build a set with nested set instances. But frozenset is hashable, so you can have frozenset elements inside a set.

n CPython built for a 64-bit CPU, each bucket in a set has two fields: a 64-bit hash code, and a 64-bit pointer to the element value—which is a Python object stored elsewhere in memory. Because buckets have a fixed size, access to an individual bucket is done by offset. There is no field for the indexes from 0 to 7 The hash() built-in function works directly with built-in types and falls back to calling hash for user-defined types. If two objects compare equal, their hash codes must also be equal, otherwise the hash table algorithm does not work. For example, because 1 == 1.0 is True, hash(1) == hash(1.0) must also be True, even though the internal representation of an int and a float are very different. Also, to be effective as hash table indexes, hash codes should scatter around the index space as much as possible. This means that, ideally, objects that are similar but not equal should have hash codes that differ widely. Example 3-17 is the output of a script to compare the bit patterns of hash codes. Note how the hashes of 1 and 1.0 are the same, but those of 1.0001, 1.0002, and 1.0003 are very different.
salt value of hash

Starting with Python 3.3, a random salt value is included when computing hash codes for str, bytes, and datetime objects, as documented in Issue 13703—Hash collision security issue. The salt value is constant within a Python process but varies between interpreter runs. With PEP-456, Python 3.4 adopted the SipHash cryptographic function to compute hash codes for str and bytes objects. The random salt and SipHash are security measures to prevent DoS attacks. Details are in a note in the documentation for the hash special method.
hasing in python

As mentioned earlier, the hash table for a set starts with 8 empty buckets. As elements are added, Python makes sure at least ⅓ of the buckets are empty—doubling the size of the hash table when more space is needed. The hash code field of each bucket is initialized with -1, which means “no hash code”

iven the literal {'Mon', 'Tue', 'Wed', 'Thu', 'Fri'}, Python gets the hash code for the first element, 'Mon'. For example, here is a realistic hash code for 'Mon'—you’ll probably get a different result because of the random salt Python uses to compute the hash code of string

Python takes the modulus of the hash code with the table size to find a hash table index. Here the table size is 8, and the modulus is 3:

Probing consists of computing the index from the hash, then looking at the corresponding bucket in the hash table. In this case, Python looks at the bucket at offset 3 and finds -1 in the hash code field, marking an empty bucke

Python stores the hash code of the new element, 4199492796428269555, in the hash code field at offset 3, and a pointer to the string object 'Mon' in the element field. Figure 3-5 shows the current state of the hash table

For the second element, 'Tue', steps 1, 2, 3 above are repeated. The hash code for 'Tue' is 2414279730484651250, and the resulting index is 2.

When adding 'Wed' to the set, Python computes the hash -5145319347887138165 and index 3. Python probes bucket 3 and sees that it is already taken. But the hash code stored there, 4199492796428269555 is different. As discussed in “Hashes and equality”, if two objects have different hashes, then their value is also different. This is an index collision. Python then probes the next bucket and finds it empty. So 'Wed' ends up at index 4, as shown in Figure 3-7.

Adding the next element, 'Thu', is boring: there’s no collision, and it lands in its natural bucket, at index 7. Placing 'Fri' is more interesting. Its hash, 7021641685991143771 implies index 3, which is taken by 'Mon'. Probing the next bucket—4— Python finds the hash for 'Wed' stored there. The hash codes don’t match, so this is another index collision. Python probes the next bucket. It’s empty, so 'Fri' ends up at index 5. The end state of the hash table is shown in Figure 3-8.

ahsh table for the set {'Mon', 'Tue', 'Wed', 'Thu', 'Fri'}. It is now 62.5% full—close to the ⅔ threshold.

Next: [List & Tuple](list_tuple.md)
