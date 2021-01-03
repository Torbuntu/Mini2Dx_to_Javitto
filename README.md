# Mini2Dx_to_Javitto
A primer on transitioning from Mini2Dx development to Javitto on Pokitto.

Sections are update as information is learned. 

## Contents
- [Introduction](#introduction)
  - [Mini2Dx](#mini2dx)
  - [Javitto](#javitto)
- [Major Differences](#major-differences)
  - [Javitto is not 'java'](#javitto-is-not-java)
  - [Build System](#build-system)
- [Getting Started Links](#getting-started)
- [Porting Brick Breaker Sample](#porting-brick-breaker)
  - [Background](#background)
  - [Paddle](#paddle)

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

#### Getting Started

[Mini2Dx Downloads](https://mini2dx.org/downloads.html) - this will have a project generator tool. This requires of course the use of a JDK to use, I suggest using [sdkman](https://sdkman.io/) and using it to grab adoptopenjdk. With the project generated, you can follow the setup guide for using an IDE (such as intellij, eclipse or my favorite Apache NetBeans). Other options are of course available, such as Vim and using gradle cli ;) 

[FemtoIDE Downloads](https://github.com/felipemanga/FemtoIDE/releases) - Download the correct release for your OS then follow the [installation instructions](https://github.com/felipemanga/FemtoIDE/wiki/Installation). Once FemtoIDE is running, select New Project, and from the Template select 4 - Java Game. This will then setup and open your new Javitto project in FemtoIDE with a basic demo setup. The wiki has a lot of other information for working with FemtoIDE, but I also recommend maybe even moreso the [Pokitto Forums](https://talk.pokitto.com/t/tool-femtoide)


#### Porting Brick Breaker

Mini2Dx has a collection of sample projects for folks to see the framework in action. Here we will port [Brick Breaker Sample](https://github.com/mini2Dx/mini2Dx-sample-breakout) to Javitto.

Start by making a new Javitto project. I'm calling mine BrickPort. I also went ahead and cleared out the template to the most basic pieces:

```Java
import femto.mode.HiRes16Color;
import femto.Game;
import femto.State;
import femto.input.Button;
import femto.palette.Psygnosia;
import femto.font.TIC80;

class Main extends State {

    // the screenmode we want to draw with
    HiRes16Color screen; 

    // start the game using Main as the initial state
    // and TIC80 as the menu's font
    public static void main(String[] args){
        Game.run( TIC80.font(), new Main() );
    }
    
    // Avoid allocation in a State's constructor.
    // Allocate on init instead.
    void init(){
        screen = new HiRes16Color(Psygnosia.palette(), TIC80.font());
    }
    
    // Might help in certain situations
    void shutdown(){
        screen = null;
    }
    
    // update is called by femto.Game every frame
    void update(){
        // clear the screen with the first color from the palette
        screen.clear(0);
        
        // Update the screen with everything that was drawn
        screen.flush();
    }
}

```
I also went and just removed the files for Dog and Background, leaving only Main.java and project.json. 

###### Background

Looking in the Mini2Dx Brick Breaker's project, the majority of the game code is inside the Core subproject folder. From there, let us start with the `Background.java` file, since it is at the top and easy to work from.

In our own project, make a `Wall.java` file. This will be the same as the Background.java from Mini2Dx Brick Breaker but we call it Wall to not conflict with the png file we will copy over. All this file will do is draw the cool brick background for the game field. 

For this to work, we will need to copy over the asset `background.png` from the Breakout assets directory into our project's root directory. For making things easier, also make sure to name it with an upper case B - `Background.png`. 

Notice however that this file is actually quite massive and high quality. On the little Pokitto this will take up pretty substantial space. So using an editor like Aseprite (or LibreSprite) let's knock that down. I decided on 32x32 to make sure it didn't lose too much detail. 

Then we add the Javitto code to `Wall.java`:
```Java
import femto.mode.HiRes16Color;

class Wall {
    // wall dimentions: 32x32
    Background background = new Background();
    
    void draw(HiRes16Color screen){
        for(int x = 0; x < 220; x+=32){
            for(int y = 0; y < 176; y+=32){
                background.draw(screen, x, y);
            }
        }
    }
}
```
We import the screen mode from Femto, and then instantiate our Background asset. Notice that we don't actually have a java class for Background. FemtoIDE generates that automatically using the Background.png image! Very awesome ;) I simplified the rendering logic quite a bit as well, using a number of hard coded values. In Pokitto programming, trying to save space becomes important with larger games. So reducing things to the bare minimum can be helpful. However, don't focus on preemptive optimization. 

With this class ready, let's add it to our Main.java to see how it works out:
```Java
import femto.mode.HiRes16Color;
import femto.Game;
import femto.State;
import femto.input.Button;
import femto.palette.Psygnosia;
import femto.font.TIC80;

class Main extends State {

    // the screenmode we want to draw with
    HiRes16Color screen; 
    
    Wall wall;

    // start the game using Main as the initial state
    // and TIC80 as the menu's font
    public static void main(String[] args){
        Game.run( TIC80.font(), new Main() );
    }
    
    // Avoid allocation in a State's constructor.
    // Allocate on init instead.
    void init(){
        screen = new HiRes16Color(Psygnosia.palette(), TIC80.font());
        wall = new Wall();
    }
    
    // Might help in certain situations
    void shutdown(){
        screen = null;
    }
    
    // update is called by femto.Game every frame
    void update(){
        // clear the screen with the first color from the palette
        screen.clear(0);
        wall.draw(screen);
        // Update the screen with everything that was drawn
        screen.flush();
    }
}
```
Running this we should now have just a simple brick background. Awesome! 

###### Paddle

Similar to the background bricks, we need to copy over `paddle.png`. Again, upper casing it to `Paddle.png` and srhinking it down in a pixel editor. In Aseprite I shrank it to 35%. 

I called the class for this handler `Player.java`. 
```Java
import femto.mode.HiRes16Color;
import femto.input.Button;

class Player {
    Paddle paddle;
    int x, y;
    
    Player(){
        paddle = new Paddle();
        reset();
    }
    
    void update(){
        if(Button.Left.isPressed()){
            if(x > 1) x-=2;
        }
        
        if(Button.Right.isPressed()){
            if(x < 218-paddle.width())x+=2;
        }
    }
    
    void draw(HiRes16Color screen){
        paddle.draw(screen, x, y);
    }

    // Simple bounding box collision method
    boolean collides(float x2, float y2, float w2, float h2){
        return (x < x2 + w2 && x + paddle.width() > x2 && y < y2 + h2 && y + paddle.height() > y2);
    }
    
    void reset(){
        // Center on screen
        x = 110-paddle.width()/2;
        // Height less 10
        y = 166; 
    }
}
```
Simply uses the Paddle sprite with some input and drawing positions for now. Instead of returning a collision box, we added a custom bounding box collides method. 

Back in Main.java we add the Player object
```Java
import femto.mode.HiRes16Color;
import femto.Game;
import femto.State;
import femto.input.Button;
import femto.palette.Psygnosia;
import femto.font.TIC80;

class Main extends State {

    // the screenmode we want to draw with
    HiRes16Color screen; 
    
    Wall wall;
    Player player;

    // start the game using Main as the initial state
    // and TIC80 as the menu's font
    public static void main(String[] args){
        Game.run( TIC80.font(), new Main() );
    }
    
    // Avoid allocation in a State's constructor.
    // Allocate on init instead.
    void init(){
        screen = new HiRes16Color(Psygnosia.palette(), TIC80.font());
        wall = new Wall();
        player = new Player();
    }
    
    // Might help in certain situations
    void shutdown(){
        screen = null;
    }
    
    // update is called by femto.Game every frame
    void update(){
        // clear the screen with the first color from the palette
        screen.clear(0);
        
        player.update();
        
        
        
        wall.draw(screen);
        player.draw(screen);
        
        // Update the screen with everything that was drawn
        screen.flush();
    }
}
```
Notice I do update first, and then draw the player (paddle) after drawing the wall. This way the paddle shows up on top of the wall graphics. 

# TODO: finish porting brick breaker
