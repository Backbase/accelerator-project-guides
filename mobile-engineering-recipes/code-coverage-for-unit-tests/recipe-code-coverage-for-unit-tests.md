# Recipe - Code coverage for unit tests
## What is Code Coverage?

Code coverage is the percentage of lines of your code that is being executed by your tests. This is an indicative of how safe your code is and how bug proof it can be while refactoring or adding new features. Code coverage helps in the easy maintenance of the codebase, exposure of bad code, and results in faster time to market.

Code coverage is measured using the following formula:

**Code Coverage Percentage = (number of unique lines of code executed / total number of lines of code) \* 100.**

> To measure the lines of code that are actually exercised by test runs, various criteria are taken into consideration. We have outlined below a few critical coverage criteria that companies use.
> 
> 1.  **Function Coverage** – The functions in the source code that are called and executed at least once.
>     
> 2.  **Statement Coverage** – The number of statements that have been successfully validated in the source code.
>     
> 3.  **Path Coverage** – The flows containing a sequence of controls and conditions that have worked well at least once.
>     
> 4.  **Branch or Decision Coverage** – The decision control structures (loops, for example) that have executed fine.
>     
> 5.  **Condition Coverage** – The Boolean expressions that are validated and that executes both TRUE and FALSE as per the test runs.
>     

## What to test?

When making your test strategy you should aim set your team to success by understanding what is important to test in your project and what is not. This should be discussed before Sprint 1, and should be part of the DoD (definition of done) of a user story.

You should keep in mind the following points:

*   All Backbase’s dependencies are already tested. This means that we don't need to write tests and can rely on the fact that all dependencies we use are already UI tested and/or unit tested.
    
*   You should write unit test to all the business logic of features developed by your mobile team - view model methods, use cases and utility classes should be tested
    

> As part of BB way of working, at the start of each sprint, we have a meeting called “QA Kick Off” where together with QAs we decide which scenarios should be covered by Unit tests and which by UI tests

### The Model Bank case

Given that all Backbase dependencies are already tested, any instance of ModelBank will have all its journey tested as OOTB. Additionally, ModelBank also provides a suite of UI Tests aimed to test the app as a whole, evaluating the behaviour of all the journeys altogether.

## Setting a code coverage target

It’s worth to start by mentioning that 100% of code coverage is most of times both an unrealistic and pointless goal. Often meeting this target would not be possible because of time constraints, as well as due to the fact that there are components that simply cannot be tested (private components or elements would be tested on UI test). And, spending time and effort on ways to achieve a 100% coverage would not add to the project quality. **Having 100% covered code doesn’t mean you have 100% secure tested code**. You should take in consideration the quality of the test being added.

When setting a code coverage target it is important to take into account benefit vs effort. This means that you choose the target to improve the quality of the codebase, not to please a certain metric. So, discuss with your team some of the following questions.

*   _Does it worth the effort of aiming for a high code coverage?_
    
*   _Does the client have the time allocation for implementing those tests?_
    
*   _What code coverage criteria make sense to my team?_
    

With that in mind, we recommend you to follow a guideline rather than a specific number.

|     |     |
| --- | --- |
| **Code coverarge (%)** | **Guideline** |
| 0 - 20 | Not acceptable |
| 20 - 40 | Bare minimum |
| 40 - 60 | Normal |
| 60 - 80 | Great |
| 80 - 100 | Outstanding |

### Related documentation

*   [![](./recipe-code-coverage-for-unit-tests-0.png)Code Coverage](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/07-code_coverage.html)
    
*   [![](./recipe-code-coverage-for-unit-tests-1.png)Test in Android Studio  |  Android Developers](https://developer.android.com/studio/test/test-in-android-studio)
    
*   [![](./recipe-code-coverage-for-unit-tests-2.png)Code coverage](https://en.wikipedia.org/wiki/Code_coverage)