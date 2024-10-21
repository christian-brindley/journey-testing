# Sample Postman tests for PingOne user journeys

## Introduction

This repository contains a sample Postman collection for testing journeys in PingOne Advanced Identity Cloud (AIC). The collection should also work with journeys configured within your own deployment of the PingOne identity platform.

You may have arrived here via the [related blog article](https://medium.com/@christian.brindley/testing-journeys-in-pingone-advanced-identity-cloud-7e7da92c1aaf) providing more general information about testing journeys.

## Sample journey

The sample consists of a main login journey, with an inner journey applying MFA for unregistered devices. This uses simple MFA via email using a randomly generated one time password, with the option to remember the device for future logins.

### Outer journey

![Login Journey](/images/LoginJourney.png)

### Inner MFA journey

![Inner Journey - MFA](/images/LoginJourneyMFA.png)

## Sample Postman collection

The sample collection includes the following folders:

- <b>Initialisation</b> contains a request to create a test userid.
- <b>Login Journey - Unregistered Device</b> is the set of requests to run through userid/password validation, MFA via email, and device registration.
- <b>Login Journey - Registered Device</b> can be run immediately afterwards to check that device registration has worked.
- <b>Cleanup</b> removes the test user.
- <b>Logs</b> can be used to pull logs for the latest transaction ID used by journey tests

## Setup

### Import the sample journey

Use the AIC administration console to import the sample journey from the [journeys folder](/journeys). You should now see two new journeys in your tenant configuration

- Login Demo
- Login Demo - MFA

### Create an email template for MFA

The Postman request for retrieving the OTP email expects the OTP at the beginning of the email subject line.

[Create an email template](https://backstage.forgerock.com/docs/idcloud/latest/tenants/email-templates.html#create-a-new-email-template) called `otp` for use by the inner MFA journey. The template should have the email subject set as follows

```
{{object.otp}} is your verification code
```

### Import the Postman collection and environment

Start up Postman and import the collection and environment from the [postman folder](/postman).

The collection uses the [mailslurp service](mailslurp.com) to receive MFA emails over REST, but may be adapted to use any REST compliant email platform.

## Configure the Postman environment

Set the variables within the Postman environment as follows:

| Variable                 | Value                                                                                                                   | Example                                    |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| iamFQDN                  | The domain name for your AIC tenant                                                                                     | openam-demo.forgeblocks.io                 |
| testUsername             | The login username                                                                                                      | jane.doe                                   |
| testPassword             | The login password                                                                                                      | H&g88Djhi0vI                               |
| testDeviceId             | The test device ID used for the Device Profile nodes                                                                    | b52e535d-764c-4a1a-94dc-50a5e2165a93       |
| testDeviceMetadata       | The test device metadata used for the Device Profile nodes. Note that this should be a JSON string, with escaped quotes | Refer to example in the sample environment |
| logApiKey                | The log api key downloaded from the AIC administration console                                                          |                                            |
| logApiSecret             | The log api secret downloaded from the AIC administration console                                                       |                                            |
| mailSlurpInboxId         | The inbox ID taken from the mailslurp administration console. Usually the email address without the domain component.   |                                            |
| mailSlurpAPiKey          | The API key downloaded from the mailslurp administration console                                                        |                                            |
| testEmail                | The email address to use for MFA                                                                                        |                                            |
| tenantEmailSender        | The originating address used for the email sent by the journey for MFA                                                  | noreply@notexist.com                       |
| serviceAccountId         | The service account ID downloaded from the AIC administration console                                                   |                                            |
| serviceAccountPrivateKey | The full service account private key JWT downloaded from the AIC administration console                                 |                                            |

## Allow cookie access

Some of the Postman requests require access to cookies - for example to clear cookies at the start of a journey, and check cookies at the end. In order to allow this, the Postman cookie settings need to be updated to allow access to cookies for the domain name of your AIC tenant. Refer to the [Postman documentation](https://learning.postman.com/docs/sending-requests/response-data/cookies/#use-the-cookie-manager) for details.

## Run the tests

Run the collection folders in the order in which they appear in the collection to

- Create a test user
- Login for the first time and perform MFA, selecting the option to remember the device
- Login again, this time without MFA
- Delete the test user

## View the logs

Run the API request under the Logs folder to pull the logs for the last journey you tested. If there are more than 1000 records generated by the journey test, you can check the `_pagedResultsCookie` parameter and run the request again to get the next page of log messages.
