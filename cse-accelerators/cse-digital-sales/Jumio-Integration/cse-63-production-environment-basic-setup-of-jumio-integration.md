# CSE-63 : Production Environment: Basic Setup of JUMIO Integration
|     |     |
| --- | --- |
| **Jira Ticket** | [![](https://backbase.atlassian.net/rest/api/2/universal_avatar/view/type/issuetype/avatar/17301?size=medium)CSE-63: Production Environment: Basic Setup of JUMIO IntegrationDone](https://backbase.atlassian.net/browse/CSE-63) |
| **Effort** |     |

1.  Refer [https://backbase.atlassian.net/l/c/nsR0zPez](https://backbase.atlassian.net/l/c/nsR0zPez)
    
2.  Involve the Sales Account Manager to review and sign-off on the production licensing for ComplyAdvantage. Since this step will involve the client and legal ensure that you budget enough time, it may take several weeks.
    
3.  Request to create a JUMIO account for **production environment** - **Contact person to be added.**
    
    1.  By default an EU account is created, if otherwise is not stated.
        
    2.  Ensure to mention the region for account when in the email/ticket.
        
    3.  The SLA to process the request is 270 sec (4.5 minutes) in the Production Environment. Refer - [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%97%93-Timelines-and-SLAs](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%97%93-Timelines-and-SLAs)
        
4.  After getting the account, Admin should add project team members to it. Ensure that the API keys are stored securely and have been restricted to a subset of users with access to production.
    
5.  Admin role need to create API Key for the team members.
    
6.  **SA/BE Engineer validates API Key by using Postman. Postman Collection is available at** [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources) **. - TBD**
    
7.  **Devops/BE Engineer should configure the URL in the DevOps pipeline code. - SLA - 5-10 minutes - TBD**
    
8.  **Account Configuration -** SA to request Comply Advantage to transfer the configuration from the non production environment to the production environment.  
    \* In some cases the non-prod environment may become production (this will depend on the customer).