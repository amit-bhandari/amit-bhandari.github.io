+++
title = 'Google Interview Experience - SDE-3 (L4)'
date = 2024-10-08T17:25:36+05:30
draft = true
description = "I recently joined Google and would like to share my interview experience for anyone who is intending to prepare for an android engineering role at Google."
+++

## Backstory
I never imagined that I would be writing this blog post in 2024, especially after joining Amazon earlier in the year (read more about it {{< newtabref href=../amazon-interview-experience/ title="here" >}}). However, sometimes we can't control where life takes us.

I spent 5 months working at Amazon this year, and my experience was both remarkable and memorable. Contrary to the widespread perceptions of Amazon's work environment, my experience was quite different. I had the opportunity to work on an internal product called Amazon Enterprise Access (AEA), which allows Amazonians to securely access corporate resources from their personal Android and iOS devices. AEA also functions as an authentication mechanism for some internal and third-party applications that require Amazon SSO in an Android work profile. The work culture and team were exceptional—professional, supportive, and filled with talented individuals.

During those 5 months, I gained substantial exposure to how authentication works in enterprise environments and learned firsthand how security is a top priority in ensuring the protection of both organizational and user data. Interestingly, I wasn't actively seeking a change while at Amazon; in fact, the first round of Google interviews began even before my Amazon interviews. The Google interview process is known to be lengthy and can sometimes take up to a year.

### Why did I leave Amazon?
Honestly, it was a very difficult decision to leave Amazon after just 5 months, but there were a couple of reasons that led me to switch to Google. The most significant was an offer from the YouTube team to work on their new video creation product. Given my extensive experience in the video domain and my personal interest in video technologies, it was an easy decision. Additionally, the opportunity to work on a user-facing product, rather than an internal one, was something I had been looking forward to. With a few other personal reasons factored in, I made the tough choice to leave Amazon earlier than expected.

## Preparation and DSA saga
Twitter and LinkedIn are currently flooded with DSA (Data Structures and Algorithms) gurus and courses, leading many to believe that constantly grinding DSA is the key to getting into FAANG. This holds true mostly for new graduates. Even then, landing an interview can be extremely challenging if you're not from a tier 1 college. For experienced professionals, however, it's about much more than just DSA—you need strong system design knowledge relevant to your domain, along with a good amount of luck (if you get a particularly tough DSA question in an interview, luck might not be on your side).

In my 8 years as a software engineer, I’ve never paid much attention to solving DSA problems on platforms like leetcode, as I was able to secure interviews and offers based solely on the products I built or worked on. At the start of 2024, I decided to begin practicing DSA from scratch. While I had all the theoretical knowledge needed to solve DSA problems, that's not enough for interviews where you need to solve problems under time pressure—something that requires a lot of practice. You can see my leetcode {{< newtabref href=https://leetcode.com/u/amitbhandaric/ title="profile" >}} where I’ve solved around 450 problems in 3 months.

### Planning
If you're aiming to get into FAANG, you need to take your preparation seriously and plan it with clear objectives. Simply following every DSA course or reading the success stories of random wannabe FAANG influencers won't help. It's important to keep in mind that preparing and applying to these companies requires a long-term commitment, as their interview processes are often unpredictable and can take months, if not years.

Many FAANG recruiters are constantly scouting for talent on LinkedIn, and you'll often receive messages for open positions, especially if you have relevant experience in a particular domain and a well-rounded profile. If that's not the case, referrals are the next best way to secure interviews. Direct applications through career websites, unfortunately, have a very slim chance of leading to interview calls.

### Data Structure Fundamentals 
If you truly want to understand the theory behind data structures and algorithms, the following two books are the best resources I’ve found. They are fairly complex and can take a long time to finish, but a solid foundation will benefit you throughout your software engineering career.

Regardless of whether you're preparing for interviews or not, one of these books should be on your reading list if you want to build a strong foundation in computer science fundamentals.

1. {{< newtabref href=https://link.springer.com/book/10.1007/978-3-030-54256-6 title="The Algorithm Design Manual by Steven S. Skiena" >}}
2. {{< newtabref href=https://mitpress.mit.edu/9780262046305/introduction-to-algorithms/ title="Introduction to Algorithms by Cormen" >}}

### Coding Questions 
There are literally hundreds of online resources for preparing for DSA coding questions (hence the infamous phrase "DSA grinding"). While which resource works best for you is subjective, only a handful of these resources are truly worth your time.

The most important thing to focus on is ensuring you cover all the key topics that are commonly asked in interviews and solve enough problems to build confidence in those areas. In my early days, I made a few mistakes—one was spending too much time on trick questions (bitwise operators, mathematical tricks like LCA, or any question where the solution relies on an uncommon approach). Another mistake was attempting too many HARD LeetCode problems, which only wasted time and caused unnecessary frustration.

Some people claim you need to be able to solve a hard LeetCode problem in 45 minutes, but in my opinion and experience, it’s very difficult to do so unless you already know the trick behind that particular problem. If you're aiming to prepare for hard problems, I recommend first becoming proficient at solving two easy or medium questions within a 40-minute timeframe. If you're struggling with two medium problems in that time, tackling hard problems might not be the right focus for you at this moment.

### Resources
I don’t quite remember how I stumbled upon the {{< newtabref href=https://www.youtube.com/@CodeHelp title="CodeHelp" >}} channel on YouTube and their {{< newtabref href=https://www.youtube.com/watch?v=WQoB2z67hvY&list=PLDzeHZWIZsTryvtXdMr6rPh4IDexB5NIA title="Complete C++ Placement DSA Course" >}} playlist, but the entire course is completely free and of outstanding quality. I particularly liked the content because Love Babbar covers almost all the important DSA topics with a great style, and his Hindi narration made it even easier for me to follow along and absorb the material.

For those who prefer learning in English, another excellent resource is {{< newtabref href=https://neetcode.io/ title="Neetcode" >}}. His YouTube videos are incredibly clear and helpful, and he has also done a great job organizing a preparation roadmap on his website.

The only money I spent during my DSA preparation was on LeetCode Premium, which I subscribed to specifically to access question sets for Google, Meta, and Amazon. If you don’t need to see the question banks for specific companies, the free version of LeetCode should be more than enough.

**Disclaimer: I am not affiliated with any of the resources mentioned above. I’m sharing them purely based on my personal experience and opinion. You should explore these resources to see if they suit your needs, and if not, feel free to find others that work better for you.**

## Interview Process
There are plenty of Medium articles that detail the Google hiring process, which remains consistent across similar roles. Due to the NDA between Google and me, I can’t disclose specific interview questions. However, most of the coding questions I encountered were custom-made by the interviewers and weren’t available on LeetCode or similar platforms. All the questions were of medium difficulty.

### Screening
The screening round consisted of a 45-minute coding session where I was asked one medium-level LeetCode question. I solved it comfortably and received a callback for the next stage, which involved two more coding rounds.

### DSA (2 Interviews)
Similar to the screening round, I had two additional coding rounds. The problems were slightly more challenging, and in one of the interviews, I had to solve two questions. Both rounds included discussions on time and space complexity.

### Android System Design
After clearing the three DSA rounds, I was scheduled for an Android system design round. However, due to some internal miscommunication, the interviewer wasn't aware of the system design focus and ended up conducting a fourth DSA round. The question was medium-high in difficulty, and I struggled a bit to find the solution but eventually managed with some helpful hints from the interviewer.

Because of the mix-up, I was scheduled for another Android system design round. This time, I was asked a typical Android system design question. You can refer to my previous {{< newtabref href=../amazon-interview-experience/ title="post" >}} for resources to prepare for Android system design interviews.

### Behavioral
After clearing the technical rounds, I had a behavioral round (also known as the `Googleyness` round). Unfortunately, I didn’t perform well in this round. The interviewer asked typical behavioral questions, but for some reason, they weren’t satisfied with my responses, and the interview ended before the scheduled 45 minutes. As expected, I received a `no-hire` feedback for this round.

After that, I assumed the process was over and moved on with my routine. There was complete radio silence for nearly a month.

### Team Matching 
Even though I received a `no-hire` in the Googleyness round, my recruiter didn’t give up. For the next month, they continued to present my profile to multiple teams, hoping to secure a strong statement of support from one of the hiring managers. Fortunately, my resume landed on the desk of the YouTube Create team's hiring manager, who was working on a product similar to what I built at Rizzle from 2019-2023. The manager requested a team matching call through my recruiter.

The team matching round is essentially a formal discussion where hiring managers speak with candidates who have completed the technical interview rounds. They pitch their product, explain the role, and provide details about the team. My conversation with the hiring manager went well, and they provided a strong statement of support for me.

### Hiring Committee 
After my recruiter submitted the feedback from all my technical and behavioral rounds, along with the hiring manager’s statement of support, the hiring committee reviewed my case. Thankfully, they responded positively, and in the following weeks, my offer was rolled out along with the necessary formalities.

## Offer and Beyond
I joined Google as a Noogler (New Googler) just last week and am on my way to becoming a full-fledged Googler soon! If you’re looking for a referral for one of the openings at Google, feel free to reach out and share your resume via {{< newtabref href=https://www.linkedin.com/in/amit-bhandari-c/ title="LinkedIn" >}}. I’ll be happy to help with a referral—provided I like your resume, and probably after a mock interview.





