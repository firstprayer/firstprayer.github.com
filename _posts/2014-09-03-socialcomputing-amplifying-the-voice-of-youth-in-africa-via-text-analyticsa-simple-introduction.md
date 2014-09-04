---
layout: post
title: "Hear People's Voice - Text Classification in SMS data"
description: "This article is a summary of the KDD2013 best paper: 'Amplifying the Voice of Youth in Africa via Text Analytics'. It describes a system used to rank text messages so that people working in the U-report project in Uganga can discover and react to social issues faster and more efficiently. "
category: 
tags: ["computer science", "social computing", "data mining"]
---

<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>


This article is kind of a summary of the KDD2013 best paper [Amplifying the Voice of Youth in Africa via Text Analytics](http://www.prem-melville.com/publications/unicef-kdd2013.pdf).    

### 1. Background  

U-report, a SMS platform operated by UNICEF(the United Nations Children's Fund) in Uganda, is a system enables community members to share important issues around them, such as child abusement, violence, disease outbreaks, etc. About 10000 text messages about such topics are received each week(the statistic at the time the paper was published, could be more now). Approximately 39% of them require an SMS response with advice or information and 7% require an action taken by the government or other organizations, sometimes as fast as possible. There're different departments handling different kinds of messages, so different kinds of message should be routed to staff in these different departments so that they can inspect these messages and react in times. This paper mainly discusses such a message-understanding and routing system developed by IBM as UNICEF staff to deal with this problem.    

### 2. Objective   

The system aims at **messages ranking** for a specific message category, because the staff in UNICEF will choose a category first(like *water* or *emergency*), then inspect the messages in the descending order of relevance.   

### 3. Technical Approaches  

Well, I'm a CS guy, so I should somehow explain how the system works. If not interested in this section, jump to the next section which might be more interesting. 

So The authors of the system and this paper tried many methods. The first one is to construct a **key-word list** for each category. The result was surprisingly not bad, at least for some categories like *health & nutrition*. However, when it comes to the other categories like *social policy*, the list is obviously insufficient to find out related messages.   

The next approach is **text classification**. Both Naive Bayes and SVM are tried in the experiment, which shows that classification methods are helpful in finding out discriminative terms. For example, the top 20 most discriminative terms for the category of *violence against children* are: **defilement, child, female, fgm, sacrifice, defiled, raped, abuse, circumcision, girl, violence, genital, rape, mutilation, practice, defile, cases, defiling, man, beaten**, and only 7 of them are in the keyword list provided by experts. On average this approach performs better.   

At this step, the authors noticed a problem: **spelling errors are common**, because English proficiency among the system users is not always high. This is a big noise in the data. So they developed a process to correct spelling errors and map abbreviations to their canonical forms(i.e. **b4** to **before**). This pre-processing of text helps improving the performance of both approaches mentioned above.  

Next, the authors thought even though text classification methods are better, the keyword lists provided by experts should not be discarded completely because they do provide useful prior knowledge. So a **dual supervision** method was used. The method they adopted is called [Pooling Multinomails](http://www.prem-melville.com/pooling-multinomials-kdd09.pdf), which can integrate prior lexical knowledge with the training data to obtain more predictive model.   

In details, this method models each document as the combination of independent words. So following Bayes Equation we have the predicted category of document d:  

<p>
\[
argmax_{c_j}P(c_{j})P(d\|c_j)=argmax_{c_j}P(c_{j})\prod_{i}P(w_i\|c_j)
\]
</p>

Now, we actually have two set of prior probability<script type="math/tex">P(w_i\|c_j)</script>. One is <script type="math/tex">P_e(w_i\|c_j)</script>, calculated by the labeled data. The other one is <script type="math/tex">P_f(w_i\|c_j)</script>, which is given by the expert knowledge, which is the keyword list mentioned in the first approach. Therefore, the final conditional probability is given as:  

<p>
\[
P(w_i\|c_j)=a_1P_e(w_i\|c_j)+a_2P_f(w_i\|c_j)
\]
</p>

To select <script type="math/tex">a_i</script>, the author indicate two methods: one is just 0.5-0.5, the other one uses the equation <script type="math/tex">a_i=log\frac{auc_i}{1 - auc_i}</script> while <script type="math/tex">auc_i</script> is [*AUC*](http://en.wikipedia.org/wiki/Area_under_the_curve_(pharmacokinetics)) of model i, which indicates how well the model performs on the data. However the authors claimed using the naive 0.5 and 0.5 weight was actually better, and they thought over-fitting might be the reason. 

The model built up by now is actually quite satisfying. However, the authors observed that the **false-positive rate** at the top results is **unacceptably high**. Their solution was to re-rank the results. So again, the keyword list became very useful. The authors took the following strategy:

- If a message matches at least one keyword, it should be ranked above messages that do not have a match (irrespective of model score).  
- Amongst all messages that have a match, retain the ordering given by the model.  
- All messages that do not have a match, appear at the bottom of the list, ranked by model score.  

Through this re-rank process, the system could now achieve a higher accuracy within top results. People inspecting these message should be happy about that they can find relevant messages much faster. 

### 4. Impacts & Comments
The impacts of this system is as the interesting as the technical methods mentioned in this technical paper. With the help of the system, the U-report staff can better understand and react to people's need, and better allocate their limited resources. At the time the paper was published, using the useful assistance from this system, the U-report project had resulted in several notable policy changes in Uganga, such as the Children Act Amendment against corporal punishment in schools. All Chief Administrative Officers (one for each of the 112 districts) have agreed to receive SMS updates on issues in their localities. The system also supported the visualization of trending topics in development issues by geographic regions.   

This project seems quite successful because it improves the produtivity and efficiency in U-report. Will U-report project result in such as a great accomplishment today if there were not the system? Not sure, and no one could prove that. I believe that the project does help the U-report a lot, but what's crucial is not the system, but those people working in U-report and react to people's messages, and the system is just helping them to do better. 

And also I'm thinking about is it possible to replicate the whole model, including the system and the U-report project, to anywhere else? The invention of this system should make it easier to do so. Developers might need to deal with localization, which could take a while, but not a really big problem. And A phone to text messages is common now. But the key point should be the governments. If a government is willing to listen to the valuable voices of people about what are the issues happening right next to them, it should try to establish such a system. It worths a shot, right? I expect to see this in my hometown within 10 years, how about you? 
 




