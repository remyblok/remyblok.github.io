---
title: "How to: Quickly cancel multiple running Power Automate flows?"
tags: ["Power Platform",  "Power Automate", "extension", "grease monkey", "userscript" ]
image: /assets/2022-04-29/header.jpg
---
[Microsoft Power Automate](https://www.prodwaregroup.com/our-solutions/microsoft-power-platform/microsoft-power-automate/) is very powerful in creating automation flows. There are many ways to trigger your flows. But have you ever created a flow that was triggered so often that it starts breaking the system? How do you kill all such offending flows? Power Automate does not offer an easy way to cancel all your flows. I had the same issue, so I created a user script that adds a 'Cancel all Flows' button to Power Apps. This post explains how you can do it too.

![many running flows](/assets/2022-04-29/running-flows.png)
<!--more-->
## How can the Power Automate problem occur and what impact does it have?
As I said, there are many ways to trigger a Power Automate Cloud flow. I have had multiple scenario's that caused the flow to be run too much. Here are some scenarios I encountered:
- The flow is triggered by a change of a Dataverse record. A bulk update was applied to all records in a table. This caused the flow to run thousands of times, once for each record.
- Create recursive flow calls. By default, Power Automate prevents you from making recursive child flow calls. But using the Request trigger and HTTP calls you can still achieve recursing calls. At times this can be really useful. When your flow has a bug and your recursion never breaks you will end up with thousands of runs which just keep multiplying.

First thing is to turn off you flow. This won't cancel your current runs, but al least no new runs will be started.

After a while Power Automate will hit the brakes. The environment is still bound by your licenses. Running so many flows will deplete your allowance to run flows and do API calls. A warning message will be shown that your flow is now throttled. 

![Different warning messages](/assets/2022-04-29/warning-message.png)

This throttling then causes the runs to slow down to a crawl. Completing all of the thousands of flows is then almost impossible. In the end the flow will be automatically turned off, but all running instances will not stop. They will run till they are finished. 

If your flow is throttled, the cancel button may work quickly, but for the flow to actually cancel the running actions ??? it can still take a long time. So even when the page shows that there are no runs; flows may still be running until they are cancelled and consume resources.

### The manual way of attempting to fix the problem
You can cancel a flow. But each instance needs to be cancelled manually. And Each Page only shows 50 flow runs. So, you need to keep clicking 'load more' and then click the cancel button for each flow. This is obviously very time consuming, and you can easily forget to cancel one.

This is not the way.

## The solution
So, you have thousands of flows running. Microsoft does not offer any way to cancel all these flows. What now?

I had to solve this exact issue. I created a userscript that added two buttons on the toolbar in Power Automate. These buttons are added on the Run history page of each flow.

![Extra buttons](/assets/2022-04-29/buttons.png)

### Count running flows
This button just counts all running flows. If there are many flows running this will take a considerable amount of time. 

A window will pop-up indicating how many flows are accounted for. This number will steadily increase while the count is running.

![Counting](/assets/2022-04-29/count-flows-running.png)

Once it comes to an end it will give you the results. You can also click the Stop button to stop the count.

![Count complete](/assets/2022-04-29/count-flows-complete.png)

### Cancel all flows
This is the main attraction: a single click to cancel all your running instances of a flow. If there are many flows running this will take a considerable amount of time. This is compounded by the fact that only 1000 flows can be canceled every 15 minutes. 

A window will pop-up indicating how many flows have been cancelled. This number will increase in steps of 50, because this is the page size.

![Cancelling](/assets/2022-04-29/cancel-flows-running.png)

Eventually all flows will be cancelled. A message will show how many flows have been cancelled in the end.

![Cancel all Complete](/assets/2022-04-29/cancel-flows-complete.png)

So now there are no longer any running flows left in your history. As I said, flows can take a while to actually cancel their running actions. 

![No more flows](/assets/2022-04-29/no-flows.png)

## How to get the goodies
This extension has been build using a userscript. A userscript is a piece of JavaScript that is injected into a webpage. This is done using the Browser extension [Tampermonkey](https://www.tampermonkey.net/). It can be installed in every major browser.

Once you have installed Tampermonkey in your browser, you can install userscripts. In this case I installed the script `Power Automate - Cancel all runs`. The script can be found on [Greasy Fork](https://greasyfork.org/en/scripts/440454-power-automate-cancel-all-runs). This is a site with many userscripts.

When you open the page. you will be greeted with a green `Install this script` button. Once you click this Tampermonkey will ask you to install it. 

![Install Script](/assets/2022-04-29/install-script.png)

After that the script will be running whenever you are on Microsoft Power Automate site. 

## Prevention is as good as a cure too
Power Automate is very powerful, but we can misuse it and break it. Microsoft does not always offer the tools to easily handle the task at hand. Luckily with userscripts we can amend the functionality of a website. In this case a simple script to be able to cancel all runs. 

The best thing is of course to test your flows and try to prevent this action needed in the first place. For example:
- Make sure flows are turned off when doing a bulk update.
- Add a call depth counter and do not run when you get too deep. Now you cannot (easily) go too deep in recursing flows.

At Prodware we find the limits of the technology to get the most out of it. In those cases we need to be creative to solve the issues that we encounter. Using standard tools, like Tampermonkey, to extend a website's functionality is a very useful way to extend our tools. 

I hope this experience and solution will help you in your daily use of Power Automate!