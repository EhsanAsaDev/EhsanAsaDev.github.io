---
layout: post
title: Refactoring life with tdd approach
comments: false
categories: [ lifestyle ]
tags: [About, Developer, TDD]
image: https://hpf-happify-marty-prod-user-uploads.happify.com/cms_uploads/en_US/img/happifiers/one-thing-you-may-not-know-about-change-1_5434036.jpg
---

These days I'm refactoring myself, and I hope writing here be a part of these refactoring.
As a developer we should like refactoring. The main goals of refactoring are make our code more efficient, 
more readable, and more elegant, so we should think about that every time we can, 
when we're actively coding, when we're in launching time 
even thou when we want to sleep (I think this type is the most interested one) 
also we should do it when we have a bug, when we want to implement a new feature, 
when we don't have anything to do and finally when we are in the day before release 
( again I think this type is the most excited one). how about  requirements or maybe better called pre-requirements? 
we need reliable test scenarios. With these scenarios developers can be confident and motivated about refactoring. 
The process is obvious we change something, run test, fixed problem and again change something ...
 


I'm trying to refactor myself in level of the system not the recalls because I think this is a long term target.
so if I want writing test about it maybe It is hard. the main problem is 

```java

@Test
private void testRefactoringMyself_SuccessD() throws Exception {

	assertTrue(havePlan());
	List<plan> plans= planService.getAll();
	for(Plan plan : plans){
	
		//assertTrue(plan.isAchieved());
		
		progressService.beBetterABitFromLastDay(plan);
		
	}
}

```



