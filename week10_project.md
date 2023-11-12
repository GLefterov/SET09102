## Week 10 Portfolio Entry
### Descriptive Summary of the Issue

During this week, I focused on enhancing a feature in our MAUI application as described in Issue #130. The task involved creating a user interface for listing experts and implementing a reservation system. This feature is crucial for our app as it directly interacts with the end-users, providing them with the functionality to reserve experts for their needs.
Code Snippets and Good Software Design Practice

Here are some snippets from the code I worked on, demonstrating the application of good software design practices:

```csharp

// BasicExpertService.cs
public class BasicExpertService : IBasicExpertService
{
    // Implementation of data retrieval and business logic
}

// TeamRequestPage.xaml.cs
public partial class TeamRequestPage : ContentPage
{
    private readonly IBasicExpertService _expertService;

    public TeamRequestPage(IBasicExpertService expertService)
    {
        InitializeComponent();
        _expertService = expertService;
    }
}

```
**Commentary:** By implementing an interface (IBasicExpertService) and using dependency injection in TeamRequestPage, I adhered to the SOLID principles, particularly the Dependency Inversion Principle. This approach enhances testability and maintainability.

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();
    var experts = await _expertService.GetAvailableExpertsAsync();
    AvailableExperts.Clear();
    foreach (var expert in experts)
    {
        AvailableExperts.Add(expert);
    }
}
```

**Commentary:** In OnAppearing, data is loaded asynchronously using await _expertService.GetAvailableExpertsAsync(). Asynchronous operations prevent the UI from freezing while data is being loaded.


## Descriptive Summary of Test Code

```csharp
[TestClass]
public class TeamRequestPageTests
{
    [TestMethod]
    public void ReserveExpert_UpdatesExpertStatusAndListView()
    {
        // Arrange
        var page = new TeamRequestPage();
        var initialAvailableCount = page.AvailableExperts.Count;

        // Assume we reserve the first available expert
        var expertToReserve = page.AvailableExperts.First();

        // Act
        page.ReserveExpert(expertToReserve);

        // Assert
        Assert.IsFalse(expertToReserve.IsAvailable);
        Assert.AreEqual(initialAvailableCount - 1, page.AvailableExperts.Count);
        // Further assertions can be made to check the UI components if needed
    }
}
```
**Commentary:** Description: This test will simulate the user action of reserving an expert and will verify if the expert's availability status is updated correctly and the ListView is refreshed.

```csharp
[TestClass]
public class BasicExpertServiceTests
{
    [TestMethod]
    public async Task GetAvailableExpertsAsync_ReturnsOnlyAvailableExperts()
    {
        // Arrange
        var service = new BasicExpertService();

        // Act
        var availableExperts = await service.GetAvailableExpertsAsync();

        // Assert
        Assert.IsTrue(availableExperts.All(e => e.IsAvailable));
    }
}

```

**Commentary:** This test will verify if the GetAvailableExpertsAsync method correctly filters and returns only the experts marked as available.

These tests are designed to validate the core functionalities of our application. The unit test ensures the service logic is correctly filtering available experts, and the integration test checks the behavior of the UI and underlying logic when an expert is reserved. Both tests are essential for ensuring the reliability and correctness of our application.

## Code Review

During the code review process, I received valuable feedback which helped me refine my approach to software development. One key area of improvement was in enhancing the readability and maintainability of my code, especially within the XAML files. Initially, my DataTemplate was overly complex, which could hinder future modifications. Post-review, I simplified this, making it more accessible for other team members.

The feedback process underscored the importance of having different perspectives on the same codebase. It not only helps in identifying potential issues but also in adopting best practices that might not be immediately apparent to the original author.

* **Encapsulation in Models:** The Status property in BasicExpert was initially public, which was changed to internal to enhance encapsulation.
* **Simplification of XAML DataTemplate:** The DataTemplate in TeamRequestPage.xaml was complex and was simplified for better readability and maintainability.

I implemented these changes, which improved the code's quality and maintainability.

## Code review for a teammate
```csharp
private SQLiteAsyncConnection _dbConn;

        private async Task ConnectToDB()
        {
            if (_dbConn != null)
                return;

            _dbConn = new SQLiteAsyncConnection(DatabaseSettings.DBPath, DatabaseSettings.Flags);
            await _dbConn.CreateTableAsync<LogisticsOperation>();
        }
        public LogisticsService()
        {
            _dbConn = new SQLiteAsyncConnection(DatabaseSettings.DBPath, DatabaseSettings.Flags);
        }

        public async Task<List<LogisticsOperation>> GetAllOperations()
        {
            return await _dbConn.Table<LogisticsOperation>().ToListAsync();
        }
```
db connection initialization - The constructor initializes _dbConn, but there's also a ConnectToDB method which seems redundant since it checks for _dbConn nullity and then initializes it. Consider removing the redundancy to streamline the connection setup.



async - The asynchronous nature of ConnectToDB might not be fully utilized since it's called within a synchronous context in AddLogisticsOperation. Be cautious about mixing asynchronous and synchronous contexts as it can lead to performance bottlenecks.


## Reflective Summary
**Personal Growth in Software Engineering:** Over the past weeks, my approach to software engineering has evolved significantly. I've become more adept at writing modular and reusable code, a skill that has proven invaluable in our collaborative team environment. For instance, while working on Issue #130, I implemented a feature with reusability in mind, which later facilitated seamless integration into another module without any code duplication. This not only saved time but also enhanced the overall quality of our application.

**Enhanced Focus on Code Quality and Efficiency**: I've also sharpened my focus on code quality and efficiency. By adopting Test-Driven Development (TDD) practices more rigorously, I've managed to reduce the occurrence of bugs in my code submissions significantly. This shift has not only improved my productivity but also contributed to a more robust codebase for the team.

**Learning and Adapting from Peer Feedback:** Receiving and acting on feedback from code reviews has been a cornerstone of my growth. I've learned to embrace constructive criticism, using it as a stepping stone to refine my coding practices. For example, a recent suggestion from a peer to simplify my code logic led to a more elegant and maintainable solution, which was well-received by the team.

**Streamlined Communication and Collaboration:** Our team has made substantial progress in streamlining communication and collaboration. The introduction of weekly  stand-ups has significantly improved project transparency and team alignment. These brief meetings have become a platform for addressing blockers and sharing updates, ensuring everyone is in sync. In our case as this is a learning module it was really helpful to have such sessions to just split duos to code review each other as well rotate the teams.
## Future Enhancements

While we've made great strides, there's always room for improvement. Unfortunately some people don't have advanced git knowledge and .NET MAUI proficiency. As so, I'd suggest some improvements in the near future:

**Pair Programming:** Implementing pair programming sessions, especially for complex features or bug fixes, can enhance team collaboration and knowledge transfer into people without proficiency in the tech stack we use for this project. I'm a fan of the tendency that we all can learn by each other. As so, each developer can be in help of another.

**Feedback Mechanism Enhancement:** Establishing a more structured feedback mechanism, both for code and overall team performance, can help in identifying areas of improvement and ensure continuous growth. I've spotted some people organize sessions to share their feedback (not through the github platform), so they were talking through the issues they've found. I think it would be much better to actually implement that kind of CR feedback, as the owner of the code could understand CR outcomes better.

## Links:
- [Pull Request](https://github.com/Software-Engineering-Red/MAUI-APP/pull/132)
- [Code Review](https://github.com/Software-Engineering-Red/MAUI-APP/pull/125)
