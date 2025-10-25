# 技巧
## 自定义类作为集合元素
- 自定义类作为集合元素进行比较时，需要正确覆写equals()方法
```java
 List<PersonList> personLists = new ArrayList<>();
personLists.add(new PersonList("qwe", 10));
personLists.add(new PersonList("asd", 11));
System.out.println(personLists.contains(new PersonList("qwe", 10)));

class PersonList {
    private String name;
    private int age;
    PersonList(String name, int age) {
        this.name = name;
        this.age = age;
    }
    // List的实现类通过元素的equals()方法比较两个元素是否相等，
    // 因此，放入的元素必须正确覆写equals()方法
    @Override
    public boolean equals(Object obj) {
        if (obj instanceof PersonList p) {
            // 使用Objects.equals()静态方法比较两个引用类型是否相等的目的是省去了判断null的麻烦
            return Objects.equals(this.name, p.name) && this.age == p.age;
        }
        return false;
    }
}

```
## 集合自定义类排序
- 实现 Comparable 接口, 重写 compareTo(Object obj) 方法
```java
/**
 * 实现 Comparable 接口, 重写 compareTo(Object obj) 方法
 */
class Student implements Comparable {
    private int age;
    public Student(int age) {
        this.age = age;
    }
    @Override
    public int compareTo(Object o) {
        Student student = (Student) o;
        return this.age - student.age;
    }
}
// 比如优先队列 PriorityQueue
Queue<Student> queue = new PriorityQueue<>();
queue.offer(new Student(10));   
queue.offer(new Student(20));
queue.offer(new Student(30));
// 根据重写的 compareTo 方法, 排序后, 输出 10, 20, 30
```
- 传入实现的 Comparator 接口, 重写 compare(Object o1, Object o2) 方法
```java
/**
 * 传入实现的 Comparator 接口, 重写 compare(Object o1, Object o2) 方法
 */
// 例如优先队列 PriorityQueue
Queue<Student> queue = new PriorityQueue<>(new Comparator<>() {
    @Override
    public int compare(Student o1, Student o2) {
        // 通过Integer.compare()方法比较两个整数的大小
        return Integer.compare(o1.getAge(), o2.getAge());
    }
});
```