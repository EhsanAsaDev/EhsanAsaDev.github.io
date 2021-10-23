---
layout: post
title: Run Cucumber integration test on Spring Project
comments: false
categories: [ Spring boot ]
tags: [BDD, Cucumber, gherkin ]
image: assets/images/BDD_cucumber_strip.png
excerpt: What is the BDD? What is cucumber integration testing?
         How we should write feature files? How we should config cucumber on the Spring context?
         In this post, we have a web base interactive game, and we want to test it with Cucumber.

---

### BDD

BDD is an extension of TDD, Like TDD our development are base on test scenarios. we can start from test and try to
explain behavior of our application. we focus on user-story to test all feature of the application.

Cucumber is a framework that supports behavior-driven development (BDD). Cucumber introduce simple english language for
writing the test call Gherkin. by using Gherkin language everyone on team can read and write test scenarios. It helps
technical and nontechnical stockholders working together simply, so we can have mush collaborate and communicate in the
team.

I want to show you what everyone is showing in the last. please read it carefully.
![cucumber feature file](/assets/images/cucumber_feature_file.PNG)


Do you enjoy this? is this meaning full for you? 

For our case, it could be written by someone who knows about Mancala
game rules. It's not necessary they would be a developer. Generally, feature tests could be written by everyone in the
team, like product owner, designer, and even customer. Each feature test help developer to obtain a deep understanding
of business and also it is also a good asset for validating features of the final product.

#### How we can run behavior test written by the Gherkin in Spring projects
I provided a production-ready project
on
[GitHub](https://github.com/EhsanAsaDev/mancala-game/)
and in this post, I want to show you how we can run Cucumber test on Spring projects.


##### Maven dependency
You might need to add these dependencies on pom.xml
```xml
<dependency>
    <groupId>io.cucumber</groupId>
    <artifactId>cucumber-spring</artifactId>
    <version>6.11.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>io.cucumber</groupId>
    <artifactId>cucumber-java</artifactId>
    <version>6.11.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>io.cucumber</groupId>
    <artifactId>cucumber-junit</artifactId>
    <version>6.11.0</version>
</dependency>
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
</dependency>


```

<br/>
##### Feature files
I suggest that create a folder Features under src/test/resources and create a separated feature file for each 
integration test scenario.

in this path [src/test/resources/features](https://github.com/EhsanAsaDev/mancala-game/tree/main/src/test/resources/features) :
You can find feature files. One of them is for testing the rest service and another for testing from the client side (browser).

In this post, I'm focusing on running integration test by web driver (browser), so we need this scenario:
[mancalaBrowser.feature](https://github.com/EhsanAsaDev/mancala-game/blob/main/src/test/resources/features/mancalaBrowser.feature)

but you can see [mancalaRest.feature](https://github.com/EhsanAsaDev/mancala-game/blob/main/src/test/resources/features/mancalaRest.feature)
This also is a good example to how test rest services with cucumber.

##### Glue code
Now we need to map each single Gherkin step to Java code. For this, we need to create a glue code.
something like this:


```java
@Given("first user browser is open")
public void firstUserBrowserIsOpen(){
    firstUserBrowser=openAndInitiateBrowser(new Point(0,0));
}

@And("first user browser is on main page")
public void firstUserIsOnMainPage() {
    firstUserBrowser.navigate().to(mainPageUrl);
}

@When("first user enter name in name box")
public void firstUserEnterNameInNameBox() {
    firstUserBrowser.findElement(By.id("name")).sendKeys("firstUser");
}

@Then("one game is created")
public void oneGameIsCreated() throws InterruptedException {
    Thread.sleep(2000);
    Alert alert = firstUserBrowser.switchTo().alert();
    if (alert.getText().startsWith("Your created a game. Game id is: ")) {
        gameId = alert.getText().substring(33);
        System.out.println(gameId);
        alert.accept();
    } else {
        Assert.fail();
    }
}
```

you can find more detail on [src/test/java/ex/com/challenge/cucumber/glue/](https://github.com/EhsanAsaDev/mancala-game/tree/main/src/test/java/ex/com/challenge/cucumber/glue)


##### Configurations

Then we need to run our Cucumber tests with JUnit, for this we should have one class
with an annotation @RunWith(Cucumber.class):

```java

@RunWith(Cucumber.class)
@CucumberOptions(
        plugin = {"pretty", "html:target/reports/cucumber.html",
                "json:target/reports/cucumber.json",
                "junit:target/reports/cucumber.xml"},
        features = "src/test/resources/features")
public class CucumberIntegrationTest {

    public CucumberIntegrationTest() {
    }
}
```
As you can see I introduced where is feature files located and also I use some useful plugins. plugins help us to use
the result of cucumber test in other tools like Jenkins.

This is an example of html plugin report:
![Cucumber HTML plugin sample result](/assets/images/cucumber_html_plugin_result.PNG)


Finally, we need to integrate the Cucumber tests with the Spring Application Context. 

```java

@CucumberContextConfiguration
@SpringBootTest(classes = {
        MancalaChallengeApplication.class,
        CucumberIntegrationTest.class},
        webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
public class SpringIntegrationTest {
}   
```
<br/>

##### Run Cucumber test
For testing a web application you need a browser driver. It depends to you. You can download any browser driver.
I download Firefox driver, you can find it on [mancala-game/src/test/resources/drivers/](https://github.com/EhsanAsaDev/mancala-game/tree/main/src/test/resources/drivers)
but you should config that on your glue code I did it on openAndInitiateBrowser().

```java

 private WebDriver openAndInitiateBrowser(Point position) {
        String projectPath = System.getProperty("user.dir");

        System.setProperty("webdriver.gecko.driver"
                , projectPath + "/src/test/resources/drivers/geckodriver.exe");
        WebDriver webDriver = new FirefoxDriver();

        webDriver.manage().timeouts().implicitlyWait(Duration.ofSeconds(30));
        webDriver.manage().timeouts().pageLoadTimeout(Duration.ofSeconds(30));

        webDriver.manage().window().setPosition(position);
        webDriver.manage().window().setSize(new Dimension(800, 500));

        return webDriver;
    }

```
Now run the MancalaBrowserSteps test. You can sit on your chair and see how two browsers open and two users playing with 
each other Mancala game. This is very enjoyable. I promise it!

![Cucumber Integration_Test](/assets/images/cucumber_integration_test.PNG)


