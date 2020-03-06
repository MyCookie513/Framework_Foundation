### 1.ThreadLocal

* 使用效果； 使用定一个ThreadLocal值就可以在不同的线程中存入不同的值，取得时候根据每个线程的不同而不同； 

* java线程中的私有保险箱 ，设置同一个变量，a ， 但在不同线程get到不同的值，底层的实现是每个线程内部都有一个小型的map ，存储着自己保险中的 a的值；

```java
  ThreadLocal<Long> longLocal = new ThreadLocal<Long>();  
  ThreadLocal<String> stringLocal = new ThreadLocal<String>();  

```

* 内部值的使用 

```java
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
```

* 存入新值

```java
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }

```

* 总结 ： 每个线程都有自己的一个map ， ThreadLocal变量在真正进行调用的时候要根据调用她的线程到保险箱中去查询代表的值， 