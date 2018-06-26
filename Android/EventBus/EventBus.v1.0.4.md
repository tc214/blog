###   事件总线-AndroidEventBus  
   
####   概述   
 EventBus是一个用于简化Andorid、Fragment、Threads、Service之间信息传递的一个发布/订阅事件集。  
 
 
 
####   使用方式   
1.注册事件 注册EventBus，EventBus会获取当前类中的订阅方法，包括方法的参数，类型，优先级等信息。在获取的方法中，会先遍历缓存。如果在缓存中没有才去调用获取方法。
e:  
EventBus.getDefault().register(this);    
EventBus.getDefault()是获取单例对象，然后注册
```java  
    private EventBus() {  // 私有构造函数
        this(DESCRIPTOR);
    }

    public EventBus(String desc) {
        this.mDesc = DESCRIPTOR;
        this.mSubcriberMap = new ConcurrentHashMap();
        this.mStickyEvents = Collections.synchronizedList(new LinkedList());
        this.mLocalEvents = new ThreadLocal<Queue<EventType>>() {
            protected Queue<EventType> initialValue() {
                return new ConcurrentLinkedQueue();
            }
        };
        this.mDispatcher = new EventBus.EventDispatcher((EventBus.EventDispatcher)null);
        this.mMethodHunter = new SubsciberMethodHunter(this.mSubcriberMap);
        this.mDesc = desc;
    }
    // 获取单例对象
    public static EventBus getDefault() {
        if (sDefaultBus == null) {
            Class var0 = EventBus.class;
            synchronized(EventBus.class) {
                if (sDefaultBus == null) {
                    sDefaultBus = new EventBus();
                }
            }
        }

        return sDefaultBus;
    }
	
      //注册事件
public void register(Object subscriber) {
        if (subscriber != null) {
            synchronized(this) {
                this.mMethodHunter.findSubcribeMethods(subscriber);
            }
        }
    }  
     // 寻找订阅方法
    public void findSubcribeMethods(Object subscriber) {
        if (this.mSubcriberMap == null) {
            throw new NullPointerException("the mSubcriberMap is null. ");
        } else {
            for(Class clazz = subscriber.getClass(); clazz != null && !this.isSystemCalss(clazz.getName()); clazz = clazz.getSuperclass()) {
			    // 拿到clazz所有方法（不包括继承的方法）
                Method[] allMethods = clazz.getDeclaredMethods();

                for(int i = 0; i < allMethods.length; ++i) {
                    Method method = allMethods[i];
                    Subscriber annotation = (Subscriber)method.getAnnotation(Subscriber.class);
                    if (annotation != null) {
                        Class[] paramsTypeClass = method.getParameterTypes();
                        if (paramsTypeClass != null && paramsTypeClass.length == 1) {
                            Class<?> paramType = this.convertType(paramsTypeClass[0]);
                            EventType eventType = new EventType(paramType, annotation.tag());
                            TargetMethod subscribeMethod = new TargetMethod(method, eventType, annotation.mode());
                            this.subscibe(eventType, subscribeMethod, subscriber);
                        }
                    }
                }
            }

        }
    }
    // 保存订阅者，为每一个事件类型创建一个list，来保存该事件类型的所有订阅者
    private void subscibe(EventType event, TargetMethod method, Object subscriber) {
        CopyOnWriteArrayList<Subscription> subscriptionLists = (CopyOnWriteArrayList)this.mSubcriberMap.get(event);
        if (subscriptionLists == null) {
            subscriptionLists = new CopyOnWriteArrayList();
        }

        Subscription newSubscription = new Subscription(subscriber, method);
        if (!subscriptionLists.contains(newSubscription)) {
            subscriptionLists.add(newSubscription);
            this.mSubcriberMap.put(event, subscriptionLists);
        }
    }	
```   
	
2. 使用注解来获取订阅方法 


3.处理消息  
```java  

 @Subscriber(tag = SubEvent.EVENT_TAG_updateStatus)
    public void updateStatus(SubEvent event)
    {
         ....
    }   
```	
  
4.产生事件，发送消息 postEvent，会把这个Event加入消息队列，然后通过Event的类型信息来得到它的订阅方法，然后根据相应的订阅方式反射调用订阅方法。没有选择的threadMode的post一般都会在UI线程执行。如果当前post不是UI线程，这边会用Handle的机制来让接受Event的方法运行在UI线程。  

EventBus.getDefault().post(new SubEvent(), SubEvent.EVENT_TAG_updateStatus);  

```java  
  public void post(Object event, String tag) {
        ((Queue)this.mLocalEvents.get()).offer(new EventType(event.getClass(), tag));  // 接收事件
        this.mDispatcher.dispatchEvents(event); // 分发事件
    }  
```
5. 解除注册，将该对象从对象缓存列表中移除，获取当前对象的订阅列表，然后将其从订阅列表移除：
 EventBus.getDefault().unregister(this);  
 
 ```java   
 public void unregister(Object subscriber) {
        if (subscriber != null) {
            synchronized(this) {
                this.mMethodHunter.removeMethodsFromMap(subscriber);
            }
        }
    }  
public void removeMethodsFromMap(Object subscriber) {
        Iterator iterator = this.mSubcriberMap.values().iterator();

        while(true) {
            CopyOnWriteArrayList subscriptions;
            label37:
            do {
                if (!iterator.hasNext()) {
                    return;
                }

                subscriptions = (CopyOnWriteArrayList)iterator.next();
                if (subscriptions != null) {
                    List<Subscription> foundSubscriptions = new LinkedList();
                    Iterator subIterator = subscriptions.iterator();

                    while(true) {
                        Subscription subscription;
                        Object cacheObject;
                        do {
                            if (!subIterator.hasNext()) {
                                subscriptions.removeAll(foundSubscriptions);
                                continue label37;
                            }

                            subscription = (Subscription)subIterator.next();
                            cacheObject = subscription.subscriber.get();
                        } while(!this.isObjectsEqual(cacheObject, subscriber) && cacheObject != null);

                        Log.d("", "### 移除订阅 " + subscriber.getClass().getName());
                        foundSubscriptions.add(subscription);
                    }
                }
            } while(subscriptions != null && subscriptions.size() != 0);

            iterator.remove();
        }
    }  
```	


####  优点和缺点    
优点：
*  代码的简洁性，并且能够有效地降低消息发布者和订阅者之间的耦合度。  
*  使用方便。  

缺点：效率不高。


####   使用场景   
不一定要使用事件总线，根据实际需求，找到最有解。  


 