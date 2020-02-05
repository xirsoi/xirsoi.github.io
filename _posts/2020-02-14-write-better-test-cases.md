---
layout: post
title: "Write Better Test Cases"
date: 2021-02-14 08:00:00-0600
categories: software testing
tags: manual-testing
excerpt: Learn some general advice for writing good manual test cases, examples included!
---

Writing test cases can be hard, but it can be easier *if* you know the right stuff. During this article we'll imagine we are writing test cases for a hypothetical catalog management system for a library.

## Keep Tests Small, Focused, and Clear

Small and Focused tests are easier to complete, easier to write, and easier to manage. It's the difference between building a single API endpoint and building the entire API.

With each individual test being relatively simple, there are fewer variables that must be juggled as you write and/or execute the test. Further: when looking at the current status of a test run, the health of the current build is much clearer.

### Example 1

Let's assume Test Case 123 covers the 'Create New Book' and 'Update Existing Book' happy path scenarios, and Test Case 321 covers the scenario of 'Creating a Book with No Author'. If Test Case 123 fails, what's broken? We don't know without digging into the test result and looking closely at the bug report. On top of that, any metrics in use to determine the health of the system based on pass/fail ratios will be inaccurate. A large test like Case 123 can also hide problems: if the 'Create New Book' part of the test fails, the 'Update Existing Book' part might not run, so there's no information on whether it would have succeeded. Having that information can help point to what part of the system is failing, speeding debugging.

If Test Case 321 fails we know immediately that 'Creating a Book with No Author' is broken and can act accordingly (for instance, not bothering to do any more bad-request scenarios until the bug is fixed).

Clear tests are essential because manual tests are for humans, specifically *other* humans that aren't looking at any source code, whether for automation or the system under test. Keep this in mind when choosing your words and "tone of voice,". Your co-workers will appreciate it, and so will you in 6 months when you've forgotten everything you wrote!

### Example 2

Consider Test Case 456:

1. log in
2. get the test book
3. change all the things, making sure each one can be changed individually and multiple things can be changed simultaneously

and Test Case 654:

1. open book "Test Book 654" for editing
2. verify that each of the following can be changed individually:
   1. Title
   2. Author
   3. Summary
   4. ...
3. verify that the Title, Author, and Summary can be changed simultaneously
4. reset the book's Title to "Test Book 654"

Test Case 456 is missing key details (eg., "what's the 'test book'?") and can easily be passed incorrectly if one of the fields is missed by the tester and that one happens to fail. Case 456 also neglects to reset the system so that the next person to run the test can find the 'test book' reliably.

## Avoid Being Overly Specific

This is mostly helpful to keep maintenance easier. You don't want to update many test cases because a small detail changed. For the same reason you don't want to have the same feature tested in many different test cases. Covering the same scenario in multiple test cases also wastes valuable time, since it adds nothing to our coverage.

This dovetails nicely with **Small/Focused/Clear**: there may be several small, focused tests which each touch a given feature, but they should mostly be focused on different aspects of the feature; a small change to the feature should only affect a small number of highly-focused tests.

Test Case 654 is certainly more clear than 456, but it may be too specific due to listing each field; each team/project will need to find the right balance.

### Example 3

It is unnecessary to give a specific menu path to reach a particular page in any tests that aren't trying to verify that path. If the page is moved to another section or a section is renamed, every test that included the full menu path will need to be updated, even though nothing they are trying to verify will have changed.

Consider tests of MS Office: any formatting tests that included "Click Edit -> Format" would have been broken by the introduction of the Ribbon even though the basic functionality being tested was still present.

## Avoid Extraneous Steps/Info

Anything not needed makes the test case longer and makes it harder to focus on what's important. This can take the form of unnecessarily wordy instructions, unneeded context, or even whole test steps that aren't necessary.

I've seen test cases whose first step is "Open the application" or "Open your browser and navigate to the site." In the vast majority of cases these are givens, so including them explicitly in the test just wastes keystrokes and time. I've also seen "Close the browser/application" and "report results" at the end of test cases; again, these are unnecessary.

## One Step, One Assertion

You may have heard this in the context of unit tests, but this advice works well for manual tests as well.

If you have multiple instructions and assertions in one test step it can be difficult for someone executing the test case to determine the relation between each action and it's expected results. Also, like **Small/Focused/Clear**, when a step is marked as failing it's obvious at a glance what went wrong.

### Example 4

Here we have a test for 'Updating a Book Record'. This case is also an example of a manual test case being used as documentation for the behavior of an automated test.

| Step              | Action                                                                                                        | Expected Result               |
|:----------------|:--------------------------------------------------------------------------------------|:-------------------------------|
| 1                   | Create a new Book record. Update the book's Author attribute.               | The Update is successful and the returned book object has the updated Author value and all other attributes are unchanged and correct. |
| 2                   | Update the record by removing the publication date. Now update it by making the publication date in the future and change the title (at the same time). | The Update is successful and the returned book record still has the original publication date.<br />The Update is successful and both the title and publication date are updated. The isReleased flag is false. |
| 3                   | ... more steps ...                                                                                          | ... more assertions ...         |

The first step includes two instructions and one assertion. Note that we aren't testing the create functionality in this step, so we don't bother asserting that the create was a success and returned the correct object. That would be redundant with the 'Create New Book' test.

The second step screws it all up and makes the step harder to follow. Step 2 is clearly two independent steps and there's no reason to squish them into one. This is obviously a trivial example, but I've seen dozens, perhaps hundreds, of test cases with similar and even more heinous things.

I'd like to point out that the second step also means this test case covers happy path and sad path scenarios. Whether that's okay is up to you and your team, but I tend to separate use cases into their own test for clarity.

## Format For Reading

All tests should be read far more frequently than they're written. Giving a test a once-over to ensure that each step is formatted correctly can easily save the test runner 30 seconds per run by being more clear and by being formatted in the expected way - a 10-minute formatting pass can easily pay for itself in 20 runs of the test (and likely far fewer when the test is referenced by a developer in trying to replicate a bug so they can resolve it).

In most test case management tools using Shift + Enter in test steps keeps lines together for paragraphs. This removes the extra blank line.
Use the code block formatting option when using bits of code or JSON to clearly mark them apart. The monospace font helps when reading it as well.
