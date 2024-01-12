# Ingesting CodeCov Test Coverage

## Overview
In this example, you will create blueprint for `codecov_coverage` that ingests all repositories and their associated coverage report from your CodeCov account. You will add some python script to make API calls to CodeCov REST API and fetch data for your account. In addition to ingesting data via REST API, you will configure webhooks to automatically update your entities in Port anytime an event occurs in your CodeCov account.


## Getting started

Log in to your Port account and create the following blueprints:

### Coverage blueprint
Create the coverage blueprint in Port [using this json file](./resources/codecov_blueprint.json)


### Running the python script

The list of variables required to run this script are:
- `PORT_CLIENT_ID` - Your Port client id
- `PORT_CLIENT_SECRET` - Your Port client secret
- `CODECOV_TOKEN` - CodeCov API access token
- `CODECOV_SERVICE_PROVIDER` - Git hosting service provider. Acceptable values are `github`, `github_enterprise`, `bitbucket`, `bitbucket_server`, `gitlab` and `gitlab_enterprise`
- `CODECOV_SERVICE_PROVIDER_ACCOUNT_NAME` - Username from the Git service provider


> **_NOTE:_** Find your Port credentials using this [guide](https://docs.getport.io/build-your-software-catalog/sync-data-to-catalog/api/#find-your-port-credentials)


To ingest data from your CodeCov account to Port, run the following commands: 

```bash
export PORT_CLIENT_ID=<ENTER CLIENT ID>
export PORT_CLIENT_SECRET=<ENTER CLIENT SECRET>
export CODECOV_TOKEN=<ENTER CODECOV TOKEN>
export CODECOV_SERVICE_PROVIDER=<ENTER CODECOV SERVICE PROVIDER>
export CODECOV_SERVICE_PROVIDER_ACCOUNT_NAME=<ENTER CODECOV SERVICE PROVIDER ACCOUNT NAME>

git clone https://github.com/port-labs/example-codecov-test-coverage.git

cd example-codecov-test-coverage

pip install -r ./requirements.txt

python app.py
```

## Port Webhook Configuration

Webhooks are a great way to receive updates from third party platforms, and in this case, CodeCov. To [create a codecov webhook notification](https://docs.codecov.com/docs/notifications), you will first need to generate a webhook URL from Port.

Follow the following steps to create a webhook:
1. Navigate to the **Builder** section in Port and click **Data source**;
2. Under **Webhook** tab, click **Custom integration**;
3. In the **basic details** tab, you will be asked to provide information about your webhook such as the `title`, `identifier` `description`, and `icon`;
4. In the **integration configuration** tab, copy and paste the [webhook configuration file](./resources/codecov_webhook_config.json) into the **Map the data from the external system into Port** form;
5. Take note of the webhook `URL` provided by Port on this page. You will need this `URL` when subscribing to events in CodeCov;
6. Test the webhook configuration mapping and click on **Save**;



## Creating CodeCov webhook notification
1. From your CodeCov account, open **Settings** and then click on the **Global YAML** tab at the left sidebar menu;
2. In the YAML editor, add the following codecov configuration to notify Port anytime an event occurs in your repositories:
Remember to replace `YOUR_PORT_WEBOOK` with the value of the `URL` you received after creating the webhook configuration in Port.

```yaml
coverage:
  notify:
    webhook:
      default:
        only_pulls: false
        url: YOUR_PORT_WEBHOOK
```

6. Click **Save changes** to save the webhook configuration;

For more information on customizing the notification service, follow [this documentation](https://docs.codecov.com/docs/notifications#standard-notification-fields)

All set! When any changes occur in your CodeCov account, a webhook event will be triggered to the URL provided by Port. Port will then parse the events based on the mapping and subsequently update the catalog entities.