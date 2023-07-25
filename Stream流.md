### 
### 
### 
### 
### 
### 
### 
### 
### 一、函数式编程思想
#### 1.1、概念
> 面相对象思想需要关注用什么对象完成什么事情。而函数式编程思想就类似于我么数学中的函数。它主要关注的是对数据进行了什么操作。

#### 1.2、优点
> - 代码简洁，开发快速
> - 接近自然语言，易于理解
> - 易于“并发编程”


### 二、Lambda表达式
> Lambda表达式是JDK1.8中的一个语法糖。它可以对某些匿名内部类的写法进行简化。它是函数式编程思想的一个重要体现，让我们不用关注是什么对象，而是更去关注我们对数据进行了什么操作。

#### 2.1、基本格式
> (参数列表) -> {代码}

##### 例一
> 改造前
> 正常新建线程并执行（匿名内部类）

```java
new Thread(new Runnable() {
    @override
    public void run() {
        System.out.println("今天天气真好");
    }
}).start();
```
> 改造后
> 使用Lambda表达式新建线程并执行
> 我们只关注它的形参列表以及方法体

```java
new Thread(() -> {
    System.out.println("今天天气真好");
}).start();

//进阶：
//方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
//参数类型可以省略
new Thread(() -> System.out.println("今天天气真好")).start();
```
##### 例二
> 改造前

```java
public static int calculateNum(IntBinaryOperator operator){
    int a = 10;
    int b = 20;
    return operator.applyAsInt(a,b);
}

public static void main(String[] args) {
    int num = calculateNum(new IntBinaryOperator() {
        @Override
        public int applyAsInt(int left, int right) {
            return left + right;
        }
    });
    System.out.println(num);
}
```
> 改造后
> **注意：**
> 通常情况下，我们会先将匿名内部类写出来，然后再将光标移动到new的内部类上，然后Alt+Enter，就会提示该匿名内部类是否可以被Lambda表达式简化(Replace with lambda)
> 
> 若看不懂lambda表达式，则可以把光标移动到形参列表的参数中，然后Alt+Enter，就可以将lambda表达式转换成匿名内部类(Replace lambda with anonymous class)

```java
public static void main(String[] args) {
    int num = calculateNum((int left, int right) ->{
        return left + right;
    });
    System.out.println(num);
}

//进阶：
//方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
//参数类型可以省略
public static void main(String[] args) {
    int num = calculateNum((left, right) -> left + right);
    System.out.println(num);
}
```
##### 例三
> 改造前

```java
public static void printNum(IntPredicate predicate){
    int[] arr = {1,2,3,4,5,6,7,8,9,10};
    for (int i : arr) {
        if (predicate.test(i)) {
            System.out.println(i);
        }
    }
}

public static void main(String[] args) {
    printNum(new IntPredicate() {
        @Override
        public boolean test(int value) {
            return value %2 == 0;
        }
    });
}
```
> 改造后

```java
public static void main(String[] args) {
    printNum((int value) -> {
        return value %2 ==0;
    });
}

//进阶：
//方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
//参数类型可以省略
public static void main(String[] args) {
    printNum(value -> value %2 == 0);
}
```
##### 例四
> 改造前

```java
/**
 * Function泛型中，左边的泛型会作为形参，右边的泛型会作为返回值
 * @param function
 * @return
 * @param <R>
 */
public static <R> R typeConver(Function<String,R> function){
    String str = "12345";
    R result = function.apply(str);
    return result;
}

public static void main(String[] args) {
    Integer result = typeConver(new Function<String, Integer>() {
        @Override
        public Integer apply(String s) {
            return Integer.valueOf(s);
        }
    });
    System.out.println(result);
}
```
> 改造后

```java
public static void main(String[] args) {
    Integer result = typeConver((String s) ->{
        return Integer.valueOf(s);
    });
    System.out.println(result);
}

//进阶：
//方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
//参数类型可以省略
public static void main(String[] args) {
    Integer result = typeConver(s -> Integer.valueOf(s));
    System.out.println(result);
}
```
##### 例五
> 改造前

```java
public static void forEachArr(IntConsumer consumer){
    int [] arr = {1,2,3,4,5,6,7,8,9,10};
    for (int i : arr) {
        consumer.accept(i);
    }
}

public static void main(String[] args) {
    forEachArr(new IntConsumer() {
        @Override
        public void accept(int value) {
            System.out.println(value);
        }
    });
}
```
> 改造后

```java
public static void main(String[] args) {
    forEachArr((int value) -> {
        System.out.println(value);
    });
}

//进阶：
//方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
//参数类型可以省略
public static void main(String[] args) {
    forEachArr(value -> System.out.println(value));
}
```
#### 2.2、省略规则
> 1. 参数类型可以省略
> 2. 方法体只有一句代码时，大括号、return和唯一一句代码的分号可以省略
> 3. 方法只有一个参数时小括号可以省略
> 4. 以上这些规则都记不住也可以省略不记

### 三、Stream流
> Java8的Stream使用的是函数式编程模式，如同它的名字一样，它可以被用来对**集合或数组**进行链状流式的操作。
> 可以更方便的让我们对集合或数组操作

#### 3.1、测试案例
> 准备数据

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```
```java
@Data
//无参构造
@NoArgsConstructor
//带参构造
@AllArgsConstructor
//equals和hashCode方法
@EqualsAndHashCode
public class Author {
    //id
    private Long id;
    //姓名
    private String name;
    //年龄
    private Integer age;
    //简介
    private String intro;
    //作品
    private List<Book> books;
}
```
```java
@Data
//无参构造
@NoArgsConstructor
//带参构造
@AllArgsConstructor
//equals和hashCode方法
@EqualsAndHashCode
public class Book {
    //id
    private Long id;
    //书名
    private String name;
    //分类
    private String category;
    //评分
    private Integer score;
    //简介
    private String intro;
}
```
```java
private static List<Author> getAuthors(){
    //数据初始化
    Author author = new Author(1L, "蒙多", 33, "一个从菜刀中明悟哲理的祖安人", null);
    Author author2 = new Author(2L, "亚拉索", 15, "狂风也追逐不上他的思考速度", null);
    Author author3 = new Author(3L, "易", 14, "是这个世界在限制他的思维", null);
    Author author4 = new Author(3L, "易", 14, "是这个世界在限制他的思维", null);

    //书籍列表
    ArrayList<Book> books1 = new ArrayList<>();
    ArrayList<Book> books2 = new ArrayList<>();
    ArrayList<Book> books3 = new ArrayList<>();

    books1.add(new Book(1L,"刀的两侧是光与暗","哲学,爱情",88,"用一把刀划分了爱恨"));
    books1.add(new Book(2L,"一个人不能死在同一把刀下","个人成长,爱情",99,"讲述如何从失败到成功"));

    books2.add(new Book(3L,"那风吹不到的地方","哲学",85,"带你用思维去领略世界的尽头"));
    books2.add(new Book(3L,"那风吹不到的地方","哲学",85,"带你用思维去领略世界的尽头"));
    books2.add(new Book(4L,"吹或不吹","爱情,个人传记",56,"一个哲学家的恋爱观注定很难把"));

    books3.add(new Book(5L,"你的剑就是我的剑","爱情",56,"无法想象一个武者能对他的伴侣这"));
    books3.add(new Book(6L,"风与剑","个人传记",100,"两个科学家灵魂和肉体的碰撞会激起怎么"));
    books3.add(new Book(6L,"风与剑","个人传记",100,"两个科学家灵魂和肉体的碰撞会激起怎么"));

    author.setBooks(books1);
    author2.setBooks(books2);
    author3.setBooks(books3);
    author4.setBooks(books3);

    List<Author> authorList = new ArrayList<>(Arrays.asList(author, author2, author3, author4));
    return authorList;
}
```
##### 例一
> 打印所有年龄小于18的作家的名字，并且需要去重

```java
public static void main(String[] args) {
    List<Author> authors = getAuthors();
    authors
            //把集合转换成流
            .stream()
            //调用去重方法
            .distinct()
            //过滤，根据年龄(lambda表达式)
            .filter(author -> author.getAge() < 18)
            //循环打印name(lambda表达式)
            .forEach(author -> System.out.println(author.getName()));

}
```
> 可以使用debug添加断点查看Stream流做了些什么

![image.png](https://cdn.nlark.com/yuque/0/2022/png/29713630/1668925453846-3e534607-a009-47f3-94ec-50fbf5185b8b.png#averageHue=%2364654e&clientId=udc9eeec3-79bf-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=600&id=u12acb536&margin=%5Bobject%20Object%5D&name=image.png&originHeight=750&originWidth=1602&originalType=binary&ratio=1&rotation=0&showTitle=false&size=120900&status=done&style=none&taskId=uaf5a271c-8d8c-41a9-9147-31f07f803ab&title=&width=1281.6)
#### 3.2、常用操作
##### 3.2.1、头操作（创建流）
> 单列集合：
> - 集合对象.stream()

```java
List<Author> authors = getAuthors();
Stream<Author> stream = authors.stream();
```
> 数组：
> - Arrays.stream(数组)
> - Stream.of(数组)

```java
Integer [] arr = {1,2,3,4,5};
Stream<Integer> stream = Arrays.stream(arr);
Stream<Integer> stream2 = Stream.of(arr);
```
> 双列集合(key,value)：
> - 转换成单列集合后再创建

```java
Map<String, Integer> map = new HashMap<>();
map.put("蜡笔小新",19);
map.put("黑子",17);
map.put("日向翔阳",16);

Stream<Map.Entry<String, Integer>> stream = map.entrySet().stream();
```
##### 3.2.2、中间操作
###### filter
> 可以对流中的元素进行条件过滤，符合过滤条件的才能继续留在流中。
> 例如：
> - 打印所有姓名长度大于1的作家的姓名

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //先获取姓名，再获取姓名长度，然后进行比较 - 中间操作
            .filter(author -> author.getName().length() > 1)
            //输出过滤后的结果 - 终结操作
            .forEach(author -> System.out.println(author.getName()));
```
###### map
> 可以对流中的元素进行计算或者转换
> 例如：
> - 把作家对象转换成作家的名字(字符串)然后输出
>    - 对象转字符串

```java
//将对象转换成字符串
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //获取作家对象中的所有作家名字，然后返回名字的集合 - 中间操作
            .map(author -> author.getName())
            //输出作家名字 - 终结操作
            .forEach(s -> System.out.println(s));

//将对象转换成Integer 然后再进行计算
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //获取作家对象中的所有作家年龄，然后返回年龄的集合 - 中间操作
            .map(author -> author.getAge())
            //将所有的年龄加10 - 中间操作
            .map(age -> age + 10)
            //输出所有作家的年龄 - 终结操作
            .forEach(age -> System.out.println(age));
```
###### distinct
> 可以去除流中的重复元素
> 例如：
> 打印所有作家的姓名，并且要求其中不能有重复元素
> **注意：distinct方法是依赖Object的equals方法来判断是否是相同对象的，所以需要注意重写equals方法**
> - **Object的equals方法是等值判断**

```java
List<Author> authors = getAuthors();
authors
        //把集合转换成流 - 头操作
        .stream()
    	//去重 - 中间操作
        .distinct()
        //输出所有的作家姓名 - 终结操作
        .forEach(author -> System.out.println(author.getName()));
```
###### sorted
> 可对流中的元素进行排序
> 例如：
> 对流中的元素按照年龄进行降序排序，并且要求不能有重复的元素
> **注意：sorted有两个方法：**
> - **空参的方法：需要在实体类中实现Comparator接口，并指定字段进行排序**
> - **带参的方法：参数是一个匿名内部类（可用lambda表达式）**
> - **排序规则：**
>    - **小的减去大的 - 升序（从小到大）**
>    - **大的减去小的 - 降序（从大到小）**

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //去重 - 中间操作
            .distinct()
            //排序 - 中间操作
            .sorted((o1, o2) -> o2.getAge() - o1.getAge())
            //输出所有年龄 - 终结操作
            .forEach(author -> System.out.println(author.getAge()));
```
###### limit
> 可以设置流的最大长度，超出的部分将被抛弃
> 例如：
> 对流中的元素按照年龄进行降序排序，并且要求不能有重复的元素，然后打印其中年龄最大的两个作家的姓名

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //去重 - 中间操作
            .distinct()
            //排序 - 中间操作
            .sorted((o1, o2) -> o2.getAge() -o1.getAge())
            //设置流的长度(截取) - 中间操作
            .limit(2)
            //输出所有年龄 - 终结操作
            .forEach(author -> System.out.println(author.getName()));
```
###### skip
> 跳过流中的前n个元素，返回剩下的元素
> 例如：
> 打印除了年龄最大的作家外的其它作家，要求不能有重复元素，并且按照年龄降序排序

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //去重 - 中间操作
            .distinct()
            //排序 - 中间操作
            .sorted((o1, o2) -> o2.getAge() -o1.getAge())
            //设置跳过的元素个数 - 中间操作
            .skip(1)
            //输出所有年龄 - 终结操作
            .forEach(author -> System.out.println(author.getName()));
```
###### flatMap
> map只能把一个对象转换成另一个对象来作为流中的元素。
> 而flatMap可以把一个对象转换成多个对象作为流中的元素。
> - flatMap返回的是一个个的stream流对象
> 
例一：
> 打印所有数据的名字，要求去重

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //将一个作家对象转换成多个书本对象（因为书本对象是一个集合，因此是多个书本对象）
            .flatMap(author -> author.getBooks().stream())
            //去重
            .distinct()
            //输出所有书本的名字
            .forEach(book -> System.out.println(book.getName()));
```
> 例二：
> 打印现有数据的所有分类，要求对分类进行去重。不允许出现这种格式：哲学，爱情
> - **split方法：传入一个分隔符，根据分隔符分隔，然后返回一个数组**

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //将一个作家对象转换成多个书本对象（因为书本对象是一个集合，因此是多个书本对象）
            .flatMap(author -> author.getBooks().stream())
            //对书籍进行去重
            .distinct()
            //将分类数组转换成一个个分类(category)的stream流
            .flatMap(book -> Arrays.stream(book.getCategory().split(",")))
            //对分类进行去重
            .distinct()
            //输出所有分类的名字
            .forEach(category -> System.out.println(category));
```
##### 3.2.3、终结操作
###### forEach
> 对流中的元素进行遍历操作，我们通过传入的参数去指定通过遍历获得的元素去进行一些操作
> 例如：
> 输出一个作家对象集合中的所有作家的名字

```java
List<Author> authors = getAuthors();
    authors
            //把集合转换成流 - 头操作
            .stream()
            //将作家对象转换成作家姓名字符串
            .map(author -> author.getName())
            //去重
            .distinct()
            //输出所有作家的名字
            .forEach(name -> System.out.println(name));
```
###### count
> 可以用来获取当前流中元素的个数
> 例如：
> 打印这些作家的所出书籍的数目，要求去重

```java
List<Author> authors = getAuthors();
    long count = authors
            //把集合转换成流 - 头操作
            .stream()
            //将作家对象转换成作家姓名字符串
            .flatMap(author -> author.getBooks().stream())
            //去重
            .distinct()
            //对stream流中的数据进行统计 - 终结操作
            .count();
    System.out.println("共有："+count + "本书");
```
###### max&min
> 可以用来获取流中数据的最大/最小值
> 例如：
> 分别获取这些作家的所出书籍的最高分和最低分并输出
> **注意：需要实现comparable接口进行比较**
> - **comparable接口虽然可以作用于排序，但是它的主要功能是进行比较，通过比较来排序**

```java
List<Author> authors = getAuthors();
Integer max = authors
        //把集合转换成流 - 头操作
        .stream()
        //将作家对象转换成作家姓名字符串
        .flatMap(author -> author.getBooks().stream())
        //将书本对象转换成书本评分
        .map(book -> book.getScore())
        //对stream流中的数据进行统计 - 终结操作
        .max((score1, score2) -> score1 - score2)
        //因为max的返回值是一个option对象，因此我们需要去该对象中获取值
        .get();
System.out.println("最大值：" + max);
Integer min = authors
        //把集合转换成流 - 头操作
        .stream()
        //将作家对象转换成作家姓名字符串
        .flatMap(author -> author.getBooks().stream())
        //将书本对象转换成书本评分
        .map(book -> book.getScore())
        //对stream流中的数据进行统计 - 终结操作
        .min((score1, score2) -> score1 - score2)
        //因为max的返回值是一个option对象，因此我们需要去该对象中获取值
        .get();
System.out.println("最小值：" + min);
```
###### collect
> 把当前流转换成一个集合
> 例如：
> 获取一个存放所有作者名字的List集合
> - Collectors - 是一个工具方法，在java.util.stream包下

```java
List<Author> authors = getAuthors();
List<String> nameList = authors
        .stream()
        .map(author -> author.getName())
        .collect(Collectors.toList());
System.out.println(nameList);
```
> 例如：
> 获取一个所有书名的set集合
> 1. 先将所有作者的书转换成多个书对象
> 2. 将多个书对象的书名转换成一个流
> 3. 将stream流中的数据转换成一个set集合

```java
List<Author> authors = getAuthors();
Set<String> bookName = authors
        .stream()
        .flatMap(author -> author.getBooks().stream())
        .map(book -> book.getName())
        .collect(Collectors.toSet());
System.out.println(bookName);
```
> 例如
> 获取一个map集合
> - key：作者名
> - value：List<Book>
> 1. 转换成map集合的时候，toMap方法有两个参数
>    - key
>    - value
> 2. 先获取作者的姓名作为key
> 3. 然后获取作者的书本作为value
> 
**注意：转换map集合时，需要注意key是否有重复元素，若有重复的key则会报错，因此会先进行一个去重的操作**

```java
List<Author> authors = getAuthors();
Map<String, List<Book>> map = authors
        .stream()
        .distinct()
        .collect(Collectors.toMap(author -> author.getName(), author -> author.getBooks()));
System.out.println(map);
```
###### reduce归并
> 对流中的元素按照自己指定的计算方式得出一个结果
> reduce的作用是把stream流中的元素给组合起来，我们可以传入一个初始值，他会按照我们的计算方式依次拿流中的元素在初始化值的基础上进行计算，然后再拿计算得出的结果再和后面的元素计算
> 内部的计算方式如下：
> reduce参数列表（传一个参数和传两个参数各不相同，只传一个参数时，会默认将流中的第一个元素赋值给累加变量）：
> 1. 就是初始化一个值，用来接收累加值
> 2. 就是累加的值和下一个参数
> 
流程：使用初始化值(累加值)去加上下一个值，然后再用累加值去加下一个参数

```java
T result = identity；
for (T element : this stream)
    result = accumulator.apply(result, element)
return result;
```
> 注意：
> 单字段累加和多字段累加不一样
> 单字段累加：不需要使用一个对象去接收累加值，需要使用map进行分组，然后进行累加，最终用适合的类型去接收值即可
> 多字段累加：需要一个对象临时的去接收多字段的累加值

```java
//正常累加 - 多字段累加
List<Author> authors = getAuthors();
Author author1 = authors.stream()
        .reduce((x, y) -> {
            Author author = new Author();
            author.setAge(x.getAge() + y.getAge()); 
            author.setId(x.getId() + y.getId());
            return author;
        }).get();

System.out.println(author1.getId() + ":" + author1.getAge());

//Bigdicimal类型累加 - 多字段累加
User u=list.stream()
    	.reduce((x,y)->{
            User user=new User();
            user.setNum1(x.getNum1().add(y.getNum1()));
            user.setNum2(x.getNum2().add(y.getNum2()));
            return user;
        }).get();
        System.out.println(u.getNum1()+"------------"+u.getNum2());

//单字段累加（数组）
List<Integer> list= Arrays.asList(new Integer[]{1,2,3,4,5,6,7,8,9});
Integer sum=list.stream().reduce((x,y)->x+y).get();
System.out.println(sum);

//单字段累加（BigDicimal）
List<User> list=new ArrayList<>();
User user1=new User();
user1.setNum1(new BigDecimal(123));
user1.setNum2(new BigDecimal(100));
list.add(user1);
User user2=new User();
user2.setNum1(new BigDecimal(100));
user2.setNum2(new BigDecimal(100));
list.add(user2);
BigDecimal sum=list.stream().map(User::getNum1).reduce(BigDecimal::add).get();
System.out.println(sum);
```
##### 3.2.4、查找与匹配（终结操作）
###### anyMatch - 查找
> 可以用来判断是否有任意符合匹配条件的元素，结果为boolean类型
> 例如：
> 判断是否有年龄在29以上的作家

```java
List<Author> authors = getAuthors();
boolean flag = authors
        .stream()
        .anyMatch(author -> author.getAge() > 29);
System.out.println(flag);
```
###### allMatch - 查找
> 可以用来判断所有的指定元素是否都符合匹配条件，结果为boolean类型。如果都符合则结果为true，否则结果为false
> 例如：
> 判断是否所有的作家都是成年人
> - 它会对所有指定的字段的元素进行一个判断

```java
List<Author> authors = getAuthors();
boolean flag = authors
        .stream()
        .allMatch(author -> author.getAge() >= 18);
System.out.println(flag);
```
###### noneMatch - 查找
> 可以判断流中的元素是否都不符合匹配条件。如果都不符合则结果为true，否则结果为false
> 例如：
> 判断作家是否都没有超过100岁
> - 如果没有大于100岁的则为true

```java
List<Author> authors = getAuthors();
boolean flag = authors
        .stream()
        .noneMatch(author -> author.getAge() > 100);
System.out.println(flag);
```
###### findAny - 匹配
> 获取流中的任意一个元素。该方法没有办法保证获取的一定是流中的第一个元素。
> ifPresent方法：如果调用方的Optional对象中没有元素，则该方法不执行 - 防止空指针异常
> 例如：
> 获取任意一个年龄大于18的作家，如果存在则输出该作家的名字

```java
List<Author> authors = getAuthors();
Optional<Author> any = authors
        .stream()
        .filter(author -> author.getAge() > 18)
        .findAny();
any.ifPresent(author -> System.out.println(author.getName()));
```
###### findFirst - 匹配
> 获取流中的第一个元素
> 例如：获取一个年龄最小的作家，并输出他的姓名
> - 因为我们并不知道最小的年龄是几岁，因此我们先对年龄进行一个排序，让最小年龄的排在第一个，然后使用该方法就可以获取到最小年龄的作家

```java
List<Author> authors = getAuthors();
Optional<Author> first = authors
        .stream()
        .sorted((o1,o2) -> o1.getAge() - o2.getAge())
        .findFirst();
first.ifPresent(author -> System.out.println(author.getName()));
```
##### 3.2.5、注意事项
> 惰性求值
> - 如果没有终结操作，没有中间操作是不会得到执行的
> 
流是一次性的
> - 一旦一个流对象经过终结操作后，这个流就不能再被使用
> 
不会影响原数据
> - 正常情况下流的一系列操作时不会影响原集合中的数据的
> - 除非在留中对原对象进行了修改（因为地址的传递导致原对象数据得到了变更） - 一般不用去操心，很少遇到


### 四、Optional
> 我们在编写代码的时候出新最多的就是空指针异常。所以在很多情况下我们需要各种非空判断。
> 尤其是对象中的属性还是一个对象的情况下，这种判断会更多。
> 而过多的判断语句会让我们的代码显得非常臃肿。
> 所以在JDK8中引入了Optional，养成使用Optional的习惯后你可以写出更优雅的代码来避免空指针异常。
> 并且很多函数式编程相关的API中也都用到了OPtional，如果不会使用Optional也会对函数式编程的学习造成影响。

#### 4.1、使用
##### 4.1.1、创建对象
> Optional就好像是包装类，可以把我们的具体数据封装Optional对象内部，然后我们去使用OPtional中封装好的方法，操作封装进去的数据就可以非常优雅的避免空指针异常
> 
> 我们一般使用Optional的静态方法**ofNullable**来把数据封装成一个Optional对象。无论传入的参数是否为null都不会有问题。

```java
Author author = getAuthor();
Optional<Author> authorOptional = Optional.ofNullable(author);
```
> 如果你确定一个对象不是空的时候，则可以使用Optional的静态方法**of**来吧数据封装成Optional对象。
> - 其实ofNullable方法底层就是一个非空判断，返回值是根据传进来的对象使用三目运算符进行判断，若为空，则调用empty()，不为空则调用of()并把参数传进去

```java
Author author = getAuthor();
Optional<Author> authorOptional = Optional.of(author);
```
> 如果一个方法的返回值类型是Optional类型。而如果我们经判断发现某次计算得到的返回值为null，这个时候就需要把null封装成Optional对象返回。这时则可以使用Optional的静态方法**empty**来进行封装
> 例如：
> 模仿ofNullable方法封装一个Optional对象

```java
public Optional<author> getAuthorOptional(){
    Author author = new Author(xxxxxx);
    return author == null ? Optional.empty() : Optional.of(author);
}
```
##### 4.1.2、安全消费值
> 我们获取到一个Optional对象后肯定需要对其中的数据进行使用。这时我们就可以使用其** **方法来消费其中的数据。
> 这个方法会判断其内部封装的数据是否为空，不为空时才会执行具体的消费代码。这样使用起来就会更加安全了。
> 例如：
> 避免空指针异常的运用

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
authorOptional.ifPresent(authors -> System.out.println(authors.getName()));
```
##### 4.1.3、获取值
> 如果我们想获取Optional中的值自己进行处理，则可以使用**get**方法获取，但是不推荐。因为当Optional内部的数据为空的时候会出现异常

##### 4.1.4、安全获取值
> 如果我们期望安全的获取值。我们不推荐使用get方法，而是使用Optional提供的以下方法。

###### orElseGet
> 获取数据并且设置数据为空时的默认值。如果数据不为空就能获取到该数据。如果为空则根据你传入的参数来创建对象作为默认值返回
> - 使用lambda表达式创建一个对象，将这个对象作为默认值

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
Author author = authorOptional.orElseGet(() -> new Author(4L, "耶？", 24, "祖安人", null));
System.out.println(author.getName());
```
###### orElseThrow
> 获取数据，如果数据不为空就能获取到该数据。如果为空则根据你传入的参数来创建异常抛出

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
Author author = authorOptional.orElseThrow(() -> new RuntimeException("数据为空，请及时处理"));
System.out.println(author.getName());
```
##### 4.1.5、过滤
> 我们可以使用filter方法对数据进行过滤。如果原本是有数据的，但是不符合判断，也会变成一个无数据的optional对象。
> - 满足条件则使用原对象，不满足条件则返回一个空的OPtional对象

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
authorOptional.filter(author -> author.getAge()>18)
        .ifPresent(author -> System.out.println(author.getName()));
```
##### 4.1.6、判断
> 我们可以使用**isPresent**方法进行判断数据是否存在。
> 若为空，则返回false，若不为空，则返回true。
> 但是这种方式并不能体现Optional的好处，更推荐使用ifPresent方案

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
if (authorOptional.isPresent()) {
    System.out.println(authorOptional.get().getName());
    System.out.println(authorOptional.get().getAge());
}
```
##### 4.1.7、数据转换
> OPtional还提供了map可以让我们对数据进行转换，并且转换得到的数据还是被Optional包装好的，保证了我们的使用安全。
> 例如：
> 获取作家的书籍集合

```java
Optional<Author> authorOptional = Optional.ofNullable(getAuthor());
Optional<List<Book>> booksOptional = authorOptional.map(author -> author.getBooks());
booksOptional.ifPresent(books -> System.out.println(books));
```

### 五、函数式接口
> 只有一个抽象方法的接口我们称之为函数接口
> JDK的函数式接口都加上了@FunctionalInterface注解进行标识。但是无论是否加上该注解，只要接口中只有一个抽象方法， 都是函数式接口

#### 5.1、常见的函数式接口
##### 5.1.1、Consumer消费接口
> 根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中对传入的参数进行消费

##### 5.1.2、Function计算转换接口
> 根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中对传入的参数计算或转换，把结果返回

##### 5.1.3、Predicate判断接口
> 根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中对传入的参数进行条件判断，并返回判断的结果

##### 5.1.4、Supplier生产型接口
> 根据其中抽象方法的参数列表和返回值类型知道，我们可以在方法中创建对象，把创建好的对象返回

#### 5.2、常用的默认方法
##### and
> 我们在使用Predicate接口的时候可能需要进行条件判断的拼接，而and方法相当于是使用&&来拼接两个判断条件
> 例如：
> 打印作家中年龄大于17并且姓名的长度大于1的作家
> - 一般情况下不会这样用，因为简化成lambda表达式后会报错，因此常常直接把两个判断条件直接使用&&拼接在一起

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .filter(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getAge() > 17;
            }
        }.and(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getName().length() > 1;
            }
        }))
        .forEach(author -> System.out.println(author.getName() + "::" + author.getAge()));
```
##### or
> 我们在使用Predicate接口的时候，可能需要进行判断条件的拼接。而or方法相当于是使用||来拼接两个判断条件
> 例如：
> 打印作家中年龄大于17或者姓名长度小于2的作家

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .filter(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getAge() > 17;
            }
        }.or(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getName().length() < 2;
            }
        }))
        .forEach(author -> System.out.println(author.getName() + "::" + author.getAge()));
```
##### negate
> Predicate接口中的方法，negate方法相当于是在判断语句前面加了个!，表示取反
> 例如：
> 打印年龄不大于17的作家

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .filter(new Predicate<Author>() {
            @Override
            public boolean test(Author author) {
                return author.getAge() > 17;
            }
        }.negate())
        .forEach(author -> System.out.println(author.getName() + "::" + author.getAge()));
```

### 六、方法引用
> 我们在使用lambda表达式时，如果方法体中只有一个方法的调用时(包括构造方法)，我们可以用方法引用进一步简化代码

#### 6.1、推荐用法
> 我们在使用lambda时不需要考虑什么时候用方法引用，用哪种方法引用，方法的引用格式是什么。我们只需要在写完lambda方法发现方法体只有一行代码，并且是方法的调用时，使用快捷键尝试是否能够直接转换成方法引用即可。
> 当我们方法引用使用的多了，慢慢的也可以直接写出方法的引用。

#### 6.2、基本格式
> 类名或者对象名 :: 方法名

#### 6.3、语法详解
##### 6.3.1、引用类的静态方法
> 其实就是引用类的静态方法
> 格式
> 类名 :: 方法名
> 使用前提：
> 如果我们在重写方法的时候，**方法体中只有一行代码**，并且这行代码是**调用了某个静态方法**，并且我们要把重写的**抽象方法中所有的参数都按照顺序传入了这个静态方法中**，这个时候我们就可以引用类的静态方法

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .map(new Function<Author, Object>() {
            @Override
            public Object apply(Author author) {
                return author.getAge();
            }
        })
        .map(age -> String.valueOf(age));
```
> 改造后

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .map(author -> author.getAge())
        .map(String::valueOf);
```
##### 6.3.2、引用对象的实例方法
> **格式：**
> 对象名 :: 方法名
> **使用前提：**
> 如果我们在重写方法的时候，**方法体中只有一行代码**，并且这行代码是**调用了某个对象的成员方法**，并且我们把要重写的**抽象方法中所有的参数都按照顺序传入了这个成员方法中**，这个时候我们就可以引用对象的实例方法

```java
List<Author> authors = getAuthors();
StringBuilder stringBuilder = new StringBuilder();
authors
        .stream()
        .map(author -> author.getName())
        .forEach(name -> stringBuilder.append(name));
```
> 改造后

```java
List<Author> authors = getAuthors();
StringBuilder stringBuilder = new StringBuilder();
authors
        .stream()
        .map(author -> author.getName())
        .forEach(stringBuilder::append);
```
##### 6.3.3、引用类的实例方法
> **格式：**
> 类名 :: 方法名
> **使用前提：**
> 如果我们在重写方法的时候，**方法体中只有一行代码**，并且这行代码是**调用了第一个参数的成员方法**，并且我们把要**重写的抽象方法中剩余的所有参数都按照顺序传入了这个成员方法中**，这个时候我们就可以引用类的实例方法

```java
interface UseString {
    String use(String str, int start, int length);
}

public static String subAuthorName(String str,UseString useString) {
    int start = 0;
    int length = 1;
    return useString.use(str,start,length);
}

public static void main(String[] args) {
    subAuthorName( "三更草堂",new UseString(){
        @Override
        public String use(String str, int start, int length) {
            return str.substring(start, length);
        }
    });
}
```
> 改造后
> - 第一个参数作为调用方去调用自己的成员方法，第二三个参数作为第一个参数的成员方法的参数

```java
interface UseString {
    String use(String str, int start, int length);
}

public static String subAuthorName(String str,UseString useString) {
    int start = 0;
    int length = 1;
    return useString.use(str,start,length);
}

public static void main(String[] args) {
    subAuthorName( "三更草堂", String::substring);
}
```
##### 6.3.4、构造器引用
> 如果方法体中的一行代码时构造器的话可以使用构造器引用。
> **格式：**
> 类名 :: new
> **使用前提：**
> 如果我们在重写方法的时候，**方法体中只有一行代码**，并且这行代码是**调用了某个类的构造方法**，并且我们把**要重写的抽象方法中的所有参数都按照顺序传入了这个构造方法当中**，这个时候我们就可以引用构造器。

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .map(author -> author.getName())
        .map(name -> new StringBuilder(name))
        .map(stringBuilder -> stringBuilder.append("-瞎写的").toString())
        .forEach(s -> System.out.println(s));
```
> 改造后

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .map(author -> author.getName())
        .map(StringBuilder::new)
        .map(stringBuilder -> stringBuilder.append("-瞎写的").toString())
        .forEach(s -> System.out.println(s));
```
### 七、高级用法
#### 7.1、基本数据类型优化
> 我们在使用stream的方法时，很多地方都使用了泛型。所以设计到参数和返回值都是引用数据类型。
> 那么在操作这些数据的时候，它会对原数据先进行一个拆箱(拆成八大基本类型)再去进行运算，然后再进行装箱，包装成对应的包装类，那么在数据量大的时候，他会反复的进行一个自动的装箱和拆箱的动作，这是非常耗费性能的。
> 因此在实际开发过程中，若我们知道了将要操作的数据是什么类型，就可以使用以下方法进行提前转换
> 例如：mapToInt,mapToLong,mapToDouble,flatMapToInt,flatMapToDouble等等

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .map(Author::getAge)
        .map(age -> age + 10)
        .filter(age -> age > 18)
        .map(age -> age + 2)
        .forEach(System.out::println);
```
> 优化后

```java
List<Author> authors = getAuthors();
authors
        .stream()
        .mapToInt(Author::getAge)
        .map(age -> age + 10)
        .filter(age -> age > 18)
        .map(age -> age + 2)
        .forEach(System.out::println);
```
#### 7.2、并行流
> 当流中有大量元素的时候，我们可以使用并行流去提高操作的效率。其实并行流就是把任务分配给多个线程去完成。如果我们自己去用代码实现的话其实会非常的复杂，并且要求你对并发编程有足够的理解和认识。而如果我们使用Stream流的话，我们只需要修改一个方法的调用就可以使用并行流来帮助我们实现，从而提高效率
> 在拿到stream流后调用parallel()方法，或直接在获取stream流的时候调用parallelStream()方法直接拿到并行流对象
> - 并行并不代表一定快

```java
List<Author> authors = getAuthors();
authors.stream().parallel();

authors.parallelStream();
```
