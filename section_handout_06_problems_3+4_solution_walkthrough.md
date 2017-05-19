# Section Handout 06: Problems 3 and 4 Walkthrough

## Getting Started: Understanding the Critters Assignment

First things first. Read through (at least) the first two pages of [the assignment](https://web.stanford.edu/class/cs106a/homework/6-critters/spec.pdf), paying attention to how the program works, and what exactly you are being asked to implement. A high-level view of the program is this:

* There are a bunch of `Critter`s in a world
* Each type of `Critter` has distinctive features (color, movement pattern, etc.)
* `Critter`s eat when they find food, but only if they are hungry
* If two `Critter`s collide, they fight according to rules specific to each `Critter` type

For clarification: you are *not* expected to write any of the code that actually drives the program. All you need to do is fill in the classes for each of the different types of `Critter`s. These classes will be called by the pre-written `run()` method. Basically, on each animation frame, the `run()` method will loop through all `Critter`s on the screen, asking each one how it should be drawn, how to move it, and so on. Thus, each `Critter` should keep track of its own characteristics such as color, `String` representation (how it's drawn), and what its next movement should be.

## Problem 3: `Rabbit` SETUP

For this problem, we are instructed to define a `Critter` class named `Rabbit`, which must obey the following set of behavior patterns (which we must implement):

* **constructor:** `public Rabbit()`
    * The constructor is a special method that every object class has. It's what gets called when you write something like `GLine l = new GLine();` (or more relevantly, when you write something like `Rabbit r = new Rabbit();`). The constructor's job is to initialize any field variables belonging to the class to their default values.
* **color:** dark gray (`Color.DARK_GRAY`)
    * This is the color that a `Rabbit` object must return when the driver asks it what color it is, so that it can be drawn appropriately.
        * Accessed via `getColor()`
* **eating behavior:** alternates between `true` and `false` (`true`, `false`, `true`, ...)
    * Every time the `eat()` method is called, the `Rabbit` must return a `true` or `false`, alternating each time it is called.
        * Accessed via `eat()`
* **fighting behavior:** if opponent is a `Bird` or `Vulture`, then scratch; otherwise, roar
    * If a `Rabbit` collides with another Critter, it must return an attack specific to the type of `Critter` it is fighting.
        * Accessed via `fight(String opponent)`
* **movement behavior:** hops in an L-shaped pattern: first north twice, then south twice, then east twice, and repeat
    * When the driver asks the `Rabbit` how to move, the `Rabbit` should return a direction. Notice that this does not mean that every time a `Rabbit` moves, it goes up twice, down twice, and right twice. Rather, it means that the first and second times the driver calls a specific `Rabbit` object, it should move up one unit; the third and fourth times, it should move down one unit; the fifth and sixth times, it should move right one unit. Beyond this, the pattern should repeat.
        * Accessed via `getMove()`
* **toString:** `“R”`
    * This is the `String` that the driver will receive when it asks a `Rabbit` how it should be drawn. It is how you would tell a `Rabbit` apart from, say, a `Bird` or an `Ant`.
        * Accessed via `toString()`

Thus, we must implement a total of 6 methods: `public Rabbit()`, `public Color getColor()`, `public boolean eat()`, `public Attack fight(String opponent)`, `public Direction getMove()`, and `public String toString()`. Let's get started!

## Problem 3: `Rabbit` CODE

### Step 1: Class

First, let's create the `Rabbit` class, making sure that it's an extension of the `Critter` class.

```
public class Rabbit extends Critter {

}
```

Since `Rabbit extends Critter`, every `Rabbit` automatically has all of the properties and characteristics of a default `Critter`. This is a concept we call **inheritance**: The **parent class** `Critter` “passes on” its properties to its **child class** `Rabbit`.

By default, a `Critter` has all of the same methods as a `Rabbit`, but each method has a boring default behavior. It never eats (`eat()` always returns `false`), always forfeits fights (`fight()` always returns `Attack.FORFEIT`), is colored black (`getColor()` returns `Color.BLACK`), never moves (`getMove()` always returns `Direction.CENTER`), and is represented as a question mark (`toString()` returns `“?”`).

If we wanted to, we could leave these methods as they are, and our `Rabbit` would have all of the same characteristics. However, we want our `Rabbit` to follow a different set of behavior rules, so we will **override** the default `Critter` characteristics by rewriting all of the methods.

You may be wondering why we bother to make `Rabbit` extend `Critter` at all, if we're planning to override all of its methods. The answer is that if we do it this way, then we can make a whole bunch of `Critter`s that have the same method names, but implement those methods differently. So all `Rabbits` and `Ants` have `eat()` and `fight()` methods (as well as some other methods), but they each do them differently. The coolest part is that since they're both under the umbrella of `Critter`s, we can group them together and call their `eat()` and `fight()` methods, and each of them will respond to those calls in their own unique way. This is what class inheritance is all about!

### Step 2: Constructor

Our next step is to create the constructor for our `Rabbit` class. The syntax for constructors is always the same.

```
public class Rabbit extends Critter {
    public Rabbit() {
        // We'll fill this in soon!
    }
}
```

We'll leave the constructor empty for now, because we don't have anything to initialize yet.

*Note: Sometimes constructors don't actually have to initialize anything, in which case we wouldn't have to write it out at all. However, we will use ours soon, so I'm putting it here for later.*

### Step 3: Color

Let's first write our `getColor()` method. It should return the color `Color.DARK_GRAY`, so its return type has to be a `Color` object.

```
public class Rabbit extends Critter {
    public Rabbit() {
        // We'll fill this in soon!
    }
    
    public Color getColor() {
        return Color.DARK_GRAY;
    }
}
```

And that's it for color!

### Step 4: Eat

Remember that our `eat()` method needs to alternate between returning `true` or `false` each time it's called. This means that our `Rabbit` class needs a way to keep track of whether or not it is hungry at any given point. Let's create a `boolean` field variable called `hungry`, which will do just that for us. We'll use our constructor to initialize `hungry` to `false`!

```
public class Rabbit extends Critter {
    private boolean hungry;
    
    public Rabbit() {
        hungry = false;
    }
    
    // getColor() code...
}
```

*Note: The constructor is only called when the `Rabbit` is first created, so the constructor will only set `hungry` to `false` once, and then never get in the way again.*

We now create our `eat()` method, which flips `hungry` so that it alternates every time it is called, and then returns `hungry` to whatever called it.

```
public class Rabbit extends Critter {
    private boolean hungry;
    
    public Rabbit() {
        hungry = false;
    }
    
    // getColor() code...
    
    public boolean eat() {
        hungry = !hungry;
        return hungry;
    }
}
```

*Note: You may think that it would be more logical to initialize `hungry` as `true`, and then return that before flipping it. However, notice that once we use the return statement, the method is done. Anything after the return statement will not be executed, so if we returned `hungry` before flipping, we would just never flip. Thus, we initialize `hungry` to false, and flip it before the very first time we return it so that the first time `eat()` is called, our `Rabbit` is hungry.*

### Step 5: To String

I'm going to go slightly out of the order we used earlier and implement the `toString()` method here, because things get a little more complicated afterwards. This method just needs to return the single letter representation of a `Rabbit` so that the driver knows how it should draw it in the window. The `String` representation of a `Rabbit` is the letter `“R”`, so we write our method accordingly.

```
public class Rabbit extends Critter {
    
    // earlier code...
    
    public String toString() {
        return "R";
    }
}
```

### Step 6: Fight

Our next step is to implement the `fight()` method, which takes in as a parameter a `String` representation of the `Critter` that it is fighting. It needs to return the type of attack that it will use against its opponent, which is determined by the identity of its opponent. If our `Rabbit` is fighting either a `Bird` or a `Vulture`, then we scratch; otherwise, we roar.

`Attack`s are referenced in a similar manner as `Color`s. We can return `Attack.SCRATCH`, `Attack.ROAR`, `Attack.POUNCE`, or `Attack.FORFEIT`.

If you take a look at the [assignment spec](https://web.stanford.edu/class/cs106a/homework/6-critters/spec.pdf), you'll find that `Bird`s and `Vulture`s are represented by the same `String`s (`"^"`, `">"`, `"V"`, or `"<"`, depending on what their last move was). The only thing that distinguishes them visually is their color (`Bird`s are blue, `Vulture`s are black). Thus, we can check to see if our `Rabbit`'s opponent is a `Bird` or a `Vulture` by checking if it is a `"^"`, `">"`, `"V"`, or `"<"`, and attack accordingly.

```
public class Rabbit extends Critter {
    
    // earlier code...
    
    public Attack fight(String opponent) {
        if (opponent.equals("^") || opponent.equals(">") ||
            opponent.equals("V") || opponent.equals("<")) {
            return Attack.SCRATCH;
        } else {
            return Attack.ROAR;
        }
    }
}
```

### Step 7: Move

Finally, our `getMove()` method must return a direction each time it is called. This is the trickiest part of the problem, because remember that each time `getMove()` is called, it has to return the next direction in its pattern. Thus, it would be a great idea to use an `int` field variable to keep track of how many moves we have made at any given point. Let's create that variable, and use our constructor to initialize it to `0`.

```
public class Rabbit extends Critter {
    private boolean hungry;
    private int moves;
    
    public Rabbit() {
        hungry = false;
        moves = 0;
    }
    
    // other code...
}
```

Now, we realize that our `Rabbit`'s movement is a 6-step pattern: two steps up, two steps down, and two steps to the right. Thus, we can increment `moves` every time we take a step, and use its value to determine which direction we return. `Direction`s are referenced similarly to `Color`s and `Attack`s. We can return `Direction.NORTH`, `Direction.EAST`, `Direction.SOUTH`, `Direction.WEST`, or `Direction.CENTER`.

```
public class Rabbit extends Critter {
    private boolean hungry;
    private int moves;
    
    public Rabbit() {
        hungry = false;
        moves = 0;
    }
    
    // other code...
    
    public Direction getMove() {
        moves++;
        
        if (moves <= 2) {
            return Direction.NORTH;
        } else if (moves <= 4) {
            return Direction.SOUTH;
        } else {
            return Direction.EAST;
        }
    }
}
```

*Note: Similarly to when we flipped `hungry` before returning it, we must to increment `moves` before we return a `Direction`, because the method is over as soon as we hit a return statement.*

There is just one small issue left to fix: currently, the `Rabbit` will return `NORTH` the first two times, and `SOUTH` the next two times, but every call after that will return `EAST`. To fix this, we have to catch when `moves` hits `7`, and set it back to `1` (since after the sixth step, we would have to take the first step again).

```
public class Rabbit extends Critter {
    private boolean hungry;
    private int moves;
    
    public Rabbit() {
        hungry = false;
        moves = 0;
    }
    
    // other code...
    
    public Direction getMove() {
        moves++;
        
        if (moves > 6) {
            moves = 1;
        }
        
        if (moves <= 2) {
            return Direction.NORTH;
        } else if (moves <= 4) {
            return Direction.SOUTH;
        } else {
            return Direction.EAST;
        }
    }
}
```

And that's it! We are done with Problem 3 from the Section 06 Handout. Here is all of the code in one block for your convenience.

```
public class Rabbit extends Critter {
    private boolean hungry;
    private int moves;
    
    public Rabbit() {
        hungry = false;
        moves = 0;
    }
    
    public Color getColor() {
        return Color.DARK_GRAY;
    }
    
    public boolean eat() {
        hungry = !hungry;
        return hungry;
    }
    
    public String toString() {
        return "R";
    }
    
    public Attack fight(String opponent) {
        if (opponent.equals("^") || opponent.equals(">") ||
            opponent.equals("V") || opponent.equals("<")) {
            return Attack.SCRATCH;
        } else {
            return Attack.ROAR;
        }
    }
    
    public Direction getMove() {
        moves++;
        
        if (moves > 6) {
            moves = 1;
        }
        
        if (moves <= 2) {
            return Direction.NORTH;
        } else if (moves <= 4) {
            return Direction.SOUTH;
        } else {
            return Direction.EAST;
        }
    }
}
```

## Problem 4: `WhiteRabbit` SETUP

For this problem we must define a `Critter` class called `WhiteRabbit`. A `WhiteRabbit` is almost exactly the same as a `Rabbit`, with two exceptions:

* A `WhiteRabbit` is, well, white, instead of the dark gray of a normal `Rabbit`
* A `WhiteRabbit`'s movement pattern is 5 hops up, down, and to the right, instead of 2 like a normal `Rabbit`

So we could write up the entire `WhiteRabbit` class like we did for the `Rabbit` class, and tell it to do basically all of the same things that `Rabbit`s do, with these two exceptions. That would actually work perfectly well, but we would end up rewriting a lot of the exact same code. Instead, there's a much cooler way to do this!

## Problem 4: `WhiteRabbit` CODE

### Step 1: Class

Remember earlier, when we made `Rabbit` extend `Critter`, so that it inherited all of the `Critter` class's attributes? Well, this time we're going to do something similar, so that we don't have to rewrite all of the parts that are identical between `Rabbit` and `WhiteRabbit`: we're going to make `WhiteRabbit` extend `Rabbit`!

```
public class WhiteRabbit extends Rabbit {
    
}
```

This way, `WhiteRabbit` inherits everything that we already wrote for `Rabbit`, and all we have to do is override the two methods that are different. Wow, inheritance is great!

Fun fact: We mentioned earlier that `Rabbit` is a child class of `Critter`, but now `WhiteRabbit` is a child class of `Rabbit`. This means that `WhiteRabbit` is now a **grandchild class** of `Critter`!

### Step 2: Color

The first thing that's different between `Rabbit`s and `WhiteRabbit`s is their color. We need to override `Rabbit`'s `getColor()` method to return white instead of dark gray.

```
public class WhiteRabbit extends Rabbit {
    public Color getColor() {
        return Color.WHITE;
    }
}
```

And that's it for color! Notice that we don't have to do anything fancy to tell our code to use `WhiteRabbit`'s version of `getColor()` instead of `Rabbit`'s. Java knows to use the version that belongs to the “youngest generation” whenever it can.

### Step 3: Move

This one is slightly trickier, and to solve it, we need to back up a bit and make a small change to our `Rabbit` class. Recall what we did for `getMove()` back in `Rabbit`:

```
public class Rabbit extends Critter {
    
    // other code...
    
    public Direction getMove() {
        moves++;
        
        if (moves > 6) {
            moves = 1;
        }
        
        if (moves <= 2) {
            return Direction.NORTH;
        } else if (moves <= 4) {
            return Direction.SOUTH;
        } else {
            return Direction.EAST;
        }
    }
}
```

`Rabbit` had a 6-step pattern to its movement, but we know that it was split into three 2-step patterns (2 steps up, down, and to the right). Well, we now want our `WhiteRabbit` to follow a 15-step pattern, but it's really three 5-step patterns (5 steps up, down, and to the right). So instead of using hard-coded numbers inside those `if` statements, why don't we make the numbers specific to the type of `Rabbit` we're using? For a default `Rabbit`, the number is 2. But for a `WhiteRabbit`, the number needs to be 5. So let's make an extra method called `getHopSize()` that returns the appropriate number. So in `Rabbit`, we will add this method, and change `getMove()` to pull its numbers from that method instead.

```
public class Rabbit extends Critter {
    
    // other code...
    
    public Direction getMove() {
        moves++;
        
        if (moves > 3 * getHopSize()) {
            moves = 1;
        }
        
        if (moves <= getHopSize()) {
            return Direction.NORTH;
        } else if (moves <= 2 * getHopSize()) {
            return Direction.SOUTH;
        } else {
            return Direction.EAST;
        }
    }
    
    public int getHopSize() {
        return 2;
    }
}
```

Make sure that you understand this change completely before moving on!

Okay, now that we've changed `Rabbit` to get its “hop size” from a method, all we have to do in WhiteRabbit is override the `getHopSize()` method for its own personalized purposes.

```
public class WhiteRabbit extends Rabbit {
    public Color getColor() {
        return Color.WHITE;
    }
    
    public int getHopSize() {
        return 5;
    }
}
```

And that's it. We are done with `WhiteRabbit`!

## Closing

I hope these problems have helped you see how inheritance is super cool, and how extended classes can be really useful and time-saving when you're trying to create classes that are mostly the same but slightly different. The entire Critters assignment is just variations on these ideas, so if you really understood all this well, then you'll do great on the assignment.

Let me know if anything here was not explained clearly enough, and I'd be happy to fix things up.

Good luck, and have fun!
- Michael
