---
title:  "Adding Stats to The Doomsday Game"
categories: doomsday_game
tags: games math python
layout: post
---

[Last time]({% post_url 2021-11-18-doomsday-algorithm-game %}), we created a game for getting better at using the Doomsday Algorithm. After trying the game out a few times, I realized that there were a few simple but useful improvements I could make:
1. **Allow the user to make several guesses before ending the game and printing the answer**
2. Use only dates in the current calendar to keep the doomsday fixed (at least in easy mode).
3. Use a file to record the number of games and wins to allow a success rate to be printed
4. Add game modes that can be chosen with an argument

**We'll focus on the first improvement in this post and the other three in future posts.** 

**All of the code for this project is available on [GitHub](https://github.com/joshcwinton/doomsday-game/).**

## Making multiple guesses
For starters, we'll give the user three guesses to get the correct answer. Later on, we'll change this to a default that can be overridden with a command line argument.

To implement this concept, we'll place the logic that handles the guessing and checking inside a `while` loop that will repeat as long as the maximum number of guesses hasn't been reached.

```python
def main():
    random_date = randomDate()
    weekday = getDayOfWeek(random_date)
    print(random_date)
    
    max_guesses = 3
    guesses = 0
    
    # Allows multiple guesses
    while guesses < max_guesses:
        # Print message and read input
        guess = input("Make a guess: ")

        # Check whether the answer is correct
        if(guess.lower() == weekday.lower()):
            print("Correct!")
            return
        else:
            print("Incorrect!")
            guesses += 1

    print("Game over!")
```

In this code, the user can guess up to three times before the loop terminates and they see the `"Game over!"` message. If the user gets the answer right before that, the program exits.


### Adding arguments
To add arguments, we can use the `argparser` module. I've added the argument `--max_guesses` using the `ArgumentParser.add_argument()` function. The default value is three, which is the number of guesses the user will be able to make when `--max_guesses` is not specified.

```python
def main():
    parser = argparse.ArgumentParser(description='Guess the weekday for a random date')
    parser.add_argument('--max_guesses', default=3, dest='max_guesses', type=int)
    args = parser.parse_args()
    
    random_date = randomDate()
    weekday = getDayOfWeek(random_date)
    print(random_date)
    
    max_guesses = args.max_guesses
    ...
```

Now we can make the game sudden death by starting it with `python3 main.py --max_guesses=1` or make it more lenient using `--max_guesses=100` (even though I'd probably give up if it took that long 😅).  

**Next time, we'll look at how we can add a feature that keeps track of game statistics.**