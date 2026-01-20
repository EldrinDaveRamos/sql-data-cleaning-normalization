# sql-data-cleaning-normalization
Cleaned and standardized a real world dataset of 2631 rows across multiple tables and columns. Performing data validation, handled missing and inconsistent records, and prepared the dataset for explanatory analysis.


This project focuses on cleaning and standardizing a real-world dataset containing 2,631 rows across multiple relational tables. The dataset includes company layoffs, industry classifications, earnings data, and date fields.


Removed duplicates and standardized inconsistent records

Fixed incorrect date formats and missing values

Normalized company and industry names

Ensured referential integrity across tables

Prepared the dataset for downstream exploratory analysis


A public, non-sensitive dataset used for practicing SQL-based cleaning and normalization workflows.
(No raw data files included for privacy and to avoid repository weight.)

MySQL

SQL (CTEs, JOINs, Window Functions, Aggregations)

SQL QUERIES:

SELECT DISTINCT(TRIM(company))
FROM layoffs_staging2;

Update layoffs_staging2
set company = TRIM(company);

Select *
From layoffs_staging2
Where industry like 'crypto%';

update layoffs_staging2
set industry = 'Crypto'
Where industry Like 'Crypto%';

select distinct country, trim(Trailing '.' from country)
from layoffs_staging2
order by 1;

update layoffs_staging2
set country = trim(Trailing '.' from country)
Where country Like 'United States%';

Select distinct country
from layoffs_staging2;

select date,
str_to_date(date, '%m/%d/%Y')
from layoffs_staging2;

update layoffs_staging2
set date = str_to_date(date, '%m/%d/%Y')

Select *
from layoffs_staging2
where total_laid_off is null
And percentage_laid_off is null;

select *
from layoffs_staging2
where industry is null
or industry = '';

select *
from layoffs_staging2
where company LIKE 'Bally%';

Select t1.industry, t2.industry
From layoffs_staging2 t1
JOIN layoffs_staging2 t2
	on t1.company = t2.company
    AND t1.location = t2.location
WHERE (t1.industry is null or t1.industry ='')
AND t2.industry is not null

update layoffs_staging2
set industry = null
where industry ='';


UPDATE layoffs_staging2 t1
join layoffs_staging2 t2
	on t1.company = t2.company
set t1.industry = t2.industry
WHERE (t1.industry is null)
AND t2.industry is not null;


DELETE
from layoffs_staging2
where total_laid_off is null
And percentage_laid_off is null;

ALTER TABLE layoffs_staging2
DROP column	row_num;

Select company, SUM(total_laid_off)
from layoffs_staging2
group by company
order by 2 desc;

Select country, SUM(total_laid_off)
from layoffs_staging2
group by country
order by 2 desc;


Select industry, SUM(total_laid_off)
from layoffs_staging2
group by industry
order by 2 desc;

Select YEAR(date) ,SUM(total_laid_off)
from layoffs_staging2
group by YEAR(date)
order by 1 desc;

SELECT country, COUNT(*) AS country_count
FROM layoffs_staging2
GROUP BY country
ORDER BY country_count DESC;

SELECT COUNT(DISTINCT country) AS number_of_countries
FROM layoffs_staging2;

SELECT 
    country,
    ROW_NUMBER() OVER (ORDER BY country) AS country_id
FROM (
    SELECT DISTINCT country
    FROM layoffs_staging2
) AS unique_countries;


Select industry,YEAR(date) , SUM(total_laid_off) as total_layoff_per_year
from layoffs_staging2
WHERE industry IN ('retail' AND 'consumer')
group by industry, YEAR(date)
order by industry, total_layoff_per_year desc;

select industry, country
from layoffs_staging2
where industry in ('transportation', 'travel')
group by industry, country;

Select distinct country,location, (count(industry))
from layoffs_staging2
where industry in ('Transportation')
group by country, location,industry;

WITH Company_year (company, years, total_laid_off) as 
(
Select company, YEAR(date), SUM(total_laid_off)
FROM layoffs_staging2
group by company, YEAR(date)
), Company_Year_Rank as
(Select *,
DENSE_RANK() OVER (Partition by years Order by total_laid_off DESC) As ranking
From Company_year
Where years is not null
)
select *
from Company_Year_Rank
Where Ranking <= 5;






