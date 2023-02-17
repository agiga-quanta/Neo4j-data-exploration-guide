# Guide to explore a music dataset with neo4j
This is a guide to exploring and visualizing data with `neo4j`. The [dataset](https://github.com/agiga-quanta/Neo4j-data-exploration-guide/tree/main/music_data) used here is a music dataset that has three types of entities: `Song`,`Playlist`, and `User` entities. This guide shows a step-by-step procedure for retrieving, setting up, and examining the dataset in `neo4j`. A new section has also been added to show how to visualize our data with `NeoDash` - a dashboard builder built for neo4j desktop.

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
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634306-f8c94cb6-d9ba-42fd-994f-a96e88edb3ff.png">

Once there, hover your cursor near the title shown below and you will see a pencil icon that allows you to rename the project.  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634331-4af62d78-18ff-4c3e-abad-71dd567fef8a.png">

After the creation process, click `Add` on the right side and select `Local DBMS` to launch a project that would use a local dataset. It will ask you for a name, and in this case, the name chosen is "Music dataset".  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634371-8d410aec-ff52-44c0-9a46-586cb821ac9f.png">

You have now created and are ready to create the dataset in neo4j. Since our dataset are already made above in csv files, you can import them into the project by following the steps below:
- Select the `...` option  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219154173-59613b7a-ecae-4593-a804-e6b31bb924f3.png">

- Follow the path to `Open folder` -> `Imports`  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219154221-54e480fb-cdda-41c3-8741-c506a7677629.png">

- Then you can insert the csv files from the dataset [(here)](https://github.com/agiga-quanta/Neo4j-data-exploration-guide/tree/main/music_data). Once done, you can close the window.

### 2.2: Populating the project with the music dataset
To begin, click `start` to start your project:   
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634818-46e50235-9de7-4487-96e1-2a0b95d0ee2d.png">

Once clicked, the database will take some time to get ready, after that, it should look like this:   
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634865-65309d5c-af29-4732-8065-2ab051e12209.png">

Now, click on the `Open` button to open Neo4j Browser, an interface to Neo4j Server. After opening it, your screen should look like this:   
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218634914-4bf117e8-efee-4d41-8277-640babdf77db.png">

First, we create unique constraints for `song_id`, `playlist_id`, and `user_id` to preserve their uniqueness in the dataset. The code below does just that:
```cypher
//Create unique constraints
CREATE CONSTRAINT unique_song IF NOT EXISTS FOR (s:Song) REQUIRE s.Song_id IS NODE UNIQUE;
CREATE CONSTRAINT unique_playlist IF NOT EXISTS FOR (p:Playlist) REQUIRE p.Playlist_id IS NODE UNIQUE;
CREATE CONSTRAINT unique_user IF NOT EXISTS FOR (u:User) REQUIRE u.User_id IS NODE UNIQUE;
```
The final result looks like below:  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218947659-83659644-227e-489d-a518-4d850f62632d.png">

After that, you can now begin populating the project with the three entity types:
- For `Song` entity:
```cypher
//Load song data
LOAD CSV WITH HEADERS FROM 'file:///song_data.csv' AS sline
MERGE (s:Song {
    song_id: sline.song_id, 
    song_name: sline.song_name,
    artist: sline.artist,
    emotion: sline.emotion, 
    playlist_id: sline.playlist_id 
})
RETURN s
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218947907-b0f2410f-a5cb-4d00-8397-77cc6a640bb1.png">

- For `Playlist` entity:
```cypher
//Load playlist data
LOAD CSV WITH HEADERS FROM 'file:///playlists_data.csv' AS pline
MERGE (p:Playlist {
    playlist_id: pline.playlist_id, 
    playlist_name: pline.playlist_name,
    purpose: pline.purpose,
    times_played: pline.times_played, 
    user_id: pline.user_id 
})
RETURN p
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218947965-f8c36696-df5f-48f7-8da9-00661c2cb83b.png">

- For `User` entity:
```cypher
//Load user data
LOAD CSV WITH HEADERS FROM 'file:///user_profile.csv' AS uline
MERGE (u:User {
    user_id: uline.user_id, 
    user_name: uline.user_name,
    location: uline.location, 
    user_type:uline.user_type,
    playlist_id: uline.playlist_id` 
})
RETURN u
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218948032-758c8789-8ebc-4207-a930-e3f93405959b.png">

In order to provide the entities with their respective relationships, the code below allows you to link `User` with `Playlist` through `:SUBSCRIBE_TO`.
```cypher
//Create Subscribe relationship
LOAD CSV WITH HEADERS FROM 'file:///subscribe_relationship.csv' AS srlines
UNWIND srlines as srline
    MATCH (u:User {user_id: srline.from}), (p:Playlist {playlist_id: srline.to})
    MERGE (u)-[:SUBSCRIBE_TO {subscribe_relationship_id: srline.subscribe_relationship_id, from: srline.from, to: srline.to, recommended_by: srline.recommended_by}]->(p)
RETURN *
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218948126-bd971be0-7e1f-4866-9de3-ff7317388aaa.png">

 You can also link `Song` with `Playlist` through `:ADDED_TO`.
```cypher
//Create ADDED_TO relationship
LOAD CSV WITH HEADERS FROM 'file:///added_relationship.csv' AS arlines
UNWIND arlines as arline
    MATCH (s:Song {song_id: arline.from}), (p:Playlist {playlist_id: arline.to})
    MERGE (s)-[:ADDED_TO {added_relationship_id: arline.added_relationship_id, from: arline.from, to: arline.to, added_by: arline.added_by}]->(p)
RETURN *
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218948238-20199781-7286-4c3b-b5e9-393cb8a8aea8.png">

Finally, the graph is fully created. Similar to the graph at the end of Part 1 above, the graph can be displayed with the command below, and it should look identical:
```cypher
MATCH (n) RETURN *
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218948332-3ceee971-b8d2-44a7-8485-a97e5468f492.png">

## Part 3: Examining the music dataset
Now, to examine the dataset, we can try to use neo4j to answer the following questions:
- How many subscriptions do each playlist have?
- Which individual/enterprise listens to a particular song, such as the song "Hey jude"?
- Which songs are added to playlists by a random user, for example user "Gon"?

### 3.1 How many subscriptions do each playlist have? 
To solve this question, the idea is to count how many subscription each playlist has. The code below should explain itneed to count the users who are `:SUBSCRIBE_TO` all playlists. 
```cypher
MATCH (p:Playlist)<-[:SUBSCRIBE_TO]-(u:User)
RETURN  p.playlist_name as playlist_name, COUNT(u) as number_of_subscription
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218953960-42362c85-9bb6-4b08-b3d0-48c23a447667.png">
From this query, we can see neoclassic 2020 has the most number of subscription.

### 3.2 Which individual/enterprise listens to the song "Hey jude"?
To solve this question, we are looking for `u:User` who has a relationship `:SUBSCRIBE_TO` to the `:Playlist` that also has the relationship `:ADDED_TO` that comes from `:Song` "Hey jude". The convenient thing about neo4j is that you can take that sentence above and put it in a readable phrase such as below, and the result will be shown.
```cypher
MATCH (s:Song {song_name: "Hey Jude"})-[:ADDED_TO]->(:Playlist)<-[:SUBSCRIBE_TO]-(u:User)
RETURN u.user_name as user_name
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218954016-05768aee-6f23-48d3-8973-b30306f99597.png">
Turns out both Helen and the Cafe au Lait are subscribed to playlists that has "Hey Jude".

### 3.3 Which songs are added to playlists by a random user, for example user "Gon"?
To solve this question, we need to search the relationship path again. However, this time it will be the other way around, where we have the user, but now looking for the songs. The query shown below is another way of writing, where you can ask neo4j to search the data pathway without specifying anything besides the user name "London Symphony". 
```cypher
MATCH (u:User {user_name: "Gon"})-[:SUBSCRIBE_TO]->(:Playlist)<-[ar:ADDED_TO {added_by: "UID 1"}]-(s:Song)
RETURN COUNT(s) as number_of_recommendation
```
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/218953869-67fd7836-6942-4d50-816f-4dcb50b55da3.png">

## Part 4: Building dashboard with [`NeoDash`](https://neo4j.com/developer-blog/neodash-2-0-a-brand-new-way-to-visualize-neo4j/#:~:text=Once%20you%E2%80%99re%20done%20building%20your%20dashboard%2C%20you%20can,same%20database%20can%20load%20your%20dashboard%20into%20NeoDash.)
> `NeoDash` is an open-scource dashboard editor made in expanding neo4j's data. Specifically developing into creating dashboard, `NeoDash` allows you to group multiple visualizations, in many formats such as tabular, charts, maps, and more, while also capable of being shared with others.
### 4.1 Installing `NeoDash`
Firstly, to install `NeoDash`, click on the "four square" button on the left and then select `Graph Apps Gallery`:  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219416884-921c7358-bf54-4ca5-9e9e-cce926dcf577.png">

Secondly, locate `NeoDash` app and then click install. After the installation is complete, you can close the window.  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219417056-02384468-b877-490a-a392-b0ed79086944.png">

Now on neo4j desktop, you should see a new app available for you: `NeoDash`  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219417634-d0ddbdfd-211e-4723-bc8f-addebd895aa6.png">

### 4.2 Launching `NeoDash`
The first time using this app, a warning message will pop up, asking for permission to access the graph data:  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219418257-95620132-52ad-42e3-b857-7106e0ec2539.png">

You can click Yes. The data it can access are only those limited to the project "Music dataset". When opening, `NeoDash` will ask if you want to create a new dashboard, or load an existing dashboard, or connect to neo4j desktop. Since we are starting new, but we already have the data in neo4j desktop project, we can select `Connect to Neo4j Desktop`.   
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219420528-f66a9b1e-493e-4f26-9e04-ca1e3e9e4433.png">  

Once inside, you will be greeted with the following screen:  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219765488-b10fa080-3fd3-4e9a-9555-350ac3e65f45.png"> 

At the top, in the blue bar is where you can type the `Dashboard Name`.  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219765586-f961cd13-46f4-43ff-8c20-eea398f5caf5.png"> 

In a dashboard, you can have many `pages`. Usually these are for separating each of your topic, so readers do not feel overwhelmed. You start at the `Main Page` in this case.  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219765689-f10b599d-ff51-4466-8dd2-37f5a7a7c368.png"> 

In a page, there are these square boxes that are called `reports`. In this case, you start with two reports:  
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219765770-ab51d016-0fc8-4502-bb50-bf90a14f8b95.png">  
The left report even tells you some basic instruction on how to edit and run query while the right one shows the dataset!   

In each report, you can do multiple things:
1. Edit the name of the report. By clicking on the name, you can type to change the name of the report
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219766715-b0d50446-e49a-4909-83c0-7dfbd296a69d.png"> 

2. Move the report around the dashboard. By clicking and holding, you can move the report around any where in the dashboard.
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219767172-cb717ac3-d9e5-45f3-a56d-983c5481f62c.png"> 

3. Edit the query to change the content displaying. Clicking the icon will take you into the setting page of the report. Here you can also edit the queries, as well as setting the type of dashboard you want to display on the report.
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219767822-f4febc8b-8f96-442b-9f27-a360609f956c.png"> 

   - Edit query: You can change the query in this section below. 
    <img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219768501-5222c076-85f0-4141-9ec5-ebec364ba910.png"> 

   - Edit result type: You can change the result type clicking this button. 
    <img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219768293-f5a9ead1-93eb-432b-b917-7f35c67ae09b.png"> 
    
   - Edit data base that you are using. For when you are dealing with multiple databse from neo4j. 
    <img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219768373-d65e8dcf-c537-47ca-bc18-53263ffe503b.png">  
    
   - Once you are done, you can click `save` in the upper right corner to save and visual your query into the report.
    <img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219770221-19167c2d-834d-4505-ab58-d19775851555.png"> 
    
4. Change the label of the content displayed in the report. Once click, it will drop down a menu for you to choose which label/properties you want the content to show. 
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219767651-c3a6bfca-edd1-4731-be91-b66be8c48ded.png"> 

5. Change graph focus. This allows the graph to automatic change zoom level and re-center the graph for better fitting.
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219767270-46916933-3076-4016-a2b7-258c8233f4a0.png"> 

6. Change interactive. This will lock the interactive of the graph, so you cannot accidentally interact with the graph while moving your mouse in it.
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219767583-00c12081-e86a-4eed-9cbe-9f4d45eb36a3.png"> 

### 4.3 Saving dashboard in `NeoDash`
#### 4.3.1 Saving dashboard
On the left column, you can save the dashboard by clicking the save icon.   
<img width="640" alt="image" src="https://user-images.githubusercontent.com/60938608/219427487-0049a058-b41e-43b9-afe6-77c78b64bfaf.png">

You will then be asked whether to save the dashboard into neo4j, or into a separate file. Both options have their pros and cons:
|| `Save to Neo4j` | `Save to File`  | 
|-----|---------------|---------------|
| Distinct difference | You can quickly access the dashboard through NeoDash by connecting to Neo4j Desktop, NeoDash will search the project for the node and let you connect easily. This is benefitial if you are focusing working on one dataset at a time, and need to access the data directly.  | You can choose which dashboard you want to work on. This is a boon for those who do not have access to the data directly, and only work on creating dashboard |
| Choose saving option | <img width="320" alt="image" src="https://user-images.githubusercontent.com/60938608/219759943-0c084ddd-cb70-4537-a920-55cf324c8780.png"> | <img width="320" alt="image" src="https://user-images.githubusercontent.com/60938608/219759886-9adb0bba-478f-4351-bf79-fc27fe680071.png"> |
| Save the dashboard | <img width="320" alt="image" src="https://user-images.githubusercontent.com/60938608/219760410-492f1eb3-da9d-411b-be2c-772990eb3b91.png"> | <img width="320" alt="image" src="https://user-images.githubusercontent.com/60938608/219763518-07ce51a8-c524-4aa9-ae66-a1f1bbd8d1c1.png"> |

#### 4.3.2 Loading a dashboard 

Show how to load

And others can access the dashboard just by connecting to `neo4j desktop`. 







