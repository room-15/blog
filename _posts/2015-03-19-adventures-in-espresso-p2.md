---
layout: post
title: Adventures in Espresso and Unit Testing Part 2
post_author: Carl Anderson
---

One of the articles that I read, while researching how to write good UI tests was [this one](http://code.tutsplus.com/articles/maintainable-automated-ui-tests--net-35089).  And one of the most profound ideas from that article was:

> Good UI tests are ones that have good software design principles applied to them.

### Designing reusable tests

*You can view Part 1 of this series [here](http://room-15.github.io/blog/2015/03/17/adventures-in-espresso/)*

Having gotten Espresso up and running, I excitedly started writing test code. My first real test was terrible. It was worse than the BrittleTest shown in that article. As a "low hanging fruit", I wanted to write a test that just loaded each individual screen in my app. Since some of them were only accessible when you were logged in, I decided to write one huge monolithic test that visited tons of different screens and logged into the app while visiting them (and then logged out at the end of the test). My test was terrible because it was just one gigantic test doing far too many things at once, and because it was fragile - any change to the layout / navigation of my code would have broken the test. If it was difficult to fix the test, then all of my testing would just have to be thrown away.

So I deleted that test and started over, with the principles of applying the Page Object pattern to my app. Each page (Activity / Fragment)  in my app was going to get a separate class that knew how to manipulate that specific screen. Additionally, for any screen that has some sort of user input (EditText, etc), I would create a POJO object that these classes would take, and be able to input that POJO properly into the fields.

I'm a big believer in concrete examples, so let's examine one of my new test situations. I wanted to test whether a user could log into my app using their Email account. When the app starts up, the user is presented with a MainBrowseActivity that has numerous tabs displayed. In order to log in, the user pushes the profile button, which displays an OnboardingFragment where we try to get the user to sign up for the app. The button on this Onboarding fragment takes a user to a SignUpActivity, which has a login button on it. This login button takes them to a LoginActivity, where they (finally) can put in their credentials and log into the app.

![Code Flow]({{ site.url }}images/Flow.png)

After writing all of my test code, this is the top-level test that I run to do a login:

{% highlight java %}
    public void testLoginAndLogoutWithEmail() {
      TestMainBrowseActivity main = new TestMainBrowseActivity();
      TestLoginActivity login = main.navigateToOnboarding().navigateToSignup().navigateToLogin();
      EmailLoginPOJO emailLoginPOJO = EmailLoginPOJO.createValidLogin();
      login.login(emailLoginPOJO);
      main.navigateToSettings().logout();
    }
{% endhighlight %}

Let's look at what this code is doing.

> TestMainBrowseActivity main = new TestMainBrowseActivity();

Since my tests want to replicate the behavior of a user's flow through the app, I don't start with the Login page (although Espresso allows that). Instead I instantiate the Page Object corresponding to the first page of the app:

{% highlight java %}
    public class TestMainBrowseActivity {

        public TestMainBrowseActivity() {
            onView(withId(R.id.action_bar_me_button)).check(matches(isDisplayed()));
        }

        public TestOnboardingFragment navigateToOnboarding() {
            onView(withId(R.id.action_bar_me_button)).perform(click());
            return new TestOnboardingFragment();
        }

        public TestSettingsActivity navigateToSettings() {
            openActionBarOverflowOrOptionsMenu(getInstrumentation().getTargetContext());
            onView(withText("Settings")).perform(click());
            return new TestSettingsActivity();
        }
    }
{% endhighlight %}

The first thing to notice about this code, is that the constructor does an Espresso test to see if one of the buttons for that Activity is visible. I do a similar test in each of the Page Objects. If one of the obvious UI components isn't showing, then clearly something in the navigation is probably messed up, and the test should fail at that point. One thing I'm not doing, that I might do in a future is try to encapsulate the navigation into separate Navigator classes.

The next thing to notice with the code, is that when one screen is told to navigate to another one, it alone knows what button to push to do this, and then it returns the proper instantiation of the next Page Object. Thus in *navigateToOnboarding()*, it's critical to actually load that page first by clicking the button, so that the TestOnboardingFragment can, in its own constructor, validate that the screen has properly loaded.

Now that I've demonstrated the structure of the Page Objects, this line should be fairly straightforward:

> TestLoginActivity login = main.navigateToOnboarding().navigateToSignup().navigateToLogin();

*navigateToOnboarding()* is returning a TestOnboardingFragment, which tests in its constructor to ensure that fragment is being displayed. And then, on that object we're calling *navigateToSignup()* which returns a TestSignupActivity, and the pattern repeats, until we have the TestLoginActivity which we actually want to act on.

In the next line, we're creating (from a static factory method) a valid login POJO object:

> EmailLoginPOJO emailLoginPOJO = EmailLoginPOJO.createValidLogin();

Here is that function:

{% highlight java %}
public static EmailLoginPOJO createValidLogin() {
    EmailLoginPOJO emailLoginPOJO = new EmailLoginPOJO();
    emailLoginPOJO.setEmail("noIdidntPutValidLoginCredential@intoThisExample.com");
    emailLoginPOJO.setPassword("butThanksForChecking");
    return emailLoginPOJO;
}
{% endhighlight %}

The advantage here, is that if the requirements for a valid email login change, all I have to do is modify the POJO, and the TestLoginActivity, and none of the rest of the code has to be modified at all. I can see at a later time, adding additional static factory methods that generate invalid logins, to test things like invalid email addresses, and invalid usernames. 

Finally we can perform the actual login:

> login.login(emailLoginPOJO);

Here's the Login function in our TestLoginActivity class:

{% highlight java %}
public TestUserDetailFragment login(EmailLoginPOJO pojo) {
  onView(withId(R.id.login_email)).perform(typeText(pojo.getEmail()));
  onView(withId(R.id.login_pass)).perform(typeText(pojo.getPassword()));
  onView(withId(R.id.login_button)).perform(click());

  try {
    sleep(4000);
  } catch (InterruptedException e) {
    e.printStackTrace();
  }

  return new TestUserDetailFragment();
}
{% endhighlight %}

As you can see, we first take the POJO and apply the data to the specific EditText fields on that page, and then click the login_button. One thing I haven't implemented yet (but Espresso supports) is a way to wait for results before continuing with the current test, called a IdlingResource. For now I just have the sleep command while I wait for the user login to hit the backend server, but will be replacing that eventually. Finally, once the login is successful, the user is landed on their own profile page, so I instantiate a UserDetail Page Object and return it.

Finally, since we've at this point successfully logged in (or detected a failure), we want to log out before continuing further:

> main.navigateToSettings().logout();

The UserDetail is merely a fragment within the MainBrowseActivity, so I can reuse my previous instantiation of that object to navigate to the settings activity and tell that activity to log out, and then we're done.

Hopefully this has given you a good structure from which to write your own resuable, maintainable Android UI tests. Feedback is, of course, welcome in the comments!

#### About the author

*Carl Anderson is the lead Android developer for [Trover.com](http://trover.com)'s Android app, having gotten his feet wet doing Android development at Amazon.com for the Kindle Fire. He has over 13 years of experience in software development, and has been writing Android code since 2013. He lives in Seattle with his wife and two daughters.*
