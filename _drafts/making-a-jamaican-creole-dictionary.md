---
title:  "Making a Jamaican Creole Dictionary"
categories: jamaican-creole 
tags: jamaican-creole text-processing scraping python 
layout: post
---
Recently there has been an ongoing effort by several groups to legitimize [Jamaican Creole](https://en.wikipedia.org/wiki/Jamaican_Patois) as an official language. These efforts have included the translation of the New Testament of the Bible into that language. Since I'm involved with a group that has adopted and is actively promoting the use of Jamaican Creole, I decided that it might be useful to create a "dictionary" by compiling a list of all the words that appear in this translation. This post describes the process I went through to achieve this as well as the code that I wrote, which is in [this](https://github.com/joshcwinton/jcr-dictionary) repository on GitHub. The repository also includes a [colab notebook](https://colab.research.google.com/drive/1ilM7uNOo9euFMYF3k-0MAWvNX7jWGlR1?usp=sharing) where you can follow along.

## Prerequisites
### [Di Jamiekan Nyuu Testiment](https://www.bible.com/versions/476-jnt-di-jamiekan-nyuu-testiment)
I wasn't able to find a plain text copy of this translation so part of this project involved reading the text off of the [bible.com website](https://bible.com), where the translation is freely available. I've made the text I parsed [available](https://github.com/joshcwinton/jcr-dictionary/blob/main/JCR_words) in the repository for this project.

### Python
I chose to use Python for this project because I felt that it lends itself to working with text.
#### [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
I used BeautifulSoup to work with the HTML that I read from bible.com. It can be installed with pip using `python3 -m pip install bs4`.

## Getting Started
The bulk of this project involves simply getting the raw text from bible.com. To achieve this I used [`urllib.request`](https://docs.python.org/3/library/urllib.request.html#module-urllib.request) which provides an interface for creating an HTTP request. 

To actually build the request, I needed to be able to request each chapter of each book of the text individually because the site displays each chapter as a page. Since I couldn't think of a better way to determine which chapters should be requested, I stored the number of chapters in each book in a dictionary that maps the abbreviation of the book name that bible.com uses in its url to the number of chapters it has. I could then interpolate the url with the abrreviated book name and each chapter to make the request. The snippet below shows how to make the request for the first chapter of Di Jamiekan Nyuu Testiment:

```python
from urllib.request import Request, urlopen

book = 'MAT'
chapter = 1

url = f"https://www.bible.com/bible/476/{book}.{str(chapter)}.JNT"
req = Request(url, headers={'User-Agent': 'Mozilla/5.0'})
page = urlopen(req).read()
```

For the rest of this post, I'll continue with this book and chapter but the process can be generalized by iterating over the key, value pairs in the dictionary described above and then making a request for each chapter from chapter 1 to the number of chapters in that book.

Now that we have made the request and stored the response in `page`, we can begin to use BeautifulSoup to work with the HTML. At the moment, page is just a soup (😉) of characters, so BeautifulSoup will be really handy for making sense of it. 

The first step in parsing will be to create a `BeautifulSoup` object from the HTML string, which we'll call `soup`. This will then provide us with some useful functions for finding elements in the HTML. 

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(page, 'html.parser')
```

After some quick inspect-elementing on bible.com, I realized that every verse was wrapped in a `span` element with the class `_content`. There were also some blank elements that appeared with this structure, but since they were blank anyway, I just ignored them.

To select these elements, we use the `soup`'s [`find_all`](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find-all) function which takes a tag name and some properties and returns a list of elements that match those criteria.

```python
verse_elems = soup.find_all('span', class_='content')
```

We can now iterate over each of these elements and get their inner text using the `.text` property of the BeautifulSoup `Tag` object, which is how the HTML elements are represented.

```python
verses = [verse.text for verse in verse_elems]
```

Now that we have the text of each verse, we can split it into words and add them to our array[^1]. I used the [`filter`](https://docs.python.org/3/library/functions.html#filter) function to only include alphanumeric characters in the words I added and the `.lower()` function to make sure that capitalized words weren't counted seperately.

```python
words = []

for verse in verses:
    for word in verse.split():
        words.append(''.join(filter(str.isalnum, word.lower())))
```

Finally, to get the full list, I can cast the `words` list to a `Set` and then back to a list to remove duplicates and then use `sorted` to put the words in alphabetical order.

```python
words = sorted(list(set(words)))
```

We can check how many words were found using `len(words)` (I got 169).

With the list finalized, we can now write it to a file, which will have each word on a line:

```python
with open('JCR_words.txt', 'w') as f:
    for word in words:
        f.write(word + "\n")
```

And that's it! We now have a list of all words in Matyu 1 and we can generalize it to all books and chapters.

In my next post, I'll go through how we can use the generated text file to create a frequency count and index.

[^1]: I definitely could have used a set here but I used an array because I wanted to preserve word frequencies for the time being.