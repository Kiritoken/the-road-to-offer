# Stream流式计算


```java
User u1 = new User(1,"a",21); User u2 = new User(2,"b",22); User u3 = new User(3,"c",23); User u4 = new User(4,"d",24); User u5 = new User(6,"e",25); // 集合就是存储
List<User> list = Arrays.asList(u1, u2, u3, u4, u5);
// 计算交给Stream流
// lambda表达式、链式编程、函数式接口、Stream流式计算 list.stream()
        .filter(u->{return u.getId()%2==0;})
        .filter(u->{return u.getAge()>23;})
        .map(u->{return u.getName().toUpperCase();})
        .sorted((uu1,uu2)->{return uu2.compareTo(uu1);})
        .limit(1)
        .forEach(System.out::println);
```