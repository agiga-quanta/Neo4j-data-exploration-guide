# Guide to explore a music dataset with neo4j
This is a guide to exploring and visualizing data with `neo4j`. The dataset used here is a music dataset that has three types of entities: `Song`,`Playlist`, and `User` entities. This guide shows a step-by-step procedure for retrieving, setting up, and examining the dataset in `neo4j`.

## Part 1: Introducing the music dataset
To begin with, below is a dataset scheme to show how the three entity types `Song`, `Playlist`, and `User` are related to each other:
<img width="750" alt="image" src="https://user-images.githubusercontent.com/60938608/218912894-1252df75-4e19-43d2-bafc-6ddd87cdcffa.png">

### 1.1 Exploring entities:
There are three types of entities in the dataset:   
1. Song: The song has a `song_id`, `song_name` of the song, name of the `artist`, and the `emotion` evoked from Users 
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218912937-3ee1cd55-df2b-46e1-8cb4-1a1e8cfb5b69.png">

2. Playlist: The playlist has a `playlist_id`, the `playlist_name` of the playlist, the overall `purpose` of the playlist, and the number of `times_played` the playlist has been played.   
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218913068-b950cb0d-be53-49c2-baf2-c2ecd4fcf0f6.png">

3. User: The user has a `user_id`, their `user_name`, their geolocation in`location`, and the `user_type` of user. This is important because there are two types of users in this case: an individual, and an enterprise.   
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218912592-7d22bc48-5776-48bf-a975-734535906796.png">

### 1.2 Explaining two relationships
There are also 2 relationships between the three types:
1. `ADDED_TO`: this relationship is between a song and a playlist, showing the song being added to the playlist. Therefore, the relationship will also have property `from` and `to` to show where they begin and end. There is also a property called `added_by`, which indicates who added the song to the playlist.  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218912546-46c4f9a7-5a8d-4892-a4fa-816c99fcc679.png">

2. `SUBSCRIBE_TO`: users access the playlists by subscribing to them. Similarly, the relationship will also have property `from` and `to`, to show where they begin and end. Another property of this relationship is `recommended_by`, which shows who recommended the playlist to the user.  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218913041-e5094d50-3b6f-4e85-8645-b73b1d9d724b.png">

### 1.3 Visualizing with all data in the dataset
In this example, a detailed visualization of the graph below, where red represents songs, yellow represents playlists, and blue represents users.  

<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/218912637-7efaf8b6-dac8-4f3d-9c6b-cf167ba4dbba.png">

## Part 2: Setting up dataset in `neo4j`
### 2.1: Creating a new project in `neo4j`
Neo4j is a great tool for data exploration, especially for relational datasets such as the music dataset above. To start, create a new project by clicking on the `New` button shown below:  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634306-f8c94cb6-d9ba-42fd-994f-a96e88edb3ff.png">

Once there, hover your cursor near the title shown below and you will see a pencil icon that allows you to rename the project. 
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634331-4af62d78-18ff-4c3e-abad-71dd567fef8a.png">

After the creation process, click `Add` on the right side and select `Local DBMS` to launch a project that would use a local dataset. It will ask you for a name, and in this case, the name chosen is "Music dataset". 
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634371-8d410aec-ff52-44c0-9a46-586cb821ac9f.png">

You have now created and are ready to create the dataset in neo4j. 

### 2.2: Creating data 
To begin, click `start` to start your project:  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634818-46e50235-9de7-4487-96e1-2a0b95d0ee2d.png">

Once clicked, the database will take some time to get ready, after that, it should look like this:  
<img width="600" alt="image" src="https://user-images.githubusercontent.com/60938608/218634865-65309d5c-af29-4732-8065-2ab051e12209.png">

Now, click on the `Open` button to open Neo4j Browser, an interface to Neo4j Server. After opening it, your screen should look like this:   
<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/218634914-4bf117e8-efee-4d41-8277-640babdf77db.png">

```cypher
Playlist_id: pline.`Playlist ID`, 
Name: pline.`Playlist name`, 
Effect: pline.Effect
```

```cypher
CREATE CONSTRAINT [constraint name] IF NOT EXISTS FOR (n:Node) REQUIRE n.id IS NODE UNIQUE;
```

## Part 3: Neo4j queries explore
Now, to explore the data, we can try to use neo4j to answer the following questions:
- How many subscriptions do each playlist have?
- Which individual/enterprise listens to the song "Hey jude"?
- What are the songs that the "London Symphony" listens to?

At any time, you can open the `Summary data.png` to verify the answers below. 
<img width="978" alt="image" src="https://user-images.githubusercontent.com/60938608/218635173-d626d76f-4261-45a1-a206-ac5d4f5be5c3.png">

### How many subscriptions do each playlist have?
To solve this question, we need to count the users who are `:SUBSCRIBE_TO` all playlists. 
By deploying the `COUNT()` command, we can list the number of users who listens to each playlist. Another note here is the naming at the header of the table. Since we specify the for the `p.name` of the playlist to be called `playlist`, and the `COUNT(u)` as number_of_subscription, that's how we can change the header. The following two questions will not have this to demonstrate the difference.
![image](https://user-images.githubusercontent.com/60938608/218635200-c94ebf58-8946-449c-88c1-be7d8d26160f.png)

### Which individual/enterprise listens to the song "Hey jude"?
To solve this question, we are looking for `u:User` who has a relationship `:SUBSCRIBE_TO` to the `:Playlist` that also has the relationship `:ADDED_TO` that comes from `:Song` "Hey jude". The convenient thing about neo4j is that you can take that sentence above and put it in a readable phrase such as below, and the result will be shown.
![image](https://user-images.githubusercontent.com/60938608/218635233-d46beafb-10a1-488c-be77-86eaef354281.png)


### What are the songs that the "London Symphony" listens to?
To solve this question, we need to search the relationship path again. However, this time it will be the other way around, where we have the user, but now looking for the songs. The query shown below is another way of writing, where you can ask neo4j to search the data pathway without specifying anything besides the user name "London Symphony". 
![image](https://user-images.githubusercontent.com/60938608/218635255-41cf6b35-366d-4940-8b28-9ea0f19a0bed.png)


