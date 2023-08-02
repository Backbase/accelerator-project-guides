# Journey Migration Guide
*   1 [Introduction](#Introduction)
*   2 [Overview](#Overview)
*   3 [Migration Paths](#Migration-Paths)
    *   3.1 [Integrate Journeys in existing Widget apps](#Integrate-Journeys-in-existing-Widget-apps)
    *   3.2 [Migrate to Journey Productized apps](#Migrate-to-Journey-Productized-apps)

# Introduction

With Backbase moving to Journey-based Productized apps, Product teams are no longer introducing new features to the Widget Collection, All new features are being provided in Journeys.

We created this guide to help understand what are the current possibilities of using the latest from Backbase and gradually migrating a Widget-based application to a Journey Producized app.

# Overview

In this guide, we are going to explain the 2 major paths to how our existing customers can consume Backbase’s latest features as well as providing use cases to help make a decision on what would be the most convenient strategy to move forward.

1.  Integrate Journeys in existing Widget apps
    
2.  Migrate to Journey Productized apps (Integrate Widgets + Migrate Widgets)
    

# Migration Paths

## Integrate Journeys in existing Widget apps

In case there is a need to implement a new capability, there is a possibility that this capability is shipped by the product team as a Journey with no widget equivalent, For that, journeys can co-exist together with Widgets in a widget-based application.

#### Steps:

1.  Add the Journey dependencies to the app
    
2.  Initialize the Journey and Usecase
    
3.  Setup the navigation to the Journey from the desired source.
    

For detailed technical guidance on how to complete the integration, [refer to the community documentation](https://community.backbase.com/documentation/Retail-Banking/latest/journeys_in_widget_architecture "https://community.backbase.com/documentation/Retail-Banking/latest/journeys_in_widget_architecture")

> Note: If there is a plan to integrate more and more journeys, maybe it's best to consider migrating to Journey apps and gradually migrate widgets.

## Migrate to Journey Productized apps

When it’s time to move to Journey Productized apps and still want to use some of the OOTB/Custom Widgets that have been Implemented before, it's best to follow the phases of this path that would help you to gradually to migrate all your Custom widgets to Journeys as an end result.

When starting with the Journey productized app, you will notice how fast is it to configure the app, connect it to your environment, and get it to production with the OOTB features, which would leave you with time to implement the extra features you would like to deliver to the users, maybe most of those features are available in the Widgets that you used in the previous App. For that, we split this path into 2 phases so it helps to speed up the time to market

#### Phase 1:

It's not necessary to rewrite all the widgets at the beginning to match the architecture of the Journeys, We created a technical guide to explain how can you integrate OOTB/Custom widgets into Journey Productized apps.

In this phase, you would need to rely on a few things that are not part of the productized apps anymore

1.  CX model.json
    
2.  NavigationListener
    

#### Phase 2:

Once you are done with Phase 1 and you think it's time to migrate the Custom Widgets to journeys, we created another guide that would help you with understanding how to refactor those widgets into Journey and how to reuse the Layouts and Business logic implemented.