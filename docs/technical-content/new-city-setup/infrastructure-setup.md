---
layout: default
title: Infrastructure Setup
parent: New City Setup
grand_parent: Technical Content
---
# Infrastructure Setup

## Before you begin

* Setup an AWS Account for the infrastructure described in [Developer Resources]()
* Setup a Heroku account as a PaaS for application deployment
* Setup a CircleCI account for your pipeline
* Setup a Google Maps API Key for address auto completion functionality
* Setup an Elastic Cloud instance for Search functionality

## Cost Expectations
*Pricing Last Updated: 03/01/2021*

Github 
* **($0.00/mo)** Maintaining an instance of PDM can be supported using Github's Free tier for organizations. 
* For more info see [Github Pricing](https://github.com/pricing)

Heroku 
* Production Environment - **($72.01/mo)** Hobby level Dynos with Standard level Data Services
* Heroku Lower Environments (2) - **($31.01/month per env)** Hobby levels for both Dynos and Data Services
* For more information see [Heroku Pricing](https://www.heroku.com/pricing)

AWS
* **(~$2.50/mo)** It's "pay-as-you-go" for various AWS services, but we average about this much.
* For more information see [AWS Pricing](https://aws.amazon.com/pricing/)

Circle CI
* **(~$50.00/mo)** We use the Linux plan with 1 paid and 1 free container
* For more information see [Circle CI Pricing](https://circleci.com/pricing/)

Docker
* **($15.00/mo)** Team Plan
* For more information see [Docker Pricing](https://www.docker.com/pricing)

Elastic Cloud
* **($16.00/mo)** Standard Plan
* For more information see [Elastic Cloud Pricing](https://www.elastic.co/pricing/)

Google Maps API
* **($0.00/mo)** We use the Geocoding and Places APIs but our usage is below the monthly $200 credit and we do not pay any costs.
* For more information see [Google Maps API Pricing](https://cloud.google.com/maps-platform/pricing)

**Total Per Month** - About $217.53

## Getting Started

### Uses
* The expectation is that any infrastructure changes that need to be done in any higher environments be done using Terraform scripts.
* This guide helps new contributors get setup with Terraform so they can make changes to their application infrastructure if/when the need arises.
* You can find their scripts under the `infra/envs/<env name>` directory.

### Setting Up Local Env for Provisioning
* Install Terraform [here](https://learn.hashicorp.com/tutorials/terraform/install-cli#install-terraform)
* Install AWS-CLI  [here](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html)
* Navigate to project directory and cd into `infra/envs/<env name>`
* Run `aws configure --profile pdm-terraform`  to setup pdm-terraform profile
  * Grab the key ID and Secret Access key from your AWS account
  * Setup `us-east-1` as the default region
  * Leave the default format blank (hit enter)
* Confirm profile has been created with `aws configure list-profiles`. For more information on named profiles, look [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)
* Run `terraform init `

  **Note:** Ensure that terraform is using remote state in s3, stored in noipm-terraform bucket. If you are, you should see the following message after executing the command:
    ```
    Initializing the backend...

    Successfully configured the backend "s3"! Terraform will automatically use this backend unless the backend configuration changes.
    ```

### Manual Steps before Provisioning
* Add Secrets to AWS Secrets Manager for respective environments (e.g. ci/Env/Config, staging/Env/Config)
  ```
  AUTH0_CLIENT_SECRET
  AWS_ACCESS_KEY_ID
  AWS_BUCKET_NAME
  AWS_SECRET_ACCESS_KEY
  LANG
  NEW_RELIC_APP_NAME
  NEW_RELIC_ENABLED
  NEW_RELIC_LICENSE_KEY
  NEW_RELIC_NO_CONFIG_FILE
  NODE_ENV
  HEROKU_API_TOKEN
  ```

### Steps to Provision 
* Navigate to project directory and cd into `infra/envs/<env name>`
* Run `terraform init`
* Run `terraform plan`
* Provide the Heroku API key for the NOIPM Infrastructure account:
  * Go to https://dashboard.heroku.com/account
  * Scroll to **"API Key" Section-> Click "Reveal"**
* If everything looks right, then run `terraform apply`
* Follow previously listed steps to provide the Heroku API key
* Type "yes" to confirm provisioning the changes

### Manual Steps after Provisioning
* Create a scheduler job for restarting the Heroku Server Dynos
  * Why? Heroku automatically restarts all Dynos daily. In an effort to avoid restarts during business hours, we have created a scheduled job to manually kick off server restarts. Having this job run daily resets the timer on Heroku's automated restarts (thus allowing us to control the restart times).
  * How? 
    * Login to Heroku Dashboard
    * Click on `Heroku Scheduler` under add-ons
    * Click `Create Job`
    * Under Schedule, select "Every Day at" and choose a time which will not conflict with business hours
    * Under Command, paste the following API call: 
      * `curl -n -X DELETE https://api.heroku.com/apps/noipm-$NODE_ENV/dynos -H "Content-Type: application/json" -H "Accept: application/vnd.heroku+json; version=3" -H "Authorization: Bearer $HEROKU_API_TOKEN"`
    * Click `Save Job`
