# *Predictions*

 - The *Prediction* component of the API is a prediction made by a *Model* on a *Document*
    
After a *Model* has been trained and a *Document* has been created, a *Prediction* can be made as shown in the example below:

(TTNote: Again, since 'model' is referenced in this section, consider resequencing the 'models' section (https://docs.lucidtech.ai/getting-started/introduction/models) to be listed above this one. Consider adding links to each section as well.)

```commandline
>> las predictions create las:document:fe1cfdb5de254bb68d7c2763d3861baf las:model:03bce190cf4343efb3eb5065bd999844  
{
  "predictionId": "las:prediction:b68233f457f54342bd65acbdb7053637",
  "modelId": "las:model:03bce190cf4343efb3eb5065bd999844",
  "documentId": "las:document:fe1cfdb5de254bb68d7c2763d3861baf",
  "predictions": [
    {
      "label": "bank_account",
      "value": "05395598149",
      "confidence": 0.9996034754459902
    },
    {
      "label": "customer_id",
      "value": "218395942l7317305",
      "confidence": 0.9697508355421477
    },
    {
      "label": "total_amount",
      "value": "439.00",
      "confidence": 0.9529096553664052
    },
    {
      "label": "due_date",
      "value": "2020-01-25",
      "confidence": 0.035909883433622676
    }
  ],
  "timestamp": 1612950528,
  "inferenceTime": 3.6797611713409424
}
```

Note: Common in all *Predictions* is that they have a list of dictionaries in the format shown below: 
```
    {
      "label": "<field-name>",
      "value": "<predicted-value>",
      "confidence": <confidence-of-prediction>
    }
```


By specifying a `confidence` with every prediction, you can make decisions on whether to use or discard the results 
depending on the accuracy required for a specific case. 

In the example above, the confidence reveals that the `due_date` is likely to be wrong, 
while the `bank_account` is very likely to be correct.



*Predictions* can also be listed, which can be useful for making statistics or inspecting the performance of the model:
```commandline
>>las predictions list
{
  "predictions": [
    {
        ...
    }
  ],
  "nextToken": null, 
}
```


