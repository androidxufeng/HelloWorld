JSON [(官网)](http://json.org/json-zh.html)是一种文本形式的数据交换格式，它比XML更轻量、比二进制容易阅读和编写，调式也更加方便。其重要性不言而喻。
解析和生成的方式很多，Java中最常用的类库有：JSON-Java、Gson、Jackson、FastJson等，本次我向大家介绍的是Gson。

### 1.Gson的基本用法
Gson提供了 *fromJson()* 和 *toJson()* 两个直接用于解析和生成的方法，前者实现反序列化，后者实现序列化。


##### 基本数据类型的解析
```java
Gson gson = new Gson();
int i = gson.fromJson("100", int.class);              //100
double d = gson.fromJson("\"99.99\"", double.class);  //99.99
boolean b = gson.fromJson("true", boolean.class);     // true
String str = gson.fromJson("String", String.class);   // String
```

##### 基本数据类型的生成
```java
Gson gson = new Gson();
String jsonNumber = gson.toJson(100);       // 100
String jsonBoolean = gson.toJson(false);    // false
String jsonString = gson.toJson("String"); //"String"
```

##### POJOl类的解析和生成
```java
public class User {
    //省略其它
    public String name;
    public int age;
    public String emailAddress;
}
```

生成JSON
```java
Gson gson = new Gson();
User user = new User("徐烽",24);
String jsonObject = gson.toJson(user); // {"name":"徐烽","age":24}
```
解析JSON
```java
Gson gson = new Gson();
String jsonString = "{\"name\":\"徐烽\",\"age\":24}";
User user = gson.fromJson(jsonString, User.class);
```

### 2.属性重命名@SerializedName 注解的使用

从上面POJO的生成与解析可以看出json的字段和值是的名称和类型是一一对应的，但也有一定容错机制(如第一个例子第3行将字符串的99.99转成double型，
你可别告诉我都是字符串啊)，但有时候也会出现一些不和谐的情况，如：期望的json格式
```java
  {"name":"xufeng","age":24,"emailAddress":"xxx@example.com"} 
```
实际
```java
  {"name":"xufeng","age":24,"email_address":"xxx@example.com"}
 ```
 这对于使用PHP作为后台开发语言时很常见的情况，php和js在命名时一般采用下划线风格，而Java中一般采用的驼峰法，让后台的哥们改吧 前端和后台都不爽，
 但要自己使用下划线风格时我会感到不适应，怎么办?难到没有两全齐美的方法么?
 
 那么对于json中email_address这个属性对应POJO的属性则变成：
 ```java
 //此时"email_address"和"emailAddress"均可匹配
 @SerializedName("email_address")
  public String emailAddress;
  
  // 更有甚者,此时可以对上三种
  @SerializedName(value = "emailAddress", alternate = {"email", "email_address"})
  public String emailAddress;
 ```
 ### 3.Gson中使用泛型解析array
 例：JSON字符串数组
 ```java
 ["Android","Java","PHP"]
 ```
 当我们要通过Gson解析这个json时，一般有两种方式：使用数组，使用List。而List对于增删都是比较方便的，所以实际使用是还是List比较多.
```java
Gson gson = new Gson();
String jsonArray = "[\"Android\",\"Java\",\"PHP\"]";
String[] strings = gson.fromJson(jsonArray, String[].class);
List<String> stringList = gson.fromJson(jsonArray, new TypeToken<List<String>>() {}.getType());
```

### 4.GsonBuilder配置
```java
Gson gson = new GsonBuilder()
        //序列化null
        .serializeNulls()
        // 设置日期时间格式，另有2个重载方法
        // 在序列化和反序化时均生效
        .setDateFormat("yyyy-MM-dd")
        // 禁此序列化内部类
        .disableInnerClassSerialization()
        //生成不可执行的Json（多了 )]}' 这4个字符）
        .generateNonExecutableJson()
        //禁止转义html标签
        .disableHtmlEscaping()
        //格式化输出
        .setPrettyPrinting()
        .create();
```
### 5.字段过滤的几种方法
字段过滤Gson中比较常用的技巧，特别是在Android中，在处理业务逻辑时可能需要在设置的POJO中加入一些字段，但显然在序列化的过程中是不需要的。 

#### 5.1 基于@Expose注解

@Expose 注解从名字上就可以看出是暴露的意思，所以该注解是用于对外暴露字段的。
可是我们以前用Gson的时候也没有@Expose 注解还不是正确的序列化为JSON了么?是的，所以该注解在使用new Gson() 时是不会发生作用。
毕竟最常用的API要最简单，所以该注解必须和GsonBuilder配合使用。

使用方法： 简单说来就是需要导出的字段上加上@Expose 注解，不导出的字段不加。注意是不导出的不加。
```java
@Expose //
@Expose(deserialize = true,serialize = true) //序列化和反序列化都都生效，等价于上一条
@Expose(deserialize = true,serialize = false) //反序列化时生效
@Expose(deserialize = false,serialize = true) //序列化时生效
@Expose(deserialize = false,serialize = false) // 和不写注解一样
```
举个例子
```java
public class Category {
    @Expose 
    public int id;
    @Expose 
    public String name;
    @Expose 
    public List<Category> children;
    //不需要序列化,所以不加 @Expose 注解，
    //等价于 @Expose(deserialize = false,serialize = false)
    public Category parent; 
}
```
同时使用Gson时也必须用GsonBuilde
```java
Gson gson = new GsonBuilder()
        .excludeFieldsWithoutExposeAnnotation()
        .create();
```


#### 5.2基于版本（@Since和@Until）
Gson在对基于版本的字段导出提供了两个注解 @Since 和 @Until,和GsonBuilder.setVersion(Double)配合使用。@Since 和 @Until都接收一个Double值

使用方法：当前版本(GsonBuilder中设置的版本) 大于等于Since的值时该字段导出，小于Until的值时该该字段导出。
```java
class SinceUntilSample {
    @Since(4)
    public String since;
    @Until(5)
    public String until;
}

public void sineUtilTest(double version){
        SinceUntilSample sinceUntilSample = new SinceUntilSample();
        sinceUntilSample.since = "since";
        sinceUntilSample.until = "until";
        Gson gson = new GsonBuilder().setVersion(version).create();
        System.out.println(gson.toJson(sinceUntilSample));
}
//当version <4时，结果：{"until":"until"}
//当version >=4 && version <5时，结果：{"since":"since","until":"until"}
//当version >=5时，结果：{"since":"since"}
```
#### 5.3 基于访问修饰符
什么是修饰符? public、static 、final、private、protected 这些就是，所以这种方式也是比较特殊的。

使用方式：
```java
class ModifierSample {
    final String finalField = "final";
    static String staticField = "static";
    public String publicField = "public";
    protected String protectedField = "protected";
    String defaultField = "default";
    private String privateField = "private";
}
```

使用GsonBuilder.excludeFieldsWithModifiers构建gson,支持int形的可变参数，值由java.lang.reflect.Modifier提供，
下面的程序排除了privateField 、 finalField 和staticField 三个字段。
```java
ModifierSample modifierSample = new ModifierSample();
Gson gson = new GsonBuilder()
        .excludeFieldsWithModifiers(Modifier.FINAL, Modifier.STATIC, Modifier.PRIVATE)
        .create();
System.out.println(gson.toJson(modifierSample));
// 结果：{"publicField":"public","protectedField":"protected","defaultField":"default"}
```
 
