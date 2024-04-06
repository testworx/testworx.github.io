---
layout:     post
comments: true
title:      "Using Default Test Data"
subtitle:   "For automated tests"
date:       2017-05-07 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

<h2>Why use default test data?</h2>
<p>Imagine a really long online registration form requiring a user to navigate through multiple pages, entering data as they go.  Suppose we wanted to test some functionality several pages into the journey.  It is highly likely that we don't really care about the data we enter on earlier pages in the journey, we just want it to get to the page under test.  As such, why waste time crafting this data for each specific test if we don't care about it?</p>
<p>By creating default data, we are able to simplify our test creation, using the defaults for the stuff we don't care about and overriding the data specific to the test in question.</p>

<h2>Git Repository</h2>
<p>A git repository is available containing all the examples used in this blog post:  <a href="https://github.com/testworx/default-test-data">here</a></p>

<h2>Encapsulating Test Data with Groovy Beans</h2>
<p>A simple solution to avoid specifying new data for each test is to encapsulate data in Groovy beans (or Java beans!), pre populating the bean properties with default data.</p>
<p>For the purposes of this example, think of a "bean" as a class containing pre-populated properties accessed via getters and setters.</p>

<h4>Example data entity</h4>
<p>It is always good to define data in terms of the business entities involved in the test.  Lets assume we are testing a Login page.  We would typically have <i>Username</i> and <i>Password</i> fields.  We might consider the data for these fields as belonging to a <i>User</i>.  A simple data entity might be a User that we model with <i>User.groovy</i>.</p>
<p>
  <pre>
      <code>package dataentities

class User {
    def username = "testUser"
    def password = "abc123"
    def firstname = "Tester"
    def surname = "TestSurname"
    def email = "testuser@test.com"
}</code>
  </pre>
</p>

<h2>Writing Automated Tests that use Data Entities</h2>
<p>Now we have created our test data, we want to use it to do some testing!  Lets write some tests for a Login page that demonstrate how we can read and override the default data.  Note that the examples below will compile but won't run.</p>

<h4>Example test that reads the default data</h4>
<p>Lets look at a test that verifies a successful login in <i>LoginPageSpec.groovy</i>.</p>
<p>
  <pre>
      <code>def "User is able to log in with valid username and password"() {
        given: "A user"
        User userDetails = new User()

        and: "The user is on the Login page"
        to LoginPage

        when: "The user enters their login credentials"
        enterLoginDetails(userDetails)

        and: "Submits their credentials"
        submitCredentials()

        then: "The user is successfully logged in"
        title == "Welcome $userDetails.firstname $userDetails.surname"
    }</code>
  </pre>
</p>

<h4>Example test that overrides the default data</h4>
<p>Lets look at a test that overrides the default password in <i>LoginPageSpec.groovy</i>.</p>
<p>
  <pre>
      <code>def "User is unable to log in without specifying a password"() {
        given: "A user with no password specified"
        User userDetails = new User()
        userDetails.with {
            password = ""
        }

        and: "The user is on the Login page"
        to LoginPage

        when: "The user enters their login credentials"
        enterLoginDetails(userDetails)

        and: "Submits their credentials"
        submitCredentials()

        then: "The user is not logged in"
        title == "Login failed!"
    }</code>
  </pre>
</p>

<h4>Example Login page object</h4>
<p>Here is the sample page object, <i>LoginPage.groovy</i>, that is used in the example tests above.  It contains a method that makes use of the data entity <i>User.groovy</i>.</p>
<p>
  <pre>
      <code>package pages

import dataentities.User
import geb.Page

class LoginPage extends Page {

    static at = {
        title == "Login"
    }

    static content = {
        usernameInput {$()}
        passwordInput {$()}
        loginButton {$()}
    }

    def enterLoginDetails(User userDetails) {
        usernameInput.value(userDetails.username)
        passwordInput.value(userDetails.password)
    }

    def submitCredentials() {
        loginButton.click()
    }
}</code>
  </pre>
</p>

<h2>Why is this a good way of doing things?</h2>
<p>Rather than thinking about all the data required for a test, we simply need to focus on the data that is directly relevant to the outcome we are testing and rely on the defaults for other values where appropriate.</p>
<p>We reduce our maintenance by writing our page object methods so that they take data entity objects rather than individual data field values giving us concise, stable method signatures, leaving it to the page object methods in question to read out the values they require, reducing maintenance over the longer term.</p>
