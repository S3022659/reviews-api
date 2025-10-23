# Azure Cosmos DB Setup CLI Commands

## 1. Login

```sh
az login
```

## 2. Create a Resource Group

```sh
az group create \
  --name reviews-lab-jc76-rg \
  --location uksouth
```

## 3. Add Cosmos Preview Extension (once)

```sh
az extension add --name cosmosdb-preview
```

## 4. Create a Cosmos Account (Serverless)

```sh
az cosmosdb create \
  --resource-group reviews-lab-jc76-rg \
  --name reviews-lab-jc76-cosmos \
  --capacity-mode Serverless \
  --backup-policy-type Periodic \
  --backup-redundancy Local
```

## 5. Create a Database

```sh
az cosmosdb sql database create \
  --resource-group reviews-lab-jc76-rg \
  --account-name reviews-lab-jc76-cosmos \
  --name reviews-db
```

## 6. Create a Container

```sh
az cosmosdb sql container create \
  --resource-group reviews-lab-jc76-rg \
  --account-name reviews-lab-jc76-cosmos \
  --database reviews-db \
  --name reviews \
  --partition-key-path "/id"
```

You have now finished provisioning a datastore for this app.

## 7. Query the URL endpoint for the Cosmos Account

```sh
az cosmosdb show \
  --resource-group reviews-lab-jc76-rg \
  --name reviews-lab-jc76-cosmos \
  --query "documentEndpoint" \
  --output tsv
```

Use the returned endpoint to point the app at its database.

### 8. Export your Cosmos key to the shell

```sh
export COSMOS_KEY=$(\
  az cosmosdb keys list \
    --resource-group reviews-lab-jc76-rg \
    --name reviews-lab-jc76-cosmos \
    --query primaryMasterKey \
    -o tsv \
)
```

With the key stored in a local env var, you can now successfully run the app locally.

## 9. Seed the database with test data

```sh
npm run seed
```

## 10. Run the web service

```sh
npm start
```

az group create \
 --name reviews-test-jc76-rg \
 --location uksouth

## 4. Create a Cosmos Account (Serverless)

```sh
az cosmosdb create \
  --resource-group reviews-test-jc76-rg \
  --name reviews-test-jc76-cosmos \
  --capacity-mode Serverless \
  --backup-policy-type Periodic \
  --backup-redundancy Local
```

## 5. Create a Database

```sh
az cosmosdb sql database create \
  --resource-group reviews-test-jc76-rg \
  --account-name reviews-test-jc76-cosmos \
  --name reviews-db
```

## 6. Create a Container

```sh
az cosmosdb sql container create \
  --resource-group reviews-test-jc76-rg \
  --account-name reviews-test-jc76-cosmos \
  --database reviews-db \
  --name reviews \
  --partition-key-path "/id"
```

az storage account create \
 --name reviewstestjc76funcstore \
 --location uksouth \
 --resource-group reviews-test-jc76-rg \
 --sku Standard_LRS

az functionapp create \
 --name reviews-test-jc76-func \
 --resource-group reviews-test-jc76-rg \
 --storage-account reviewstestjc76funcstore \
 --consumption-plan-location uksouth \
 --runtime node \
 --functions-version 4

export COSMOS_KEY=$(\
 az cosmosdb keys list \
 --resource-group reviews-lab-jc76-rg \
 --name reviews-lab-jc76-cosmos \
 --query primaryMasterKey \
 -o tsv \
)

az functionapp config appsettings set \
 --name reviews-test-jc76-func \
 --resource-group reviews-test-jc76-rg \
 --settings COSMOS_KEY=$COSMOS_KEY COSMOS_ENDPOINT="https://reviews-test-jc76-cosmos.documents.azure.com:443/ COSMOS_DATABASE_ID=$COSMOS_DATABASE_ID COSMOS_CONTAINER_ID=$COSMOS_CONTAINER_ID"

func azure functionapp publish reviews-test-jc76-func

export COSMOS_ENDPOINT="https://reviews-test-jc76-cosmos.documents.azure.com:443/"
export COSMOS_DATABASE_ID="reviews-db"
export COSMOS_CONTAINER_ID="reviews"
