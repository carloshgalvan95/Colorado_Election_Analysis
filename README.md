# Colorado Election Audit Analysis
---


# Project Overview
The Colorado Board of Elections will be performing an election audit of the tabulated results for US congressional precinct in Colorado. The aspects that will be taken into consideration will be:

1. Calculate the total number of votes cast.
2. Get a complete list of candidates who received votes.
3. Calculate the total number of votes each candidate received.
4. Calculate the percentage of votes each candidate won.
5. Determine the winner of the election based on popular vote.

After obtaining and auditing that information we will be adding the following data:

- The vote turnout for each county.
- The percentage of votes from each county out of the total count.
- The county with the highest turnout.

## The power of participation
The purpose of adding this data is to get parameters on the actual percentage of the population in voting age that are participating in the taking of electoral decisions for the wellbeing of their state. It is crutial to encourage and emphasize the importance of every citizen's participation to respect the rights of every social, racial and cultural population group. Broaden and incrementing the sample taken when taking decisions through a voting process ensures the best decision is always taken.

# Audit transparency

In order to mantain utmost transparency during the audit process, we will leave this task to the most accurate possible auditor, a computer.

The PyPoll_Challenge algorithm works in the following way:
1. First, we are going to set up our working enviroment, we will be using **csv** and **os** dependencies.

2. The next step is to define our input and output files, in this case, **input:** election_results.csv our dataset and **output:** election_analysis.txt. a simple text file will helps us keep everything transparent.

3. Next we will be defining our variables.

```
# Add our dependencies.
import csv
import os

# Add a variable to load a file from a path.
file_to_load = os.path.join("Resources", "election_results.csv")

# Add a variable to save the file to a path.
file_to_save = os.path.join("Analysis", "election_analysis.txt")

# Initialize a total vote counter.
total_votes = 0

# Candidate Options and candidate votes.
candidate_options = []
candidate_votes = {}

# 1: Create a county list and county votes dictionary.
county_list = []
county_votes = {}

# Track the winning candidate, vote count and percentage
winning_candidate = ""
winning_count = 0
winning_percentage = 0

# 2: Track the largest county and county voter turnout.
largest_turnout_county = ""
votes_largestcounty = 0

```
4. Then once our variables are defined, we will be reading the csv file and converting it to a list of dictionaries.

5. For each row in the CSV file we will be keeping count of the votes in our previously defined vote variable and getting the neccesary information depending on the candidate and county. If the candidate doesn't match any existing candidate we will be keeping record in our candidate list, and keeping count of every candidate's received vote.

```
# Read the csv and convert it into a list of dictionaries
with open(file_to_load) as election_data:
    reader = csv.reader(election_data)

    # Read the header
    header = next(reader)

    # For each row in the CSV file.
    for row in reader:

        # Add to the total vote count
        total_votes = total_votes + 1

        # Get the candidate name from each row.
        candidate_name = row[2]

        # 3: Extract the county name from each row.
        county = row[1]

        # If the candidate does not match any existing candidate add it to
        # the candidate list
        if candidate_name not in candidate_options:

            # Add the candidate name to the candidate list.
            candidate_options.append(candidate_name)

            # And begin tracking that candidate's voter count.
            candidate_votes[candidate_name] = 0

        # Add a vote to that candidate's count
        candidate_votes[candidate_name] += 1

        # 4a: Write an if statement that checks that the
        # county does not match any existing county in the county list.
        if county not in county_list:

            # 4b: Add the existing county to the list of counties.
            county_list.append(county)

            # 4c: Begin tracking the county's vote count.
            county_votes[county] = 0

        # 5: Add a vote to that county's vote count.
        county_votes[county] += 1
```
6. We will be saving this results in the previously mentioned text file to analyze the overall total results, how every county impacted this results and verifying the winning candidate.
7. Using a loop to go over every country in our previously created county list, we are going the get the information of votes and percentage of the total votes for every county that participated in the election. Then, comparing using an if statement to determine the largest county turnout.

```

# Save the results to our text file.
with open(file_to_save, "w") as txt_file:

    # Print the final vote count (to terminal)
    election_results = (
        f"Election Results\n"
        f"-------------------------\n"
        f"Total Votes: {total_votes:,}\n"
        f"-------------------------\n\n"
        f"County Votes:\n")
    print(election_results, end="")

    txt_file.write(election_results)

    # 6a: Write a for loop to get the county from the county dictionary.
    for county in county_list:
        # 6b: Retrieve the county vote count.
        totalvotes_county = county_votes.get(county)

        # 6c: Calculate the percentage of votes for the county.
        percentagevotes_county = float(totalvotes_county) / float(total_votes) * 100

        # 6d: Print the county results to the terminal.
        county_results = (
            f"{county}: {percentagevotes_county:.1f}% ({totalvotes_county:,})\n"
        )
        print(county_results)
        # 6e: Save the county votes to a text file.
        txt_file.write(county_results)
        # 6f: Write an if statement to determine the winning county and get its vote count.
        if totalvotes_county > votes_largestcounty:
            votes_largestcounty = totalvotes_county
            largest_turnout_county = county

    # 7: Print the county with the largest turnout to the terminal.
    largestcounty = (
        f"\n-------------------------\n"
        f"Largest County Turnout: {largest_turnout_county}\n"
        f"-------------------------\n"
    )
    print(largestcounty)

    # 8: Save the county with the largest turnout to a text file.
    txt_file.write(largestcounty)
    
```

8. The only thing left is to get the resulst of the election. We will be getting the total votes for every candidate and its percentage.
9. Having all that information at hand. We can then finish our audit getting our winning candidate.
10. Write down every candidate vote count, percentage and winner in our output text file.

```
# Save the final candidate vote count to the text file.
    for candidate_name in candidate_votes:

        # Retrieve vote count and percentage
        votes = candidate_votes.get(candidate_name)
        vote_percentage = float(votes) / float(total_votes) * 100
        
        # Print each candidate's voter count and percentage to the
        # terminal.
        candidate_results = (
            f"{candidate_name}: {vote_percentage:.1f}% ({votes:,})\n"
            )
        print(candidate_results)

        #  Save the candidate results to our text file.
        txt_file.write(candidate_results)

        # Determine winning vote count, winning percentage, and candidate.
        if (votes > winning_count) and (vote_percentage > winning_percentage):
            winning_count = votes
            winning_candidate = candidate_name
            winning_percentage = vote_percentage

    # Print the winning candidate (to terminal)
    winning_candidate_summary = (
        f"-------------------------\n"
        f"Winner: {winning_candidate}\n"
        f"Winning Vote Count: {winning_count:,}\n"
        f"Winning Percentage: {winning_percentage:.1f}%\n"
        f"-------------------------\n")
    print(winning_candidate_summary)

    # Save the winning candidate's name to the text file
    txt_file.write(winning_candidate_summary)
    
```

# Summary

|             | **Vote Distribution** |     |
|-------------------------|-----------------------|------------------|
| **Candidate**               | **Votes Received**      | **Vote Percentage**  |
| Charles Casper Stockham | 85,213                | 23.0%            | 
| Diana DeGette           | 272,892               | 73.8%            |   
| Raymon Anthony Doane    | 11,606                | 3.1%             |

| **Colorado Elections** | **Results** |
|------------------------|-------------|
| **Total Casted Votes** | 369,711     |
|-----------------|------------|
| **County**      |**Arapahoe**|
| **Casted Votes**|   24,801   |
| **Percentage**  |   6.7%     |
|-----------------|------------|
| **County**      |**Denver**|
| **Casted Votes**|   306,055  |
| **Percentage**  |  82.8%     |
|-----------------|------------|
| **County**      |**Jefferson**|
| **Casted Votes**|   38,855   |
| **Percentage**  |   10.5%    |

The analysis of the election show that:
- There were 369,711 votes cast in the election.
- The candidates were:
- Charles Casper Stockham
- Diana DeGette
- Raymon Anthony Doane

The candidate results were:
- **Charles Casper Stockham** received **23.0%** of the vote and **85,213** number of votes.
- **Diana DeGette** received **73.8%** of the vote and **272,892** number of votes.
- **Raymon Anthony Doane** received **3.1%** of the vote and **11,606** number of votes.
- The winner of the election was:
    ー **Diana DeGette**, who received **73.8%** of the vote and **272,892** number of votes.
    
**Vote Turnout**                      |  **Vote Distribution**
:-------------------------:|:-------------------------:
<img src="https://github.com/carloshgalvan95/Election_Analysis/blob/main/Resources/Vote_Turnout.png" height="365" width="1500" />  | <img src="https://github.com/carloshgalvan95/Election_Analysis/blob/main/Resources/Vote_Distribution.png" height="365" width="1500" />

# Election_Analysis2.0.py
---
Python gives is a powerful tool that goes way beyond the capabilities of data analysis that we could possibly imagine doing with just Microsoft Excel, both the size of the data and the processing speed we have with VBA or Excel cannot compare to well written Python code.

However, there are even better ways to do Data Analysis and the more tools we have, the better and quicker the job gets done. To demonstrate so, and also to think beyond the reach of the current audit analysis we can get the same exact results using Pandas dependency and Jupyter Notebooks, aka... 

Election_Analysis2.0.**ipynb**

# Resources
- Data Source: election_results.csv
- Software: Python 3.6.1, Visual Studio Code, 1.38.1
