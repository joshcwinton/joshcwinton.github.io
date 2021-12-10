---
title:  "Adding Stats to The Doomsday Game"
categories: games
tags: games math python
layout: post
---

[Last time]({% post_url 2021-11-18-doomsday-algorithm-game %}), we created a game for getting better at using the doomsday algorithm. In this post, we'll look at ways to improve on this basic idea by keeping track of some relevant statistics. The statistics we'll look at today are:

1. Games played
2. Games won

We'll approach this task by writing these numbers to a JSON file. The JSON file format is easy to work with because it can represent a variety of data. 

To implement this idea, we'll write two functions: one that creates the results file if none exists and one that reads an existing results file and updates it with any new results.

Let's start by looking at how to create this file:
```python
def createResultsFile():
    results = {'games': 0, 'wins': 0}
    with open('results.json', 'w') as f:
        f.write(json.dumps(results))
```
 While inside our program, we'll represent our results as a dictionary. For now, we'll just be keeping track of the total number of games and how many of those games were wins. Once the dictionary is created, we write it to a file in the JSON format. We'll only call this function when the file doesn't already exist, so it makes sense to start with zero games and zero wins.

 Next, we can write the function that reads and updates the results file when it does exist:
 ```python
def updateResultsFile(win):
    if not os.path.exists('results.json'):
        createResultsFile()

    with open('results.json', 'r') as f:
        results = json.load(f)

    with open('results.json', 'w') as f: 
        results['games'] += 1
        if win:
            results['wins'] += 1
        f.write(json.dumps(results))
    win_rate = results['wins']/results['games']
    print("Your win rate is {:2.0%}".format(win_rate))
 ```
 Note that we create a fresh results file whenever an existing one isn't found. 

 Once we are certain that a results file exists, we can read it back in with `json.load()` which automatically converts the JSON file back into the dictionary structure that we use in this program.

This function will only be called at the end of a game, so we can increment the number of the games. Then, we can check whether the last game was a win and if it was, increment the number of wins. After those two things are updated, we can write the file with the new data using the same method we used to create it. At this point, we can also show the user their updated win rate.

And that's it! We saw how to keep track of results from this game in JSON file. 

This may be the last post in the series, but if it isn't, next time we'll look at some more enhancements for this little game.