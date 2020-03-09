# Filter

## Contents

[1.	Overview](#Overview)

[2.	Overview](#Introduction)

[3.	Overview](#Configuration)

[4.	Overview](#Few-Rules-for-Writing-JSON Path)

[5.	Overview](#Working)

[6.	Overview](#Developer-Section)

## Overview
In Finacle at times, there is a need to evaluate conditions (or filters) based on fields of a json object. Hence there is a need for a common filter Java library which can take an input, a condition and return True/False after evaluation of the condition.

## Introduction
Filter is a Java library which evaluates a filter condition (or logical condition) on an input json object. This library allows complicated condition having both expression and operation like “AND” and “OR”. Filter object is defined in terms of json object.
	
Library will return True or False after evaluating the logical expression given in the filter condition. 


## Configuration

  No.| Field Name | Description | Data Types | Mandatory
------------ | ------------- | ------------ | -------------  | ------------- 
1 | type | Field tells what Boolean operation to be done on operands. Possible values are “AND” ,“OR”. | String | Yes
2 | cond	| Array containing object of all the conditional expression. Mandatory if any filter conditions needs to be added. |	Array |	Yes 
2.1 |	lhs	| Left operand of the expression. |	Object | Yes
2.1.1	| type | Type specifies what kind of value being configured. Ex: "FIELD" tells value field contains path. Possible values are :“CONSTANT”, ”BOOLEAN”, ”FIELD” | String |	Yes
2.1.2 | value	| Value field contains value of configured type. | String	| Yes
2.2	| operator	| Operation to be performed on left and right operand. Possible values are: ==,!=,<,>,<=,>=. | String	| Yes
2.3	| rhs	| Right operand of the expression. | Object	| No
2.3.1	| type | Type specifies what kind of value being configured. Ex: "FIELD" tells value field contains path. Possible values are : “CONSTANT”, ”BOOLEAN”, ”FIELD” | String | Yes
2.3.2	| value	| Value field contains value of configured type. |String | Yes

## Few Rules for Writing JSON Path:

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
        "type": "FIELD",
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
            "type": "FIELD",
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
            "type": "FIELD",
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

Basic lowest level json construct for filter (also referred as logical condition) is given below. 
```
    {
      "lhs": {
        "type": "FIELD",
        "value": "json.amt"
      },
      "operator": ">=",
      "rhs": {
        "type": "CONSTANT",
        "value": "10.00"
      }
    }
```

The above is a logical condition where there is a lhs field, operator and rhs field. The lhs and rhs” field will be tuple with {type, value} and relationship between them is defined using the operator.   
	
The possible values of operators are
```
* == denotes Equal to
* != denotes Not Equal to
* > denotes Greater than
* >= denotes Greater than or equal to
* < denotes Less than 
* <= denotes Less than or equal to
* ISNULL denotes Equals Null (Either field is not present or evaluates to “”)
```

Only in case of ISNULL operator, there need not be rhs field. lhs field and rhs field is mandatory for all other operators. 

Next higher-level construct is condition which is specified as 

```
{   
“type” : “AND”,     
“cond” : [ (logical_condition_1), (logical_condition_2),…(logical_condition_n)]
}
```

Here the value of type can be “AND” or “OR”.  Logical_conditions in the array are processed sequentially. 

If AND given, then all the conditions values of the cond list would be ANDed to produce the final result. On any logical_condition evaluates to **False**, evaluation of further logical_conditions would be aborted and return value of the interface function is **False**. 

If OR given, then all the conditions values of the cond list would be ORed to produce the final result. On any logical_condition evaluating to **True**, evaluation of further logical_condition would be aborted and return value of the interface function is **True**.

## Developer-Section:

Filter Java Library (jar) can be included in any project by specifying the following dependency in pom.xml of the project.

```
<dependency>
	<groupId>com.finacle.filter</groupId>
	<artifactId>filter</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</dependency>
```

The interface of the function accept two parameter one is message and the other one is filter config. The return type of the Jar is Boolean (true or false). 

#### Sample code snippet to call the library :

```
package com.finacle.App;

import com.finacle.filter.Filter;

public class App 
{
    public static void main( String[] args )
    {
	Boolean result = Filter.apply("message","filterConfig");
    }
}
```

In the above sample code **apply** is the interface which is exposed by the Filter java library, which accepts either message and Filter config in stringified format or Both in JSONObject format.

Filter Jar will evaluate the condition and return true, if the condition evaluation returns true or else false.


