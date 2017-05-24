---
layout: post
title: Manipulating text with pandas regex
permalink: pandas-regex
---

Whilst travelling home on the train yesterday, a colleague of mine
sent over an interesting challenge. He had a large number of survey
responses in which rail passengers had typed-in their rail
journeys. The responses were completely unstructured and I saw an
opportunity to deploy the full power of pandas for string
manipulation.

Grab the code for this post from my [Jupyter notebook](https://s3-eu-west-1.amazonaws.com/shared-notebooks/string-extract.ipynb).

## Pandas regex extract to create columns

One of many great things about the pandas project is the extensive
documentation. While browsing the string functions I found [this
entry](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.str.extract.html)
on the `str.extract()` function. It turns out that named groups are
extracted and converted into new columns, which is useful in all sorts
of ways.

In my experience, regex is a great place to start for even the most
complex text data challenges (yes, before those off-the-shelf NLP
solutions). For example, jupyter produced the following logs on the
command line:


``` markdown
[I 11:43:06.097 NotebookApp] 0 active kernels
[I 11:43:06.097 NotebookApp] The Jupyter Notebook i...
[W 11:43:06.509 NotebookApp] /path/to/some/file doe...
[I 11:43:19.053 NotebookApp] Kernel started: b3a285...
```

I just copied this from my terminal and created a one-column DataFrame like this:

| | logs |
|---|---|
| **0** | [I 11:43:06.097 NotebookApp] 0 active kernels |
| **1** | [I 11:43:06.097 NotebookApp] The Jupyter Noteb... |
| **2** | [W 11:43:06.509 NotebookApp] /path/to/some/fil... |
| **3** | [I 11:43:19.053 NotebookApp] Kernel started: b... |

Now we might want to individually extract the level, timestamp, and
message for each of the log entries. We tage each group with
`(?P<name>)` - these will be the titles of the columns. This regex:

``` python
df.logs.str.extract(
    r"\[(?P<level>\w)(?P<timestamp>\d*\:\d*\:\d*\.\d*)"
    r" (?:NotebookApp]) (?P<message>.*)",
    expand=True)
```

outputted our extracted information as a new DataFrame:

|  | level | timestamp| message|
|---|---|---|---|
| **0** |     I |11:43:06.097| 0 active kernels|
| **1** |     I | 11:43:06.097| The Jupyter Notebook is running at: http://loc...|
| **2** |     W | 11:43:06.509| /path/to/some/file doesn't exist|
| **3** |     I | 11:43:19.053| Kernel started: b3a28561-eb8a-43a2-8c25-f08e02..|

## Train journey survey data

Back to my train journey of analysing train journeys. The survey had
asked passengers to describe their journey in an unstructured text
field. Here's a preview of the DataFrame:

|  | journey |
|---|---|
| **0** | Winchester & London Waterloo |
| **1** | brighton-east croydon |
| **2** | Vauxhall to Redhill (via Clapham junction) |
| **3** | EUS - BHM |

A few challenges here: station codes, bigram destinations. Let's start
with something similar to above to separate out the destinations in
everybody's journey. This time we'll use different
delimiters (such as `to` or `&`) to separate out the different
destinations:

``` python
extracted = df.journey.str.extract(
    r"(?P<start>.*?)\W*(?:-|\bto\b|\band\b|&)\W*(?P<endvia>.*)",
    expand=True)
extracted = extracted.endvia.str.extract(
    r"^(?P<end>.*?)\W*(?:chan(?:ing|e) at|via|$)\W*(?P<via>.*)",
    expand=True)
df = df.join(extracted)
```

|  | journey | start | end | via |
|---|---|---|---|---|
| **0** | Winchester & London W... | Winchester | London Waterloo||
| **1** | brighton-east croydon | brighton | east croydon | |
| **2** | Vauxhall to Redhill (... | Vauxhall | Redhill | Clapham Junction)|
| **3** | EUS - BHM | EUS | BHM | |

I was really impressed with this feature in pandas. It allowed me to
create my start and end columns and, after some string normalisation,
I could `groupby('start').size()` to see statistics. Pandas + regexp
is a great tool for data scientists getting hands on with text data.

**[You can grab the notebook here](https://s3-eu-west-1.amazonaws.com/shared-notebooks/string-extract.ipynb)**
