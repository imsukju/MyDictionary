### Transaction

자바에서 트랜잭션(Transaction)은 데이터베이스와 같은 영구적인 데이터 저장소에서 일련의 작업들이 모두 성공적으로 완료되거나, 또는 실패했을 때 원래 상태로 되돌리는 것을 보장하기 위한 메커니즘입니다. 트랜잭션은 ACID 속성(Atomicity, Consistency, Isolation, Durability)을 통해 데이터의 무결성을 유지합니다.

### 트랜잭션의 주요 역할:

1. **원자성(Atomicity)**: 트랜잭션 내의 모든 작업은 하나의 단위로 취급됩니다. 모든 작업이 성공하거나, 하나라도 실패하면 전체 트랜잭션이 롤백되어 모든 변경 사항이 무효화됩니다. 예를 들어, 은행 이체 작업에서 돈이 출금되었지만 입금이 실패하는 경우, 출금 작업도 롤백되어야 합니다.
```java
public class AtomicityExample {
    public static void main(String[] args) {
        String url = "jdbc:sqlite:example.db";

        try (Connection conn = DriverManager.getConnection(url)) {
            if (conn != null) {
                conn.setAutoCommit(false); // 트랜잭션을 수동으로 관리

                try (Statement stmt = conn.createStatement()) {
                    // 계좌 테이블 생성 (존재하지 않으면)
                    String createTableSQL = "CREATE TABLE IF NOT EXISTS accounts (id INTEGER PRIMARY KEY, balance INTEGER)";
                    stmt.execute(createTableSQL);

                    // 샘플 데이터 삽입 (기존에 존재하지 않으면)
                    String insertDataSQL = "INSERT INTO accounts (balance) VALUES (1000), (1000)";
                    stmt.execute(insertDataSQL);

                    // 트랜잭션 시작
                    String withdrawSQL = "UPDATE accounts SET balance = balance - 500 WHERE id = 1"; // 출금
                    String depositSQL = "UPDATE accounts SET balance = balance + 500 WHERE id = 2";  // 입금

                    stmt.executeUpdate(withdrawSQL);
                    stmt.executeUpdate(depositSQL);

                    // 트랜잭션 성공적으로 커밋
                    conn.commit();
                    System.out.println("트랜잭션 성공");
                } catch (SQLException e) {
                    // 예외 발생 시 롤백
                    conn.rollback();
                    System.out.println("트랜잭션 실패: " + e.getMessage());
                }
            }
        } catch (SQLException e) {
            System.out.println(e.getMessage());
        }
    }
}


```

2. **일관성(Consistency)**: 트랜잭션이 완료되면 데이터베이스는 일관된 상태를 유지해야 합니다. 이는 트랜잭션 전에 데이터베이스가 일관된 상태였다면, 트랜잭션 후에도 여전히 일관된 상태여야 함을 의미합니다.

3. **격리성(Isolation)**: 동시에 여러 트랜잭션이 실행될 때, 각각의 트랜잭션은 독립적으로 실행되어야 하며, 서로의 중간 상태를 볼 수 없도록 격리됩니다. 이를 통해 데이터 무결성을 보호하고 동시성 문제를 방지합니다.

4. **내구성(Durability)**: 트랜잭션이 성공적으로 커밋된 후에는 시스템 장애가 발생하더라도 그 결과가 영구적으로 반영됩니다. 즉, 커밋된 트랜잭션의 결과는 손실되지 않습니다.

### 자바에서의 트랜잭션 관리:

자바에서 트랜잭션을 관리하는 방법은 크게 두 가지로 나뉩니다:

1. **프로그래매틱 트랜잭션 관리**: 개발자가 트랜잭션의 시작, 커밋, 롤백을 코드로 직접 제어합니다. 보통 `try-catch-finally` 블록을 사용하여 트랜잭션을 명시적으로 관리합니다.

    ```java
    Connection conn = null;
    try {
        conn = dataSource.getConnection();
        conn.setAutoCommit(false);
        
        // 데이터베이스 작업 수행
        // 예: PreparedStatement.executeUpdate()

        conn.commit(); // 모든 작업이 성공하면 커밋
    } catch (SQLException e) {
        if (conn != null) {
            conn.rollback(); // 실패하면 롤백
        }
    } finally {
        if (conn != null) {
            conn.close(); // 연결 닫기
        }
    }
    ```

2. **선언적 트랜잭션 관리**: 스프링 프레임워크와 같은 프레임워크에서 제공하는 기능을 이용해 어노테이션이나 XML 설정을 통해 트랜잭션을 관리합니다. 이 방법은 코드가 더 간결해지며, 트랜잭션 관리를 비즈니스 로직에서 분리할 수 있습니다.

    ```java
    @Transactional
    public void transferMoney(Account fromAccount, Account toAccount, BigDecimal amount) {
        // 돈 이체 작업
    }
    ```

이 경우 스프링이 자동으로 트랜잭션의 시작, 커밋, 롤백을 처리해 줍니다.

트랜잭션은 복잡한 데이터 조작에서 데이터 무결성을 유지하고 일관된 결과를 보장하는 중요한 역할을 합니다.


`TransactionSynchronizationManager`는 스프링 프레임워크에서 트랜잭션 관련 작업을 관리하는 데 사용되는 유틸리티 클래스입니다. 이 클래스는 트랜잭션 동기화 관련 정보를 스레드 로컬(ThreadLocal)로 관리하여, 특정 트랜잭션에서 동기화 작업(예: 리소스 바인딩, 트랜잭션 이벤트 리스너 등)을 수행할 수 있도록 지원합니다.

주요 기능은 다음과 같습니다:

1. **트랜잭션 리소스 관리**: `TransactionSynchronizationManager`는 현재 스레드와 연결된 트랜잭션 리소스를 관리합니다. 이는 데이터베이스 연결이나 다른 트랜잭션 리소스를 트랜잭션 내에서 공유하고 관리할 수 있게 합니다.

2. **트랜잭션 동기화**: 트랜잭션이 시작될 때 리소스를 등록하거나, 트랜잭션이 커밋되거나 롤백될 때 이를 감지하고 적절한 처리를 할 수 있도록 동기화 작업을 수행합니다.

3. **트랜잭션 상태 추적**: 현재 스레드의 트랜잭션 상태(예: 활성 트랜잭션 여부)를 추적하여, 트랜잭션 관련 작업을 안전하게 수행할 수 있도록 돕습니다.

4. **트랜잭션 이벤트 리스너**: 트랜잭션이 커밋되거나 롤백될 때 특정 작업을 실행하도록 이벤트 리스너를 등록할 수 있습니다.

이 클래스는 일반적으로 개발자가 직접 사용할 일은 거의 없으며, 스프링 프레임워크가 내부적으로 트랜잭션 관리와 관련된 작업을 수행할 때 주로 사용됩니다.

더 자세한 정보는 [여기](https://gptonline.ai/ko/)에서 확인할 수 있습니다.