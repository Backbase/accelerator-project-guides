# How to: identify the order of next steps/journeys in the flow based on the user input in a prior journey/step
*   1 [Introduction](#Introduction)
*   2 [Create custom questionnaire](#Create-custom-questionnaire)
*   3 [Load custom questionnaire](#Load-custom-questionnaire)
*   4 [Save answers to the questionnaire on case data](#Save-answers-to-the-questionnaire-on-case-data)
*   5 [References](#References)

## Introduction

This is a guide on how to identify the order of next steps/journeys in the flow based on the user input in a prior journey/step.

**Practical Use Case:**

how to load a specific questionnaire based on the product selected at the beginning of the flow. The OOTB product selection journey comes with 3 default products: _**High-Rate Savings**_, _**Online Savings Account**_ and _**5-Year CDs**_.

The product selection widget can be configured to enable [_**Multiple selection**_](https://community.backbase.com/documentation/flow/2021-08/journey_widgets_product_selection_understand "https://community.backbase.com/documentation/flow/2021-08/journey_widgets_product_selection_understand") - selecting more than one product, this guide supports both single and multiple selections.

## Create custom questionnaire

In Flow, a questionnaire is basically a JSON file mapping each question with its corresponding frontend component. Flow - FE template rendering engine consumes this JSON data to render each question accordingly.

Example of `Net annual income` question:

`{ "type": "dropdown-single-select", "key": "netAnnualIncome", "templateOptions": { "label": "What is your net annual income ?", "placeholder": "Please select", "options": [ { "label": "$1M-$5M", "value": "$1M-$5M" }, { "label": "$500K-$1M", "value": "$500K-$1M" }, { "label": "$200K-$500K", "value": "$200K-$500K" }, { "label": "$50K-$200K", "value": "$50K-$200K" }, { "label": "$10K-$50K", "value": "$10K-$50K" }, { "label": "Up to 10K", "value": "Up to 10K" } ] }, "validators": { "validation": [ "required", "singleOption" ] } }`

The component is a drop-down single select component with a required validation rule.

Other components supported are:

|     |     |
| --- | --- |
| **Component** | **Type** |
| Text | input-text |
| DropDownSingleSelect | dropdown-single-select |
| ButtonGroup | button-group |
| RadioGroup | radio-group |
| Email | input-email |
| Phone | input-phone |
| Date | input-date |
| DividedDate | input-divided-date |
| Password | input-password |
| Checkbox | input-checkbox |

> Please note that the Flow template rendering engine does not support multiple select components like a checkbox group. It only supports a checkbox as a single component making it impossible to apply a validation rule to a group of checkbox items.

## Load custom questionnaire

To load the questionnaire, implement an `ActionHandler` and give it a name for example `kyc-questions-loader`.

Select questionnaire based on the product selected by the applicant:

`switch (selectedProduct.getProductName()) { case "High-Rate Savings": kycName = "kyc/HighRatedSavings.json"; break; case "Online Savings Account": kycName = "kyc/OnlineSavings.json"; break; case "5-Year CDs": kycName = "kyc/5YrCDs.json"; break; default: kycName = "kyc/kyc.json"; }`

The JSON files need to be placed in the resource folder.

Load the selected questionnaire:

`StringWriter writer = new StringWriter(); IOUtils.copy(new ClassPathResource(kycName).getInputStream(), writer, StandardCharsets.UTF_8); String json = writer.toString().replace("\n", "");`

## Save answers to the questionnaire on case data

To save the answers to case data implement another `ActionHandler` and give it a name for example `submit-kyc-data`.

Validate the submitted answers:

`for (AnswerDto answer : answers) { QuestionDto question = Optional.ofNullable(questions.get(answer.getId())) .orElseThrow(RuntimeException::new); if (question.getType() == QuestionType.SINGLE && answer.getSelectedOptions().size() > 1) { log.debug("Single answer question must have one answer only. Actual number: {}", answer.getSelectedOptions().size()); errors.add(OnboardingKycError.OUS_002); return errors; } //Exclude text component from this validation by excluding Question of type multiple if (!question.getOptions().containsAll(answer.getSelectedOptions()) && question.getType() != QuestionType.MULTIPLE) { log.debug("Answers don't match the question"); errors.add(OnboardingKycError.OUS_003); return errors; } }`

Update case data:

`applicant.setKycInformation(kycResult.getBody()); final Case updatedCase = journeyMapper.write(onboarding, null, null, caseKey.toString());`

Case data with answers to a questionnaire - HighRatedSavings.json:

`{ "isJointAccount": false, "mainApplicantEmail": "johndoe@test.com", "mainApplicant": { "firstName": "John", "lastName": "Doe", "dateOfBirth": "1990-01-01", "phoneNumberVerified": false, "email": "johndoe@test.com", "emailVerified": true, "citizenship": { "citizenshipType": "US_CITIZEN", "citizenshipReview": { "needed": false } }, "identityVerificationInitiation": { "userReference": "6f800bba-8f78-3cec-adaa-b2a7e5f4252a", "transactionReference": "e3a051f1-bdc6-4c03-b253-1d048b744499" }, "identityVerificationResult": { "verificationId": "approved", "documentStatus": "APPROVED_VERIFIED", "identitySimilarityStatus": "MATCH", "identityValidityStatus": "true", "rejectReasonCode": "", "rejectReasonDescription": "", "identityReason": "", "rejectReasonDetails": "" }, "kycInformation": { "answers": [ [ { "id": "netAnnualIncome", "label": "What is your net annual income ?", "answers": [ "Up to 10K" ] }, { "id": "totalCashLiquidAssets", "label": "Total cash and liquid assets", "answers": [ "Up to 10K" ] }, { "id": "sourcesOfIncomeSavings", "label": "What is your source of income ?", "answers": [ "Salary" ] }, { "id": "occupationTitle", "label": "Occupation Title", "answers": [ "Backend Engineer" ] }, { "id": "occupationEmployeeName", "label": "Employer Name and Address", "answers": [ "Backbase" ] } ] ] }, "agreements": [ { "policyType": "PRIVACY_POLICY", "accepted": true, "acceptedAt": [ "2021-11-08T14:17:10.891314Z" ] }, { "policyType": "TERMS_AND_CONDITIONS", "accepted": true, "acceptedAt": [ "2021-11-08T14:17:10.891423Z" ] } ], "address": { "numberAndStreet": "Second Avenue 111", "zipCode": "12345", "city": "New York", "state": "NY", "apt": "21" }, "antiMoneyLaunderingInfo": { "amlPersonApplicant": { "fullName": "John Doe", "yearOfBirth": 1990 }, "reviewNeeded": false, "reviewApproved": true, "status": "SUCCESS", "amlResult": { "matchStatus": "no_match", "shareUrl": "https://www.backbase.com", "matches": [] } }, "mambuAccountId": "5YBC12" }, "isMainApplicantFlow": true, "productSelection": { "selectedProducts": [ { "referenceId": "premium-savings", "productName": "High-Rate Savings" } ] }, "caseTitle": "john doe" }`

## References

[https://github.com/baas-devops-cse/us-onboarding-service/releases/tag/2021.08-0.5.0](https://github.com/baas-devops-cse/us-onboarding-service/releases/tag/2021.08-0.5.0 "https://github.com/baas-devops-cse/us-onboarding-service/releases/tag/2021.08-0.5.0")