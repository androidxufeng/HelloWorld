#### Distinct
  去重 不多加 描述  Flowable.just(1,2,2,3),下游只能接收到1,2,3
  
#### filter
 过滤器，和FileFilter作用类似
 ```java
 Observable.just(1, 20, 65, -5, 7, 19)
                .filter(new Predicate<Integer>() {
                    @Override
                    public boolean test(@NonNull Integer integer) throws Exception {
                        return integer >= 10;
                    }
                }).subscribe(new Consumer<Integer>() {
            @Override
            public void accept(@NonNull Integer integer) throws Exception {
                mRxOperatorsText.append("filter : " + integer + "\n");
                Log.e(TAG, "filter : " + integer + "\n");
            }
        });
 ```
 下游只会收到大于10的事件。
 
 #### buffer
 
 将多个事件打包成一个list发送，<br>
 Observable.just("one", "two", "three", "four", "five"）
            .buffer(3,1)输出的序列
            
* one two three
* two three four
* three four five
* four five
* five

第一个参数代表最多几个数据，第二个参数代表下次跳过数量 buffer（3,3） = buffer（3）

#### Timer 默认在子线程
 Observable.timer(3,TimeUnit.SECONDS)   延迟3s后发射一个Long型的0，然后onComplete，只能发射一个事件
 
#### interval 同样在子线程
Observable.interval(0,2,TimeUnit.SECONDS) 会不断发射long型事件 0,1,2....

+ 参数一，延迟多少执行
+ 参数二，后面每个执行间隔
+ 参数三，时间单位

#### skip 
 Observable.just(1, 2, 3, 4, 5)
 .skip(2)
 <br> 跳过前两个 下游只能接收到3,4,5
 
#### take 至多接收几个数据
 Observable.just(1,2,3,5)
                .take(3)
 <br>
 下游只能接收到1,2,3
 
#### debounce  防抖动
```java
Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                // send events with simulated time wait
                emitter.onNext(1); // skip
                Thread.sleep(400);
                emitter.onNext(2); // skip
                Thread.sleep(405);
                emitter.onNext(3); // skip
                Thread.sleep(100);
                emitter.onNext(4); // deliver
                Thread.sleep(605);
                emitter.onNext(5); // deliver
                Thread.sleep(510);
                emitter.onComplete();
            }
            .debounce(500, TimeUnit.MILLISECONDS) // 如果一个事件后面500ms后又有一个事件，则前一个事件不会被接收
  ```
  
  
  #### last 取出最后一个值
  Observable.just("xufe", "xufeng")
                .last("default");<br>
    如果发射数据是空，last中的参数代表是默认值
  
#### merge 和concat类似，但不保证顺序，两者之间无联系
```java
final String[] aStrings = {"A1", "A2", "A3", "A4"};
        final String[] bStrings = {"B1", "B2", "B3"};

        final Observable<String> aObservable = Observable.fromArray(aStrings);
        final Observable<String> bObservable = Observable.fromArray(bStrings);

        Observable.merge(aObservable.delay(3, TimeUnit.SECONDS), bObservable)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(getObserver());
 ```
 由于aString被延迟了3s，所以bString会先执行
 
 #### reduce
```java
 Observable.just(1, 2, 3, 4)
                .reduce(new BiFunction<Integer, Integer, Integer>() {
                    @Override
                    public Integer apply(Integer t1, Integer t2) {
                        Log.d(TAG, "apply: t1 = " + t1 + " t2= " + t2);
                        return t1 + t2;
                    }
                })
                //reduce 后面只能传入single或者是maybe sh
                .subscribe(getObserver());
 ```
 
 会将 第一个事件和第二事件生成 事件A ，然后和事件3 生成事件B 最后和时间4生成事件 C 下游只能接收到 C
 
 
 #### scan  
 和reduce类似，但会返回中间结果 及上述所说的事件A B C
 
 
 
