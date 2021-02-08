# Algorithms

## Chapter 5 - Bit Manipulation

## Chapter 6 - Math and Logic Puzzles

## Chapter 7 - Object Oriented Design

- ask tons of questions about the problem
  - who what where when why
- figure out core objects, think business domain
- think about relationships between core objects
- think about actions within the system and how it affects each core object

### Singleton
  - a class that only has one instance
  - usually used for global static class 

### Factory Method
  - essentially creates an instance of a class, used ina centralized place that possibly needs to create different types of classes
  - 

## Chapter 8 - Recurssion and Dynamic Programming

## Chapter 9 - System Design and Scalability

### Handling the Questions
- communicate
- start broad
- convey your thoughts even if you are figuring it out
- acknowledge interview concerns
- be careful with assumptions, state them explicitly
- estimate when necessary
- drive the design process
  
### Design: Step by Step

**Step 1: Scope the Problem**
- scope out the problem so that you understand what you are designing and the extent at which things operate

**Step 2: Make Reasonable Assumptions**
- be smart about the assumptions you need to make, talk to your interviewer

**Step 3: Draw the Major Components**
- Use the white board, draw everything
  
**Step 4: Identify the Key Issues**
- bottlenecks and major challenges?
- take interviewers guidance and use it
  
**Step 5: Redesign for the Key issues**
- update design to accomadate for the key issues
  
### Algorithms that Scale: Step by Step
- instead of large scale system design they may ask you to design a single feature or algorithm with the intention of scale

**Step 1: Ask Questions**
**Step 2: Make believe**
- design firstly without performance or memory constraints
- This will help guide you to the general outline for solution
**Step 3: Get Real**
- go back and address the constraints to make it work now
**Step 4: Solve Problems**
- Solve the issues you address in **Step 3**
- you might be able to build upon the solution defined in step 2 or have to scrap it entirely
- iterative design, will allow you to address new issues
- you arent expected to design facebook over 30 mins
  - its normal to create a solution with flaws, its good to point them out to show you are aware
  

### Key Concepts

### Horizontal vs Vertical Scaling
- vertical = add more resources (more machines, more memory etc)
    - usually easier
- horizontal = increase number of nodes, more servers

### Load Balancer
- distribute load of a work so that each server/component is being optimized and one isnt being throttled

### Database Denormalization or NoSQL
- sql joins are very slow as system grows larger
- denormalization: adding redundant info into a db to speed up reads.
  - ex: you have a project that has a list of tasks, adding something like the project name to the task table so you can get all the info you need from just the task table
- or just use NoSQL to avoid all joins ever hehe

### Database Partitioning (Sharding)
- splitting the data across multiple machines while ensuring where data is still
- many systems use many different kinds
**Vertical Partitioning**
  - partitioning by feature (ex: social media; one for messages, one for contacts etc)
**Key/Hash-Based Partitioning**
- use an id or key to parition the data
- ex: allocate N nervers and put the data on mod(key,n) 
- issue: fixing number of servers and if we were to add more it would be very expensive
**Directory-Based Partitioning**
- have a lookup table where your data is partioned
- problem: single point of failure
- problem: lookup table is under constant load (aka bottleneck)

### Caching
- caching frequent things to avoid heavy repeated computations

### Asynchronous Processing & Queues
- move slow operations off thread or async
- use pre-processing (load data even if not 100% correct as its better to prevent stalling or long loading screens)
- alert users when the operation is done in the background
  
### Networking Metrics
- **bandwidth**: theoretical max of data through
- **throughput**: actual data through
- **latency**: how logn it takes data to go from A -> B

### MapReduce
- a program to process large amounts of data
- uses a mapping function to transform data into a k,v pair
- reduce takes a key and a set of associated values and "reduces" them somehow, emits a new key

### Considerations
- Failures: plan for system failure
- Availablility: how often a system operates
- Reliability: how prone the system is to be available
- Read Heavy vs Write Heavy
- Security: whew
- 
