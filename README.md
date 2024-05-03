# SQL Data Cleaning

## Data Set (https://www.kaggle.com/datasets/swaptr/layoffs-2022)
---


##### Created Table as **layoffs** under **world_layoffs** database.

```
SELECT * FROM world_layoffs.layoffs;
```

##### First thing we want to do is create a staging table. This is the one we will work in and clean the data. We want a table with the raw data in case something happens

```
CREATE TABLE world_layoffs.layoffs_staging LIKE world_layoffs.layoffs;
```

```
INSERT layoffs_staging SELECT * FROM world_layoffs.layoffs;
```

##### Remove Duplicates

First let's check for duplicates

```
SELECT *
FROM world_layoffs.layoffs_staging;
```
```
SELECT *
FROM (
	SELECT company, location, industry, total_laid_off,percentage_laid_off,
`date`, stage, country, funds_raised_millions,
		ROW_NUMBER() OVER (
			PARTITION BY company, location, industry, total_laid_off,percentage_laid_off,
      `date`, stage, country, funds_raised_millions
			) AS row_num
	FROM 
		world_layoffs.layoffs_staging
) duplicates
WHERE 
	row_num > 1;
```


These are the ones we want to delete where the row number is > 1 or 2 or greater essentially. create a new column and add those row numbers in. Then delete where row numbers are over 2, then delete that column

```
SELECT *
FROM world_layoffs.layoffs_staging;
```

```
CREATE TABLE `world_layoffs`.`layoffs_staging2` (
`company` text,
`location`text,
`industry`text,
`total_laid_off` INT,
`percentage_laid_off` text,
`date` text,
`stage`text,
`country` text,
`funds_raised_millions` int,
row_num INT
);
```

```
INSERT INTO `world_layoffs`.`layoffs_staging2`
(`company`,
`location`,
`industry`,
`total_laid_off`,
`percentage_laid_off`,
`date`,
`stage`,
`country`,
`funds_raised_millions`,
`row_num`)
SELECT `company`,
`location`,
`industry`,
`total_laid_off`,
`percentage_laid_off`,
`date`,
`stage`,
`country`,
`funds_raised_millions`,
		ROW_NUMBER() OVER (
			PARTITION BY company, location, industry, total_laid_off,percentage_laid_off,
`date`, stage, country, funds_raised_millions
			) AS row_num
	FROM 
		world_layoffs.layoffs_staging;
```

Now that we have this we can delete rows were row_num is greater than 2

```
DELETE FROM world_layoffs.layoffs_staging2
WHERE row_num >= 2;
```
