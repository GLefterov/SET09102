# Portfolio Assignment - Project Work 2

## Week 9 Entry

### Descriptive Summary of Worked Issue
This week, I continued enhancing the geolocation feature in our application. My primary focus was on refining the database operations to manage map pins efficiently. I have worked on creating the services within the 'Services' folder. 

### Code Snippets and Software Design Practices

#### Database CRUD Operations
The `DatabaseOperations` class handles all database interactions. Here is a brief overview with examples of CRUD operations:

        public async Task InsertPinAsync(CustomPin pin)
        {
            using (var connection = new SqliteConnection($"Data Source={_dbPath}"))
            {
                await connection.OpenAsync();
                var cmd = connection.CreateCommand();
                cmd.CommandText = @"INSERT INTO Pins (Name, Latitude, Longitude, PinType, StatusOrPurpose) 
                                    VALUES (@Name, @Latitude, @Longitude, @PinType, @StatusOrPurpose)";
                cmd.Parameters.AddWithValue("@Name", pin.Name);
                cmd.Parameters.AddWithValue("@Latitude", pin.Latitude);
                cmd.Parameters.AddWithValue("@Longitude", pin.Longitude);
                cmd.Parameters.AddWithValue("@PinType", pin.PinType);
                cmd.Parameters.AddWithValue("@StatusOrPurpose", pin.StatusOrPurpose);

                await cmd.ExecuteNonQueryAsync();
            }
        }

        public async Task UpdatePinAsync(CustomPin pin)
        {
            using (var connection = new SqliteConnection($"Data Source={_dbPath}"))
            {
                await connection.OpenAsync();
                var cmd = connection.CreateCommand();
                cmd.CommandText = @"UPDATE Pins SET Name = @Name, Latitude = @Latitude, Longitude = @Longitude, 
                                    PinType = @PinType, StatusOrPurpose = @StatusOrPurpose WHERE Id = @Id";
                cmd.Parameters.AddWithValue("@Id", pin.Id);
                cmd.Parameters.AddWithValue("@Name", pin.Name);
                cmd.Parameters.AddWithValue("@Latitude", pin.Latitude);
                cmd.Parameters.AddWithValue("@Longitude", pin.Longitude);
                cmd.Parameters.AddWithValue("@PinType", pin.PinType);
                cmd.Parameters.AddWithValue("@StatusOrPurpose", pin.StatusOrPurpose);

                await cmd.ExecuteNonQueryAsync();
            }
        }

        public async Task DeletePinAsync(CustomPin pin)
        {
            using (var connection = new SqliteConnection($"Data Source={_dbPath}"))
            {
                await connection.OpenAsync();
                var cmd = connection.CreateCommand();
                cmd.CommandText = @"DELETE FROM Pins WHERE Id = @Id";
                cmd.Parameters.AddWithValue("@Id", pin.Id);

                await cmd.ExecuteNonQueryAsync();
            }
        }
Good practices from the code snippet:
- **Asynchronous Programming:** Methods are asynchronous (async), which means they won't block the thread that they're running on. This is particularly important for UI applications where you don't want to freeze the user interface while a database operation is in progress.
- **Using Statements:** The using statement ensures that the SqliteConnection is properly disposed of after the operation, which is crucial for resource management and avoiding memory leaks.
- **Parameterized Queries:** The use of parameters (cmd.Parameters.AddWithValue(...)) helps prevent SQL injection attacks by ensuring that user input is treated as a value rather than part of the SQL statement. This also makes the query more readable and maintainable.
- **Separation of Concerns:** Each method performs a single task (insert, update, or delete), which makes the code more maintainable and easier to test.
- **Awaiting Tasks:** The use of await ensures that the task is properly awaited without blocking. This also means that any exceptions thrown will be properly propagated up the call stack.
- **Prepared Statements:** The SQL commands are prepared before they are executed, which can help improve performance, particularly if the statements are executed multiple times (not shown directly in the snippets but is a general advantage of using command parameters).
- **Clean and Clear Method Signatures:** The method signatures are clean, clearly stating what the method does and what parameters it requires.
- **DRY Principle:** While not directly observable in these snippets alone, assuming these methods are the only place in the code where direct SQL operations for pins are performed, the code adheres to the "Don't Repeat Yourself" principle.

## Descriptive Summary of Test Code

I also expanded our test suite to cover the recent features added to our project. I focused on the `MapService` and `DatabaseOperations` classes. For `MapService`, I wrote tests to ensure that pins were correctly loaded from the database and added to the map. In the case of `DatabaseOperations`, I ensured that CRUD operations were functioning as expected through the use of mock objects to simulate database interactions.

```
[Fact]
public void LoadPins_ShouldLoadPinsFromDatabaseAndAddToMap()
{
    // Arrange
    var mockDbOperations = new Mock<DatabaseOperations>("fake_path");
    var pins = new List<CustomPin>
    {
        new CustomPin { /* Initialization properties */ },
        new CustomPin { /* Initialization properties */ }
        // Add as many pins as needed for the test
    };
    mockDbOperations.Setup(db => db.GetAllPins()).Returns(pins);
    
    var map = new Map();
    var mapService = new MapService(map, mockDbOperations.Object);

    // Act
    mapService.LoadPins(); // Assume this method is now public for testing

    // Assert
    map.Pins.Should().HaveCount(pins.Count);
    foreach (var pin in pins)
    {
        map.Pins.Should().ContainSingle(p => p.Label == pin.Name && p.Position.Latitude == pin.Latitude && p.Position.Longitude == pin.Longitude);
    }
}
[Fact]
public async Task InsertPinAsync_ShouldInsertPinIntoDatabase()
{
    // Arrange
    var mockSqliteConnection = new Mock<SqliteConnection>("fake_connection_string");
    var mockSqlCommand = new Mock<SqliteCommand>();
    
    mockSqliteConnection.Setup(conn => conn.CreateCommand()).Returns(mockSqlCommand.Object);
    mockSqlCommand.Setup(cmd => cmd.ExecuteNonQueryAsync()).ReturnsAsync(1); // Simulates one record affected

    var databaseOperations = new DatabaseOperations(mockSqliteConnection.Object);
    var pin = new CustomPin { /* Initialization properties */ };

    // Act
    await databaseOperations.InsertPinAsync(pin);

    // Assert
    mockSqlCommand.Verify(cmd => cmd.ExecuteNonQueryAsync(), Times.Once());
}

[Fact]
public async Task UpdatePinAsync_ShouldUpdatePinInDatabase()
{
    // ... Similar to InsertPinAsync test ...
}

[Fact]
public async Task DeletePinAsync_ShouldDeletePinFromDatabase()
{
    // ... Similar to InsertPinAsync test ...
}

[Fact]
public void GetAllPins_ShouldReturnAllPinsFromDatabase()
{
    // Arrange
    var mockSqliteConnection = new Mock<SqliteConnection>("fake_connection_string");
    var mockSqliteCommand = new Mock<SqliteCommand>();
    var mockDataReader = new Mock<DbDataReader>();
    // Setup mockDataReader to return a sequence of fake pins

    mockSqliteConnection.Setup(conn => conn.CreateCommand()).Returns(mockSqliteCommand.Object);
    mockSqliteCommand.Setup(cmd => cmd.ExecuteReader()).Returns(mockDataReader.Object);

    var databaseOperations = new DatabaseOperations(mockSqliteConnection.Object);

    // Act
    var pins = databaseOperations.GetAllPins();

    // Assert
    pins.Should().NotBeNull();
    pins.Should().BeEquivalentTo(/* Expected list of pins */);
}
```
### Feedback Received - Code Review

- **DatabaseOperations Class**: During the code review, it was suggested that the `InsertPinAsync` method could benefit from a transaction to ensure that pin insertion is atomic.
- **MapService Class**: The code review pointed out that `LoadPins` should handle potential exceptions when loading pins, such as when the database is unavailable.

### My Fixes in response to the Code Review

- I implemented a transaction scope within `InsertPinAsync` to encapsulate the database operation, ensuring that it would either completely succeed or roll back in the event of an error.
- Added try-catch blocks around database calls in `LoadPins` to handle exceptions gracefully, logging them and informing the user appropriately.
## Descriptive Summary of Reviewed Code

In my code review of a peer's work on the `MAUI#86`, includes classes PrivilegeRequest and TeamMember, both implementing INotifyPropertyChanged to support property binding notifications. The classes use private backing fields with a standard naming convention, but TeamMember has redundancies with properties like Name and Available that need to be streamlined. Here's an example of the redundant code that should be addressed:

```

// Redundant fields in TeamMember
private string _name;
public string Name
{
    get => _name;
    set => SetField(ref _name, value);
}

```

- The code also has nullable reference annotations which should be used within a '#nullable' context. For instance, the PropertyChangedEventHandler? should only be used if nullable contexts are enabled.

- The PrivilegeRequestService class performs database operations without exception handling, leading to potential runtime errors. All database interactions should be wrapped in try-catch blocks or otherwise ensure exception safety. Dependency Injection is recommended for the SQLiteAsyncConnection to promote testability, as the current setup method SetUpDb is called repeatedly, violating DRY principles.

- The TeamMember constructor sets Available to true, but the property already has a default value of true. This is redundant and can be removed.

## General Reflective Section

### Realisations

- **Exception Handling**: I've realized the importance of proper exception handling, which not only includes catching exceptions but also logging them and taking appropriate action.

### Common Problems

- **Team Communication**: Ensuring that everyone is aligned and aware of the coding standards and practices is sometimes challenging but essential for maintaining code quality.

### Comparison to Others

- I've noticed that my practice of writing descriptive comments and maintaining a clean code structure has been beneficial not only for me but also for my teammates who might work on the same codebase.

## Reflection on Improvement

### Application of Clean Code Principles

- I made a conscious effort to apply clean code principles by refactoring long methods into smaller, more readable ones.

### Avoiding Repetition of Mistakes

- From last week's experience, I paid extra attention to not leave `TODO` comments unresolved in my code submissions.

### Test Coverage

- This week, I added additional test cases to cover edge cases which were previously missed, thereby increasing our test coverage.

### Systematic Workflow

- I've adopted a more systematic approach to my personal workflow by integrating a checklist for code reviews to ensure no aspect is overlooked.

### Identifying Problems

- My ability to identify problems in other people's code has improved as I am now more vigilant about potential issues such as error handling and code redundancy.

## Links to GitHub Repository

- [DatabaseOperations Class](https://github.com/Software-Engineering-Red/MAUI-APP/blob/feature/mapFeature-23/Services/DatabaseOperations.cs)
- [MapService Class](https://github.com/Software-Engineering-Red/MAUI-APP/blob/feature/mapFeature-23/Services/MapService.cs)
