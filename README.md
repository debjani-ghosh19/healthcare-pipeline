Healthcare Dimensional Data Model — Databricks Lakehouse

A HIPAA-aware(Health Insurance Portability and Accountability Act) dimensional data warehouse for healthcare analytics on the Databricks Lakehouse.

All sample/test data in this repo is synthetic — no real PHI.

Overview
1. Dimensional model: 9 dimensions (patient, provider, facility, payer, diagnosis, procedure, medication, labtest, date) and 5 fact tables (encounter, encounter_diagnosis, claims, labresult, medication_order) — technically a fact constellation (multiple fact tables sharing conformed dimensions), not a single-fact star schema — built on the Medallion architecture (Bronze → Silver → Gold), fully governed by Unity Catalog. Implemented SCD2 for dim patient via hash based change detection and dim provider and SCD1 for remaining dimensions.

2. Storage access: The raw (Bronze) and conformed (Silver) Unity Catalog catalogs store their underlying data files in an Azure Data Lake Storage Gen2 (ADLS Gen2) storage account. Unity Catalog accesses this storage through an Azure Databricks Access Connector, which uses a managed identity (service principal). This managed identity must be assigned the Storage Blob Data Contributor role on the storage account; otherwise, the catalogs cannot read from or write to the storage.
3. PHI protection: Unity Catalog column masks are applied to sensitive fields such as MRN and SSN. Access is enforced based on the logged-in user using current_user(), ensuring each user sees only the data they are authorized to view from a single physical table, without maintaining separate masked copies.

4. Ingestion: Azure Data Factory pulls source files from a GitHub landing zone into ADLS Bronze.
5. Sample/test data: synthetic CSV, JSON samples for every Bronze table.
