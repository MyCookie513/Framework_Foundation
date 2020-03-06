### 不能实现lazy加载

* 作为一个静态属性在类中,任何进行类的加载的方式，都会引起创建一个对象

  ```java
   private final static SingletonObject1 instance=new SingletonObject1();
  
      static {
          System.out.println("singleton 类的初始化");
      }
      public  static SingletonObject1 getInstance(){
          System.out.println("加载返回实例"
                  );
          return  instance;
      }
  ```

  

### 能够进行lazy加载

* 方式一 ： dubble check 

  ```java
      private volatile SingletonObject3 instance;  
      private SingletonObject3 getInstance(){
          if(instance==null){
              //避免高并发产生多个实例
              synchronized (SingletonObject3.class){
                  if(instance==null){
                      instance= new SingletonObject3();
                  }
              }
  
          }
          return instance; //实例要标注volatile，这样才能避免重排序而引起的问题
      }
  ```

* 方式二 ： 内部静态类

  ```java
      private  SingletonObject6 instance;
      private static  class InstanceHolder{
         private static final  SingletonObject6 instance =new SingletonObject6();
       
      }
      public static  SingletonObject6 getInstance(){
          return InstanceHolder.instance;
      }
  ```

* 方式三 ： 内部枚举类的调用

  ```java
      private enum Singleton{
          INSTANCE;
          private final SingletonObject8 instance;
          Singleton(){
              instance=new SingletonObject8();
          }
          public SingletonObject8 getInstance(){
              return instance;
          }
      }
      public static SingletonObject8 getInstance(){
          return  Singleton.INSTANCE.getInstance();
      }
  ```





### 应用场景

* 主要用于资源共享的情况下，避免由于资源操作时导致的性能或损耗等。、
  1. 控制整个应用程序的配置文件
  2. 日志打印
  3. 数据库连接池
  4. web中的计数器
* 需要一个人对所有的资源进行控制管理
  1. windows的任务管理器
  2. 多线程中的线程池主线程





















