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

Number of comment i post on youtube, add videos to watch-later and time i spent on youtube does not depend on weekend or weekdays and time of the day.



# Code

Importing some libraries:
```python
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
from bs4 import BeautifulSoup
import re
```

To upload the youtube data into system:
```python
from google.colab import files

# Upload files
uploaded = files.upload()
```


Clean the **comment** data from unwanted features and group them based on time of the day, day of the week, moth of the year:

```python
# Manually define the column names based on the comments format
columns = [
    "Comment_ID", "Channel_ID", "Timestamp", "Price",
    "Parent_Comment_ID", "Broadcast_ID", "Video_ID", "Comment_Text"
]

# Load the CSV with manual column names
comments_df = pd.read_csv("comments.csv", skiprows=1, names=columns, encoding="utf-8")

# Convert the 'Timestamp' column to datetime
comments_df['timestamp'] = pd.to_datetime(comments_df['Timestamp'])

# Add 'year', 'hour', and 'day' columns
comments_df['year'] = comments_df['timestamp'].dt.year
comments_df['hour'] = comments_df['timestamp'].dt.hour
comments_df['day'] = comments_df['timestamp'].dt.day_name()

# Group by hour
comments_by_hour = comments_df.groupby('hour').size()

# Group by day of the week
comments_by_day = comments_df.groupby('day').size().reindex(
    ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
)

# Save cleaned comments data
comments_df.to_csv("cleaned_comments.csv", index=False)
print("Cleaned comments data saved to 'cleaned_comments.csv'.")

```


Clean the **watch later** data from unwanted features and group them based on time of the day, day of the week, moth of the year:

```python

# Define column names for the watch-later format
columns = ["Video_ID", "Timestamp"]

# Load watch-later.csv with manually specified column names
watch_later_df = pd.read_csv("watch-later.csv", skiprows=1, names=columns, encoding="utf-8")

# Convert the 'Timestamp' column to datetime
watch_later_df['timestamp'] = pd.to_datetime(watch_later_df['Timestamp'])

# Add 'year', 'hour', and 'day' columns
watch_later_df['year'] = watch_later_df['timestamp'].dt.year
watch_later_df['hour'] = watch_later_df['timestamp'].dt.hour
watch_later_df['day'] = watch_later_df['timestamp'].dt.day_name()

# Group by hour
watch_later_by_hour = watch_later_df.groupby('hour').size()

# Group by day of the week
watch_later_by_day = watch_later_df.groupby('day').size().reindex(
    ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
)

# Save cleaned watch-later data
watch_later_df.to_csv("cleaned_watch_later.csv", index=False)
print("Cleaned watch-later data saved to 'cleaned_watch_later.csv'.")

```


Clean the **watch-history** data from unwanted features and group them based on time of the day, day of the week, moth of the year(also mapped them as a date data):

```python

# Mapping Turkish month abbreviations to English
month_map = {
    "Oca": "Jan", "Şub": "Feb", "Mar": "Mar", "Nis": "Apr", "May": "May", "Haz": "Jun",
    "Tem": "Jul", "Ağu": "Aug", "Eyl": "Sep", "Eki": "Oct", "Kas": "Nov", "Ara": "Dec"
}

# Read the file as plain text
with open("watch-history.html", "r", encoding="utf-8") as file:
    content = file.read()  # Read the entire file as a single string

# Regex to extract date strings
date_pattern = r"\b\d{1,2} \w{3} \d{4} \d{1,2}:\d{2}:\d{2}\b"

watch_history = []

# Search for all matches in the content
matches = re.findall(date_pattern, content)
print(f"Total matches found: {len(matches)}")  # Debugging: Print total matches

for date_string in matches:
    # Replace Turkish month abbreviation with English
    for turkish, english in month_map.items():
        date_string = date_string.replace(turkish, english)
    print(f"Mapped date string: {date_string}")  # Debugging: Print mapped date strings

    try:
        # Parse the date
        watch_time = datetime.strptime(date_string, "%d %b %Y %H:%M:%S")
        # Append year, hour, and day to the list
        watch_history.append({
            "date": watch_time,  # Full datetime
            "year": watch_time.year,
            "hour": watch_time.hour,
            "day": watch_time.strftime("%A")  # Full day name
        })
    except ValueError as e:
        print(f"Error parsing date: {date_string} - {e}")

# Create DataFrame from the processed data
watch_history_df = pd.DataFrame(watch_history)

# Display the DataFrame
print(watch_history_df.head())

# Save cleaned data to a CSV file
if not watch_history_df.empty:
    watch_history_df.to_csv("cleaned_watch_history.csv", index=False)
    print("Cleaned data saved to 'cleaned_watch_history.csv'.")
else:
    print("No valid dates found in the file.")

```

some example result for this code:
```python
Mapped date string: 17 Sep 2023 08:26:28
Mapped date string: 17 Sep 2023 08:26:21
Mapped date string: 17 Sep 2023 08:25:32
Mapped date string: 17 Sep 2023 08:25:07
```




Divide data by videos Watched by Time of Day:

```python

# Group by hour
time_of_day_grouped = watch_history_df.groupby('hour').size()

# Print the grouped data for verification
print("Videos Watched by Time of Day:")
print(time_of_day_grouped)



# Group by day of the week
day_of_week_grouped = watch_history_df.groupby('day').size()

# Ensure correct order of days
day_of_week_grouped = day_of_week_grouped.reindex(
    ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
)

# Print the grouped data for verification
print("Videos Watched by Day of the Week:")
print(day_of_week_grouped)

```


Output:

```python

0     3011
1     1145
2      443
3       88
4      253
5      213
6      368
7      757
8     1800
9     2832
10    3214
11    2084
12    2713
13    2587
14    2280
15    2601
16    2772
17    2809
18    2567
19    2252
20    2998
21    2715
22    3308
23    3890
dtype: int64
Videos Watched by Day of the Week:
day
Monday       6826
Tuesday      6690
Wednesday    7010
Thursday     7151
Friday       6875
Saturday     6413
Sunday       8735
dtype: int64

```




## Plot Histogram Videos Watched by Time of the Day


```python

import matplotlib.pyplot as plt

# Plot: Videos Watched by Time of Day
plt.figure(figsize=(12, 6))
time_of_day_grouped.plot(kind='bar', width=0.9, color='skyblue', edgecolor='black')
plt.title('Videos Watched by Time of Day', fontsize=16)
plt.xlabel('Hour of the Day', fontsize=14)
plt.ylabel('Number of Videos Watched', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```


![image](https://github.com/user-attachments/assets/346600b9-0a8d-4ee2-88e3-f9262fb35333)




## Plot Histogram Videos Watched by Day of the Week

```python

# Plot: Videos Watched by Day of the Week
plt.figure(figsize=(12, 6))
day_of_week_grouped.plot(kind='bar', color='orange', width=0.9, edgecolor='black')
plt.title('Videos Watched by Day of the Week', fontsize=16)
plt.xlabel('Day of the Week', fontsize=14)
plt.ylabel('Number of Videos Watched', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```


![image](https://github.com/user-attachments/assets/f8ad86f0-aaf0-4076-b163-b1373260f199)








## Plot Histogram Comments by Time of Day

```python

# Plot: Comments by Time of Day
plt.figure(figsize=(12, 6))
comments_by_hour.plot(kind='bar', width=0.9, color='skyblue', edgecolor='black')
plt.title('Comments by Time of Day', fontsize=16)
plt.xlabel('Hour of the Day', fontsize=14)
plt.ylabel('Number of Comments', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```



![image](https://github.com/user-attachments/assets/2e4d971b-80f7-41d3-8748-a07b36bbac82)




## Plot Histogram Comments by Day of the Week


```python

# Plot: Comments by Day of the Week
plt.figure(figsize=(12, 6))
comments_by_day.plot(kind='bar', color='orange', width=0.9, edgecolor='black')
plt.title('Comments by Day of the Week', fontsize=16)
plt.xlabel('Day of the Week', fontsize=14)
plt.ylabel('Number of Comments', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```


![image](https://github.com/user-attachments/assets/acc72f28-2eaa-4356-a81f-23ec063fd604)





## Plot Histogram Videos Added to Watch-Later by Time of Day

```python

# Plot: Videos Added to Watch-Later by Time of Day
plt.figure(figsize=(12, 6))
watch_later_by_hour.plot(kind='bar', width=0.9, color='skyblue', edgecolor='black')
plt.title('Videos Added to Watch-Later by Time of Day', fontsize=16)
plt.xlabel('Hour of the Day', fontsize=14)
plt.ylabel('Number of Videos Added', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```



![image](https://github.com/user-attachments/assets/55523eff-3fed-4434-8866-0af4714f3848)






## Plot Histogram Videos Added to Watch-Later by Day of the Week


```python

# Plot: Videos Added to Watch-Later by Day of the Week
plt.figure(figsize=(12, 6))
watch_later_by_day.plot(kind='bar', color='orange', width=0.9, edgecolor='black')
plt.title('Videos Added to Watch-Later by Day of the Week', fontsize=16)
plt.xlabel('Day of the Week', fontsize=14)
plt.ylabel('Number of Videos Added', fontsize=14)
plt.xticks(rotation=0, fontsize=12)
plt.yticks(fontsize=12)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()

```




![image](https://github.com/user-attachments/assets/baee829b-ed36-44e8-84a1-a6d4171241a9)









# Findings


As a result of my analyze i found out that:

For the videos i watched by time of the day, i was not very active time between 01:00-07:00 in the morning however on other times the number of videos watched are quite close.

For the videos i watched by day of the week, it goes evenly for weekdays and weekends.

For the comments posted on youtube by the day of the week, i can say that i did the most posting on saturday and least posting on sunday, moreover on weekdays i posted comments on the average.

For the comments posted on youtube by the time of the day.

For the videos i added to watch-later by day of the week, it goes evenly for weekdays and weekends.

For the videos i added to watch-later by time of the day, i was not very active time between 23:00-03:00 however on other times the number of videos watched are close.


As a conclusion: i can say that my hypothesis is generally holding.



# Limitations and future work

## Limatations:

The analysis heavily relies on data exported from Google Takeout. While comprehensive, this data might not capture every nuance of my YouTube usage, maybe if I could have used YouTube's API, i could have fetch more useful and meaningful data for my project.

The dataset does not include details about the content (e.g., video genres or categories). This limits the ability to analyze preferences or trends in the types of videos watched.

While exploratory analysis was conducted, the findings are not backed by advanced statistical tests or machine learning models. This could leave room for unverified assumptions or overlooked patterns.

## Future Work:

I could collect data continuously over a longer period to analyze long-term trends and reduce variability. 

Analyze metadata about the videos watched (e.g., category, duration, or channel popularity) to understand preferences better.

Use machine learning techniques to predict future behavior, such as peak usage times or days with higher engagement. Explore clustering to identify unique behavioral patterns (e.g., weekday vs. weekend habits).

Extend the analysis to compare individual behavior with general trends in YouTube usage, based on publicly available datasets. Collaborate with others to investigate how YouTube usage patterns vary across demographics or geographic locations.
