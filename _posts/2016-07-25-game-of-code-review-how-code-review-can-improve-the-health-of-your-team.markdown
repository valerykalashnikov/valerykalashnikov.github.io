---
title: Game of Code Review. How code review can improve the health of your team
layout: post
date: "2016-07-25 12:58:05"
categories: githubflow codereview
---


In fact code review process is a part of agile workflow when developers can spread their skillset across of the team making them more self-organized and responsible. It allows the team to learn better the code base, explore new technologies and gives an overview of a project, which means everyone knows what happens in the whole project not only the code one owns.

## So, what is code review?

When you’re done with implementing your feature, another developer goes through your code and asks:

  * If all cases are implemented
  * If the new code is well-tested
  * If there are any errors in the code
  * If the new code matches the existing best practices and guidelines

Feature should be accepted once and only all of the above requirements are passed.

In this article we will not discuss types of code review such using separate email thread, chat or pair programming. All of us did it and nobody likes smiles substituting the commas, colons and parenthesis in the code.

## Why does it matter?

### Sharing the knowledge

As we said, it spreads the knowledge. There aren’t any person who knows only a specific part of the codebase. This implies you can go to vacation and just take a rest without being disturbed by phone calls and emails from the team.

### Estimation
Everybody knows estimation problems and it hurts in most cases. During code review, the author and reviewer know everything about time spent for the feature. They can provide a good feedback and use special arguments during meetings and brainstorms improving the quality of further estimation.

### Discipline
By preparing new feature to review, developers get used to make a separate branch for a new feature, then open pull requests, assign it and waiting for a review. Also this means no pushes to master anymore. Your code becomes nicer and cleaner and developers are more disciplined and happy because they like the code they did.

### Communication
The strong team is a team with strong communicative skills. A mess in the code, in the organization process and in entire life is based on miscommunication.
But… not every developer likes corporate parties with drinks and funny competitions, however everybody know that a good developer is the one who likes to code.
During the reviewing process developers talk about what they like the most and make their own contests - code review contests.
At [BlameWarrior](http://blamewarrior.com) we’re trying to help developers to make their own contests and to grow the discipline using code reviews, but firstly we have to say some words about code review problems.

## Code review problems
Here are the common issues we experienced while reviewing someone’s code:

  * Who do they think they are to consider themselves better than me and tell me that this is bad design? It works!
  * Why do I have to review the mess written by a new junior developer instead of implementing a new rocket-science feature?
  * I don’t know who will responsible for that pull request. I’ll just leave it here… for a month… or maybe a year.
  * Dude, sorry, I forgot about your pull request, I’ll review it, seriously.
  * Sorry boss, we forgot to accept his pull request, we’ll need some more time now to review it and merge, you know…

## Crush your code review problems with the fist of BlameWarrior

We have to admit, although people like ideal results, they are not ideal themselves. Only few of us like to review or being reviewed, especially when it comes to pull requests with lots of commits and improvements.

In the previous chapters we told you about theoretical aspect of code review. Enough theory, let’s proceed to the practical part.

Imagine, you decided to use code reviews to improve your codebase. Here are your steps:

  1. Create a new branch for your feature and implement it.
  2. Create a pull request
  3. Assign somebody as a reviewer
  4. Reviewer gets a notification about pull request and start commenting your code asking the question pointed below
  5. After review is done you have to reply to comments made to elaborate your idea. This often needs to be repeated until both you and reviewer come to agreement
  6. Fix your code in the current branch with accordance to remarks
  7. After everyone is clear, the pull request gets merged to master

But without of people nonideality they can:

  * Forget to assign reviewer to new pull request or even be afraid to assign someone. As a result nobody gets notified about the pull request and it can abandoned for a long time (it is not a joke, some pull requests can be reviewed in a year)
  * Be discontent because somebody don’t have time to review changes and he don’t have time to merge the branch with tons of conflicts.
  * Merge unreviewed changes before 5 minutes to deadline. Developers can miss changes because of lack of notifications about new unassigned pull requests.
  * Hate code review and git flow because of pointed below and promote this point of view to junior developers.

We believe that the mission of every developer is to look after his code, and we cannot stand watching our colleagues giving up the benefits of best practices. Thus we created [BlameWarrior](http://blamewarrior.com) - a project that helps to make your code base less smelly.

With BlameWarrior you can:

  1. “Attack” the author of the code by discussing their changes. The "Random Almighty" will pick and assign a reviewer every time you open a new PR.
  2. “Defend” your technical decisions and get rewarded for the every line you have changed and advocated. The bigger the pull request, the bigger the reward you get.
  3. Be perpetuated in the Hall of Fame and obtain awards blessed by the Allpowered Gods.

Want to give BlameWarrior a try? Here is what you need to do:

  1. Get in invitation here: [https://blamewarrior.com/invitation/requests/new](https://blamewarrior.com/invitation/requests/new)
  2. Go to [https://blamewarrior.com](http://blamewarrior.com)
  3. Sign in via GitHub
  4. Choose a repository in which you want to set up your battleground.

That’s it. Do code review and use GitHub flow the way you did before. BlameWarrior assigns the reviewer, calculates scores for comments and diffs automatically in the background. You only need check your scores and positions in the Hall of Fame. We didn’t do any kind of email notifications intentionally because we don’t like tons of emails from services  - all important notifications GitHub already does.

Let's bathe in the glory with BlameWarrior and may the Gods be with you!





