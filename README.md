# QC-Data-Application
A Microsoft Power App for entering CT QC data,  storing data in SharePoint lists, and displaying data in Power BI dashboard


# Problem

At my workplace, we were seeing many issues with documenting QC on our CT scanners. This is a daily process and required by state regulations.

Some of the problems we were seeing with our old method of documentation were:
1. Staff forgetting to do QC
2. Staff not notifying anyone when QC values were out of target range
3. Issues with saving data in Excel files, lost data, access issues to the files.


# Action
I decided we needed a better method for documenting data and communicating issues. I looked into several options but landed on utilizing the Microsoft Power suite of apps as they were one of the most accessible suite of tools available to me at my work.

For collecting the data, decided on creating a webbased application in Power Apps. This would help reduce the issues we were having with Excel sheets perviously, especially with spreadsheets being locked or becoming corrupted with multiple users accessing them on our SharePoint site.

For storing the data, I explored a few traditional database options but utlimately landed on SharePoint lists as it was something I could easily connect to my application and also was something easily accessible for the toolset I am allowed to use at work.

For displaying the data, I decided on Power BI, again becaues it is part of the Microsoft suite and easily accessible, but also because it is a great tool for streaming and displaying data. 

Lastly, for helping with reminders for staff to do QC and for communicating values that are out of range, I used Power Automate to monitor the SharePoint list for new entires, analyze if any contain values that are out of range, and then email out an alert with details if values are outside of limits.

## Power Apps
Power apps was a good choice for this project as we previously stored most of our data on SharePoint. Power Apps has an integration with SharePoint, allowing me to drirectly embed the application into a ShaerPoint site. This is helpful for staff access and giving the ability to control access to the app, dashboard, and data.

The application allows for scanners to be grouped into several regions and then easily navigating to the site specific scanner.

![(screenshots/QC App Home Page.png)](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/QC%20App%20Home%20Page.png)

![(screenshots/Scanner Selection Screen.png)](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/Scanner%20Selection%20Screen.png)

The data entry screen allows for staff to pick dates, enter if the scanner was open or not that day, enter data, and any additional notes. It has some required fields and also will alert staff it they are trying to submit data out of range. If so, they are instructed if they still want to submit to add a note explaining the value.

![(screenshots/QC Entry App Screenshot.png)](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/QC%20Entry%20App%20Screenshot.png)

The application required me to find solutions to many interesting problems. One such was a way to have the submit button do an initial check on the value against the stored limits in a SharePoint list, make sure all required fields are filled in, notify the user if there are any issues before submitting. 

Here is an example of the code for just this submit: [Power Apps Code](https://github.com/metaphysika/QC-Data-Application/blob/main/Power%20Apps%20Code)

Lastly, the application also allows for editing previous submissions. This is useful if staff is instructed to repeat any scans and needs to update values or just correct simple data entry errors that may have occured.

## SharePoint Lists

All the data is routed to a SharePoint list named CT Daily QC. This list will track all entries. It is also where the edit screen will pull values from.

![ShaerPoint List](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/Daily%20QC%20SharePoint%20List%20Table.png)

There is also an Equipment List in SharePoint that store the informatoin for scanner/site names and their limits that both Power Apps and Power Automate can access to do their checks.

## Power BI

Poewr BI was a useful way for displaying the historical data visually. Power BI also has the ability to integrate directly into a SharePoint site, so I could embed the dashboard on the same webpage that contained the QC entry applictaion.

The dashboard just needed some straight forward visualization; it just needed a way to show the daily values as a line chart for each measure. There were some interesting challenges with making the visualization a bit more useful, though. One was finding a way to dynamically display the range of acceptable values for each measure behind the line graph. I ended up finding a way to do so by using a combo visual with a bar graph to display the acceptable range of values behind the line with the measured values.

![Dashboard](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/CT%20QC%20Dashboard%20-%20Power%20BI.png)

This visualization made it easy to quickly see any days where values were out of range and also see any days where days were missed.

## Power Automate
Lastly, I used Power Automate to monitor all entires through application. This flow monitors new items in a SharePoint list and checks if certain values exceed predefined limits. If any values exceed the limits or if a note is added, an email is sent to specified recipients with details of the submission.

![Alt text](https://github.com/metaphysika/QC-Data-Application/blob/main/screenshots/Power%20Automate%20Check%20QC%20Limits.png)

The flow consists of two main sections:

The first section checks if the "AxialWater" value is null. If it is, it checks if any of the specified conditions are true. If any condition is true, it appends the exceeded values to the "variables_ExceededValues" array.
The second section checks if the "AxialWater" value is not null. If it is not null, it checks if any of the specified conditions are true. If any condition is true, it appends the exceeded values to the "variables_ExceededValues" array.
The conditions in both sections compare the values from the trigger outputs with the corresponding upper and lower limits from the "outputs_Get_items" array. If a condition is true, it means that the value exceeds the limit. This then triggers the final email branch, which emails out an alert to staff about the value that was submitted that is out of range. The email inlcudes information on the date, scanner, submitted value, and any notes that were left by user.

I also created a second flow that checks if sites forgot to do QC. This flow was interesting because it required me to find a way to check if the site was closed on the weekends and then not send alerts for weekend days.

# Results
This application has proven very useful. My team has been able to better track when sites have values out of range. We are then able to more quickly investigate if there is a problem with the scanner that needs addressing. We also are seeing much better compliance with QC being done. If there are days that are missed, we are notified day of so we still have the chance to go and run the tests still on the same day. Lastly, we have had zero issues with data loss or file corruption like we were frequently having with the old method of using Excel files.


