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

### 1.3 Visualizing with all data in dataset
In this example, a detailed visualization of the graph below, where red represents songs, yellow represents playlists, and blue represents users.  

<img width="1200" alt="image" src="https://user-images.githubusercontent.com/60938608/218912637-7efaf8b6-dac8-4f3d-9c6b-cf167ba4dbba.png">
------------------------------
EXPLAIN MORE HERE
------------------------------
## Part 2: Neo4j import  
### 2.1: Outside of Neo4j
Neo4j is a great tool for data exploration, especially relational data such as our music database above. To start, we create a new project by clicking on the `New` button shown below:  
![image](https://user-images.githubusercontent.com/60938608/218634306-f8c94cb6-d9ba-42fd-994f-a96e88edb3ff.png)

Once there, you can hover your cursor near the title shown below and you will see a pencil icon that allows you to rename the project. 
![image](https://user-images.githubusercontent.com/60938608/218634331-4af62d78-18ff-4c3e-abad-71dd567fef8a.png)

After the creation process, click `Add` on the right side and select `Local DBMS` to launch a database that would use local data. It will asks you for a name, and in this case, the name chosen is "Music data flow db", since we will be exploring and visualizing the music db. 
![image](https://user-images.githubusercontent.com/60938608/218634371-8d410aec-ff52-44c0-9a46-586cb821ac9f.png)

You have now created a database. However, this database does not have our database yet. In order to do that, we will have to import the database. Click on the `...` shown below to access more options for the database. 
![image](https://user-images.githubusercontent.com/60938608/218634404-5a2d5705-713d-49ac-9a10-068ce7f8adbf.png)

A drop-down menu should have an `Open folder` option, click it. After that, select `Import`. Neo4j will now attempt to open the local import folder in a new window. 
![image](https://user-images.githubusercontent.com/60938608/218635781-ab1ee2da-f8b3-4c1d-8202-58f211d9d2e5.png) 

The import folder should look like below: 
![image](https://user-images.githubusercontent.com/60938608/218634434-95ab9bd3-8e61-4bb2-99dc-8a85d8d7230b.png)

Attached to this Github repository is a folder name music data. You can also click [here](https://github.com/agiga-quanta/Neo4j-data-exploration-guide/tree/main/music%20data) to go to it. Once you have downloaded it, proceed to copy the files into the import folder. The final result should look like below:
![image](https://user-images.githubusercontent.com/60938608/218634763-365985fb-76ac-4cec-a99a-dee9f80c0068.png)

Now you have your data at the right place and ready to import your data for exploring and visualizing! Congrats!

### 2.2: Importing data 
To begin, click `start` to start your project:
![image](https://user-images.githubusercontent.com/60938608/218634818-46e50235-9de7-4487-96e1-2a0b95d0ee2d.png)

Once clicked, the database will take some time to get ready, after that, it should look like this:
![image](https://user-images.githubusercontent.com/60938608/218634865-65309d5c-af29-4732-8065-2ab051e12209.png)

Now, click on the `Open` button to open Neo4j Browser, an interface to Neo4j Server. After opening it, your screen should look like this: 
![image](https://user-images.githubusercontent.com/60938608/218634914-4bf117e8-efee-4d41-8277-640babdf77db.png)

A note for you reader, Neo4j has a specialized command for importing csv files into the db. It's called `LOAD CSV` and ` LOAD CSV WITH HEADERS` for those with a header row. The format for the address of the import folder is `file:///` and then followed by the file name. In this case, we have three files to import: `Playlists.csv`, `Song data.csv`, and `User profile.csv`. 

For files without a space in their name, you can enter the name with no issue:
![image](https://user-images.githubusercontent.com/60938608/218634956-3a71e02f-7966-4f3d-b180-1ca4149a4ae1.png)

However, for the other two files, you have to explicitly type the space by adding `%20` where the space is. That would results in your query looking like this:

![image](https://user-images.githubusercontent.com/60938608/218634999-8c69f0e2-59b1-4f05-85bf-278b20f72199.png)
![image](https://user-images.githubusercontent.com/60938608/218635016-b70f80e5-2603-4add-b391-779bb074ba76.png)

These 3 commands are used to import the files and create the data assets with their respective properties. I would like to bring a special note to the assignment of the properties. 
```cypher
Playlist_id: pline.`Playlist ID`, 
Name: pline.`Playlist name`, 
Effect: pline.Effect
```
- By using the `LOAD CSV WITH HEADERS` command, you can directly assign the value from the headers column without directly saying where the columns are. Had you used the `LOAD CSV` without the headers, you would have had to specify which column each item is from. 
- Similar to the naming when using `LOAD CSV WITH HEADERS`, the naming of each header when having space needs special care. You do not have to add the `%20` for the space, but you will have to add the \` character before and after to properly identify the headers.

For our current dataset, since it is curated, we do not have to worry about duplicated data. However, in real-life situations, where data can come from more than one source, we need to add constraints so our data does not become messy and unordered. You can ensure the uniqueness of each data by giving each data asset a unique constraint. By using the command below, you can achieve such a thing.

```cypher
CREATE CONSTRAINT [constraint name] IF NOT EXISTS FOR (n:Node) REQUIRE n.id IS NODE UNIQUE;
```

To explain the code: 
- `CREATE CONSTRAINT` is the command itself. 
- [constraint name] will be the name of the unique constraint. It will also be the error message popping up if a query violates it. 
- `IF NOT EXISTS FOR (n:Node)` is for checking if the data asset type "Node" has a constraint on `n.id` yet or not. If there is already, the `IF NOT EXISTS FOR (n:Node)` will proceed to ignore this line and proceed.
- `REQUIRE n.id IS NODE UNIQUE` is the content of the constraint. This example means the id of `n` must be unique.

Finally, we can add the relationship of the data assets between the datasets. The data flow here shown in the Meta Scheme shows that `Song` will be `ADDED TO` a `Playlist` - or multiple of them - and `User` will be `SUBSCRIBE TO` a `Playlist`.

Therefore the codes below will allow you to add the relationships:
![image](https://user-images.githubusercontent.com/60938608/218635082-2dd69b8f-98df-414e-a4a2-3a8ec5aaf8f0.png)
This code shows how we are searching for `:Playlist` noted as `p`, and `:Song` noted as `s`. Our conditions for them are that s' `InPlaylist` which is an added property that has the playlist ids that added this song. We then tell merge those `s` to those `p` with the relationship `:ADDED_TO`. The square brackets `[]` are used to denote relationships in neo4j. 

![image](https://user-images.githubusercontent.com/60938608/218635099-0dd37ac3-cc3f-4cdc-8ad5-76ca125b8ff8.png)
Similarly, the subscribe relationship has the above code, where we are searching for `:Playlist` noted as `p`, and `:User` noted as `u`. The condition this time is that the user subscription property `Subscribe_to` must contain the `Playlist_id`. Once found, the same thing as above happened, where the nodes `p` and `u` are linked by the relationship `:SUBSCRIBE_TO`.

It is complete, you have now built the graph.
![image](https://user-images.githubusercontent.com/60938608/218635139-c8846429-b1a1-402c-b93e-14d89705da7e.png)

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


