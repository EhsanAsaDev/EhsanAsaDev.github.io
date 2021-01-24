---
layout: post
title: Continuous Life Refactoring with TDD Approach
comments: false
categories: [ lifestyle ]
tags: [About, Developer, TDD]
image: assets/images/new_way.png
excerpt: These days I'm refactoring myself, and I hope writing here could be a part of this refactoring.
         As a developer, we should like refactoring. The main goals of refactoring are making our code more efficient, 
         more readable, and more elegant, so we should think about ...
---

These days I'm refactoring myself, and I hope writing here could be a part of this refactoring.
As a developer, we should like refactoring. The main goals of refactoring are making our code more efficient, 
more readable, and more elegant, so we should think about that every time we can, 
when we're actively coding, when we're in launching time 
and even when we want to sleep (I think this type is the most interesting one) 
also we should do it when we have a bug, when we want to implement a new feature, 
when we don't have anything to do and finally when we are in the day before release 
( again I think this type is the most exciting one). How about requirements or maybe better-called pre-requirements? 
we need reliable test scenarios. With these scenarios, developers can be confident and motivated about refactoring. 
The process is obvious, we write/run the test, make it something, try to make it better and again write/run the test ...

![test driven development](/assets/images/test_driven_development.png)
 

I'm excited about starting this refactor. We can talk about all the things that are wrong about 
and confidently say everything that we want to do... But doing it is a whole different thing. I'm trying to refactor 
myself in level of the system not the level of the results. I know our habits shape the system we live with that, 
and success is a product of our system, so I should focus on changing my life habits instead of focusing too much on 
the goals I want to achieve. 

As a developer I like to have test scenarios before each refactoring so this time I want to have a test for refactoring myself.
Think about it! by having test scenarios like developers who want to refactor their code, we can be confident enough, 
track ourselves easily, have an immediate feedback. For example, for each day we try to have a plan and try to be 
better than yesterday and at the end of the day, we can validate our attempts and progress with tests. 
just Like real world developers when their test has failed it's not the end of the world! we try again and again to build better things and with 
this method, we can always stay on the path.

I know it is very abstract but let's get our hands dirty with some code:

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

        //for good habits that you would like to have
        if(habit.getType().equal("good")){
                
            //The best way to start a new good habit
            assertTrue(habit.haveTimeAndLocationToStart());

            //Make it attractive and easy
            assertTrue(habit.isAtractiveAndEasyToDo());
            
            //Somtimes it’s easier to build a new habit in a new environment
            assertTrue(habit.isNeedNewEnviroment() ? habit.isProvideNewEnviroment() : true );

        }   
        // for bad habits that you  don't want to have 
        else{

             //Try to make the bad habit difficult or impossible to do
             assertFalse(habit.isEveryThingsReadyToDo());

             // Replace the bad habit with a good one
             assertFalse(habit.isDetermindGoodOneToReplace());
   

        }     

        //find a partner who has the same goal and can give you extra motivation
        asserTrue(isFindSomeoneToAccompanying())
        
        //Think about who you want to be without this habit and make sure that you like that    
        asserTrue(habit.isFutureImpactClearEnough())
		

	}
    //Walk slowly but never backwards and track your habits
    assertTrue(progressService.beBetterABitFromYesterday(habit,today));

    


}
``` 
I hope this piece of code has more meaning for you than the sentence I wrote. ;)

P.S I got some ideas of this post from `Atomic Habits` book.