# Filter

## Contents

1. Overview
2. Introduction
3. Flow Diagram
4. Configuration
5. Working

## Overview
When large quantity of data are being used by many application, but each application needs their own set of input (not all but only selected data is needed.) Every operation will have their own specific set of condition, only when the payload data satisfies these condition, they needs to be sent further for that operation.

## Introduction
Filter is an Java library which help to select only right data which satisfies the configured condition of the application. This library does the job of informing the application whether the data needs to be sent further or not.

Filter can allows to have any kind of complicated condition having both expression and operation like “AND” and “OR”.

Library will return true or false after evaluating the condition provided. True will signify that the data had satisfied the condition and false will signify the data didn’t satisfy condition, and can be ignored. 

## Flow Diagram

![Filter Design](https://github.com/Raghavendralacharya/Filter/blob/master/Design.png)

## Configuration

  No.| Field Name | Description | Data Types | Mandatory
------------ | ------------- | ------------ | -------------  | ------------- 
1 | type | Field tells what Boolean operation to be done on operands. Possible values are “AND” ,“OR”. | String | Yes
2 | cond	| Array containing object of all the conditional expression. Mandatory if any filter conditions needs to be added. |	Array |	Yes 
2.1 |	lhs	| Left operand of the expression. |	Object | Yes
2.1.1	| type | Type specifies what kind of value being configured. 
Ex: JSON_PATH tells value field contains path. Possible values are :“CONSTANT”, ”BOOLEAN”, ”JSON_PATH” | String |	Yes
2.1.2 | value	| Value field contains value of configured type. | String	| Yes
2.2	| operator	| Operation to be performed on left and right operand. Possible values are: ==,!=,<,>,<=,>=. | String	| Yes
2.3	| rhs	| Right operand of the expression. | Object	| No
2.3.1	| type | Type specifies what kind of value being configured. Ex: JSON_PATH tells value field contains path. Possible values are : “CONSTANT”, ”BOOLEAN”, ”JSON_PATH” | String | Yes
2.3.2	| value	| Value field contains value of configured type. |String | Yes

#### Few Rules for Writing JSON Path:

1. If value is present inside any object it should be written with “.” delimiter. 
```
Ex: data:{ 
    amt : ”15” 
    }
```
If you want to specify JSON path to get value “15”. It can be written as **“data.amt”**. 

2.	If value is present inside an array then the index of the value should be specified with “.” delimiter.
```
Ex1: data:[“high”,”moderate”,”low”]
```
If you want to specify JSON path to get value “high”. It can be written as **“data.1”**.
```
Ex2: 
data:[
{“damage”: ”high”},
{“damage”: ”moderate”},
{“damage”: ”low”}
]
```
If you want to specify JSON path to get value “moderate”. It can be written as **“data.2.damage”**.
#### Filter config example:
```
{
  "type": "AND",
  "cond": [
    {
      "lhs": {
        "type": "JSON_PATH",
        "value": "json.amt"
      },
      "operator": ">=",
      "rhs": {
        "type": "CONSTANT",
        "value": "10.00"
      }
    },
    {
      "type": "OR",
      "cond": [
        {
          "lhs": {
            "type": "JSON_PATH",
            "value": "json.amt"
          },
          "operator": "ISNULL",
          "rhs": {
            "type": "",
            "value": ""
          }
        },
        {
          "lhs": {
            "type": "JSON_PATH",
            "value": "json.boolVal"
          },
          "operator": "==",
          "rhs": {
            "type": "BOOLEAN",
            "value": true
          }
        }
      ]
    }
  ]
}
```

## Working:

Filter accepts message and config. Config will have all the condition which needs to be applied. 

Conditions in the cond list are processed sequentially. 

If AND given, then all the conditions values of the cond list would be ANDed to produce the final result. On any condition evaluating to False, evaluation of further conditions would be aborted and False is returned to the calling parent. 

If OR given, then all the conditions values of the cond list would be ORed to produce the final result. On any condition evaluating to True, evaluation of further conditions would be aborted and True is returned to calling parent.

lhs and rhs can have input json field (complete json path) /Boolean/Constant in value field. 
	
The possible values of operators are

* == denotes Equal to
* != denotes Not Equal to
* \> denotes Greater than
* \>= denotes Greater than or equal to
* < denotes Less than 
* <= denotes Less than or equal to
* ISNULL denotes Equals Null (Either field is not present or evaluates to “”)

If any operand value is json value then that respective data will be fetched from message. 
