---
title: Automations
layout: post
lang: en
---

### Introduction to SinricPro Automations

SinricPro empowers users to create intelligent, automated routines for their smart homes by connecting devices and defining logical workflows. With its intuitive drag-and-drop automation builder, you can design custom scenarios that respond to events—whether triggered by device states, time, or environmental conditions—without writing a single line of code.

From simple tasks like turning on lights at sunset to complex sequences involving multiple devices and time-based delays, SinricPro automations make your smart home smarter and more responsive. Whether you're setting up routines for daily life or creating personalized experiences, SinricPro's automation tool offers flexibility, ease of use, and powerful functionality—all within a clean, user-friendly interface.

## Step 1: Basic Info

![Sinric Pro automation creation intro](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-info.png) 

The first step in creating an automation is to provide basic information about it.

### Fields

- **Automation Name**  
  Enter a descriptive name for your automation.  
  *Example:* `TV Time`

- **Automation Description**  
  Provide a brief description of what the automation does. This helps you remember its purpose later.  
  *Example:* `When TV is on, dim lights`

### Navigation
After filling out the basic information, click the **Next** button to proceed to the Flow configuration.

---

## Step 2: Flow Configuration

![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-flow.png) 

In this step, you define the logic of your automation by setting up starters, conditions (optional), and actions.

### Device Starters, Conditions, and Actions

This panel lists available devices that can be used as triggers or components in your automation. You can drag and drop these devices into the appropriate sections (Starter, Conditions, or Actions).

#### **Starter**

This is the event that initiates the automation. It's the "when" or "if" part that gets the process going. Examples `TV turned on`

#### **Conditions (Optional)**

These are additional checks that happen after the starter has been met. If the specified conditions are not met, the automation's actions will not execute, even if the starter has been triggered.

#### **Actions**

Actions to be taken. eg: Turn on the lights, adjust the thermostat.

 
####  Additional Triggers Based Events:

- **At a specific time**  
  *Example:* `5:30pm on a Sunday`  
  Triggers the automation at a predefined time.

  ![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-select-a-time.png)


- **At sunrise or sunset**  
  *Example:* `1hr before sunrise`  
  Triggers based on astronomical events.

  ![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-sunset-or-sunrise.png)

- **Between two times**  
  *Example:* `between 08:00 to 18:00`  
  Runs the automation only during a specified time window. (Used as a condition)

  ![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-between-two-times.png) 

- **Time delay**  

  Adds a delay between steps in the automation flow. (Used as action)

  ![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-add-time-delay.png) 

- **Push Notification**  
  Sends a push notification when triggered.

  ![Sinric Pro automation creation flow](https://help.sinric.pro/public/img/sinricpro-portal-automation-creation-push-notification.png) 

> 💡 Tip: Use these options to add time-based or contextual logic to your automation.

Once you've configured all parts of the automation:

- Click the **Save** button in the bottom-right corner to store your automation.

---

## Example Automation Flow

Let’s walk through an example:

1. **Basic Info**
   - Automation Name: `Evening Lights`
   - Automation Description: `Turn on living room lights at sunset`

2. **Flow**
   - **Starter:** Add "At sunset" from the right panel.
   - **Conditions:** None (optional).
   - **Actions:** Drag "living room light" into the Actions section and set it to turn on.

This creates a simple automation that turns on the living room lights every evening at sunset.

---

## Best Practices

- Use clear and concise names and descriptions.
- Test automations after saving to ensure they work as expected.
- Combine device events with time-based triggers for smarter home behavior.