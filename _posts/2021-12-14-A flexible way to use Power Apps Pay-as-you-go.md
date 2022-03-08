---
title: "A flexible way to use Power Apps: Pay-as-you-go. What does it mean?"
tags: ["Power Platform", "Licensing", "Pay-as-you-Go", "Subscription"]
image: /assets/2021-12-14/header.png
coauthors:
  - name: Joop Melchers 
    linkedin: https://www.linkedin.com/in/joopmelchers/
  - name: Mario Arias Escalona
    linkedin: https://www.linkedin.com/in/marioariasescalona/
---
Microsoft has released a new pricing plan for [Power Apps](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-apps/) at [Ignite 2021](https://myignite.microsoft.com/home) called the [Pay-as-you-go plan](https://docs.microsoft.com/en-us/power-platform/admin/pay-as-you-go-overview). Let's delve into what this big change in Power Apps licensing entails.

Until now there were only two payment plans for Microsoft Power Apps:

![Power App Plans](/assets/2021-12-14/image1.png)

These two plans cover many of the use cases, but do not consider some common scenarios. This can lead to some businesses not wanting to embrace the use of the [Power Platform](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-apps/desk-planner/) because the plan does not fit their use.
<!--more-->
Let's imagine the following scenario to demonstrate the usefulness of the Pay-as-you-go plan more clearly:

A company, with 1000 employees, may consider creating an application where employees can request vacation. Although this application will be used by all employees of an organization, it is usually only used by a few employees each month. Let’s say there are only 200 people using it each month. Each month the employees that will request vacation will also be different people. 

Using the existing pricing plans per application, each user would need a monthly license for the application. Even when the employee does not use it that month. This results in a monthly recurring price of € 4200 per month paid upfront. This price may be seen as excessive for an application of this type.

To solve a case like this, Microsoft has released a new pricing plan called the **Pay-as-you-go plan**. Microsoft describes this plan as follows:

> The best option for companies that want the agility of paying only when a user runs an application for a monthly period. 

![Pay-as-you-go pricing](/assets/2021-12-14/image2.png)

Let’s use the Pay-as-you-go plan to license the use of the same holiday request app. We established that about 200 users will use the app each month. That means you would have to pay € 1686. This is a big price difference with the other plans. If more of less employee use the App that month the cost will reflect that.

Note that the Pay-as-you-go pricing is roughly twice as expensive as the Per App plan. This means that if less then half of the expected users actually use the app each month, the pay-as-you-go plan will be cheaper.

Let's look more closely at this new pricing plan.

## Is this really low code with low cost for Microsoft Power Apps?
The first thing is that a company will be able to pay for **Power Apps** with an Azure subscription. This allows you to create and share Apps without having to pay in advance (nothing is charged until the app is used). Linking a Dataverse environment to an Azure subscription is done from the Power Platform Administration center or from Power Apps as shown below. This link is set up for all the apps in the current environment.

![Setup billing](/assets/2021-12-14/image3.png)

Once an environment is linked to an Azure subscription, App usage and any **Dataverse** or **Microsoft Power Platform** request usage above the included amounts are billed against the subscription using **Azure** meters (see below).

![Azure subcription](/assets/2021-12-14/image4.png)

The element that relates the different environments to the Azure subscription is called the **billing policy**. When you create a billing policy, an accompanying Azure resource is also created; called the Power Platform account resource. When an environment is linked to Azure through a billing policy, it becomes a **Pay-as-you-go environment**. Note that an environment can only be linked to one billing policy at a time. You can change the policy that an environment is linked to or remove it from a billing policy at any time.
 
![Setup billing complete](/assets/2021-12-14/image5.png)

## The detail of Microsoft Power Apps Pay-as-you-go - Payment meters
There are three meters in Azure for the Pay-a-you-go plan:
1. **Power Apps**: The Power Apps per app meter allows users to use any type of app without the need for a Power Apps license. The Power Apps per app meter provides access to canvas apps, model-driven apps and portals for authenticated users. It measures the total number of unique monthly active users of each App or portal in a pay-per-use environment.

   An **active user** is considered as someone who opens an App or portal **at least once a month**. Repeated access of an app/portal in the month by the same user is not counted. **If users are running multiple applications, they will be counted as active users for each application that they run that month.**  

   If a user has a Power Apps license per user, the user is not counted in the meter. If a user has a Microsoft 365 license, which provides access to Power Apps and uses an application with standard connectors, the user is also not counted in the meter.

   Billing: € 8,43 per active user/app/month

   ![Billing users](/assets/2021-12-14/image6.png)

2. **Dataverse**: Dataverse pay-as-you-go meters allow you to pay for the use of all three Dataverse storage categories (database, log, and file). In pay-as-you-go environments, the first GB of Dataverse database storage and the first GB of file storage capacity are free. However, any log storage capacity will be billed immediately. This is because the log storage capacity is only used if you decide to enable auditing for an environment.

   Billing:
   - For databases more than 1 GB: € 41,92 GB/month
   - For file storage more than 1 GB: € 2.10 GB/month
   - For any log use: € 10.48 per GB/month

   ![Billing storage](/assets/2021-12-14/image7.png)
 
3. **Power Platform requests** (coming soon): Power Platform request entitlements are structured as daily entitlements (requests/day). In Pay-as-you-go environments, users and flows that exceed their daily entitlement will be billed to the linked Azure subscription. With the Power Apps per app meter, **you get an entitlement of 6,000 API calls per user/app/day**. Note that flows still need to be licensed with a base license (either Power Automate per user, Power Automate per flow, or Office).

   Billing: € 0.000035 per request/day above the limit.

   ![Billing API calls](/assets/2021-12-14/image8.png)

   \* Prices shown may not reflect actual price due to factors such as currency, country, and regional variance.

## Increased visibility of Power Apps usage with Billing information
Aggregated billing information is available in **Azure Cost Management**, and detailed usage breakdowns are available in the **Power Platform** admin center.

In **Azure Cost Management**, we can filter and view billing details by meter or by Azure resource. **Each billing policy corresponds to a Power Platform account resource**. Therefore, to see the charges for a specific billing policy, it will be enough to filter the Power Platform resource of interest.

![Azure billing overview](/assets/2021-12-14/image9.png)

Although Azure Cost Management can show the amount that was billed for each meter and each billing policy, it does not provide a breakdown of the environments, applications, and users that triggered the use of the meters. To obtain this information, a downloadable report is available on the **Billing Policies** page of the Power Platform admin center.

![Billing policies](/assets/2021-12-14/image10.png)
 
Below we can see an example of this report:

![Billing report](/assets/2021-12-14/image11.png)
 
## A great step forward in the Power Platform
We believe that it is a great step to continue expanding the licensing and billing scenarios in the **Power Platform**. As we said at the beginning of the article, the lack of options left some scenarios uncovered. Although the interest of the platform can be adapted to the needs of almost any organization, it may mean not using it because the licensing is not adapted to the actual use that companies are going to make of **Power Apps**.

This change is very fresh and is subject to change. It is still in the preview phase. We think that moving billing to Azure subscriptions is a successful step, since it gives much more flexibility and the possibility of scaling the price of applications for very diverse and more limited use cases.

What do you think, will this increased flexibility convince you to take the leap into the Power Platform?

Note: The original article was written in Spanish by our Prodware colleague [Mario Arias Escalona](https://www.linkedin.com/in/marioariasescalona/). Please take look at his personal blog The [*Power Platform Cave*](https://www.thepowerplatformcave.com/) for more Power Platform content. 
