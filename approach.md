---
layout: default
title: Approach
---

Overview of Requirements
========================

The City of Santa Cruz Event Hub is a central, community-based event database that replaces
the current ad-hoc collection of calendars maintained by media organizations.  The Event Hub
will:

* provide event producers and sponsors with a centralized web site for publishing information
about their venues and upcoming events in the city
* simplify the process of publishing customized calendar feeds for web, social, and
traditional media
* save time for event attendees by connecting an event with social media and ticket purchase
options

Unlike existing commercial event calendar services, the Event Hub will be focused on the Santa Cruz
community and will give producers the ability to communicate and coordinate the local event schedules.
For example, the producer of a folk music artist could search for appropriate venues in the Event
Hub and for the dates of performances tagged "folk music" to find the best time and place for their
artist's concert in Santa Cruz.  A local solution also benefits media outlets with an existing web
presence, like the [Good Times](http://www.gtweekly.com/index.php/all-about-santa-cruz-activities-visitors-guide.html)
and [Santa Cruz Sentinel](http://www.santacruzsentinel.com/entertainment), by providing
hands-on integration assistance and quick turnaround of feature requests and improvements.

The first phase of the Event Hub deployment will consist of a secure web site where event producers
can register for an account, enter information about venues, schedule events, and publish events
to Facebook.  The site will provide an HTTP-based, RESTful API for
publishers to use to fetch event information in a variety of formats for display on their web sites.
Stretch goals for the initial version of the site will be a Wordpress calendar plugin and the ability
to publish to external calendaring services like Eventbrite and Meetup.


Workflow
========

This section describes how the Event Hub will be used by producers and publishers.

Example #1
----------
### First-time Producer

Phil is the vice-president of the Santa Cruz Rustic Roses gardening club.  Over the past few years,
the club has grown mainly by word-of-mouth, with only a handful of gardeners attending the monthly
meetings.  The club has decided to hold an open house at Jenny's place overlooking the harbor to
attract people who are interested in talking about roses.  Phil is in charge of promoting this event.
He remembers reading about the new City of Santa Cruz Event Hub web site in the Sentinel, so he
browses the online edition until he finds a link to the Event Hub web site.

Once he opens the site on his laptop, Phil looks over the home page, which explains what the site
does and shows a couple of example events.  He finds the "Register" button right away -- it's big
and near the top of the screen -- clicks it, and enters his name and email address in the first
fields of the provided form.  The next section of the form asks if he is an individual, a club, or
a company.  Phil selects "club" and is shown an alphabetical list of all of the clubs already registered in
the Hub.  Santa Cruz Rustic Roses isn't in the list, so he selects "New Club" and types in the club
name and the contact information for Arlene, the president of the club.  Finally, Phil picks a
password and completes the registration form.

On the main screen, Phil sees a list of his venues (empty) and a list of events (also empty).  He
clicks on the "Add Event" button at the bottom of the event list and is taken to the new event
entry page.  He fills in the date of the open house, adds "Rustic Roses Open House" in the title
field, types in a short description, and clicks on 
the "Publish" button.  A popup appears that says, "You haven't selected a venue for your event.  Would
you like to do that now?"  Phil clicks on the "Yes" button and is taken to the new venue entry page.
He enters Jenny's address in the new venue form, skips adding a picture, and clicks on the "Save"
button, which takes him back to the new event entry page.  He notices that Jenny's house is now
selected for the event venue.  Now that he's looking at the event again, Phil realizes that he isn't
sure if they will actually be serving gluten-free snacks, so he clicks on the "Save for Later" button
on the event entry screen and the "Logout" button at the top of the page.

A quick call to Arlene confirms that there will be wheat-free cookies, so Phil goes back to his browser,
logs in to the Event Hub, and sees the open house event in the list of his events.  He also notices that
Jenny's house is listed under his venues.  Phil clicks on the "Publish" button next to the open house
event and a popup appears asking him to confirm that the event is to be published.  He clicks the "Ok" 
button, and another popup appears informing him that the event has been published to the Hub.  


Example #2
----------
### Club Owner

Mike is the owner of "Mike's Mosh Pit", a club off of Ocean Street that specializes in producing local
heavy metal bands.  The Pit has been open for almost ten years, and Mike has been using
[Google Calendar](http://calendar.google.com/) to keep track of his bookings.  This has
been working well for him, but it is a pain to publish his show dates to all of the local newspapers
and web sites.  He signed up for the Event Hub when he first heard about it and entered information about
the club, but at the time he thought he had to enter events by hand so he logged out.  A friend told him
that he could just upload his existing calendar, so he thought he'd give the Event Hub another shot.

Mike opens the Event Hub web site and clicks on the "Login" button, but realizes that he's forgotten
his password.  He clicks on the "Forgot Password" button, enters his email address, and clicks on the
"Send Password" button.  A few minutes later, he receives an email message instructing him to click on
a URL in the email message to log in with a temporary password.  He follows the instructions and chooses
a new password, writing it down so he doesn't forget it for next time.

Mike notices that the thumbnail picture of the Pit is old -- it was taken before he repainted the outside
jet black.  He clicks on the venue entry and is taken to the edit venue page.  Mike clicks to remove the
old picture, then uploads a new picture from his computer.  He also adds his new web site to the venue information,
then clicks on the "Save" button and is taken back to the main Hub page.

At the bottom of the (empty) list of events, Mike notices a new section titled "Import" with a couple of
buttons next to it.  One of them says "Google Calendar", so he clicks on that and is taken to the iCal
import page.  He reads the instructions for locating the URL to his Google calendar in iCal format, then
opens a new browser window and follows them.  He copies the iCal URL from Google and pastes it into the
import page, then clicks the "Import" button.  A cool little animated spinner graphic pops up, and a few
seconds later, a message appears telling him that his import is complete.  Mike clicks the "Ok" button
and is taken back to the main page.

On the main page, Mike sees a list of upcoming events pulled from his Google Calendar.  Each event has a
"Publish" button next to it.  He clicks the "Publish" button on the first event and confirms that it should
be published, frowning a little bit as he contemplates going through that process for all 65 upcoming shows.
He then notices a "Publish All Future Events" link at the bottom of the event list, smiles, and clicks on it.
Mike clicks the "Ok" button in the publish all confirmation popup and is happy to see all of his upcoming
shows published to the Hub.

Later that day, the drummer for the Oozing Eyeballs calls Mike and cancels their show for the upcoming
Friday night.  Mike opens up Google Calendar and deletes the show.  A few hours later, he remembers the
Event Hub and logs in.  Sure enough, the Oozing Eyeballs show has been removed from the Event Hub as well.


Support and Availability
========================

Our team is local to Santa Cruz and can provide support via email, instant messaging, and by phone.  Our
preference would be to develop the Event Hub as an open source project hosted on [Github](https://github.com/),
which will provide source control, issue tracking, and wiki capabilities for the development process.
During the detailed design period of the project, our team will be available for in-person meetings with
the City staff, Modern Tribe, and stakeholders.

The team is available immediately to begin work on the project. 

