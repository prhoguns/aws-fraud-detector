# How I Created  a fraud detection system on AWS

**Originally published:** Wed, 15 Mar 2023 04:15:55 GMT

**Original post:** [Read it on My Cloud Journey](https://rhoguns.hashnode.dev/how-i-created-a-fraud-detection-system-on-aws)

> Article text reproduced from the original post’s RSS body. Formatting was converted to Markdown and screenshots point to local copies. See [image notes](project-notes.md) for annotations.

Amazon Fraud Detector is a service that uses machine learning to make it easier to identify potentially fraudulent online activities such as online payment fraud and the creation of fake accounts.

This is a guide on how I used this service to build, train and deploy a fraud detection model which could potentially be used by e-commerce sites to reduce fraudulent activities.

# Introduction

Amazon Fraud Detector is a fully managed service that uses machine learning (ML) and more than 20 years of fraud detection expertise from Amazon to predict predefined outcomes based on user configuration.

For this project, I used user data provided by aws for learning purposes but the same process could be used on custom user data(I might sometimes refer to this as the training data).

This is the configuration I used:

![](images/01.png)

## Setting up

The first step in training the model will be uploading the dataset for this project I uploaded my data into an s3 bucket.

The next step was creating an event for the Amazon Fraud Detector model. To do this I selected entities on the menu and clicked on create, I named the entity "customer" and selected create the entity.

The next step was to create labels for fraudulent and legitimate transactions, I did this by selecting "Labels" from the menu on the left and creating 2 labels named "fraud" and "legit" respectively

I then create an event by again using the menu on the left and clicking on the event. I named this event "registration" which is supposed to indicate a customer registration event, and I selected the entity "customer" which I created earlier. For the IAM role, I typed in the name of the s3 bucket I created earlier with the user data, it created an IAM role named `AmazonFraudDetector-DataAccessRole-\***` and for data location I provided the location of the bucket containing the user data.

## Training and Deployment

This is how I trained and deployed the model.

The first step was to click on the model in the left navigation panel, click on "Add model" and then "Create a model" I named the model "fraud\_detection\_model" then I selected "Online Fraud Insights" for model type and for the Event type I used the "registration" event I created earlier.

In the Historical event data section, for IAM role I choose the IAM role I created Previously and for the training data location, I provide the S3 location of the training data file.

Then I got to the label classification section here I used the labels I already created, for Fraud labels I picked "fraud" and for Legitimate labels, I picked "legit"

I then got to the review page and clicked on "create and train model" which trained the model. This part took about 30 minutes to complete. after that was done I clicked on the version number and looked at the model performance it showed the metrics generated, including the Score distribution and the Confusion matrix. This data is useful in defining the appropriate thresholds for prediction using the detector. The data looked like this

![](images/02.png)

I also saw the False positive rate (FPR), True positive rate (TPR), and Precision values in the table which can be used to choose the threshold predictor.

I then scrolled up and clicked on "Actions " and clicked on "Deploy model version" which started the deployment of the fraud detector.

## Creating and Publishing

First I selected "Outcomes" from the menu on the left and then I clicked on create. I named the new outcome "high risk" which is meant to describe a high-risk fraud registration, I then created 2 more outcomes and named the "low\_risk" and "medium\_risk".

The next step is to create a detector. to do this I selected detectors from the menu on the left, then clicked on "create detectors". on the define detector details page I name the detector "detector-getting-started" which is meant to use model scores and rules to predict outcomes and I used the event created earlier "registration" as the event type.

Next, I added the model I created earlier "fraud\_detection\_model" to the fraud detection model. The next step is to define rules, I created a rule name "auto-fraud-rule" and typed "$<fraud\_detection\_model>\_insightscore >= 900" for expression and selected "high\_risk" as the outcome, this is going to set the upper threshold to 900 and make that a high-risk outcome, then I added 2 more rules for the medium risk low-risk outcomes. I then reviewed and created the detector and then I published

## Testing

I Ran son tests in the console to show how well the fraud detection system works, Here are the results:

![](images/03.png)

![](images/04.png)

### Conclusion

I am excited to have successfully built and deployed a fraud detection model using Amazon Fraud Detector. Through this project, I have gained valuable insights into how machine learning can be used to detect and prevent fraudulent activities online. I have also learned how to use Amazon Fraud Detector's various features and tools to configure and fine-tune my model to achieve the desired results.
