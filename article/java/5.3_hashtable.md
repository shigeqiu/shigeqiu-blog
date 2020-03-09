# Hashtable

<!-- TOC -->

- [Hashtable](#hashtable)
  - [扩容](#%e6%89%a9%e5%ae%b9)
  - [数组最大容量](#%e6%95%b0%e7%bb%84%e6%9c%80%e5%a4%a7%e5%ae%b9%e9%87%8f)
  - [存放的最大元素数量](#%e5%ad%98%e6%94%be%e7%9a%84%e6%9c%80%e5%a4%a7%e5%85%83%e7%b4%a0%e6%95%b0%e9%87%8f)

<!-- /TOC -->

## 扩容

``` java
protected void rehash() {
    int oldCapacity = table.length;
    Entry<?,?>[] oldMap = table;

    // //新容量=旧容量 * 2 + 1
    // overflow-conscious code
    int newCapacity = (oldCapacity << 1) + 1;
    if (newCapacity - MAX_ARRAY_SIZE > 0) {
        if (oldCapacity == MAX_ARRAY_SIZE)
            // Keep running with MAX_ARRAY_SIZE buckets
            return;
        newCapacity = MAX_ARRAY_SIZE;
    }
    Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];

    modCount++;
    threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
    table = newMap;

    for (int i = oldCapacity ; i-- > 0 ;) {
        for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null ; ) {
            Entry<K,V> e = old;
            old = old.next;

            int index = (e.hash & 0x7FFFFFFF) % newCapacity;
            e.next = (Entry<K,V>)newMap[index];
            newMap[index] = e;
        }
    }
}
```

## 数组最大容量

``` java
/**
* The maximum size of array to allocate.
* Some VMs reserve some header words in an array.
* Attempts to allocate larger arrays may result in
* OutOfMemoryError: Requested array size exceeds VM limit
*/
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

## 存放的最大元素数量

``` java
threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
```