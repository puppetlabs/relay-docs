# Analytics & Data collection 
Relay automatically collects data about how you use the service.

## What data does Relay collect? 
Relay collects the following data:
- User's name and company
- Usage events within the Relay website and application
- User locale
- Operating system
- Workflow events
- Workflow logs

## How does sharing this data benefit you? 
We exclusively use the collected data to provide better support and improve the product by identifying issues that users are running into when using the product.

## How do you safeguard this data? 
We start by excluding sensitive information, including secrets or connection information, from data collection. 

For the data we do collect, Relay uses Google Cloud Platform (GCP) for its backend service and stores unencrypted data in GCP database products: Redis, Cloud SQL, and Google Cloud Storage. 

Encrypted data is stored in Hashicorp Vault, which is configured to have a write-only trust relationship with the user-facing APIs; once encrypted, data can only be deleted or replaced and not read.

## Opting out 
During the beta period, there is no ability to opt out of data collection.
