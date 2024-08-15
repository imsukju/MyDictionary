### property 
주로 애플리케이션 설정과 구성 정보를 관리하는 데 사용됩니다.
Spring에서는 애플리케이션이 실행될 때 필요한 다양한 설정 정보를 외부 파일에 정의하고 이를 애플리케이션 내에서 쉽게 참조할 수 있게 합니다.


```java
@Configuration
public class AppConfig {

    @Bean
    public PropertySourcesPlaceholderConfigurer properties() {
        PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
        Properties properties = new Properties();
        properties.setProperty("jdbc.driver.className", "com.mysql.jdbc.Driver");
        properties.setProperty("jdbc.url", "jdbc:mysql://localhost:3306/mydb");
        configurer.setProperties(properties);
        return configurer;
    }

    @Value("${jdbc.driver.className}")
    private String driverClassName;

    @Value("${jdbc.url}")
    private String url;

    @Bean(destroyMethod = "close")
    public BasicDataSource myDataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername("root");
        dataSource.setPassword("misterkaoli");
        return dataSource;
    }
}
```
## @Value의 역활  
@Value Annotation를 사용하여 $기호와 함께 "jdbc.driver.className" 라는 키값을 설정하면 이 키의 대한 값을 driverClassName에 세팅합니다  

## @Bean(destroyMethod = "close")의 역할
빈 인스턴스에서 애플리케이션 컨텍스트가 닫힐 때 호출할 메서드의 이름입니다. 예를 들어, JDBC DataSource 구현체의 close() 메서드나 Hibernate SessionFactory 객체에 해당합니다. 이 메서드는 인수가 없어야 하며, 어떠한 예외도 던질 수 있습니다.
Spring에서는 빈이 소멸될 때 호출할 메서드를 지정할 수 있으며, Spring이 이를 자동으로 찾아주는 기능도 있습니다. 이 기능을 원하지 않으면 꺼둘 수도 있고, 이 방법은 주로 리소스 해제를 위해 사용됩니다.
default 설정은 AbstractBeanDefinition.INFER_METHOD 입니다