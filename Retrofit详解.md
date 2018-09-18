## [Retrofit](https://github.com/square/retrofit)
[Retrofit优秀教程1](https://www.jianshu.com/p/308f3c54abdd "怪盗kidou")

[Retrofit源码分析](https://www.jianshu.com/p/0c055ad46b6c)

### 1.注解类型
![Retrofit注解](https://upload-images.jianshu.io/upload_images/944365-ee747d1e331ed5a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)


#### 1.1 网络请求方法
@GET、@POST、@PUT、@DELETE、@HEAD分别对应http中的网络请求方式

@HTTP可以代替上述的所有方法，通过method，path，hasBody来设置属性
```java
public interface GetRequest_Interface {
    /**
     * method：网络请求的方法（区分大小写）
     * path：网络请求地址路径
     * hasBody：是否有请求体
     */
    @HTTP(method = "GET", path = "blog/{id}", hasBody = false)
    Call<ResponseBody> getCall(@Path("id") int id);
    // {id} 表示是一个变量
    // method 的值 retrofit 不会做处理，所以要自行保证准确
}
```
#### 1.2 标记 （暂时不太懂）
##### 1.2.1 @FormUrlEncoded 表示请求体是Form表单
每个键值对都需要用@Filed来注解键名
##### 1.2.2 @MultiPart 表示请求体是支持文件上传的Form表单
每个键值对都需要用@Part来注解键名
##### 1.2.3 @Streaming 
表示返回的数据以流的形式返回，适用于返回数据较大的场景（如果没有使用该注解，会默认将返回数据写在内存中，之后读取数据也是在内存中读取）

#### 1.3 网络请求参数
##### @Header添加不固定请求头 & @Headers添加固定的请求头
##### @Filed & @FiledMap
+ 作用：发送 Post请求 时提交请求的表单字段
+ 具体使用：与 @FormUrlEncoded 注解配合使用

```java
// @Header
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)

// @Headers
@Headers("Authorization: authorization")
@GET("user")
Call<User> getUser()

//=============================================================
@POST("/form")
@FormUrlEncoded
Call<ResponseBody> testFormUrlEncoded1(@Field("username") String name, @Field("age") int age);

/**
* Map的key作为表单的键
*/
@POST("/form")
@FormUrlEncoded
Call<ResponseBody> testFormUrlEncoded2(@FieldMap Map<String, Object> map);
}
 // @Field
Call<ResponseBody> call1 = service.testFormUrlEncoded1("Carson", 24);

// @FieldMap
// 实现的效果与上面相同，但要传入Map
Map<String, Object> map = new HashMap<>();
map.put("username", "Carson");
map.put("age", 24);
Call<ResponseBody> call2 = service.testFormUrlEncoded2(map);

//======================================================================

```

### 2.创建retrofit实例
```java
Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://fanyi.youdao.com/") // 设置网络请求的Url地址
                .addConverterFactory(GsonConverterFactory.create()) // 设置数据解析器
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create()) // 支持RxJava平台
                .build();
```
