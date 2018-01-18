title: Rxjava2场景化学习-单个Observable
author: raiseyang
date: 2017-11-18 16:34:23
tags:
---
# Rxjava2场景化学习-单个Observable


### 多转多
场景:
> 查询数据库时,发射需要查询的id,返回对应对象.
```
Observable.just("raise", "django", "john")
        .flatMap(new Function<String, ObservableSource<Persion>>() {
            @Override
            public ObservableSource<Persion> apply(String s) throws Exception {
                //query db
                return Observable.just(new Persion(s));
            }
        });
```
`flatMap`可以变换发射的对象,示例中发射对象由多个`String`->多个`Persion`;
<!-- more -->
### 多转单
场景:
> 查询数据库时,需要将查询到的每条数据发射出来,然后转换成集合再发射(接口需要).
```
Observable.just("raise", "django", "john")
        .flatMap(new Function<String, ObservableSource<Persion>>() {
            @Override
            public ObservableSource<Persion> apply(String s) throws Exception {
                //query db
                return Observable.just(new Persion(s));
            }
        })
        .toList()
        .toObservable();
```
`toList`可以将发射的数个对象转换成一个`List`对象发射,注意*toList将返回`Single`而非Observable*,为了操作方便,使用`toObservable`再此转换成熟悉的Observable`;

### 单转多
> 需要遍历某个集合.
```
List<String> list = Arrays.asList("raise", "django", "john");
Observable.fromIterable(list);
Observable.just(list)
        .flatMap(new Function<List<String>, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(List<String> strings) throws Exception {
                return Observable.fromIterable(strings);
            }
        });
```
`Observable.fromIterable`可以代替`for`循环,产生Rxjava式的循环.

### 线程切换
> 安卓开发中,在主线程是调用http请求图片,然后在主线程中做图片展示
```
Observable.just(image_url)
        .subscribeOn(Schedulers.io())
        .flatMap(new Function<String, ObservableSource<Bitmap>>() {
            @Override
            public ObservableSource<Bitmap> apply(String s) throws Exception {
                return http_get_image(s);//模拟获取图片
            }
        })
        .observeOn(AndroidSchedulers.mainThread())
        .doOnNext(new Consumer<Bitmap>() {
            @Override
            public void accept(Bitmap b) throws Exception {
                image_view.setImageBitmap(b);
            }
        })
        .observeOn(Schedulers.io())
        .doOnNext(new Consumer<Bitmap>() {
            @Override
            public void accept(Bitmap b) throws Exception {
                //数据库,更新图片展示次数
            }
        })
        .subscribe();
```
`subscribeOn`将线程指定为新建`io`线程;
`flatMap`前未指定线程,所以运行在当前线程,也就是`io`线程,从网络上获取图片;
`observeOn`切换线程为安卓main线程;
`doOnNext`运行在切换后的main线程,更新图片;
`observeOn`切换线程为`新io`线程;
`doOnNext`运行在切换后的`新io`线程,更新数据库记录;
- `subscribeOn`只需要在创建Observable时,指定一次运行线程即可.后续对流的操作需要切换线程时,使用`observeOn`即可.

### 错误处理
> 向服务端发起请求时,若出现异常(网络超时,连接失败等),将直接抛出`Exeception`,此时我们并不想让订阅者去处理该错误,我们希望转化该错误,模拟一个服务器的正常返回,只是携带的错误码是我们定义的.
```
RetrofitProvider.getApi()
.checkToken(json)
//如果出现网络异常,抛出9999状态码
.onErrorResumeNext(throwable -> {
    ServerResponse response = new ServerResponse();
    response.setStatus(9999);
    response.setMsg(throwable.toString());
    return Observable.just(response);
})
.flatMap(response -> {
    if(response.getStatus() == 9999){
        //处理网络异常
    }else{
        //处理请求成功数据
    }
})
```
`onErrorResumeNext`将异常转换为一个普通对象发射.

### 定时器
> App启动时,将会异步去网络上注册用户;界面启动之后,判断该用户是否有效,若有效则请求网络数据,若无效则提示错误.
```
Observable.interval(5,2, TimeUnit.SECONDS)
        .observeOn(Schedulers.io())
        .subscribe(new Observer<Long>() {
            Disposable disposable;
            @Override
            public void onSubscribe(Disposable d) {
                this.disposable = d;
            }
            @Override
            public void onNext(Long aLong) {
                if (isValid()) {
                    disposable.dispose();
                    fetch_data();
                }
                if (aLong > 10) {
                    disposable.dispose();
                    Observable.error(new RuntimeException("register error"));
                }
            }
            @Override
            public void onError(Throwable e) {
                //更新错误UI
            }
            @Override
            public void onComplete() {
            }
        });
```
`interval`启动另一个线程定时发射一个`long`型数据,从0开始,没发射一次+1,第一次发射在5s后,后面间隔时间2s发射新值;