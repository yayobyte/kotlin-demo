# CI/CD with GitHub actions

## Intro
All the actions in GitHub should be placed in a folder called `.github/actions`

## Secrets
Make sure you create the secrets to keep all sensitive information encrypted and ready to use in the actions like:
`${{secrets.SECRET_NAME}}`

    -name: dockerhub login\
     run: docker login -u ${{ secrets.USERNAME }} -p ${{ secrets.PASSWORD }}

## Pre-defined workflows

Login with azure action

    uses: azure/login@v1

To check out the current branch

    uses: actions/checkout@v3


## Azure Cloud
Let's deploy the code into azure. But let's install first all the dependencies

Install `azure-cli`

> brew update && brew install azure-cli

Go to Azure and configure `resource-group`

Execute next command to 

    az ad sp create-for-rbac --name "myApp" --role contributor \
    --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
    --sdk-auth
    
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    
The command should output a JSON object similar to this:
    
    {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
    }
  
Then you go to `SECRETS` in GitHub and create every key to make them available during workflow

Change the workflow file to consume the `secret credentials` and connect to `azure`

      - name: Login with azure action
        uses: azure/login@v1
        with:
          creds: '{"clientId":"${{ secrets.CLIENT_ID }}","clientSecret":"${{ secrets.CLIENT_SECRET }}","subscriptionId":"${{ secrets.SUBSCRIPTION_ID }}","tenantId":"${{ secrets.TENANT_ID }}"}'
          allow-no-subscriptions: true

Go to `azure` and create a new `kubernetes` cluster, and then you can use the command line to connect to the cluster

Next, let's configure in the workflow the connection to the cluster:

      - name: Connect to kubernetes cluster
        uses: azure/aks-set-context@v1
        with:
          creds: '{"clientId":"${{ secrets.CLIENT_ID }}","clientSecret":"${{ secrets.CLIENT_SECRET }}","subscriptionId":"${{ secrets.SUBSCRIPTION_ID }}","tenantId":"${{ secrets.TENANT_ID }}"}'
          cluster-name: githubactionskube
          resource-group: default_resource_group

