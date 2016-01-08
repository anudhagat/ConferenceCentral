Conference Central Application using Google's App Engine.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Provided Functionality
Initial Code provided by Udacity had the following functionality:
1. Log in / log out using Google + login.
2. Create and update a user's profile.
3. Create a Conference.
4. Register and unregister for a conference.
5. Query for all Conferences.
6. Query for all Conferences created by you.
7. Query for all Conferences based on filters on properties.

## My Additions

### Model

I created a Session (subclassed from ndb.Model) class and Session form (subclassed from messages.Message) class with the following properties:
* name (String, required field)
* highlights (String)
* speaker (String)
* duration (Integer)
* organizerUserId (String)
* typeOfSession(list of Strings)
* date (Date)
* startTime (Time, specified in 24 hour format for easy querying).

Each session class is created and stored with a ancestor relationship to a conference. In this way, it is easy to get all sessions belonging to a conference by doing a ancestor query.

I chose to model the speaker data of a session as a single string field instead of a list of strings or as a separate class/kind for the sake of simplicity.

In addition to these properties, the SessionForm has websafekey property that stores the session's key.

I have added an additional property to the Profile and Profile Form class: sessionsInWishlist. This is a list of strings, and it stores the keys of sessions that are in the user's wishlist.

### Endpoints

I have added the following endpoints. Each endpoint is explained in detail below.

1. getConferenceSessions(websafeConferenceKey): Given a conferencey key, query and return all sessions from the datastore thave have that conference as an ancestor.

2. getConferenceSessionsByType(websafeConferenceKey, typeOfSession): Given a conference key and a string for type of session, query and return all sessions that have the given string in the list typeOfSession.

3. getSessionsBySpeaker(speaker): Given a string for speaker, return all sessions in the datastore that have the given speaker.

4. createSession(SessionForm, websafeKey): Given the conference key websafeKey in the request header, add a Session class to the datastore using the information supplied in SessionForm class in the request header. Do these checks before adding the session:
* the user is loged in
* the user adding the session is the same as the one who created the conference
* the conference (with key websafeKey) exists
* the name of session is supplied (required property)

5. addSessionToWishlist(SessionKey): Given the session key, adds the session to the user's list of sessions they are interested in attending (add the key to the sessionsInWishlist property of the Profile class). The user may add any sessions to their wishlist. This functionality is not restricted to conferences for which the user is registered.

6. getSessionsInWishlist(): Given the websafe key for a conference, queries and returns all the sessions in that conference that belong to the user's wishlist (sessionsInWishList property of the user's Profile class)

7. deleteSessionInWishlist(SessionKey): Given the session key, removes the session from the user’s list of sessions(the list sessionsInWishlist property of the user's Profile class).

8. getFeaturedSpeaker(): Returns a string value of the featured speaker in memcache. When a session is created, a query is done on all sessions with that have the same speaker. If there is more than one session with this speaker, this speaker becomes the featured speaker. A memcache entry is added with the speaker's name and a list of his/her sessions (names only). This endpoint only returns the name of the speaker using the memcache entry.

#### Two Additional Queries

9. getSessionsByTime(startTime): Given a string with start time of the session (in HH:MM 24 hour format), return all sessions that start with the given start time. A user can use this query when they want to choose which session to add to his/her wishlist. Since they can only attend one session per start time, this query will be useful in choosing one session per time slot.

10. getSessionsInWishlistBySpeaker(speaker): Given a string with the speaker's name, return all sessions that have that speaker. This query is useful to the user so that they can attend all sessions by their favorite speaker.

### Query Problem

Problem:
Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

Answer:

The problem with this query is that it contains two inequalities on two different properties(!= 'workshop' and < 7 pm). Google's ndb has a query restriction that you can not query more than one inequality on more than one property. To get around this restriction, you can query for one inequality (say, all sessions with a start time of < 7 pm) and then programmatically remove all sessions that do not satisfy the other inequality (have a session of type workshop). I have implemented this query as an endpoint: twoInequalitiesQuery().

Another workaround might be change the model Session. Idea 1: You can have a boolean property notAWorkshop that is set in create session. Idea 2: type of session is a list of enum types and there is a small number of number of them. For example, type of session might be equal to ['Lecture', 'Workshop', 'Food', 'Social']. Then the query can be get all sessions that are equal to any of the other values ['Lecture', 'Food', 'Social']. This can be combined with the < 7 pm inequality to get the results.

## Supplied Setup Instructions from Udacity
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.

[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool
