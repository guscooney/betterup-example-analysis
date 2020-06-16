betterup-example-analysis
==============================

Example analyses for BetterUp conversations.

### Getting started

For analysis, we recommend creating a separate repository for each project that you work on. This repository can act as a template that will setup an environment for accessing and analyzing competitions.

You can create a new repository using this one as a template (copies the entire repo, but none of the git history, branches, etc. that you don't need) by clicking the green "Use this template" button that appears in the repository header on GitHub:

![](https://user-images.githubusercontent.com/1799186/74196423-0a0d7d80-4c12-11ea-88f2-8582d3835a80.png)

#### Prerequisites

 - Python >= 3.6


 #### Recommended prereqs

  - conda
  - ffmpeg >= 4.2

## Steps to get started:

Getting started requires creating a Python environment (recommended), installing the requirements, and running jupyter. A couple of useful commands are provided for you in the Makefile (you can see the options and what they do by just running `make` at the command line or by reading the Makefile). There's nothing 

### 1. Create a new Python environment (recommended)

If you have conda (which we assume later on), this will create a new conda environment for you named `betterup-example-analyses`.

```
$ make create_environment
```

### 2. Activate the environment (recommended)

```
$ conda activate betterup-example-analysis
```

### 3. Install the requirements

**Note:** You will need access to the private repository `drivendataorg/betterup-convo-pipeline` which hosts the code for the `ConvIO` object. Reach out on the #convo-analytics BetterUp Slack channel to be provided access.

```
$ make requirements
```

### 4. Accessing conversations on S3

In order to access conversations on AWS S3, you will have to have access to the bucket where the _processed_ conversations live. As of writing, those conversations are in `s3://betterup-conv-analysis/processed/` Generally, ConvIO will do the right thing to get your AWS credentials, but there are a few ways to use explicit credentials if you need:


#### Defined in code

You can pass your credentials directly to `AwsManager` with:

```
from convio.aws import AwsManager
from convio.convio import ConvIO

aws = AwsManager(
    aws_access_key_id="ACCESSKEYGOESHERE",
    aws_secret_access_key="ACCESSSECRETGOESHERE",
)

convo = ConvIO(path_to_convo, aws_manager=aws)
```

In general this is not preferred, since it makes you likely to check these secrets into version control.


#### Environment variables

If the following environment variables exist, they will be used as your credentials

```
AWS_ACCESS_KEY_ID=ACCESSKEYGOESHERE
AWS_SECRET_ACCESS_KEY=ACCESSSECRETGOESHERE
```

If you are using the `python-dotenv` package, you may have these varaibles defined in a `.env` file. You can use these credentials just by making sure those variables get loaded:

```
# load any variables defined in the .env file
from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())

# import after the variables are in the environment
from convio.convio import ConvIO

# the AWSManager will use the variables from .env
convo = ConvIO(path_to_convo)
```


#### `~/.aws/credentials` file

If you ran `aws configure` from the awscli package, you will have a `~/.aws/credentials` file with your credentials. This file optionally has multiple "profiles" if you have more than one set of credentials. If you don't specify a profile, but this file exists we will use the `default` profile.  If you do have a different profile, you could use those credentials with:

```
from convio.aws import AwsManager
from convio.convio import ConvIO

aws = AwsManager(
    aws_profile="profile-name-here",
)

convo = ConvIO(path_to_convo, aws_manager=aws)
```


### 5. Running the example notebooks

Run a jupyter server to serve the notebooks in the notebooks folder so you can use them interactively. If you're just interested in reviewing the notebooks, 

```
$ jupyter notebook notebooks/
```

You can also see the [rendered notebooks on GitHub](/notebooks) if you don't want to run a Jupyter server.


### 6. Do your own analysis

#### Specific conversation

Let's say I know I want to anayze the conversation with ID `abc-def`. I can do that by point directly to it, for example:

```
from convio.convio import ConvIO

convo = ConvIO("s3://betterup-conv-analysis/processed/abc-def")
```

#### All conversations

If I want to look at statistics across conversations, I can load all of the conversations in this way, which will return to me a list of all of the processed conversations as `ConvIO` objects. 

```
from convio.convio import ConvIO

convo_list = ConvIO.load_conversations(
   's3://betterup-conv-analysis/processed/',
)
```


#### Other documentation

The [example notebook](https://github.com/drivendataorg/betterup-convo-pipeline/blob/master/docs/examples.ipynb) for ConvIO provides additional information about the available functions for ConvIO and how to use them.

Installing the `betterup-convo-pipeline` as we do in this repository will also make the interactive docs for `ConvIO` available. To get these run the following command in the terminal, which will start a server with the docs:

```
$ convio-docs
```

![](https://user-images.githubusercontent.com/1799186/74199880-4349eb80-4c1a-11ea-9dd6-9190e84a4904.png)



Project Organization
------------

    ├── Makefile           <- Makefile with useful commands
    ├── README.md          <- The top-level README for developers using this project.
    ├── data
    │   ├── processed      <- A local folder for storing data transformed during this analysis.
    │   └── raw            <- A local folder for storing raw data.
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    └── setup.py           <- makes project pip installable (pip install -e .) so src can be imported


--------

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
