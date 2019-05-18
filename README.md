# a2ml - Automation of AutoML
Th A2ML ("Automate AutoML") project is a set of scripts to automate Automated Machine Learning tools from multiple vendors. The intention is to provide a common API for all Cloud AutoML vendors.  Data scientists can then train their datasets against multiple AutoML models to get the best possible predictive model.  May the best "algorithm/hyperparameter search" win.

## The PREDIT Pipeline
Every AutoML vendor has their own API to manage the datasets and create and
manage predictive models.  They are similar but not identical APIs.  But they share a
common set of stages:
* Importing data for training
* Train models with multiple algorithms and hyperparameters
* Evaluate model performance and choose one or more for deployment
* Deploy selected models
* Predict results with new data against deployed models
* Review performance of deployed models

Since ITEDPR is hard to remember we refer to this pipeline by its conveniently mnemonic anagram: "PREDIT" (French
for "predict"). The A2ML project provides classes which implement this pipeline for various Cloud AutoML providers
and a command line interface that invokes stages of the pipeline.

## A2ML Classes
The A2ML Model class in A2ML.PY abstracts out the PREDICT (ICTEDPR) pipeline.  Implementations are provided for Google Cloud AutoML Tables (GCModel) and Auger.AI (Auger).   We will be adding support for Microsoft Azure AutoML soon. If you want to add support for another AutoML provider of your choice.  Implement a child class of Model as shown below (replacing each "pass" with your own code.

```
  class AnotherAutoMLModel(Model):  
      def __init__(self):
          pass     
      def predict(self,filepath,score_threshold):
          pass
      def review(self):
          pass
      def evaluate(self):
          pass
      def deploy(self):
          pass
      def import_data(self):
          pass
      def train(self):
          pass
```

## The A2ML CLI

This is the command line interface for the A2ML classes. It provides command line options
for each stage in the PREDICT Pipeline.

Usage:
```
$ a2ml [OPTIONS] COMMAND [ARGS]...
```
Commands:
* new       Create new A2ML application.
* import    Import data for training.
* train     Train the model.
* deploy    Deploy trained model.
* evaluate  Evaluate models after training.
* predict   Predict with deployed model.
* review    Review specified model info.

To get detailed information on available options for each command, please run:

```
$ a2ml command --help
```

## Configuration Options

After a new A2ML experiment is created, the overall experiment configuration
that applies to all providers is stored in CONFIG.YAML. The options available in the include:

* name - the name of the experiment
* source - the CSV file to train with
* target - the feature which is the target
* exclude - features to exclude from the model
* budget - the time budget in milliseconds to train 
* providers - list of AutoML providers: gooogle (for Google Cloud), azure (for Microsoft Azure), or auger

Below is an example CONFIG.YAML:
```
name: moneyball
source: https://storage.cloud.google.com/moneyball/baseball.csv?folder&organizationId=465405141758&_ga=2.120546504.-1320989109.1554983622
target: RS
exclude: Team,League,Year
budget: 3600
providers: google, azure 
```

### Google AutoML Tables Configuration
Some options are specified to certain providers. For Google AutoML Tables here
are the available options:

* metric - How to measure the accuracy of the model. For classification select from: MAXIMIZE_AU_ROC, MINIMIZE_LOG_LOSS, MAXIMIZE_AU_PRC. For regression select from: MINIMIZE_RMSE, MINIMIZE_MAE, MINIMIZE_RMSLE. See more at (Google AutoML Model Optimization)[https://cloud.google.com/automl-tables/docs/models]
* region - the compute region on Google Cloud: defaults to "us-central1"
* project - the name of the project in Google Cloud
* dataset_id - the Google Cloud dataset after source import, will be persisted to GOOGLE.YAML after import data

Here is a sample GOOGLE.YAML file:
```
region: us-central1
metric: MINIMIZE_MAE
project: automl-test-237311
```

### Azure AutoML Configuration

Azure's configuration options are:
* region - Azure compute region. Default is eastus2
* metric - For classification: accuracy, AUC_weighted, average_precision_score_weighted, norm_macro_recall, precision_score_weighted. For regression: spearman_correlation, normalized_root_mean_squared_error, r2_score, normalized_mean_absolute_error. For time series: spearman_correlation, normalized_root_mean_squared_error, r2_score, normalized_mean_absolute_error. 

Here's an example AZURE.YAML file:
```
metric: spearman_correlation
region: eastus2
```
### Auger AutoML Configuration

## Development Setup

We strongly recommend to install Python virtual environment:

```
$ pip install virtualenv virtualenvwrapper
```

Clone A2ML:

```
$ git clone https://github.com/deeplearninc/a2ml.git
```

Setup dependencies and A2ML command line:

```
$ pip install -e .[all]
```

Running tests and getting test coverage:

```
$ pytest pytest --cov='a2ml' --cov-report html tests/  
```
