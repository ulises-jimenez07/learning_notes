# MLOps

## Best Practices

1. Code  on how a feature is generated should be track underversion control and published with aotu-incremented or timestamp hash versions
2. Many of the paramters extracted for feature engineering depend on the data used for training. Ensure data is reproducible
3. If replacing by extracting random samples, always set a seed.
4. Record the order of the features
5. Recored applied feature transformation
6. Record hyperparameters

## Architecture

1. Every model specifictaion undergoes a code review and is checked into a repository
2. Models can be quickly and safely rolled back to a previous serving version.
3. The full pipeline in integration testing
4. all input features code is tested
5. Model specifications code is unit tested
6. Model quality is tested before attempting to serve it

### Approaches

1. Embedded
   1. Model artifact packaged within the consuming application
   2. Predict on the fly
   3. Simple
2. Dedicated Model API
   1. Model is wrapped in a service that can be deployed independently
3. Model published aas data
   1. Different publish/subscribe patters
4. Offline predictions


# MLOPs GCP

- MLOPs refers to the combination of best practices, principles and tools for deploying, monitoring, and
mantaining machine learning models.
- ML Ops focus not only in code but also data

## Components

1. Datascience
2. Pipeline components
3. CI/CD
4. Other aspects
   1. feature store
   2. Performance monitoring
   3. Model versioning

## CI/CD

**Continous Integration** in ML revols around the automated integration of code and data changes into the main project.
**Continous Deployment** automaticall deploying the integrated and tested code

## Artifact/Container Registry
Private docker registry


1. Activate
   1. Container Registry
   2. Artifact Registry
   3. Cloud Build
2. Install
   1. Pytest
   2. Flask

Cloud run for 
- web applications
- api end points
- event-driven application
- **ml model serving**

## Example
1. contenainerize Flask app
2. Push container registry
3. Deploy image to cloud run
4. execute and test the pytest script for the flask application
5. deploy the application to cloud build for automated builds, testing and deployment
6. set up github repository and cloud build triggers to automate CI/CD


Activate API AIM

## Continous training

Model performance starts degrading.The model is not trained in new arrived data and deliver it
in and deployt 

### Pipeline that
1. Data validation
2. Model Training
3. Model evaluation: Performance metrics checks
4. Save and deploy the new model artifactt
5. Automated Monitoring and Alerting

### Apache Airflow

- is an open source platform for programatically authoring, scheduling and monitoring workflows.
It allosw to define complex worfloas as directed acyclic graphs and manage the execution tasks
- Popular choice for data science and ML orchestration
- Allows to orchestrate the execution of various ML taks and dependencies such as:
  - Data validation
  - Model training
  - Model evaluation
  - Deployment in a scalable and automated manner

