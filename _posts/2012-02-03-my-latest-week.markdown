---
layout: post
title: "My latest week"
category: experience
tags: [Objective-C, Statistics, Management, epiGenesys, iOS]
---
{% include JB/setup %}

This past week I've been working at epiGenesys Ltd. alongside [Dominic Wroblewski](http://domness.github.com "Dom's Blog") on a couple of iOS applications we had been developing during our Summer Internship there. The two projects were of completely different nature, with the Statistics application "SampSize" being an application designed to go hand-in-hand with an associated article for the [British Medical Journal](http://www.bmj.com/), whilst the other is a Survey application designed to be utilised in Hospitals to replace its paper form equivalent.

### SampSize

The Stastics application is one which suffered from naive adrenaline rushed implementation and postponed design choices and if to repeat the project; I would definitely attempt to spend more time attempting to develop an abstracted process for the handling of data throughout the application, which was a more complex and convoluted range of data to incorporate than their similar nature would lead you to believe. However, the project was not a disaster. We are now familiar with compiling external C-libraries for multiple device architectures (for both Simulator and Device), through the use of the [GNU Scientific Library](http://www.gnu.org/software/gsl/) and I am much more confident with the interpretation and implementation of mathematical functions in numerous languages.

There are several extensions on top of the GSL in order to calculate:

- CDF for a Non-central Students T distribution

- Superiority
  - Crossover and Parallel Trials
  - Normal and Binary Data
  - Sample Size and Power
- Non-Inferiority
  - Crossover and Parallel Trials
  - Normal and Binary Data
  - Sample Size and Power
- Equivalence
  - Crossover and Parallel Trials
  - Normal and Binary Data
  - Sample Size and Power
- Bioequivalence
  - Crossover and Parallel Trials
  - Normal Data
  - Sample Size and Power
- Precision
  - Crossover and Parallel Trials
  - Normal and Binary Data
  - Sample Size and Precision

I am quite proud of the calculations involved in these extensions, and would love to have open-sourced them after a minor clean-up, but alas it seems unlikely.

### Survey

The survey application was worked on primarily by Dom this week, as with a stronger mathematics background it made more sense for me to work on the stats app, before helping helping with this one. During the week we updated several aspects to utilise the latest iOS 5 APIs, re-wrote several features and functionalities to incorporate the latest specification changes, re-wrote the way data is handled within the app (to encrypt it when stored locally), and finally hooked it up to a different web-server setup to push data to.

I am quite proud of the outcome of the survey application, which has grown and evolved over several specification changes fluidly, adapting to new feature requirements and huge changes to the data handled by the application.
