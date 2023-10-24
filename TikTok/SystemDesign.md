# System Design - TikTok

# **How will you design TikTok ?**

**Functional Requirements -**

1. User should be able to upload videos. (Max video length - 1 minute) (Videos can be caption-able)
2. Tiktok feed where you should be able to see videos.
3. Users should be able to follow each others.
4. Should be able to see videos from whom they are following.
5. Users should be able to perform actions on videos. Like saving, commenting etc.

**Non Functional Requirements -**

1. Availability - Highly Available (99.999%)
2. Latency
3. Scalability - 1 million daily active users.

**Counter Questions →**

1. Will the videos on the feed be only from the people I follow or other algorithmic videos will be there too.

**Estimates →**

- Videos 5 MB per video.
- Each users uploads 2 videos per day.
- Total user uploads size - 10 MB Per day.
- User Metadata - 1KB per user/day.

**Database Schema →**

1. User -
   - user_id - Primary Key, uuid, Auto Increment
   - first_name - string
   - last_name - string
   - email - string
   - phone_number - string
2. Video - We will be storing the actual video at a cloud storage.
   - id - Primary Key, Auto Increment
   - video_link - varchar
   - user - User
   - meta_data - string
3. UserActivity -
   - user_id - User
   - following - [User]
   - likes - [video_link]
   - favorites - [video_link]

**Endpoints -**

1. /upload_video - user_data + video + meta_data → stores the actual video at a Blob storage. and link to the video at Relational DB.
2. /view_feed - GET → using uuid → CACHE → precache_service ← Read-Only DB ← Primary DB ← /upload_video
3. /user_actions - (Following users + Liking videos + Commenting) - GET → RDS

![Screenshot 2023-10-06 at 1.03.12 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/4fd32f6d-2f23-4952-97e4-9d87cf957351/1a23f496-0c27-4ef0-ad42-81f3c0a5cadb/Screenshot_2023-10-06_at_1.03.12_AM.png)

**Followup Questions -**

1. What can be the bottleneck for this problem. and how will you resolve it?

   Databases can be bottleneck. -

   Solution →

   1. Have Write-Only DB and Read-Only DB.

   1. Database Sharding (Location of the user || video category).
   1. Multiple Replicas to avoid single point of failure.
   1. We should be able to auto scale the db.

**My Questions -**

1. What is a Blob Store?

   Blob storage is a type of cloud storage storage that is optimised for storing large amount of unstructured data like audio, text, video. Eq - Amazon S3, Google Cloud Storage etc.

2. What is a pre-cache service?

   Pre cache is a service that stores data inside a cache in hopes of getting future requests. Good for reducing latency and reducing the number of requests made to the server.

   Good for storing Static files like HTML, CSS, JS files.

   types -

   1. client side pre caching - when we store assets on a users device.
   2. server side pre caching - when we store assets on a server. this can be done using CDN or just store the data inside server’s file system.

**NOTE** : With system design interviews you are not to required to go into the details of the apis and all but define the overview, flow and the architecture of the system.
