# Instructions
## Get your bearings
* Run the repl to get things started
* Open up `src/java/replit/Main.java`

<details open>
<summary>
Reminders:
</summary>

* The Main class creates an application window
* the `start()` method sets everything up and starts things running
* the block beginning with `new AnimationTimer() {` creates an anonymous inner class that is a subclass of AnimationTimer
    - its `handle(long curTime)` method is called once every frame to allow state to be updated and the display to be redrawn
    - instance variables of the anonymous class persist across calls of `handle()` but *local* variables defined inside `handle` disappear every time

</details>

## Solve the bounce bug
<details open><summary>Click to hide/show</summary>

* the code is intended to display a disc bouncing around the canvas, but it seems like some bounces don't work quite right, with the disc bouncing off-screen
* can you figure out what to change in the code so that the disc always bounces correctly

* Hints:
    1. <details><summary> What does bouncing actually mean? </summary>

        - the disc should "reverse direction" when its edge touches one of the edges of the canvas
        - if the disc bounces off the left/right edge of the canvas, its y-motion should stay the same, but its x-motion needs to switch direction
        - if the disc bounces off the top/bottom edge of the canvas, its x-motion should stay the same, but its y-motion needs to switch direction

        </details>
    2. <details><summary> javafx coordinate system</summary>

        - javafx uses an x,y coordinate system just like you're used to from math class **except** ***the numbers mean DIFFERENT things***
        - the top left corner is the origin (0,0) in javafx
        - as you move rightwards, the x-coordinate increases like normal, but as you move *downwards*, the y-coordinate *increases* (backwards from what you're used to)
        - `Main` has two static variables `CANVAS_WIDTH` and `CANVAS_HEIGHT` which do what they sound like
        - `gc.fillOval(x, y, radius*2, radius*2)` is giving the x,y-coordinate of the top left corner of the oval's **bounding box**, with the next two arguments corresponding to the width and height of the oval
        - a ***bounding box*** of a shape is a rectangular area that completely encloses some shape, i.e., the top edge of the bounding box just barely touches the highest point of the shape, the left edge of the bounding box just barely touches the left-most point of the shape, etc.

        </details>
    3. <details><summary> detecting edge collisions</summary>

        - since we're working with doubles while thinking of individual frames, it's important to remember that the disc is actually taking little tiny hops
        - we should expect the edge of disc to perfectly and exactly touch the edge of canvas
        - more likely: disc makes tiny hop over the edge of the canvas
        - we can detect if disc has hopped over edge by looking at coordinates of top-left corner of the bounding box (`x`, `y`)
        - for example, to detect hopping over the left edge of the canvas, we can ask if the left edge of the disc (the imaginary line with x-coordinate equal to the `x` variable) is left of the left edge of the screen (0) using something like `if (x < 0)`
        - when such a hop happens we should place the disc so that it's fully on the canvas (in this case, setting `x` to `0`) and then reverse the corresponding velocity (in this case, `xvel`)

        </details>
    4. <details><summary> thinking about the fix </summary>

        - notice that the problem is the bottom and right edge
        - it almost looks like the disc goes halfway off the canvas before hitting a wall
        - let's think about the right edge
        - to detect right edge bounce we want to know if the disc has "hopped" over the right edge, i.e., if the right edge of the disc is right of the right edge of the screen
        - recall that the imaginary line with x-coordinate equal to `CANVAS_WIDTH` should be the right edge of the screen
        - what is the right edge of the disc? it's certainly not the imaginary line with x-coordinate equal to `x` because that's the *left edge* of the disc!
        - how far from the left edge is the right edge?
        - the expression for the right edge should be `x + dist` where `dist` is the distance between left edge and right edge

        </details>
    5. <details><summary>Don't forget to adjust the position resetting</summary>

        - once you figure out what correct the `else if` condition should be, remember that this will also change what you set `x`/`y` to in the body of the `else if`
        - for example, we need to set `x` such that the right edge of disc is just barely touching right edge of canvas
        - if right edge of disc is `x + dist`, we want to make sure that equals `CANVAS_WIDTH`, i.e., `x + dist == CANVAS_WIDTH`
        - if we solve the equation above for `x` we find the value which we should set `x`

    </details>

</details>

## Make a Disc class
<details><summary>Click to hide/show</summary>  

1. <details><summary>  Make a class file</summary>  

    * open the files sidepane by clicking the Files button in the sidebar
    * click the `replit` folder
    * make a new file by clicking the folded-corner page with a plus-sign button towards the top right of the Files pane
    * name the file `Disc.java`
    </details>
2.  <details><summary> Imports</summary>

    * copy in the first several lines from `Main.java`. 
    * **Make sure you include the `package replit` line**

    </details>
3. <details><summary> class skeleton</summary>

    - fill out the first "real" line (not counting package/imports) in the normal way
    </details>
4. <details><summary> Instance variables </summary>

    - make instance variables for everything a disc will need to do its job (it needs to be able to bounce around and be drawn)
    </details>
5. <details><summary> Constructor</summary>

    - Write a constructor for Disc that accepts x,y, x-velocity, y-velocity, radius, and Color, in that order
    </details>
6. <details><summary> `update(double dt)` method</summary>

    - write an `update(double dt)` method that takes in a double corresponding to the amount of time that has passed since previous update (in seconds)
    - the method should "move" the disc by updating states of appropriate variables
    - don't forget the bounces!

    </details>
7. <details><summary> `draw(GraphicsComponent gc)`</summary>

    - write the `draw` method which draws the disc
    </details>
    


</details>

## Switch Main code to use `Disc` class
<details><summary>Click to hide/show</summary>

  * make a Disc instance variable in AnimationTimer and initialize it when it's declared
  * in the `handle()` method call `update` and `draw` on the Disc

</details>

## Add randomDisc() method to Main
<details><summary>Click to hide/show</summary>

  * This method should make a new random disc and ***return it***
  * remember that you can use the formula: `(int) (Math.random() * (max - min +1) + min)` to generate a random int between min and max (inclusive)
  * remember that `Color.rgb(r, g, b)` makes a color with RGB values `r,g,b` (between 0 and 255). For example, the reddest red is `Color.rgb(255, 0, 0)` while `Color.rgb(255,0,255)` is the purplest purple

</details>

## Make list of Disc objects in `Main.start()` using `randomDisc()`
<details><summary>Click to hide/show</summary>

  * Make an ArrayList of discs
  * Note that we need to make the ArrayList in the `start` method of `Main` *before* the handle method
  * use a for loop to fill the ArrayList with discs using the `randomDisc()` method
</details>

## Loop through list of Disc objects and call update and draw on each of them
<details><summary>Click to hide/show</summary>
    * loop through the ArrayList of discs
    * note that this loop must be *in* the `handle` method
    * for each disc, call `update` and then `draw`

</details>



# How to deal with errors
* if your program keeps crashing without running and you can't see anything wrong with code, try typing `mvn clean` in the *Console* tab. Then rerun
* If you get black screen with spinning replit logo, refresh
* Other than the first time running the repl (or the first time running after `mvn clean`), there should be a lot less output now, so finding errors should be easier
* look carefully for the actual error message and find the file and line number
* google `javafx BLAH` where BLAH is the error you got