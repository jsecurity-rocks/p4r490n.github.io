---
layout: post
title: "My OSCP Experience"
subtitle: "And why it pays off to be determined"
date: 2021-11-03 13:00:00 -0400
background: '/img/bg-about.jpg'
---
# Why I decided to go for OSCP with 0 IT experience
Since I always had a "thing" for computers and technology and understanding how things work underneath of it all, after researching for quite some time the path I can take to achieve my goal of becoming a penetration tester I discovered the OSCP certificate. Even though marked as a "scary" certificate to take, it did not deter me from jumping into it.  
The only "minor" issue in this story is that I did not have *any* programming, networking, system administration, Linux or Windows security practices and pentesting experience.  
To bridge the gap between what I know and what I don't know - I knew I would have to have a solid plan, determination, and be ready to put in the time.   

The goal of this article is to help the aspiring OSCPs to analyze what I've done and gather useful information which later they can use.

After all - one of the easiest ways to help others is to simply share your knowledge. 

# Table of Contents
- [Preparation before the first exam attempt](#preparation-before-the-first-exam-attempt)
  - [1st Phase - IT foudnation & networking](#1st-phase---it-foudnation--networking)
  - [2nd Phase - Overthewire & Tryhackme](#2nd-phase---overthewire--tryhackme)
  - [3rd Phase - HackTheBox](#3rd-phase---hackthebox)
  - [4th Phase - The OSCP Labs](#4rd-phase---the-oscp-labs)
- [Attempt No. 1](#attempt-no-1)
- [Attempt No. 2](#attempt-no-2)
- [Attempt No. 3](#attempt-no-3)
- [What changed in between 3rd and 4th attempt](#what-changed-in-between-3rd-and-4th-attempt)
- [Attempt No. 4](#attempt-no-4)
- [Exam report](#exam-report)
- [What I reccommend to OSCP students](#what-i-reccommend-to-oscp-students)
- [Plans for the future](#plans-for-the-future)
- [Special Shoutouts](#special-shoutouts)
- [Resources that helped me](#resources-that-helped-me)

# Preparation before the first exam attempt
Since the certification requires some background of all of the areas that I had 0 experience and knowledge about - I decided to take it step by step and split the preparation into phases - firstly by building the foundation and then working my way up to the more complex areas.  

## 1st Phase - IT foudnation & networking
I decided to cover the following Comptia's courses with the help of [**Professor Messer**](https://www.youtube.com/c/professormesser):
- A+
- Network +
- Security+ (only certain topics)

The information I learned from Professor Messer's videos helped me greatly. I was amazed at how well and concise his explanations were, and most interestingly, all of this information is *feely* available on the internet. Note that I did not schedule any exams - I only covered the information that is under Compia's syllabus and I felt that I was ready for the next phase.  
## 2nd Phase - Overthewire & Tryhackme
After getting networking and foundational IT knowledge it was time to step it up. My first online lab environment was [**Overthewire**](https://overthewire.org/wargames/). With the help of walkthroughs I completed some *wargames* but I learned I would need a more *hold-my-hand* approach - hence I joined [**TryHackMe**](https://tryhackme.com/).  

This is a platform I highly recommend to the people aspiring to get into pentesting since it is very beginner-friendly. There are many paths you can choose to complete to get a better understanding of the concepts you know nothing or very little about.  

I was on this platform for a month, during which I completed around 20 boxes (mostly with the help of walkthroughs and guides). After the month passed I decided it was time for the next phase.  

## 3rd Phase - HackTheBox
There is a certain character from a famous MMORPG with a famous quote - "You are not prepared!".  
Oh boy, I was not indeed.  
As soon as I joined [**HackTheBox**](https://www.hackthebox.com/), I was struggling to complete *"easy"* rated boxes. I suppose this is not a surprise, since two months before that I did not know what a Virtual Machine was, right?
However, there is one pentester that helped me more than I can ever describe - [**IppSec**](https://www.youtube.com/c/ippsec).  
I watched many of his YouTube videos, learning first hand how his thought process works when enumerating and exploiting machines, and most importantly which tools and techniques he uses. I couldn't recommend his videos enough.  
I completed around 30 retired boxes on HackTheBox before I decided that I can jump into the OSCP labs.  
## 4th Phase - The OSCP Labs
Having jumped into OSCP labs after around 2 to 3 months of prior preparation, I was a bit stumped when I saw the lab environment. It took me some time to get comfortable, though, after 60 days which I had in it, I managed to root 40 boxes.  

It was overall a very productive experience, where I built my methodology and enumeration techniques. On a few boxes, I used forum hints since I did not want to lose my time in the lab. During the lab, I actively used the InfoSecPrep server and met many other students who were willing to help with many topics, and I also helped others as much as I could.  

I watched [**Tib3rius**](https://www.udemy.com/course/windows-privilege-escalation/)'s  and [**TheCyberMentor**](https://www.thecybermentor.com/)'s privilege escalation courses and made extensive notes on them.  

I had a hard time following the Buffer Overflow section from the PDF, so I took a look at *TheCyberMentor*'s Youtube Channel and it helped me tremendously. I also practised *BOF* in the TryHackMe's Buffer Overflow prep room that *Tib3rius* made. I can say that this was enough to prepare me for the exam's *BOF* machine.  

It is worth noting that I choose not to do the exercises for 5 points since I believe my time would be better spent in the lab (I had only 60 days).  
# Attempt No. 1
A week after my lab time ended I had a scheduled exam.  

As you are aware I am not able to disclose any particular details about the exam, but what I can say is that I was awake for all 24 hours of it, and managed to down the Buffer Overflow and 10 point machine, and I was able to get the initial foothold on two 20-point machines, however, I was unable to root them.  

Now when I come back to this exam attempt and rethink how could I do things differently I am fairly confident that I would not have major trouble with it since I now recognize the priv-esc vectors, however at that point in time, I must admit that my understanding of the certain aspects of privilege escalation revealed to be lacking. This resulted in my first failed attempt.  

Having studied intensively for the last 6 months and failing the exam, I felt overburned, but I never had a thought in my mind that I will not try again, since I always believed that I can do it.  
# Attempt No. 2
I took a break from pentesting for some time and had the next exam attempt in February of 2021.  
I must say that this attempt was a pure nightmare. Even though I was confident in my web enumeration and getting an initial foothold on boxes, this exam proved me very wrong.  

Even though I did not enter the exam with *" I'll pwn it all"* mindset, I was still thinking that privilege escalation is something that I will likely have more trouble with, and most definitely not the initial foothold phase, since in the lab I was able to get the footholds more easily that to escalate my privileges.  

Everything I did on this attempt didn't seem to work, and I felt defeated and disappointed that I have previously wrongly identified my weaknesses during the preparation. I was not questioning If I'm able to pass the exam eventually, but rather If I'm able to successfully evaluate and identify what I need to work on to improve my methodology.  

On this attempt, I only got the Buffer Overflow and 10 points machine. 
# Attempt No. 3
I took this attempt in May of 2021 after the cool off period that Offsec gives after the 2nd failed attempt.  
As always I started with Buffer Overflow and completed it with ease. I started working on other boxes and focused on 25 point machine. After getting a foothold there I was stuck for quite some time on the privilege escalation. In the meantime, I rooted the other 20 point machine and decided that I will focus the rest of my time back on 25 point one - since I couldn't crack the other 20 and 10 point machines no matter what I tried.  

After nearing the end of my exam somehow got a root shell on 25 point machine, however, I was not able to replicate the attack vector. Now I know what you are thinking. How is this even possible? You rooted a box, so you should be able to replicate the attack and that is it!  

While that might be true, what I understood after this attempt is that I was simply lacking the knowledge about certain areas of Windows privilege escalation, plus being awake for more that 24 hours did not help.  

Even though I created the report for this exam attempt in the attempt to try to document what I thought was the correct attack vector, I had major trouble while using LatEx to generate the report, and experienced what we call "technical difficulties" while compiling it from markdown.  Overall, while some may call all of this unlucky, looking back at it I can simply add that being unprepared also had something to do with it.  

# What changed in between the 3rd and 4th attempt
I got my first job involving network security as a help desk. This pushed me even harder to complete my OSCP journey.  

It is safe to say that something *clicked* after my third exam attempt.  
I calmly reevaluated all of my previous attempts and I finally understood where my weaknesses lie and how I can eliminate them. 
I noted them all down and went through them one by one - from web enumeration to parts of privilege escalation.  As as I did in between all exam attempts - I reactivated my Proving Grounds Subscription, however, this time I also used Cyberseclabs and TryHackMe.  

[**CyberSecLabs**](https://www.cyberseclabs.co.uk/) proved to have mostly easy boxes, however, they contain the most common misconfiguration and helped me quite a bit with Windows privilege escalation. I highly recommend checking the platform out.  
I also started watching *Alh4zr3d* on Twitch and found his content to be very helpful with improving enumeration methodologies.  

# Attempt No. 4
I approached the 4th attempt with a much calmer and cooler head. I downed the Buffer Overflow and moved to the 25 point machine. To my surprise It was the same machine from my third attempt, however, something that I cannot disclose was changed by Offsec. I still managed to get an initial foothold, and again I was stuck on privilege escalation like last time.  

Now I decided to go for the first 20 point machine and saw that it was the one from the last time. I only replicated the previous exploit that worked and I was root in no more than 30 minutes.  
Checking the last 20 point box (surprise, surprise), I saw that it was the same as the one last time I couldn't even get an initial foothold on. However, this time, I can simply say that due to my improved methodology and enumeration skills, I managed to find that foothold in 15 minutes and get the root on the machine in another 15.  
Since now with the user on 25 point, Buffer Overflow, and two 20 points boxes I had the passing grade.  

I decided to spend the rest of my time on privilege escalation on 25 points box since it simply intrigued me. After one hour I was able to get SYSTEM on the box with a full understanding of how and why the escalation works.  

Now with 90 points in my pocket, I only took more screenshots and after roughly 14 hours I had all that I needed to make the report.
# Exam report
My exam ended at 11 AM, and I started to write the report from 1 PM, and I finally finished at 1 AM. I took it a bit slower to make sure everything is in order. Every proof screenshot I triple checked and went through the report a couple of times from a neutral point of view to get a better understanding if I needed to change or add something.  
In the end, the report was 80 pages, and I was sure everything inside of it can be 100% replicated.  The template I used was a modified Offsec one from *whoisflynn*'s GitHub repo.  

Just around 24 hours later, I got the email that I passed.  



# What I recommend to OSCP students
These are my recommendations as somebody who experienced 3 unsuccessful attempts.  

1. Make sure to know how to use your time effectively and efficiently. Plan things out in advance, double or triple check your plan, and then execute it. Simple as that.
2. DO NOT hesitate to ask for help sometimes. I can't stress this enough. In some cases, I could have saved more time simply by asking for help on discord/forums, but I decided to be overly stubborn, and in the end, ended up wasting my time and getting frustrated.
3. Besides thorough note-taking, after every practice box, write down the things that you've learned. In my mind, putting this "on paper" solidified what I've previously done on a box.
4. In the case you fail the exam and compromise any machine, take extensive notes and copy/paste your commands - since you might get the same one next time and this can save you some time.  
5. Get some sleep during the exam and take regular breaks.  
6. The last and the most important thing- DO NOT BE AFRAID OF FAILURE. I view life as a constant learning process with its ups and downs, and not passing the exam multiple times was a part of that process. What I learned by experiencing this taught me just as much if not even more about the importance of consistency and determination. If you don't pass the exam, use this to your advantage, and not as something to keep you down. Analyze what you've done, what you could have done differently, and next time things are more likely to go your way.  
   
# Plans for the future
After passing the OSCP, I am now even more determined to go for OSWE. One step at a time.
# Special Shoutouts
I would like to give special shoutouts to the following people since without their content this journey would not be possible.  
- [IppSec](https://www.youtube.com/c/ippsec) - his youtube videos, consistency, methodology and overall knowledge are an inspiration
- [TheCyberMentor](https://www.youtube.com/c/TheCyberMentor) - for explaining the Buffer Overflows in a very understandable way and for great Privilege Escalation courses
- [Tib3rius](https://www.twitch.tv/0xtib3rius) - for making the [**Autorecon**](https://github.com/Tib3rius/AutoRecon) tool that I used in exam and for making awesome Privilege Escalation courses
- [Alh4zr3d](https://www.twitch.tv/alh4zr3d) - for streaming quality content where I learned much about enumeration and Active Directory attacks  


# Resources that helped me
- Learning Platforms
    - [TryHackMe](https://tryhackme.com/)
    - [CyberSecLabs](https://www.cyberseclabs.co.uk/)
    - [HackTheBox](https://www.hackthebox.com/)  
- Youtube Channels
    - [Professor Messer](https://www.youtube.com/c/professormesser)
    - [IppSec](https://www.youtube.com/c/ippsec)
    - [TheCyberMentor](https://www.youtube.com/c/TheCyberMentor)
- Twitch
    - [Alh4zr3d](https://www.twitch.tv/alh4zr3d)
    - [Tib3rius](https://www.twitch.tv/0xtib3rius)
- Other resources
    - [Hacktricks](https://book.hacktricks.xyz/) - the cheatsheet grimoire I used
    - [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) - incredibly helpful payload cheatsheet
    - [RevShells](https://www.revshells.com/) - reverse shell generator
    - [CyberChef](https://gchq.github.io/CyberChef/) - encoding/encryption swiss knife
    - [Crackstation](https://crackstation.net/) - online pre-computed password lookup tables (use only CTF hashes please)
    - [GTFO Bins](https://gtfobins.github.io/) - Linux binaries and their capabilities
    - [LOLBAS](https://lolbas-project.github.io/) - Windows binaries and their capabilities
    - [PortSwigger's WebSecurity Academy](https://portswigger.net/web-security) - SQL injeciton practice
    - [NetSecTrophyRoom](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview#) - List of OSCP-like machines