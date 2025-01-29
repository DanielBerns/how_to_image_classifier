# Machine Learning system

## Building Machine Learning Systems is Hard
Starting from the very beginning, 
the process for a functional and useful system contains at least all of the following steps:

1. Ideation and defining of your problem statement
2. Acquiring (or labelling) of a dataset
3. Exploration of your data to understand its characteristics
4. Building a training pipeline for an initial version of your model
5. Testing and performing error analysis on your model’s failure modes
6. Iterating from this error analysis to build improved models
7. Repeating steps 4-6 until you get the model performance you need
8. Building the infrastructure to deploy your model with the runtime characteristics your users want
9. Monitoring your model consistently and use that to repeat any of steps 2-8

[source](https://www.mihaileric.com/posts/setting-up-a-machine-learning-project/)


## Defining Your Machine Learning Problem
Arguably the most important step in creating a machine learning application is the problem definition. 
This means both determining what problem you want your application to solve (the value add) as Ill as how you will measure success (your metrics).

----------------------------------------------------------------------------------------------------------------------------------------------------------------
Given that this is an election year where many people across the US will be looking to form their opinion about candidates through various internet outlets, for the purposes of this project, I will tackle the problem of fake news detection. In the modern day, fake statements are more prevalent and are able to spread more virally than ever before.

To help alleviate this problem, I will want to build an application that can assess the truthfulness of statements made by either political speakers or social media posts.

Once completed, I would ideally like to deploy our project as a web browser extension that can be run in realtime on statements that users are reading on their pages.

As far as metrics is concerned, if our tool can automatically detect whether a statement is true or false on a random web page with at least 50% accuracy, then I will consider our project a success. Typically success if measured by some sort of business metric, but since I don’t have a formal metrics like that, this (somewhat arbitrary) threshold for accuracy will suffice.

----------------------------------------------------------------------------------------------------------------------------------------------------------------

## Organizing Your Repository and Tooling
Now that I have our product goal and metrics defined, let’s describe how I will organize our application code repository. I’m choosing to spend some time talking about this because there is not a clear consensus in the community on best practices for organizing machine learning projects.

This makes it very difficult to quickly look at new projects and understand how the separation of concerns is defined, making collaboration and iteration slow.

As a counterpoint, consider the convention around how Java projects are structured. Because of a community agreed-upon consensus, any Java programmer can jump into a new codebase and immediately know where to find what.

In lieu of a common convention, I will describe practices for organizing machine learning projects, born out of my experience with various projects over the years.

At the top-level, our system will be structured as follows:

Source/
  project/
    assets/
    config/
    deploy/
    core/
        model/
        utils/
    notebooks/
    scripts/
    tests/
    LICENSE
    README.md
    requirements.txt

Data/
  project/
     raw/
     processed/
     reports/
     model_checkpoints/

Here is what each each item is responsible for:

Source/project: 

1. assets: This will store any images, plots, and other files we need: we can put here css files, icons, logos.
2. config: any configuration files needed for model training and evaluation here.
3. deploy: This will store any files needed for deployment including Dockerfiles, etc.
4. core: This will store all the source for building, training, and evaluating our models.
5. notebooks: This will store any Jupyter notebooks used for any data analysis.
6. scripts: This will store any one-off scripts for generating model artifacts, setting up application environments, or processing the data.
7. tests: Here I will include any unit tests, integration tests, and data tests to ensure the correctness of our system.
8. LICENSE: Our software license.
9. README.md: A high-level description of the project.
10. requirements.txt: This will store the code dependencies for our project. This is a standard practice for Python-based projects.

Data/project
This will store our project data 
1. raw: untouched from the original source 
2. processed: featurized or updated for current use case
3. model_checkpoints: model weights binaries for training and deploying.
4. reports: human readable generated outputs (logs and descriptions).


## Getting Your Initial Dataset
Data is the heart of any machine learning project. Having high quality data with your desired characteristics can be the difference between a product that does what you want it to and one that is nonfunctional.

In practice, having the right data is even more important for final success than the exact modelling algorithm you use.

So rather than spend too much time developing complex models, your time is often better spent just cleaning up your labels. 

n the early stages of a project, we want to acquire a reasonably-sized dataset that we can use to build a v1 of a system including an end-to-end pipeline.

The machine learning lifecycle is iterative and every step will be refined as the project unfolds. Therefore our dataset doesn’t have to be massive, but it does have to be large enough to exhibit the properties that we want.

There are a few options here. We can either:
1. Use a crowdsourcing platform such as Amazon Mechanical Turk to have humans curate and label our data (a costly, time-consuming process).
2. Use an existing publicly-available dataset.
3. Write!!


## Performing Exploratory Data Analysis

[source](https://www.mihaileric.com/posts/performing-exploratory-data-analysis/)

Once we have a first version of our data, we need to perform exploratory data analysis (EDA) which is a skill that every data scientist/machine learning engineer should have. At a high-level, the goal of EDA is to probe our data in as many ways as possible to gain an understanding for its characteristics. A few questions that can guide our exploration:

1. What is the label distribution? Do we have a balanced/imbalanced dataset?
2. What are the distributions of feature values?
3. Are there any features that are malformed?

Among other things, the goals of these questions are to derive insights about our data that can ultimately inform our modelling choices such as features or algorithms to use.

### Clean Data - A Machine Learning Myth
It’s worth pointing out that no dataset is perfectly clean.

Data is laden with inconsistencies, outliers, biases, and noise that make the job of modelling a lot more difficult. Even when you are taking curated research datasets, it’s always worth doing some sanity-checking of your model before you start drawing meaningful conclusions.


## Data Preprocessing
As a first step in building our model, we need to get data in the right format for ingestion into the training pipeline. 

Now we want to take those data insights from EDA and clean and preprocess the initial raw data into the X \rightarrow yX→y mapping we need for supervised learning.

To do this, we will define a number of stand-alone scripts that do this cleaning and preprocessing.

We use stand-alone scripts because this makes it easier to separate out pieces of functionality that can be eventually be used as executable stages in our pipeline. In practice, these stages are eventually automated as workflows with tools like Airflow.

We first define a script called normalize_and_clean_data.py. This script first reads in our data 
and then performs normalization and cleaning.

## Defining the Model
When defining the model, we certainly have a number of choices around architecture and model class. The exact model we choose is a function of a number of factors such as optimal performance on downstream metrics but also practical considerations like resource contraints or latency.

In the early stages of a project, one of the most important considerations is not to focus too much on getting the best-performing model.

Rather the goal is to get some model fully integrated into a pipeline where it is servicing requests to a user, so that you can actually measure the product-level metrics you care about (user engagement, click-through rate, etc.) These metrics will inform any further model development efforts.


# The Training Pipeline
Ok, so now we have a cool model. Let’s build it into a functional training/evaluation pipeline.

One important step in any good training pipeline is making it easily configurable, i.e. making it easy to plug-and-play different components/attributes of the pipeline such as model, featurization, or data parameters.

You typically make these pipelines configurable by either passing in commandline-level arguments or by providing a configuration file.

I personally prefer the configuration route because it allows the actual configuration file to be version-controlled and tracked in the code repository.

We will configure our pipeline using a JSON-formatted file. 

The definition is relatively straightforward. We define our model type, where we should pull data from, where we should write the model, whether we are in evaluate or train mode, and any model-specific parameters.

We next move to our train pipeline, which will be responsible for stitching together everything into an executable series of steps. 

## Functionality Tests
So now we’re written all this stuff. How do we know any of this even works?

We can certainly run the full pipeline (and we will!) but are we really going to have to retrain a model from scratch every time we make a small change to the model code?

This quickly becomes impractical, especially if we are working with a team on a shared codebase.

To get around that problem, we need to include functionality tests. This is a very common practice in broader software engineering but sadly not something I’ve seen a lot of in the machine learning community.

There are a few resources that do a good job of covering the different kinds of testing in machine learning, so check them out for further details.

For our purposes we will write a few different functionality tests.

One specific kind of test we will employ is data integrity testing.

What we mean here is tests that check whether your data source has the expected format, types, range of values, etc.

If the data doesn’t have the format you expect, the rest of your pipeline will be screwed up since data is the start of your training pipeline.

Data testing becomes especially important when you have a continuous ETL pipeline running that is ingesting, processing, and dumping new data periodically.

While here we are dealing with a static research dataset, we will go through the exercise of writing data integrity tests anyway.

To do this, we will use the library Great Expectations. It allows you to specify what you expect of your data in neat snippets of JSON.

## Error Analysis And Model V2

Our goal is to get some model up and running that could be trained via a configurable pipeline and provide immediate value to users.

We want to build a model that achieved roughly >50% accuracy on a held-out test set.

At this point we would ideally deploy our model to users, get people interacting with it, and use those results to inform an improved model. This reflects the continuous feedback-loop based nature of ML product development

Insert image

Confusion matrix

## Model V2
We will now work on building another model on our dataset. When doing this we could leverage the new feature insights we gained from our feature and error analysis to iterate on our random forest model.

## Deploy

First, we will study how to deploy our model via a REST API.

Afterwards we will discuss how to setup continuous integration so that we can constantly update, test, and deploy the latest version of our project.

### Setting Up a Prediction Rest API

Run the model server-side and make REST API calls to the server.

