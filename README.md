# Mini2Dx_to_Javitto
A primer on transitioning from Mini2Dx development to Javitto on Pokitto

## Contents
- [Introduction](#introduction)
  - [Mini2Dx](#mini2dx)
  - [Javitto](#javitto)
- [Major Differences](#major-differences)
  - [Javitto is not 'java'](#javitto-is-not-java)
  - [Build System](#build-system)

## Introduction
#### [Mini2Dx](https://mini2dx.org/) 
- Mini2Dx is an open-source beginner-friendly, master-ready framework for rapidly prototyping and building 2D games in Java

#### [Javitto](https://github.com/felipemanga/femtoide/wiki)
- Javitto is the name applied to using Java syntax to make games on [Pokitto](https://www.pokitto.com/). [FemtoIDE](https://talk.pokitto.com/t/tool-femtoide) is the tool used for making these types of games. 

The purpose of this primer is to help clarify some differences and similarities in the aim of making developing games on Pokitto much quicker with experience gained from using Mini2Dx. 


## Major Differences

I will document here some of the more key things that have come up in my journey to learn Javitto.

#### Javitto is not 'Java'
Java for Mini2Dx is actual Java, but in FemtoIDE using Javitto you don't use the JDK to develop games. Everything you need to program is included inside of FemtoIDE. This is because Javitto doesn't use the JDK, and is translated down to C++ and then compiled directly to Pokitto. So there is no JVM nor JDK involved. 

#### Build System

Mini2Dx uses a java build system to manage dependencies, compile, build and export games. 

FemtoIDE manages everything in-house. When you generate a java project in FemtoIDE in the root it will contain a `project.json` file which will behave similarly to the `build.gradle` in a Mini2Dx project. 


