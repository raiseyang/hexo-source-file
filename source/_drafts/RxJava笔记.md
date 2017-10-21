title: RxJava2笔记
author: raiseyang
date: 2017-08-06 17:16:47
tags:
---
# 创建一个

# Observable Observer
发射或处理事件；
```
//产生数据端
        final Observable<Integer> observable = Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> e) throws Exception {
                Trace.d(TAG, "subscribe() thread name = %s", Thread.currentThread().getName());
                e.onNext(1);
//                e.onError(new NullPointerException("11"));
                e.onNext(2);
//                e.onError(new NullPointerException("11"));
                e.onComplete();
                e.onNext(3);

            }
        });
        //处理数据端
        final Observer<Integer> observer = new Observer<Integer>() {
            @Override
            public void onSubscribe(Disposable d) {
                Trace.d(TAG, "onSubscribe() d = %s", d.isDisposed());
//                d.dispose();
            }

            @Override
            public void onNext(Integer integer) {
                Trace.d(TAG, "onNext() [integer] " + integer + " thread name = " + Thread.currentThread().getName());
            }

            @Override
            public void onError(Throwable e) {
                Trace.e(TAG, "onError() e = " + e);
            }

            @Override
            public void onComplete() {
                Trace.i(TAG, "onComplete() ");
            }
        };

        observable
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(observer);
```

# flatMap
将每一个发射事件，转化为一个或多个发射事件；
```
Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> e) throws Exception {
                e.onNext(1);
                e.onNext(2);
                e.onNext(3);
            }
        }).flatMap(new Function<Integer, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(Integer integer) throws Exception {

                ArrayList<String> list = new ArrayList<>();
                list.add(integer + " value");
                list.add(integer + " value");
                list.add(integer + " value");

                return Observable.fromIterable(list).delay(10, TimeUnit.MILLISECONDS);
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Trace.d(TAG, "accept() s = %s", s);
            }
        });
```
# zip
将2个Observable压缩至1个Observable发射；最先调用`e.onComplete()`结束；
```
Observable<Integer> integerObservable = Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> e) throws Exception {
                e.onNext(1);
                e.onNext(2);
                e.onNext(3);
                e.onComplete();
            }
        }).subscribeOn(Schedulers.io());
        Observable<String> stringObservable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                e.onNext(1 + "");
                e.onNext(2 + "");
                e.onNext(3 + "");
                e.onComplete();
            }
        }).subscribeOn(Schedulers.io());

        Observable.zip(integerObservable, stringObservable, new BiFunction<Integer, String, String>() {
            @Override
            public String apply(Integer integer, String s) throws Exception {
                return integer + "  " + s;
            }
        }).observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        Trace.d(TAG, "accept() s = %s", s);
                    }
                });
```
# Flowable Subscriber
当发射数量太多的时候使用，能控制流量；
```
        Flowable flowable = Flowable.create(new FlowableOnSubscribe<Integer>() {
            @Override
            public void subscribe(FlowableEmitter<Integer> e) throws Exception {
                e.onNext(1);
                e.onNext(2);
                e.onNext(3);
            }
        }, BackpressureStrategy.ERROR);
        Subscriber subscriber = new Subscriber<Integer>() {
            @Override
            public void onSubscribe(Subscription s) {
                s.request(Long.MAX_VALUE);
                Trace.d(TAG, "onSubscribe() ");
            }

            @Override
            public void onNext(Integer o) {
                Trace.d(TAG, "onNext() o = %s", o);
            }

            @Override
            public void onError(Throwable t) {
                Trace.e(TAG, "onError() e = " + t);
            }

            @Override
            public void onComplete() {
                Trace.d(TAG, "onComplete() ");
            }
        };
        flowable.subscribe(subscriber);

```


# 参考资料
[简书RxJava2专题](http://www.jianshu.com/c/299d0a51fdd4)