# Student Report Card Scraper

> **Note:** Neither this script nor the included URL works any longer. The underlying issue was fixed after it was brought to the attention of the IT admin.
> The code is shared purely for **educational purposes** to demonstrate how using incrementing numeric IDs in URLs create vulnerabilities which can be exploited, and why they should be avoided.

In 11th grade, I discovered a flaw in the way my school distributed online report cards.
A simple URL with a numeric `id` parameter was sent via SMS to each parent's mobile number. 
This URL redirected the user to the student's report card. 
The issue with this system was that the `id` could be incremented to access other students' report cards without any form of authentication.

Basic structure:

```
https://donboscovaduthala.in/u.php?id=<number>
```

By incrementing the number, anyone could view another student’s report card.
To demonstrate the severity of this vulnerability to the school IT team, I wrote this Python script that:

1. Iterates through a known range of student IDs.
2. Fetches each student's report card page.
3. Parses student details using **BeautifulSoup**.
4. Stores the extracted data into a CSV file.

## How It Works

### Libraries Used

* **requests** – To send HTTP GET requests to the report card URLs.
* **BeautifulSoup (bs4)** – To parse HTML and extract relevant student details.
* **csv** – To store the scraped data in a structured CSV file.

### Code Flow

1. **Setup**
   
   Initialize an empty list `details_list` to store student data.

3. **Iterating Through IDs**
   
   Use a `for` loop through the desired students in steps of `4`.
   It is in steps of 4 because each report card could be accessed through 4 concurrent IDs.

   * Each ID corresponds to a report card.
   * `y` holds the current ID as a string for URL building.
   * `z` tracks the index of the fetched student.

5. **Fetching HTML Content**
   
   Construct the target URL:

   ```python
   url = "https://donboscovaduthala.in/u.php?id=" + y
   ```

   Fetch the page using:

   ```python
   page = requests.get(url)
   soup = BeautifulSoup(page.content, 'lxml')
   ```

7. **Extracting Student Details**
   
   Use `soup.find(text="...")` to locate labels in the HTML, then navigate sibling elements to get:

   * **Name**
   * **Class**
   * **Division**
   * **ID No**
    
   These are appended as a dictionary to `all_details`.

9. **Saving Data**
    
   After the loop, collect CSV headers from the first record:

   ```python
   keys = details_list[0][0].keys()
   ```

   Write all entries into `details.csv` using `csv.DictWriter`.

## Purpose and Impact

The purpose of this scraper was to **prove the vulnerability** to school authorities, not to misuse the data. After presenting the issue, the vulnerability was closed and the entire system was reworked.

