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






## Design Decisions

### Task 1: Add sessions to conference

- First I have defined the Session and SessionForm classes. The Session model class contains the following properties: name(StringProperty, required); organisationUserId(StringProperty); highlights(StringProperty); speakers(KeyProperty, repeated); location(StringProperty); duration(StringProperty); typeOfSession(StringProperty, required); date(DateProperty); time(TimeProperty).


- Since a conference can have multiple sessions, I used a parent-child implementation. This allows for strong consistent querying to the datastore, as sessions can be queried by their conference ancestor. However, there is a downside about ancestor quering, entities with the same ancestor are limited to 1 write per second. 


- As for speaker, I defined a *Speaker* model class, so there's one entity in the datastore for each Speaker, and one entity in the datastore for each Session, each with a KeyProperty that points to a Speaker. With keyProperty, the datastore offers high write throughput, but only eventual consistency. 




### Task2: Add sessions to user wishlist
In order to store the user wishlist information, I added a property called *sessionWishlist* in the *Profile* model class. Then added two endpoint methods as follows:


- *addSessionsToWishlist* : Add the selected session to the current user's wishlist. It first gets the current user profile and gets the session using the sessionKey. After this, it will check if the session exists and if the users have already added this session to their wishlist. Then it will add the session key to the user's session wishlist and update the user profile.


- *getConfSessionsInWishlist* : Get all the conference sessions in the user's wishlist. This endpoint method only returns the sessions that are not only in the user's wishlist, but also in a given conference.


### Task3: Work on indexes and queries
##### Create indexes
I have checked the *index.yaml* file, the indexes for queries are added automatically. And when run this application on google app engine, I haven't seen a *exception NeedIndexError()* message.


##### Two additional queries
I added two additional queries that might be useful for this application.
- *getConfSessionsByTime* : This endpoint method helps the user to find sessions that happen in a specific date and also within a specific time interval. Also, the returned sessions are ordered based on their start time. Since some users may not be avaible all the time during the conference, this query can help them to easily find the sessions that are given in their spare time.
- *getSessionsByCityAndDate* : This endpoint method returns sessions that are given in a specific city and between the chosen start date and end date. Some users may travel to a city for a couple of days. They may want to know the conferences and related sessions that are given at this city within their travel period.


##### Query related problems
The *queryNonWorkshopSessions* endpoint queries for all non-workshop sessions before 7 pm. When implementing this datastore query, there arises a problem caused by the limitations of datastore query. Queries are only allowed to have inequality filters for a single property, and it would raise exceptions when you are trying to combine too many filters or use inequalities for multiple properties. So here I cannot filter on the startTime and typeOfSession at the same time. In order to sovle this, I only used the inequality filter for the startTime property and obtained all the sessions before 7 pm. Then I returned all sessions that are non-workshop using if statement.



### Task4: Add a task
In the *createSession* endpoints, every time when a new session is added to a conference, the speakers are checked. If a speaker of this session has multiple sessions at this conference, then they become the new featured speaker. And the new featured speaker is added to memecache under the "FEATURED_SPEAKER" key. Since getting the new featured speaker is not something that a user expects while creating a session, I handled the process of updating featured speaker as a task and put it to a taskqueue.

Then the endpoint *getFeaturedSpeaker* checks the memcache to get the featured speaker info. If it exists, the endpoint returns the speaker name and all the session names that are given by this speaker.


## Support

If you have any issues about the conference organisation app, please let me know.
My email address is yanhong.zhou05@gmail.com.