# Approach During the Interview
In my humble opinion the Design Interview is meant to to gauge how well an individual can take on ambiguity. 

In other words, how well you can structure the unstructured. 

---

A quick google search gives you these following steps,  which are what you will find most of the time.

* Step 1: Requirements clarifications.
* Step 2: Back-of-the-envelope estimation.
* Step 3: System interface definition.
* Step 4: Defining data model.
* Step 5: High-level design.
* Step 6: Detailed design.
* Step 7: Identifying and resolving bottlenecks.

---

These steps are very helpful but, without some guidence, can be executed poorly. The idea is to approach these problems systematically but not mindlessly in a way that every decision you take is backed by some reasonable criteria.

---

### Step 1: Requirements clarifications

Getting the requirements for a design is about making a list of 3-5 functional requirements and maybe 2-4 non functional requirements. This is pretty straightforward.

---

### Step 2: Back-of-the-envelope estimation

The important thing here is to consider the features defined for your system. Ask yourself things like

This normally breaks down in 4 sections:

* Traffic
    * Number of users
    * Number of petitions(posts, uploads, feeds generated) per month/day/year/5 years
    * Ratio of read:write
* Storage
    * Size of objects/files/images/tweets etc
    * Total storage needed for 5 years
* Bandwith
    * Speed of read transacctions multiplied by size of object
    * Speed of writted transacctions multiplied by size of object
* Memory 
    * Caching needs
    * Normally just use the 80/20 rule for hot data

Where to start?
    Normally doing all of the calculations takes too much time. So you must decide what is important in the system.
    Ask yourself about which of these 4 sections are happening at a large scale. And from there do the minimum calculations you need to answer your questions.

---

### Step 3: System interface definition

This is also pretty straightforward, define the end result. What should I be able to call at the end of the day?
Normally this will be some API definition. So think about what you will need for input and what you should expect as the output.

---

### Step 4: Defining data model

Identify the main entities you need for your system. Basically you need to choose whether to use a SQL or NoSQL solution to store your tabular data.
Ask yourself these questions:

* Does you data have a lot of relations?
    * Yes? SQL, else NoSQL
* How much will your data scale?
    * So much you need a distributed data store? NoSQL, else SQL
* Do you need ACID transactions?
    * This will be important when high consistency is needed.
    * Yes?
        * Depends. The easiest implementation would be to use SQL
        * But if you also need large scale then you would need to incorporate the support for ACID properties programmatically in your logic. (Example in Dropbox design)
        * In that case you can use NoSQL but it will be more difficult to implement.
    * Else
        * NoSQL
* Do you need partitioning? (Same as data scale question)
    * Yes? NoSQL, else SQL

Also take into account block storage, basically store your files/images/videos in S3.

---

### Step 5: High-level design

Draw a block diagram of 5-6 boxes. **DON'T** focus too much on scaling at this point.
The idea is to get a basic solution for this system. Maybe seperate your services on seperate servers or add a load balancer or two but no more.
Give a clear idea of how the requests will be processed, how communication will be handled and where you will store data.

---

### Step 6: Detailed design

At this point you should've driven the entire design except maybe if yout interviewer points something out. 
Now you should let go a bit and let your interviewer lead what components you should focus on. You should further develop two or three more components.
If you don't receieve any cue from your interviewer and take the initiative of mentioning what components seem more important to focus on. Maybe even make a list of questions in order of importance. Explain why they're in that order.
For example:
1. Since we will be storing a massive amount of data, how should we partition our data to distribute it to multiple databases? Should we try to store all the data of a user on the same database? What issue could it cause?
2. How will we handle hot users who tweet a lot or follow lots of people?
3. Since users’ timeline will contain the most recent (and relevant) tweets, should we try to store our data so that it is optimized for scanning the latest tweets?
4. How much and at which layer should we introduce cache to speed things up?
5. What components need better load balancing?

You should now go on to present **MULTIPLE** approaches for each problem, mentioning the tradeoffs of each approach and choosing what approach you will go with and why.

Don't focus too much on changing your block diagram because it could consume too much time and it's not too important. Maybe add some changes to convey your idea, it's ok to work through this part like a conversation.

---

### Step 7: Identifying and resolving bottlenecks

This focuses a bit more on your systems relaiability. Maybe it wasn't on your non functional requirements but it doesn't mean that you shouldn't think of single point of failures or data replication. Think of these questions:
* Is there any single point of failure in our system? What are we doing to mitigate it?
* Do we have enough replicas of the data so that we can still serve our users if we lose a few servers?
* Similarly, do we have enough copies of different services running such that a few failures will not cause a total system shutdown?
* How are we monitoring the performance of our service? Do we get alerts whenever critical components fail or their performance degrades?
