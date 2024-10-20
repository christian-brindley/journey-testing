# Sample Postman tests for PingOne user journeys

## Introduction

This repository contains a sample Postman collection for testing journeys in PingOne Advanced Identity Cloud (AIC). The collection should also work with journeys configured within your own deployment of the PingOne identity platform.

## Sample journey

The sample consists of a main login journey, with an inner journey applying MFA for unregistered devices. This uses simple MFA via email using a randomly generated one time password.

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

### Import the Postman collection and environment

Start up Postman and import the collection and environment from the [collection folder](/collection).

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

## Run the tests

Run the collection folders in the order in which they appear in the collection to

- Create a test user
- Login for the first time and perform MFA, selecting the option to remember the device
- Login again, this time without MFA
- Delete the test user

## View the logs

Run the log API request under the Logging folder to view the logs for the last journey you tested. If there are more than 1000 records generated by the journey test, you can check the `_pagedResultsCookie` parameter and run the request again to get the next page of log messages.
