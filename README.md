**Overview:** Developed a Natural Language Processing (NLP) model to automate the assignment and severity prediction of tickets. The model uses a Random Forest Classifier and achieved an accuracy rate of 86%.

Steps and Tools:

**Data Collection and Preprocessing:** Collected a dataset of past tickets with assigned categories and severity levels. Cleaned and preprocessed the text data, including removing stop words, stemming, and lemmatization using NLTK (Natural Language Toolkit).  **Feature Extraction:** Employed TF-IDF (Term Frequency-Inverse Document Frequency) to convert the text data into numerical features suitable for machine learning.  **Model Selection:** Chose the Random Forest Classifier due to its robustness and ability to handle high-dimensional data. 
 **Training and Validation:** Split the dataset into training and validation sets. Trained the Random Forest model on the training set and tuned hyperparameters using cross-validation. 
 **Evaluation:** Evaluated the model's performance using accuracy, precision, recall, and F1-score. Achieved an accuracy rate of 86%.  **Impact:** This model automated the process of ticket categorization and severity assessment, reducing manual effort and improving response times for critical issues.


This folder contains the following scripts:

**helpers.py:** This script contains helper functions that assist in various NLP and processing tasks. It’s used in the training and prediction models.  **nltktraining.py:** This script is responsible for training an NLP model using NLTK and other libraries. It can be scheduled as a cron job for periodic updates.  **nltkprediction.py:** This script performs predictions using the trained model. Like the training script, it’s also scheduled to run periodically using cron.

## Configuration Files

### cronfile
This file defines the cron jobs that periodically execute training and prediction tasks:

```bash
# For instance -- Run the training job every Tuesday at 12:38 PM
38 12 * * 2 /usr/local/bin/python /usr/app/src/nltktraining.py >> /var/log/cron.log 2>&1

# Run the prediction job every 5 minutes
*/5 * * * * /usr/local/bin/python /usr/app/src/nltkprediction.py >> /var/log/cron.log 2>&1
```


# Dockerfile
The Dockerfile sets up a Python environment with all the necessary dependencies, installs cron, and configures it to execute the tasks defined in the cronfile.
```bash
FROM python:latest
WORKDIR /usr/app/src
RUN apt-get update && apt-get install -y cron

COPY helpers.py ./
COPY nltktraining.py ./
COPY nltkprediction.py ./
COPY cronfile /etc/cron.d/cronfile

RUN pip install pandas tensorflow scikit-learn pymongo nltk
RUN chmod 0744 ./nltktraining.py ./nltkprediction.py
RUN chmod a+x /etc/cron.d/cronfile
RUN crontab /etc/cron.d/cronfile
RUN touch /var/log/cron.log
CMD cron && tail -f /var/log/cron.log
```
# How to Use
**Install dependencies:** Ensure all required libraries are installed.  **Docker Setup:** Build and run the Docker container using the provided Dockerfile.  **Cron Jobs:** The cron jobs are defined in the cronfile, which will automatically schedule and run training and prediction tasks.

Example to build the Docker image: 
```bash
docker build -t ticket-processor .
docker run -d ticket-processor
```
