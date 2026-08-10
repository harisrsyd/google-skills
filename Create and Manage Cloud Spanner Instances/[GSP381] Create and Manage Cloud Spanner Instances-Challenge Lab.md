# [GSP381] Create and Manage Cloud Spanner Instances: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/643/labs/612221)

Setup environment variable
```bash
export PROJECT_ID=$DEVSHELL_PROJECT_ID
export REGION="...."
```
## Task 1. Create a Cloud Spanner instance
```bash
gcloud spanner instances create banking-ops-instance \
--config=regional-$REGION \
--description="Banking Ops Instance example" \
--nodes=1 \
--labels=env=dev
```

## Task 2. Create a Cloud Spanner database
```bash
gcloud spanner databases create banking-ops-db \
--instance=banking-ops-instance
```

## Task 3. Create tables in your database
- Table: **Portofolio**
```bash
gcloud spanner databases ddl update banking-ops-db --instance=banking-ops-instance \
  --ddl="CREATE TABLE Portfolio (
    PortfolioId INT64 NOT NULL,
    Name STRING(MAX),
    ShortName STRING(MAX),
    PortfolioInfo STRING(MAX))
    PRIMARY KEY (PortfolioId)"
```
- Table: **Category**
```bash
gcloud spanner databases ddl update banking-ops-db --instance=banking-ops-instance \
  --ddl="CREATE TABLE Category (
    CategoryId INT64 NOT NULL,
    PortfolioId INT64 NOT NULL,
    CategoryName STRING(MAX),
    PortfolioInfo STRING(MAX))
    PRIMARY KEY (CategoryId)"
```
- Table: **Product**
```bash
gcloud spanner databases ddl update banking-ops-db --instance=banking-ops-instance \
  --ddl="CREATE TABLE Product (
    ProductId INT64 NOT NULL,
    CategoryId INT64 NOT NULL,
    PortfolioId INT64 NOT NULL,
    ProductName STRING(MAX),
    ProductAssetCode STRING(25),
    ProductClass STRING(25))
    PRIMARY KEY (ProductId)"
```
- Table: **Customer**
```bash
gcloud spanner databases ddl update banking-ops-db --instance=banking-ops-instance \
  --ddl="CREATE TABLE Customer (
    CustomerId STRING(36) NOT NULL,
    Name STRING(MAX) NOT NULL,
    Location STRING(MAX) NOT NULL)
    PRIMARY KEY (CustomerId)"
```

## Task 4. Load simple datasets into tables
- Insert into table **Portofolio**
```bash
gcloud spanner databases execute-sql banking-ops-db --instance=banking-ops-instance \
  --sql='INSERT INTO Portfolio (PortfolioId, Name, ShortName, PortfolioInfo)
  VALUES 
    (1, "Banking", "Bnkg", "All Banking Business"),
    (2, "Asset Growth", "AsstGrwth", "All Asset Focused Products"),
    (3, "Insurance", "Insurance", "All Insurance Focused Products")'
```
- Insert into table **Category**
```bash
gcloud spanner databases execute-sql banking-ops-db --instance=banking-ops-instance \
  --sql='INSERT INTO Category (CategoryId, PortfolioId, CategoryName)
  VALUES 
    (1, 1, "Cash"),
    (2, 2, "Investments - Short Return"),
    (3, 2, "Annuities"),
    (4, 3, "Life Insurance")'
```
- Insert into table **Product**
```bash
gcloud spanner databases execute-sql banking-ops-db --instance=banking-ops-instance \
  --sql='INSERT INTO Product (ProductId, CategoryId, PortfolioId, ProductName, ProductAssetCode, ProductClass)
  VALUES 
    (1, 1, 1, "Checking Account", "ChkAcct", "Banking LOB"),
    (2, 2, 2, "Mutual Fund Consumer Goods", "MFundCG", "Investment LOB"),
    (3, 3, 2, "Annuity Early Retirement", "AnnuFixed", "Investment LOB"),
    (4, 4, 3, "Term Life Insurance", "TermLife", "Insurance LOB"),
    (5, 1, 1, "Savings Account", "SavAcct", "Banking LOB"),
    (6, 1, 1, "Personal Loan", "PersLn", "Banking LOB"),
    (7, 1, 1, "Auto Loan", "AutLn", "Banking LOB"),
    (8, 4, 3, "Permanent Life Insurance", "PermLife", "Insurance LOB"),
    (9, 2, 2, "US Savings Bonds", "USSavBond", "Investment LOB")'
```

## Task 5. Load a complex dataset
- Prepare for the Dataflow Job (create bucket and a folder with empty file)
```bash
gsutil mb gs://$PROJECT_ID
touch emptyfile
gsutil cp emptyfile gs://$PROJECT_ID/tmp/emptyfile
```
- Ensure Dataflow API enabled
```bash
gcloud services disable dataflow.googleapis.com --force
gcloud services enable dataflow.googleapis.com
```
- Copy file .csv yang dibutuhkan untuk insert bulk data
```bash
gsutil cp gs://spls/gsp381/Customer_List_500.csv gs://$PROJECT_ID/Customer_List_500.csv
```
- Create manifest.json and copy it to Cloud Storage created before
```bash
cat <<EOF > manifest.json
{
  "tables": [
    {
      "table_name": "Customer",
      "file_patterns": [
        "gs://$PROJECT_ID/Customer_List_500.csv"
      ],
      "columns": [
        {"column_name": "CustomerId", "type": "STRING"},
        {"column_name": "Name", "type": "STRING"},
        {"column_name": "Location", "type": "STRING"}
      ]
    }
  ]
}
EOF

# Unggah manifest.json ke Cloud Storage
gsutil cp manifest.json gs://$PROJECT_ID/manifest.json
```
- Run Dataflow Job
```bash
gcloud dataflow jobs run import-customer-job \
    --gcs-location="gs://dataflow-templates-$REGION/latest/GCS_Text_to_Cloud_Spanner" \
    --region="$REGION" \
    --parameters \
instanceId="banking-ops-instance",\
databaseId="banking-ops-db",\
importManifest="gs://$PROJECT_ID/manifest.json"
```

## Task 6. Add a new column to an existing table
```bash
gcloud spanner databases ddl update banking-ops-db --instance=banking-ops-instance \
  --ddl='ALTER TABLE Category ADD COLUMN MarketingBudget INT64;'
```

## Congratulations!! 🎉🎉