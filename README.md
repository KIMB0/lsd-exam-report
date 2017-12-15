# Report - Large Systems Development

*By Group E: Alexander, Danny, Kim.*

[Project description is found here](https://github.com/datsoftlyngby/soft2017fall-lsd-teaching-material/blob/master/assignments/08-Project_report.md)

## Introduction

Throughout the Large Systems Development (LSD) course at our first semester of the PBA in Software Development at Copenhagen Business Academy, it was required of us to create a functional clone of the already existing [Hacker News](https://news.ycombinator.com/) system, and then afterwards maintain another group’s system with the same specifications as ours.
We have a lot of scattered documentation and specifications surrounding this project. This is our final LSD report, and simultaneously our attempt to collect all the different information in one place, while documenting our thoughts on these.

## 1. Requirements, architecture, design and process

### 1.1. System requirements

#### Purpose of the system

Hacker news is a social news website focusing on computer science and entrepreneurship. It is similar to reddit. It is a website where users can post stories, showcases, questions, polls and more. It enables users to debate by letting them comment on stories and other users comments. For the user to comment on a story or another comment, they have to be logged in.
Users can upvote and downvote stories or other users comments. However unlike reddit, the logged in user needs at least 500 karma points before they’re able to downvote content. Karma points are calculated by the number of upvotes a given user’s content has received, minus the number of downvotes. All users have the option to flag submitted content as spam.

#### Scope of the system

##### Functional requirements

The system was to be built as a web application. The backend should be built as a RESTful API, whereas the frontend should be an application interacting with the backend to retrieve the requested data. The implementation language for both services was ours to choose. The frontend should present the user with an overview of stories, and the user should then be able to:

* See new stories
* Comments on stories
* Show something that a user made
* Able to ask a question
* See asked questions
* See job opportunities at startups funded by Y Combinator
* Submit a story
* Sign up and be a part of the community
* Login

The above is also the major functions that are the most important part of the system.
The RESTful API needed to be servicing external programs. It had to provide stories, comments, jobs, Ask HN and polls. Everything was built up as an item, and an item were to be identified by its id, which is an unique integer. The Item ids should then live under `/item/<id>`.

##### Nonfunctional requirements

The system needed to have an uptime of more than 95% and should not lose any content. Throughout a period of time, a simulator would continuously be posting items to our service.

### 1.2. Development process

Our development process consisted of thorough brainstorming and mindmapping as a team. Early in the process, we were to make a [Requirement Analysis Document](https://github.com/KIMB0/LSD_frontend/blob/master/Documents/Requirements%20Analysis%20Document.md)(RAD) as complete as we could. Obviously we couldn’t specify all the requirements and the structure right away, but this was a good start. We began creating and defining the scope for the project. This was quite easy, as the idea was to create a clone of an already existing system. Therefore, most of the scope and important features were in place after doing a rundown of the original Hacker News features. Afterwards we created a lot of design models. You can read more about these in section 1.4 - "Software Design”.
This helped us keep better tabs of the different features and tools of this complex system, so we had a basis to start from, even before we started implementing the actual system.

We kept track of all of our future tasks and functionalities on Github using issues. Everytime we made some new fixes or features we used pull requests, which was helping to making sure that another developer from the group was always reviewing the code before it would go into production.

### 1.3. Software architecture

The above architecture shows the connection between the different endpoints that the Hacker News clone consists of. To make it formal; a user makes a request to the website, which is hosted on a droplet from DigitalOcean with the IP: 138.197.42.192. When the user navigates through the site, the frontend server will request for data (through a firewall) on the API server. The API server is hosted on Hetzner.com as a Virtual Machine with IP: 94.130.57.246. This server processes the requests from the frontend server and sends a request further on to the database server, which is lying on a different droplet. The database process’ the request and sends a response back through the chain, that we just got through.
The model that the database is built up upon is explained below. It consists of two collections: Items and Users.

#### Item

When the user is using the system and looking at stories, comments, jobs, Ask HNs and even polls, they are all related to the same data model: Item. They're identified by their ids, which are unique integers, and they live under `/item/<id>`.
[All Item properties and examples can be seen here](https://github.com/KIMB0/lsd-exam-report/blob/master/item-data-model.md).

#### User

The second data model is the User. They can be found under the following route: `/user/<id>`. Like with items, it’s only possible to retrieve users that are active in the system.
[All User properties and examples can be seen here](https://github.com/KIMB0/lsd-exam-report/blob/master/user-data-model.md).

Now in the base system there are no more data models. The whole system is built up upon these. Instead of having a lot of different data models, everything is just Items and Users. This is identical to the way the original Hackernews is built.
We were to receive a large payload of simulated data, which meant we had to create a transformation adapter. The only problem was that this data had its own format. It wasn’t split up into Items and Users. Instead it was a single combination of the two. The format looked like this:

```json
{"username": "<string>",
 "post_type": "<string>",
 "pwd_hash": "<string>",
 "post_title": "<string>",
 "post_url": "<string>",
 "post_parent": <int>,
 "hanesst_id": <int>,
 "post_text": "<string>"
 }
```

Our idea of a solution to this was to simply create an adapter that parted the above attributes into the matching attributes with both the Item and the User data models.

### 1.4. Software design

As mentioned earlier, we created a lot of different design models (mostly UP-based) before even beginning our implementation, so that we would have a better overview of the project and the architecture in general. The mentioned models included:

* Success criteria tables
* Functional and nonfunctional requirements
* System models
* Scenarios
* Use cases (brief, casual and fully dressed)
* Use case diagrams
* Domain diagrams
* Sequence diagrams
* And more…

All of these can be found in our [RAD](https://github.com/KIMB0/LSD_frontend/blob/master/Documents/Requirements%20Analysis%20Document.md).

### 1.5. Software implementation

When we finally started implementation, we began to write the RESTful API service in Java with the JAX-RS framework, the RESTFul documentation in [Swagger-UI](http://94.130.57.246:9000/swagger-ui/), and the frontend in TypeScript/HTML/CSS with the Angular framework. These specifications did not change throughout the project.

#### Backend

We (rather impulsively) chose MongoDB to be our database service. This was based on the fact that we had worked with it before, and it was known to be fast, reliable, and secure. Maintaining this turned out to be one of the biggest challenges throughout the process (read more in section 2 - Maintenance and SLA status).

We followed our project requirements well throughout implementation, with only a few complications. These were solved rather quickly when simply putting our head together. As mentioned earlier, we used the Github Issues system to keep track of bugs, future implementations, and important factors. This worked extremely well, as it was possible to review, comment and discuss these issues in depth through Github.

The initial server setup went well, mostly due to previous experience in deploying Java applications to a Glassfish server from other assignments in the LSD course.

Overall, the first iteration of the backend implementation went along nicely. It wasn’t until the simulated data started rolling in that we had our first major problems, which will be covered later.

#### Frontend

We started off by creating a new Angular 4<sup>1</sup> project by using the Angular CLI. Next we created and styled all the components (pages) the website needed and then made an organized structure for them.
Afterwards we created the services (also called providers) which were going to contain HTTP calls to the backend REST API. We were focusing on implementing the requirements, so therefore we created a HTTP GET method which could get all items and list them on one of the pages. Then we made POST methods; one for login, one for signup and one for posting an item, but only if the user is logged in.

Then we created a droplet on DigitalOcean where we installed an Apache2<sup>2</sup> web server to serve our Angular project. We used the second smallest one, because the smallest didn’t have enough ram to install Angular through npm<sup>3</sup>.

Since we didn’t want to transfer the built frontend from our local pc to our droplet manually, we used Travis CI<sup>4</sup> to do the job for us. Here we had some troubles with the SSH key that was encrypted by Travis. The server kept rejecting it. We found out that it was because Windows couldn’t encrypt the SSH private key properly through the Travis CLI. The solution was to do it on an existing Vagrant virtual machine running Ubuntu. Here we generated a public-private key pair and used Travis CLI commands to encrypt the private key. We copied the encrypted key to our local machine, and Travis then gained access to deploy on our web server.

The frontend also consists of a few unit-tests which is written with the Jasmine framework<sup>5</sup> and run by the Karma framework<sup>6</sup>.

## 2. Maintenance and SLA status

We were operators of Group D’s system. Their repositories can be found here:

* [Frontend](https://github.com/ERPedersen/HackerNewsFrontend)
* [Backend](https://github.com/ERPedersen/HackerNewsBackend)

### 2.1. Hand-over

Group D supplied us with their technical handover document, which can be found [here](https://github.com/ERPedersen/HackerNewsBackend/blob/master/Group6_Handover_Documentation.md).
It was very thorough and contained a lot of information about requirements, architecture, deployment processes, server location, credentials and more. We felt that some of this data was misplaced in the handover documentation. Mainly the requirements and scope of the project. Maybe it was because we were developing a system with the exact same requirements and specifications. But we agreed that at the same time it didn’t hurt to have in-depth information about the system we were going to operate.
Fortunately, it also contained important details and information about their system. Where to state bugs/issues, how to submit questions to the group, how to test the system, how to access their server statistics and the like. This made it very easy to keep a close eye on their system.

### 2.2. Service-level agreement

Many of the service-level details can also be found in the [handover documentation](https://github.com/datsoftlyngby/soft2017fall-lsd-teaching-material/blob/master/assignments/08-Project_report.md) from before. We didn’t have any disagreements about any of the metrics, although we were excited to see how they were to be kept by the other group. Mainly we noticed the promised uptime of 95%. While this was a requirement, it’s a hard promise to keep, as unexpected problems have a tendency to show up out of nowhere, even though precautions have been taken.
We also noted this line:

> “We do not expect for the system to be incredibly popular, so we have fairly low expectations for concurrent user numbers.”

It indicates that they were (at least to some degree) unaware that every system were going to receive very large amounts of simulated data. We decided to keep an extra eye on the server/system performance after the first items started rolling in.

### 2.3. Maintenance and reliability

In order to make sure that Group D’s system was keeping its promises from the SLA, we kept a close eye on their system and its status after the simulator was started. We mainly checked the `/status` and `/latest` routes of their API endpoint. The `/status` route should return “Alive” if the system was alive and well. Throughout monitoring Group D’s system, we did not experience any other status than “Alive”. The `/latest` should return the id of the latest item consumed and stored in the database. This worked like a charm. Fast and responsive, even when there were a lot of items in the database.

We began to experience some problems with the server around november 5th, and then again around november 7th. Both times the system had problems showing the `/latest` stats for about 24 hours. We spoke to the group around the time, and we were told that the problems had something to do with sudden spikes in the group’s Amazon Server. We were shown statistics from the AWS (Amazon Web Server) Dashboard, and it seemed like huge spikes of data sporadically hit and killed the server. The group later fixed this, and since then their systems has been running smoothly from what we have been seeing, steadily increasing the number of items in their database.

According to [this error chart](http://138.68.91.198/error_chart.svg), the simulator sometimes experienced a small range of problems when trying to insert new items in the database. These errors included:

* 401 (Unauthorized)
* 415 (Unsupported Media Type)
* 500 (Internal Server Error)
* 503 (Service unavailable)
* ReadTimeout
* ConnectionError

While we never got to use their system while it had these errors (due to our timing), they do make sense. The first and biggest one of their problems was the HTTP 415 error. This was because the group had prepared the application to only accept/parse messages that declared the JSON type in the header. The simulator script did no such thing at first, so no items got stored correctly to begin with. The HTTP 503 error confirms what we mentioned before; that the system had its ~48 hours of downtime. The simulator script would then receive this error, as the server was offline.

Despite of the relatively few errors, we didn’t experienced any critical or fatal problems that forced us to contact the group. All in all, they created a stable and fast system. They are also in the top five of groups that holds the largest number of simulated items consumed. That information is found in [this chart](http://138.68.91.198/chart.svg).

## 3. Discussion

In this final section of the report, we are going to tie the first and the second part of the report together, and conclude these

### 3.1. Technical discussion

As mentioned earlier, the whole beginning of the project went well, and our service was fully functional and responsive without any trouble for the first million items [according to this chart](http://138.68.91.198/chart.svg). The problems began to arise after this point, and we began consuming items at a slower rate and steadily falling behind.
To break it down, we had three major problems with our own system throughout the project. In chronological order:

1. ReadTimeout
2. Maven rebuild duplication
3. MongoDB CPU usage

The first error we encountered is one we noticed after the earlier mentioned [error chart](http://138.68.91.198/error_chart.svg) was revealed to us. It clearly states that the simulation script sending items into our system encountered an error called “ReadTimeout”. According to other developers<sup>7</sup>, this means that a timeout occurs while reading data. It has been hard to troubleshoot as our logs doesn’t show a lot, but we suspect that it’s due to a timeout set on the simulator sending items. If the simulator had a timeout, and our server was under pressure handling too many requests at the same time, it might not send an “ok” response back to the simulator in time. The error chart states that we lost more than half a million items this way.
Troubleshooting this was difficult when there was no trace of the error on our server side. Our latest idea was to try and change our code, so that the insertion of Items would work asynchronous instead of synchronous. The idea was that if our server could set aside its own thread every time a new item comes in, no items would have to wait in a queue, thereby hopefully eliminating the timeout issue. Unfortunately, we didn’t have enough time to implement and test this theory.

The second error was revealed to us when the server suddenly crashed and couldn’t be started again. We found out that it was the target application folder that duplicated itself infinitely, until the built War-file<sup>10</sup> was so big that it reached the maximum possible size and crashed the server. More details on this can be found in our [Post Mortem Analysis](https://github.com/KIMB0/LSD_frontend/blob/master/Documents/Post_Mrtem_Analysis_GroupE.pdf).

The third major error we encountered was the resource usage of our database. As our dataset grew, MongoDB’s built-in data queries had a hard time keeping up with the pressure of the many items inserted per minute. This led to a CPU usage of 200% by MongoDB. This caused instability, slow operations and crashes serverwide. We fixed this problem by isolating the MongoDB instance, and by changing Mongo’s indexing pattern. More details on this can be found in [our blog entry on the subject](https://github.com/KIMB0/ufo-blog-entry/blob/master/README.md).

### 3.2. Group work reflection & Lessons learned

During our project, the first important thing that we learned was: resource management and SOA. This is meant as a reference to a mistake of covering all the systems on one server. It is as a Software Engineer known to be a good idea to split up your services into a more Service Oriented Architecture (SOA). This intends to avoid the monolithic architecture, where everything is interwoven. We should have separated our components from the very beginning to avoid that everything would go offline if the server went offline. The only thing we split apart was our frontend from our backend.

The second important lesson we learned was to keep in touch with a system for longer time. We learned how to monitor, log, setup security, and maintain a system properly. It was a good experience to try out, so we know how it works in the real world.

The last thing was the idea of Continuous Integration, which was helpful when we needed to implement a new feature or patch new changes quickly. It also reduced the time spent on these things. It could now be focused elsewhere. In the future, this is properly one the things that we will use the most, since it is a timesaver. And as a developer you will very often find yourself out of time.

The intended roles didn’t strike through, and we don’t think that it would have improved our workflow if we followed the process. We think it’s more about communication when it comes to such a small team. If we were a team of 10-50 people, we would suggest to follow the assigned roles, so that no one would be getting in over their head, and thereby more prone to lose track of the development process.
We managed to keep track of our process with Github issues. Is this an ideal method? For us it worked very well; for others this might not be the case.

As operators the idea was good, but the concept was not brought to its fullest. The biggest issue was the project scope. It was a copy of an original system and it wasn’t going to be used in real life. This meant that it wasn’t really suffering from human interaction and mistakes. The only thing the simulator did was to push a lot of data into the system. Not interact. Although monitoring the other group’s system was mostly exciting and informative, we felt that doing this wasn’t as straightforward as it could have been. Some of the errors that we could have spotted were missed due to the fact that we also had to maintain our own system simultaneously.

## Conclusion

As a conclusion to the project, it might had been a good idea to start by digging into some documentation and searching around for information regarding the different tools we were going to be using. This includes all from MongoDB as database choice, Glassfish as application server, Java JAX-RS as a REST API, and Prometheus as monitoring tool. We should’ve been more educated in MongoDB to avoid the complications we experienced throughout the project. Also, we should’ve dug more into JAX-RS as a REST API, when it comes to the high amount of traffic we would be receiving. We’ve suspected that because Java has to convert the JSON format into an object and back again, it would take longer time than using javascript as instance, which is the name object notation.
Glassfish as an application server is overdone, when it comes to small applications like this. We could’ve considered other minimal application servers like Apache<sup>11</sup>, Tomcat<sup>12</sup> or Jetty<sup>13</sup>. In this case they would, probably, have suited our needs better.
The monitoring of the system was a great idea, but the Prometheus tool, like Glassfish, was a little overkill. It was only a single application that has to be monitored and in that case, this could’ve been done differently. As an alternative we could’ve used Monit<sup>8</sup>, which is a lightweight process supervision tool, that likely would’ve done the necessary. We are currently running Monit on the separate MongoDB server.

Apart from the more technical approaches; we didn’t follow any of the traditionally used development management frameworks like Scrum, Unified-Process or Extreme-Programming. What we did was we used Github as our backlog and then took it from there. A project member assigned himself to an issue, to keep track of who was doing what. We don’t believe a lot of engineers use this approach, but for us this worked quite well. It was nice to have everything at one place, instead of using multiple tools for the same purpose; thereby keeping the project on track.

## References

1. https://angular.io/
2. https://httpd.apache.org/
3. https://www.npmjs.com/
4. https://travis-ci.org/
5. https://jasmine.github.io/
6. https://karma-runner.github.io/1.0/index.html 
7. https://stackoverflow.com/questions/3069382/what-is-the-difference-between-connection-and-read-timeout-for-sockets
8. https://en.wikipedia.org/wiki/Monit
9. https://github.com/arnaudsj/monit
10. https://en.wikipedia.org/wiki/WAR_(file_format)
11. https://httpd.apache.org/
12. http://tomcat.apache.org/
13. https://www.eclipse.org/jetty/