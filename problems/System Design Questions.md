# System Design and Scalability

- [System Design and Scalability](#system-design-and-scalability)
  - [9.1 Stock Data](#91-stock-data)
  - [9.2 Social Network](#92-social-network)
  - [9.3 Web Crawler](#93-web-crawler)
  - [9.4 Duplicate URLS](#94-duplicate-urls)
  - [9.5 Cache](#95-cache)
  - [9.6 Sales Rank](#96-sales-rank)
  - [9.7 Personal Financial Manager](#97-personal-financial-manager)
  - [9.8 Pastebin](#98-pastebin)
  
NOTE: This section is less about the answers and more notes that are important to learn
NOTE: I will not be writing down each questions description

## 9.1 Stock Data
- no sql would be better for variable changing data
- easy queries for users
- json format is universal for people as a service
- will require a web server to interface with as we dont want clients directly tying into the database

## 9.2 Social Network
- Scope for now just not large scale data
- Bi-directional BFS will help with finding shortest path 
- Scope for million:
  - The user data will definitely sit on multiple machines
  - We can use a hashlookup that will keep which person sits on what machine (stored in db)
  - We can then call to that server to get the person data for that (can contain queries to its database shard)
- Optimization:
  - Batch calls to a server such that instead of visiting a new machine everytime, we watch until we have a min limit to go fetch to reduce requests
  - since people are usually friends with people near them, we should split people -> machine by location as it will most likely reduce the number of jumps needed
  - Yuo could prioritize searching people with larger pools of friends as that could aid the search to find you and the other person
  - could cache results of repeated calls, using DP caching to store shortest path between nodes possibly

## 9.3 Web Crawler
- simple BFS where to avoid loops we have a hashset of visited pages
- how do we determine if a webpage has been visit?
- We can't look at url => diff urls might be same page, and others not
- We can't look at content => diff content on same page is likely
- We need to do both and compare it as a similarity rather than an absolute
- We can then use bFS where we search things based on priority (priority queue)
- We can create the hash based on content and the url
- When going to a web page we check the server (db) if there exists a page that has a similar hash
  - if so insert into database with low priority
  - else, crawl the webpage for links and add those to the queue (database) with higher priority

## 9.4 Duplicate URLS
- creating a hashset of urls for fast lookup is the basis of the solution for this
- but the problem is that storing all urls in a hashtable could take TB of data
- What we are going to do is split up the data into manageable chunks such that urls with teh same hash gets written to seperate files
- we can then do a second pass on that document written for that chunk and see if it is duplicated (could stored as a hash table: url =>counts)
- this would rely on our hash to make sure we put the same hashes in the same textfile which can be easily done

## 9.5 Cache 
- we need a cache that can do the following:
- fast lookup for a result(value) from a query(key)
- purge old data so that the cache does not hold old out of date data
- We can use a hashtable for fast lookup
- and use a linked list to remove tail elements that are old when the list gets longer than we want
- we can use the hashtable link to the node itself, and we can move it to the front of the list ot update its "relevancy"
- Scalability:
  - We can split up the cache across all our machines such that each machine holds a portionof the cache
  - we can than use a hash to determine which machine holds which key in the cache so that we can retrieve a machines cache and see if it hits
  - updating results when content change:
    - We can impose a self removing cache key system where after X amount of seconds a query result will get removed from the cache
      - this is for when we know popular data is being changed often and we cant rely on the cache
    - on top of that we can periodically crawl through teh cache and check if the results are still teh same, if not we can remove it
- Optimizations:
  - if we had a query that was really popular it would always go to the machine j, we could have a seperate caceh on each machine that could cache which query was directed to what machine, so we can easily point to machines we know have the real cache

## 9.6 Sales Rank
- we dont need the mostup to date data for this system, best selling can be a couple days old or a week late, wont matter
- we can store this is a simple file directory where each directory has a list of sales
- We will instantly store things like sales and stuff
- but we can sort it occasionally every hour or so whenever we feel it is necessary
- to get overall best selling we can do a merge sort of each directory and get the top N for two merged arrays, then move on to the next, etc etc

## 9.7 Personal Financial Manager
- this system will entirely be data pulling since the bank accounts wont be giving us data
- we dont need to set up instanteous data pulling since people most likely wont need to check this data that often
- email push notifications?
- we should utilize a job/task queuing system for things like:
  - data pulling
  - categorizer
  - budget analzyer
  - email notifications
- We canuse a priority system to get jobs to be completed sooner, still making sure low priority jobs get completed at a point
- this system will contain a large number of inactive users, we should make sure to scale so that we either deactivate them or allocate less resources towards it
  - involves account tracking activity
- nosql db is probably better to have document based as opposed to data joining in sql

## 9.8 Pastebin
- since the documents can be large and searching them or doing anything like that is needed, we dont need a traditional DB (wasted overhead)
  - we can store it as files on a server
- we can partition files across multiple servers with our main webserver keepign track of which url and document is stored on which machine
  - this can be used in conjuction with the url hashing method we make to generate
- to store visits and analytics we can then have a simple db that stores ip,date,time in a row with the associated url with it
- use a cache to reduce file reading time on documents that are being read often
  - We can use a priority system where higher priority files are being kept in the cache whereas others get kicked out
    - we can  utilize the linkedlist hashset for that
- generating URLS:
  - we could use guids but thats overkill and ugly
  - We can simple just use random 10 alphanum character
  - chances for collision are low but we can verify it by checking to see if it already exists
    - if so we can generate a new one and repeat, the chance for collision gets lower and lower