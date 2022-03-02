---
title: How can you harness the full potential of Microsoft Power Apps?
tags: ["Power Platform", "Power Apps", "Power Automate", "Microsoft Power Platform", "Low Code", "Fusion Teams", "Model-driven App", "Citizen developer", "Pro dev", "Dataverse" ]
image: /assets/2021-11-23/header.jpg
---

Microsoft positions Power Apps as a Low Code platform for creating Apps. Power Apps is part of the [Power Platform](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/), which includes Power BI, Power Automate and Dataverse, all Low Code solutions.

On the other hand, Microsoft is also advocating for [Fusion Development teams](https://www.linkedin.com/pulse/solving-real-business-issues-low-code-fusion-how-do-joop-melchers/). These are teams that consist of citizen developers, consultants, but also .Net and Web developers. The type of developers that are normally not focused on Low Code but create High Code solutions.

In this article we look at [Microsoft Power Apps](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-apps/) and the range of possibilities it offers in its toolbox. Plus, what you can do with Low Code development and what the ‘upgrade’ paths are, including High Code solutions, to create an App that meets your requirements.
<!--more-->
## What is a Model-driven Power App?
![Model-driven App](/assets/2021-11-23/model-driven-app.png)

Let’s start with a Model-driven app. This is the most Low Code app that is available in the toolbox. As the name implies, it is driven by the model. In this context that means the data model of Dataverse. So, the model we create in Dataverse drives how our App behaves. Dataverse gives us the first extension point for our App.

A Model-driven app consists of a menu, or sitemap as it is called. Each menu item is linked to a Page. There are three kinds of pages: Dashboards, Tables and Canvas Pages. So, for each menu option you can decide what is the best kind of page to fulfill the requirements.

With Dashboards it is possible to create overviews of your data to quickly access it. Or you can add charts and graphs for a visual representation. 
Tables are direct views on your tables in Dataverse. In Dataverse you can design how the data is represented in lists and forms. Forms have some interesting extension points for custom UI controls. 

Last is the Canvas Page. You may have heard of Canvas Apps in the context of Power Apps. Canvas Pages is Canvas Apps, the only difference is that instead of a complete App it is just one page. With Canvas Pages we can create a custom UI to interact with the data. This is also the last extension point we look at. 
## What is Dataverse used for?
Dataverse used to be known as Common Data Services. Dataverse lets you securely store and manage data that's used by business applications.

I would almost say the possibilities of Dataverse are endless. There are many options you have in order to create a model that works for you. Most of these things are “No-“ or “Low-code” options. Let’s quickly cover the essentials.
### How creating a Table in Dataverse supports data integrity
The basics are of course the ability to add columns of all usual types (text, number, yes/no, combo boxes, etc.) and add relations to other tables. 

Let’s just look at the more interesting features that are available to change the behavior of your app:
1.	Calculated columns: Determine the value of a column based on other values or based on date and time. Useful to calculate ‘years active’ for example
2.	Keys: Enforce unique values in a column or a set of columns
3.	Business Rules: Easily change values or change the UI of a form when editing data
4.	Commands: Add buttons to the ribbon of a Form to take specific actions (In preview)
### The benefits of running Power Automate flows
Next you can run [Power Automate](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-automate/) flows. These Flows are triggered when records are created or changed. You can then run more complex calculations or change data in related entities as well. The big selling point of Flows is also that they can easily integrate with other systems and data sources. So you can easily extend your app with the data of other systems.

Developing flows still falls under the Low Code acronym, but it contains an expression language that is crucial to understand for most use cases of Power Automate. So some developer insight is preferred. 

The flows always run after the record has been saved, i.e. the flows runs asynchronously. So do not depend on a flow to ‘fix’ your data. If the Flow fails for some reason the data is not updated. At the moment that you save the data  it needs to be valid. 
### When and how to add Business Logic within Dataverse
Sometimes it is needed to run Business Logic synchronously. So, make changes to the data while it is being saved, e.g. when it is saved everything is in the correct state. For simple logic, Business Rules on the table can be used and will be sufficient. But for more complex scenarios we can use Plug-ins. 

Plug-ins are created using C#/.Net. They can be configured to run at different moments to execute custom code. This code runs inside Dataverse as part of its processing. This means that we can create logic that amends or validates data before a record is saved. And also prevent invalid data from being saved when something is wrong. 

Plug-ins can also be used to add new Actions to an entity. Normally you have “new”, “update”, “delete” etc. But maybe you have a specific action, like ‘Clone’, that needs to be specifically programed. That is also possible using a Plug-in. This action can then be connected to a Command button on the Ribbon of a Form. 
## How Power Apps allows us to change the UI behavior for better user experience
![PCF gallery](/assets/2021-11-23/pcf-gallery.png)

The possibilities of Dataverse give us many tools to make sure our data is OK and adds additional logic to this. However, the end user will see the UI of the App and interact with that. By default there are standard representations of data, but sometimes we want something different. Maybe we want 5 stars to indicate a rating or to integrate the address with a postal code check.
### How do you use components in Power Apps?
For that reason Microsoft has introduced the Power Apps Component Framework (PCF). With this framework it is possible to build Custom UI components. These components can then be added to your Forms. Any field on a form can be changed to another component. This gives great control over the look and feel of your app.

There are many pre-built components out there. The most important source to know is the [PCF Gallery](https://pcf.gallery/). This is a curated list of components that are ready to be downloaded and used. Even better: most of them are free to use.
### Creating your own PCF Controls
Of course, there are scenarios where you need your own custom UI element. Something that caters directly to your business needs, perhaps. Using the PCF, new UI controls can be created. In the PCF Gallery there are many open-source components, those can be used as starter for your own.

The Components are created using TypeScript (which is comparable to JavaScript), html and CSS. These are typical techniques that are used for web development. A web developer will feel right at home creating a custom component for your app.
### Adding Application Logic
Using UI Controls we can change the look, feel and behavior of one field on our form. But maybe we want to have application logic that encompasses the whole form. For example, show a warning on the form if a related entity has a problem.

In that case we can utilize JavaScript. The JavaScript can be loaded into the form and can be executed when the form is loaded or saved, or when data changes are made on the form. 
## Microsoft Canvas Pages as an alternative to model-driven apps for creating a user interface
So, we have learned two possibilities of Dataverse and how to extend the UI of model-driven Apps. But what if the Model Driven App UI is just not right for the job? Then we get into the realm of Canvas Pages.

The editor of canvas pages lets you create whatever UI you can imagine, but within limits. The Canvas toolbox consists of all kinds of standardized UI controls that can be used to build your UI. On top of that there is the expression language, [Microsoft Power Fx](https://docs.microsoft.com/en-us/power-platform/power-fx/overview), to program in. 
### PCF components in Canvas Pages
If the default UI components are insufficient to build your app it is again possible to use PCF components to extend the possibilities. In the PCF gallery you can search for components built specifically for Canvas apps. There are fewer controls for Canvas than for Model driven, so the chance is higher you will need to develop your own component sooner or later. 
## Beyond low-code – when are Custom developed pages relevant?
Last but not least is the ‘nuclear option’. Let’s imagine that with all the tools above it is not possible to create a UI with the required logic that matches your requirements. Your Model Driven app consists of many pages, probably many pages can be created using the techniques described above. But maybe there is this one complex page that cannot be done. 

In that case we can develop a custom web page. This is basically a stand-alone web app, for example hosted in Azure. We can use iframes to load our app seamlessly into the Model Driven page. Plus, Dashboard pages and Entity Forms have the possibility to add an iframe out of the box.

Using a Dashboard with your web app does not need to have context. Or Forms when the context is important. Imagine selecting a record from a list which opens in the Form where the only item is your web app. In that case the app knows which entity and record has been opened. 

Obviously now you can go all out in creating your UI and logic for the application. There may be a back-end server that can execute complex business logic. Dataverse has comprehensive APIs so the back-end can still integrate with all the data and logic that is already available there. 

Microsoft has released [Fluent UI](https://developer.microsoft.com/en-us/fluentui#/). This is a toolkit for creating web apps using React. The toolkit gives the same UI components in the same style that are available in Model driven apps. This way the look and feel of your web app can still match the overall Model-driven app.

Going this route requires full-stack developers that are capable of creating the UI, the back-end server and the integration with Dataverse. This territory is very far from Low Code where we began. 
## Conclusion
The aim of this article was to neatly illustrate the power of Power Apps. It is not just a Low Code solution for simple apps. It is a richly featured application environment. It is easy to get started using Low Code, but when that becomes a limiting factor there are always options to extend beyond those boundaries. 

Here at Prodware we always try to use the right tools from the toolbox to solve our customer’s needs. Knowing and understanding these possibilities is a big part of that solution. By choosing the right page type for the job we can create apps that completely fulfill the requirements, while using No or Low Code solutions to keep the development costs low and adding High Code parts to deliver the tricky bits of the overall solution. 

Understanding the possibilities in the toolbox of Power Apps is essential in creating world-class apps using Power Apps.
### Oh, and what are Power Apps Portals?
The third brother in the Power Apps family is Power Apps Portals. The technology of Power Apps Portals is different from Model driven and Canvas Apps. Microsoft is actively integrating the Model Driven and Canvas eco-systems. What is written here is not applicable to Power Apps Portals. Also, the goals of the two worlds are different, internal apps vs. external portal. So expect these worlds to remain separated for a while.  Let me know if you want me to cover this subject in a separate Power Platform blog.
