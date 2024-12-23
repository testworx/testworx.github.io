---
layout:     post
comments: true
title:      "Don't Substitute Good Diagrams for Easier Diagrams"
subtitle:   
date:       2024-12-23 00:00:00
author:     "Nick Oppersdorff"
header-img: "img/post-bg-02.jpg"
---

### Introduction
A picture speaks a thousand words, or so the saying goes...

Diagrams are an integral part of system design.  Whilst wordy requirements can hide a lot of ambiguity, and mask an author's understanding, or lack of, a diagram holds that understanding up to the light.  "This is how I think it looks".  These are the states an entity will pass through.  This is the logical flow as we process an entity.  This is the sequence of interactions between the components in our system as the entity is processed.  It's all there, in black and white (and maybe some colour for the creatives).  

Different diagrams serve different purposes and require different levels of detail and understanding.  Our choice of diagram can have a material impact on the quality of the software that is developed, and create oppotunities for missing functionality and serious defects.

As it's Christmas, let's consider a non-technical scenario that we all understand: the delivery/non-delivery of a parcel....

#### The Logical Flow

![flow_diagram.png](/assets/img/2024/december/flow_diagram.png)


This flow diagram diagram provides a reasonable (for this example at least!) represeantion of the logical flow in the delivery of a parcel.  Most people could look at it and understand it.  But there is a big difference between this high level flow, and actually being able to build a reliable delivery network.  

- Who even touches the parcel? 
- Who is responsible for carying it to the delivery address?  
- Who physically returns it to the sender?

Being able to answer these questions is the difference between the parcel moving correctly from the Sender -->  Sorting Facility --> Recipient, and being left in a warehouse somewhere, uncollected or undelivered.

#### Who's Job is it Anyway?
To understand a process with multiple stages, we really need to identify the actors involved, and the hand offs between them.  To do this, a sequence diagram is a much better tool.  

![sequence_diagram.png](/assets/img/2024/december/sequence_diagram.png)

This diagram gives us a much better understanding of how our process works in terms of the people (components) and their responsibilities.  We can clearly see who hands off to who, and what happens when a delivery address cannot be found.

Delving down to this level of detail helps us answer the questions that our flow diagram couldn't.  If we understand this, it is less likely that the parcel will remain underlivered.

#### Why?
The diagrams offer us different levels of detail when it comes to specifiying a process.  We have used a simple idea to communicate how one diagram might not offer us the detail we need.