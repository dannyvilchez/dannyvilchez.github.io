---
title: Building a portfolio instead of learning another tool
date: "2025-07-14"
summary: "The tech job market is a mess, so I'm done padding my resume and I'm building a portfolio"
description: "The tech job market is a mess, so I'm done padding my resume and I'm building a portfolio"
toc: false
readTime: true
autonumber: false
math: false
tags: ["postfolio", "tech industry"]
showTags: true
draft: false
---

Today, hiring in the tech industry is more difficult than it has ever been.

There are fewer tech jobs available.

There's an increasing number of candidates.

And AI is exacerbating the situation.

The volume of resumes is insane, and to alleviate the pressure, HR platforms are using more automation and AI to filter resumes down to a manageable amount. We are witnessing a war of AI. Candidates use AI to pass HR filters, and HR platforms use AI to combat the increased use of AI for resume generation.

Let's be fucking real, do we all really believe candidates are proficient in every single technology tool mentioned in their resumes?

It's not uncommon to see something like this in a resume's skills section:

    Relational Databases: PostgreSQL, MySQL, Microsoft SQL Server, Oracle Database, MariaDB, Amazon RDS, Google Cloud SQL, Azure SQL Database
    NoSQL Databases: MongoDB, DynamoDB, Couchbase, Cassandra, Firebase Realtime Database
    Data Warehousing: Snowflake, Google BigQuery, Amazon Redshift, Azure Synapse Analytics, Databricks

The laws of physics prevent the above from being true. It's impossible to be proficient in all of these tools. No one is working on PostgresSQL, MySQL, and SQL Server simultaneously. We only have two eyes and one brain.

In reality, we're proficient in the broader categories, like "Relational Databases." If you're proficient with SQL Server, you're likely capable of working with Postgres. Sure, there are differences, but SQL flavors aren't a good reason to dismiss a candidate. Yet resumes are optimized like above, so that HR platforms don't automatically reject a candidate's resume for not matching the job description.

I'm not going to dive into the issues with job descriptions. We all know they suck ass. They've always sucked ass. And if you look at job boards or your own company's listings you'll see that they continue to suck ass.

Given this increased level of friction in the hiring process, I'm seeing that companies are going back to good ol' referrals.

And nothing pairs better with a referral than a portfolio.

So to shield myself against this clusterfuck of an industry, I’ve decided that the time I used to spend learning new tools (which served me well in the early years of my career) will now go toward building a portfolio. A set of projects that proves to hiring managers that I’m a competent developer and that my resume isn’t just a melting pot of keywords.

This isn’t some major revelation.

Everyone knows that having projects you can showcase strengthens your job prospects. That said, in the hundreds of resumes I’ve reviewed, I haven’t seen a single portfolio, and after five years of working in data, I don’t have a portfolio. The biggest reason? It’s fucking hard, and takes time outside of work to create. But I suspect that in this post-AI world, having a portfolio is one of the most valuable assets you can have for job security.

So since I’ve been accumulating fitness and health metrics for a long time through my Fitbit and Garmin watches, and tracking my weight and calories, my plan is to build a data warehouse to store all of that data. From there, I can build different types of data products.

It will be split into four major phases/smaller projects:

1. Creating the database and ingesting all historical data for fitness activities (weightlifting, running, cycling, and swimming), as well as health metrics from Fitbit and Garmin.
2. Developing a metrics layer and orchestration system for ingesting the data on an automated, regular basis.
3. Showcasing the data on my portfolio website through engaging data visualizations.
4. Developing a GPT layer where I can ask questions using natural language and get answers about my training and health data.

Not going to lie, it feels scary to take on this project and specially while doing it publicly, even though no one reads this blog yet. I have a large graveyard of unfinished projects that I once committed to finishing. A part of me really doesn't want this project to join that shitty hall of fame. con

But as the great Sam Reich always says,

_"The only way to learn is by playing. The only way to win is by learning. And the only way to begin is by beginning."_
