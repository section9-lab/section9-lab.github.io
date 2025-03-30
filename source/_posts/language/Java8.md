---
title: Java8
date: 2022-01-12
category:
  - Language
tag:
  - java
  - api
sticky: false
---

# Java8


## 一、四大函数式接口
### 1、Function 函数式接口
```java
package main;
import java.sql.SQLOutput;
import java.util.function.Function;
public class FunctionDemo {
    public static void main(String[] args) {
//        Function<String,String> function = new Function<String, String>() {
//            @Override
//            public String apply(String s) {
//                return s;
//            }
//        };
        Function<String,String> function = (str)->{return str;};
        System.out.println(function.apply("dsaf"));
    }
}
```

### 2、Predicate 断定型接口
```java
package fourinterface;
import java.util.function.Predicate;
/*
* 断定型接口:有一个输入参数,返回值只能是 布尔值
* */
public class PredicateDemo {
    public static void main(String[] args) {
        //判断字符串是否为空
//        Predicate<String> predicate = new Predicate<String>() {
//            @Override
//            public boolean test(String s) {
//                return s.isEmpty();
//            }
//        };
        Predicate<String> predicate = (str)->{return  str.isEmpty();};
        System.out.println(predicate.test(" "));
    }
}
```
### 3、Consumer 消费性接口
```java
package fourinterface;
import java.util.function.Consumer;
/*
* Consumer 消费型接口: 只有输入,没有返回值
* */
public class ConsumerDemo {
    public static void main(String[] args) {
//        Consumer<String> consumer = new Consumer<String>() {
//            @Override
//            public void accept(String s) {
//                System.out.println(s);
//            }
//        };
        Consumer<String> consumer = (str)->{
            System.out.println(str);
        };
        consumer.accept("hello");
    }
}
```
### 4、Supplier 供给型接口
```java
package fourinterface;
import java.util.function.Supplier;
/*
* Supplier 供给型接口 :没有参数 , 只有返回值
* */
public class SupplierDemo {
    public static void main(String[] args) {
//        Supplier supplier =new Supplier() {
//            @Override
//            public Object get() {
//                return 1024;
//            }
//        };
        Supplier supplier = ()->{return 1024;};
        System.out.println(supplier.get());
    }
}
```
## 二、Stream
### 2.1 预定义数据
```java
@Data
@AllArgsConstructor
public class Student {
    private String id;
    private String name;
    private LocalDate birthday;
    private int age;
    private double score;
}

final List<Student> students = Lists.newArrayList();
students.add(new Student("1", "张三", LocalDate.of(2009, Month.JANUARY, 1), 12, 12.123));
students.add(new Student("2", "李四", LocalDate.of(2010, Month.FEBRUARY, 2), 11, 22.123));
students.add(new Student("3", "王五", LocalDate.of(2011, Month.MARCH, 3), 10, 32.123));
```

### 2.2.1 元素数量：counting
```java
students.stream().collect(Collectors.counting())
```

### 2.2.2 平均值：averagingDouble、averagingInt、averagingLong
```java
students.stream().collect(Collectors.averagingDouble(Student::getScore))

students.stream().collect(Collectors.averagingInt(s -> (int)s.getScore()))
students.stream().collect(Collectors.averagingLong(s -> (long)s.getScore()))

students.stream().collect(Collectors.averagingInt(Student::getAge))
students.stream().collect(Collectors.averagingDouble(Student::getAge))
students.stream().collect(Collectors.averagingLong(Student::getAge))
```

### 2.2.3 和：summingDouble、summingInt、summingLong
```java
students.stream().collect(Collectors.summingInt(s -> (int)s.getScore()))
students.stream().collect(Collectors.summingDouble(Student::getScore))
students.stream().collect(Collectors.summingLong(s -> (long)s.getScore()))

students.stream().collect(Collectors.summingInt(Student::getAge))
students.stream().collect(Collectors.summingDouble(Student::getAge))
students.stream().collect(Collectors.summingLong(Student::getAge))
```

### 2.2.4 最大值/最小值元素：maxBy、minBy
```java
// Optional[Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)]，注意返回类型是Optional
students.stream().collect(Collectors.minBy(Comparator.comparing(Student::getAge)))
// Optional[Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)]，注意返回类型是Optional
students.stream().collect(Collectors.maxBy(Comparator.comparing(Student::getAge)))
```

### 2.2.5 统计结果：summarizingDouble、summarizingInt、summarizingLong
```java
// IntSummaryStatistics{count=3, sum=66, min=12, average=22.000000, max=32}
students.stream().collect(Collectors.summarizingInt(s -> (int) s.getScore()))
// DoubleSummaryStatistics{count=3, sum=66.369000, min=12.123000, average=22.123000, max=32.123000}
students.stream().collect(Collectors.summarizingDouble(Student::getScore))
// LongSummaryStatistics{count=3, sum=66, min=12, average=22.000000, max=32}
students.stream().collect(Collectors.summarizingLong(s -> (long) s.getScore()))

// IntSummaryStatistics{count=3, sum=33, min=10, average=11.000000, max=12}
students.stream().collect(Collectors.summarizingInt(Student::getAge))
// DoubleSummaryStatistics{count=3, sum=33.000000, min=10.000000, average=11.000000, max=12.000000}
students.stream().collect(Collectors.summarizingDouble(Student::getAge))
// LongSummaryStatistics{count=3, sum=33, min=10, average=11.000000, max=12}
students.stream().collect(Collectors.summarizingLong(Student::getAge))
```

### 2.3.1 聚合元素：toList、toSet、toCollection
```java
// List: [1, 2, 3]
final List<String> idList = students.stream().map(Student::getId).collect(Collectors.toList());
// Set: [1, 2, 3]
final Set<String> idSet = students.stream().map(Student::getId).collect(Collectors.toSet());
// TreeSet: [1, 2, 3]
final Collection<String> idTreeSet = students.stream().map(Student::getId).collect(Collectors.toCollection(TreeSet::new));
```

### 2.3.2 聚合元素：toMap、toConcurrentMap
```java
// {1=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123), 2=Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), 3=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)}
final Map<String, Student> map11 = students.stream()
    .collect(Collectors.toMap(Student::getId, Function.identity()));
 
 // {1=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123), 2=Student(id=2, name=李四birthday=2010-02-02, age=11, score=22.123), 3=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)}
final Map<String, Student> map2 = students.stream()
    .collect(Collectors.toMap(Student::getId, Function.identity(), (x, y) -> x));

// {1=张三, 2=李四, 3=王五}
final Map<String, String> map3 = students.stream()
    .collect(Collectors.toMap(Student::getId, Student::getName, (x, y) -> x));

// {10=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123), 11=Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), 12=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)}
final Map<Integer, Student> map5 = students.stream()
    .collect(Collectors.toMap(Student::getAge, Function.identity(), BinaryOperator.maxBy(Comparator.comparing(Student::getScore))));
```

### 2.3.3 分组：groupingBy、groupingByConcurrent
```java
// List: {10=[Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)], 11=[Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123)], 12=[Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)]}
final Map<Integer, List<Student>> map1 = students.stream().collect(Collectors.groupingBy(Student::getAge));
// Set: {10=[Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)], 11=[Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123)], 12=[Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)]}
final Map<Integer, Set<Student>> map12 = students.stream().collect(Collectors.groupingBy(Student::getAge, Collectors.toSet()));

// {1=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123), 2=Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), 3=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)}
final Map<String, Student> map3 = students.stream()
    .collect(Collectors.groupingBy(Student::getId, Collectors.collectingAndThen(Collectors.toList(), list -> list.get(0))));

// {1=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123), 2=Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), 3=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)}
final Map<String, Student> map2 = students.stream()
    .collect(Collectors.toMap(Student::getId, Function.identity(), (x, y) -> x));
```


### 2.3.4 分组：partitioningBy
```java
// List: {false=[Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)], true=[Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)]}
final Map<Boolean, List<Student>> map6 = students.stream().collect(Collectors.partitioningBy(s -> s.getAge() > 11));
// Set: {false=[Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123), Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123)], true=[Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123)]}
final Map<Boolean, Set<Student>> map7 = students.stream().collect(Collectors.partitioningBy(s -> s.getAge() > 11, Collectors.toSet()));
```

### 2.3.5 链接数据：joining
```java
// javagosql
Stream.of("java", "go", "sql").collect(Collectors.joining());
// java, go, sql
Stream.of("java", "go", "sql").collect(Collectors.joining(", "));
// 【java, go, sql】
Stream.of("java", "go", "sql").collect(Collectors.joining(", ", "【", "】"));
```

### 2.3.6 操作链：collectingAndThen
```java
// {1=Student(id=1, name=张三, birthday=2009-01-01, age=12, score=12.123), 2=Student(id=2, name=李四, birthday=2010-02-02, age=11, score=22.123), 3=Student(id=3, name=王五, birthday=2011-03-03, age=10, score=32.123)}
final Map<String, Student> map3 = students.stream()
    .collect(Collectors.groupingBy(Student::getId, Collectors.collectingAndThen(Collectors.toList(), list -> list.get(0))));

// []，结果为空，是因为例子中所有人的年龄都是对的
students.stream()
        .collect(
                Collectors.collectingAndThen(Collectors.toList(), (
                        list -> list.stream()
                                .filter(s -> (LocalDate.now().getYear() - s.getBirthday().getYear()) != s.getAge())
                                .collect(Collectors.toList()))
                )
        );
//更简洁
students.stream()
        .filter(s -> (LocalDate.now().getYear() - s.getBirthday().getYear()) != s.getAge())
        .collect(Collectors.toList());
```


### 2.3.7 操作后聚合：mapping
```java
// [张三, 李四, 王五]
students.stream()
        .collect(Collectors.mapping(Student::getName, Collectors.toList()));

// [张三, 李四, 王五]
students.stream()
        .map(Student::getName)
        .collect(Collectors.toList());
```

### 2.3.8 聚合后操作：reducing
```java
// Optional[66.369]，注意返回类型是Optional
students.stream()
        .map(Student::getScore)
        .collect(Collectors.reducing(Double::sum));
// 66.369
students.stream()
        .map(Student::getScore)
        .collect(Collectors.reducing(0.0, Double::sum));
// 66.369
students.stream()
        .collect(Collectors.reducing(0.0, Student::getScore, Double::sum));
// Optional[66.369]，注意返回类型是Optional
students.stream().map(Student::getScore).reduce(Double::sum);
// 66.369
students.stream().map(Student::getScore).reduce(0.0, Double::sum);
```

## 三、Optional
主要作用是消除 NullPointException

::: code-tabs#java

@tab Java7

```java
List<String> list = getList();
List<String> listOpt = list != null ? list : new ArrayList<>();
```

@tab Java8

```java
List<String> listOpt = Optional.ofNullable(getList())
        .orElse(new ArrayList<>());
```

:::

**举例:**
假设，我们有一个User类，内部有个Address类，在内部有个street属性，我们现在想要获取一个User对象的street值。如果是以前，我们需要各种判断是否是null，代码会写成这样：

::: code-tabs#java

@tab Java7

```java
User user = getUser();
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        String street = address.getStreet();
        if (street != null) {
            return street;
        }
    }
}
return "not specified";
```

@tab Java8

```java
String result = Optional.ofNullable(getUser())
        .map(User::getAddress)
        .map(Address::getStreet)
        .orElse("not specified");
```

:::

## 四、Time
在旧版的 Java 中，日期时间 API 存在诸多问题：

- 非线程安全,所有的日期类都是可变的。

- 设计很差，java.util.Date同时包含日期和时间，而java.sql.Date仅包含日期，用于格式化和解析的类在java.text包中定义。

- 时区处理麻烦，因此Java引入了java.util.Calendar和java.util.TimeZone类，但他们同样存在上述所有的问题。

> Java 8 在 java.time 包下提供了很多新的 API。
### 4.1 LocalDateTime
```java
// 获取当前的日期时间
LocalDateTime currentTime = LocalDateTime.now();
System.out.println("当前时间: " + currentTime);
        
LocalDate date1 = currentTime.toLocalDate();
System.out.println("date1: " + date1);
        
Month month = currentTime.getMonth();
int day = currentTime.getDayOfMonth();
int seconds = currentTime.getSecond();
        
System.out.println("月: " + month +", 日: " + day +", 秒: " seconds);
        
LocalDateTime date2 = currentTime.withDayOfMonth(10).withYe(2012);
System.out.println("date2: " + date2);
        
// 12 december 2014
LocalDate date3 = LocalDate.of(2014, Month.DECEMBER, 12);
System.out.println("date3: " + date3);
        
// 22 小时 15 分钟
LocalTime date4 = LocalTime.of(22, 15);
System.out.println("date4: " + date4);
        
// 解析字符串
LocalTime date5 = LocalTime.parse("20:15:30");
System.out.println("date5: " + date5);
```
输出：
```txt
当前时间: 2016-04-15T16:55:48.668
date1: 2016-04-15
月: APRIL, 日: 15, 秒: 48
date2: 2012-04-10T16:55:48.668
date3: 2014-12-12
date4: 22:15
date5: 20:15:30
```
### 4.2 ZonedDateTime
```java
// 获取当前时间日期
ZonedDateTime date1 = ZonedDateTime.parse("2015-12-03T10:15:30+05:30[Asia/Shanghai]");
System.out.println("date1: " + date1);
        
ZoneId id = ZoneId.of("Europe/Paris");
System.out.println("ZoneId: " + id);
        
ZoneId currentZone = ZoneId.systemDefault();
System.out.println("当期时区: " + currentZone);
```
输出：
```txt
date1: 2015-12-03T10:15:30+08:00[Asia/Shanghai]
ZoneId: Europe/Paris
当期时区: Asia/Shanghai
```

[参考]https://xie.infoq.cn/article/df361a1280773d32208550293
