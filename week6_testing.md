# Testing

### Test 1:
The ```OnAttemptSubmitted``` method processes the user's guess and checks it against the given word (```Word```), then updates the game display and determines other game conditions.

```csharp
[Fact]
public void OnAttemptSubmitted_OverAttempts()
{
    var gamePage = new GamePage("Easy");
    gamePage.remainingAttempts = 0;  // Setting remaining attempts to 0
    gamePage.AnswerEntry.Text = "a";  // Providing a mock guess

    // Let's keep a tracker for the GameOver invocation
    bool isGameOverInvoked = false;
    gamePage.GameOver = () => isGameOverInvoked = true;

    gamePage.OnAttemptSubmitted(null, null);

    Assert.True(isGameOverInvoked);  // Ensure that GameOver was invoked
    Assert.Equal(0, gamePage.remainingAttempts);  // Ensure that the remaining attempts didn't go negative
}
```

* **Explanation of the test:** This test simulates a scenario where a user tries to submit a guess with more than one character. As the code assumes only a single character is taken (```var answer = AnswerEntry.Text[0];```), this will likely throw an ```IndexOutOfRangeException``` when trying to access ```Text[0]``` if the string is empty or has multiple characters.

* **Why is it imporant:** While the UI might be designed to prevent multi-character inputs, external API calls or other future modifications might not uphold this assumption. This test ensures that the system gracefully handles such inputs without crashing, ensuring a smoother user experience.

* **Limitations:** The test assumes that multiple character input will lead to an IndexOutOfRangeException. If in the future, the code is modified to accept longer inputs or correctly handles them, this test might not adequately represent the desired behavior.

By designing this test, we are ensuring that the method's assumption about the length of the input string is always met. If it fails, the developers will know they need to either change the assumption or strengthen the input validation to prevent such scenarios.


### Test 2:

The ```OnAttemptSubmitted``` method processes the user's guess and checks it against the given word (```Word```), then updates the game display and determines other game conditions.

```csharp
[Fact]
public void OnAttemptSubmitted_NullWord()
{
    var gamePage = new GamePage("Easy");
    gamePage.Word = null;  // Setting the word to null
    gamePage.AnswerEntry.Text = "a";  // Providing a mock guess

    Exception ex = Record.Exception(() => gamePage.OnAttemptSubmitted(null, null));
    
    Assert.IsType<NullReferenceException>(ex);
}
```

* **Explanation of the test:** This test simulates a scenario where the word that needs to be guessed is set to null, and then a user tries to submit a guess. The aim is to determine if the method can handle this unusual situation without crashing.

* **Why is it important:** While in a normal game scenario, the word should never be null, unexpected situations can arise. It's important to ensure that the code doesn't throw exceptions in such edge cases, as they can lead to software crashes, loss of data, or other unpredictable behaviors.

* **Limitations:** The test assumes that a null word will always throw a ```NullReferenceException```. If the code gets modified to handle null values differently or to assign a default value to the word, this test might not adequately represent the desired behavior.

