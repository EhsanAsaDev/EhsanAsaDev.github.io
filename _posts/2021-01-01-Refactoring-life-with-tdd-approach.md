---
layout: post
title: Refactoring Life with TDD Approach
comments: false
categories: [ lifestyle ]
tags: [About, Developer, TDD]
image: https://enable.com/images/blog/155-a.png
excerpt: These days I'm refactoring myself, and I hope writing here be a part of these refactoring.
         As a developer we should like refactoring. The main goals of refactoring are make our code more efficient, 
         more readable, and more elegant, so we should think ...
---

These days I'm refactoring myself, and I hope writing here be a part of these refactoring.
As a developer we should like refactoring. The main goals of refactoring are make our code more efficient, 
more readable, and more elegant, so we should think about that every time we can, 
when we're actively coding, when we're in launching time 
even though when we want to sleep (I think this type is the most interested one) 
also we should do it when we have a bug, when we want to implement a new feature, 
when we don't have anything to do and finally when we are in the day before release 
( again I think this type is the most excited one). How about requirements or maybe better called pre-requirements? 
we need reliable test scenarios. With these scenarios developers can be confident and motivated about refactoring. 
The process is obvious we change something, run test, fixed problem and again change something ...
 

I'm excited about starting this refactor. We can talk about all of the things that are wrong with it 
and confidently say every thing that we want to do... But doing it is a whole different thing. I'm trying to refactor 
myself in level of the system not the level of the results. I know our habits shape our the system we live with that, so 
I should improve and change my life habits. 
As a developer I want to have test scenarios for each refactoring so this time I want to have a test for refactoring myself.
The idea is clear, I need something for test my refactoring progress and don't know something more clear than code. 
The context is different, but I think at least it can be a good experience.
so if I want writing test about it maybe It is hard. the main problem is 

I hope this piece of code have more meaning for you than the sentence I wrote. ;)

```java
/**
 * @author: EhsanAsaDev
 */
@Test
private void testRefactoringMyself_SuccessD() throws Exception {

	assertTrue(havePlan());
	List<habit> habits= planService.getByName("refactoringMyself");
	for(Habit habit : habits){
	
		//assertTrue(plan.isAchieved());

        //The Best Way to Start a New Habit
        assertTrue(habit.haveTimeAndLocationToStart());
		
       //Walk Slowly But Never Backward
		assertTrue(progressService.beBetterABitFromYesterday(habit,today));
		
	}
}
```

P.S Some ideas behind this post is get from `Atomic Habits` book.





