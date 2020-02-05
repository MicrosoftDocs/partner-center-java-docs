---
title: Partner Center SDK for Java
description: Getting started with the Partner Center SDK for Java.
---

# Get started with development using the Partner Center SDK

This guide walks you through setting up a development environment. You will then create a customer and an order to perform some basic tasks, like assigning licenses or querying Azure utilization records. When you are done, you will be ready to start using the SDK in your own Java applications.

## Prerequisites

- A direct, or indirect provider, enrollment into the Cloud Solution Provider program. If you do not have an active enrollment, [join the Cloud Solution Provider program](https://partner.microsoft.com/cloud-solution-provider/csp-enrollment).
- [Java 8](https://developers.redhat.com/products/openjdk/download/)
- [Maven 3](https://maven.apache.org/download.cgi)

> [!NOTE]  
> If you are Control Panel Vendor (CPV) please work with your Partner Development Manager (PDM) to learn more about your options for getting access to the Partner Center API.

## Set up authentication

Partner Center supports the app only and app + user authentication flows. It is important to note that not all Partner Center operations support the app only authentication flow. Review the [Partner Center scenarios](https://docs.microsoft.com/partner-center/develop/scenarios) to learn which authentication flow is supported for each operation.

### App only authentication

Perform the following to create the resource needed to perform app only authentication.

1. Open the [App management](https://partner.microsoft.com/pcv/apiintegration/appmanagement) feature of Partner Center.
2. Click the *Register* link found under the *Web App* section.

This will create a new Azure AD application that is configured for use with the Partner Center API. Use the information available after registering an application to create the credentials object.

```java
IPartnerCredentials appCredentials = PartnerCredentials.getInstance().generateByApplicationCredentials(
    "ApplicationId"
    "ApplicationSecret",
    "AccountId");
```

Replace the *ApplicationId*, *ApplicationSecret*, and *AccountId* values from the App management page.

### App + user authentication

Perform the following to create the resource needed to perform app + user authentication.

1. Open the [App management](https://partner.microsoft.com/pcv/apiintegration/appmanagement) feature of Partner Center.
2. Click the *Register* link found under the *Native App* section.

This will create a new Azure AD application that is configured for use with the Partner Center API. Use the information available after registering an application to create the credentials object. With this type of authentication you will need a class that implements *IAadLoginHandler* interface. You can reference the [AadUserLoginHandler](https://github.com/Microsoft/Partner-Center-Java-Samples/blob/master/sdk/src/main/java/com/microsoft/store/partnercenter/samples/AadUserLoginHandler.java) class as an example.

```java
AadLoginHandler loginHandler = new AadUserLoginHandler();

IPartnerCredentials userCredentials = PartnerCredentials.getInstance().generateByUserCredentials(
    "ApplicationId",
    loginHandler.authenticate(), loginHandler);
```

Replace the *ApplicationId* value with the information from the app management page.

## Tooling

### Create a new Maven project

> [!NOTE]
> This guide uses Maven build tool to build and run the sample code, but other build tools such as Gradle also work with the Partner Center SDK.

Create a Maven project from the command line in a new directory on your system:

```bash
mkdir java-partner-test
cd java-partner-test
mvn archetype:generate -DgroupId=com.contoso -DartifactId=PartnerApp  \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

This creates a basic Maven project under the `testPartnerApp` folder. Add the following entries into the project `pom.xml` to import the libraries used in the sample code in this tutorial.

```xml
<dependency>
    <groupId>com.microsoft.store</groupId>
    <artifactId>partnercenter</artifactId>
    <version>1.14.0</version>
</dependency>
```

## Create a customer

Create a new file named `PartnerApp.java` in the project's `src/main/java/com/contoso` directory and paste in the following block of code. Update the values used to generate the credentials and any other string you see fit.

```java
package com.contoso;

import java.util.Random;

import com.microsoft.store.partnercenter.IAggregatePartner;
import com.microsoft.store.partnercenter.models.Address;
import com.microsoft.store.partnercenter.models.customers.Customer;
import com.microsoft.store.partnercenter.models.customers.CustomerBillingProfile;
import com.microsoft.store.partnercenter.models.customers.CustomerCompanyProfile;

public class PartnerApp
{
    public static void main(String[] args)
    {
        IAggregatePartner partnerOperations = PartnerCredentials.getInstance().generateByApplicationCredentials(
            "ApplicationId"
            "ApplicationSecret",
            "AccountId");

        Address address = new Address();

        address.setFirstName("Gena");
        address.setLastName("Soto");
        address.setAddressLine1("One Microsoft Way");
        address.setCity("Redmond");
        address.setState("WA");
        address.setCountry("US");
        address.setPostalCode("98052");
        address.setPhoneNumber("4255550101");

        CustomerBillingProfile billingProfile = new CustomerBillingProfile();

        billingProfile.setCulture("en-US");
        billingProfile.setEmail("gena@wingtiptoys.com");
        billingProfile.setLanguage("en");
        billingProfile.setCompanyName("Wingtip Toys" + new Random().nextInt());
        billingProfile.setDefaultAddress(address);

        CustomerCompanyProfile companyProfile = new CustomerCompanyProfile();

        companyProfile.setDomain("WingtipToys" + Math.abs(new Random().nextInt()) + ".onmicrosoft.com");

        Customer customerToCreate = new Customer();

        customerToCreate.setBillingProfile(billingProfile);
        customerToCreate.setCompanyProfile(companyProfile);

        Customer newCustomer = partnerOperations.getCustomers().create(customerToCreate);
    }
}
```

Run the sample from the command line:

```bash
mvn compile exec:java
```

When the program finishes, you will be able to verify that a new customer has been created through the [Partner Center Dashboard](https://partner.microsoft.com/pcv/dashboard/overview).

## Create an oder

To create a subscription for a customer you must submit an order. Replace the main method in `PartnerApp.java` with the below, be sure to update the credential values and customer identifier. This code will create an Azure subscription for the specified customer.

```java
public static void main(String[] args)
{
    IAggregatePartner partnerOperations = getUserPartnerOperations();
    String customerId = "CUSTOMER_ID_MAKING_THE_PURCHASE";
    String offerId =  "MS-AZR-0145P"; // If you are using the integration sandbox this offer should be MS-AZR-0146P.

    OrderLineItem lineItem = new OrderLineItem();
    lineItem.setOfferId(offerId);
    lineItem.setFriendlyName("Microsoft Azure");
    lineItem.setQuantity(1);

    List<OrderLineItem> lineItemList = new ArrayList<OrderLineItem>();
    lineItemList.add(lineItem);

    Order order = new Order();
    order.setLineItems(lineItemList);
    order.setReferenceCustomerId(customerId);

    Order createdOrder = partnerOperations.getCustomers().byId(customerId).getOrders().create(order);
}

private static IAggregatePartner getUserPartnerOperations()
{
    IAadLoginHandler loginHandler = new AadUserLoginHandler();

    IPartnerCredentials userCredentials = PartnerCredentials.getInstance().generateByUserCredentials(
            "SPECIFY-YOUR-APPLICATION-ID-HERE",
            loginHandler.authenticate(),
            loginHandler);

    return PartnerService.getInstance().createPartnerOperations(userCredentials);
}
```

> [!NOTE]
> The implementation of the *AadUserLoginHandler* class has been omitted from this documentation. You can find a sample implementation, that leverages the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code), of this class [here](https://github.com/microsoft/Partner-Center-Java-Samples/blob/master/sdk/src/main/java/com/microsoft/store/partnercenter/samples/AadUserLoginHandler.java).

Run the code as before using Maven:

```bash
mvn clean compile exec:java
```

When the program finishes, you will be able to verify it created a new Azure subscription for the specified customer using the  [Partner Center Dashboard](https://partner.microsoft.com/pcv/dashboard/overview).
