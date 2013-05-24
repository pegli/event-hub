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

Implementation
==============

Platform
--------

The core of the Event Hub service will be an [Apache CouchDB](http://couchdb.apache.org/)
server.  CouchDB is a stable and secure open-source "NoSQL" database that stores data documents in
[JSON](http://json.org/) format and provides an HTTP API for creating, modifying,
and transforming stored documents.  CouchDB is extremely scalable, easy to maintain, and has been used by
[commercial](http://www.ocastalabs.com/wp-content/uploads/CouchDB-Case-Study-Agora.pdf)
[government](http://readwrite.com/2010/08/26/lhc-couchdb) entities for data storage
since 2005.  The CouchDB project recently released [version 1.3.0](http://docs.couchdb.org/en/latest/changelog.html#version-1-3-0) and
dramatically improved its documentation site, a strong indication that the project is healthy and active.
Several companies provide hosting and support services for CouchDB.  While the Event Hub service
will be hosted at Cruzio, it is always desirable to have options for future support and expansion.

CouchDB is a natural fit for the Event Hub requirements.  Originally written by
[Damien Katz](http://www.couchbase.com/leadership) to be the "database of the Internet,"
it was designed from the ground up to both store data and host web applications that operate on
that data.  The server does not require predefined schemas or document formats, leaving the
structures of the documents up to the application developer and allowing those structures to evolve
over time as needs change.  While JSON is the native format of a document, CouchDB provides
a means of [transforming](http://wiki.apache.org/couchdb/Formatting_with_Show_and_List)
documents and collections of documents to any arbitrary format.  For example, a JSON document representing
an event venue could be transformed to HTML for presentation in a web browser, or a collection of
events could be converted to [Atom syndication format](http://www.atomenabled.org/developers/syndication/)
for publication.  Documents may contain any number of binary attachments that can also be accessed
through the built-in HTTP server.  Attachments are usually used to hold media associated with a document
and are a perfect fit for storing photos and video.

CouchDB can store HTML pages, CSS stylesheets, and JavaScript files as data documents
and serve them over HTTP, removing the need for a separate application server.  A web application
served by CouchDB and operating on CouchDB data documents is known as a "couchapp."  Some example
couchapps can be viewed on the [CouchApp project home page](http://couchapp.org/page/index).
There are several tools available for assembling files into a couchapp document and publishing
the app to a CouchDB server.  The event producer web site and administrative web site will be written as
couchapps.

CouchDB ships with a built-in user database, role-based security, and support for both HTTP Basic
and OAuth authentication.  For additional security, the Event Hub service will run behind a
[HAProxy](http://haproxy.1wt.eu) software load balancer/HTTP proxy which will limit the
HTTP requests that are forwarded to CouchDB to a set of predefined URLs.  Combining CouchDB with
an HTTP proxy is a common configuration for public-facing couchapps.

A single installation of CouchDB can store many millions of documents and attachments.  Our team has
deployed services based on CouchDB that host over five million documents and several terabytes of
storage on a single physical server without any issues in service availability or response time.
If the scope of the Event Hub project eventually includes a requirement for high availability, we can use
CouchDB's [replication](http://guide.couchdb.org/draft/replication.html) feature to synchronize
data between two or more physical servers and HAProxy to load balance requests among the servers.
Replication can also be used by developers to pull a copy of the production database to their local
machines to test couchapps prior to deployment.

Any periodic tasks like exports or backups that need to be initiated by the Event Hub service will
be written in a scripting language appropriate to the task.  For example, an "export to Eventbrite"
process might be implemented in Python using their [client library](http://developer.eventbrite.com/doc/#libraries).


Workflow
--------

This section describes how the Event Hub will be used by producers and publishers.

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


Data Model
----------

It is important to have an external definition of the data model for the documents being stored because
CouchDB does not intrinsically provide any schema definitions.  This definition can change over time as
new features are added or requirements are modified.

Some notes about data formats:

* Long text like descriptions of venues or events can be in either plain text or a markup format
  that can be converted to HTML or plain text.  Suggested formats for the initial deployment are
  text, HTML, and [Markdown](http://daringfireball.net/projects/markdown).
* The `timestamp` type is a long integer representing seconds since the UNIX epoch.  Timestamps 
  are not affected by time zones or daylight savings time and can be directly compared, unlike many
  string-based date formats.
* Hours for relative times (for example, "every day at 11 am") are expressed in 24-hour time.

### User

A *user* is an individual person with login credentials, an email address, a set of roles specifying
which actions they are allowed to perform, and an association with one or more producers.  A user's
login name, password, and roles are stored in the CouchDB `_users` database; see the
[CouchDB documentation](http://wiki.apache.org/couchdb/Security_Features_Overview#Authentication_database)
for details on the built-in properties of a user document.  Additional user properties are stored
in a "profile" document in the main database.

<table>
  <caption>User profile document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>username</td>
      <td>string</td>
      <td>the user's login identifier; matches the username in the _users database</td>
    </tr>
    <tr>
      <td>email</td>
      <td>string</td>
      <td>the user's email address</td>
    </tr>
    <tr>
      <td>handle</td>
      <td>string</td>
      <td>the name of the user to be shown in events and venue listings</td>
    </tr>
    <tr>
      <td>avatar</td>
      <td>image attachment</td>
      <td>a small photo of the user</td>
    </tr>
  </tbody>
</table>

### Producer

A *producer* is an individual, company, club, or organization that will be creating events in the
Event Hub.  Examples of producers might include [The Catalyst](http://www.catalystclub.com) club,
[The 418 Project](http://www.the418.org/), and
[Toastmasters](http://evening.toastmastersclubs.org/).
When a user registers for the Event Hub site, they must be associated with one or more producers.

<table>
  <caption>Producer document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>name</td>
      <td>string</td>
      <td>the public name of the producer (e.g. "The Catalyst Club", "Working Mom's Circle")</td>
    </tr>
    <tr>
      <td>modified_by</td>
      <td>string</td>
      <td>the username of the user who last modified this producer</td>
    </tr>
    <tr>
      <td>modified</td>
      <td>timestamp</td>
      <td>the last date that the producer was modified</td>
    </tr>
    <tr>
      <td>members</td>
      <td>array of string</td>
      <td>the usernames of the users who can modify this producer and post events on its behalf</td>
    </tr>
    <tr>
      <td>venues</td>
      <td>array of string</td>
      <td>the document IDs of the venues owned by this producer</td>
    </tr>
    <tr>
      <td>logo</td>
      <td>image attachment</td>
      <td>a logo image for the producer</td>
    </tr>
  </tbody>
</table>

### Venue

A venue is a location for an event.  Venues may be permanent or temporary.  Venue
documents contain information about the venue

<table>
  <caption>Venue document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>name</td>
      <td>string</td>
      <td>the public name of the venue (e.g. "Moe's Alley", "Point Pinos Grill", "Santa Cruz Civic Auditorium")</td>
    </tr>
    <tr>
      <td>street_address</td>
      <td>string</td>
      <td>the address of the venue (e.g. "1740 17th Ave.")</td>
    </tr>
    <tr>
      <td>city</td>
      <td>string</td>
      <td>the venue's city</td>
    </tr>
    <tr>
      <td>state</td>
      <td>string</td>
      <td>the venue's state (probably going to be "CA" in all cases!)</td>
    </tr>
    <tr>
      <td>postal_code</td>
      <td>string</td>
      <td>the postal code of the venue</td>
    </tr>
    <tr>
      <td>latitude</td>
      <td>double</td>
      <td>the latitude of the venue for locating it on a map</td>
    </tr>
    <tr>
      <td>longitude</td>
      <td>double</td>
      <td>the latitude of the venue for locating it on a map</td>
    </tr>
    <tr>
      <td>phone</td>
      <td>string</td>
      <td>the phone number of the venue</td>
    </tr>
    <tr>
      <td>website</td>
      <td>URL</td>
      <td>the venue's web site</td>
    </tr>
    <tr>
      <td>capacity_standing</td>
      <td>integer</td>
      <td>the number of guests that can be accommodated if not seated</td>
    </tr>
    <tr>
      <td>capacity_seated</td>
      <td>integer</td>
      <td>the number of guests that can be accommodated if seated</td>
    </tr>
    <tr>
      <td>ada_access</td>
      <td>boolean</td>
      <td>if true, the venue facilities comply with the Americans With Disabilities Act</td>
    </tr>
    <tr>
      <td>modified_by</td>
      <td>string</td>
      <td>the username of the user who last modified this venue</td>
    </tr>
    <tr>
      <td>modified</td>
      <td>timestamp</td>
      <td>the last date that the venue was modified</td>
    </tr>
    <tr>
      <td>published</td>
      <td>boolean</td>
      <td>set to true to make this venue public</td>
    </tr>
    <tr>
      <td>tags</td>
      <td>array of string</td>
      <td>user-defined tags for the venue to aid in searching for venues</td>
    </tr>
    <tr>
      <td>photos</td>
      <td>image attachments</td>
      <td>zero or more images of the venue</td>
    </tr>
  </tbody>
</table>

Other potential fields may be added to indicate whether the venue has food and drink
service, number and type of bathrooms, whether a box office is present, and so on.

### Event

An event is any sort of meeting or performance in a venue that has a starting and
ending time.  Events may be recurring or non-recurring.

<table>
  <caption>common Event document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>name</td>
      <td>string</td>
      <td>the name of the event (e.g. "Sons of Italy BBQ Picnic")</td>
    </tr>
    <tr>
      <td>description</td>
      <td>string</td>
      <td>text or markup describing the event</td>
    </tr>
    <tr>
      <td>producer</td>
      <td>string</td>
      <td>the document ID of the producer of the event</td>
    </tr>
    <tr>
      <td>venue</td>
      <td>string</td>
      <td>the document ID of the venue of the event</td>
    </tr>
    <tr>
      <td>published</td>
      <td>boolean</td>
      <td>set to true to make this event public</td>
    </tr>
    <tr>
      <td>ticket_url</td>
      <td>url</td>
      <td>the website where tickets for this event can be purchased</td>
    </tr>
  </tbody>
</table>

<table>
  <caption>non-recurring Event document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>venue_open</td>
      <td>timestamp</td>
      <td>the time at which the venue will open for guests for this event</td>
    </tr>
    <tr>
      <td>start</td>
      <td>timestamp</td>
      <td>the starting time of the event</td>
    </tr>
    <tr>
      <td>finish</td>
      <td>timestamp</td>
      <td>the finish time of the event</td>
    </tr>
  </tbody>
</table>

<table>
  <caption>recurring Event document properties</caption>
  <thead>
    <tr>
      <th>name</th>
      <th>type</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>venue_open_hr</td>
      <td>integer</td>
      <td>the hour at which the venue will open for guests for each event</td>
    </tr>
    <tr>
      <td>venue_open_mi</td>
      <td>integer</td>
      <td>the minute at which the venue will open for guests for each event</td>
    </tr>
    <tr>
      <td>repeat_type</td>
      <td>string</td>
      <td>one of "daily", "weekly", "monthly"</td>
    </tr>
    <tr>
      <td>repeat_interval</td>
      <td>integer</td>
      <td>repeat every n of "repeat type", e.g. 2 days</td>
    </tr>
    <tr>
      <td>repeat_on</td>
      <td>integer</td>
      <td>
        For weekly repeats, a bitmask starting with Sunday indicating which days of the week to repeat;
        e.g. 20 (binary 0010100) means repeat every Tuesday and Thursday.  For monthly repeats, the 
        day of the month to repeat.
      </td>
    </tr>
    <tr>
      <td>ends</td>
      <td>timestamp</td>
      <td>
        the date after which no more events of this type should be shown.  Can be null or absent to indicate an
        event that repeats indefinitely.
      </td>
    </tr>
  </tbody>
</table>

Producer Web Site
=================

Login
-----

Registration
------------

User Profile
------------

Event and Venue List
--------------------

New/Edit Venue
--------------

New/Edit Event
--------------

Import Events
-------------

### iCal


Data Feeds
==========

TODO transformation to output formats

Facebook Integration
--------------------

The Event Hub will integrate with Facebook's [Graph API](https://developers.facebook.com/docs/reference/api/)
to allow producers to push events to their Facebook calendars.  Users who wish to integrate with Facebook
will need to authorize the Event Hub Facebook app and allow the app to post events to their account or page.

The Event Hub Facebook app will be created and managed by our team during the development and integration
process.  Control of that app will be transferred to the appropriate City department when development is complete.


Support and Availability
========================

Our team is local to Santa Cruz and can provide support via email, instant messaging, and by phone.  Our
preference would be to develop the Event Hub as an open source project hosted on [Github](https://github.com/),
which will provide source control, issue tracking, and wiki capabilities for the development process.
During the detailed design period of the project, our team will be available for in-person meetings with
the City staff, Modern Tribe, and stakeholders.

The team is available immediately to begin work on the project. 

