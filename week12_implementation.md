# Project work  5

## Issue summary:
This week, I worked on a feature for the UNDAC app to allow operational team leaders to log errors and request additional resources. The challenge was to create a user interface for inputting errors and resources, and to implement the backend logic for storing and displaying this data.
[Issue#159](https://github.com/Software-Engineering-Red/MAUI-APP/pull/179)
```csharp
public class ErrorResourcePair
    {
        public int Id { get; set; }
        public string Error { get; set; }
        public string Resource { get; set; }

        // Default constructor
        public ErrorResourcePair()
        {
            // Initialize with default values if needed
            Error = string.Empty;
            Resource = string.Empty;
        }

        // Overloaded constructor for initializing properties
        public ErrorResourcePair(string error, string resource)
        {
            Error = error ?? throw new ArgumentNullException(nameof(error));
            Resource = resource ?? throw new ArgumentNullException(nameof(resource));
        }
```
**Commentary:** This model adheres to the Single Responsibility Principle (one of the SOLID principles), focusing solely on representing an error-resource pair. It's a clear example of the DRY principle as well, avoiding repetition by centralizing the error-resource data structure. It does also include exception handling which allows a program to deal with unexpected situations without crashing. By catching exceptions, the program can handle errors gracefully and continue running or shut down safely.

```csharp
private void OnSubmit()
{
    // Validate input before proceeding
    if (string.IsNullOrWhiteSpace(ErrorInput))
    {
        // Handle the error appropriately, e.g., notify the user
        NotifyUser("Error input is required.");
        return;
    }

    if (string.IsNullOrWhiteSpace(ResourceInput))
    {
        // Handle the error appropriately, e.g., notify the user
        NotifyUser("Resource input is required.");
        return;
    }

    try
    {
        // Create a new ErrorResourcePair instance
        var errorResourcePair = new ErrorResourcePair(ErrorInput, ResourceInput);

        // Add the pair to the collection
        ErrorResourcePairs.Add(errorResourcePair);

        // Optionally, save to database or perform other actions
        SaveErrorResourcePairToDatabase(errorResourcePair);

        // Clear the input fields after successful submission
        ResetInputFields();
    }
    catch (Exception ex)
    {
        // Log the exception and notify the user
        LogError(ex);
        NotifyUser("An error occurred while processing your request.");
    }
}
```
**Commentary:** This piece of code validates the input, as this prevents the creation of invalid "ErrorResourcePair" objects. Also the `onSubmit()` method is kept focused on its primary task. Auxiliary actions like resetting input fields, saving data, notifying users, and logging errors are delegated to separate methods. This makes the code more readable and maintainable.

  ## Code Quality Evaluation  

  ## Code Review

During the code review, a significant suggestion was made regarding the enhancement of code readability and maintainability through the use of comments, especially in areas where the logic became more complex. This feedback was crucial as it highlighted an area that I had overlooked - the importance of making the code understandable not just to machines, but to humans as well.

Also in our Discord server, I got that feedback from Brandon - Your implementation of the SaveErrorAsync method in ResourceRequestService is efficient, but it lacks exception handling. Consider wrapping the database operations in a try-catch block to handle potential exceptions and ensure application stability.

The Code Review from Nikola provided me useful insights into enhancing the readability, maintainability, and robustness of my code. Two key areas were highlighted: the need for improved code comments for complex logic, and the incorporation of exception handling in critical database operations.

**Action Taken:** In response, I meticulously revisited the segments of the codebase where the logic was dense or intricate. I introduced explanatory comments, particularly in the ResourceRequestViewModel's OnSubmit method, detailing each step's purpose and its contribution to the overall functionality. This included clarifications on input validation, data processing, and the logic flow.

**Technical Reflection:** The process of commenting complex logic segments aligns with the principles of good software documentation. It serves as an internal guide, elucidating the rationale behind specific coding decisions, thereby facilitating future modifications and debugging processes. This practice is not only a reflection of good software engineering but also an embodiment of proactive team collaboration.

The specific suggestion from a peer, Brandon, via our Discord server, highlighted a critical improvement area in the SaveErrorAsync method within the ResourceRequestService. The method, while efficient, lacked a mechanism to handle potential exceptions during database interactions.

**Action Taken:** Acknowledging the validity of this feedback, I implemented a try-catch block around the database operations in the SaveErrorAsync method. This addition is designed to catch and handle exceptions, thereby preventing the application from crashing and ensuring a smoother user experience.

**Technical Reflection:** The integration of exception handling is a fundamental aspect of robust software design, particularly in database operations where the risk of unexpected errors is high. This approach adheres to the principles of defensive programming, anticipating potential failures in the system and mitigating their impact. It demonstrates a commitment to software reliability and resilience, key tenets of high-quality software development.


## Reflective summary:

During this week, I faced several issues which will be discussed in this section:
- **Error Handling:** Not every developer in the team was following the good practices, and in such situation we found out our application sometimes crashes after a trying to input any user information to the application Another significant issue was the lack of robust error handling, especially in critical sections like database interactions. This omission can lead to unanticipated crashes and instability in the application, impacting the user experience and reliability. So because of that I invested some extra time in actually going through code and seeing where would it be most needed to have Error Handlings implemented and wrote some where it was needed to fix the issue.
- **Code Consistency:** As we were quite a big team, we had different code styles amongst different people. With multiple developers contributing, there were instances of inconsistent coding styles and practices. This inconsistency can lead to confusion and makes the codebase harder to maintain and scale. At some point I noticed that Zsambor was fixing the style of the code going through different files and functionalities which was extremely helpful as it made our repository much more robust and clean to work on.
- **Balance between flexibility and standards:** Working in a team with many developers and developin styles, we encountered two ends of a rope - the first one was extremely flexible code, which sometimes failed and the second end of the rope - very standardized code that was long and wasn't really future-proof and there wasn't a way we could improve it in the future. In the dynamic realm of software development, the balance between individual flexibility and adherence to team standards is crucial for harmonious and efficient project progression. This equilibrium ensures that while each developer's unique approach and creativity are respected, the overall codebase remains cohesive, readable, and maintainable. In a recent project, I implemented this balance by establishing a clear set of coding guidelines collaboratively with the team. These guidelines outlined essential aspects like naming conventions, code structure, and documentation standards. At the same time, I encouraged the team to express their individual coding styles within these boundaries, fostering a sense of ownership and creativity.
- **Merging Conflicts:** As it was the last week of the module practicals, we wanted to merge the develop into master. Unfortunately after 5 weeks of coding from such a huge team with proactive developers, we ended up with a huge diversification in coding styles and also we had some people target 'master' in their  Pull Requests, which ended up making a huge clash while trying to merge develop into master. In a large team, merge conflicts are common, especially when multiple people work on the same components. It's essential to have a clear workflow and frequent code integrations to minimize these issues. While trying to solve as many merge conflicts as possible, I really found some useful tools for the Visual Studio 2022 that I was using. 

While working on this module I also realized working in a team requires flexibility. I realized that being adaptable in accepting changes, whether in project requirements or in adapting to different coding styles, is crucial. This adaptability not only helped in maintaining the project's momentum but also in personal growth, as I learned to quickly adjust and find solutions in changing scenarios. I really had a feeling that during the last week, I learned the most. Having to deal with so many tasks in a timely manner, meant that I would really need to open up to other developers' practices and accept the different ways people think and implement their functionalities. 

I had to learn to take into account team members advices and code review feebacks. Intergrating their feedback was not only about making changes but also about understanding the rationale behind them. For me that meant learning and researching what's the better way to implement things according to the needs of the project. This practice helped me see the bigger picture and appreciate how individual contributions fit into the larger project goals.
