+++
title = 'Amazon Interview Experience - Android Engineer SDE-II'
date = 2024-05-04T21:56:17+05:30
draft = false
+++

I recently joined Amazon and would like to share my interview experience for anyone who is intending to prepare for an engineering role at Amazon.

With enough preparation and a bit of luck, anyone can crack FAANG. (Though you need to be a decent engineer; otherwise, no amount of preparation can help you get a role!)

I will not be able to share the exact questions asked in my interview due to an NDA, but I will share all the resources that helped me prepare for the role and the kinds of questions asked in each round.

## Backstory

I got laid off in December from a startup where I worked mostly on a short video app and a text-to-video product due to the financial crunch faced by the startup. I was obviously disappointed as we built great products over the period of the last 5 years. It had been almost more than 6-7 years since I had actually prepared for interviews, and I was not sure where to start.

I gave a couple of interviews without any preparation right away and failed to clear the screening rounds of DSA coding questions. No matter how good of an engineer you are, you need to keep practicing DSA questions to be able to clear screening rounds. So I decided to take a break of a couple of months to practice and refresh my DSA skills before giving interviews for good companies.

## Applying for the job

I got the interview via a referral, which is the fastest way to secure an interview. Instead of relying on a direct application, you should always seek referrals. There are two types of referrals at Amazon: one where an Amazon employee knows and can vouch for the candidate, and another where an Amazonian refers someone from their network without personally knowing the candidate.

It's always better to get the first kind of referral, and for that purpose, you need to find someone at Amazon who can truly vouch for you. I would be more than happy to provide a referral for you; reach out to me via email with your resume for the same. I have opened up some slots for mock interviews over the weekend; you can book one **[here](https://topmate.io/amit_bhandari_c/)** (it’s free of cost and subject to my availability)."

## Round 1 - DSA 

The first round was divided into two parts: 30 minutes of coding questions and 30 minutes of behavioral questions to assess me on Amazon's leadership principles. I was asked a medium difficulty question based on graphs, for which I had to construct a graph from input data and perform a BFS (Breadth-First Search) to obtain the desired output.

Having practiced many graph-related questions on LeetCode beforehand, I was able to solve the problem comfortably within 15-20 minutes.

Following questions for leetcode are the closest one which I could found

* [Problem 1](https://leetcode.com/problems/keys-and-rooms/description/) 
* [Problem 2](https://leetcode.com/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/description/)


Resources which I used for my DSA preparation 

* [neetcode.io](https://neetcode.io/roadmap) 
* [150 Interview Questions](https://leetcode.com/studyplan/top-interview-150/) 
* [75 Interview Questions](https://leetcode.com/studyplan/leetcode-75/) 
* [Codehelp](https://www.youtube.com/watch?v=WQoB2z67hvY&list=PLDzeHZWIZsTryvtXdMr6rPh4IDexB5NIA) (Specially for DP, Recursion problem solving)

Interviewer was convinced with my DSA and coding skills and moved on to **[behavioural questions](#behavioural-questions)** section of interview. 


## Round 2 - Logical and Maintainable code writing

This round focuses more on candidates ability to write code which is clean, maintainable and provides logical separation of concerns. 

Candidates with decent exposure of working in team of more than 5-6 good engineers do not even need to prepare for this round to be able to ace it. 
All you need to do is write code which can be maintained in long run by following good practices such as naming classes, variables, methods properly, providing comments wherever needed etc.

For logical separation part, you need to be well aware about OOPs principals to be able to write good abstractions for classes needed for given problem, implement proper data hiding and single responsibility principle and write extensible code.
Good knowledge about SOLID principles really comes handy for this round. 

Kind of problem which you will get in this round highly depends on your interviewer and team for which you are being interviewed for. If you are a experienced engineer, you do not even need to prepare for this round, you already know this stuff (hopefully!)

Interviewer was convinced with my approach to the problem and code and moved on to **[behavioural questions](#behavioural-questions)** section of interview. 


## Round 3 - Mobile System Design round

This round is specifically for mobile engineer roles (Android or iOS or Hybrid). If you are interviewing for generic SDE roles, this would probably be backend system design round for you.

Question asked to me in this round was specific to the role for which I was being interviewed for (I realized it after I joined Amazon). Generally kind of question which would be asked in this interview highly depends on team/role for which you are being interviewed for. 
There are only handful of resources available for mobile system design preparation online, listing some of them below.

* [Mobile System Design](https://github.com/weeeBox/mobile-system-design)
* [Mobile System Design Mocks](https://www.youtube.com/@alementuev)


If you are interviewing for backend role, here are couple of resources which I found great
* [System Design Resources](https://github.com/ashishps1/awesome-system-design-resources)
* [System Design Interview – An insider's guide](https://www.amazon.com/System-Design-Interview-insiders-Second/dp/B08CMF2CQF)


Interviewer was convinced with mobile system design capabilities and moved on to **[behavioural questions](#behavioural-questions)** section of interview. 


## Round 4 - Bar Raiser 

The Amazon Bar Raiser interview is a 30-60 minutes virtual interview with an Amazon employee not part of the hiring team. 
The purpose of the Bar Raiser is to provide oversight to the entire hiring process by allowing 4-5 interviewers to compare notes to ensure that incoming employees are better than the top 50% of current employees.
You will probably never know which one of your round was conducted by bar raiser (unless you get offer and you decide to join Amazon). 

In this round, I was given a backend system design problem. Even though backend is not my expertise, I clarified interviewer that whatever knowledge I have in backend system design is purely theoretical. 
Interviewer insisted that I provide solution to the best of my capability. Fortunately I had decent exposure in backend technolgoies being worked on NodeJS and Java based backends and I had read through couple of backend system design books as well. 

I was able to draw and model data flow for given problem and also answered and made changes to my design after few follow up questions and constraint changes. Interviewer was convinced of my system design skills and moved on to **[behavioural questions](#behavioural-questions)** section of interview. 

Clearing bar raiser round improves your chances of getting strong hire rating and good offer! 

## Behavioural Questions 

Last 30 minutes of each of above 4 rounds had common section reserved for behavioural situation based questions. 
In this round, interviewer is interested in knowing how well you have applied **[leadership principles](https://www.amazon.jobs/content/en-gb/our-workplace/leadership-principles)** of Amazon in your work experience. 

If you intend to clear amazon interviews, you must prepare very well for these questions and ensure that your answer captures some of the amazon's leadership principles. You should use **[STAR](https://www.amazon.jobs/en-gb/software-development-interview-prep?INTCMPID=OAAJAZ100026B#/lessons/voFh_g5SwbFdRbRu8VSWWtvh-f1g54PJ)** method to answer these questions.

I was asked wide range of questions by each interviewer like `Tell me about a time when you had ...`, `Tell me about your most proud moment`, `How did you handle situation like...` etc

I found very good **[resource](https://managementconsulted.com/amazon-leadership-principles/)** which you can use to prepare for these questions. 


## Extras
Amazon has documented their interview process very well **[here](https://www.amazon.jobs/en-gb/software-development-interview-prep?INTCMPID=OAAJAZ100026B#/lessons/fxggI6Y3AxoOjvF9oKV_gky-TSFACjCu)** and you should read through the guide to understand the process.


## Offer and Beyond

After all of the rounds are conducted, interview panel meets for 30 minutes where feedback from each round is discussed and decision of hire vs no-hire is made. 
HR reached out to me after this feedback call letting me know that I received hire decision with good feedback and made an offer. 
I accepted the offer and joined Amazon within 2 weeks.


## Mock Interviews 

Its always a great idea to appear for mock interviews before going for real interviews as it increases your confidence (specially for DSA and coding rounds).
I did not give any mock interviews but I had appeared for bunch of interviews before amazon and they kind of acted like mock interviews for me. 

If you are planning to interview for FAANG, I highly recommend giving mock interviews. There are some platforms where you can sign up and schedule mocks (mostly paid) or you can tap into your network (professional or personal) to convince someone to take your mock. 

I am offering to take mock interviews whenever I find some time over the weekend, you can book session for the same **[here](https://topmate.io/amit_bhandari_c/)** (it's completely free!)


Feel free to reach me out on email or linkedin if you need further assistance. 

