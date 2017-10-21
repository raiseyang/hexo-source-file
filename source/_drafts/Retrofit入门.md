title: Retrofit入门
author: raiseyang
date: 2017-08-05 18:23:55
tags:
---
> Retrofit2+RxJava2方式

GET添加参数：

```
	//password,username都会作为get参数
    @GET("polls/register/?password=123456")
    Observable<ResponseBody> register(@Query("username") String username);
    ```
    
动态path:
```
    @GET("polls/{id}/")
    Observable<ResponseBody> question(@Path("id") int id);
```
