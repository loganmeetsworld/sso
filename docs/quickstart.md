# Initial Setup / Operations

`SSO` is used to provide single-sign-on authentication and authorization for internal web
applications, however, there are a few one-time prerequisites that need to be handled manually.

**NOTE: all commands should be performed on your HOST OS.**

## Google OAuth Setup

Configuring `sso` requires that we first configure a Google OAuth Project.

#### Create a new Google Cloud project

Use a web browser to access the Google Cloud [Resource Manager](
https://console.developers.google.com/cloud-resource-manager). Log into your account, if you have
not already done so.

![Manage Resources](images/setup-manage_resources.jpg)

Select "Create Project". Provide the requested details (Project Name, etc), and click "Create". You
will have a notification when your project has finished being created; this should only take a
few moments.

![Notification](images/setup-notification.jpg)

#### Configure Google OAuth

Once your project has been created, click [here](
https://console.cloud.google.com/apis/credentials/consent) to configure the "OAuth consent screen"
for your project. Make sure that the name of the project you have created appears next to the words
"Google Cloud Platform" in the toolbar at the top of the website; if it does not, switch to your
project by clicking the name that appears there, and selecting the project you just created.

Fill in the field "Product name shown to users". We recommend the name `SSO Authenticator` for this
field. Hit "Save" after filling in this field.

![OAuth Consent Screen](images/setup-consent_screen.jpg)

After this, click [here](https://console.cloud.google.com/apis/credentials) to configure
credentials for the service. Click the button labeled "Create credentials", and select the option
"OAuth client ID".

![Create Credentials Dropdown](images/setup-create_credentials.jpg)

The next screen will ask you to choose an "Application Type"; select "Web application". You will be
asked to fill in three fields.
- **Name**: Any appropriate name is fine (e.g. `Dev`).
- **Authorized JavaScript origins**: We recommend leaving this field blank.
- **Authorized redirect URIs**: Add the URI of your `sso-proxy` deployment, with the path suffix
`/oauth2/callback`. For example, if your `sso-proxy` will  be accessible at the domain
`sso.example.com`, then add the URI `https://sso.example.com/oauth2/callback`.

![Credentials](images/setup-credentials.jpg)

Once all of this has been entered, click the "Create" button. A dialog box will open with two
fields, a "client ID" and a "client secret". Copy both of these values to a safe and secure
location before clicking the "OK" button to proceed. The "client ID" and "client secret" strings
will be used to configure your `sso` deployment, as described below.

## Docker and Docker Compose Provisioning

Next, third party dependencies must be installed and configuration files modified:

Setup Docker and Docker Compose on your machine by following the directions at https://docs.docker.com/compose/install/

Configure SSO
 
<flesh this out>


## Run the containers

Four containers are in the quickstart docker-compose.yml included in this repo. They are `sso_auth` and `sso_proxy`
containers, which run the `sso` code. A `nginx-proxy` container which acts as a dynamic proxy front-ending the cluster,
and lastly a `payload` container which runs the payload that's `sso` is providing auth services for.

Copy the `docker-compose.yml.example`, `env.example` and `proxy_configs.yml.example` files to new instances removing the
.example trailer on the file name.

Config those files per the instructions in http://./quickstart.md#docker-and-docker-compose-provisioning

Execute the containers by running the `quickstart.sh` script, and run a `docker ps` to see all four containers are up.

![StartScript](images/start-script.jpg)

Open the payload via your browser with the address http://payload.sso.sso-test.io

Your browser will detect that you are not authenticated and re-direct you to the sso-auth at http://sso-auth.sso-test.io/sign_in?client_id=XXXX

![StartAuth](images/start-auth.jpg)

Press the Sign In With Google button

sso-auth will detect that you are not logged in and re-direct your to a google-auth page at https://accounts.google.com/signin/oauth/oauthchooseaccount?client_id=

![ChooseAccount](images/choose-account.jpg)

Choose the account you want to use that is authorized for with sso-proxy for this payload.
 
The google-auth will redirect you back to the desired payload page http://payload.sso.sso-test.io and you will now have access.

![PayloadScreen](images/payload-screen.jpg)
 
The `sso` payloads can be changed by following the https://./sso_config.md doc to change
sso-proxy configs and then restarting the sso-proxy container.
