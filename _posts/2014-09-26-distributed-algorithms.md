---
layout: post
title: "Distributed algorithms"
description: ""
category: 
tags: ["computer-science", "algorithms"]
published: false
---


I want to discuss some distributed algorithms here. We're told that we need to be familiar with algorithms and data structures, so we go to websites like LeetCode to get us exercised. Yet, the questions in these websites are mostly restricted in the scope of the traditional algorithms, like "maximum palindrome substring" or "longest consecutive sequence". They don't cover a lot about distributed algorithms, which are a special family of algorithms that focus on solving problems under distributed environments(if you find one websites cover this type of algorithms very well, please let me know) and are much more interesting.

I've just started to be interested in this so I'm not going to be general and give some kind of summary like a literature survey. I'll just discuss some examples.


1.  Find the i th largest number in a sorted array. 

	This is very easy if we can have the whole array in our memory. However, suppose we divide it and store the partial lists in k different machines separately, and we're distributing values randomly into these k lists, while keep each partial list sorted. How can we get the value of the ith number, which is the ith largest number in the whole collection?

	First, we can consider a merge sort approach. We tell each machine to return its first i values, and we merge them together, then we find the i th value. This method is simple, and it works when i is small. However, if i is large, even larger than the length of each partial list, or we have way too many distributed machines, this method is clearly not the optimal solution.

	Second, we might consider to do something similary binary search. Suppose we only have two partial list, we can divide each list into two parts from the middle. So we have list_a_part_a, list_a_part_b, list_b_part_a, list_b_part_b. We can throw away one of these four with some comparison. For instance, if i > (len(list_a) + len(list_b)) / 2, and list_a_part_a[-1] -- which means the last element in python, > list_b_part_a[-1], then we can throw away list_b_part_a, and transfer the problem to find the (i - len(list_b_part_a)) number in the rest collection. However, the method doesn't scale up neither.

	So we might need to think in very different directions. Finding the value in the i th index is not easy, how about finding the index of a given value k? To do that, we can simple find the number of values less than k, and sum them up. It's not difficult, and it can be done in O(log(n)) time. 

	So, this leads to an interesting direction. When we do normal binary search, the lower bound and upper bound are for the index, not the value, and we keep updating the lower bound and upper bound until we find the target value. What if we set the upper bound and lower bound for the value, and use binary search's divide strategy to update the lower bound and upper bound, until we get the target index?

	In detail, we set lower_bound=min([p[0] for p in partial_lists]), upper_bound=max([p[-1] for p in partial_lists]). Each time we take the middle=(lower_bound + upper_bound) / 2, and find out the index of middle. If the index is smaller than i, we set lower_bound=middle, and we set upper_bound=middle if index is larger than i. We keep doing it until the index is equal to i. It's also possible the value we search its index for in each iteration might not be in the lists, but it's easy to deal with so I just skip this part. 

	So it's a very interesting question. I got stuck by it in an interview and nailed it by many, many hints of the interviewer. But, it's absolutely much more interesting than 90% problems on LeetCode.


### Well I cannot find many examples right now but I will add up as soon as I find interesting distributed algorithms.

