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

## Flow Diagram

![Filter Design](https://github.com/Raghavendralacharya/Filter/edit/master/Design.png)
