## Zookeeper

### 树形数据结构

<img src="images/tree.jpeg" style="zoom:70%;" />

* 🌲型的数据结构组成

* Znode的引用方式为路径引用 （类似于文件管理系统中的文件路径）

  + /动物/小狗

* **Znode**

  <img src="https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy83OTg2NDEzLTcyYWY3ZTdlNDY1NWI4MmIucG5n?x-oss-process=image/format,png" style="zoom:67%;" />

  + ACL 这个节点的访问权限，即那些IP可以访问这个节点
  + 元信息： 版本号，时间戳，事务ID，大小等等；
  + child和data是子节点的引用和当前节点的数据
  + Znode 并不是用来存储大规模业务数据，而是用于存储**少量的状态和配置信息**，`每个节点的数据最大不能超过 1MB`。主要用于多读少写

* Znode 节点四种类型

  + 持久化节点（创建节点的客户端与zookeeper断掉后，该节点依然存在，持久化到当地硬盘）
    + 顺序节点
    + 普通
  + 临时节点（与client维护一个心跳，如果连接点宕机就会自动释放）
    + 顺序节点
    + 普通

* zookeeper中对节点的基本操作

  + 增删改查，设置数据，判断节点是否存在，获取所有的子节点；





### Watcher table

* watcher是客户端注册到Znode节点上的触发器   **所以这里还要维护一个key（节点路径）--value（clients）**

##### 设置watcher与触发

* `exists`，`getData`，`getChildren` 属于读操作。Zookeeper 客户端在请求读操作的时候，可以选择是否**读时设置 Watcher**
* **写时触发watcher**当这个 Znode 发生改变，也就是调用了 `create`，`delete`，`setData` 方法的时候，将会触发 Znode 上注册的对应事件，请求 Watch 的客户端会接收到异步通知。节点删除也会异步通知对应clients





### spring cloud 中eureka注册中心和zookeeper数据结构的对比

* 维护的数据结构的不同

  + zookeeper是一个树型 ， eureka是一个双层map 来维护

* 数据改变后异步通知的对象不同

  + zookeeper只向订阅这个节点的watchers进行通知

  + eureka向所有的client进行通知（刷新客户端当地的缓存）







### zookeeper集群一致性的维护

