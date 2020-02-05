---
layout: post
comments: true
title: Machine Common Sense
---



This January through my externship at IBM Research in Cambridge, MA I got introduced to a very interesting area of AI, that is Machine Common Sense. To be more precise, the project was called "Reasoning about Visual Intuitive Physics". In this post I want to introduce this area and hopefully make people reading this more interested in Machine Common Sense.
<img src="/files/voe.gif" width="100%" height="auto" display="block">


### Are intelligent systems intelligent?

It turns out that "intelligent systems" are not that intelligent, or at least not as much as someone out of the field would think. Many of these systems are very specialized to do well in particular tasks, like classifying an image or translating from one lanaguage to another. However, these  systems lack what we call "common sense", the basic ability of humans to reason and understand things in a way that is common to nearly everyone and can expected from nearly everyone without the need for debate. A system that can beat the best players in the game Go, doesn't know what a glass is and that if a glass is broken you shouldn't pour water in it.

### What do you mean by "Reasoning about Visual Intuitive Physics"?
We want to develop models that can reason about the physical world from visual perspective, i.e. models that can simulate what our common sense tells us about the physical world based on what we have seen or are seeing. 

 - As a very simple example, we know that if if a ball rolls behind some sort of an occluder, we intuitively know the ball is still there even though we don't see it (Object-permanence). 
 - We also know that, the thing that will come out rolling will still be a ball; it won't suddenly turn into die or cone or some other shape (Shape constancy)
 - Based on the speed of the ball we know when we can expect to see the ball coming out of behind the occluder and we know the ball cannot teleport itself and arrive faster han expected (Spatio-Temporal continuity). 

 These things are pretty intuitive for us humans and we don't even think about them, but are very difficult for machines. 


### Woah, but even a baby knows that?!
Yep. Actually, developmental psychology researchers have been working hard and experimenting with babies with years, to find that babies of only a couple of months show signs of common sense in certain areas. For example, if one makes a trick where an object disappears in front of a baby, the baby will stare at the scene for longer amount of time, indicating surprise, due to Violation of Expectation (VoE). The looking time has been used as a measure of "surprise" in babies. In our model we draw from developmental psychology and output a surprise score for a video, indicating the amount of surprise in a video. A plausible video should ideally get 0 surprise.
<br>
<img src="/files/surprised.jpg" width="100%" height="auto" display="block">
<br>

### So is common sense all about making sense of what you see?
Nope! It's not all about vision. After all, if it were all about vision, what would blind people do? They do have common sense, that much we know. Common sense manifests itself in many ways in human's life. As an example, think about language and the sentences we utter. If I say "I went to the restaurant on February 4th", our common sense would tell us that we probably went in the restaurant on the date February 4th. However, what if there is a street called February 4th and the restaurant is on that street? This is gramatically possible option, but much less likely. For us it was easy, but machines really wouldn't know unless we factor in some sort of common sense model.

### Interesting, where can I read more?
Some useful links:

- [Paper: Machine Common Sense Concept paper](https://arxiv.org/pdf/1810.07528.pdf)
- [Paper: one particular approach of modeling VoE](http://physadept.csail.mit.edu/papers/adept.pdf)
- [Paper: for the dataset](https://arxiv.org/pdf/1803.07616.pdf).
- [Website: Description and access to dataset](https://intphys.com/)


