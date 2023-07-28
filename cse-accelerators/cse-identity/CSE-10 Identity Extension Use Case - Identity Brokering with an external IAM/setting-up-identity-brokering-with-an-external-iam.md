# Setting up Identity brokering with an external IAM
*   1 [Introduction](#Introduction)
    *   1.1 [Time estimations](#Time-estimations)
*   2 [Setting up AzureAD](#Setting-up-AzureAD)
    *   2.1 [basic setup](#basic-setup)
    *   2.2 [creating claims](#creating-claims)
    *   2.3 [Associating user groups to claims](#Associating-user-groups-to-claims)
*   3 [Creating a new Identity Provider](#Creating-a-new-Identity-Provider)
    *   3.1 [basic setup](#basic-setup.1)
    *   3.2 [Setting up the redirect URI in AzureAD](#Setting-up-the-redirect-URI-in-AzureAD)
*   4 [Setting up a new authentication flow](#Setting-up-a-new-authentication-flow)
    *   4.1 [creating redirector flow](#creating-redirector-flow)
    *   4.2 [overriding client flow configuration](#overriding-client-flow-configuration)
    *   4.3 [Mapping attributes from token claims](#Mapping-attributes-from-token-claims)
*   5 [testing the integration](#testing-the-integration)
*   6 [Additional resources](#Additional-resources)

----------

## Introduction

Backbase clients that have a modern technology stack will quite likely have IAM (Identity and Access Management) facilities integrated in their IT infrastructure. IAM is used as a centralized entity that manages the identities of users and applications, keeping track of the devices used within the network and managing the access rights of each user and applications.

Backbase’s Identity console provides the ability to integrate with existing IAM infrastructures present within an organization. these IAM solutions can be In-house solutions or provided by a third party (Such as Microsoft, Amazon or Google). such providers can implement protocols like Security Assertion Markup Language 2.0 (SAML 2.0), Oauth or by using (the more common, complete and recommended) OpenID standard. By integrating with existing capabilities, Identity Console can be deployed with minimal overhead in an organization with IAM capabilities.

This guide details the steps needed to deploy an external IAM provider and integrating it with Identity Console.

> The scope of this guide covers setting up Microsoft’s Azure AD, other providers will follow similar steps.

### Time estimations

A basic setup of an external IAM is a very time efficient task. a simple implementation (like the one on this guide) should take no longer than one day provided access to this guide. Custom, complex mappings and rules can take up to five days.

----------

## Setting up AzureAD

### basic setup

*   create an external email address, for convenience, use [https://outlook.live.com/owa/](https://outlook.live.com/owa/)
    
*   create a new azure account using the newly created email address [![](./setting-up-identity-brokering-with-an-external-iam-0.png)Create Your Azure Free Account Today | Microsoft Azure](https://azure.microsoft.com/en-us/free/)
    
*   create a new Azure Active Directory Service
    

![](./setting-up-identity-brokering-with-an-external-iam-1.png)

*   register a new application  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-2.png)
    
*   go into the application and click on Certificate & Secrets  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-3.png)
    
*   Create a new secret  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-4.png)
    

Make sure to save the secret value as it will only be visible during creation time

![](./setting-up-identity-brokering-with-an-external-iam-5.png)

### creating claims

In order to get values from the IAM, it is necessary to map the user’s attributes into the identity token

*   In the application menu, click on Token Configuration  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-6.png)
    
*   Click on Add Optional Claim  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-7.png)
    
*   Select ID as the token type  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-8.png)
    
*   select one or more attributes to map and click save.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-9.png)
    

### Associating user groups to claims

A common use case is to give roles to users that are members of specific groups.  
  

*   check that all the needed groups are present in Azure AD  
      
    

![](./setting-up-identity-brokering-with-an-external-iam-10.png)

*   ensure that the users are enrolled in the proper groups
    

![](./setting-up-identity-brokering-with-an-external-iam-11.png)

*   add a new group claim to the Id token as shown in the image  
      
    

![](./setting-up-identity-brokering-with-an-external-iam-12.png)

The basic configuration is now complete, however the Azure application authentication service still needs a valid redirect URI, this URI will be created on the next step once the Identity provider is configured in Identity console

----------

## Creating a new Identity Provider

### basic setup

*   Select the realm where the application resides  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-13.png)
    
*   Click on Identity providers  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-14.png)
    
*   click on Add Provider and select OpenID Connect 1.0  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-15.png)
    

the list of adapters under the social category allow for any user that has an account with those providers to login into the application. That scenario is outside of the scope of this guide and should not be used for the purpose of using a third party to integrate Identity Console with a backbase client unless otherwise specified.

*   select an alias for the new adapter and make sure it is enabled.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-16.png)
    
*   Go back to the Azure application manager and click on endpoints  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-17.png)
    
*   copy the OpenID Connect metadata document URI  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-18.png)
    
*   go back to the Identity Console’s Identity Provider creation form, at the bottom of the form there’s a field to add a configuration url, named Import External IDP Config. Paste the metadata document URL there.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-19.png)
    
*   Paste the client secret created during the AzureAD setup step.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-20.png)
    
*   If done correctly, the form will be populated with the required values. if everything is properly populated, click save.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-21.png)
    

### Setting up the redirect URI in AzureAD

*   From the newly created Identity Provider, copy the redirect URI value.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-22.png)
    
*   go back to the Azure Console and click on Authentication  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-23.png)
    
*   click on add a platform  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-24.png)
    
*   Select Web  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-25.png)
    
*   paste the Endpoint URL and click on configure  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-26.png)
    

----------

## Setting up a new authentication flow

### creating redirector flow

*   In Identity Console, click on authentication  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-27.png)
    
*   in the flows tab, click new  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-28.png)
    
*   enter an Alias for the new flow and save  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-29.png)
    
*   select the newly created flow and click on new execution  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-30.png)
    
*   select the Identity Provider Redirector provider, click save.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-31.png)
    
*   on the list of executions, click on actions, config.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-32.png)
    
*   Enter the alias of the Identity provider created previously and save.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-33.png)
    
      
      
    

### overriding client flow configuration

> for the purpose of this guide, bb-web-client in the employee realm will be used. during normal configuration, where a custom flow is not necessary, this will often not be needed.

*   Click on clients on the identity console  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-34.png)
    
*   Click on the target client’s name
    
*   at the bottom of the form, under Authentication Flow Overrides, change the Browser flow to the newly created flow.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-35.png)
    

### Mapping attributes from token claims

*   click on Identity providers, click on the Identity provider to modify  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-36.png)
    
*   click on the mappers tab, and then on create.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-37.png)
    
      
      
    
*   Mapper type should be set to Atrribute Importer  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-38.png)
    

> note that this is only mapping claims to attributes. other types of mapping such as groups will require setting the mapper as a claim to role mapper type

![](./setting-up-identity-brokering-with-an-external-iam-39.png)

*   under claim name, enter the name as created in the azure console  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-40.png)
    

*   select a name for the attribute to be mapped to.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-41.png)
    

> if the name of the attribute mapped overrides one of the default attributes (username, email, et al)
> 
> the attribute will be overridden by the value provided by the provider. for example, the user name will be a random string if there’s no mapping pointing to a claim value.

it is recommended to at least map the email value and trust the email value in the identity provider settings for a smooth login experience.

----------

## testing the integration

*   In the Identity Console, click on clients.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-42.png)
    
*   click on the base url of the desired client  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-43.png)
    
*   if the configuration has been setup properly, the authentication process will redirect to the MS Azure login page and allow the user to log into the application with their azure credentials.  
      
    
    ![](./setting-up-identity-brokering-with-an-external-iam-44.png)
    

----------

## Additional resources

*   [![](./setting-up-identity-brokering-with-an-external-iam-45.png)Overview of SAML](https://developers.onelogin.com/saml)
    
*   [![](./setting-up-identity-brokering-with-an-external-iam-46.png)OAuth Community Site](https://oauth.net/)
    
*   [![](./setting-up-identity-brokering-with-an-external-iam-47.png)OpenID Foundation website](https://openid.net/)
    
*   [![](./setting-up-identity-brokering-with-an-external-iam-48.png)OpenID Connect (OIDC) on the Microsoft identity platform - Microsoft Entra](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-protocols-oidc)
    
*   [![](./setting-up-identity-brokering-with-an-external-iam-49.png)Secure your application by using OpenID Connect and Azure AD - Training](https://docs.microsoft.com/en-us/learn/modules/secure-app-with-oidc-and-azure-ad)
    
*   [Server Administration Guide](https://www.keycloak.org/docs/latest/server_admin/#_identity_broker)
    
*   [Identity Configuration for entitlements synchronisation for a brokered realm](https://backbase.atlassian.net/wiki/spaces/CSE/pages/3180332007)