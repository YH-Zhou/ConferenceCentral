# Conference Organisation App

This project is a cloud-based API server to support a provided conference organization application that exists on the web as well as a native Android application. 
The API supports the following functionalities found within the app: 
- create new conferences and sessions within the conference
- user authentication and user profiles store/retrieval
- register for conferences and add interested session into user wishlists
- various manners to query the data.

This project is hosted on a cloud-based hosting platform - Google App Engine. It allows to develop applications quickly, horizontally scale to support hundreds of thousands of users on a variety of platforms including web or mobile.


## Table of contents

- [Setup Instructions](#setupinstructions)
- [Implemented Endpoints](#implementedendpoints)
- [Design Decisions](#designdecisions)
- [Support](#support)



## Setup Instructions

- Download [python(version2.7)](https://www.python.org/downloads/) on your computer;
- To deploy this API server locally, download and install the [Google App Engine SDK](https://cloud.google.com/appengine/downloads) for Python. 
- Clone this repository/Download the conference organisation app source code. 
- Registered in [Google Developer Console](https://console.developers.google.com) to obtain application ID and client ID.(My app ID is "conference-central-1013")
- Update the value of client ID in app.yaml to the application ID you just registered.
- Update the values at the top of settings.py to the your client IDs.
- Update the value of CLIENT_ID in static/js/app.js to your Web client ID.
- Open the Google app engine launcher, choose File > Add Existing Application, and then browse the files, add this application. After this, run this application after deploying it.
- Now your can visit your local server's address [localhost:7080](http://localhost:7080), you can also visit the [google api explorer](http://localhost:7080/_ah/api/explorer) to test all the endpoints.




## Implemented Endpoints
This project implements the following endpoints:
- *getProfile* : Get user profile.
- *saveProfile* : update user profile.
- *createConference* : Create a new conference
- *updateConference* : Update conference with provided updated info.
- *getConference* : Get the request conference by the websafeConferenceKey.
- *getConferenceCreated* : Return the conferences created by the current user.
- *registerForConference* : Register the selected conference for user.
- *unregisterFromConference* : Unregister the selected conference for user.
- *getConferencesToAttend* : Get a list of conferences that the user has registerd for.
- *queryConferences* : Help the user to perform queries about the conferences.
- *createSession* : Create a new session for a specific conference.
- *getConferenceSessions* : Get a list of sessions in a specific conference.
- *getConferenceSessionsByType* : Get a list of conference sessions that are of the required type.
- *addSessionsToWishlist* : Add the selected session to the current user's wishlist.
- *getConfSessionsInWishlist* : Get all the conference sessions in the user's wishlist.
- "getSessionsBySpeaker" : Get all the sessions that are given by a specific speaker.
- *queryConferenceSessions* : Query for sessions in a conference by some filters.
- *queryNonWorkshopSessions* : Query for all non-workshop sessions before 7 pm.
- *getConfSessionsByTime* : Get a list of conference sessions that are given between the required time intervals.
- *getAnnouncements* : Return announcement from memcache.
- *getFeaturedSpeaker* : Return the sessions of the featured speaker.


## Support

If you have any issues about the conference organisation app, please let me know.
My email address is yanhong.zhou05@gmail.com.