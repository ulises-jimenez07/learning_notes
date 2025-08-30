# Software Architecture & System Design Practical Case Studies

## Introduction to Software Architecture & System Design

This section outlines the foundational concepts and a step-by-step process for system design. A key takeaway is that there is no single "correct" solution for any given system design problem. The process involves:

1. Gathering Functional and Non-Functional Requirements
2. Defining the system's API and the sequence of events
3. Designing to meet those requirements

## Design a Highly Scalable Image Sharing Social Media Platform

This case study focuses on designing a platform similar to Instagram or Flickr, capable of handling a massive number of users and images.

### Sequence Diagram and System's API

This diagram illustrates the flow of key user actions, from registration to viewing a timeline.

```mermaid
sequenceDiagram
    participant UserA as User A
    participant WebApp as Web App Service
    participant UserDetails as Store User Details
    participant StoreImage as Store User Image
    participant UserB as User B
    participant HomePage as Homepage
    participant Timeline as Load User Timeline
    participant UpdateFollowers as Update Followers

    UserA->>WebApp: get_homepage()
    WebApp->>HomePage: Registration Page
    UserA->>WebApp: register_new_user(first, last, username, email..)
    WebApp->>UserDetails: Store User Details
    WebApp-->>UserA: User_id + Auth Token

    UserA->>WebApp: post_image(user_id, image)
    WebApp->>StoreImage: Store User Image
    WebApp-->>UserA: Confirmation

    UserB->>WebApp: login()
    WebApp-->>UserB: Create Auth Token
    UserB->>WebApp: search_user(name/lastname/username)
    WebApp-->>UserB: Page with User A Images
    UserB->>WebApp: follow_user(follower_user_id, target_user_id)
    WebApp->>UpdateFollowers: Update Followers
    WebApp-->>UserB: Confirmation

    UserB->>WebApp: get_timeline(user_id)
    WebApp->>Timeline: Images of all users, followed by User B
```

**Explanation:** The sequence diagram shows two main workflows. The first is for User A registering and posting an image. The second is for User B, who logs in, searches for User A, follows them, and then requests their personal timeline. This highlights the core API calls and the interaction between a user and the Web App Service.

### Functional Software Architecture Diagram

This diagram shows the initial breakdown of the system into key services and their data stores.

```mermaid
graph TD
    subgraph Client
        UserA[User A]
        UserB[User B]
    end

    subgraph Backend
        WebS[Web App Service]
        UsersS[Users Service]
        SearchS[Search Service]
        TimelineS[Timeline Service]
        PostsS[Posts Service]
    end

    subgraph Data Stores
        ObjectStore[(Object Store)]
        DocStore[(Document Store)]
        SearchDocStore[(Search Document Store)]
        KeyValueStore[(Key/Value Store)]
        SQLDB[(SQL Database)]
    end

    UserA -- "Register User, Log In" --> WebS
    UserA -- "Post Image" --> WebS
    UserA -- "Get Homepage" --> WebS
    UserB -- "Search User" --> WebS
    UserB -- "Get Timeline" --> WebS

    WebS -- "Register/Log In" --> UsersS
    WebS -- "Search User" --> SearchS
    WebS -- "Get Timeline" --> TimelineS
    WebS -- "Post Image" --> PostsS

    UsersS --> DocStore
    SearchS --> SearchDocStore
    TimelineS --> KeyValueStore
    PostsS --> SQLDB
    PostsS -- "Upload Image" --> ObjectStore
```

**Explanation:** The functional diagram presents a microservices-based architecture. The Web App Service acts as a central entry point. Specific services handle different domain functionalities: Users Service for user data, Search Service for searching, Timeline Service for user feeds, and Posts Service for handling image posts. Each service uses a data store optimized for its specific task.

### Final Software Architecture Diagram

This final diagram refines the architecture by adding an API Gateway, a CDN, and an Image Processing Pipeline to handle scalability and performance.

```mermaid
graph TD
    subgraph Client
        UserA[User A]
        UserB[User B]
    end

    subgraph Backend
        APIGateway(API Gateway)
        WebS[Web App Service]
        ImagePipeline(Image Processing Pipeline)
        UsersS[Users Service]
        SearchS[Search Service]
        TimelineS[Timeline Service]
        PostsS[Posts Service]
    end

    subgraph Data Stores
        ObjectStore[(Object Store)]
        DocStore[(Document Store)]
        SearchDocStore[(Search Document Store)]
        KeyValueStore[(Key/Value Store)]
        SQLDB[(SQL Database)]
    end

    UserA -- "Requests" --> APIGateway
    UserB -- "Requests" --> APIGateway
    APIGateway -- "Web Requests" --> WebS
    APIGateway -- "Post Image" --> ImagePipeline

    WebS --> UsersS
    WebS --> SearchS
    WebS --> TimelineS
    WebS --> PostsS

    ImagePipeline -- "Store Original" --> ObjectStore
    ImagePipeline -- "Process & Store" --> ObjectStore

    UsersS --> DocStore
    SearchS --> SearchDocStore
    TimelineS --> KeyValueStore
    PostsS --> SQLDB

    subgraph Performance
        CDN(CDN)
    end
    PostsS -- "Image URLs" --> CDN
    CDN -- "Serves Images" --> UserB
```

**Explanation:** The addition of an API Gateway provides a single entry point for all client requests, handling authentication and routing. The Image Processing Pipeline is a critical component for scalability, as it asynchronously processes uploaded images (resizing, watermarking, etc.) to optimize them for various devices. Finally, a Content Delivery Network (CDN) is used to cache and deliver images and other static assets to users globally, reducing latency and load on the main servers.

## Design a Video-On-Demand (VOD) Streaming Service

This section covers the architecture of a VOD service like Netflix or YouTube.

### Sequence Diagram and API

This diagram shows the core user flows for both viewers and content creators.

```mermaid
sequenceDiagram
    participant Viewer
    participant ContentCreator
    participant VideoSearch
    participant GetVideoContent
    participant VideoIngestion
    
    Viewer->>VideoSearch: video_search(search_query)
    VideoSearch-->>Viewer: Top 20 Search Results
    Viewer->>VideoSearch: video_search(search_query, offset=20)
    VideoSearch-->>Viewer: Next 20 Search Results
    Viewer->>GetVideoContent: get_video(video_id)
    GetVideoContent-->>Viewer: Video URL
    Viewer->>GetVideoContent: play(video_id)
    GetVideoContent-->>Viewer: bytes

    ContentCreator->>VideoIngestion: login() + get_upload_page()
    VideoIngestion-->>ContentCreator: Video Upload Page
    ContentCreator->>VideoIngestion: upload_video(video)
    ContentCreator->>VideoIngestion: set_video_metadata(title, author, description..)
    VideoIngestion-->>ContentCreator: Confirmation
    VideoIngestion->>VideoIngestion: Video Ingestion
    VideoIngestion->>Notification: Email Notification - Video is Available
```

**Explanation:** The diagram shows the request-response cycle for a viewer searching for and playing a video. For a content creator, it outlines the video upload process, from logging in and getting the upload page to setting metadata and receiving a final confirmation.

### Functional Software Architecture Diagram

This diagram outlines the initial functional services and data flow.

```mermaid
graph TD
    subgraph Actors
        Creator[Content Creator]
        User[User]
    end
    
    subgraph Services
        APIGateway(API Gateway)
        WebApp(Web App Service)
        Transcoding(Transcoding Service)
        VideoPackaging(Video Packaging)
        VideoData(Video Data Service)
        Search(Search Service)
        Notification(Notification Service)
    end

    subgraph DataStores
        ObjectStore1[(Object Store)]
        ObjectStore2[(Object Store)]
        ObjectStore3[(Object Store)]
        NoSQLDB[(NoSQL Database)]
        SearchDB[(Search Database)]
        SQLDB[(SQL Database)]
    end

    Creator -- "Upload Video" --> WebApp
    User -- "Get Video URL" --> WebApp
    User -- "Search Query" --> WebApp

    WebApp -- "Upload Video" --> Transcoding
    WebApp -- "Get Video URL" --> VideoData
    WebApp -- "Search Query" --> Search
    WebApp -- "Email Notification" --> Notification

    Transcoding --> ObjectStore2
    Transcoding --> VideoPackaging
    VideoPackaging --> ObjectStore3
    VideoPackaging --> VideoData
    VideoData --> NoSQLDB
    Search --> SearchDB
    Notification --> SQLDB
```

**Explanation:** The architecture is designed for the unique challenges of video. A Transcoding Service converts uploaded videos into multiple formats and resolutions. Video Packaging prepares the transcoded videos for streaming. A dedicated Video Data Service manages video metadata, and a Search Service handles search queries.

### Final Software Architecture Diagram

This diagram adds a CDN for efficient video delivery.

```mermaid
graph TD
    subgraph Actors
        Creator[Content Creator]
        User[User]
    end

    subgraph Services
        APIGateway(API Gateway)
        WebApp(Web App Service)
        Transcoding(Transcoding Service)
        VideoPackaging(Video Packaging)
        VideoData(Video Data Service)
        Search(Search Service)
        Notification(Notification Service)
    end

    subgraph DataStores
        ObjectStore1[(Object Store)]
        ObjectStore2[(Object Store)]
        ObjectStore3[(Object Store)]
        NoSQLDB[(NoSQL Database)]
        SearchDB[(Search Database)]
        SQLDB[(SQL Database)]
    end

    subgraph Performance
        CDN(CDN)
    end
    
    Creator -- "Video Upload" --> WebApp
    User -- "Video Streaming" --> CDN
    User -- "Search Query" --> APIGateway
    User -- "Get Video URL" --> APIGateway

    APIGateway -- "Requests" --> WebApp
    APIGateway -- "Video Streaming" --> CDN

    WebApp --> Transcoding
    WebApp --> VideoData
    WebApp --> Search
    WebApp --> Notification

    Transcoding --> ObjectStore2
    Transcoding --> VideoPackaging
    VideoPackaging --> ObjectStore3
    VideoPackaging --> VideoData
    VideoData --> NoSQLDB
    Search --> SearchDB
    Notification --> SQLDB
    
    VideoPackaging --> CDN
```

**Explanation:** The final architecture incorporates a CDN for both static assets and the transcoded video files, which are the most bandwidth-intensive part of the application. This ensures low-latency video streaming to viewers worldwide by caching video segments at edge locations close to the users.

## Design a Highly Scalable, Real-Time Instant Messaging Service

This case study focuses on designing a messaging platform like WhatsApp or Slack.

### Sequence Diagram and System's API

This diagram shows the flow of signup, login, and message sending.

```mermaid
sequenceDiagram
    participant UserA as User A
    participant WebApp as Web App Service
    participant UserB as User B
    participant UserC as User C
    participant Authentication as Authentication
    participant StoreMessage as Store Message
    participant DeliverMessage as Deliver Message
    
    UserA->>WebApp: signup(full name, username, password, etc)
    WebApp->>Authentication: Authenticate
    Authentication-->>WebApp: Authentication token + User Id
    WebApp-->>UserA: Authentication token + User Id

    UserA->>WebApp: send_message(user_id1, user_id2..., text)
    WebApp->>StoreMessage: Store Message
    StoreMessage-->>WebApp: group_chat_id
    WebApp->>UserB: deliver_message(group_id, sender_id, text)
    WebApp->>UserC: deliver_message(group_id, sender_id, text)
```

**Explanation:** This diagram illustrates the core API calls and message flow. It shows how a user signs up and gets an authentication token. Then, when a message is sent, it is stored in the system and delivered to the intended recipients.

### Functional Software Architecture Diagram

This diagram shows the initial breakdown of services for an instant messaging platform.

```mermaid
graph TD
    subgraph Actors
        UserA[User A]
        UserB[User B]
        UserC[User C]
    end

    subgraph Services
        WebApp(Web App Service)
        Users(Users Service)
        ChatHistory(Chat History Service)
        GroupsChannels(Groups & Channels)
    end

    subgraph DataStores
        SQLDB[(SQL Database)]
        NoSQLDB[(NoSQL Database)]
        SQLNoSQLDB[(SQL/NoSQL Database)]
    end

    UserA -- "Requests" --> WebApp
    UserB -- "Requests" --> WebApp
    UserC -- "Requests" --> WebApp

    WebApp -- "Manage Users" --> Users
    WebApp -- "Store Chat" --> ChatHistory
    WebApp -- "Manage Groups/Channels" --> GroupsChannels

    Users --> SQLDB
    ChatHistory --> NoSQLDB
    GroupsChannels --> SQLNoSQLDB
```

**Explanation:** This architecture separates concerns into specialized services. The Users Service handles user data, Chat History Service stores and retrieves messages, and Groups & Channels manages the metadata for different chat rooms.

### Final Software Architecture Diagram

This diagram adds components crucial for real-time messaging, such as an API Gateway and a Connection Management service.

```mermaid
graph TD
    subgraph Actors
        UserA[User A]
        UserB[User B]
        UserC[User C]
    end

    subgraph Services
        APIGateway(API Gateway)
        WebApp(Web App Service)
        Messaging(Messaging Service)
        ConnectionManagement(Connection Management)
        Users(Users Service)
        ChatHistory(Chat History Service)
        GroupsChannels(Groups & Channels)
    end

    subgraph DataStores
        ObjectStore[(Object Store)]
        SQLDB[(SQL Database)]
        NoSQLDB[(NoSQL Database)]
        KeyValueStore[(Key/Value Store)]
        SQLNoSQLDB[(SQL/NoSQL Database)]
        Cache((Cache))
    end

    UserA -- "Requests" --> APIGateway
    UserB -- "Requests" --> APIGateway
    UserC -- "Requests" --> APIGateway

    APIGateway -- "Web Requests" --> WebApp
    APIGateway -- "Messages" --> Messaging

    WebApp -- "Manage Users" --> Users
    WebApp --> ChatHistory
    WebApp --> ConnectionManagement
    WebApp --> GroupsChannels

    Messaging -- "Deliver Messages" --> ConnectionManagement
    Messaging -- "Store Messages" --> ChatHistory
    Messaging -- "Fetch User Info" --> Users
    Messaging -- "Fetch Group Info" --> GroupsChannels
    
    ConnectionManagement -- "Manage Connections" --> KeyValueStore
    ChatHistory -- "Cache Recent" --> Cache
    ChatHistory --> NoSQLDB
    Users --> SQLDB
    GroupsChannels --> SQLNoSQLDB
```

**Explanation:** The API Gateway routes requests. The new Messaging Service is the core of the real-time functionality, handling the delivery and storage of messages. The Connection Management service is crucial for maintaining persistent connections with millions of users, often using technologies like WebSockets, and a Key/Value store to track which users are online and on which server. A Cache is added to improve performance for frequently accessed chat history.

## Design a Typeahead / Autocomplete for a Search Engine

This section focuses on designing a service that provides search suggestions as a user types.

### Functional Software Architecture Diagram

This diagram shows the components required to generate and serve typeahead suggestions.

```mermaid
graph TD
    subgraph Client
        UserA[User A]
        UserB[User B]
    end

    subgraph Services
        AutocompleteService(Autocomplete Service)
        AutocompleteUpdater(Autocomplete Updater)
        CDC(CDC)
        BigDataPipeline[Big Data Processing Pipeline]
    end

    subgraph Data
        KeyValueStore[(Key/Value Store)]
    end

    UserA -- "GET /complete?q=..." --> AutocompleteService
    UserB -- "GET /search?q=..." --> AutocompleteUpdater
    
    AutocompleteService --> KeyValueStore
    AutocompleteUpdater -- "New Queries" --> BigDataPipeline
    CDC -- "Data Updates" --> BigDataPipeline

    subgraph BigDataPipeline
        Map(Map)
        Reduce(Reduce)
    end

    BigDataPipeline --> Map
    BigDataPipeline --> Reduce
    Reduce -- "Processed Data" --> AutocompleteUpdater
```

**Explanation:** The system is split into two main flows. The "read path" is for User A, who interacts with the Autocomplete Service to get suggestions from a fast Key/Value Store. The "write path" is for User B, whose search queries are fed into a Big Data Processing Pipeline via the Autocomplete Updater. This pipeline uses MapReduce to process new data and update the suggestions. CDC (Change Data Capture) is also used to capture and process updates from other parts of the search system.

### Final Software Architecture Diagram

This diagram refines the Big Data Processing Pipeline and storage.

```mermaid
graph TD
    subgraph Client
        UserA[User A]
        UserB[User B]
    end

    subgraph Services
        AutocompleteService(Autocomplete Service)
        AutocompleteUpdater(Autocomplete Updater)
        CDC(CDC)
        BigDataPipeline[Big Data Processing Pipeline]
    end
    
    subgraph Data
        SMKV[S/M Key/Value Store]
    end

    UserA -- "GET /complete?q=..." --> AutocompleteService
    UserB -- "GET /search?q=..." --> AutocompleteUpdater

    AutocompleteService --> SMKV
    AutocompleteUpdater -- "New Queries" --> BigDataPipeline
    CDC -- "Data Updates" --> BigDataPipeline

    subgraph BigDataPipeline
        Map(Map)
        Reduce(Reduce)
    end
    
    AutocompleteUpdater -- "Sampler" --> BigDataPipeline
    BigDataPipeline --> Map
    BigDataPipeline --> Reduce
    Reduce -- "Processed Data" --> AutocompleteUpdater
```

**Explanation:** In this version, the Autocomplete Updater includes a Sampler to only process a representative sample of search queries, reducing the load on the Big Data Processing Pipeline. The Key/Value Store is now explicitly labeled as S/M (sharded and master/replica) to highlight the need for a highly available, distributed data store to handle the high-volume read traffic.

## Design a Scalable Ride Sharing Service

This section outlines the architecture of a service like Uber or Lyft.

### Sequence Diagram and System's API

This diagram shows the flow for both drivers joining the service and riders requesting a trip.

```mermaid
sequenceDiagram
    participant Rider
    participant Driver
    participant WebApp
    
    Rider->>WebApp: register(username, password, credit_card, etc)
    WebApp-->>Rider: Confirmation + User Id

    Driver->>WebApp: login(username, password)
    WebApp-->>Driver: Authentication token + Driver Id

    Driver->>WebApp: driver_join(latitude, longitude, driver_id)
    WebApp-->>Driver: Confirmation

    Rider->>WebApp: request_rider(latitude, longitude, user_id)
    WebApp-->>Driver: pick_up_rider(trip_info, latitude, longitude, etc)
    Driver->>WebApp: start_ride(driver_id, trip_id)
    WebApp-->>Driver: Confirmation
```

**Explanation:** The sequence diagram shows the core business logic. First, a driver logs in and joins the "pool" of available drivers. Then, a rider requests a trip, which triggers the system to find and notify a nearby driver. The diagram also includes the process of location updates, which are critical for real-time tracking.

### Functional Software Architecture Diagram

This diagram breaks down the rideshare service into a set of core services.

```mermaid
graph TD
    subgraph Actors
        Rider
        Driver
    end

    subgraph Services
        RidersService(Riders Service)
        DriversService(Drivers Service)
        UsersDriversService(Users/Drivers Service)
        MatchingService(Matching Service)
        TripManager(Trip Manager)
        LocationService(Location Service)
        PaymentService(Payment Service)
    end

    subgraph DataStores
        ObjectStore[(Object Store)]
        SQLDB1[(SQL Database)]
        SQLDB2[(SQL Database)]
        NoSQLDB[(NoSQL Database)]
        SQLDB3[(SQL Database)]
    end

    Rider --> RidersService
    Driver --> DriversService
    
    RidersService --> MatchingService
    DriversService --> MatchingService
    
    MatchingService --> LocationService
    MatchingService --> TripManager
    
    TripManager --> SQLDB2
    LocationService --> NoSQLDB
    PaymentService --> SQLDB3

    RidersService --> UsersDriversService
    DriversService --> UsersDriversService
    UsersDriversService --> SQLDB1
```

**Explanation:** The Riders Service and Drivers Service handle user and driver-specific functionalities. The Matching Service is the brain of the operation, pairing riders with drivers based on location and availability. The Location Service tracks real-time location data, and the Trip Manager handles the lifecycle of a trip.

### Final Software Architecture Diagram

This diagram adds services for notifications and map generation.

```mermaid
graph TD
    subgraph Actors
        Rider
        Driver
    end

    subgraph Services
        RidersService(Riders Service)
        DriversService(Drivers Service)
        UsersDriversService(Users/Drivers Service)
        MatchingService(Matching Service)
        TripManager(Trip Manager)
        LocationService(Location Service)
        PaymentService(Payment Service)
        NotificationService(Notification Service)
        TripMapGenerator(Trip Map Generator)
    end

    subgraph DataStores
        ObjectStore[(Object Store)]
        SQLDB1[(SQL Database)]
        SQLDB2[(SQL Database)]
        NoSQLDB[(NoSQL Database)]
        SQLDB3[(SQL Database)]
    end

    Rider --> RidersService
    Driver --> DriversService
    
    RidersService --> MatchingService
    DriversService --> MatchingService
    
    MatchingService --> LocationService
    MatchingService --> TripManager
    
    TripManager --> SQLDB2
    LocationService --> NoSQLDB
    PaymentService --> SQLDB3
    NotificationService --> SQLDB1
    TripMapGenerator --> ObjectStore

    RidersService --> UsersDriversService
    DriversService --> UsersDriversService
    UsersDriversService --> SQLDB1
    
    TripManager --> NotificationService
    TripManager --> TripMapGenerator
    NotificationService --> TripMapGenerator
```

**Explanation:** The final architecture includes a Notification Service to send push notifications or emails to users and drivers (e.g., "Your driver has arrived!"). A Trip Map Generator is added to create a visual representation of the trip, which can be sent as part of the ride receipt.

### Adding a Bloom Filter to the Users Service

This section explains how a Bloom filter can be used to quickly check if a username exists, minimizing database lookups for non-existent users.

```mermaid
graph TD
    subgraph Services
        UsersService[Users/Drivers Service]
    end

    subgraph BloomFilter
        Hash1["Hash1: f1(key) mod 128"]
        Hash2["Hash2: f2(key) mod 128"]
        Hash3["Hash3: f3(key) mod 128"]
    end

    subgraph BitArray
        BA(128-bit Array)
    end

    UsersService --> Hash1
    UsersService --> Hash2
    UsersService --> Hash3

    Hash1 --> BA
    Hash2 --> BA
    Hash3 --> BA
```

**Explanation:** A Bloom filter is a probabilistic data structure used to test for membership. When a user checks if a username is available, the system doesn't have to hit the database every time. Instead, it runs the potential username through several independent hash functions. The output of each hash function corresponds to a bit in a large bit array. If all the bits are set to 1, the username may exist. If even one bit is 0, the username definitely does not exist. This is a powerful optimization, as database calls are expensive.

### Adding Geohash

**Explanation:** Geohash is a geocoding system that encodes a location (latitude and longitude) into a short string of letters and digits. A key feature of Geohash is that locations that are close to each other have a similar Geohash prefix. This property is incredibly useful for a rideshare service, as it allows for efficient searching of nearby drivers. Instead of searching a massive database for all drivers within a certain radius, the system can simply search for drivers whose location data (stored as a Geohash) shares a similar prefix with the rider's location. This makes finding the closest drivers much faster and more scalable.

```mermaid
graph TD
    subgraph Actors
        Rider
        Driver
    end

    subgraph Services
        APIGateway(API Gateway)
        ConnectionManager(Connection Manager)
        RidersService(Riders Service)
        DriversService(Drivers Service)
        UsersService[Users/Drivers Service]
        MatchingService(Matching Service)
        TripManager(Trip Manager)
        LocationService(Location Service)
        PaymentService(Payment Service)
        NotificationService(Notification Service)
        TripMapGenerator(Trip Map Generator)
    end

    subgraph DataStores
        ObjectStore[(Object Store)]
        SQLDB1[(SQL Database)]
        SQLDB2[(SQL Database)]
        NoSQLDB[(NoSQL Database)]
        SQLDB3[(SQL Database)]
    end

    Rider --> APIGateway
    Driver --> APIGateway
    
    APIGateway -- "Web Requests" --> RidersService
    APIGateway -- "Web Requests" --> DriversService
    APIGateway -- "Connections" --> ConnectionManager
    
    RidersService --> MatchingService
    DriversService --> MatchingService
    
    MatchingService --> LocationService
    MatchingService --> TripManager
    
    TripManager --> SQLDB2
    LocationService --> NoSQLDB
    PaymentService --> SQLDB3
    
    RidersService --> UsersService
    DriversService --> UsersService
    UsersService --> SQLDB1
    
    TripManager --> NotificationService
    TripManager --> TripMapGenerator
    NotificationService --> TripMapGenerator
```
