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
 
 ####
