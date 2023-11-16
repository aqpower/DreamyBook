
## 前置知识

我们有两个表，对应有两个类User类和Clothes类，成员变量即表中各字段名，都可实例化，以下是构造函数
```java
public User(String userName, String userPassword, String createTime) {  
    this.userName = userName;  
    this.userPassword = userPassword;  
    this.createTime = createTime;  
}

public Clothes(int userId, String imgUrl, int type, String message, String createTime, int size) {  
    this.userId = userId;  
    this.imgUrl = imgUrl;  
    this.type = type;  
    this.message = message;  
    this.createTime = createTime;  
    this.size = size;  
}
```
所有成员变量皆为private,皆设置有公有set和get函数，例如
```java
public int getId() {  
    return id;  
}
```
> 我们数据库中的每一行都相当于这些类的实例化对象，我们插入也是提供对象即可，我们查询得到的也会是对象，我们可以自己声明对象。
## 使用方法
1. 声明并定义
```java
MyDatabase myDatabase;  
UserDao userDao;

myDatabase = MyDatabase.getDatabase(this);  
userDao = myDatabase.userDao();
```
2.之后就可以愉快的使用了，例如：
```java
User user = new User("change_name","123456",new Date().toString());  
userDao.insertUser(user);
```
```java

List<User> userList = userDao.getAllUser();
```
可以直接在主线程使用
## 接口说明
> `@parm`表示参数，`@return`表示返回
### 1.User表
```java
/**  
 * 插入一条用户数据  
 * @param user 待插入数据库的数据  
 * */  
void insertUser(User user);  
  
/**  
 * 按用户名查询用户，当找不到时候返回的列表长度为0
 * @param userName 用户名  
 * @return 用户  
 */  
List<User> findByName(String userName);  

// 获取所有用户
List<User> getAllUser();  
  
/**  
 * 根据主键找到用户，然后update  
 * @param user 用户  
 */  
void updateUser(User user);  
  
  
/**  
 * 删除  
 * @param user 根据用户进行删除  
 */  
void deleteUser(User user);
```
### 2.Clothes表

```java
/**  
 * 增加多个衣服  
 * @param clothess 衣服实例  
 */    
void insertClothess(Clothes... clothess);  
  
/**  
 * 插入一件衣服  
 * @param clothes 待插入数据库的衣服实例
 * */  
void insertClothes(Clothes clothes);  
  
/**  
 * 通过id获取衣服列表，按id降序排列  
 * @param userId 用户id  
 * @return 该id用户所拥有的衣服  
 */   
LiveData<List<Clothes>>getByUserId(int userId);  
  
/**  
 * 用于筛选衣服种类  
 * @param userId 用户id  
 * @param type 衣服类型  
 * @return 该种类的衣服  
 */  
LiveData<List<Clothes>> getByType(int userId, int type);  
  
/**  
 * 删除衣物  
 * @param clothes 需要删除的衣服实例  
 */  
void deleteClothes(Clothes clothes);  
  
/**  
 * 删除所有衣服  
 */  
void deleteAllClothes();  
  
/**  
 * 根据主键找到衣服，然后替换update  
 * @param clothes 衣服实例
 */  
void updateClothes(Clothes clothes);
```