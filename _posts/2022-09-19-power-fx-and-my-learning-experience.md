---
layout: post
title: Power Fx And My Learning Experience
date: 2022-09-19 13:24 -0600
categories: [dynamics, powerapps]
tags: [dynamics, dynamics-ce, powerapps]
---

A few weeks ago a customer asked my employer to create a custom page within their Dynamics CE (Customer Engagment) environment. This page would
contain the results of a specific table from a SQL database store externally from their CE environment. The customer wanted to be able to query
specific data from this table. With that in mind, the architect assigned to the customer approaches me and says, "I think a Canvas App
would be perfect for this situation!".

## The Problem

The requirements for the Canvas App seemed simple. The customer wanted to have a list of fields that they could use to query data. They wanted the data to load live as they typed into the individual fields, but they wanted to filter on data that would be found in multiple fields. Below is an image that represents an example of what the fields would look like:

![Power Fx Fields](/assets/pictures/powerfxfields.png)

This seemed straight forward to me at the time. I would create the fields, create a gallery to render the data from sql, and create a Power Fx function to search the data. It wasn't until I started writing my Power Fx function that I ran into issues. First, I decided to use an if statement to check the first field in the list. In Power Fx, an if statement basically looks like the following:

```
If(Condition, action for if condition is true, action for if condition is false)
```

I took the first field in the Canvas App and applied this logic. To do this, I would need to first check if the field is blank. If the field was blank, I would return the entire sql table. However, if it wasn't blank, then I would take the data from the field, compare it to the corresponding sql field, and create a filter. The resulting function looks like this:

```
If(IsBlank(Field1.Value), sqltable, Filter(sqltable, Field1.Value in sqlfield1))
```

Simple right? Up to this point, it was. However, now I needed to do this for all the other fields in the app. With this in mind, I searched Microsoft's Documentation for answers. I could not find any documentation where you could apply multiple Power Fx functions within one single gallery like I was trying to do. I was stumped. However, I did eventually come up with an idea.

## The Solution

While trying to find a solution, I decided to try nesting if statements within each other to get filter fields. I tried doing this many different ways, but I only got the function to work properly one way. First, I decided to use an if statement to check if a field was blank. If it was, then the true condition would have another nested if statement. This was to check the following field to see if it was blank.

```
If(IsBlank(Field2.Value), If(IsBlank(Field1.Value), sqltable, "Else Statement"), "Else Statement")
```

Now if all the fields were searched and were blank, then we would return the base sql table into the gallery. Second, we would need to filter on each section if a field did not come up blank. They I did this was by first checking to see if the last field in the set was blank. If it was not blank, then I would check and create a filter based on all the fields before it. If that field was blank, it would move up to the next field and do the same logic.

```
If(IsBlank(Field2.Value), If(IsBlank(Field1.Value), sqltable, Filter(sqltable, Field1.Value in sqlfield1), Filter(sqltable, Field2.Value in sqlfield2, Field1.Value in sqlfield1)))
```

This seemed to work. When all field were blank it would return the entire table, but if there was data in a field then it would properly attempt to filter on the field and any other fields that had data. However, because of the way the function needed to be nested, it very quickly became difficult to see the different parts of the function. The final result looked something like this:

![Power Fx Fields](/assets/pictures/powerfxquery.png)

## My Thoughts

In my opinion, while this solution works, it seeems very cluttered and hard to read. I constantly was searching Google to see if other people had found a different solution because the way I was trying to solve this problem felt wrong. However, this was the way I found to solve this problem. As a Software Developer and Engineer, I wish I knew of a way to solve this problem with code, which would of have made my problem more simple to solve.

Did you find a different way to solve this problem? Send me a message, I would love to know what you did.
