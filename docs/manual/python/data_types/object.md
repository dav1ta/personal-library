Variables Are Not Boxes

In 1997, I took a summer course on Java at MIT. The professor, Lynn Andrea Stein—an award-winning computer science educator who currently teaches at Olin College of Engineering—made the point that the usual “variables as boxes” metaphor actually hinders the understanding of reference variables in OO languages. Python variables are like reference variables in Java, so it’s better to think of them as labels attached to objects
sentiel obj

END_OF_DATA = object()
# ... many lines
def traverse(...):
# ... more lines
  if node is END_OF_DATA:
    raise StopIteration
# etc.

Copies Are Shallow by Default
Function Parameters as References

In CPython, the primary algorithm for garbage collection is reference counting. Essentially, each object keeps count of how many references point to it. As soon as that refcount reaches zero, the object is immediately destroyed: CPython calls the del method on the object (if defined) and then frees the memory allocated to the object. In CPython 2.0, a generational garbage collection algorithm was added to detect groups of objects involved in reference cycles—which may be unreachable even with outstanding references to them, when all the mutual references are contained within the group. Other implementations of Python have more sophisticated garbage collectors that do not rely on reference counting, which means the del method may not be called immediately when there are no more references to the object. See “PyPy, Garbage Collection,

wref = weakref.ref(a_set)

weakref.finalize to register a callback function to be called when an object is destroyed
The WeakValueDictionary Skit

The class WeakValueDictionary implements a mutable mapping where the values are weak references to objects. When a referred object is garbage collected elsewhere in the program, the corresponding key is automatically removed from WeakValueDictionary. This is commonly used for caching. Our demonstration of a WeakValueDictionary is inspired by the classic Cheese Shop skit by Monty Python, in which a customer asks for more than 40 kinds of cheese, including cheddar and mozzarella, but none are in stock.

import weakref

            stock = weakref.WeakValueDictionary() catalog = [Cheese('Red Leicester'), Cheese('Tilsit'), ... Cheese('Brie'), Cheese('Parmesan')] ... for cheese in catalog: ... stock[cheese.kind] = cheese ... sorted(stock.keys()) ['Brie', 'Parmesan', 'Red Leicester', 'Tilsit'] del catalog sorted(stock.keys()) ['Parmesan'] del cheese sorted(stock.keys())

A temporary variable may cause an object to last longer than expected by holding a reference to it. This is usually not a problem with local variables: they are destroyed when the function returns. But in Example 6-19, the for loop variable cheese is a global variable and will never go away unless explicitly deleted

Not every Python object may be the target, or referent, of a weak reference. Basic list and dict instances may not be referents, but a plain subclass of either can solve this problem easily

class MyList(list): """list subclass whose instances may be weakly referenced""" a_list = MyList(range(10))
a_list can be the target of a weak reference

wref_to_a_list = weakref.ref(a_list)

I was surprised to learn that, for a tuple t, t[:] does not make a copy, but returns a reference to the same object

Next: [Overview](../functions/index.md)
