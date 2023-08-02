# How to: Include a review screen
This article aims to demonstrate the process of including a review screen in the onboarding flow. According to the user story, the user should see a summary of all the entered data before setting up their credential.

## Instructions

### Add a new step to the interactions

The first step in the implementation is to add a new interaction to the flow, meaning you have to make some changes to the service. The task is simple enough for a frontend developer to do end-to-end, but projects may decide to split the responsibilities.

Find the JSON file with the declaration of the interactions. At the moment of this implementation, the path to the file was `/main/resources/interactions/onboarding.json`.

Now you have to introduce a new step object, linking it to the flow accordingly. In our case, `identity verified` should transition to the `review` step, while `review` goes to `credentials`. Since the review step doesn’t change the data of the flow, we’re using the “empty-handler”:

`... { "name": "identity-verified", "enabled": "true", "forward": [ { "condition": "true", "step": "review" } ], "handlers": [ { "name": "fetch-co-applicant" }, { "name": "agree-to-terms" }, { "name": "empty-handler", "transitions": [ { "condition": "true", "step": "review" } ] } ] }, { "name": "review", "enabled": "true", "forward": [ { "condition": "true", "step": "credentials" } ], "handlers": [ { "name": "fetch-co-applicant" }, { "name": "agree-to-terms" }, { "name": "empty-handler", "transitions": [ { "condition": "true", "step": "credentials" } ] } ] }, { "name": "credentials", "enabled": "true", "handlers": [ { "name": "fetch-co-applicant" }, { "name": "agree-to-terms" }, { "name": "setup-credentials", "transitions": [ { "condition": "success() && actionResult.body.isJointAccount && actionResult.body.isMainApplicantFlow == true", "step": "successfully-done-co-applicant" }, { "condition": "success()", "step": "successfully-done" } ] } ] }, ...`

The new step will be available once you recompile and restart the service.

> The occupation step doesn’t return by default, so you have to add it to the onboarding model. To do that, find the `OnboardingDto.java` file and import `com.backbase.flow.application.uso.casedata.KycAnswers`, adding `private KycAnswers kycInformation` to the list of attributes.

### Create a new FE journey and add to the flow

Assuming that you have the service ready with the new step, you can now add a new journey to the web app.

Start creating the library using Backbase Schematics:

`$ npx ng generate widget --name ono-review-journey-ang`

Link the journey component to the review route adding it to the prospect model in sdlc:

`{ "extends": "container-bb-manageable-area", "children": [ { "extends": "bb-us-journey-ang", "area": "0", "children": [ { "classId": "FlowInteractionContainerComponent", "deploymentPath": "interactions", "interactionName": "onboarding", "apiVersion": "v2", "servicePath": "us-onboarding/client-api/interaction", "children": [ ... { "classId": "OnoStepContainerAngComponent", "route": "review", "interactionName": "onboarding", "children": [ { "classId": "OnoReviewJourneyAngComponent" } ] }, ...`

> If needed, you can add other preferences to the component, such the paths to the background images.

Regenerate the app model so you can see it in the Angular development server:

`$ npm run sdlc:build-model:prospect`

Add the new step to FlowInteractionCoreModule in your app module:

`import { OnoReviewJourneyAngModule } from '@backbase/ono-review-journey-ang'; const providers: any[] = environment.production ? [] : (environment.mockProviders as any); FlowInteractionCoreModule.forRoot({ actionConfig: { ... 'setup-credentials': { updateCdo: true }, 'submit-kyc-data': { updateCdo: true }, 'agree-to-terms': { updateCdo: true }, 'review': { updateCdo: false }, 'empty-handler': { updateCdo: false }, 'kyc-questions-loader': { updateCdo: false }, 'identity-verification-initiation': { updateCdo: false }, ... steps: { ... credentials: ['credentials'], welcome: ['fetch-co-applicant-data'], kyc: ['kyc'], review: ['review'], 'terms-and-conditions': ['terms-and-conditions'], 'identity-verified': ['identity-verified'], 'identity-verification': ['identity-verification'], ... OnoReviewJourneyAngModule,`

> The review step doesn’t change the data of the flow, meaning it doesn’t update CDO, which is the accumulated data. Hence, set the review action with `updateCdo: false`.

### Implementation of the review screen

For the implementation of this example, we’re injecting FlowInteractionService to the journey, which provides us the flow data.

The data is handled as an Observable called `data$`, which is mapped to the format we want it to have in our template:

`readonly data$: Observable<FieldList> = this._flowInteraction.cdo.data.pipe( map(({ firstName, middleName, lastName, dateOfBirth, email, phoneNumber, address, ssn, kycInformation }) => [ { label: 'First Name', value: firstName }, { label: 'Middle Name', value: middleName }, { label: 'Last Name', value: lastName }, { label: 'Date of Birth', value: new DatePipe(this.locale).transform(dateOfBirth) }, { label: 'Email Address', value: email }, { label: 'Phone Number', value: phoneNumber }, { label: 'Address', value: [ { label: 'Street Number and Name', value: address?.numberAndStreet }, { label: 'Apt/Suite', value: address?.apt }, { label: 'City', value: address?.city }, { label: 'State', value: address?.state }, { label: 'Zip Code', value: address?.zipCode } ] }, { label: 'SSN Number', value: ssn?.replace(/(\d{5})(\d{4})/, '*****$2') }, { label: 'Occupation', value: (kycInformation as KycInformation)?.answers[0]?.map(({ label, answers }) => ({ label, value: answers.join(', ') })) } ] ));`

Two stateless components were created to assist on the list rendering, `field-list` and `field-item`, which the data of the observable is binded using async pipe:

`<div class="card"> <div class="card-body"> <bb-us-field-list [list]="data$ | async"></bb-us-field-list> </div> </div> <div class="bb-button-bar bb-button-bar--spacing-xl"> <bb-load-button-ui [isLoading]="loading$ | async" color="primary" (click)="submitAction()" class="bb-button-bar__button">Continue </bb-load-button-ui> </div>`

Since we’re using `empty-handler` to move to the next step, call this handler on submit, moving to the next step on success:

`submitAction() { this._flowInteraction.call({ action: 'empty-handler', body: { payload: {} } }) .pipe(take(1)) .subscribe(() => { this._flowInteraction.nav.next(); }); }`

## Related articles

 

*   Page:
    
    [How-to: group steps horizontally](/wiki/spaces/CSE/pages/3333685249/How-to%3A+group+steps+horizontally)
    
*   Page:
    
    [\[Android\] How-to: Integrate a Flow Journey into the Productized App](/wiki/spaces/CSE/pages/3516760418)
    
*   Page:
    
    [How to: Enable multiple products selection](/wiki/spaces/CSE/pages/3499721196/How+to%3A+Enable+multiple+products+selection)
    
*   Page:
    
    [Add edit button to fields in Review screen](/wiki/spaces/CSE/pages/3471835233/Add+edit+button+to+fields+in+Review+screen)
    
*   Page:
    
    [How-to: add custom icons](/wiki/spaces/CSE/pages/3331981784/How-to%3A+add+custom+icons)