# CSE-26 : Non Production Environment: Basic Setup of Comply Advantage Integration
**Description:**

<table data-number-column="false"><colgroup><col style="width: 164px;"><col style="width: 514px;"></colgroup><tbody><tr><td rowspan="1" colspan="1" colorname="" data-colwidth="165"><p data-renderer-start-pos="23"><strong data-renderer-mark="true">Jira Ticket</strong></p></td><td rowspan="1" colspan="1" colorname="" data-colwidth="515"><p data-renderer-start-pos="38"><span data-inline-card="true" data-card-url="https://backbase.atlassian.net/browse/CSE-26"><span class="loader-wrapper"><a href="https://backbase.atlassian.net/browse/CSE-26" tabindex="0" role="button" data-testid="inline-card-resolved-view" class="css-c5xdw eeajecn0"><span data-testid="inline-card-icon-and-title" class="css-1p7ax5 e158gagu2"><span class="css-7h21dr e158gagu6"><span class="css-klch0j e158gagu7"></span><img class="smart-link-icon css-168a27h e1bjtjov0" src="https://backbase.atlassian.net/rest/api/2/universal_avatar/view/type/issuetype/avatar/17301?size=medium" data-testid="inline-card-icon-and-title-image"></span><span class="smart-link-title-wrapper css-0 e158gagu8">CSE-26: Non Production Environment: Basic Setup of Comply Advantage Integration </span></span><span class="css-fuzljm e158gagu4"><span class="css-1n260w6 e158gagu3"><span class="css-zzgkh6" data-testid="inline-card-resolved-view-lozenge" style="max-width: 100%;"><span data-testid="inline-card-resolved-view-lozenge--text" class="css-1et8jlg" style="max-width: calc(200px - var(--ds-space-100, 8px));">Done</span></span></span></span></a></span></span></p></td></tr><tr><td rowspan="1" colspan="1" colorname="" data-colwidth="165"><p data-renderer-start-pos="46"><strong data-renderer-mark="true">Effort</strong></p></td><td rowspan="1" colspan="1" colorname="" data-colwidth="515"><p data-renderer-start-pos="56">&nbsp;</p></td></tr></tbody></table>

1.  Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744)
    
2.  Request to create a Comply Advantage account for **non-production environment,** to be shared and used in Development, Test and Acceptance (or Staging) environments. In other words, for all non-production environments you get one ComplyAdvantage environment. - **Contact person to be added.**
    
    1.  By default an EU account is created, if otherwise is not stated.
        
    2.  Ensure to mention the region for account when in the email/ticket.
        
3.  After getting the account, Admin should add project team members to it.
    
4.  Admin role need to create API Key for the team members.
    
5.  SA/BE Engineer validates API Key by using Postman. Postman Collection is available at [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources) .
    
6.  Devops/BE Engineer should configure the URL in the DevOps pipeline code. - SLA - 5-10 minutes
    
7.  PM to setup client onboarding call between Comply Advantage & the client. Here Comply Advantage explains the working of their system.
    
8.  Account Configuration
    
    1.  Lead BA requests the client for 100 users worth data with Name, DoB & expected results. This is required to fine tune Comply Advantage’s AI algorithm to generate sensible results eventually. Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources) for the template.
        
        1.  **Advise the client to send the data to Backbase securely.**
            
        2.  This needs to be the real data.
            
    2.  This data needs to be **securely** transmitted to Comply Advantage by Backbase. **Details of how awaited.**
        
    3.  Admin role needs to configure the Search Categories. Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#TIPS-AND-IMPORTANT-NOTES](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#TIPS-AND-IMPORTANT-NOTES) for details.
        
        1.  Switch off ‘Adverse Media’ category.
            
        2.  **BA** needs to check with the client on which search category(s) to be enabled. Details of available categories - [Comply Advantage x Backbase (AML journey)- Go to production pre-requisit | Comply Advantage documentation](https://backbase.atlassian.net/wiki/spaces/OO/pages/2796946763/Comply+Advantage+x+Backbase+AML+journey+-+WoW+production#Comply-Advantage-documentation)
            

**Acceptance Criteria:**

– Run through the steps above & conduct it step by step to achieve the output of fully configured Comply Advantage account, resulting in an API key.

> Final step of this exercise: please consider feedback for R&D DS.