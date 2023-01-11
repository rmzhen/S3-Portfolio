# User stories & C4-model

## Table of content
- [Intro](#intro)
- [User stories](#user-stories)
- [C4-model](#c4-model)

## Intro
The main project I'l be working on during semester 3 is a dashboard with shazam and lastfm itegrations. To get a rough idea what functionalities the project needs, I've defined user stories and sketched a C4-model for the application.


## User stories
### Preface
The definition of a user here is someone who:
- Makes use of the Shazam app.
- Enjoys music.
- Is familiar with using a web application and PC's or smartphones. 

### User stories
- As a user, I want to see my Shazam history within the dashboard.
	- The first page that will be displayed after registering or logging in must show the registered user's Shazam history.
	- General song information (e.g. song name and artist name) must be displayed for every result in the history page.
- As a user, I want to get notified if my inputs were succesfully handled.
	- The FE shoud display an error message when an existing song entry is given as input.
	- The FE shoud display an error message when an invalid song entry is given as input.
	- The BE should send an error message when the given song entry already exists in the DB.
	- The BE should send an error message when the given song entry is invalid.
	- The BE should call the DB to check the existence of the given entry.
	- The FE should contain tests for the error handling.
	- The BE should contain tests for the error handling.
- As a user, I want to see the band details of the Shazam result I have selected.
	- The artist name with the artist description must be displayed on the dashboard when an artist is selected.
- As a user, I want to scrobble my results to Last.fm when I desire.
	- The dashboard must have an option for the user to scrobble their Shazam result to Last.fm.
- As a user, I want to be able to input a song for the app to detect what song it is.
	- The dashboard should be able to accept an audio file.
	- The dashboard should return the results found from the given audio file.	

*21 december 2022. Subject to change.*

## C4-model
### Context
![SystemContext-thumbnail](../images/structurizr-1-SystemContext.png)
*14 September 2022. Subject to change.*

### Container
![Containers-thumbnail](../images/structurizr-1-Containers.png)
*14 September 2022. Subject to change.*
