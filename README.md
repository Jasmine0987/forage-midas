# Midas Core

A Spring Boot financial transaction processing system built for the JPMC Advanced Software Engineering Forage program. Midas Core demonstrates a practical implementation of event-driven architecture, using Kafka to process real-time transactions and update user balances in a persistent data store.

## What Midas Does

Midas Core is a backend service that:
- Manages user account records with ID, name, and balance
- Processes financial transactions (transfers between users) via Apache Kafka
- Exposes a REST API to query account balances
- Persists all user data to a database using Spring Data JPA
- Provides task-based testing infrastructure to validate transaction workflows

This project serves as both a learning tool and a hands-on challenge, guiding developers through building event-driven components step by step.

## Why Use Midas

- **Learn Event-Driven Architecture**: See how Kafka fits into a Spring Boot application with real-time transaction processing
- **Master Spring Boot Patterns**: Database access via repositories, dependency injection, testing with embedded Kafka
- **Hands-On Challenge**: Complete five progressive tasks, each building on the previous one
- **Clean Code Structure**: Well-organized source and test directories that separate concerns

## Getting Started

### Prerequisites

- Java 17 or later
- Maven (or use the included Maven Wrapper)

### Installation

Clone the repository:

```bash
git clone https://github.com/Jasmine0987/forage-midas.git
cd forage-midas
```

### Running the Application

Start the Spring Boot application:

```bash
./mvnw spring-boot:run
```

On Windows:
```bash
mvnw.cmd spring-boot:run
```

### Running Tests

Execute the full test suite:

```bash
./mvnw test
```

Each test corresponds to a task:
- **Task One**: Verify the application boots successfully
- **Task Two**: Observe Kafka transaction messages arriving in the system
- **Task Three**: Debug a transaction workflow and track balance updates
- **Task Four**: Trace balance state changes after processing a set of transactions
- **Task Five**: Query the REST API and retrieve final balances for multiple users

To run a specific task:
```bash
./mvnw test -Dtest=TaskOneTests
```

## Project Structure

```
src/
  main/java/com/jpmc/midascore/
    component/          Database persistence abstraction
    entity/             JPA entities (UserRecord)
    foundation/         Domain models (Transaction, Balance)
    repository/         Spring Data repository interfaces
    MidasCoreApplication.java    Spring Boot entry point
  test/java/com/jpmc/midascore/
    BalanceQuerier.java      REST client for balance queries
    FileLoader.java          Test data file loading utility
    KafkaProducer.java       Kafka transaction publisher
    UserPopulator.java       Database initialization helper
    TaskOneTests - TaskFiveTests    Progressive learning tasks

.mvn/                 Maven wrapper files
pom.xml               Maven build configuration
application.yml       Spring Boot configuration (application properties)
services/
  transaction-incentive-api.jar    Pre-built service dependency
```

## How It Fits Together

1. **Startup** (`TaskOneTests`): Spring Boot application initializes with all components
2. **Event Production** (`TaskTwoTests`): Test loads transaction data from files and publishes via `KafkaProducer` to a Kafka topic
3. **Event Consumption** (`TaskThreeTests`): The application consumes these transactions (via listeners you'll implement) and applies balance updates
4. **State Queries** (`TaskFourTests`): Debugger-assisted inspection of database state after transaction processing
5. **REST API** (`TaskFiveTests`): A REST endpoint exposes user balances for external queries via `BalanceQuerier`

**Data Flow:**
```
Test Data File → FileLoader → KafkaProducer → Kafka Topic 
                                              ↓
                                    Your Kafka Listener 
                                              ↓
                                    Update UserRecord Balance 
                                              ↓
                                    Persist via DatabaseConduit 
                                              ↓
                                    REST API queries via BalanceQuerier
```

## Core Components

### Entities & Models

- **`UserRecord`** (JPA Entity)
  - Represents a user account with id, name, and current balance
  - Persisted to database via Spring Data JPA
  
- **`Transaction`** (Domain Model)
  - Represents a financial transfer: sender ID, recipient ID, amount
  - Deserialized from Kafka messages

- **`Balance`** (Domain Model)
  - Represents a user's current balance state
  - Returned by REST API queries

### Data Access

- **`UserRepository`** (Spring Data CrudRepository)
  - Provides CRUD operations for UserRecord
  - Implements custom `findById(long id)` method

- **`DatabaseConduit`** (Component)
  - Abstraction layer for database operations
  - Saves UserRecord instances via the repository

### Kafka & Messaging

- **`KafkaProducer`** (Test Component)
  - Parses transaction data from strings
  - Publishes `Transaction` objects to a configurable Kafka topic (via `general.kafka-topic` property)

### Testing Utilities

- **`FileLoader`** (Component)
  - Loads transaction and user data from classpath resources
  - Returns lines as string arrays for parsing

- **`UserPopulator`** (Component)
  - Initializes the database with seed user data
  - Used in Tasks Three, Four, and Five

- **`BalanceQuerier`** (Component)
  - HTTP client that queries the REST API endpoint at `http://localhost:33400/balance?userId=<id>`
  - Returns user balance after all transactions are processed

## Configuration

### Application Properties

Edit `application.yml` to configure:
- Database connection (when using a real database)
- Kafka broker settings (topic name, bootstrap servers)
- Server port (Task Five runs on port 33400)

Example addition to `application.yml`:
```yaml
general:
  kafka-topic: transactions

spring:
  jpa:
    hibernate:
      ddl-auto: update
```

## What to Implement

This repository provides the framework and tests. You will implement:

1. **Kafka Consumer/Listener**: Handle incoming `Transaction` messages from Kafka
2. **Transaction Logic**: Apply balance updates (debit sender, credit recipient)
3. **REST Controller**: Expose a `/balance` endpoint to query user balances
4. **Error Handling**: Gracefully handle invalid transactions or missing users

The test files in `src/test/java/com/jpmc/midascore/TaskXTests.java` guide you on what to build and provide debugging hooks via breakpoints.

## Key Dependencies

From `pom.xml`:
- **Spring Boot 3.2.5** - Framework foundation
- **Spring Data JPA** - Database access (auto-configured)
- **Spring Kafka** - Kafka message handling
- **JUnit 5** - Test framework
- **Testcontainers** - File loading utility in tests

## Support & Resources

- Review the [Spring Boot documentation](https://spring.io/projects/spring-boot)
- Explore [Spring Data JPA](https://spring.io/projects/spring-data-jpa) for database operations
- Learn about [Spring Kafka](https://spring.io/projects/spring-kafka) for event handling
- Check test files (`TaskOneTests` through `TaskFiveTests`) for expected behavior

## Troubleshooting

**Application won't start**
- Ensure Java 17+ is installed: `java -version`
- Check Maven settings: `./mvnw -version`

**Tests timeout waiting for Kafka**
- Embedded Kafka should start automatically; check logs for port conflicts on 9092

**Balance query returns 404**
- Verify Task Five runs with `SpringBootTest.WebEnvironment.DEFINED_PORT`
- Ensure the REST endpoint is implemented at the expected URL

**Transaction data not found**
- Check that test data files exist in `src/test/resources/test_data/`
- Verify `FileLoader` is correctly loading from classpath

## Contributing

This is an educational project for the JPMC Forage program. Improvements and bug reports are welcome. For substantial changes, please create an issue first to discuss.

## License

This project is provided as-is for educational purposes as part of the JPMC Advanced Software Engineering Forage program.
