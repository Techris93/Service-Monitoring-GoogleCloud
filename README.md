# Service-Monitoring-GoogleCloud

## Overview

Google Cloud's Service Monitoring streamlines the creation of microservice Service Level Objectives (SLOs) based on availability, latency, or custom Service Level Indicators (SLIs). Here, Service Monitoring will be used to create a 99.5% availability SLO and a corresponding alert.

## Objectives:

- Deploy a test application.
- Use Service Monitoring to create an SLO.
- Tie an alert to the SLO.
  
**Task 1. Deploy a test application**

In this task, A test application will be deployed to App Engine.

Deploy a test application to App Engine

To have something for Service Monitoring to connect to, a basic Node.js application will be deployed to App Engine standard.

1. In the Cloud Shell terminal, clone https://github.com/haggman/HelloLoggingNodeJS.git repo:
```
git clonehttps://github.com/haggman/HelloLoggingNodeJS.git
```

This repository contains a basic Node.js web application used for testing.

2. Change into the HelloLoggingNodeJS folder and open the index.js in the Cloud Shell code editor:
```
cd HelloLoggingNodeJS
edit index.js
```
4. In the cloud shell code editor, look at the app.yaml file.
App Engine standard uses this file to define the runtime required by the application.
	
5. In the cloud shell code editor, look at the package.json file.
Not only does this define the Node.js application dependencies, but it also defines the start script App Engine uses to serve requests.

![image](https://i.imgur.com/xWVm8iO.png)

6. Return to the Cloud Shell window. If the Cloud Shell is not visible, click Open Terminal.
	
7. In the Cloud Shell terminal, create a new App Engine app:
```
gcloud app create --region=us-central
```
This must be done once in each new project that is running App Engine applications. App Engine is a regional technology, thus the region switch.

8. Deploy the Hello Logging app to App Engine:
```
gcloud app deploy
```
Wait until the deploy completes before moving on.

![image](https://i.imgur.com/XBS6Iu8.png)

9. When prompted, type `y` and press `Enter`.

10. Copy the URL to your newly deployed app from the console and open it in a new browser tab.

11. Verify a Hello World! response.

![image](https://i.imgur.com/boB6bfP.png)

**Task 2. Use Service Monitoring to create an availability SLO**

In this task, the following will be performed:

- Use Service Monitoring to create an availability SLO.
- Create an alert tied to your SLO.
- Trigger the alert.
  
**Place some load on the application**

1. At the top of the Cloud Shell interface, press the Add icon to Open a new tab.

2. In the new tab, use a simple bash while loop to generate load on your application:
```
while true; \
docurl -s https://$DEVSHELL_PROJECT_ID.appspot.com/random-error \
-w '\n';sleep .1s;done
```
The loop generates ten requests per second. The URL is to the /random-error route, which generates an error about every 1000 requests, so you should see approximately 1 error every 100s.

3. Leave the loop running in its Cloud Shell tab and move on to the next step.

**Use Service Monitoring to create an availability SLO**

We have a working App Engine application that is currently throwing an error approximately every 1000 requests. Imagine we want to create an availability SLO with a target of 99.5%, and an alert that will notify us if our SLO is in danger. That's exactly what Service Monitoring makes easy.
	
 1. In the Google Cloud Console, use the Navigation menu to navigate to App Engine | Dashboard.

2. Scroll down to the Server Errors section. 

3. Use the Navigation menu to navigate to Error Reporting.

4. Use the Navigation menu to navigate to Monitoring.

It takes a moment for the monitoring workspace to create.

6. Once it loads, click Services.

7. Notice that Service Monitoring already sees your default App Engine application. If it doesn't, wait a minute and refresh the page until it appears in the table.

8. Click the default App Engine application to drill into it.

9. Click `+Create SLO` to start the new SLO dialog.

10. Select the Availability metric, leave the evaluation method set to Request-based, and then click Continue.

11. Take a moment to investigate the details the SLI details displayed, then click Continue.

12. To define the SLO, set the Period type to Rolling and the Period length to 7 days to calculate the SLO on a constantly moving 7-day window of time.

13. Set the Goal to 99.5% and the charts fill in, though it's typically difficult to see that 99.5 to 99.9 difference.

14. Click the red dashed line, and the chart will zoom in to make things easier to see.

15. Click Continue, notice the default name, and submit the new SLO by clicking Create SLO.

![image](https://i.imgur.com/98hgS1q.png)

**Investigate the new SLO and create an alert for it**

• Under the Current status of 1 SLO section, expand the new SLO and investigate the information it displays. Move between the three tabs, Service level indicator, Error budget, and Alerts firing, investigating each.

![image](https://i.imgur.com/DUdg6Pz.png)

**Create an alert tied to the availability SLO**

The SLO has been created and so far, you are well within your objective. Since the SLO target is 99.5%, and the SLI should be showing a current measurement level of about 99.9%, that means that your application is using approximately 1/5 of its error budget, so the error budget should be displaying about 80%. If you start to burn through your error budget at an unexpectedly fast rate, it would be nice for an alert to fire to let you know.

There are several ways to create an alert for an SLO in Service Monitoring.
	1. Because you are looking at the expanded SLO interface, click the Alerts firing tab and select CREATE SLO ALERT.

2. Set the Display name to Really short window test. Because you are doing a test and not setting values, that would make sense in production.
	
 3. Set the Lookback duration to 10 minutes and the burn rate threshold to 1.5.
	
 4. Click Next.

 5. Click on the drop-down arrow next to Notification Channels, then click on Manage Notification Channels.

A Notification channels page will open in a new tab.

6. Scroll down the page and click on ADD NEW for Email.
	
 7. In the Create Email Channel dialog box, enter your personal email address in the Email Address field and a Display name.
	
 8. Click on Save.
	
 9. For Who should be notified, use the Manage notification channels link to add your email address as a notification channel and select that. Remember, this link opens a new tab so close it once your email address has been added, and then Save the new alert.

10. Click on Notification Channels again, then click on the Refresh icon to get the display name you mentioned in the previous step.

11. Now, select your Display name and click OK.

12. Click Next.

13. Skip What are the steps to fix the issue? (optional) and click Save.

14. On the SLO page, switch back to the Service level indicator tab. It should not display our alert as a red dotted line.

15. Once again, clicking the line will zoom in view. In the upper-right corner of the page, click Auto Refresh so the charts update automatically.

**Trigger the alert**

Modify the application and trigger the alert.

1. Switch back to the Cloud Shell view, Open the Editor, if it's not already displayed, and re-open index.js.

2. Scroll to the /random-error route found at approximately line 126 and modify the value next to Math.random from 1000 to 20.

So instead of generating an error every 1000 requests, we are not going to get an error every 20 requests. That will drop our availability from 99.9$ to about 95%, which should trigger the alert.

![image](https://i.imgur.com/lBwd70i.png)

3. Close the Cloud Shell code editor and switch to the terminal window.

There are two tabs, one that's running the test loop and one that's standard.

5. In the standard (non-busy) tab, redeploy the change to App Engine:
```
gcloud app deploy
```

6. When prompted, type `y` and press Enter.
	
 7. Once the redeploy completes, switch to the tab running the test loop and verify the uptick in errors.
	
 8. Switch back to the Service Monitoring page and in the upper-right corner, verify a green check next to auto-refresh.
	
 9. Verify that the SLO is expanded and that the Service level indicator can be seen.

After a few minutes, the SLI value and chart should show clearly the decrease in performance down to about the 95% level. Within a few minutes, you should also receive the alert notification email.

![image](https://i.imgur.com/I89LCLr.png)

Note: You may see your error budget quickly drop disproportionately. The error budget calculation is made over the whole SLO window, which should be a rolling period of 7 days, but because you just started the application, your total dataset is very small, thus causing the SLO interface to display a much larger decrease in your error budget than is really happening.

If the problem is fixed, the error budget will rapidly fill back up and the actual budget remaining will be seen, though it might take a couple of days to show that.

## References
https://www.cloudskillsboost.google

