title: spring-data与mongodb学习笔记

category: java

tags: 
 - java
 - spring

date: 2017/03/01

---

&nbsp;&nbsp;&nbsp;&nbsp;

# spring-data-mongo

## repository

&nbsp;&nbsp;&nbsp;&nbsp;spring-data-mongo为mongodb的查询提供了黑魔法，只需要继承Repository接口就可以通过spring-data实现接口中的抽象方法。看一个例子：

```Java
import org.springframework.data.mongodb.repository.MongoRepository;
import java.util.List;

public interface UserRepository extends MongoRepository<User, String> {
    List<User> findByUsername(String username);
    List<User> findByUsernameLike (String username);
    List<User> findByEmail(String email);
}
```

&nbsp;&nbsp;&nbsp;&nbsp;尽管只创建了一个接口和若干未实现的抽象方法，但是在spring-data-mongo的黑魔法之下就已经可以使用了：

```Java
    public String find(@RequestParam String username){
        return userDao.findByUsername(username).toString();
    }
    
```
# spring和mongoTemplate

