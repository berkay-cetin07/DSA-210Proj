# DSA-210Proj
Introduction to Data Science Course Project

Sabanci University DSA210 Introduction to Data Science Course Fall 2024-2025 Term Project.
This project will be an analysis on my own Youtube usage.

## Motivation

The motivation behind this analysis is to gain a deeper understanding of my YouTube usage patterns. By examining my viewing habits via my watch history considering which days i watch more videos, and what time of the day i watch more videos, also how often do i engage with comments,and  which days and times i add videos to my Watch Later playlist. I aim to uncover meaningful insights about my online behavior and interests.


## Data Source

I have only one source of data:

Data is directly exported from Google Takeout, selecting YouTube ve YouTube Music data request.


## My Hypothesis

Number of comment i post and time i spent on youtube does not depend on weekend or weekdays and specific month of a year and also time of the day.



# Code

Importing some libraries:
```python
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
from bs4 import BeautifulSoup
```

To upload the youtube data into system:
```python
from google.colab import files

# Upload files
uploaded = files.upload()
```


