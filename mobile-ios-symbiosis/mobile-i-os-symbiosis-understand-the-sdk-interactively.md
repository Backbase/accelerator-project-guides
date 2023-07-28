# Mobile - [iOS] Symbiosis: understand the SDK interactively
|     |     |
| --- | --- |
| **Status** | IN PROGRESS |
| **Driver** | @Samuel Cortez |
| **Contributors** | @Stanislav Volskyi |
| **Informed** | @Rafael Nascimento @Giuseppe Deraco |
| **Repo url** | [![](https://github.githubassets.com/favicon.ico)https://github.com/backbase-rnd/okr-symbiosis-ios](https://github.com/backbase-rnd/okr-symbiosis-ios) \- Connect your Github account |
| **Kanban board** | [![](https://backbase.atlassian.net/rest/api/2/universal_avatar/view/type/project/avatar/41925)Symbiosis](https://backbase.atlassian.net/jira/software/c/projects/SYM/boards/4469) |

By definition, symbiosis is a consistent connection between two beings. It’s present all around the nature and it’s fundamental for the balance of life. Humans and intestinal bacteria have this relationship as well. But how does this concept fits this initiative? The _Retail Universal App\[1\]_ acts as host which then the Symbiosis attaches itself to it, reading its values and configurations to offer unique features to the end user.

**This project has one main goal: to make the SDK easier to be fully understood.**

It is a collection of several features/tools in an app that will provide an unique experience for developers, business analysts, product owners, designers and solution architects. It is an ambitious premisse so we’ll start off progressively.

## Tech-wise, how does it work?

In short, the current approach relies on two strategies:

*   Mirror API\[2\]: reflective programming API built-in in Swift
    
*   Precompiling powered auto generated code: the interface of the SDK is read and code is generated based on its findings.
    
    *   The SDK interface is read with Sourcery\[3\]
        
    *   Through the template file an auto generated code is produced
        
    *   The auto generated code offers support to implement techniques which will then result in feature
        

----------

_\[1\] We’ll start off with this product but the current solution may be extended further to other products_

_\[2\] Mirror API:_ [![](./mobile-i-os-symbiosis-understand-the-sdk-interactively-0.png)Mirror | Apple Developer Documentation](https://developer.apple.com/documentation/swift/mirror)

_\[3\] Sourcery:_ [![](./mobile-i-os-symbiosis-understand-the-sdk-interactively-1.png)GitHub - krzysztofzablocki/Sourcery: Meta-programming for Swift, stop writing boilerplate code.](https://github.com/krzysztofzablocki/Sourcery)