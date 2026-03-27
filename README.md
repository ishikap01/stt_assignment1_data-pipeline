Assignment 1: The "Box Office Bomb" Data Pipeline
Total Marks: 20
Deadline: 7:00 PM, 22nd January, 2026
Background Story
You are working as a data engineer at a media analytics startup. Your team is conducting a study on financial failures in the film industry. They want to understand the relationship between Production Budgets, Estimated Losses, and Critical Reception (IMDb/Metacritic scores).
There is no clean dataset available. You have identified a Wikipedia page, "List of biggest box-office bombs," as your primary source. However, this data is meant for human eyes, not machines. It contains:
Visual noise: Status symbols (e.g., §, †) attached to titles.

Ambiguous numbers: Budgets and losses are often ranges (e.g., "$100–160").

Messy references: Footnotes (e.g., [nb 2]) and currency symbols mixed with values.

Your manager has adopted a "Strict Schema" policy. You must build a pipeline that ingests this dirty data, validates it using Pydantic to ensure type safety, and then enriches it via the OMDb API.
Your Objective
Design and implement an end-to-end pipeline that:
Scrapes the specific "Box Office Bomb" table from Wikipedia.
Validates & Cleans the data using Pydantic models (converting ranges to averages, stripping symbols).
Enriches the valid entries with metadata (Plot, Ratings) using the OMDb API.
Categorizes the financial impact for analysts.

Tasks & Evaluation
Task 1: Scrape the "Bombs" Table (4 Marks)
Story context:
The data is locked in an HTML table. You need to extract the raw text exactly as it appears before attempting to clean it.
Task:
Target the URL: https://en.wikipedia.org/wiki/List_of_biggest_box-office_bombs
Programmatically extract the main table: "Biggest box-office bombs".
Parse the following raw string columns for every row:
Film Title (e.g., "Jungle Cruise §")
Year (e.g., "2021")
Net production budget (e.g., "$200" or "$100–160")
Estimated loss (Target the "Nominal" column, not the inflation-adjusted one).
Evaluation:
Targeting: Correctly identifies the table and handles the nested headers (Budget vs Loss sub-columns). (2 marks)
Extraction: Accurate extraction of raw strings for all rows without losing data. (2 marks)
Note:
When extracting the movie titles, you need to extract the entire raw string with the symbols, references, etc along with the titles. To extract the complete text content of an element, including any symbols or characters outside nested tags, use the .get_text() method directly on the parent element. For example: text = soup.th.get_text()

Task 2: Pydantic Data Parsing & Validation (6 Marks)
Story context:
"Garbage In, Garbage Out." Your manager forbids passing raw strings to the analysis layer. You must implement a validation layer using Pydantic. This layer will act as a firewall, rejecting bad data and cleaning messy formats.
Task:
Define a Pydantic model class (e.g., MovieData) that implements the following validators:

Title Cleaning:
Remove footnote markers (e.g., [nb 2], [1]).
Remove special status symbols: § (streaming) and † (currently playing).
Example: Input "Jungle Cruise §"  → Output "Jungle Cruise"
Numeric Parsing (Budget & Loss):
Strip currency symbols ($) and reference tags.
Handle Ranges: If a value is a range (e.g., "100–160"), you must parse both numbers and calculate the average.
Example: Input "$100–160" → Output 130.0 (float).
Year Validation:
Ensure the year is a valid integer.
Implementation Constraint:
You must use Pydantic's @field_validator (v2) or @validator (v1) decorators.
Rows that fail validation (e.g., unparsable numbers) must be dropped or logged, but must not crash the script.
Evaluation:
Regex Logic: Correct patterns to clean Titles (handling specific symbols §, †). (2 marks)
Range Handling: Logic to parse hyphens/en-dashes in budgets and calculate averages. (2 marks)
Pydantic Usage: Effective use of the model to enforce float and int types. (2 marks)

Task 3: Enrich with OMDb Data (4 Marks)
Story context:
Financial data tells us how much money was lost, but it doesn't explain why. Was it a bad script? A specific director? We need metadata to find the root cause.
Task:
For each validated movie object from Task 2:
Query the OMDb API using the clean Title and Year.
Extract and store the following specific fields from the JSON response:
Plot
Metascore (e.g., "40")
imdbRating (e.g., "5.6")
Director (e.g., "Oliver Stone")
Language (e.g., "English")

Error Handling:
If the API returns Response: "False", or if a specific field is "N/A", ensure your code handles this gracefully (store as None or NaN).
Do not discard the Wikipedia row just because OMDb is missing data.
Evaluation:
Correct construction of the API request parameters. (1 mark)
Accurate extraction of the 5 required fields. (2 marks)
Robust handling of missing data (converting "N/A" strings to None). (1 mark)

Task 4: Data Consistency Check (2 Marks)
Story context:
APIs are not perfect. Sometimes querying "The Mummy" returns the 1999 hit instead of the 2017 flop.
Task:
Compare the Wikipedia Year with the OMDb Year.
Create a column Match_Status:
"Verified": Year's match (allow a tolerance of ±1 year for release differences).
"Mismatch": Years differ by >1.
"Not Found": OMDb returned no data.
Evaluation:
Correct logic for comparison (handling integers vs strings) and labeling. (2 marks)

Task 5: Final Dataset & Categorization (4 Marks)
Story context:
The final output will be consumed by financial analysts who group losses into tiers.
Task:
Create a Pandas DataFrame from your processed objects.
Add a new column Loss_Category based on your cleaned Estimated Loss column:
"Catastrophic": Loss ≥ $100M
"Severe": Loss between $50M and $100M

"Moderate": Loss < $50M
Save the result to box_office_failures.csv.
Required Columns:
Title, Year, Director, Language, Budget_Millions, Loss_Millions, Loss_Category, IMDb_Rating, Metascore, Match_Status
Evaluation:
Correct calculation of Loss_Category using the cleaned float values. (2 marks)
Clean formatting of the final CSV (correct column headers, no dirty artifacts). (2 marks)

Submission Requirements
Submit the link to the collab notebook with all the cells run and the output visible. The collab notebook should be submitted with access to “IIT Gandhinagar”. No changes will be entertained after the submission deadline.
Code Quality: The code must be modular. The Pydantic model should be a distinct class definition.
