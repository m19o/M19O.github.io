---
published: true
title: Certified Red Team Expert (CRTE ) Review
date: 2023-3-18 06:25:00 +0200
categories: [Reviews]
tags: Reviews
---

<img src="https://i.ibb.co/LdJ2YYk/CRTE.png" alt="CRTE" border="0">

# Table of Content
1. Introduction
2. How to prepare for CRTE
    1. Useful blogs
3. Lab Review
4. Exam
    1. Should you go for it or not
  
# Introduction
The purpose of this blog to outline my experience as Security consultant/Red team operator in Windows Red Team lab course by <a href="https://twitter.com/nikhil_mitt">Nikhil Mittal</a> and provide my own insight into the course content, how to get the most advantage of the content, what is required to achieve CRTE, can you use this techniques in Red Team activities. 

# How to prepare for CRTE
As we all know Active Directory is a core service in all Enterprises, the key of hacking anything is understanding how it works, so first of all you need to know what is Active directory and why we use it after that discovering attacks will be easy for you, don’t start with CRTE content you can go for CRTP first. 

I will list some blogs I have written and you can use zer1t0 blog as a reference because it contains a lot of information and I will be releasing a blog for attacking ADCS very soon. 

## Useful blogs
- [https://zer1t0.gitlab.io/posts/attacking_ad/](https://zer1t0.gitlab.io/posts/attacking_ad/)
- [https://m19o.github.io/posts/1.1-Enum-the-AD/](https://m19o.github.io/posts/1.1-Enum-the-AD/)
- [https://m19o.github.io/posts/1.2-LDAP-the-AD/](https://m19o.github.io/posts/1.2-LDAP-the-AD/)

# Lab overview
<img src="https://i.ibb.co/qF0vjMy/Lab-Diagram.png" alt="Lab-Diagram" border="0">
I took the Advanced Bootcamp for attacking and defending Active directory, It was very big lab as you can see 8 forests and 20 domains, It will give you hands-on experience in a lot of technologies like attacking MSSQL and how to use it to jump to another forest because MSSQL break trusts, attacking Azure-AD and how on-premises Active directory connects to cloud, Abusing different services like gMSA, Laps, they added resource-based delegation too in the lab, you will learn how to use different tools for same attack, like using Rubeus, Mimikatz and Kekeo and how to connect remotely to machines for lateral movements like PSremote and Winrs.

It’s very big play ground, you can use C2 and simulate a real adversary attack by doing OPSEC safe attacks, here you can learn from your mistakes but in real life there is no space for failure. 

# Exam
I completed the lab in 30 days because I was busy with work too as we all ^_^

in the exam you will have 5 machines and you will need to escalate your privileges in all machines to achieve DA at the end. If you solved the lab with intent of learning and gaining new skills not just finishing it. the exam will be a piece of cake for you. 

> Pro-tip : every time you stuck enumerate more !

# Should you go for it or not
In my opinion if your goal to learn more about active directory security assessments you will learn a lot if you got the Bootcamp, If you are seeking to gain red teaming experience unfortunate you will do extra effort by learning how to hide your activity inside the network will doing this attacks and you should see how windows logging works in detecting this attacks, to beat the blue team you need to think like them.
