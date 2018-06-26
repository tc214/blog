###   �¼�����-AndroidEventBus  
   
####   ����   
 EventBus��һ�����ڼ�Andorid��Fragment��Threads��Service֮����Ϣ���ݵ�һ������/�����¼�����  
 
 
 
####   ʹ�÷�ʽ   
1.ע���¼� ע��EventBus��EventBus���ȡ��ǰ���еĶ��ķ��������������Ĳ��������ͣ����ȼ�����Ϣ���ڻ�ȡ�ķ����У����ȱ������档����ڻ�����û�в�ȥ���û�ȡ������
e:  
EventBus.getDefault().register(this);    
EventBus.getDefault()�ǻ�ȡ��������Ȼ��ע��
```java  
    private EventBus() {  // ˽�й��캯��
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
    // ��ȡ��������
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
	
      //ע���¼�
public void register(Object subscriber) {
        if (subscriber != null) {
            synchronized(this) {
                this.mMethodHunter.findSubcribeMethods(subscriber);
            }
        }
    }  
     // Ѱ�Ҷ��ķ���
    public void findSubcribeMethods(Object subscriber) {
        if (this.mSubcriberMap == null) {
            throw new NullPointerException("the mSubcriberMap is null. ");
        } else {
            for(Class clazz = subscriber.getClass(); clazz != null && !this.isSystemCalss(clazz.getName()); clazz = clazz.getSuperclass()) {
			    // �õ�clazz���з������������̳еķ�����
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
    // ���涩���ߣ�Ϊÿһ���¼����ʹ���һ��list����������¼����͵����ж�����
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
	
2. ʹ��ע������ȡ���ķ��� 


3.������Ϣ  
```java  

 @Subscriber(tag = SubEvent.EVENT_TAG_updateStatus)
    public void updateStatus(SubEvent event)
    {
         ....
    }   
```	
  
4.�����¼���������Ϣ postEvent��������Event������Ϣ���У�Ȼ��ͨ��Event��������Ϣ���õ����Ķ��ķ�����Ȼ�������Ӧ�Ķ��ķ�ʽ������ö��ķ�����û��ѡ���threadMode��postһ�㶼����UI�߳�ִ�С������ǰpost����UI�̣߳���߻���Handle�Ļ������ý���Event�ķ���������UI�̡߳�  

EventBus.getDefault().post(new SubEvent(), SubEvent.EVENT_TAG_updateStatus);  

```java  
  public void post(Object event, String tag) {
        ((Queue)this.mLocalEvents.get()).offer(new EventType(event.getClass(), tag));  // �����¼�
        this.mDispatcher.dispatchEvents(event); // �ַ��¼�
    }  
```
5. ���ע�ᣬ���ö���Ӷ��󻺴��б����Ƴ�����ȡ��ǰ����Ķ����б�Ȼ����Ӷ����б��Ƴ���
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

                        Log.d("", "### �Ƴ����� " + subscriber.getClass().getName());
                        foundSubscriptions.add(subscription);
                    }
                }
            } while(subscriptions != null && subscriptions.size() != 0);

            iterator.remove();
        }
    }  
```	


####  �ŵ��ȱ��    
�ŵ㣺
*  ����ļ���ԣ������ܹ���Ч�ؽ�����Ϣ�����ߺͶ�����֮�����϶ȡ�  
*  ʹ�÷��㡣  

ȱ�㣺Ч�ʲ��ߡ�


####   ʹ�ó���   
��һ��Ҫʹ���¼����ߣ�����ʵ�������ҵ����н⡣  


 