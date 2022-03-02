# Back-of-the-envelope estimation
One of the things I struggled with the most was being quick with my estimations. So I tried going through some of the problems only focusing on how well and quickly I could do my estimates.

---

## URL Shortening Service
(do your calculations now and go to next slide when finished)

---

Our system will be read-heavy. There will be lots of redirection requests compared to new URL shortenings. Let’s assume a 100:1 ratio between read and write.

Traffic estimates: Assuming, we will have 500M new URL shortenings per month, with 100:1 read/write ratio, we can expect 50B redirections during the same period:

100 * 500M => 50B
What would be Queries Per Second (QPS) for our system? New URLs shortenings per second:

500 million / (30 days * 24 hours * 3600 seconds) = ~200 URLs/s
Considering 100:1 read/write ratio, URLs redirections per second will be:

100 * 200 URLs/s = 20K/s

---

Storage estimates: Let’s assume we store every URL shortening request (and associated shortened link) for 5 years. Since we expect to have 500M new URLs every month, the total number of objects we expect to store will be 30 billion:

500 million * 5 years * 12 months = 30 billion
Let’s assume that each stored object will be approximately 500 bytes (just a ballpark estimate–we will dig into it later). We will need 15TB of total storage:

30 billion * 500 bytes = 15 TB

---

Bandwidth estimates: For write requests, since we expect 200 new URLs every second, total incoming data for our service will be 100KB per second:

200 * 500 bytes = 100 KB/s
For read requests, since every second we expect ~20K URLs redirections, total outgoing data for our service would be 10MB per second:

20K * 500 bytes = ~10 MB/s

---

Memory estimates: If we want to cache some of the hot URLs that are frequently accessed, how much memory will we need to store them? If we follow the 80-20 rule, meaning 20% of URLs generate 80% of traffic, we would like to cache these 20% hot URLs.

Since we have 20K requests per second, we will be getting 1.7 billion requests per day:

20K * 3600 seconds * 24 hours = ~1.7 billion
To cache 20% of these requests, we will need 170GB of memory.

0.2 * 1.7 billion * 500 bytes = ~170GB
One thing to note here is that since there will be many duplicate requests (of the same URL), our actual memory usage will be less than 170GB.

---

## Photo-sharing service like Instagram
(do your exercise now)

---

Let’s assume we have 500M total users, with 1M daily active users.
2M new photos every day, 23 new photos every second.
Average photo file size => 200KB
Total space required for 1 day of photos
2M * 200KB => 400 GB
Total space required for 10 years:
400GB * 365 (days a year) * 10 (years) ~= 1425TB

---

Some more in depth estimations

User: Assuming each “int” and “dateTime” is four bytes, each row in the User’s table will be of 68 bytes:

UserID (4 bytes) + Name (20 bytes) + Email (32 bytes) + DateOfBirth (4 bytes) + CreationDate (4 bytes) + LastLogin (4 bytes) = 68 bytes
If we have 500 million users, we will need 32GB of total storage.

500 million * 68 ~= 32GB
Photo: Each row in Photo’s table will be of 284 bytes:

PhotoID (4 bytes) + UserID (4 bytes) + PhotoPath (256 bytes) + PhotoLatitude (4 bytes) + PhotoLongitude(4 bytes) + UserLatitude (4 bytes) + UserLongitude (4 bytes) + CreationDate (4 bytes) = 284 bytes
If 2M new photos get uploaded every day, we will need 0.5GB of storage for one day:

2M * 284 bytes ~= 0.5GB per day
For 10 years we will need 1.88TB of storage.
UserFollow: Each row in the UserFollow table will consist of 8 bytes. If we have 500 million users and on average each user follows 500 users. We would need 1.82TB of storage for the UserFollow table:

500 million users * 500 followers * 8 bytes ~= 1.82TB
Total space required for all tables for 10 years will be 3.7TB:

32GB + 1.88TB + 1.82TB ~= 3.7TB
