# ias_approuter_sample

Minimal SAP BTP Cloud Foundry application router sample that authenticates end users with SAP Identity Authentication Service (IAS).

## What is included

- Standalone [`@sap/approuter`](https://www.npmjs.com/package/@sap/approuter) setup
- IAS-protected static UI
- Built-in `/user-api/currentUser` route to show the authenticated user
- Cloud Foundry deployment descriptor
- Sample service instance and X.509 binding configuration for IAS

## Project structure

- `/package.json` - app router dependency and start script
- `/xs-app.json` - route configuration
- `/manifest.yml` - Cloud Foundry deployment descriptor
- `/identity-service.json` - sample IAS service instance configuration
- `/x509-binding.json` - binding parameters required for IAS authentication
- `/resources` - protected static content served by the app router

## Prerequisites

- SAP BTP Cloud Foundry subaccount
- Entitlement for **Identity Authentication**
- Cloud Foundry CLI
- Node.js 20+

## Deploy to SAP BTP Cloud Foundry

1. Create the IAS service instance:

   ```bash
   cf create-service identity application ias-approuter-identity -c identity-service.json
   ```

2. Deploy the app router:

   ```bash
   cf push
   ```

   If your Cloud Foundry landscape does not honor service binding parameters from `manifest.yml`, use this fallback:

   ```bash
   cf push --no-start
   cf bind-service ias-approuter-sample ias-approuter-identity -c x509-binding.json
   cf start ias-approuter-sample
   ```

3. Open the generated route and sign in with IAS.

## How IAS authentication works here

- The catch-all route in `xs-app.json` uses `authenticationType: "ias"`.
- The app router is bound to an IAS **identity** service instance.
- The service binding uses `credential-type: "X509_GENERATED"` so the app router can perform IAS authentication with X.509 certificates.
- The sample page calls `/user-api/currentUser` to display the authenticated user details returned by the app router.

## Local development note

Running locally still requires IAS service credentials. Do not commit real credentials; instead, create local environment files such as `default-services.json` only on your machine and keep them out of version control.