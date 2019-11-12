---
title: Release notes for the Partner Center SDK for Java
description: Discover what has changed with the Partner Center SDK for Java with each release.
ms.topic: conceptual
ms.date: 11/11/2019
---

# Release

## 1.15.1 - November 2019

* Invoicing
  * [Daily Rated Usage Line Item](https://github.com/microsoft/Partner-Center-Java/blob/master/src/main/java/com/microsoft/store/partnercenter/models/invoices/DailyRatedUsageLineItem.java)
    * Added the *EntitlementId*, *EntitlementDescription*, *PCToBCExchangeRate*, *PCToBCExchangeRateDate*, *EffectiveUnitPrice*, and *RateOfPartnerEarnedCredit* properties
    * Modified the type for the *AdditionalInfo* and *Tags* properties from string to *Dictionary<String, String>*
  * [One Time Invoice Line Item](https://github.com/microsoft/Partner-Center-Java/blob/master/src/main/java/com/microsoft/store/partnercenter/models/invoices/OneTimeInvoiceLineItem.java)
    * Added the *BillableQuantity*, *MeterDescription*, *PCToBCExchangeRateDate*, *PCToBCExchangeRate*, *PriceAdjustmentDescription*, and *PricingCurrency* properties
* Products
  * Added the reservation scope operations
* Product upgrades
  * Added the ability to create a new product upgrade and get the status for active upgrades
* Usage
  * Added the ability to get meter and resource usage records
  * Removed the `SubscriptionDailyUsageRecordCollectionOperations` class due to changes with how utilization records should be obtained

## 1.14.1 - September 2019

* Dependency
  * Updated the *com.fasterxml.jackson.core.jackson-annotations* dependency from version 2.9.9 to 2.9.10
  * Updated the *com.fasterxml.jackson.core.jackson-core* dependency from version 2.9.9 to 2.9.10
  * Updated the *com.fasterxml.jackson.core.jackson-databind* dependency from version 2.9.9 to 2.9.10
  * Updated the *com.fasterxml.jackson.datatype.jackson-datatype-joda* dependency from version 2.9.9 to 2.9.10

## 1.14.0 - September 2019

* Agreements
  * Added the ability to request the Microsoft Customer Agreement
  * Removed the *AgreementType* and updated the *Agreement* and *AgreementMetaData* models to use a string for the type properties
* Auditing
  * The *Id* function in the *AuditRecord* class has been renamed to *getId*
* Authentication
  * Transitioned from [ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-java) to [MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java)
* Configuration
  * Resolved issue [#80](https://github.com/microsoft/Partner-Center-Java/issues/80) by ensuring that all buffers and streams are closed correctly.
* Customer users
  * Addressed issue [#39](https://github.com/microsoft/Partner-Center-Java/issues/39) preventing sorting from working as expected when querying customer users
* Dependencies
  * Updated the *com.microsoft.rest.client-runtime* from version 1.6.5 to 1.6.12
* Invoices
  * Both functions named By in the invoice operations class have been renamed to by
* JDK
  * Modified the target JDK from 1.7 to 1.8
* Network
  * Increased the default authentication token expiry buffer from 30 seconds to 120 seconds
* Subscriptions
  * Added the ability to activate third-party subscriptions in the integration sandbox
* Usage
  * Replaced the id and name properties with the resourceId and resourceName properties respectively
