



#   joda-time笔记

## 1、为什么要使用joda-time

如果你要表示2018年1月1号12点12分12秒，你要来怎么表示呢？jdk的Date？自从jdk1.1依赖，他的java doc就写的很清楚date自从1.1之后可以这样做，但是该方法后面被废弃了，而且不支持国际化，推荐使用java.util.Calendar，那么我们来看看Calendar怎么样来表示

```java
Calendar calendar = Calendar.getInstance();
calendar.set(2018, Calendar.JANUARY, 1, 12, 12, 12);
```

joda-time 

```java
DateTime dateTime = new DateTime(2018, 1, 1, 12, 12, 12, 0);
```

这样看来代码也都差不多，别急

我现在有这样一个需求，我希望在目前的日期上加上100天，想看下100天之后是那一天

```java
Calendar calendar = Calendar.getInstance();
calendar.set(2018, Calendar.JANUARY, 1, 12, 12, 12);
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SS E");
calendar.add(Calendar.DAY_OF_MONTH, 100);
System.out.println(sdf.format(calendar.getTime()));
```

joda-time 

```java
DateTime dateTime = new DateTime(2018, 1, 1, 12, 12, 12, 0);
String s = dateTime.plusDays(100).toString("yyyy-MM-dd HH:mm:ss E");
System.out.println(s);
```

看出差别来了吗？

jdk的代码量很大，而joda-time的代码量很少

## 2、特性

### 2.1不可变行

官网介绍，joda-time 90%的代码都是对用户不可见的，也就是封装的很好，那么最常用的五个日期时间类是：

* Instant : 不可变的类，用来表示时间轴上一个瞬时的点
* DateTime - 不可变的类，用来替换JDK的Calendar类
* LocalDate - 不可变的类，表示一个本地的日期，而不包含时间部分（没有时区信息）
* LocalTime - 不可变的类，表示一个本地的时间，而不包含日期部分（没有时区信息）
* LocalDateTime - 不可变的类，表示一个本地的日期－时间（没有时区信息）

上面这些暴露出来的对象都是不可变的，抽取其中一个

![](image\5.png)

final的，有没有和string类相似。

### 2.2 丰富的年表

![](image\6.png)

目前2.9版本支持8种

年表的概念举例 ISO：

比如2017-01-01号 星期天这个日期，你不知道计做2017年的第0周还是第一周，而且这个周日到底是这个礼拜的最后一天还是下个礼拜的第一天，总之，各个国家和地区的叫法都不一样

后来有人站出来了，那就是国际标准组织（ISO），他定义了ISO-8601规则，专门来说明这些的，比如

* 每年有52周或者53周
* 周一至周日为一个完整周
* 每周的周一是该周的第1天，周日是该周的第7天
* 每年的第一周 为 每年的第一个周四所在的周。比如 2017年1月5日为当年的第一个周四，那么 2017-01-02 至 2017-01-08 为2017年第一周
* 每年的最后一周为当年最后一个周四所在的周
* 周年，当前周所在的年份为周年。比如 2017年1月1日的周年为2016年

 这个定义很完美，但是jdk1.7 不支持，但是joda-time支持

### 2.3 时区

时区是指一个相对于英国格林威治的地理位置，用于计算时间，`DateTimeZone` 是 Joda 库用于封装位置概念的类，joda-time默认情况下以当前操作系统的时区为准进行计算的



## 3、joda-time  ReadableDateTime基本类层结构



```java
//常用类继承关系图
ReadableDateTime (org.joda.time)
    AbstractDateTime (org.joda.time.base)
        BaseDateTime (org.joda.time.base)
            DateTime (org.joda.time)
            DateMidnight (org.joda.time)
            MutableDateTime (org.joda.time)
    ReadWritableDateTime (org.joda.time)
        MutableDateTime (org.joda.time)
    BaseDateTime (org.joda.time.base)
        DateTime (org.joda.time)
        DateMidnight (org.joda.time)
        MutableDateTime (org.joda.time)
    DateTime (org.joda.time)
    DateMidnight (org.joda.time)
Instant (org.joda.time)
AbstractInstant (org.joda.time.base)
    AbstractDateTime (org.joda.time.base)
        BaseDateTime (org.joda.time.base)
            DateTime (org.joda.time)
            DateMidnight (org.joda.time)
            MutableDateTime (org.joda.time)
    Instant (org.joda.time)
ReadWritableInstant (org.joda.time)
    ReadWritableDateTime (org.joda.time)
        MutableDateTime (org.joda.time)
```

### 2.1  顶层接口ReadableInstant

这个接口是瞬间性质时间的一个抽象，并且是不可变的，也就表示时间上的某一个不可修改的瞬间

其中最常用和重要的两个类

* dateTime -- 常用类，毫秒级日期时间的不可变类，注意和`DateTimeZone` 的关系，不指定`DateTimeZone` 的情况下，默认使用操作系统所在的时区
* DateMidnight（已经废弃了，通过dateTime.withTimeAsStartOfDay方法替换掉了）-- 这个类封装某个时区（通常为默认时区）在特定年/月/日的午夜时分的时刻



## 4、使用dateTime

dateTime : 看官方文档上介绍，DateTime是线程安全且不可变的，前提是Chronology也是如此。 提供的所有标准年表类都是线程安全且不可变的。![image\2.png](image\2.png)

### (1) jdk日期转joda-time  dateTime

```java


//jdk 日期转joda time
DateTime dateTime = new DateTime(454554225L);
DateTime dateTime = new DateTime(new Date());
DateTime dateTime1 = new DateTime(Calendar.getInstance());
DateTime dateTime2 = new DateTime("2018-12-12T15:59:19.709+08:00");

```

![image/1.png](image\1.png)

这里可以是Long类型，string类型，Calendar类型和时间类型



String类型的请参照ISODateTimeFormat中的规范填写，规范如下实例：

```
* Extended      Basic       Fields
* 2005-03-25    20050325    year/monthOfYear/dayOfMonth
* 2005-03       2005-03     year/monthOfYear
* 2005--25      2005--25    year/dayOfMonth *
* 2005          2005        year
* --03-25       --0325      monthOfYear/dayOfMonth
* --03          --03        monthOfYear
* ---03         ---03       dayOfMonth
* 2005-084      2005084     year/dayOfYear
* -084          -084        dayOfYear
* 2005-W12-5    2005W125    weekyear/weekOfWeekyear/dayOfWeek
* 2005-W-5      2005W-5     weekyear/dayOfWeek *
* 2005-W12      2005W12     weekyear/weekOfWeekyear
* -W12-5        -W125       weekOfWeekyear/dayOfWeek
* -W12          -W12        weekOfWeekyear
* -W-5          -W-5        dayOfWeek
* 10:20:30.040  102030.040  hour/minute/second/milli
* 10:20:30      102030      hour/minute/second
* 10:20         1020        hour/minute
* 10            10          hour
* -20:30.040    -2030.040   minute/second/milli
* -20:30        -2030       minute/second
* -20           -20         minute
* --30.040      --30.040    second/milli
* --30          --30        second
* ---.040       ---.040     milli *
* 10-30.040     10-30.040   hour/second/milli *
* 10:20-.040    1020-.040   hour/minute/milli *
* 10-30         10-30       hour/second *
* 10--.040      10--.040    hour/milli *
* -20-.040      -20-.040    minute/milli *
*   plus datetime formats like {date}T{time}
```



### (2) joda-time  dateTime转jdk时间

```java
DateTime dateTime = new DateTime();
//转date
java.util.Date date = dateTime.toDate();
//转calendar
Calendar calendar = dateTime.toCalendar(Locale.CHINA);
//还可以输出阳历
GregorianCalendar gregorianCalendar = dateTime.toGregorianCalendar();
```

可以转成jdk的java.util.Date，Calendar和Calendar子类阳历GregorianCalendar



### (3) joda-time dateTime 访问年月日时分秒毫秒以及星期

```java
int year = dateTime.getYear();
int month = dateTime.getMonthOfYear();
int day = dateTime.getDayOfMonth();
int hour = dateTime.getHourOfDay();
int Minute = dateTime.getMinuteOfHour();
int Second = dateTime.getSecondOfMinute();
int millisOfSecond = dateTime.getMillisOfSecond();
int dayOfWeek = dateTime.getDayOfWeek();
//输出 2018-9-4 15:48:10 442  礼拜二  精确到毫秒
System.out.println(year+"-"+ month + "-" + day +" "+hour+":"+Minute+":"+Second+" "+millisOfSecond+"   礼拜"+ dayOfWeek);
```

刚好返回的**月份不用加一**，而jdk的月份需要加一，才能得到实际的月份，这里要注意下

还可以访问国际化的中文字段和阿拉伯数组表示字段

```java
String asText = dateTime.dayOfWeek().getAsText();
int i = dateTime.dayOfWeek().get();
//输出 2---星期二
System.out.println(i+"---"+asText );
```

### (4)  joda-time dateTime 计算

dateTime 可以进行简单的日期计算，不过得到的是新的实例

```java
DateTime with = dateTime.withDate(2018, 12, 12);
//输出 2018-12-12T15:48:10.442+08:00 ，可见，如果赋值年月日，那么时分秒还显示的是当前时间的时分秒
System.out.println(with);

//这里可以对日期进行加减操作
DateTime plusWith = with.plusHours(-2);
//false
System.out.println(with==plusWith);
//输出 2018-12-12T13:48:10.442+08:00
System.out.println(plusWith);
```

###  (5) dateTime 常用API

```java
DateTime withMillis(long newMillis）  -----   以毫秒为值返回dateTime的副本

DateTime withTimeAtStartOfDay()    -------- 返回这个时间点的开始时间，也就是0点，用来替换DateMidnight对象的

DateTime withFields(ReadablePartial partial)   --替换成自己想要的日期数据，并返回副本

DateTime withDate(int year, int monthOfYear, int dayOfMonth)  ---替换年月日，返回自己想要的年月日的副本，但时分秒毫秒不变，和上面的withField相似，但是一次可以替换年月日
                    
DateTime withTime(int hourOfDay, int minuteOfHour, int secondOfMinute, int millisOfSecond)   有替换年月日，当然也有替换时分秒，毫秒的

DateTime withFieldAdded(DurationFieldType fieldType, int amount)  ---对于某一属性，比如年加上多少，比如   dateTime.withFieldAdded(DurationFieldType.months(), 5) 加五年

DateTime plus(long duration) --- 加一毫秒，并且返回副本，底层是以Duration实现的，这个类以毫秒为单位，后面介绍

DateTime plus(ReadableDuration duration)   --- 加多少毫秒

DateTime plusYears(int years)   -- 加几年

DateTime plusMonths(int months)  --  加几月

DateTime plusWeeks(int weeks)  --   加几周

DateTime plusDays(int days)  -- 加几天
。。。。。。。plus太多

DateTime minus(int weeks) --- 减去一秒

DateTime minusYears(int years)   -- 减去一年

DateTime.Propey withMaximumValue()  简单理解--返回最大时间
                    
DateTime.Propey withMinimumValue()  简单理解--返回最小时间
 
```

来看几个例子加深下

#### 5.1 计算 今天晚上十二点 45天后的一月后的当周的最后一天

```java
DateTime dateTime = new DateTime();
DateTime copyDateTime = dateTime.withTimeAtStartOfDay();
DateTime fuday =copyDateTime.plusDays(45).plusMonths(1).dayOfWeek().withMaximumValue();
//2018-09-05 00:00:00 星期三
System.out.println(copyDateTime.toString("yyyy-MM-dd HH:mm:ss E"));
//2018-11-25 00:00:00 星期日
System.out.println(fuday.toString("yyyy-MM-dd HH:mm:ss E"));
```

#### 5.2  前7个月的最后一天

```java
DateTime dft = new DateTime();
DateTime dt1 = dft.withTimeAtStartOfDay().minusMonths(7).dayOfMonth().withMaximumValue();
//2018-09-05 15:13:39 星期三
System.out.println(dft.toString("yyyy-MM-dd HH:mm:ss E"));
//2018-02-28 00:00:00 星期三
System.out.println(dt1.toString("yyyy-MM-dd HH:mm:ss E"));
```



## 5、使用DateTimeFormat

### (1) DateTimeFormat介绍

格式化的创建DateTimeFormatter实例的工厂，也就是说DateTimeFormat只是生产DateTimeFormatter实例的，而真正格式化由DateTimeFormatter来执行。并且它是线程安全且不可变的，它返回的格式化程序也是如此。

除了这个类，还有两个工厂类来创建DateTimeFormatter，那么一共三个，分别是

* DateTimeFormat
* ISODateTimeFormat
* DateTimeFormatterBuilder

DateTimeFormat提供了两种不同类型的工厂，分别是

* forPattern(String Pattern)  -- 提供基于字符串格式化的DateTimeFormatter，该方式基本满足JDK日期格式化
* forStyle(String Style) -- 提供基于两个字符样式的DateTimeFormatter，表示short（短），medium（中），long（长）和full（全部）。

来我们举个例子看下上面这两句话是个什么意思

### (2) 使用 

***forPattern(String Pattern) 方式的：***

```java
DateTime dt = new DateTime();
DateTimeFormatter fmt = DateTimeFormat.forPattern("yyyy-MM-dd HH:mm:ss");
String print = fmt.print(dt);
//2018-09-04 16:58:42
System.out.println(print);
```

模式语法主要与java.text.SimpleDateFormat兼容。时区名称无法解析，但是支持更多符号。

这是目前支持的列表：

```jade
* Symbol  Meaning                      Presentation  Examples
* ------  -------                      ------------  -------
* G       era                          text          AD
* C       century of era (&gt;=0)         number        20
* Y       year of era (&gt;=0)            year          1996
*
* x       weekyear                     year          1996
* w       week of weekyear             number        27
* e       day of week                  number        2
* E       day of week                  text          Tuesday; Tue
*
* y       year                         year          1996
* D       day of year                  number        189
* M       month of year                month         July; Jul; 07
* d       day of month                 number        10
*
* a       halfday of day               text          PM
* K       hour of halfday (0~11)       number        0
* h       clockhour of halfday (1~12)  number        12
*
* H       hour of day (0~23)           number        0
* k       clockhour of day (1~24)      number        24
* m       minute of hour               number        30
* s       second of minute             number        55
* S       fraction of second           millis        978
*
* z       time zone                    text          Pacific Standard Time; PST
* Z       time zone offset/id          zone          -0800; -08:00; America/Los_Angeles
*
* '       escape for text              delimiter
* ''      single quote                 literal   
```

简单看下这个方法的底层实现

![image\3.png](image\3.png)

底层是用一个ConcurrentHashMap来缓存的，并且可以存储500个这样的，远远足够了，项目中用的也就那么几十个

***forStyle(String Style) 方式的：***

这种方式就比较复杂简单点，但不支持jdk那样的方式

第一个字符是日期样式，第二个字符是时间样式。 为短格式指定'S'字符，为中等指定'M'，为长指定'L'，为完整指定'F'。通过指定样式字符' - '可以省略日期或时间。

举个例子先看下就懂了

```java
//输出 18-9-4 下午6:02
System.out.println(DateTimeFormat.forStyle("SS").withLocale(Locale.CHINA).print(dt));
//输出 2018-9-4 18:02:06
System.out.println(DateTimeFormat.forStyle("MM").withLocale(Locale.CHINA).print(dt));
//输出 12018年9月4日 下午06时02分06秒
System.out.println(DateTimeFormat.forStyle("LL").withLocale(Locale.CHINA).print(dt));
//输出 2018年9月4日 星期二 下午06时02分06秒 CST
System.out.println(DateTimeFormat.forStyle("FF").withLocale(Locale.CHINA).print(dt));
//输出 2018-9-4
System.out.println(DateTimeFormat.forStyle("M-").withLocale(Locale.CHINA).print(dt));
```

而看看这个方法的底层实现

![](image\4.png)

底层是用AtomicReferenceArray来做缓存的，安全和复用比较好



## 6、DateTimeFormatterBuilder第二个工厂（上面提到了）



### (1) 介绍

通过方法调用创建DateTimeFormatter的***复杂***实例的工厂。当然日期格式化由DateTimeFormatter执行。

### (2) 使用

```
DateTimeFormatter dateTimeFormatter = new DateTimeFormatterBuilder()
        .appendTwoDigitYear(1975)
        //打印一个特定的字节
        .appendLiteral('-')
        //打印两位月份
        .appendMonthOfYear(2)
        //打印一个特定的字节
        .appendLiteral("-")
        //打印两位日期
        .appendDayOfMonth(2)
        .toFormatter();
//打印 1945-11-24T00:00:00.000+08:00

System.out.println(dateTimeFormatter.parseDateTime("45-11-24"));
//打印 2008-11-24T00:00:00.000+08:00
System.out.println(dateTimeFormatter.parseDateTime("08-11-24"));
```

.appendTwoDigitYear(1975)这个方法是只对打印两位数年份的计算，对赋值年份前（1975-50）=1925

年份后加上49（1975-49）=2024，那么25~00落在了1925-2000年份内，上面的45就是1945，08落在了2000~2024,那么就会打印2008,比较有趣吧



但可惜的是DateTimeFormatterBuilder 这个类不是线程安全的，而且有两个成员变量，但是这个类构建出来的DateTimeFormatter类是线程安全的，所以一般就不要用这个类来格式化日期了，用上面那个工厂方法来搞



至于其他的就不研究了



##  5、最后一个工厂ISODateTimeFormat

它规定了ISO标准的年表表示办法,不做研究



## 6、joda-time  ReadablePartial基本类层结构

ReadablePartial 接口主要来表示不可变的部分时间，比如只有时间，或者只有年月日，也就是表示的不是一个完成的时间，可能关注的是某一天，某一天的某个小时，某周的某天，这个接口类图大致如下

```java
ReadablePartial   
    LocalDate (org.joda.time)
    Partial (org.joda.time)
    YearMonthDay (org.joda.time)
    BasePartial (org.joda.time.base)
        YearMonthDay (org.joda.time)
        TimeOfDay (org.joda.time)
        YearMonth (org.joda.time)
        MonthDay (org.joda.time)
    AbstractPartial (org.joda.time.base)
        Partial (org.joda.time)
        BasePartial (org.joda.time.base)
        BaseLocal (org.joda.time.base)
    TimeOfDay (org.joda.time)
    LocalTime (org.joda.time)
    YearMonth (org.joda.time)
    MonthDay (org.joda.time)
    LocalDateTime (org.joda.time)
```

这这些接口或者类中，最重要的两个类

* LocalDate  -----  该类封装了一个年/月/日的组合
* LocalTime  ----- 这个类封装一天中的某个时间

### 6.1 LocalDate

#### 6.1.1 构造函数

```java
LocalDate localDate1 = new LocalDate(new Date());
LocalDate localDate2 = new LocalDate("2018-08-04");
LocalDate localDate3 = new LocalDate(Calendar.getInstance());
LocalDate localDate4 = new LocalDate(34329000);
```

构造函数基本和DateTime差不多

#### 6.1.2 方法

获取当前时间（静态方法）

```
public static LocalDate now()   --  获取当前时间（可以参数传入年表和时区）
```

字符串转成LocaDate（静态方法）

```java
LocalDate.parse("2018-08-04");
LocalDate.parse("2018-08-04", DateTimeFormat.forPattern("YYYY-mm-ss")); //不符合YYYY-mm-ss会报错
```

LocalDate转jdk日期

```
LocalDate.toDate()
```



日期转LocalDate

```java
LocalDate.fromCalendarFields(Calendar.getInstance());
LocalDate.fromDateFields(new Date())
```



常用方法

```java
LocalDate withField(DateTimeFieldType fieldType, int value)   --- 替换成自己想要的值，对于年月日

LocalDate withYearOfEra(int value)    替换年份
。。。。太多了

LocalDate plus(ReadablePeriod period)   --- 加多少  parse.plus(Years.TWO) 加两年

LocalDate plusYears(int years)  --- 加几年，不过底层不是plus(ReadablePeriod period)
    
LocalDate plusWeeks(int weeks)   --- 加几周  
 。。。。。太多了
 
LocalDate minus(ReadablePeriod period) ---- 减多少

int getWeekOfWeekyear()   -- 当前年的第几周，这个函数比较有用

LocalDate.dayOfMonth().withMaximumValue()   -- 当前月份最大的日期

```

### 6.2 LocalTime

和LocalDate api基本相似，不做分析





## 7. 练习

###  7.1 计算10 月中第一个星期一之后的第一个星期二

```
LocalDate  localDate= LocalDate.now();
LocalDate localDate1 = localDate
        .withMonthOfYear(10) //替换月份到10月
        .dayOfMonth().withMinimumValue() //找到当前月的最小日期
        .plusWeeks(1) //不管怎么样先加上一周
        .dayOfWeek()
        .setCopy(2); //最后找到礼拜二
//2018-10-09
System.out.println(localDate1);
```





```
LocalDate localDate1 = localDate
        .withMonthOfYear(10) //替换月份到10月
        .dayOfMonth().withMinimumValue() //找到当前月的最小日期
        .dayOfWeek().setCopy(1) //先找到礼拜一
        .plusWeeks(1) //加上一周
        .dayOfWeek()
        .setCopy(2); //最后找到礼拜二
//2018-10-09
System.out.println(localDate1);
```

答案都是对的，是不是很简单，设想一下，如果是jdk的操作那得多复杂







## 8、结语

其实这些东西只是分析了joda-time的皮毛和使用，底层没有过多探索，希望在以后使用过程中，遇到坑，慢慢填平

最后附上一个封装的API

```java
package com.oasis.captain.utils.date;

import org.apache.commons.lang3.StringUtils;
import org.joda.time.*;

import java.util.Date;

/**
 * joda-time时间工具类（基于 joda-time 2.9，版本可升级）
 *
 * @author zhangbo
 * @date 2018年9月4日21:14:03
 */
public abstract class DateUtil {

    /**
     * 默认转换格式
     */
    public static final String DEFAULT_FORMAT = "yyyy-MM-dd HH:mm:ss";

    /**
     * 默认转换格式
     */
    public static final String Y4M2 = "yyyyMM";


    /**
     * 避免时区的影响，此处初始化中国时区 --- 东八区
     *
     */
    static {
        DateTimeZone.setDefault(DateTimeZone.forID("Asia/Shanghai"));
    }

    /**
     * 获取当前时间毫秒数
     *
     * @return
     */
    public static Long getCurrentTimeMillis() {
        return System.currentTimeMillis();
    }

    /**
     * 获取当前时间
     *
     * @return
     */
    public static Date getCurrentDateTime() {
        return new Date();
    }

    /**
     * 获取当前日期
     *
     * @return
     */
    public static Date getCurrentDate() {
        return new LocalDate().toDate();
    }

    /**
     * 获取当前时间
     *
     * @return
     */
    public static Date getCurrentTime() {
        return new LocalTime().toDateTimeToday().toDate();
    }

    /**
     * 生成指定日期
     *
     * @param year  年
     * @param month 月
     * @param day   日
     * @param hour  时
     * @param min   分
     * @param sec   秒
     * @param msec  毫秒
     * @return Date
     */
    public static Date generateDate(int year, int month, int day, int hour, int min, int sec, int msec) {
        DateTime dt = new DateTime(year, month, day, hour, min, sec, msec);
        return dt.toDate();
    }

    /**
     * 生成指定日期
     *
     * @param year  年
     * @param month 月
     * @param day   日
     * @param hour  时
     * @param min   分
     * @param sec   秒
     * @return Date
     */
    public static Date generateDate(int year, int month, int day, int hour, int min, int sec) {
        DateTime dt = new DateTime(year, month, day, hour, min, sec);
        return dt.toDate();
    }

    /**
     * 生成指定日期
     *
     * @param year  年
     * @param month 月
     * @param day   日
     * @return
     */
    public static Date generateDate(int year, int month, int day) {
        LocalDate localDate = new LocalDate(year, month, day);
        return localDate.toDate();
    }

    /**
     * 生成指定时间
     *
     * @param hour 时
     * @param min  分
     * @param sec  秒
     * @param msec 毫秒
     * @return Date
     */
    public static Date generateDateTime(int hour, int min, int sec, int msec) {
        LocalTime localTime = new LocalTime(hour, min, sec, msec);
        return localTime.toDateTimeToday().toDate();
    }

    /**
     * 生成指定时间
     *
     * @param hour 时
     * @param min  分
     * @param sec  秒
     * @return
     */
    public static Date generateDateTime(int hour, int min, int sec) {
        LocalTime localTime = new LocalTime(hour, min, sec);
        return localTime.toDateTimeToday().toDate();
    }


    /**
     * 以默认格式（yyyy-MM-dd HH:mm:ss）格式化字符串到时间
     *
     * @return
     */
    public static Date formatStringToDate() {
        return formatStringToDate(null);
    }

    /**
     * 格式化字符串到时间,根据传入格式
     *
     * @param pattern
     * @return
     */
    public static Date formatStringToDate(String pattern) {
        if (StringUtils.isBlank(pattern)) {
            pattern = DEFAULT_FORMAT;
        }
        DateTime parse = DateTime.parse(pattern);
        return parse.toDate();
    }


    /**
     * 将时间格式化为字符串
     *
     * @param date
     * @param pattern
     * @return 格式化后的字符串
     */
    public static String formatDateToString(Date date, String pattern) {
        if (date == null) {
            return null;
        }
        if (StringUtils.isBlank(pattern)) {
            pattern = DEFAULT_FORMAT;
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.toString(pattern);
    }

    /**
     * 默认方式（yyyy-MM-dd HH:mm:ss）将时间格式化为字符串
     *
     * @param date
     * @return
     */
    public static String formatDateToString(Date date) {
        return formatDateToString(date, null);
    }


    /**
     * 获取当天的开始时间
     *
     * @return
     */
    public static Date getStartOfCurrentDay() {
        return getStartOfDay(new Date());
    }

    /**
     * 获取给定时间的开始时间
     *
     * @param date
     * @return
     */
    public static Date getStartOfDay(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        DateTime startOfDay = dateTime.withTimeAtStartOfDay();
        return startOfDay.toDate();
    }

    /**
     * 获取当天的结束时间
     *
     * @return
     */
    public static Date getEndOfCurrentDay() {
        return getEndOfDay(new Date());
    }

    /**
     * 获取给定时间的结束时间
     *
     * @param date
     * @return
     */
    public static Date getEndOfDay(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        DateTime endOfDay = dateTime.millisOfDay().withMaximumValue();
        return endOfDay.toDate();
    }

    /**
     * 计算两个时间相隔多少天
     *
     * @param startTime
     * @param endTime
     * @return
     */
    public static int getBetweenDays(Date startTime, Date endTime) {
        if (startTime == null || endTime == null) {
            throw new IllegalArgumentException("both parameters cannot be empty");
        }
        DateTime startDateTime = new DateTime(startTime);
        DateTime endDateTime = new DateTime(endTime);
        return Days.daysBetween(startDateTime, endDateTime).getDays();
    }

    /**
     * 计算给定时间是本年的第几周
     */
    public static int getWeekOfYear(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime startDateTime = new DateTime(date);
        return startDateTime.getWeekOfWeekyear();
    }

    /**
     * 计算当前时间是本年的第几周
     */
    public static int getCurrentDayWeekOfYear() {
        return getWeekOfYear(new Date());
    }

    /**
     * 两个日期是否在同一天
     *
     * @param date1
     * @param date2
     * @return
     */
    public static Boolean isEqualsSameDay(Date date1, Date date2) {
        if (date1 == null && date2 == null) {
            throw new IllegalArgumentException("date1 and date2 Cannot be empty at the same time");
        }
        if (date1 == null || date2 == null) {
            return false;
        }
        LocalDate localDate = new LocalDate(date1);
        LocalDate dateTime2 = new LocalDate(date2);
        return localDate.equals(dateTime2);
    }


    /**
     * 两个时间比较
     * startTime>endTime  return 1
     * startTime=endTime  return 0
     * startTime<endTime  return -1
     *
     * @param startTime
     * @param endTime
     * @return
     */
    public static int compare(Date startTime, Date endTime) {
        if (startTime == null || endTime == null) {
            throw new IllegalArgumentException("both parameters cannot be empty");
        }
        DateTime startDateTime = new DateTime(startTime);
        DateTime endDateTime = new DateTime(endTime);
        return startDateTime.compareTo(endDateTime);
    }


    /**
     * 获取给定日期的上一天的0点十分
     *
     * @param date
     * @return
     */
    public static Date getLastZeroPointDay(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.minusDays(1).withTimeAtStartOfDay().toDate();
    }

    /**
     * 获取给定日期的上一天的当前时间
     *
     * @param date
     * @return
     */
    public static Date getLastDay(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        return getPlusDay(date, -1);
    }

    /**
     * 给指定时间使加上plus天
     *
     * @param date
     * @param plus
     * @return
     */
    public static Date getPlusZeroPointDay(Date date, int plus) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.plusDays(plus).withTimeAtStartOfDay().toDate();
    }


    /**
     * 给指定时间使加上多少天（减去请填写负数）
     *
     * @param date
     * @param plus
     * @return
     */
    public static Date getPlusDay(Date date, int plus) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.plusDays(plus).toDate();
    }


    /**
     * 获取日期时间当月的总天数，如2017-02-1，返回28
     *
     * @param date
     * @return
     */
    public static int getDaysOfMonth(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.dayOfMonth().getMaximumValue();
    }

    /**
     * 根据给定日期获取当月最大日期(只返回YYYY-MM-dd类型的时间)
     * 比如2018-9-5  return 2018-09-30
     *
     * @param date
     * @return
     */
    public static Date maxDateOfMonth(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        LocalDate localDate = new LocalDate(date);
        return localDate.dayOfMonth().withMaximumValue().toDate();

    }

    /**
     * 根据给定日期获取当月最小日期，也就是1号(只返回YYYY-MM-dd类型的时间)
     * 比如2018-9-5  return 2018-09-30
     *
     * @param date
     * @return
     */
    public static Date minDateOfMonth(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        LocalDate localDate = new LocalDate(date);
        return localDate.dayOfMonth().withMinimumValue().toDate();

    }


    /**
     * 根据给定日期返回yyyyMM的整数类型
     *
     * @param date
     * @return
     */
    public static int getIntMonth(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        LocalDate localDate = new LocalDate(date);
        String yyyyMM = localDate.toString(Y4M2);
        return Integer.valueOf(yyyyMM);
    }

    /**
     * 根据给定日期返回yyyyMM的整数类型
     *
     * @param date
     * @return
     */
    public static int getIntLastMonth(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        LocalDate localDate = new LocalDate(date);
        LocalDate minusLocalDate = localDate.minusMonths(1);
        String yyyyMM = minusLocalDate.toString(Y4M2);
        return Integer.valueOf(yyyyMM);
    }


    /**
     * 判断是否是int类型的上月（格式是yyyyMM）
     *
     * @param intDate
     * @return
     */
    public static boolean isIntLastMonth(int intDate) {
        LocalDate localDate = new LocalDate();
        LocalDate minusLocalDate = localDate.minusMonths(1);
        String yyyyMM = minusLocalDate.toString(Y4M2);
        return Integer.valueOf(yyyyMM) == intDate;
    }


    /**
     * 是否周末
     *
     * @param date
     * @return boolean
     */
    public static Boolean isWeekend(Date date) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        LocalDate localDate = new LocalDate(date);
        int dayOfWeek = localDate.getDayOfWeek();
        return dayOfWeek == 6 || dayOfWeek == 7;
    }


    /**
     * 几小时之后
     *
     * @return
     */
    public static Date getAfterHours(Date date, int number) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.plusHours(number).toDate();
    }

    /**
     * 几分钟之后
     *
     * @param date
     * @return
     */
    public static Date getAfterMinutes(Date date, int number) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.plusMinutes(number).toDate();
    }

    /**
     * 几秒钟以后
     *
     * @param date
     * @param number
     * @return
     */
    public static Date getAfterSeconds(Date date, int number) {
        if (date == null) {
            throw new IllegalArgumentException("parameter cannot be empty");
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.plusSeconds(number).toDate();
    }


    public static void main(String[] args) {
        System.out.println(isWeekend(new LocalDate(2018, 9, 9).toDate()));
        System.out.println(isIntLastMonth(201808));
    }

}
```





参考文档：官方用户指南：http://www.joda.org/joda-time/userguide.html

java doc   http://www.joda.org/joda-time/apidocs/index.html

github  https://github.com/JodaOrg/joda-time