# CSE-62 : Non Production Environment: Basic Setup of JUMIO Integration
|     |     |
| --- | --- |
| **Jira Ticket** | [![](https://backbase.atlassian.net/rest/api/2/universal_avatar/view/type/issuetype/avatar/17301?size=medium)CSE-62: Non Production Environment: Basic Setup of JUMIO IntegrationDone](https://backbase.atlassian.net/browse/CSE-62) |
| **Effort** |     |

1.  Refer [https://backbase.atlassian.net/l/c/nsR0zPez](https://backbase.atlassian.net/l/c/nsR0zPez)
    
2.  Request to create a JUMIO account for **non-production environment,** to be shared and used in Development, Test and Acceptance (or Staging) environments. In other words, for all non-production environments you get one JUMIO environment. - **Contact person to be added.**
    
    1.  By default an EU account is created, if otherwise is not stated.
        
    2.  Ensure to mention the region for account when in the email/ticket.
        
    3.  The non-production environment on JUMIO side is a Production Sandbox. The SLA on this environment is different than that of JUMIO Production environment.
        
        *   For the non production JUMIO Sandbox environment, there is no SLA.
            
3.  After getting the account, Admin should add project team members to it.
    
4.  Admin role need to create API Key for the team members.
    
5.  SA/BE Engineer validates API Key by using Postman. Postman Collection is available at [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources) .
    
6.  Devops/BE Engineer should configure the URL in the DevOps pipeline code. - SLA - 5-10 minutes
    
7.  **Account Configuration**
    
    1.  Lead BA requests the client for what documents are acceptable for verifications for which allowed countries. Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%F0%9F%93%9A-Additional-Resources) for the template.
        
        1.  JUMIO has a configuration of following categories for acceptable documents under each country:
            
            1.  Passport
                
            2.  Driving License
                
            3.  All other IDs
                
    2.  Admin role/Lead BE needs to configure the ‘Customize Client’ for the client’s color scheme . Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%E2%9C%85-Task-list](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2911635090/Jumio#%E2%9C%85-Task-list) for details.
        

*   270 sec (4.5 minutes) for a request to be processed in the Production Environment.
    

**Acceptance Criteria:**

– Run through the steps above & conduct it step by step to achieve the output of fully configured JUMIO account, resulting in an API key that can be hosted as a secret on our development servers.

> Final step of this exercise: please consider feedback for R&D DS.