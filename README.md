# 🗳️ ELECTION RESULTS 2024 ANALYSIS  
**Author:** Tushar Kanojiya  
📧 **Contact:** [tkanojiya83@gmail.com](mailto:tkanojiya83@gmail.com)

---

## 📘 Table of Contents

1. [Project Overview](#project-overview)  
2. [Data Source](#data-source)  
3. [SQL Workflow](#sql-workflow)  
4. [SQL Queries (Basic, Intermediate, Advanced)](#sql-queries-basic-intermediate-advanced)  
5. [Power BI Workflow (Step-by-Step)](#power-bi-workflow-step-by-step)  
6. [Key Insights](#key-insights)  
7. [How to Run](#how-to-run)  
8. [Folder Structure](#folder-structure)  
9. [Future Enhancements](#future-enhancements)  
10. [Contact](#contact)

---

## 🧾 Project Overview

This project analyzes and visualizes **India’s 2024 Election Results** using **SQL** for data processing and **Power BI** for interactive dashboards.  
It reveals insights on **party performance**, **winning margins**, **vote share**, and **regional strengths**.

---

## 📂 Data Source

The dataset includes:
- Candidate names and parties  
- Total votes received  
- Constituency and state details  
- Winner and runner-up information  

Data was initially in **CSV/Excel** format and imported into **SQL** for analysis.

---

## 🧠 SQL Workflow

### 🪜 Steps Performed in SQL

1. **Data Import**  
   - Imported the dataset into SQL tables using `LOAD DATA INFILE` or `INSERT INTO`.

2. **Data Cleaning**  
   - Removed duplicates and null entries using `DISTINCT`, `DELETE`, and `COALESCE()`.  
   - Standardized column names and text formatting using `TRIM()` and `UPPER()`.

3. **Data Transformation**  
   - Created new calculated fields for **vote share** and **margin**.  
   - Used **joins** to merge Candidate, Party, and Constituency data.  
   - Implemented **window functions** (`RANK()`, `DENSE_RANK()`) to identify winners.

4. **Data Analysis**  
   - Aggregated total votes by party and state.  
   - Calculated **percentage margins** and **ranking**.  
   - Exported cleaned data to Power BI for visualization.

---

## 🧩 SQL Queries (Basic, Intermediate, Advanced)

### 🟢 Basic Queries

| **Purpose** | **SQL Query** | **Functions / Concepts** | **Description** |
|--------------|---------------|---------------------------|-----------------|
| Total Votes by Party | ```sql SELECT party_name, SUM(total_votes) AS Total_Votes FROM election_data GROUP BY party_name ORDER BY Total_Votes DESC; ``` | `SUM()`, `GROUP BY`, `ORDER BY` | Summarizes total votes secured by each party. |
| List Candidates by Constituency | ```sql SELECT constituency_name, candidate_name, party_name FROM election_data ORDER BY constituency_name; ``` | `ORDER BY` | Lists all candidates contesting in each constituency. |
| Constituency Count per State | ```sql SELECT state_name, COUNT(DISTINCT constituency_name) AS Total_Seats FROM election_data GROUP BY state_name; ``` | `COUNT(DISTINCT)` | Counts the total constituencies per state. |

---

### 🟡 Intermediate Queries

| **Purpose** | **SQL Query** | **Functions / Concepts** | **Description** |
|--------------|---------------|---------------------------|-----------------|
| Winner per Constituency | ```sql SELECT constituency_name, candidate_name AS Winner, party_name, MAX(total_votes) AS Votes FROM election_data GROUP BY constituency_name; ``` | `MAX()`, `GROUP BY` | Identifies top-voted candidates per constituency. |
| State-wise Party Performance | ```sql SELECT state_name, party_name, SUM(total_votes) AS Total_Votes FROM election_data GROUP BY state_name, party_name ORDER BY state_name, Total_Votes DESC; ``` | `GROUP BY`, `ORDER BY` | Displays party performance per state. |
| Vote Share per Candidate | ```sql SELECT constituency_name, candidate_name, (total_votes * 100.0 / SUM(total_votes) OVER (PARTITION BY constituency_name)) AS Vote_Percentage FROM election_data; ``` | `SUM() OVER()`, `PARTITION BY` | Calculates candidate’s vote share per constituency. |

---

### 🔴 Advanced Queries

| **Purpose** | **SQL Query** | **Functions / Concepts** | **Description** |
|--------------|---------------|---------------------------|-----------------|
| Winner vs Runner-up with Margin | ```sql SELECT A.constituency_name, A.candidate_name AS Winner, B.candidate_name AS Runner_Up, (A.total_votes - B.total_votes) AS Margin FROM (SELECT *, RANK() OVER (PARTITION BY constituency_name ORDER BY total_votes DESC) AS rnk FROM election_data) A JOIN (SELECT *, RANK() OVER (PARTITION BY constituency_name ORDER BY total_votes DESC) AS rnk FROM election_data) B ON A.constituency_name = B.constituency_name WHERE A.rnk = 1 AND B.rnk = 2; ``` | `RANK()`, `JOIN`, `PARTITION BY` | Finds the top 2 candidates and computes their vote margin. |
| Party with Highest Winning Margin | ```sql SELECT party_name, MAX(total_votes - LAG(total_votes) OVER (PARTITION BY constituency_name ORDER BY total_votes DESC)) AS Highest_Margin FROM election_data GROUP BY party_name ORDER BY Highest_Margin DESC; ``` | `LAG()`, `OVER()`, `MAX()` | Finds which party achieved the largest winning margin. |
| Comparative Vote Analysis | ```sql SELECT constituency_name, SUM(CASE WHEN party_name = 'PartyA' THEN total_votes ELSE 0 END) AS PartyA_Votes, SUM(CASE WHEN party_name = 'PartyB' THEN total_votes ELSE 0 END) AS PartyB_Votes FROM election_data GROUP BY constituency_name; ``` | `CASE WHEN`, `SUM()` | Compares votes between two parties in each constituency. |

---

## 📊 Power BI Workflow (Step-by-Step)

1. **Data Connection**  
   - Connected Power BI to SQL database.  
   - Imported tables: `Candidate`, `Party`, `Constituency`, `ElectionResults`.

2. **Data Cleaning (Power Query)**  
   - Removed nulls, duplicates, and renamed columns.  
   - Ensured correct data types for all numeric fields.

3. **Data Modeling**  
   - Established relationships between tables:  
     - `ElectionResults[party_id] → Party[party_id]`  
     - `ElectionResults[candidate_id] → Candidate[candidate_id]`  
     - `ElectionResults[constituency_id] → Constituency[constituency_id]`  
   - Built a **Star Schema** for efficiency.

4. **DAX Measures**  
   - `Total Votes = SUM(ElectionResults[votes])`  
   - `Vote Share % = DIVIDE(SUM(ElectionResults[votes]), CALCULATE(SUM(ElectionResults[votes]), ALLEXCEPT(ElectionResults, ElectionResults[Constituency]))) * 100`  
   - `Winning Margin = [Top Candidate Votes] - [Runner-up Votes]`  
   - `Seats Won = DISTINCTCOUNT(Winning Constituencies)`

5. **Dashboard Design**  
   - **Bar Chart:** Party-wise total votes  
   - **Pie Chart:** Vote share percentage  
   - **Map Visual:** State-wise party performance  
   - **Table Visual:** Candidate-wise detailed view  
   - Added **slicers** for Year, Party, and State.

6. **Formatting & Publishing**  
   - Added tooltips, color themes, and data labels.  
   - Published final dashboard to **Power BI Service** for sharing.

---

## 💡 Key Insights

1. 🏆 **Top Performing Party:** Identified the party with maximum total votes nationwide.  
2. 📊 **Vote Share Patterns:** Revealed clear dominance regions for major parties.  
3. ⚖️ **Close Contests:** Highlighted constituencies where the margin < 1000 votes.  
4. 🗺️ **State-Level Trends:** Displayed which parties led in specific regions.  
5. 📈 **Performance Comparison:** Tracked vote growth and losses compared to previous elections (if data available).  
6. 💬 **Interactive Dashboard:** Enabled users to explore data dynamically through Power BI filters.

---

## 🧰 How to Run

1. Clone the repository:
   ```bash
   git clone https://github.com/Tushar-kanojiya-4002/ELECTION-results-2024.git

