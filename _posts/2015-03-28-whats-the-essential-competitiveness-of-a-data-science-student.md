---
layout: post
title: "What's the Competitiveness of a Data Science Student?"
description: ""
category: 
tags: []
---

My major during undergraduate was software engineering, and now I'm pursuing a data science master([MCDS](http://mcds.cs.cmu.edu/)) in Carnegie Mellon University. For the question raised up by the title of this article, I have been thinking for some time, because when I'm planning my future career, I want to know what jobs I might be doing and whether I'll be good at it (or competitive at it), and whether I'll enjoy it. Although I don't think I have a satisfying answer, I'd like to say something about this topic. 

### What is Data Science About
Some people, including the one-year-ago me, think Data Science is just about Machine Learning and Statistics. It's abouting making abstraction from reality and turn it into a mathametically well-defined problem, then design sophisticated models to solve it. 

This is important, of course, but this is not the whole picture. Designing the model is just part of the work. There're questions remain:

1.  First, how do we obtain high-quality, reliable data? If this can't be guaranteed, how to adjust the model to make it robust to the noise of data? 
2.  Second, how do we store the data, in the case of a really large volume?
3.  Third, how do we process the data (especially when the data is huge) with our model and get the result we want, within a reasonable time? If our computational resource is not sufficient, how do we adjust our model to make a compromise? This is perhaps the hottest and most challenging topic today. 

The point here is, we need to consider very realistic implementation issues, instead of just worrying about math. 


### What's the Skill Set

Given the scope of Data Science defined above, there're multiple categories of skill set:

1.  **Machine Learning and Statistics**

	This is about machine learning and statistics basic knowledge. The machine learning algorithms and theories, they're essentially tools used to solve a defined practical mathematical problems. So, such mathematical and theoretical background is essential

2.  **Domain Knowledge**

	From my perspective there're two levels of domain knowledge:

	a.  *Technical*. Examples are knowledge about NLP, or Computer Vision, or Speech Recognition, etc. These are domains machine learning knowledge is being applied on. Being familiar with such domain knowledge and algorithm is critical for transfering a specific task (say, sentiment analysis in social media mining) into a math problem, and use ML/Statistical techniques to solve them.

	b.  *Business*. The second level is about bridging business domain and technical domain - how to identify, define, formulate a business problem into a technical problem. 

	Let me make one example (maybe a little immature): say an online retail company just release a brand-new wearable device, and want to identify potential buyers within its previous customers and send them an email advertisement. This is the business problem. 

	Now, to transfer this into a technically solvable problem, or problems, one need to consider: how to evaluate whether a person will buy the product or not? Maybe we can see what the person bought from the company before, and based on that history we determine whether he'll also be interested in this new device. In this case we might need to define some sort of similarity function between products. Or suppose the user connected to the company's website with his FB account, and we can have access to his FB data, then we can do some inference with NLP technique to model this person's interest from his timeline on FB, etc.

	Another problem to consider is, suppose we've defined a method, or a list of potentially feasible methods, how do we evaluate them? Maybe we can run some A/B tests, and compare the results by seeing how many percent of receivers of this ad actually make a purchase for that device? If so, how do we guarantee the fairness? Should we run different models on different partition of users, or at different time? 

	By this example, what I want to say here is I believe domain-specific knowledge is really essential for a person to be a good data scientist.

3.  **Knowledge about Computation**

	Again, there're two levels here:

	a.  The first level is how to use existing computation tools. For instance, how to write Map-Reduce programs, how to use Spark to run Machine Learning algorithms, etc. As a person who is still studying at school, I don't really have too much experience about using these tools. From the experience I had, using these tools to do some immediate-level jobs should not be too difficult, but it may be really difficult to understand how they work and make the best use of them (like configuring them to be most efficient).

	b.  The second level is how to actually implement a computation tool or framework. Those people developing Hadoop/Spark are doing this level of work and I admire them. Maybe for many companies today using open-source tools is efficient for their need, but there're also many companies need their own implementation of frameworks, especially those big names. What's more, there're more and more companies building and selling products as auxiliary service around those open-source tools, such as Cloudera.

### So, how to be an outstanding Data Scientist Candidates?

This is the key question, which I haven't completely figured out. 

Take me as an example: I've been studying both math courses and system development courses at school. Most of my course projects are machine learnign related, but on the other hand I'll be developing database system at Google for the summer internship. 

The one thing I've been thinking is, should I be a generalist of Data Science, which mean I need to be skillful at both Machine Learning algorithms and large-scale system development? Or should I be a specialist, like to be an expert on Machine Learning, in the mean time have the essential knowledge of computation, but not really expertise at it; or in the opposite way, expertise on large scale data science system development, also be capable of understanding machine learning and write machine learning codes, but no need to have really in-depth understanding of the theories behind? 

Time is limited for any person, and sometimes it's infeasible to do everything well - surely I understand that, but right now that since I feel I'm still capable of learning knowledge from different subfields in data science and balancing them, why don't I try to be a generalist first? That's what I'm trying to do right now. But in the mean time I'll keep thinking, maybe I'll have an answer in the future.

### Conclusion

There's really no conclusion for this article - I propose a question, discuss the dependent questions of it, and give my own answers to some, but not all of them. So basically I'm just writing down my thinkings. Hope they help someones. 
