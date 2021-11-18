---
title:  "A Doomsday Algorithm Game"
categories: games
tags: games math python
layout: post
---
Something that I've been interested in for a long time but never got around to actually doing is learning the Doomsday algorithm, a method for mentally calculating the day of the week for any given date.

My curiosity was rekindled a few weeks ago when the Numberphile YouTube channel posted [a video]() explaining how the method works. While I was looking at the [Wikipedia page](), I found this quote:

> To improve his speed, he practiced his calendrical calculations on his computer, which was programmed to quiz him with random dates every time he logged on.

Apparently this was mentioned in a *Scientific American* article that, unfortunately, seems to have vanished from the internet.

I thought that an interesting project might be to try replicate that program that gives the user a date and asks them to respond with the day. I've decided to implement this program in Python.

## Reading input
For starters, I need to set up a way to send a message to the reader and to get a response back. This code prints the message "make a guess: " then waits for the user to type something and hit enter. After the user responds, the program prints back their response.

```python
def main():
    guess = input("make a guess: ")
    print(guess)
    

if __name__ == '__main__':
  main()
```

## Picking a Date
Next up is generating a random date to test the user with. Since some dates are harder than others, it makes sense to prompt the user for a difficulty at the beginning, which I'll do later on. For now, let's create a random date in the past year. I got some help with this from an [article on Kite](https://www.kite.com/python/answers/how-to-generate-a-random-date-between-two-dates-in-python).

This code sets a range starting with a date a year ago and ending today. It then determines the number of days in that range and picks a day by choosing a number between zero and that number. The actual date is determined by adding that number of days to the date a year ago.

```python
# returns a random date
def randomDate():
    end_date = datetime.date.today()
    start_date = end_date.replace(year=end_date.year-1)

    time_between_dates = end_date - start_date
    days_between_dates = time_between_dates.days
    random_number_of_days = random.randrange(days_between_dates)
    random_date = start_date + datetime.timedelta(days=random_number_of_days)
    print(random_date)

```

## Finding the Day
Now that we have a date, we need to determine what day of the week it was before we prompt the user. Thankfully, this is built into the `datetime.date` object under the `.weekday()` function.

```python
def main():
    random_date = randomDate()
    weekday = random_date.weekday()
    print(random_date, weekday)
```

The `weekday` function of `datetime.date` returns a number, so to make things easier, we can convert this number to a string using `date.strft()` (which means we really could have skipped using `date.weekday()`. I'm also going to move this logic into a function that takes the date object and returns the string representation of the weekday.

```python
def getDayOfWeek(date):
    return date.strftime('%A')
```
```python
def main():
    random_date = randomDate()
    weekday = getDayOfWeek(random_date)
    print(random_date, weekday)
...
```

## Checking the Answer
Now that we know what the correct answer is, all that's left to do is to check whether the user's answer is correct. To keep things simple, I'll make the correct answer and the user's answer lowercase before I convert them.

```python
def main():
    ...
    if(guess.lower() == weekday.lower()):
        print("Correct!")
    else:
        print("Incorrect!")
    if(guess.lower() == weekday.lower()):
```
And that's it for now! In my next post I'll introduce and implement some ideas for improvements.