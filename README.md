# Repay Payment Gateway Postman Collection

Postman collection for testing the Repay payment gateway API, covering credit card transactions, ACH payments, authorization flows, refunds, and various error scenarios.

## Important Links
- [API Documentation](https://gateway.repay.com/merchants/resources/integration/integration_portal.php)
- [Documented Testing Info](https://gateway.repay.com/merchants/resources/integration/integration_portal.php?#testing_information)

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Getting Started](#getting-started)
- [Test Data](#test-data)
- [Collection Structure](#collection-structure)
- [Usage Instructions](#usage-instructions)
- [Test Scenarios](#test-scenarios)
- [Environment Variables](#environment-variables)
- [Response Codes](#response-codes)
- [Important Notes](#important-notes)
- [Contributing](#contributing)
- [License](#license)

## Overview

This Postman collection provides a complete testing suite for the Repay payment gateway API. It includes pre-configured requests covering various payment scenarios, transaction types, and error conditions. The collection is designed to help developers quickly test and integrate with the Repay payment processing system.

### API Endpoint
- **Base URL**: `https://gateway.repay.com/api/transact.php`
- **Method**: POST
- **Content-Type**: `application/x-www-form-urlencoded`

## Features

- **Pre-configured Test Cases**: Comprehensive coverage of payment scenarios
- **Automatic Variable Management**: Transaction IDs are automatically captured and reused
- **Test Scripts**: Built-in validation for each response
- **Organized Structure**: Tests grouped by category for easy navigation
- **Error Simulation**: Dedicated tests for decline scenarios and error handling
- **AVS/CVV Testing**: Special test cases for address and card verification

## Getting Started

### Prerequisites

- [Postman](https://www.postman.com/downloads/) installed on your machine
- Access to Repay's test API credentials

### Installation

1. **Download the Collection**
   - Clone this repository or download the `repay-payment-gateway-collection.json` file

2. **Import into Postman**
   - Open Postman
   - Click the "Import" button
   - Select the JSON file or drag and drop it into Postman
   - The collection will appear in your Collections sidebar

3. **Configure API Credentials**
   
   You have two options for API credentials:
   
   **Option A: Use the public test key**
   - Get the test security key from [Repay's Testing Information page](https://gateway.repay.com/merchants/resources/integration/integration_portal.php?#testing_information)
   - This key is publicly available for anyone to use for testing
   
   **Option B: Use your own account's API key**
   - Log into your Repay merchant account
   - Enable TEST MODE in your account settings
   - Use your account's security key
   
   **Setting the key in Postman:**
   - In Postman, click on the collection name
   - Go to the "Variables" tab
   - Update the `security_key` variable with your chosen key

## Test Data

### Test Credit Card Numbers

| Card Type | Test Number | Expiration |
|-----------|------------|------------|
| Visa | 4111111111111111 | 10/25 |
| MasterCard | 5431111111111111 | 10/25 |
| Discover | 6011000991300009 | 10/25 |
| American Express | 341111111111111 | 10/25 |
| Diner's Club | 30205252489926 | 10/25 |
| JCB | 3541963594572595 | 10/25 |
| Maestro | 6799990100000000019 | 10/25 |

### Test ACH Account

- **Account Number**: 24413815
- **Routing Number**: 490000018

### Triggering Test Scenarios

- **Declined Transaction**: Pass an amount less than $1.00
- **Fatal Error**: Pass an invalid card number
- **AVS Match**: Pass `888` in the address1 field, `77777` for zip
- **CVV Match**: Pass `999` in the cvv field

## Collection Structure

The collection is organized into the following categories:

### 1. Credit Card Transactions
- Simple Credit Card Sale (Visa)
- MasterCard Sale with Tax and Shipping
- American Express Sale
- Discover Sale

### 2. Authorization and Capture
- Authorization Only
- Capture Previous Authorization
- Partial Capture

### 3. Refunds and Voids
- Full Refund
- Partial Refund
- Void Transaction

### 4. ACH/eCheck Transactions
- ACH Sale - Checking Account
- ACH Sale - Savings Account (Business)

### 5. Special Transactions
- Card Validation (Zero Auth)
- Standalone Credit

### 6. Error Test Cases
- Declined Transaction (Low Amount)
- Invalid Card Number
- Expired Card
- Missing Required Field
- Invalid Security Key

### 7. AVS and CVV Testing
- AVS Match Test
- AVS Mismatch Test

## Usage Instructions

### Running Individual Tests

1. Navigate to the desired test in the collection
2. Click "Send" to execute the request
3. Review the response in the Response section
4. Check the Test Results tab for validation outcomes

### Running the Entire Collection

1. Click the "Run" button on the collection
2. Select "Run Repay Payment Gateway API Tests"
3. Configure iterations and delays if needed
4. Click "Run Repay Payment Gateway API Tests"
5. Review the test results summary

### Sequential Dependencies

Some tests have dependencies on previous tests:

1. **Run a Sale transaction first** - This creates a transaction ID used for refunds and voids
2. **Run Authorization before Capture** - Capture tests require a valid auth transaction ID
3. **Check collection variables** - The collection automatically stores transaction IDs for dependent tests

## Environment Variables

The collection uses the following variables:

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `base_url` | API endpoint URL | `https://gateway.repay.com/api/transact.php` |
| `security_key` | API authentication key | `YOUR_API_KEY_HERE` (must be set manually) |
| `transaction_id` | Stores successful sale transaction ID | (auto-populated) |
| `auth_transaction_id` | Stores authorization transaction ID | (auto-populated) |

## Response Codes

The API returns the following response codes:

| Code | Meaning |
|------|---------|
| 1 | Approved |
| 2 | Declined |
| 3 | Error |

### Common Response Fields

- `response`: Response code (1, 2, or 3)
- `responsetext`: Human-readable response message
- `authcode`: Authorization code for approved transactions
- `transactionid`: Unique transaction identifier
- `avsresponse`: Address verification result code
- `cvvresponse`: CVV verification result code
- `type`: Transaction type
- `response_code`: Detailed response code

## Important Notes

### Test Mode Limitations

- **When using your own account key, ensure TEST MODE is enabled in your merchant account**
- No actual charges are processed in test mode
- Test transactions are isolated from production data
- Some features may behave differently in production

### Common Issues and Solutions

**Issue**: Authentication errors
- **Solution**: Verify the security_key is correctly set in collection variables

**Issue**: Transactions processing as live when using your own API key
- **Solution**: Ensure TEST MODE is enabled in your Repay merchant account settings

**Issue**: Transaction ID not found for refund/void
- **Solution**: Ensure a successful sale transaction was completed first

**Issue**: Capture fails with "transaction not found"
- **Solution**: Run an authorization-only transaction before attempting capture

**Issue**: Unexpected response format
- **Solution**: Check Content-Type header is set to `application/x-www-form-urlencoded`

## Transaction Types

The collection tests the following transaction types:

| Type | Description |
|------|-------------|
| `sale` | Immediate authorization and capture |
| `auth` | Authorization only (reserve funds) |
| `capture` | Capture previously authorized funds |
| `refund` | Return funds from settled transaction |
| `void` | Cancel an unsettled transaction |
| `credit` | Standalone credit to a card |
| `validate` | Validate card without charging |

## Disclaimer

This is an unofficial test collection created for development purposes. Always refer to Repay's official documentation for the most up-to-date API specifications and requirements. This collection is provided "as is" without warranty of any kind.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

---

**Note**: This collection is designed for testing purposes only. Never use test credentials or test card numbers in production environments. Always follow PCI compliance requirements when handling payment card data.
