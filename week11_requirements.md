# Week 11 Portfolio Entry

## Issue Description

This week I worked on [Issue#128](https://github.com/Software-Engineering-Red/MAUI-APP/pull/132). The stakeholders wanted to be able to request vehicles and other equipment efficiently through the MAUI-APP, ensuring that the emergency teams have the necessary resources for disaster management operations.

### 1. BooleanToReservedConverter Class

```csharp
namespace MauiApp1.Converters
{
    public class BooleanToReservedConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            return value is bool isReserved && isReserved ? "Reserved" : "";
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotSupportedException();
        }
    }
}
```
Commentary:

- SRP: This class has a single responsibility, which is to convert a boolean value to a string representation ("Reserved" or an empty string). This makes the class easy to understand and maintain.
- Reusability: By implementing the IValueConverter interface, this converter can be reused across different parts of the application wherever such a conversion is needed.
- Robustness: The ConvertBack method throws a NotSupportedException, clearly indicating that reverse conversion is not supported, which prevents misuse.

### 2. ReserveButton_Clicked Method

```csharp
private async void ReserveButton_Clicked(object sender, EventArgs e)
{
    var button = sender as Button;
    if (button != null)
    {
        var equipmentId = (int)button.CommandParameter;
        var isReserved = ReserveEquipment(equipmentId);
        var message = isReserved ? "Equipment reserved for you" : "Reservation failed";
        await DisplayAlert("Reservation", message, "OK");
    }
}
```
Commentary:

- Asynchronous Programming: The use of async and await ensures that the UI remains responsive while the reservation process is ongoing.
- Type Safety and Casting: The method safely casts the sender to a Button and checks for null, which is a good practice to avoid runtime errors.
- Clear Logic Flow: The method's logic is straightforward and easy to follow, enhancing readability.

### 3. ReserveEquipment Method

```csharp
private bool ReserveEquipment(int equipmentId)
{
    var equipment = equipments.FirstOrDefault(e => e.ID == equipmentId);
    if (equipment != null && !equipment.Reserved)
    {
        equipment.Reserved = true;
        equipmentService.UpdateEquipment(equipment);
        return true;
    }
    return false;
}
```
Commentary:

- Encapsulation: The reservation logic is encapsulated in a separate method, which makes the code modular and easier to test.
- Use of LINQ: The use of LINQ (FirstOrDefault) for searching the equipment list is a concise and expressive way to handle collections.
- Defensive Programming: The method checks if the equipment is already reserved before proceeding, which is a good practice to ensure data integrity.

### Code Review and suggestions by reviewee

The feedback from my reviewee was: "Could be nice to implement this down the line." regarding one of the objects in the ConvertBack.
To address this feedback, I have implement the ConvertBack method to convert the display string back to a boolean value. This is useful as our UI allows toggling the reservation status directly, while keeping the code straightforward, ensuring that the converter doesn't become overly complex and difficult to maintain. Here's my implementation:

``` csharp
public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
{
    if (value is string stringValue)
    {
        return stringValue == "Reserved";
    }
    return false;
}
```

### Code Review as a Reviewer

This week I reviewed [Pull Request#179](https://github.com/Software-Engineering-Red/MAUI-APP/pull/179) from Issue#149. It's the first time I'm reviewing Gregory's code but he seems very experienced and his code is clear, well-written and it is created according to all good practices and following all principles for good quality programming.

### Reflection

- I spotted a huge improvement in my communicational skills, as we had some issues in our team. I had to basically navigate the team, just as a proejct owner would have to do in a commercial organization. This week we had serious issues with communications. The practice of doing frequent meetings didn't work as simply we were just 4 people in class, while others were absolutely inactive in our Discord server that is mainly used to communicate about the teamwork and organization. This seriously impacted the teamwork as communications between different members was seriously worsened. As everyone has to review someone else's code and his code needs to be reviewed. During this week, we basically had to declare whose of us are going to commit anything to the project at all and split duos amongst these people. I did try to help as much as possible to team members that suffered from the fact there weren't enough proactive people. 
- Also following our team's workflow and rules I deepened my comfort with the team-approved processes we follow with each commit and issue. Through this deepening, I have improved my time/productivity efficiency. Because I could compare it to the beginning when we started in week 8, this week I was almost twice as fast on code review, pull request and triggering the team workflow as my code review buddy.
- Unfortunately, every week lesser people attend our meetings, respond discord and are active in our teamwork. I'm telling 'Unfortunately' as in commercial environments it's crucial to develop the skills we've been developing the last few weeks and while less and less people are working on this project, the overall gained experience and knowledge appears to gradually worsen. However, I'm happy with the progress even with just 4-5 people weekly. Everyone seems to understand the principles and rules of the workflow we're following and everyone seems helpful. I really like the mindset of the team, as everyone is learning from the others.

- For a future improvement I would suggest we do a sprint planning session while giving away tasks and issues to people. This way we'll be able to plan which developer gets what, as some people are more experienced and are able to work on more impactful tasks, while people with less knowledge/experience could be given easier tasks based on their thrive to invest time or learn new things. If that happens, it might be a good idea to also support the sprint planning with a chat channel in discord for those people are not able to attend the session in-person. This way people won't be impacted by their availability.
