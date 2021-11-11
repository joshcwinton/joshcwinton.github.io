---
title:  "Making a Jamaican Creole Dictionary"
categories: jamaican-creole 
tags: jamaican-creole
layout: post
---
Recently there has been an ongoing effort by several groups to legitimize [Jamaican Creole]() as an official language. These efforts have included the translation of the New Testament of the Bible into that language. Since I'm involved with a group that has adopted and is actively promoting the use of Jamaican Creole, I decided that it might be useful to create a "dictionary" by compiling a list of all the words that appear in this translation. This post describes the process I went through to achieve this as well as the code that I wrote, which is in [this](https://github.com/joshcwinton/jcr-dictionary) repository on GitHub.

# Prerequisites
## [Di Jamiekan Nyuu Testiment](https://www.bible.com/versions/476-jnt-di-jamiekan-nyuu-testiment)
I wasn't able to find a plain text copy of this translation so part of this project involved reading the text off of the [bible.com website](https://bible.com), where the translation is freely available. I've made the text I parsed [available](https://github.com/joshcwinton/jcr-dictionary/blob/main/JCR_words) in the repository for this project.

## Python
I chose to use Python for this project because I felt that it lends itself to working with text.
### [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
I used BeautifulSoup to work with the HTML that I read from bible.com. It can be installed with pip using `python3 -m pip install bs4`.

# Getting Started

