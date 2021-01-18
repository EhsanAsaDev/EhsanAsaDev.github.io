---
layout: post
title: Continuous Life Refactoring with TDD Approach
comments: false
categories: [ lifestyle ]
tags: [About, Developer, TDD]
image: https://enable.com/images/blog/new_way.png
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
The process is obvious we write/run the test, make it something, try to make it better and again write/run the test ...

![Don't act like Monolith](/assets/images/test_driven_development.png)
 

I'm excited about starting this refactor. We can talk about all the things that are wrong with it 
and confidently say every thing that we want to do... But doing it is a whole different thing. I'm trying to refactor 
myself in level of the system not the level of the results. I know our habits shape our the system we live with that, 
and success is a product of our system, so I should focus and try to improve and change my life habits instead of on 
the goals I want to achieve. 

As a developer I like to have test scenarios before each refactoring so this time I want to have a test for refactoring myself.
Think about it! with having test scenarios like developers who want to refactor their code, we can be confident enough, 
we can track ourselves easily, we can have a feedback immediately. For example for each day we try to have a plan and try to be 
better than yesterday and at the end of th day we can validate our tries and progress with tests. Like real world for developers when 
their test was failed It's not sign of the end of the world! we try again and again to build a better things and with 
this method we can always stay on the path.

I know It is more abstract but let's get our hands dirty with some code:

```java
/**
 * @author: EhsanAsaDev
 */
@Test
private void testRefactoringMyself_Success() throws Exception {

	
	Plan plan = planService.getByName("refactoringMyself");
    assertNotNull(plan);
	for(Habit habit : plan.getHabits()){
	
        //be smart this way doesn't work
		//assertTrue(plan.getTrget().isAchieved());

        //for good habits you want to have
        if(habit.getType().equal("good")){
                
            //The Best Way to Start a New good Habit
            assertTrue(habit.haveTimeAndLocationToStart());

            //Make it attractive and easy
            assertTrue(habit.isAtractiveAndEasyToDo());
            
            //Somtimes it’s easier to build a new habit in a new environment
            assertTrue(habit.isNeedNewEnviroment() : habit.isProvideNewEnviroment() ? true );

        }   
        // for bad habits you want to don't have or break 
        else{

             //Try to make doing that bad habit difficult or may be impossible for yourself
             assertFalse(habit.isEveryThingsReadyToDo());

             // Replace the bad habit with a good  one
             assertFalse(habit.isDetermindGoodOneToReplace());
   

        }     

        //find partner who have same goal and can give you extra motivation
        
        
        //review and think about by or without this habit who you want to be, then shur you like that     
        asserTrue(habit.isfutureImpactClearEnogth())
		

	}
    //Walk Slowly But Never Backward and track your habits
    assertTrue(progressService.beBetterABitFromYesterday(habit,today));

    


}
```
I hope this piece of code have more meaning for you than the sentence I wrote. ;)

P.S Some ideas behind this post is get from `Atomic Habits` book.





