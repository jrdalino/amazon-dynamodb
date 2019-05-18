# Amazon Dynamodb

## Add Data tier with DynamoDB

### Step 1.1: Prepare DynamoDB Table Schema
```
$ vi ~/environment/myproject-provider-restapi/dynamodb-table.json
```
```
{
  "TableName": "providers",
  "ProvisionedThroughput": {
    "ReadCapacityUnits": 5,
    "WriteCapacityUnits": 5
  },
  "AttributeDefinitions": [
    {
      "AttributeName": "providerId",
      "AttributeType": "S"
    },
    {
      "AttributeName": "name",
      "AttributeType": "S"
    },
    {
      "AttributeName": "email",
      "AttributeType": "S"
    }
  ],
  "KeySchema": [
    {
      "AttributeName": "providerId",
      "KeyType": "HASH"
    }
  ],
  "GlobalSecondaryIndexes": [
    {
      "IndexName": "nameIndex",
      "KeySchema": [
        {
          "AttributeName": "name",
          "KeyType": "HASH"
        },
        {
          "AttributeName": "providerId",
          "KeyType": "RANGE"
        }
      ],
      "Projection": {
        "ProjectionType": "ALL"
      },
      "ProvisionedThroughput": {
        "ReadCapacityUnits": 5,
        "WriteCapacityUnits": 5
      }
    },
    {
      "IndexName": "emailIndex",
      "KeySchema": [
        {
          "AttributeName": "email",
          "KeyType": "HASH"
        },
        {
          "AttributeName": "providerId",
          "KeyType": "RANGE"
        }
      ],
      "Projection": {
        "ProjectionType": "ALL"
      },
      "ProvisionedThroughput": {
        "ReadCapacityUnits": 5,
        "WriteCapacityUnits": 5
      }
    }
  ]
}
```

### Step 1.2: Create a DynamoDB Table
```
$ aws dynamodb create-table \
--cli-input-json file://~/environment/myproject-provider-restapi/dynamodb-table.json \
--endpoint-url http://localhost:8000
```
### Step 1.3: Prepare items to upload
```
$ cd ~/environment/myproject-provider-restapi
$ vi ~/populate-dynamodb.json
```
```
{
    "providers" : [
       {
          "PutRequest": {
             "Item": {
                "providerId" : {
                   "S": "4e53920c-505a-4a90-a694-b9300791f0ae"
                },
                "name" : {
                   "S": "Evangeline"
                },
                "email" : {
                   "S": "evangeline@gmail.com"
                },
                "phone" : {
                   "S": "+6512345678"
                }
             }
          }
       },
       {
          "PutRequest": {
             "Item": {
                "providerId" : {
                    "S": "2b473002-36f8-4b87-954e-9a377e0ccbec"
                 },
                 "name" : {
                    "S": "Pauly"
                 },
                 "email" : {
                    "S": "pauly@gmail.com"
                 },
                 "phone" : {
                    "S": "+6512345677"
                 }
             }
          }
       }, 
       {
          "PutRequest": {
             "Item": {
                "providerId" : {
                    "S": "3f0f196c-4a7b-43af-9e29-6522a715342d"
                 },
                 "name" : {
                    "S": "Gary"
                 },
                 "email" : {
                    "S": "gary@gmail.com"
                 },
                 "phone" : {
                    "S": "+6512345676"
                 }
             }
          }
       }
    ]
 }
```

### Step 1.4: Add Items to the DynamoDB Table
```
$ aws dynamodb batch-write-item \
--request-items file://~/environment/myproject-provider-restapi/populate-dynamodb.json
--endpoint-url http://localhost:8000

$ aws dynamodb scan \
--table-name providers \
--endpoint-url http://localhost:8000
```

### Step 1.5: Modify our application code to read from this table instead of returning the static JSON file

### Step 1.6: Push Updated Backend Code into the CI/CD Pipeline
```
$ cd ~/environment/myproject-provider-restapi
$ git add .
$ git commit -m "Add new integration to DynamoDB."
$ git push
```

Note: In case MacOS Keychain Access expires - https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-https-unixes.html

## (Clean Up)
