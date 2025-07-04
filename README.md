# Introduction
This project explores the job market with a focus on remote Data Analyst roles.
It examines top-paying positions, the most required skills, and highlights the best skills to learn — ones that are popular among employers and offer good salaries

### The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most required for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

You can find all SQL queries here: [sql_project folder](/sql_project/) 

# Used Tools
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **SQL:** To query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system.
- **Visual Studio Code:** For database management and executing SQL queries.
- **Git & GitHub:** For sharing my SQL scripts and analysis

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

### 1. Top-paying remote data analyst jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs and excluding outlier positions. This query highlights the high-paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    name AS company_name,
    job_country,
    job_schedule_type,
    salary_year_avg
FROM job_postings_fact
LEFT JOIN company_dim 
    USING (company_id)
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home IS TRUE AND
    salary_year_avg IS NOT NULL
    AND job_id <> '226942' --to exclude outlier
ORDER BY salary_year_avg DESC
LIMIT 10
```
Here's the key takeaways from analysis of the top data analyst jobs in 2023:

- **Wide Salary Range**  
 Top 10 paying data analyst roles span from $170 000 to $336 500, indicating significant salary potential in the field.
- **Diverse Employers**  
 Companies like SmartAsset, Meta, Pinterest and AT&T are among those offering high salaries, showing a broad interest across different industries.
- **Job Title Variety**  
 There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.
- **High Rewards in the US**  
 All of the top 10 high-paying remote data analyst roles are offered by companies based in the United States, highlighting high level of compensation in the American market.

![Top Paying Roles Salary Distribution](assets\1_top_paying_jobs_salary_distribution.jpg)
*Bar graph visualizing the distribution of the top 10 paying jobs for Data Analysts. Generated via Google Sheets from my SQL query results*

### 2. What skills are required for these top-paying jobs?
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        name AS company_name,
        job_country,
        job_schedule_type,
        salary_year_avg
    FROM job_postings_fact
    LEFT JOIN company_dim 
        USING (company_id)
    WHERE
        job_title_short = 'Data Analyst' 
        AND job_work_from_home IS TRUE 
        AND salary_year_avg IS NOT NULL
        AND job_id <> '226942' --to exclude outlier
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    tpj.job_title,
    tpj.company_name,
    tpj.job_country,
    tpj.job_schedule_type,
    tpj.salary_year_avg,
    sd.skills
FROM top_paying_jobs AS tpj
JOIN skills_job_dim AS sjd 
    ON tpj.job_id = sjd.job_id
JOIN skills_dim AS sd
    ON sjd.skill_id = sd.skill_id
```

Here's the key insights from skill analysis of the top highest-paying remote data analyst jobs in 2023:

- **SQL as a Core Requirement**  
 SQL is the most frequently mentioned skill, appearing in 9 out of the top 10 highest-paying remote data analyst job postings.

- **Programming Skills Are Essential**  
 Both Python and R are commonly required, underlining the importance of programming in data analytics. Python ranks second with 8 mentions, and the presence of the Python library *Pandas* as a separate skill further emphasizes its practical relevance.

- **Visualization Tools Are Valued**  
 Tableau and Power BI appear among the top skills, highlighting the need for strong data visualization capabilities. Tableau is more frequently requested, suggesting a market preference for this tool.

- **Cloud and Spreadsheet Skills Add Value**  
 High-paying roles also mention cloud technologies such as Azure and Snowflake, along with Excel, demonstrating the value of both modern platforms and traditional tools.

![Top Skills in High-Paying Jobs](assets\2_top_skills_for_high_paying_jobs.jpg)
*Bar graph visualizing the count of skills for the top 10 paying jobs for remote data analysts. Generated via Google Sheets from my SQL query results*

### 3. What skills are most required for data analysts?
To understand which skills are most in demand for remote Data Analyst roles, I analyzed job postings by joining them with associated skill data, highlighting the top 10 most frequently requested competencies

```sql
SELECT
    sd.skills,
    COUNT (sjd.job_id) AS skill_popularity
FROM skills_dim sd
JOIN skills_job_dim sjd
    USING (skill_id)
JOIN job_postings_fact jpf
    USING (job_id)
WHERE 
    jpf.job_title_short = 'Data Analyst' AND
    jpf.job_work_from_home IS TRUE
GROUP BY sd.skills
ORDER BY skill_popularity DESC
LIMIT 10
```
Here's the key takeaways from analysis of the most required skills for remote data analysts in 2023:

- **SQL Leads**  
 With over 7,000 mentions, SQL is by far the most requested skill for remote data analyst roles.
- **Excel Remains Highly Relevant**  
 While Excel is generally not suited for working with large datasets, it continues to be a popular tool for data analysis and visualization.
- **Python Dominates Among Programming Languages**  
 Python is the third most in-demand skill, reinforcing its role as a key language for analytics.
- **Visualization Tools Are Essential**  
 Tableau and Power BI both rank high, emphasizing the value of data visualization in analytics roles.

![Top Required Skills in Data Analyst Jobs in 2023](assets\3_top_required_skills.jpg)
*Bar graph visualizing the most popular skills required for remote data analysts. Generated via Google Sheets from my SQL query results*

### 4. Which skills are associated with higher salaries?
To identify which skills are most highly rewarded in remote Data Analyst roles, I analyzed salary data alongside job-required skills to find those linked to the highest average pay.

```sql
SELECT
    sd.skills,
    ROUND(AVG(jpf.salary_year_avg),0) AS avg_year_salary
FROM skills_dim sd
JOIN skills_job_dim sjd
    USING (skill_id)
JOIN job_postings_fact jpf 
    USING (job_id)
WHERE 
    jpf.job_title_short = 'Data Analyst' AND
    jpf.salary_year_avg IS NOT NULL AND
    jpf.job_work_from_home IS TRUE
GROUP BY sd.skills
ORDER BY avg_year_salary DESC
LIMIT 10
```
Here's the key takeaways from analysis of top paying skills for remote data analysts:

- **Big Data and Data Engineering Tools**  
 Skills like **PySpark**, **Couchbase**, and **Elasticsearch** indicate the growing demand for professionals capable of handling large-scale data processing and storage. Mastery of these technologies enables efficient management and querying of complex datasets in distributed environments.

- **Version Control and Collaboration Platforms**  
  **Bitbucket** and **GitLab** appear as essential tools for source code management and team collaboration. These platforms highlight the importance of version control practices and seamless cooperation within data analytics teams.

- **Advanced Analytics and Automation**  
  Tools such as **DataRobot** and **Watson** point to the increasing role of automated machine learning and AI-powered analytics in data analysis workflows. Familiarity with these platforms allows analysts to build and deploy predictive models more effectively.

- **Programming and Data Manipulation Environments**  
  Skills including **Swift**, **Jupyter**, and **Pandas** emphasize the need for strong programming capabilities and interactive data exploration. **Jupyter notebooks** and **Pandas** remain fundamental for data manipulation, while **Swift’s** presence suggests niche or cross-disciplinary applications.

![Top Paying Skills for Remote Data Analysts in 2023](assets\4_top_paying_skills.jpg)
*Bar graph visualizing the highest-paying skills required for remote data analysts. Generated via Google Sheets from my SQL query results*

### 5. What are the most optimal skills to learn?

To identify the most optimal skills for remote Data Analyst roles, I started by selecting the highest-paying skills and then filtered this set to include only those skills that appear more frequently than average in job postings. This approach ensures a focus on skills that are both highly rewarded and in strong demand.

```sql
WITH skill_stats AS (
    SELECT
        sd.skills,
        ROUND(AVG(jpf.salary_year_avg), 0) AS avg_year_salary,
        COUNT(jpf.job_id) AS skill_popularity
    FROM skills_dim sd
    JOIN skills_job_dim sjd USING (skill_id)
    JOIN job_postings_fact jpf USING (job_id)
    WHERE 
        jpf.job_title_short = 'Data Analyst' AND
        jpf.job_work_from_home IS TRUE AND
        jpf.salary_year_avg IS NOT NULL
    GROUP BY sd.skills
)

SELECT *
FROM skill_stats
WHERE skill_popularity > (
    SELECT AVG(skill_popularity) FROM skill_stats
)
ORDER BY  
    avg_year_salary DESC,
    skill_popularity DESC
LIMIT 20;
```
Here's the key takeaways from analysis of the most optimal skills for remote data analysts:

- **Core Analytics Tools Dominate**  
 **SQL**, **Python**, **R**, and **Tableau** stand out as a combination of highly demanded and well-paid skills. These are the core technologies for querying, analyzing, and visualizing data, making them a must-have foundation for remote Data Analyst roles.

- **Emerging Technologies Drive Higher Salaries**  
 Cloud platforms and big data tools like **Snowflake**, **Azure**, **AWS**, and **Hadoop** appear less often but are associated with higher average salaries. This suggests that expertise in advanced data infrastructure can significantly increase earning potential.

- **Foundational Tools Remain Essential**  
  Despite their lower salary associations, widely used tools such as **Excel** and **PowerPoint** remain in demand. They play a key role in everyday analysis, reporting, and stakeholder communication.


![The Most Optimal Skills for Data Analyst](assets\5_optimal_skills.jpg)
*Scatter plot showing the relationship between skill demand and average salary to highlight the most optimal skills for remote Data Analysts. Generated via Excel from my SQL query results*

# What I Learned

Throughout this project, I developed and strengthened the following technical and analytical skills:

- **Advanced SQL Proficiency**  
 Improved my skills in writing queries using JOINs, CTEs, aggregate functions (COUNT, AVG), filtering, and sorting to efficiently extract and manipulate data.

- **Data Quality and Outlier Management**  
 Practiced data quality management techniques, including detecting and handling outliers, to ensure my analysis was based on clean and reliable data.

- **Analytical Reasoning and Data Interpretation**  
 Practiced interpreting and synthesizing data insights to summarize results into clear and actionable conclusions useful for career planning.

# Conclusions
### Insights  
 The following key insights summarize the most important findings regarding skills, salaries, and job market trends for remote Data Analyst positions in 2023:

- **Strong Demand for Core Analytics Skills**  
 SQL, Python, and Tableau consistently appear as both highly demanded and well-compensated skills. These form the backbone of technical requirements for remote Data Analyst roles and are essential for querying, analysis, and visualization tasks.

- **Emerging Technologies Unlock Higher Salaries**  
 Proficiency in working with cloud platforms (Azure, AWS, Snowflakes) is linked to significantly higher pay, even though these skills are less frequently required. Mastering these tools enables analysts to access advanced opportunities in big data and machine learning.

- **Traditional Tools Remain Relevant**  
 Despite the rise of modern analytics stacks, Excel and PowerPoint remain widely requested. Their continued presence reflects the importance of versatile reporting and communication skills in day-to-day analyst responsibilities.

- **High Compensation Concentrated in the US Market**  
 The highest-paying remote Data Analyst roles are offered by U.S.-based companies, with salaries ranging from $170,000 to over $330,000. This suggests strong earning potential for professionals targeting the American remote job market.

- **Cross-Disciplinary and Collaborative Skills Add Value**  
 Familiarity with version control systems (GitLab, Bitbucket) and interactive environments like Jupyter highlights the growing importance of collaboration, adaptability, and flexibility in modern data analytics workflows.

### Closing Thoughts
Working on this project helped me strengthen my SQL abilities while gaining a deeper understanding of the current remote data analyst job market. The insights I uncovered offer practical guidance on which skills to focus on for maximizing career opportunities and earning potential. For anyone aiming to succeed as a data analyst, concentrating on the most in-demand and well-paid skills is key. This experience also reinforced how important it is to keep learning and adapting as the field continues to evolve rapidly