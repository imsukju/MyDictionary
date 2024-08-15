### 엔티티 클래스
클래스 자체는 테이블과 매핑  
엔티티 클래스의 객체는 로우와 매핑

### SpringFrameWork 를 사용하는 이유
비즈니스 로직에 집중하여 S/W 생상성을 향상시키기 위해서 사용합니다.

### JDBC API
대부분 인터페이스로 이루어져 있습니다  
"com.mysql.cj.jdbc.Driver.class" 는 jdbcapi 구현한 구체


### SmipleDriverDateSource
DBC(Java Database Connectivity) API에서 데이터베이스 연결을 관리하는 방법중 하나이며
DriverManager 를 조금 더 편하게 활용하기 위하여 사용합니다

코드 작성시 차이점  
**DriverManager**
```java
//DriverManager
Connection c = DriverManager.getConnection(
    "jdbc:mysql://localhost/sbdt_db?characterEncoding=UTF-8"
   ,"root"
   ,"1234" 

);
```


**SmipleDriverDateSource**
```java
SimpleDriverDataSource dataSource = new SimpleDriverDataSource();

dataSource.setrDirverClass(com.mysql.cj.....);
dataSource.setUrl("jdbc:mysql://localhost/sbdt_db?characterEncoding=UTF-8");
dataSource.setUsername("root");
dataSource.setPassword("1234");
```