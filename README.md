# mom-texter

### Notice: I would never use this program to fool people, and thus I will lead with an introduction, project description, and a section on ethics. Then I will discuss the implementation of this project, design decisions, and key takeaways.

### Introduction
We have all experienced this problem: we go on a fun trip and are having the time of our lives, but we leave our mom's to worry about our whereabouts. Then, we have to **painstakingly** take time out of our day, only to tell our mom that we are indeed alright. This problem is especially pressing when you are in the wilderness without service or wifi, having no way to contact your mom. How is she going to know that you have all of your limbs and have not been attacked by a bear? Impossible!!! 

I have discussed this project with some of my friends, and they have the same complaint: their moms expect a dull text requiring a boring update. (To be fair, your dad may worry about your whereabouts, but this is my project, and my dad couldn't care less about where I go and for how long.)

Luckily, with the revolutionary power of artificial intelligence, and thanks to this repository, we will not have to waste our time like this ever again. 

This repository implements a command line interface which takes in the dates of a trip and then uses OpenAI's GPT-3 and Twilio's texting service to message your mom about your trip. 

### Project Goal And Requirements:
The goal of this project was to make a CLI script that allows you to schedule auto-generated texts to your mom. Thus, this project required completing a variety of tasks. One has to be able to:

1) Implement a CLI script inquiring about the dates of a trip
2) Schedule an event to trigger a script
3) Autogenerate texts to one's mother
4) Send texts to one's mother
5) Be affordable to build

Moreover, completing this process required use of cloud technologies, since I didn't have access to an at-home server. 

### Ethics: 
I must first note that I would never use this service to fool my mother, only to show affection. I feel releasing this service to others could cause harm, so that I would not do. 

I view this project as a gag with a deeper twist. The gag is that I care enough about my mom to feel that I should give texts and spend enough effort to make this mom texter, yet I lament mundane, update texts. I know that in-person updates are far more interesting and valuable, yet feel social pressure to text boring how-do-you-do messages. But this gag has a dark side, admittedly. This project shows just how easily we can use and missuse services such as OpenAI to spread false information to love ones and, in other scenarios, to society as well. These automated texts are a form of missinformation, as the bot speaks on my behalf. If I can make this project as an individual, so too can bad actors in group and with far more impact. Thus, as much this mom-texter is a portfolio project, so too is a comment on our political culture.

### Implementing Step By Step:
1) To implement my CLI script, I used node.js along with the readline package to ask questions and store dates. On the backend, the prompt function inside of cli/prompt.js would take in dates and format them to be used by the scheduler. Additionally, I also containerized this script with Docker to allow me to more easily push code changes from my local machine onto the cloud. This Docker container would be pushed to AWS ECS. 
2) Being that I chose AWS, I used Amazon Eventbridge's Fargate to schedule running AWS ECS. Fargate allows you to schedule running containers. (I could have used Kubernetes to run containers but that would be serious overkill for my purposes.) 
3) Autogenerating texts, I used OpenAI's GPT-3 model, and I called this API using node.js with randomized prompts.
4) I used Twilio's cloud to send texts, once again implemented by node.js code.
5) The services I used costed under $20 for the development stage, as I needed to test code at each step. 

In sum, I created the following design:
![image](https://github.com/hockeyiscool19/mom-texter/assets/65208198/65318104-6d91-4cb0-af24-1e1f7f0149f0)

As this picture indicates, steps 1) and 2) are contained within the AWS cloud in separated docker images. Running the CLI, we connect to amazon EventBridge, scheduling events. In turn, EventBridge's Fargate runs a separate docker image with prompts GPT-3 to create a text to send to mom and uses Twilio's cloud to send the text. 
step 1) is represented by the dockerized CLI script on the left. 

### Design Decisions:
In this section, I will not cover every decision, but I would like to give a broad discussion on my thought process. This discussion will be guided by the questions I asked myself and by general questions. 

**Why did this project necessitate using the cloud and microservices?**
This project necessitated a microservice arquitechture, since there would have to a be a clear difference between the CLI script and running the ECS contianer at the alotted time. I could simply leave my computer running or have the time to spin up my own local server. Thus, I split this repository into two folders -- one for the CLI script, the other for the ECS infrastructure. 
![image](https://github.com/hockeyiscool19/mom-texter/assets/65208198/3ba07916-9d13-46ed-8ce4-7f032e73eed6)
As you can see in this picture, both of the code to instantiate these microservices are containerized and thus separated. 

**Why use Docker and ECS?**
I used Docker since it helped manage dependencies and provide a consistent run time environment locally and within the cloud. 

**Why use Fargate instead of EC2 or Lambda?**
I chose Fargate as opposed to EC2 since I only needed this message to send out at most once a day. Running an EC2 server would have costed a lot more than Fargate. Also, I chose Faraget over Lambda since I did not need to trigger it based on an event and payload, since my script did not take in any arguments.

**Why use Twilio over Amazon SNS?**
Getting a number allotted from Amazon would take up to two weeks, whereas Twilio provisioned the number immidietly. 

### Results
I have used this service to send beautiful texts, including the following hiku:

Wilderness calling out
Mom, come explore with me
Peaceful, quiet beauty

I have heard rave reviews from my mom, who really enjoys these texts. 

### Take Aways: 
In this project, I have shown proficiency in key backend softwares and the ability to learn new ones. Among them, I learned a lot about JavaScript, Node, Docker, and AWS. To be sure, this was my first project with node, ECS, and Fargate. I was generally happy how everything worked together! Moreover, this project also necessitated that I make these services work together. Thus, I showed the ability to think systematically about how microservices work together to get the mom-texts across!
