---
title: Modeling my weightlifting data
date: "2025-07-16"
summary: "Started modeling my weightlifting data, built the schema in 3NF, and laid the groundwork for getting my first dataset into the warehouse."
description: "Started modeling my weightlifting data, built the schema in 3NF, and laid the groundwork for getting my first dataset into the warehouse."
toc: true
readTime: true
autonumber: false
math: false
tags: ["portfolio", "data-modeling"]
showTags: true
draft: false
---

## **Modeling My Weightlifting Data**

Now that the database is up and running, the next step is to start building the data model. Over the years, I’ve collected a lot of data across different areas:

- Weightlifting
- Running
- Cycling
- Swimming
- Health metrics from Garmin and Fitbit
- Weight data from a smart scale (synced to Fitbit)
- Calorie tracking from MacroFactor

It’s a lot. And while all of it ties into my overall fitness and health, trying to model it all at once would turn this into a backend project with no end. So I’m starting with the lowest-hanging fruit: weightlifting.

### **Why Weightlifting First**

I’ve been logging strength training longer than anything else. All of the data lives in two apps: RP Hypertrophy and FitNotes. RP Hypertrophy doesn’t let you export your data, which is frustrating for an app that costs $240 a year. FitNotes, on the other hand, is free and supports CSV export.

So I’m manually transferring my old RP Hypertrophy data into FitNotes, exporting it to CSV, and using that as the historical dataset.

_Side note: I’m cancelling RP Hypertrophy. It’s not a bad app and I learned a lot about planning mesocycles, but FitNotes does 100x more for free. If you’re on Android, I highly recommend it. I’ve paid to support the developer (it’s optional), and I think more people should._

## **Ingestion Strategy**

The plan is to bulk insert the CSV data into a raw schema. For now, I’ll keep it simple and truncate/reload everything on each run since the exported data doesn’t change. Once the pipeline is in place, I can optimize later to only ingest new rows.

From there, I’ll normalize the data into a 3NF model, following Inmon’s architecture. This is probably overkill for a personal fitness dataset, but I'm interested in learning about Inmon's ideas. Most modern pipelines transform raw data straight into a dimensional model. That’s fine. But this time, I want to build a fully normalized data warehouse first, then layer a star schema on top for analysis.

Would I do this for a small client? No. If all they need is a line graph, I’d throw the data into a BI tool and model it inside the BI tool and if the data needed to be distributed to many users, then I'd model it in a database. But this is a learning exercise, so I'm going to over do it on purpose.

## **The Model**

For the weightlifting portion of the system, here’s a quick overview of the key tables:

- **Exercise**: Distinct movements (e.g., “Squat”, “Pull-Up”)
- **MuscleCategory**: High-level groupings like Chest, Back, Legs
- **Muscle**: Specific muscles (e.g., Lats, Biceps) linked to a category
- **ExerciseMuscle**: Junction table linking exercises to muscles, with a primary muscle flag
- **Activity**: A single workout session, regardless of type
- **ActivityType**: Lookup for workout types (e.g., Strength, Running)
- **StrengthSet**: Each logged set in a strength session (reps, weight, date)
- **Mesocycle**: Grouping of sessions into a training block (with start/end dates)
- **WeightUnit**: Lookup for kg vs lbs (important when I’m training abroad)
- **Calendar**: Standard date dimension for time-based joins

**Exercise Model:**

![data model for exercises](/images/exercise-model.png)

**Session Model:**

![data model for activities](activity-model.png)

## **What’s Next**

- Create the tables in the database based on the model
- Manually finish moving data from RP Hypertrophy to FitNotes
- Export the CSV and bulk insert it into the raw schema
- use dbt to move data into 3NF

Once that’s done, I’ll build the transformations into the normalized structure. Then I can finally start seeing what the data actually has to say.
