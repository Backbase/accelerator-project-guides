# Mobile - Strings As Public API (SAPI)
|     |     |
| --- | --- |
| **Status** | ACTIVE |
| **Owners** | @Arthur Alves @Balazs Domok @Nour Sandid @Bart Burg |
| **Contributors** |     |
| **Repository** | [https://github.com/backbase-rnd/cs-mobile-sx](https://github.com/backbase-rnd/cs-mobile-sx) |
| **Stakeholders** | [CS Mobile](https://backbase.atlassian.net/wiki/people/team/982e601a-04d4-4482-b0e5-d0e6baf02f90), [RnD Mobile](https://backbase.atlassian.net/wiki/spaces/GUIL/pages/181633130/Mobile+Guild+RnD) |
| **Objective** | A set of tools to treat mobile strings as public API.<br><br>* It’s meant to extract all localisable strings from a project's Backbase dependencies into one file.<br>    <br>* Maintain these strings as public API, tracking additions, removals, value updates and _keys_ breaking changes.<br>    <br>* This facilitates the translation/localisation of journeys on project level. |
| **Due date** | N/A |
| **Type** | INTERNAL |

## ![:blue_book:](./1f4d8.png) Concept

Localisation, on project level, involves setting strings in configuration files, which is an approach that has [been reviewed by the Mobile Guild](/wiki/spaces/GUIL/pages/3777954129 "/wiki/spaces/GUIL/pages/3777954129"). Moving forward, we’ll **officially support the traditional ways of localising projects**, through pure `.xml` (for Android) and `.strings` (for iOS) files.

_Strings As Public API_ is meant to provide a set of features that:

*   Facilitates the translation/localisation of journeys on project level;
    
*   Tracks _string keys_ additions, removals, value updates and _keys_ breaking changes with their respective level of warning/error. Forcing the use of strings as public API.
    

## ![:dart:](./1f3af.png)  Features in Scope

|     |     |     |
| --- | --- | --- |
| **Feature** | **iOS status** | **Android status** |
| [Strings extraction](/wiki/spaces/ES/pages/4227564676) | DONE | NOT STARTED |
| Strings diff | IN REFINEMENT | IN REFINEMENT |
| Strings update | NOT REFINED | NOT REFINED |

## ![:triangular_flag_on_post:](./1f6a9.png) Milestones and deadlines

|     |     |     |     |
| --- | --- | --- | --- |
| **Milestone** | **Owner** | **Deadline** | **Status** |
| **Strings extraction - iOS Support** | @Arthur Alves | Mar 10, 2023 | complete |
| **Strings extraction - Android Support** |     | Jun 30, 2023 | NOT STARTED |
| **Create SAPI as the main tool**  <br>Make executable called `sapi` and integrate the existing extractor as a command `x`. | @Arthur Alves | May 30, 2023 | IN PROGRESS |
| **String diff - iOS Support**<br><br>Compare strings against latest release on repo:<br><br>* Show keys with updated values<br>    <br>* Show values with updated keys<br>    <br>* Show removed keys<br>    <br>* Show added keys | @Arthur Alves | Jul 30, 2023 | NOT STARTED |
| **Strings update - iOS Support**<br><br>Attempt to fetch strings for a given version and:<br><br>* Add new keys to existing strings file | @Arthur Alves | Oct 30, 2023 | NOT STARTED |

## ![:link:](./1f6a9.png) Reference materials

[Configuration for strings and static assets](https://backbase.atlassian.net/wiki/spaces/GUIL/pages/3777954129)