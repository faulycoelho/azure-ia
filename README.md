In [Azure Machine Learning studio](https://ml.azure.com/?azure-portal=true), view the Automated ML page (under Authoring).

Create a new Automated ML job with the following settings, using Next as required to progress through the user interface:

# Basic Settings:
- **Job name:** car-auto-ml
- **New experiment name:** car-price
- **Description:** Automated machine learning for car price prediction
- **Tags:** none

## Task type & data:
- **Select task type:** Regression
- **Select dataset:** Create a new dataset with the following settings:
  - **Data type:**
    - **Name:** car-prices
    - **Description:** Historic car price data
    - **Type:** Tabular
  - **Data source:**
    - **Select From web files**
  - **Web URL:**
    - **Web URL:** [https://raw.githubusercontent.com/faulycoelho/azure-ia/main/car_data.csv](https://raw.githubusercontent.com/faulycoelho/azure-ia/main/car_data.csv)
    - **Skip data validation:** do not select
  - **Settings:**
    - **File format:** Delimited
    - **Delimiter:** Comma
    - **Encoding:** UTF-8
    - **Column headers:** Only first file has headers
    - **Skip rows:** None
    - **Dataset contains multi-line data:** do not select
  - **Schema:**
    - **Include all columns other than Path**
    - **Review the automatically detected types**
    
  Select **Create**. After the dataset is created, select the **car-prices** dataset to continue to submit the Automated ML job.

## Task settings:
- **Task type:** Regression
- **Dataset:** car-prices
- **Target column:** price (integer)
- **Additional configuration settings:**
   - **Primary metric:** Normalized root mean squared error
   - **Explain best model:** Unselected
   - **Use all supported models:** Unselected. You’ll restrict the job to try only a few specific algorithms.
   - **Allowed models:** Select only RandomForest and LightGBM - normally you’d want to try as many as possible, but each model added increases the time it takes to run the job.
   - **Limits:** Expand this section
      - **Max trials:** 3
      - **Max concurrent trials:** 3
      - **Max nodes:** 3
      - **Metric score threshold:** 0.085 (so that if a model achieves a normalized root mean squared error metric score of 0.085 or less, the job ends.)
      - **Timeout:** 15
      - **Iteration timeout:** 15
      - **Enable early termination:** Selected
- **Validation and test:**
   - **Validation type:** Train-validation split
   - **Percentage of validation data:** 10
   - **Test dataset:** None
   
## Compute:
- **Select compute type:** Serverless
- **Virtual machine type:** CPU
- **Virtual machine tier:** Dedicated
- **Virtual machine size:** Standard_DS3_V2*
- **Number of instances:** 1

Submit the training job. It starts automatically.
Wait for the job to finish. It might take a while.



## Deploy and test the model
On the Model tab for the best model trained by your automated machine learning job, select Deploy and use the Web service option to deploy the model with the following settings:
- Name: predict-rentals
- Description: Predict cycle rentals
- Compute type: Azure Container Instance

Wait for the deployment to start - this may take a few seconds. The Deploy status for the predict-rentals endpoint will be indicated in the main part of the page as Running.
Wait for the Deploy status to change to Succeeded. This may take 5-10 minutes.


## Test the deployed service
Now you can test your deployed service.

1. In Azure Machine Learning studio, on the left hand menu, select Endpoints and open the predict-rentals real-time endpoint.

2. On the predict-rentals real-time endpoint page view the Test tab.

3. In the Input data to test endpoint pane, replace the template JSON with the following input data:

```
{
  "Inputs": {
    "data": [
      {
        "car_make": "Toyota",
        "car_model": "4Runner",
        "year": 0,
        "condition": "new",
        "mileage": 0
      }
    ]
  },
  "GlobalParameters": 1.0
}
```

4. Click the Test button.

5. Review the test results, which include a predicted number of rentals based on the input features - similar to this:
```
{
"Results":[
0:23712.678693295537
]
}
```
