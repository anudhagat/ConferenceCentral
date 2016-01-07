Conference Central Application using Google's App Engine.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

Initial Code provided by Udacity had the following functionality:
1. Log in / log out using Google + login.
2. Create and update a user's profile.
3. Create a Conference.
4. Register and unregister for a conference.
5. Query for all Conferences.
6. Query for all Conferences created by you.
7. Query for all Conferences based on filters on properties.

I have added the following endpoints. Each endpoint is explained in detail below.

1. getConferenceSessions(websafeConferenceKey)
2. getConferenceSessionsByType(websafeConferenceKey, typeOfSession)
3. getSessionsBySpeaker(speaker)
4. createSession(SessionForm, websafeConferenceKey)

## Setup Instructions
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
