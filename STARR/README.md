# Working with STARR

The [STAnford Research Repository (STARR)](https://med.stanford.edu/starr-tools.html)
is the School of Medicine's project that supports all sorts of clinical 
research projects. There are lots of ways to get access to and use the data; the
best way for us is to use the R [BigQuery](https://cloud.google.com/bigquery/)
API. Our collaborator [Jonathon Chen](http://healthrexlab.com/) was kind enough
to add you to his IRB and give us access to Google Cloud Project.

We will be using a mix of [SQL](https://en.wikipedia.org/wiki/SQL) and R to
query STARR for deidentified EHR (and no PHI) to build a cohort of patients who
are likely to identify as trans* or non-binary. Then we can start summarizing
the trans* patient population seen at Stanford Health Systems *for the first
time!* We can also explore some ML clustering algorithms and potentially expand
our cohort, do some [propensity score
matching](https://en.wikipedia.org/wiki/Propensity_score_matching), and explore
issues of health equity for this community. These queries and cohort will be
essential for developing more inclusive data models for Sex and Gender as well
as algorithms that can label EHR with it to enable robust reseach that will 
inform health policy.

## Perquisites
To get started we need to install the BigQuery API in R. The features we need
are still in development so we'll install the API using the `devtools` package.

```r
install.packages("devtools")
devtools::install_github("rstats-db/bigrquery")
```

No lets do a quick check with a public dataset to make sure everything is
working before we get started with STARR.

The basic steps for using `bigrquery` are to:

0. Import the library
0. Specify the Google Cloud Project ID </br>
   (If you're not already using Google Cloud you'll have use the HealthRex 
   project)
0. Declare your SQL query as a string in R
0. Call `query_exec` to execute your query and store it as a data frame

```r
## Step 0
library("bigrquery")

## Step 1
projectId <- "mining-clinical-decisions"
```

For the next two steps were gonna use the [Iowa Liquor
Sales](https://data.iowa.gov/Sales-Distribution/Iowa-Liquor-Sales/m3tr-qhgy)
data set. It's just one table with about 3 million rows, which is pretty easy
to work with just with R, so BigQuery is a bit overkill for this task.  STARR,
on the other hand, is a rather complicated [relational
database](https://en.wikipedia.org/wiki/Relational_database) with decades of
patient encounters, diagnostic tests, procedures, etc. and it's impossible to
work with it without SQL.

Say we want to look yearly trends in the revenue generated from alcohol sales
in each county. We can easily do that by using SQL to create a table. To do
that, we `SELECT` the county, year, and `SUM` the sales revenue variables and
`GROUP BY` county and year. We can also tell SQL to `ORDER BY` the new total
revenue varible from high to low: `DESC` (decending order).
```r
## Step 2
query <- "SELECT 
		county, YEAR(date) AS year, SUM(sale_dollars) AS total_revenue
	  FROM
		`fh-bigquery:liquor.iowa`
	  GROUP BY
		county, year
	  ORDER BY
		total_revenue DESC"

## Step 3
df <- query_exec(query, project=projectId, useLegacySql=FALSE)
```

Hopefully you didn't get any errors. Now try making a line plot showing the 
trend of `total_revenue` over the years with a line for each `county`. I'd
suggest using `ggplot`. Make a pull request to put your code in this folder and
display your plot on this page (or you can use the Google Doc).

<p style="text-align: center">`Delete this and put your figuers here`</p>

Sweet, hopefully that was painless. Now we can get started using STARR!

## Accessing STARR
