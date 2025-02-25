# Write

Writing to a box is almost like writing to a map. All keys have to be ASCII Strings with a max lenght of 255 chars or unsinged 32 bit integers.

```dart
var box = Hive.box('myBox');

box.put('name', 'Paul');

box.put('friends', ['Dave', 'Simon', 'Lisa']);

box.put(123, 'test');

box.putAll({'key1': 'value1', 42: 'life'})
```

You may wonder why writing works without async code. This is one of the main strengths of Hive.  
The changes are written to the disk as soon as possible in the background but all listeners are notified immediately. If the async operation fails \(which it should not\), all listeners are notified again with the old values.  
If you want to make sure that a write operation is successful just await its `Future`.

This does not work for [lazy boxes](../advanced/lazy_box.md): As long as the `Future`returned by `put()` did not finish, `get()` will return the old values \(or `null` if it doen't exist\).

The following code shows the difference:

```dart
var box = await Hive.openBox('box');

box.put('key', 'value');
print(box.get('key')); // value


var lazyBox = await Hive.openBox('lazyBox');

lazyBox.put('key', 'value');
print(lazyBox.get('key')); // null

await lazyBox.put('key2', 'value2');
print(lazyBox.get('key2')); // value2
```

{% hint style="info" %}
Writing `null` is **NOT** the same as [deleting](delete.md) a value.
{% endhint %}

