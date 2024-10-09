# 🚀 Mastering Data Analysis: Extracting Top Paying Jobs and Skills with SQL

## 📊 Project Overview

This project demonstrates advanced SQL techniques to analyze job market data, focusing on Data Analyst positions. By leveraging PostgreSQL and VSCode with SQLTools, we extract valuable insights about top-paying jobs and in-demand skills.
 
 ### ![The schema of the project](https://github.com/seotanvirbd/Job_skills_salary_analysis/blob/main/images/database_schema.png)
## 🎯 Key Objectives

- Identify the highest-paying Data Analyst roles
- Analyze skills required for top-paying positions
- Determine the most in-demand skills in the job market
- Explore the correlation between skills and salaries

## 🛠️ Tools Used

- PostgreSQL
- VSCode with SQLTools and SQLTools PostgreSQL plugin
- Data source: [Luke Barousse's SQL Project CSVs](https://lukeb.co/sql_project_csvs)

## 📁 Project Structure

The project consists of five SQL queries, each addressing a specific aspect of the job market analysis:

1. `1.top_salary_jobs.sql`: Identifies top-paying Data Analyst roles
2. `2_top_salary_jobs_skill.sql`: Analyzes skills for high-paying positions
3. `3_top_demanded_skills.sql`: Determines most in-demand skills
4. `4_top_paying_skills.sql`: Explores skills with the highest average salaries
5. `5_top_optimal_skills.sql`: Combines demand and salary data for optimal skill insights





## 💡 Key Insights

Our analysis reveals valuable insights into the Data Analyst job market:

- Remote work opportunities are prevalent among top-paying positions
- Certain skills consistently correlate with higher salaries
- There's a balance between high-demand skills and those commanding top salaries



## 1. 💰 Unveiling Top-Paying Data Analyst Jobs

```sql
SELECT	
	job_id,
	job_title,
	job_location,
	job_schedule_type,
	salary_year_avg,
	job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```
### Output:
| job_id  | job_title                                       | job_location | job_schedule_type | salary_year_avg | job_posted_date       | company_name                       |
|---------|--------------------------------------------------|--------------|-------------------|------------------|------------------------|------------------------------------|
| 226942  | Data Analyst                                     | Anywhere     | Full-time         | 650000.0         | 2023-02-20 15:13:33    | Mantys                             |
| 547382  | Director of Analytics                            | Anywhere     | Full-time         | 336500.0         | 2023-08-23 12:04:42    | Meta                               |
| 552322  | Associate Director - Data Insights               | Anywhere     | Full-time         | 255829.5         | 2023-06-18 16:03:12    | AT&T                               |
| 99305   | Data Analyst, Marketing                          | Anywhere     | Full-time         | 232423.0         | 2023-12-05 20:00:40    | Pinterest Job Advertisements        |
| 1021647 | Data Analyst (Hybrid/Remote)                    | Anywhere     | Full-time         | 217000.0         | 2023-01-17 00:17:23    | Uclahealthcareers                  |
| 168310  | Principal Data Analyst (Remote)                 | Anywhere     | Full-time         | 205000.0         | 2023-08-09 11:00:01    | SmartAsset                         |
| 731368  | Director, Data Analyst - HYBRID                 | Anywhere     | Full-time         | 189309.0         | 2023-12-07 15:00:13    | Inclusively                        |
| 310660  | Principal Data Analyst, AV Performance Analysis  | Anywhere     | Full-time         | 189000.0         | 2023-01-05 00:00:25    | Motional                           |
| 1749593 | Principal Data Analyst                            | Anywhere     | Full-time         | 186000.0         | 2023-07-11 16:00:05    | SmartAsset                         |
| 387860  | ERM Data Analyst                                 | Anywhere     | Full-time         | 184000.0         | 2023-06-09 08:01:04    | Get It Recruit - Information Technology |

## 🎯 SQL Query Breakdown

This SQL query retrieves information about the top-paying **Data Analyst** jobs that are located in **Anywhere**, based on average yearly salary. Below is a detailed breakdown of each part of the query:

## 📋 **SELECT Clause**
The query selects the following fields from the `job_postings_fact` table and its related `company_dim` table:
- **job_id**: Unique identifier for the job.
- **job_title**: The title of the job (in this case, it will always be "Data Analyst").
- **job_location**: The location of the job (in this case, it will always be "Anywhere").
- **job_schedule_type**: The type of work schedule (e.g., full-time, part-time).
- **salary_year_avg**: The average yearly salary for the job.
- **job_posted_date**: The date when the job was posted.
- **company_name**: The name of the company, retrieved from the `company_dim` table.

## 🔗 **LEFT JOIN Clause**
The query uses a `LEFT JOIN` to combine data from two tables:
- **job_postings_fact**: The main table containing job posting data.
- **company_dim**: A dimension table that holds company information. The join is based on the `company_id` field, which exists in both tables.

This `LEFT JOIN` ensures that even if a job posting doesn’t have a corresponding company in the `company_dim` table, the job data will still be included in the result (with a `NULL` company name).

## 🧳 **WHERE Clause**
The query filters the results based on specific conditions:
- **job_title_short = 'Data Analyst'**: Only retrieves job postings with the title "Data Analyst".
- **job_location = 'Anywhere'**: Only retrieves job postings where the job location is set to "Anywhere".
- **salary_year_avg IS NOT NULL**: Ensures that only job postings with a non-null average yearly salary are included in the results.

## 🏆 **ORDER BY Clause**
- **salary_year_avg DESC**: Sorts the results in descending order by the average yearly salary, so the highest-paying jobs appear first.

## 🔟 **LIMIT Clause**
- **LIMIT 10**: Limits the results to the top 10 highest-paying jobs, ensuring the query returns a concise list of the best opportunities.

## 🚀 **Summary**
This query is designed to quickly extract the top 10 **Data Analyst** jobs in "Anywhere," focusing on those that offer the highest salaries. It combines information from two tables and applies filters and sorting to present the most valuable job opportunities first.


## 2. 🧠 Decoding Skills for High-Paying Roles

```sql
WITH top_paying_jobs AS (
    SELECT	
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND 
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
# 📋 Fields Selected in CTE
- **job_id**: Unique identifier for the job.
- **job_title**: The title of the job.
- **salary_year_avg**: The average yearly salary for the job.
- **company_name**: The name of the company offering the job.

## 🔗 LEFT JOIN
Combines the `job_postings_fact` table with the `company_dim` table based on the `company_id`, ensuring that even jobs without a company entry in `company_dim` will still be included in the result set.

## 🧳 WHERE Clause
Filters to only include **Data Analyst** jobs located "Anywhere" with a non-null average salary.

## 🏆 ORDER BY
Sorts the data by the average yearly salary in descending order, so the highest-paying jobs appear first.

## 🔟 LIMIT Clause
Limits the result set to the top 10 highest-paying jobs.

## 📝 Main Query
In the main query, the `top_paying_jobs` result set is used to fetch additional data, specifically the skills required for each job.

```sql
SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
## 🔍 INNER JOIN Clauses
- **INNER JOIN skills_job_dim**: Links the `top_paying_jobs` with the `skills_job_dim` table using the `job_id` to find the skills required for each job.
- **INNER JOIN skills_dim**: Links the `skills_job_dim` with the `skills_dim` table using the `skill_id` to retrieve the names of the skills.

## 🎯 Fields Selected in Main Query
- **top_paying_jobs***: All columns from the `top_paying_jobs` CTE, which include job details like `job_id`, `job_title`, `salary_year_avg`, and `company_name`.
- **skills**: The names of the skills associated with each job.

## 🏆 ORDER BY Clause
- **salary_year_avg DESC**: Orders the final result set by the highest average yearly salary.




### 🔍 What's Happening Here?

1. We start with a Common Table Expression (CTE) named `top_paying_jobs` that reuses our previous query.
2. Then, we join this CTE with the skills dimension tables.
3. This allows us to see all the skills associated with each top-paying job.
4. The result is ordered by salary, maintaining our focus on the highest-paying roles.

### 💡 Why This Matters

This query is your skill-building roadmap! It reveals the exact skills that top-paying companies are looking for. By analyzing this data, you can strategically upskill yourself to become an irresistible candidate for these lucrative positions.

## 3. 🔥 Uncovering the Hottest Skills in Demand

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
### Output:
| skills   | demand_count |
|----------|--------------|
| sql      | 7291         |
| excel    | 4611         |
| python   | 4330         |
| tableau  | 3745         |
| power bi | 2609         |


### 🔍 What's Happening Here?

1. We're counting how many job postings require each skill.
2. The focus is on Data Analyst roles that offer remote work.
3. By grouping by skills and ordering by count, we see the most in-demand skills first.
4. We limit to the top 5 to focus on the most crucial skills.

### 💡 Why This Matters

This query is your trend-spotter! It shows you which skills are hot in the job market right now. If you're looking to make yourself more marketable, these are the skills you should prioritize in your learning journey.

## 4. 💎 Identifying the Most Lucrative Skills

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```

### 🔍 What's Happening Here?

1. We're calculating the average salary for jobs requiring each skill.
2. The focus is on remote Data Analyst positions with specified salaries.
3. Results are ordered by average salary, showing the most lucrative skills first.
4. We round the salary to the nearest whole number for readability.
5. The top 25 skills are displayed to give a comprehensive view.

### 💡 Why This Matters

This query is your salary booster! It reveals which skills are associated with the highest average salaries. If you're looking to maximize your earning potential, these are the skills you should focus on developing.

## 5. 🎯 Finding the Sweet Spot: High Demand and High Salary Skills

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True 
    GROUP BY
        skills_dim.skill_id
), 
average_salary AS (
    SELECT 
        skills_job_dim.skill_id,
        ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True 
    GROUP BY
        skills_job_dim.skill_id
)

```

### 🔍 What's Happening Here?

1. We create two CTEs: `skills_demand` (counting job postings per skill) and `average_salary` (calculating average salary per skill).
2. The main query joins these CTEs to combine demand and salary data.
3. We filter for skills with more than 10 job postings to ensure significance.
4. Results are ordered first by salary, then by demand, to find the optimal balance.
5. We limit to the top 25 skills to focus on the most valuable opportunities.

### 💡 Why This Matters

This query is your career compass! It identifies skills that are both in high demand and associated with high salaries. These are the golden skills that offer the best return on investment for your learning efforts, potentially leading to numerous job opportunities and attractive compensation packages.
# Another way 

This query extracts the top 25 most demanded skills from two tables, `skills_demand` and `average_salary`, based on their salary and demand count. Below is a detailed explanation of each part of the query:

## Tables Involved:
1. **skills_demand**: Contains data about different skills and the number of job postings requiring each skill (`demand_count`).
2. **average_salary**: Contains data about the average salary (`avg_salary`) associated with each skill.

Both tables are related by `skill_id`, a unique identifier for each skill.

## Query Breakdown:

```sql
SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN average_salary 
    ON skills_demand.skill_id = average_salary.skill_id
WHERE
    demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
1. **SELECT Clause**
   - **skills_demand.skill_id**: Fetches the unique ID of each skill.
   - **skills_demand.skills**: Fetches the name of each skill.
   - **demand_count**: Retrieves the number of job postings requiring each skill.
   - **avg_salary**: Fetches the average salary offered for each skill.

2. **INNER JOIN**
   The query uses an `INNER JOIN` to combine data from both tables based on the `skill_id`. Only records that exist in both `skills_demand` and `average_salary` will be included in the results.

3. **WHERE Clause**
   - **demand_count > 10**: Filters out skills that are not in high demand (i.e., with less than 10 job postings).

4. **ORDER BY**
   - **avg_salary DESC**: Sorts the results by the average salary in descending order, showing the highest-paying skills first.
   - **demand_count DESC**: Further sorts the results by demand count in descending order, so that for skills with the same average salary, the one with the higher demand appears first.

5. **LIMIT**
   - **LIMIT 25**: Limits the results to the top 25 most in-demand skills, ensuring the list is manageable and focused on the highest-paying and most demanded skills.

## Summary:
This query is perfect for generating insights into the most valuable skills in the job market, focusing on both demand and salary. By sorting the results first by salary and then by demand, it helps identify not only the skills that are in high demand but also the ones that offer the best financial compensation.

## 🚀 Conclusion

These SQL queries are your toolkit for navigating the Data Analyst job market. They allow you to:

1. Identify top-paying jobs and companies
2. Understand the skills required for high-salary positions
3. Recognize the most in-demand skills in the current market
4. Pinpoint skills associated with the highest salaries
5. Find the optimal balance between skill demand and compensation

By mastering these queries and understanding their insights, you're well-equipped to make informed decisions about your career path, skill development, and job search strategies. Happy data analyzing! 📊💼🎉

**Key Questions:**
- Which skills offer the best balance of high demand and high salary?
- How can Data Analysts optimize their skill set for both job security and earning potential?
- Are there any emerging skills that show promise in both demand and salary?




## 🎓 Lessons Learned

Through this project, we've gained valuable insights into data analysis and SQL:

- Complex queries can be broken down into manageable CTEs (Common Table Expressions)
- Joining multiple tables allows for rich, multidimensional analysis
- Careful consideration of NULL values and edge cases is crucial for accurate results
- Balancing demand and salary data provides a more holistic view of skill value

## 🔮 Future Work

To further enhance this project, consider the following next steps:

1. Implement time-based analysis to track trends in skill demand and salaries
2. Incorporate machine learning models to predict future high-value skills
3. Develop interactive visualizations to make insights more accessible
4. Expand the analysis to compare different job roles within the data field

## 🛠️ How to Use This Project

1. Clone this repository to your local machine
2. Ensure you have PostgreSQL installed and set up
3. Install VSCode and the SQLTools PostgreSQL plugin
4. Download the dataset from [Luke Barousse's SQL Project CSVs](https://lukeb.co/sql_project_csvs)
5. Set up your database connection in VSCode
6. Run the SQL queries in the order provided to replicate the analysis

## 📚 Additional Resources

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [SQLTools VSCode Extension](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools)
- [Data Analysis with SQL Tutorial](https://www.datacamp.com/courses/introduction-to-sql)

## 👤 About the Author

This project was developed by a Python developer and AI researcher passionate about data analysis and SQL optimization. Feel free to connect for collaborations or questions!

## 📞 Contact Information

- WhatsApp: [+8801687373830](https://api.whatsapp.com/send?phone=8801687373830)
- Facebook: [seotanvirbd](https://www.facebook.com/seotanvirbd)
- LinkedIn: [seotanvirbd](https://www.linkedin.com/in/seotanvirbd/)
- YouTube: [@tanvirbinali2200](https://www.youtube.com/@tanvirbinali2200)
- Email: tanvirafra1@gmail.com
- Blog & Portfolio: [seotanvirbd.com](https://seotanvirbd.com/)

## 🙏 Acknowledgements

Special thanks to Luke Barousse for providing the dataset and inspiration for this project.

---

Happy analyzing! 📊✨
