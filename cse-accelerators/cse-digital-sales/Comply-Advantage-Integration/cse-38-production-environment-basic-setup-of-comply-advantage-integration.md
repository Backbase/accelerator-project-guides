# CSE-38 : Production Environment: Basic Setup of Comply Advantage Integration
<table data-number-column="false"><colgroup><col style="width: 164px;"><col style="width: 514px;"></colgroup><tbody><tr><td rowspan="1" colspan="1" colorname="" data-colwidth="165"><p data-renderer-start-pos="4"><strong data-renderer-mark="true">Jira Ticket</strong></p></td><td rowspan="1" colspan="1" colorname="" data-colwidth="515"><p data-renderer-start-pos="19"><span data-inline-card="true" data-card-url="https://backbase.atlassian.net/browse/CSE-38"><span class="loader-wrapper"><a href="https://backbase.atlassian.net/browse/CSE-38" tabindex="0" role="button" data-testid="inline-card-resolved-view" class="css-c5xdw eeajecn0"><span data-testid="inline-card-icon-and-title" class="css-1p7ax5 e158gagu2"><span class="css-7h21dr e158gagu6"><span class="css-klch0j e158gagu7"></span><img class="smart-link-icon css-168a27h e1bjtjov0" src="https://backbase.atlassian.net/rest/api/2/universal_avatar/view/type/issuetype/avatar/17301?size=medium" data-testid="inline-card-icon-and-title-image"></span><span class="smart-link-title-wrapper css-0 e158gagu8">CSE-38: Production Environment: Basic Setup of Comply Advantage Integration </span></span><span class="css-fuzljm e158gagu4"><span class="css-1n260w6 e158gagu3"><span class="css-zzgkh6" data-testid="inline-card-resolved-view-lozenge" style="max-width: 100%;"><span data-testid="inline-card-resolved-view-lozenge--text" class="css-1et8jlg" style="max-width: calc(200px - var(--ds-space-100, 8px));">Done</span></span></span></span></a></span></span></p></td></tr><tr><td rowspan="1" colspan="1" colorname="" data-colwidth="165"><p data-renderer-start-pos="27"><strong data-renderer-mark="true">Effort</strong></p></td><td rowspan="1" colspan="1" colorname="" data-colwidth="515"><p data-renderer-start-pos="37">&nbsp;</p></td></tr></tbody></table>

1.  Refer [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744)
    
2.  Involve the Sales Account Manager to review and sign-off on the production licensing for ComplyAdvantage. Since this step will involve the client and legal ensure that you budget enough time, it may take several weeks.
    
3.  Request to create a Comply Advantage account for **production environment** - **Contact person to be added.**
    
    1.  By default an EU account is created, if otherwise is not stated.
        
    2.  Ensure to mention the region for account when in the email/ticket.
        
4.  After getting the account, Admin should add project team members to it. Ensure that the API keys are stored securely and have been restricted to a subset of users with access to production.
    
5.  Admin role need to create API Key for the team members.
    
6.  SA/BE Engineer validates API Key by using Postman. Postman Collection is available at [https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources](https://backbase.atlassian.net/wiki/spaces/CDDS/pages/2858224744/ComplyAdvantage#%F0%9F%93%9A-Additional-Resources) .
    
7.  Devops/BE Engineer should configure the URL in the DevOps pipeline code. - SLA - 5-10 minutes
    
8.  **Account Configuration** - SA to request Comply Advantage to transfer the configuration from the non production environment to the production environment.  
    \* In some cases the non-prod environment may become production (this will depend on the customer).