# Date-Time-Format-Conversion-Zoho-Deluge
A script that converts Deluge's date-time format into a format that is acceptabe for updating Zoho's date-time fields and vice-versa.

## Core Idea
Zoho demands a very specific format for updating date-time fields via Deluge. Suppose you want to update the time of function execution into a date-time field in Zoho. If you input `zoho.currenttime` into the update, it will return an `INVALID DATA` error due to formatting.

Let's take a look at the format difference between `zoho.currentime` and Zoho date-time fields.
* `zoho.currenttime` : 09-Dec-2020 17:25:24
* Zoho date-time fields : 2020-12-09T17:25:24-07:00

For the update to work, you would first need to convert `zoho.currenttime` into the Zoho date-time field format. Conversely, if you `GET` date-time fields on Zoho, you would not be able to perform any calculations in Deluge before converting the date-time field into the `zoho.currentime` format. All these can be done via string manipulations.

## Understanding Zoho Date-Time Field Format
The format for Zoho's date-time field can be dissected into 4 parts: [2020-12-09] [T] [17:25:24] [-07:00]
1. [2020-12-09] = date format (yyyy-MM-dd)
2. [T] = represents time
3. [17:25:24] = time format (hh:mm:ss)
4. [-07:00] = time-zone (GMT)

## Tutorial - Converting Date-Time Format from Deluge to Zoho

### Define the Time Zone
The time zone format here is based on GMT. As for us in Utah, it would be GMT-7, therefore [-07:00]. To check your company system time zone, you can go to Setup > General > Company Details. 
```javascript
timeZone = "-07:00";  //Change to your system time zone.
```

### Get the Current Date Time
`zoho.currenttime` will return you the current date-time of the function execution based on your Application Settings.
```javascript
dateTime = zoho.currenttime;
```

### Trim Out the Date and Format It
Get the index of the space " ", and use a `left` function to extract the date. Then, format it to "yyyy-MM-dd" with a `toString` function.
```javascript
dateTime_DATE = dateTime.left(dateTime.indexof(" ")).toString("yyyy-MM-dd");
```

### Trim Out the Time
Use a `len` funciton to get the character length, then minus the index of space " " to get the total number of characters from the **right** to the **space**. Then, use a `right` function to trim it out.

```javascript
dateTime_TIME = dateTime.right(dateTime.len() - dateTime.indexof(" "));
```

### Combine the Date, Time and Time Zone in the Required Format
Now that you have the date, time and time zone variables ready, merge them all together in the required format (with the "T"), and remove the additional space with the `remove` function. Your date-time is now ready to be updated into any date-time fields in Zoho.
```javascript
dateTime_FORMATTED = (dateTime_DATE + "T" + dateTime_TIME + timeZone).remove(" ");
info dateTime_FORMATTED;
```

## Tutorial - Converting Date-Time Format from Zoho to Deluge
When you `GET` a Zoho date-time field, you will be returned with the Zoho date-time format. If you need to perform calculations on Deluge, you need to reverse convert it into Deluge's date time format (which is basically the reverse of what we just did). 

```javacript
zohoDateTime = "GET_ZOHO_DATE_TIME_FIELD_HERE";
zohoDateTime_CONVERT = zohoDateTime.replaceAll("T"," ");
```

Despite not trimming out the time-zone, the date-time is now in a Deluge-parsable format allowing you to perform any [date-time additions/subtractions](https://www.zoho.com/deluge/help/functions/date-time.html). 

To update the date-time into a Zoho date-time field, you would need to format it back by [repeating the steps above.](#define-the-time-zone)

## Note
This post is for date-time **format** conversion only. For **time zone** conversion, please refer to [Zoho's documentation on `toTime` function.](https://www.zoho.com/deluge/help/functions/common/totime.html)

