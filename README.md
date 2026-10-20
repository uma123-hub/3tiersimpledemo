# 3tiersimpledemo
creating simple 3 tier dem0

![Uploading image.png…]()

just first create an billing alarm--so that u can easily get the notify on ur emial

Step 1 – Open CloudWatch
In the AWS Console search bar, type CloudWatch and open it.

On the left menu, select Alarms → Create Alarm.

Step 2 – Choose Metric
Click Select metric.

Navigate to Billing → Total Estimated Charges.

Choose Currency: INR (if available; otherwise USD).

Click Select metric again.

Step 3 – Define Threshold
Under Conditions, set:

Threshold type: Static

Whenever EstimatedCharges is Greater than 500

Period: 6 hours (21600 seconds)

Click Next.

Step 4 – Notification
Choose Create new topic or select an existing one (e.g., billing-alerts).

Add your email address and confirm the subscription when AWS sends the email.

Click Next.

📝 Step 5 – Name and Create
Name your alarm: AWS-Billing-500INR.

Review all settings and click Create alarm.
