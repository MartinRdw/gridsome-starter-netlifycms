---
title: Orchestrate a Grocery Order and Delivery Service with Zenaton (php)
date: 2019-06-14 20:08:08 -0700
slug: grocery-store-shopper-and-delivery-service
---
Let’s build a “Grocery delivery service” where a customer orders their groceries online and schedules a delivery time. Then a grocery store team member uses an app to collect the grocery items and then a courier service delivers them to the customer.



We will build this product with our business logic and combine several SaaS services to save us time. For our example, we will build several workflows communicating with each other using events.



We will write the workflows using the Zenaton PHP library and all of the jobs will be dispatched through the Zenaton agent to the Zenaton workflow engine and then executed on your workers.

For a better experience, the customer will pay only when they receive their items and as some of the items may be out of stock the customer will only be charged for items that are delivered.



The payment will be done in two steps:



authorize: When the customer places the order, we place the customer’s funds on hold for the total amount of all items ordered.

capture: When the groceries are delivered, we charge the customer for only the items that were delivered.

User stories

Customer — Places an order and selects a delivery time, then fills in their credit card and finally clicks the “checkout” button.



Shopper — Uses an app to accept a collection mission and then for each item, they report the quantity they purchased and report any items that are out of stock. When done, they hit the “collected” button.



Courier — Uses an app to accept a delivery mission, goes to the grocery store, picks up the order, and delivers it to the customer’s house. When the delivery is done they click the “dropped off” button.



So we have 3 workflows!



![](/static/images/1__3prg4mlc9hcqlbbvporfa.png)



The main workflow will be the “OrderWorkflow,” which, in addition, to running tasks, will also be responsible for triggering other workflows and coordinate all of the actions.

External events are sent by shopper and courier applications using http POST requests to some endpoints we will name “webhooks”.



Once external events are received, we prepare the corresponding internal event that we send to the main workflow: OrderWorkflow.



Let’s put them together!



When the customer ‘checks out’ on the website we use the Execute function (which is synchronous) and the “Authorize” task that locks the total amount of the order on the customer’s card.

We use the Dispatch function (which is Asynchronous) to launch the “Shopper” and “Delivery” tasks, which launch the corresponding Workflows. \[Workflows are launched by a task]

The “Broadcast new mission” task notifies the shoppers about a new collection mission.

When the shopper is finished picking up the items, they push the ‘collected’ button in the app and it triggers \[via webhook] the “collected” external event. The shopper webhook forwards this event to the shopper workflow.

This triggers the shopper workflow to send an internal event “prepared” to the order workflow.

Meanwhile, the “Create course” in the “Delivery Workflow” task creates a delivery job via API.

When the courier has delivered the groceries, he pushes the “dropped” button in the app. The delivery webhook forwards this event to the delivery workflow.

The delivery workflow sends an internal event “delivered” to the order workflow.

And last…The “Capture” task, charges the amount of the products that have been delivered to the customer.

Note how we have easily triggered tasks based on internal and external events as well as integrated multiple services all in a few lines of code:



Here is the main function of the OrderWorkflow:





Most of the tasks are pretty simple, they use the SDKs of the SaaS products, so we won’t spend time on it today, you can fake it using the sleep function for now.



Monitor the Workflow:

We can see the chronology of events (and errors) on the Zenaton dashboard.





We can also click on each task to see the detailed information and properties — where you can store objects in your programming language.



You can add any properties you want to track to your tasks or workflows. For example you can track what happened in details for the delivery part of a given order, like the request/response of the API request, … or whatever is important to store.





Zenaton dashboard:



See a realtime feed of all tasks and quickly identify any errors.

act as a logs of event

Easily visualize the sequences of events within the workflows

each step store a snapshot of the properties so useful for debugging

What can we build ?

This is the basic foundation of a product, we can now add some workflows to make them more realistic/complex



For the shopper team, we can add some mechanism of alerting escalation to ensure a good delay of picking mission acceptance.



For the delivery workflow, we can react to the network errors or challenge many delivery providers and select the cheapest one…



As we start growing, you can add some new workflows, to handle edge cases for the support team, …



There are an infinite amount of things we could do :)



Keep Shipping…

Did you notice that we spent more time on creating the logic and drawing diagrams rather than coding? Developers are already comfortable coding in their preferred language and using Zenaton we are giving them a whole lot more power.



Focus on your business and keep building things!
