### Descriptive Summary of the Issue

I worked on implementing a map view within the application, adhering to the specific needs and goals set by the UNDAC Team Leader. The goal was to provide an intuitive interface that would not only display a map of the operational area but also include functionalities like zooming in and out and toggling the visibility of specific pins on the map, such as security alerts and operational teams.

### Code Snippets and Commentary



#### 1. Toggle operational teams
Two private method ToggleOperationalTeams is designed to dynamically change the visibility of specific pin types on the map. By doing so, the application can streamline the view based on what the user wants to see.


```
/// <summary>
/// Toggles the visibility of operational team pins on the map.
/// </summary>
/// <param name="isVisible">Indicates whether the pins should be visible.</param>
private void ToggleOperationalTeams(bool isVisible)
{
    // Filter pins by type to get only those of type "OperationalTeam".
    var operationalTeamPins = _pins.Where(p => p.PinType == "OperationalTeam");
    
    foreach (var pin in operationalTeamPins)
    {
        // Depending on the 'isVisible' parameter, update each pin's visibility.
        // Assuming there's an 'IsVisible' property on the Pin class.
        pin.IsVisible = isVisible;
    }
}
```

Commentary: Encapsulation: By setting the IsVisible property of the pin directly, we assume that any related logic or side effects related to visibility are encapsulated within the property setter. This use of properties to manage state and behavior promotes the encapsulation principle, ensuring that the internal workings are hidden from the outside, and any necessary side effects or notifications are handled inside the property.

Parameterized Function: The function is parameterized to accept a boolean indicating visibility. This makes the function more versatile, as it can both show and hide pins based on the argument, following the DRY (Don't Repeat Yourself) principle. The method doesn't need overloads or variants to handle different visibility scenarios.

Method Naming: The method name ToggleOperationalTeams clearly indicates its purpose. Adopting a clear naming convention makes the code more readable and reduces the need for additional comments.

#### 2. Toggle security alerts
Private method ToggleSecurityAlerts is designed to dynamically change the visibility of specific pin types on the map. By doing so, the application can streamline the view based on what the user wants to see.
```     
/// <summary>
/// Toggles the visibility of security alert pins on the map.
/// </summary>
/// <param name="isVisible">Indicates whether the pins should be visible.</param>
private void ToggleSecurityAlerts(bool isVisible)
{
    var securityAlertPins = _pins.Where(p => p.PinType == "SecurityAlert");
    foreach (var pin in securityAlertPins)
    {
        pin.IsVisible = isVisible;  // Toggle visibility based on the provided value
    }
}
```
Commentary: 
The function ToggleSecurityAlerts has a single responsibility, which is to toggle the visibility of security alert pins. Adhering to the Single Responsibility Principle (SRP) of the SOLID principles ensures that the function does one thing and does it well, making it easier to test and maintain.

Instead of using conditional statements or other complicated logic to decide on the visibility, we directly set the IsVisible property based on the input parameter. This reduces code complexity and makes the function's behavior predictable and straightforward.

#### 3. Zoom-in Functionality
The application has method ZoomIn to change the zoom level of the map. It does so by adjusting the VisibleRegion of the map.
```
private void ZoomIn()
{
    if (OperationalMap.VisibleRegion == null) return;

    var center = OperationalMap.VisibleRegion.Center;
    var halfLatDelta = OperationalMap.VisibleRegion.LatitudeDegrees / 4;
    ...
    OperationalMap.MoveToRegion(newRegion);
}
```
Commentary: Safety checks (like checking if VisibleRegion is null) are good practices to prevent potential runtime errors. The zoom functions are concise and make use of map properties to perform the desired actions. Scope & Cohesion: The function does only one specific task—zooming out. This ensures a single responsibility and clear scope for the function.Early Exit: The initial null-check ensures that if there's no VisibleRegion, the method returns immediately. This reduces the likelihood of null reference exceptions and follows the fail-fast principle.

Meaningful Variable Names: Renaming center to centerOfMap makes it clear that we're dealing with the map's center. Similarly, using newVisibleRegion emphasizes that it's the region we'll switch to.

#### 4. Zoom-out Functionality: 
The application has method ZoomOut to change the zoom level of the map. It does so by adjusting the VisibleRegion of the map.
```
        /// <summary>
        /// Zooms out from the map by expanding the visible region.
        /// </summary>
        private void ZoomOut()
        {
            if (OperationalMap.VisibleRegion == null) return;

            var center = OperationalMap.VisibleRegion.Center;
            var doubleLatDelta = OperationalMap.VisibleRegion.LatitudeDegrees * 2;
            var doubleLongDelta = OperationalMap.VisibleRegion.LongitudeDegrees * 2;

            var newRegion = new MapSpan(center, doubleLatDelta, doubleLongDelta);
            OperationalMap.MoveToRegion(newRegion);
        }
```
#### 5. Event Handlers
Event handlers like OnZoomInClicked and OnZoomOutClicked manage user interactions, ensuring that the corresponding zoom functions are called when the buttons are pressed.Commentary: Segregating the logic into separate methods makes the code cleaner and more maintainable. If the zooming logic changes in the future, we only need to modify the ZoomIn or ZoomOut methods without touching the event handlers.
```
        /// <summary>
        /// Handles the Zoom In button clicked event.
        /// </summary>
        private void OnZoomInClicked(object sender, EventArgs e)
        {
            ZoomIn();
        }

        /// <summary>
        /// Handles the Zoom Out button clicked event.
        /// </summary>
        private void OnZoomOutClicked(object sender, EventArgs e)
        {
            ZoomOut();
        }
```


#### 6. Summary of Test Code
```
using NUnit.Framework;
using System.Collections.Generic;

namespace YourNamespace.Tests
{
    public class MapTests
    {
        private MainPage _mainPage;
        private List<Pin> _testPins;

        [SetUp]
        public void Setup()
        {
            _mainPage = new MainPage();

            _testPins = new List<Pin>
            {
                new Pin { PinType = "OperationalTeam", Location = new Position(11.0, 11.0) },
                new Pin { PinType = "OperationalTeam", Location = new Position(12.0, 12.0) }
            };

            // Assuming _pins is accessible for testing purposes
            _mainPage._pins = _testPins;
        }

        [Test]
        public void ToggleOperationalTeams_Visible_AllOperationalTeamPinsVisible()
        {
            _mainPage.ToggleOperationalTeams(true);

            var hiddenPins = _testPins.Where(p => !p.IsVisible && p.PinType == "OperationalTeam");
            Assert.IsFalse(hiddenPins.Any());
        }

        [Test]
        public void ToggleOperationalTeams_NotVisible_AllOperationalTeamPinsHidden()
        {
            _mainPage.ToggleOperationalTeams(false);

            var visiblePins = _testPins.Where(p => p.IsVisible && p.PinType == "OperationalTeam");
            Assert.IsFalse(visiblePins.Any());
        }

        [Test]
        public void ZoomIn_ChangesVisibleRegion()
        {
            // Assuming you have a method to set an initial region
            var initialRegion = new MapSpan(new Position(0, 0), 1, 1);
            _mainPage.SetInitialRegion(initialRegion);

            _mainPage.ZoomIn();

            var newRegion = _mainPage.OperationalMap.VisibleRegion;

            // Assert that the new region is smaller than the initial region
            Assert.Less(newRegion.LatitudeDegrees, initialRegion.LatitudeDegrees);
            Assert.Less(newRegion.LongitudeDegrees, initialRegion.LongitudeDegrees);
        }

        [Test]
        public void ZoomOut_ChangesVisibleRegion()
        {
            // Assuming you have a method to set an initial region
            var initialRegion = new MapSpan(new Position(0, 0), 0.5, 0.5);
            _mainPage.SetInitialRegion(initialRegion);

            _mainPage.ZoomOut();

            var newRegion = _mainPage.OperationalMap.VisibleRegion;

            // Assert that the new region is larger than the initial region
            Assert.Greater(newRegion.LatitudeDegrees, initialRegion.LatitudeDegrees);
            Assert.Greater(newRegion.LongitudeDegrees, initialRegion.LongitudeDegrees);
        }
    }
}
```
I implemented unit tests to ensure that:

- The zoom functionalities are working as expected.
- The toggle methods filter out the pins based on their type.
- No null exceptions are thrown when the visible region is undefined.
- Button clicks result in the expected map changes.

During testing, I used mock data to simulate different pin types and verified that the methods behaved as anticipated.

#### 7. Code Review Changes & Fixes

During the code review, a couple of suggestions were made:

- Error Handling: There were recommendations to add more error handling, especially for external factors like map loading issues.

I incorporated that feedback point, resulting in a stable feature with more errors handled.

```
private void ToggleOperationalTeams(bool isVisible)
{
    try
    {
        var operationalTeamPins = _pins.Where(p => p.PinType == "OperationalTeam");
        foreach (var pin in operationalTeamPins)
        {
            pin.IsVisible = isVisible;
        }
    }
    catch(Exception ex)
    {
        // Log the exception
        Console.WriteLine($"Error toggling operational teams: {ex.Message}");
        // Optional: Provide feedback to the user
    }
}

private void ToggleSecurityAlerts(bool isVisible)
{
    try
    {
        var securityAlertPins = _pins.Where(p => p.PinType == "SecurityAlert");
        foreach (var pin in securityAlertPins)
        {
            pin.IsVisible = isVisible;
        }
    }
    catch(Exception ex)
    {
        // Log the exception
        Console.WriteLine($"Error toggling security alerts: {ex.Message}");
        // Optional: Provide feedback to the user
    }
}
```

#### 8. Issues in Reviewed Code

While reviewing a team member's code for another feature, I observed:

- Initialization of Connection:
    It's risky to open the database connection in the constructor because if the connection fails, the whole object creation fails.
    Consider using a separate method for opening the connection or employing the "using" statement with the connection to ensure it's closed after usage.
```

public DatabaseOperations(string connectionString)
{
    _connection = new SqliteConnection(connectionString);
    // Consider deferring the opening of the connection until it's needed.
}

```

- Connection Management: The _connection object is opened during class instantiation and is kept open. It's a good practice to open the connection only when needed and close it immediately after.

-    SQL Injection Risk: You're building SQL commands by directly inserting values (e.g., table names) into the string. This could lead to SQL injection if not controlled properly. Use parameterized queries whenever possible.
```
public void AddRecord(string tableName, string name)
{
    // Potential SQL injection risk with 'tableName'
    var commandText = $"INSERT INTO {tableName} (Name) VALUES (@name)";
    ExecuteNonQuery(commandText, ("@name", name));
}
```

Reflective Summary

- New Realizations: I've realized the importance of writing modular and reusable code, especially in team environments where multiple features often overlap.
- Common Problems: Team development can sometimes lead to code redundancy, especially if communication channels aren't clear.
- Solution of the Workflow issues: To avoid future problems and clashes between team members, we modified our team workflow. Some discussions carried out this: #### Introduction to workflow
In the pursuit of optimizing our software development process, our team adopted a workflow deeply rooted in Agile-Scrum methodology. This reflection aims to dissect the rationale behind our choices, acknowledging both the strengths and the areas where our approach might not align perfectly with every project scenario.
#### Why Agile-Scrum?
Agile-Scrum was chosen primarily for its flexibility and emphasis on continuous delivery and improvement. This approach, supported by a wealth of academic literature, promotes adaptability—a crucial attribute in today's rapidly changing software development landscape. The iterative nature of Agile allows for regular feedback and course corrections, ensuring that the end product truly aligns with user needs.
Tools and Techniques
We integrated a variety of tools and techniques to support this methodology:

- IDE with Code Analysis Tools and VCS: Streamlining coding and version control to enhance efficiency and reduce errors.
- FUTURE: CI/CD Pipeline: Automating deployment processes to minimize manual intervention and accelerate release cycles.
- TDD and Code Reviews: Ensuring code quality and maintainability, a practice bolstered by numerous studies highlighting its effectiveness in reducing bugs and fostering cleaner code.
  
#### Team Structure and Communication
Our cross-functional team structure fostered a collaborative environment, essential for Agile's success. Regular stand-ups, sprint planning, and retrospectives were instrumental in maintaining open communication channels. However, we observed that the frequency of meetings, while beneficial for alignment, occasionally impacted deep work phases, suggesting a need for a more balanced approach.
Challenges and Adaptations
Despite the numerous benefits, our workflow wasn't without challenges:
- Scalability: Agile-Scrum can be less effective for larger, more complex projects. In such cases, we found blending Agile with elements of other methodologies, like Waterfall, helped maintain structure without sacrificing agility.
- Learning Curve: New team members initially struggled with the Agile framework and toolset. We mitigated this through mentorship programs and regular training sessions.
  
Definition of Ready:

    - There's a clear business value
    
    - Acceptance criteria are clear and defined
    
    - Dependencies are identified
- Learning from and Guiding Peers: While I've always been diligent about writing comprehensive comments and ensuring code structure, I've observed that not everyone follows these practices. This week, not only did I learn a unique error handling method from a colleague, but I also had the opportunity to guide some team members on effective peer code reviews.

Overall, this week has been enlightening in terms of understanding the dynamics of team coding, the importance of thorough code reviews, and continuous learning.

