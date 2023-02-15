# Guide to explore a music dataset with neo4j
This is a guide to exploring and visualizing data with `neo4j`. The dataset used here is a music dataset that has three types of entities: `Song`,`Playlist`, and `User` entities. This guide shows a step-by-step procedure for retrieving, setting up, and examining the dataset in `neo4j`.

## Part 1: Introducing the music dataset
To begin with, below is a meta graph to show how the three entity types `Song`, `Playlist`, and `User` are related to each other:  
<img width="750" alt="image" src="https://user-images.githubusercontent.com/60938608/219086977-ede3ff1e-0da8-4bde-a696-d6ff61298d0e.png">

### 1.1 Exploring entities:
There are three types of entities in the dataset:   
1. Song: The song has a `song_id`, `song_name` of the song, name of the `artist`, and the `emotion` evoked from Users  

| song_id | song_name       | artist           | emotion               | playlist_id  |
|---------|-----------------|------------------|-----------------------|--------------|
| SID 1   | Humanity        | Thomas Bergersen | Hopeful, fullfillment | PID 1        |
| SID 2   | High C’s        | Thomas Bergersen | Complex               | PID 1, PID 2 |
| SID 3   | Wandering nomad | Cody Francis     | Calming               | PID 2        |
| SID 4   | Centuries       | Fall Out Boy     | Hyping                | PID 3        |
| SID 5   | Hey jude        | The Beatles      | Nostalgic             | PID 4        |

2. Playlist: The playlist has a `playlist_id`, the `playlist_name` of the playlist, the overall `purpose` of the playlist, and the number of `times_played` the playlist has been played.   

| playlist_id | playlist_name               | purpose    | times_played | user_id             |
|-------------|-----------------------------|------------|--------------|---------------------|
| PID 1       | Neoclassic 2020             | Impressing | 4            | UID 1, UID 3, UID 5 |
| PID 2       | Coffee house thematic drink | Comforting | 12           | UID 2, UID 6        |
| PID 3       | Rock collection 2015        | Hyping     | 6            | UID 2, UID 5        |
| PID 4       | Playlist of 20th century    | Emotional  | 16           | UID 4, UID 7        |

3. User: The user has a `user_id`, their `user_name`, their geolocation in`location`, and the `user_type` of user. This is important because there are two types of users in this case: an individual, and an enterprise.   

| user_id | user_name                  | location          | user_type  | playlist_id  |
|---------|----------------------------|-------------------|------------|--------------|
| UID 1   | Gon                        | Tokyo, Japan      | Individual | PID 1        |
| UID 2   | Helen                      | Olympus, Greece   | Individual | PID 2, PID 3 |
| UID 3   | Ian                        | Paris, France     | Individual | PID 1        |
| UID 4   | Jack                       | Sydney, Australia | Individual | PID 4        |
| UID 5   | London Symphony Orchestral | London, England   | Enterprise | PID 1, PID 3 |
| UID 6   | Cafe au lait               | Toronto, Canada   | Enterprise | PID 2        |
| UID 7   | Spotify                    | Online            | Enterprise | PID 4        |

### 1.2 Explaining two relationships
There are also two relationships between the three types:
1. `ADDED_TO`: this relationship is between a song and a playlist, showing the song being added to the playlist. Therefore, the relationship will also have property `from` and `to` to show where they begin and end. There is also a property called `added_by`, which indicates who added the song to the playlist.   

| subscribe_relationship_id | from  | to    | recommended_by |
|---------------------------|-------|-------|----------------|
| SRID 1                    | UID 1 | PID 1 | Friends        |
| SRID 2                    | UID 2 | PID 2 | Friends        |
| SRID 3                    | UID 2 | PID 3 | Discovery      |
| SRID 4                    | UID 3 | PID 1 | Partnered team |
| SRID 5                    | UID 4 | PID 4 | Discovery      |
| SRID 6                    | UID 5 | PID 1 | Partnered team |
| SRID 7                    | UID 5 | PID 3 | Friends        |
| SRID 8                    | UID 6 | PID 2 | Partnered team |
| SRID 9                    | UID 7 | PID 4 | Discovery      |

2. `SUBSCRIBE_TO`: users access the playlists by subscribing to them. Similarly, the relationship will also have property `from` and `to`, to show where they begin and end. Another property of this relationship is `recommended_by`, which shows who recommended the playlist to the user.  

| added_relationship_id | from  | to    | added_by |
|-----------------------|-------|-------|----------|
| ARID 1                | SID 1 | PID 1 | UID 1    |
| ARID 2                | SID 2 | PID 1 | UID 2    |
| ARID 3                | SID 2 | PID 2 | UID 5    |
| ARID 4                | SID 3 | PID 3 | UID 6    |
| ARID 5                | SID 4 | PID 4 | UID 7    |
| ARID 6                | SID 5 | PID 5 | UID 4    |

### 1.3 Visualizing with all data in the dataset
In this example, a materialized graph for visualization of the dataset is below, based on the meta graph above.  

<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/219093458-d05d4fea-7694-4475-99b3-98637fa27591.png">

## Part 2: Setting up dataset in `neo4j`
### 2.1: Creating a new project in `neo4j`
Neo4j is a great tool for data exploration, especially for relational datasets such as the music dataset above. To start, create a new project by clicking on the `New` button shown below:  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634306-f8c94cb6-d9ba-42fd-994f-a96e88edb3ff.png">

Once there, hover your cursor near the title shown below and you will see a pencil icon that allows you to rename the project.  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634331-4af62d78-18ff-4c3e-abad-71dd567fef8a.png">

After the creation process, click `Add` on the right side and select `Local DBMS` to launch a project that would use a local dataset. It will ask you for a name, and in this case, the name chosen is "Music dataset".  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634371-8d410aec-ff52-44c0-9a46-586cb821ac9f.png">

You have now created and are ready to create the dataset in neo4j. 

### 2.2: Populating the project with the music dataset
To begin, click `start` to start your project:   
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634818-46e50235-9de7-4487-96e1-2a0b95d0ee2d.png">

Once clicked, the database will take some time to get ready, after that, it should look like this:   
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634865-65309d5c-af29-4732-8065-2ab051e12209.png">

Now, click on the `Open` button to open Neo4j Browser, an interface to Neo4j Server. After opening it, your screen should look like this:   
<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/218634914-4bf117e8-efee-4d41-8277-640babdf77db.png">

First, we create unique constraints for `song_id`, `playlist_id`, and `user_id` to preserve their uniqueness in the dataset. The code below does just that:
```cypher
//Create unique constraints
CREATE CONSTRAINT unique_song IF NOT EXISTS FOR (s:Song) REQUIRE s.Song_id IS NODE UNIQUE;
CREATE CONSTRAINT unique_playlist IF NOT EXISTS FOR (p:Playlist) REQUIRE p.Playlist_id IS NODE UNIQUE;
CREATE CONSTRAINT unique_user IF NOT EXISTS FOR (u:User) REQUIRE u.User_id IS NODE UNIQUE;
```
The final result looks like below:  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218947659-83659644-227e-489d-a518-4d850f62632d.png">

After that, you can now begin creating the three entity types:
- For `Song` entity:
```cypher
//Create Song entities
MERGE (:Song {
    song_id: "SID 1", 
    song_name: "Humanity",
    artist: "Thomas Bergersen",
    emotion: "Hopeful, fulfillment", 
    playlist_id: "PID 1" 
})
MERGE (:Song {
    song_id: "SID 2", 
    song_name: "High C's",
    artist: "Thomas Bergersen",
    emotion: "Complex", 
    playlist_id: "PID 1, PID 2" 
})
MERGE (:Song {
    song_id: "SID 3", 
    song_name: "Wandering nomad",
    artist: "Cody Francis",
    emotion: "Calming", 
    playlist_id: "PID 2" 
})
MERGE (:Song {
    song_id: "SID 4", 
    song_name: "Centuries",
    artist: "Fall Out Boy",
    emotion: "Hyping", 
    playlist_id: "PID 3" 
})
MERGE (:Song {
    song_id: "SID 5", 
    song_name: "Hey Jude",
    artist: "The Beatles",
    emotion: "Nostalgic", 
    playlist_id: "PID 4" 
})
```
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218947907-b0f2410f-a5cb-4d00-8397-77cc6a640bb1.png">

- For `Playlist` entity:
```cypher
//Create Playlist entities
MERGE (:Playlist {
    playlist_id: "PID 1", 
    playlist_name: "Neoclassic 2020",
    purpose: "Impressing",
    times_played: 4, 
    user_id: "UID 1, UID 3, UID 5" 
})
MERGE (:Playlist {
    playlist_id: "PID 2", 
    playlist_name: "Coffee house theme 2012",
    purpose: "Comforting",
    times_played: 4, 
    user_id: "UID 2, UID 6" 
})
MERGE (:Playlist {
    playlist_id: "PID 3", 
    playlist_name: "Rock collection 2015",
    purpose: "Hyping",
    times_played: 4, 
    user_id: "UID 2, UID 5" 
})
MERGE (:Playlist {
    playlist_id: "PID 4", 
    playlist_name: "Playlist of 20th century",
    purpose: "Emotional",
    times_played: 4, 
    user_id: "UID 4, UID 7" 
})
```
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218947965-f8c36696-df5f-48f7-8da9-00661c2cb83b.png">

- For `User` entity:
```cypher
//Create User entities
MERGE (:User {
    user_id: "UID 1", 
    user_name: "Gon",
    location: "Tokyo, Japan",
    user_type: "Individual", 
    playlist_id: "PID 1" 
})
MERGE (:User {
    user_id: "UID 2", 
    user_name: "Helen",
    location: "Olympus, Greece",
    user_type: "Individual", 
    playlist_id: "PID 2, PID 3" 
})
MERGE (:User {
    user_id: "UID 3", 
    user_name: "Ian",
    location: "Paris, France",
    user_type: "Individual", 
    playlist_id: "PID 1" 
})
MERGE (:User {
    user_id: "UID 4", 
    user_name: "Jack",
    location: "Sydney, Australia",
    user_type: "Individual", 
    playlist_id: "PID 4" 
})
MERGE (:User {
    user_id: "UID 5", 
    user_name: "London Symphony Orchestral",
    location: "London, England",
    user_type: "Enterprise", 
    playlist_id: "PID 1, PID 3" 
})
MERGE (:User {
    user_id: "UID 6", 
    user_name: "Cafe au Lait",
    location: "Toronto, Canada",
    user_type: "Enterprise", 
    playlist_id: "PID 2" 
})
MERGE (:User {
    user_id: "UID 7", 
    user_name: "Spotify",
    location: "Online",
    user_type: "Enterprise", 
    playlist_id: "PID 4" 
})
```
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218948032-758c8789-8ebc-4207-a930-e3f93405959b.png">

In order to provide the entities with their respective relationships, the code below allows you to link `User` with `Playlist` through `:SUBSCRIBE_TO`.
```cypher
//Create :SUBSCRIBE_TO relationship
MATCH (u:User {user_id: "UID 1"}), (u2:User {user_id: "UID 2"}), (u3:User {user_id: "UID 3"}), (u4:User {user_id: "UID 4"}), (u5:User {user_id: "UID 5"}), (u6:User {user_id: "UID 6"}), (u7:User {user_id: "UID 7"}), (p:Playlist{playlist_id: "PID 1"}), (p2:Playlist{playlist_id: "PID 2"}), (p3:Playlist{playlist_id: "PID 3"}), (p4:Playlist{playlist_id: "PID 4"})
MERGE (u)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 1", recommended_by: "Friends"}]->(p)
MERGE (u2)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 2", recommended_by: "Friends"}]->(p2)
MERGE (u2)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 3", recommended_by: "Discovery"}]->(p3)
MERGE (u3)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 4", recommended_by: "Partnered team"}]->(p)
MERGE (u4)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 5", recommended_by: "Discovery"}]->(p4)
MERGE (u5)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 6", recommended_by: "Partnered team team"}]->(p)
MERGE (u5)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 7", recommended_by: "Friends"}]->(p3)
MERGE (u6)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 8", recommended_by: "Partnered team"}]->(p2)
MERGE (u7)-[:SUBSCRIBE_TO {subscribe_relationship_id: "SRID 9", recommended_by: "Discovery"}]->(p4)
```
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218948126-bd971be0-7e1f-4866-9de3-ff7317388aaa.png">

 You can also link `Song` with `Playlist` through `:ADDED_TO`.
```cypher
//Create :ADDED_TO relationship
MATCH (s:Song {song_id: "SID 1"}), (s2:Song {song_id: "SID 2"}), (s3:Song {song_id: "SID 3"}), (s4:Song {song_id: "SID 4"}), (s5:Song {song_id: "SID 5"}), (p:Playlist{playlist_id: "PID 1"}), (p2:Playlist{playlist_id: "PID 2"}), (p3:Playlist{playlist_id: "PID 3"}), (p4:Playlist{playlist_id: "PID 4"})
MERGE (s)-[:ADDED_TO {ar_id: "ARID 1", added_by: "UID 1"}]->(p)
MERGE (s2)-[:ADDED_TO {ar_id: "ARID 2", added_by: "UID 2"}]->(p)
MERGE (s2)-[:ADDED_TO {ar_id: "ARID 3", added_by: "UID 5"}]->(p2)
MERGE (s3)-[:ADDED_TO {ar_id: "ARID 4", added_by: "UID 6"}]->(p3)
MERGE (s4)-[:ADDED_TO {ar_id: "ARID 5", added_by: "UID 7"}]->(p4)
MERGE (s5)-[:ADDED_TO {ar_id: "ARID 6", added_by: "UID 4"}]->(p2)
```
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218948238-20199781-7286-4c3b-b5e9-393cb8a8aea8.png">

Finally, the graph is fully created. Similar to the graph at the end of Part 1 above, the graph can be displayed with the command below, and it should look identical:
```cypher
MATCH (n) RETURN *
```
<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/218948332-3ceee971-b8d2-44a7-8485-a97e5468f492.png">

## Part 3: Examining the music dataset
Now, to examine the dataset, we can try to use neo4j to answer the following questions:
- How many subscriptions do each playlist have?
- Which individual/enterprise listens to a particular song, such as the song "Hey jude"?
- Which songs are added to playlists by a random user, for example user "Gon"?

### How many subscriptions do each playlist have?
To solve this question, the idea is to count how many subscription each playlist has. The code below should explain itneed to count the users who are `:SUBSCRIBE_TO` all playlists. 
```cypher
MATCH (p:Playlist)<-[:SUBSCRIBE_TO]-(u:User)
RETURN  p.playlist_name as playlist_name, COUNT(u) as number_of_subscription
```
<img width="900" alt="image" src="https://user-images.githubusercontent.com/60938608/218953960-42362c85-9bb6-4b08-b3d0-48c23a447667.png">
From this query, we can see neoclassic 2020 has the most number of subscription.

### Which individual/enterprise listens to the song "Hey jude"?
To solve this question, we are looking for `u:User` who has a relationship `:SUBSCRIBE_TO` to the `:Playlist` that also has the relationship `:ADDED_TO` that comes from `:Song` "Hey jude". The convenient thing about neo4j is that you can take that sentence above and put it in a readable phrase such as below, and the result will be shown.
```cypher
MATCH (s:Song {song_name: "Hey Jude"})-[:ADDED_TO]->(:Playlist)<-[:SUBSCRIBE_TO]-(u:User)
RETURN u.user_name as user_name
```
<img width="900" alt="image" src="https://user-images.githubusercontent.com/60938608/218954016-05768aee-6f23-48d3-8973-b30306f99597.png">
Turns out both Helen and the Cafe au Lait are subscribed to playlists that has "Hey Jude".

### Which songs are added to playlists by a random user, for example user "Gon"?
To solve this question, we need to search the relationship path again. However, this time it will be the other way around, where we have the user, but now looking for the songs. The query shown below is another way of writing, where you can ask neo4j to search the data pathway without specifying anything besides the user name "London Symphony". 
```cypher
MATCH (u:User {user_name: "Gon"})-[:SUBSCRIBE_TO]->(:Playlist)<-[ar:ADDED_TO {added_by: "UID 1"}]-(s:Song)
RETURN COUNT(s) as number_of_recommendation
```
<img width="900" alt="image" src="https://user-images.githubusercontent.com/60938608/218953869-67fd7836-6942-4d50-816f-4dcb50b55da3.png">


