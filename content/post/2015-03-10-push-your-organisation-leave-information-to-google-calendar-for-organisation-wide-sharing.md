---
title: Push your organisation leave information to Google Calendar for organisation
  wide sharing
author: ''
date: '2015-03-10'
slug: leave-information-google-spreadsheet-calendar-updates
categories: []
tags: []
---

TL;DR

You can use google spreadsheet to record your leave information and then use script editor (use the code below) to push your leave record as event to the google calendar. The calendar could then be shared with people to inform them about the leave status or any other events.

<hr>

We are a decent size company of 30-35 people and people are not aware of who is on leave when. There are lots of HR tools available - some open source, some paid services. We are still manageable without systems though the tools would have helped. 

Some of my colleagues were complaining that they are not aware of each other leaves and hence had to ask others to know about their whereabouts. The easiest solution is to maintain the list in google spreadsheet and push those information to google calendar. Share that calendar to the organisation users and done. Now someone has to enter the information in the spreadsheet and the leave information appears as an event in the calendar. 

It took around 2 hours to come up with good looking code that solved the basic problem. It not only creates the leave information but also allows to edit it. 

Here are step by step instructions to implement Google Spreadsheet records to Google Calendar events

* Create Google Spreadsheet with the following columns [`Date`, `Staff name`, `Leave Type`, `Full Day?`, `First Half?`, `Leave Form Filled?`, `Calendar Set`, `Event Id`]
![Spreadsheet columns](/post/2015-03-10-leave-spreadsheet-calendar_files/spreadsheet-start.png "Spreadsheet columns")
* Update the column name as you deem necessary. If you add new columns, then you might have to change the column index in the code below, update few codes here and there. The logic here is people can take full-day or half-day leave. If it's half-day, then it could be first half or second half. Then we also encourge people to fill leave form so that they can see if they have approved their leave or not before taking leave. 
* Open `Tools > Script Editor` and paste the code below in the editor.
  * Update your google calendar id in the pasted code inside `pushToCalendar` function
  * Go to your google calendar, select `Calendar Settings` and find `Calendar Address`
![Calendar](/post/2015-03-10-leave-spreadsheet-calendar_files/calendar-id.png "Calendar id example")  
* Select `Select function > onOpen` and click Run
![Run onOpen](/post/2015-03-10-leave-spreadsheet-calendar_files/run-onopen-function.png "Run onOpen Function")
* `Leave Calendar > Update Leaves` menu will appear in your spreadsheet
* Add leave information and select `Leave Calendar > Update Leaves`
![Leave information entry](/post/2015-03-10-leave-spreadsheet-calendar_files/leave-records-entries.png "Leave information entry")
* If leave information is changed and needs to be updated, then delete `y` under `Calendar Set` column and select `Leave Calendar > Update Leaves`
* The calendar events will be updated.

```js
//ref: http://www.javascript-spreadsheet-programming.com/2013/01/object-oriented-javascript-part-2.html
//ref: http://www.adammcfarland.com/2013/08/09/tutorial-using-google-spreadsheets-to-update-a-google-calendar-our-new-vacation-management-system/

function Leave(value) {
  this.values = value;
  this.init(value);
}
Leave.prototype = {
  constructor: Leave,
  init: function(value) {
    this.date = value[0];
    this.name = value[1];
    this.leaveType = value[2];
    this.fullDay = value[3];
    this.firstHalf = value[4];
    this.leaveFormFilled = value[5];
    this.calendarEntered = value[6];
    this.calendarEventId = value[7];
  },
  getTitle: function() {
    var leaveInfo = this.leaveType;
    if(this.fullDay) {
      leaveInfo += " [Full Day]";
    } else {
      if(this.firstHalf) {
        leaveInfo += " [First Half]";
      } else {
        leaveInfo += " [Second Half]";
      } 
    }
    var filled = "[x] ";
    if(this.leaveFormFilled) {
      filled = "";
    }
    return filled + this.name + " - " + leaveInfo;
  },
  isValid: function() {
    if((this.name!="") && (this.date>0)) return true;
    else return false;
  },
  isNew: function() {
    return ((this.calendarEntered != 'y') && (this.calendarEventId == ""));
  },
  isEdit: function() {
    return ((this.calendarEntered != 'y') && (this.calendarEventId != null));
  },
  getDescription: function() {
    var desc = "Name: " + this.name;
    desc += "\nType: " + this.leaveType;
    desc += "\nTime: " + (this.fullDay?"Full":(this.firstHalf?"First Half":"Second Half"));
    desc += "\nLeave Form: " + (this.leaveFormFilled?"Filled":"Not Filled");
    return desc;
  }
}

function pushToCalendar() {
  //spreadsheet variables
  var sheet = SpreadsheetApp.getActiveSheet();
  var lastRow = sheet.getLastRow(); 
  var range = sheet.getRange(2,1,lastRow,8);
  var values = range.getValues();

  var calendar = CalendarApp.getCalendarById('xxxxxx-google-calendar-id')
  
  var numValues = 0;
  for (var i = 0; i < values.length; i++) {   
    var leave = new Leave(values[i]);
    if(leave.isValid()) {
      if(leave.isNew()) {
        var event = calendar.createAllDayEvent(leave.getTitle(), leave.date);
        event.setDescription(leave.getDescription());
        //get ID
        var eventId = event.getId();
        //mark as entered, enter ID
        sheet.getRange(i+2,7).setValue('y');
        sheet.getRange(i+2,8).setValue(eventId);
        
      } else if(leave.isEdit()) {
        var event = calendar.getEventSeriesById(leave.calendarEventId); 
         event.setTitle(leave.getTitle());
         event.setDescription(leave.getDescription());        
         sheet.getRange(i+2,7).setValue('y');        
      }
    }
  }
}

//add a menu when the spreadsheet is opened
function onOpen() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet();
  var menuEntries = [];  
  menuEntries.push({name: "Update Leaves", functionName: "pushToCalendar"}); 
  sheet.addMenu("Leave Calendar", menuEntries);  
}

```


