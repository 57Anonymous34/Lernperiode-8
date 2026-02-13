---
title: Mein Tutorial
---

# Goal

In this tutorial, you will learn how to build a small arcade game using **Lua** and the **LÖVE2D** framework.

# Game Idea

You control a ball with your mouse cursor. Enemies spawn outside the screen and move toward you.  
If an enemy touches you, the game ends. The longer you survive, the higher your score gets.

# Previous Knowledge

We’ll assume you already know:

- Basic programming ideas (variables, functions, if-statements, loops)
- Basic file handling (creating files and folders)

You do **not** need to be a Lua expert yet — we will explain the important parts.

# What you'll learn

By the end, you’ll understand:

- How a LÖVE2D project is structured
- How the LÖVE callbacks (`love.load`, `love.update`, `love.draw`) work
- How to split your code into modules (`Enemy.lua`, `Button.lua`)
- How to detect collisions (distance check)
- How to create a simple menu and game-over screen

# Tutorial

In this section, we will explain the most important concepts used in this project and show how they work together.

We will focus on:
- The LÖVE2D game loop
- Game states (menu, running, game over)
- Player movement using the mouse
- Enemy behavior and collision detection

---

## The LÖVE2D Game Loop

LÖVE2D automatically calls specific functions while your game is running.

The three most important ones are:

```lua
function love.load()
end

function love.update(dt)
end

function love.draw()
end
```

## What do they do?

love.load()
- Runs once when the game starts.
- Used to load assets, create buttons, and initialize variables.

love.update(dt)
- Runs every frame.
- Used for game logic like movement, collisions, and scoring.
- dt (delta time) is the time passed since the last frame.

love.draw()
- Runs every frame.
- Used only for drawing graphics and text to the screen.


## Game States

Our game uses multiple states to control what happens on the screen:

- Menu
- Running (gameplay)
- Ended (game over)

We store them inside a table:


```lua
local game = {
    state = {
        menu = true,
        paused = false,
        running = false,
        ended = false
    }
}
```

To switch between states, we use a helper function:


```lua
local function changeGameState(state)
    game.state["menu"] = state == "menu"
    game.state["paused"] = state == "paused"
    game.state["running"] = state == "running"
    game.state["ended"] = state == "ended"
end
```

This ensures that only one state is active at a time.


## Player Movement (Mouse Controlled)

The player is controlled by the mouse cursor.

We store the player data like this:

```lua
local player = {
    radius = 20,
    x = 30,
    y = 30
}
```


Every frame, we update the player position:

```lua
player.x, player.y = love.mouse.getPosition()
```
This means the player circle always follows the mouse.


## Enemy Behavior

Enemies are created using a separate file (Enemy.lua).
Each enemy moves toward the player every frame.

Movement logic:

```lua
move = function (self, player_x, player_y)
    if player_x - self.x > 0 then
        self.x = self.x + self.level
    elseif player_x - self.x < 0 then
        self.x = self.x - self.level
    end

    if player_y - self.y > 0 then
        self.y = self.y + self.level
    elseif player_y - self.y < 0 then
        self.y = self.y - self.level
    end
end
```


The variable level controls how fast the enemy moves.


## Collision Detection

To check if the enemy touches the player, we calculate the distance between them:


```lua
checkTouched = function (self, player_x, player_y, cursor_radius)
    return math.sqrt((self.x - player_x)^2 + (self.y - player_y)^2) <= cursor_radius * 2
end

```

## Explanation

- We calculate the distance using the Pythagorean theorem
- If the distance is small enough, a collision occurs
- When a collision happens, the game switches to the ended state

## Code Example (Complete Interaction)

This snippet shows how everything works together inside love.update:


```lua
if game.state["running"] then
    for i = 1, #enemies do
        if enemies[i]:checkTouched(player.x, player.y, player.radius) then
            changeGameState("ended")
        else
            enemies[i]:move(player.x, player.y)
        end
    end

    game.points = game.points + dt
end

```



# Result

![Save the Ball gameplay](SaveBall.gif)




# What could go wrong?

If the game does not start, one possible reason is a syntax error in a Lua file.
Even a small typo can prevent the game from running.
