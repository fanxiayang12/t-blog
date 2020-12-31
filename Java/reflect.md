# 如何通过反射获取对象的包括类的继承层次中的所有属性？
    在API请求中，基于请求安全校验的考虑，需要对所有参数签名，签名的过程中要求从请取出对象的所有参数，根据字典顺序排序，拼接各个参数。

    类关键代码

```java
    abstract class AEntity {
        private String supClassParam;
    }
    class Entity extends AEntity {
        private String subClassParam;
    }
```
## Class.getDeclaredFields()
    获取当前类自身的所有字段，包括public、protect、private，甚至static的，然而该类存在继承类，该方法无法获取到继承类中的任何方法。因此，无法满足该场景下的使用。
## Class.getFields()
    获取当前类以及类继承层次中，所有的public修饰的属性。显然，该类的属性存在private，就无法获取。
## 顺着继承类层次向上逐层getDeclaredFields()
```java
    List<Field> fieldsList = new ArrayList<>();
    // 查询类继承层级中所有的字段
    Class<?> cls = this.getClass();
    while (cls != Object.class) { // 一直到最高类为止
        Field[] fields = cls.getDeclaredFields();
        fieldsList.addAll(Arrays.asList(fields));
        cls = cls.getSuperclass(); // 向上
    }
```
    