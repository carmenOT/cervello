Covid-19 pandEmic impacts on mental health Related conditions Via multi-database nEtwork: a LongitutinaL Observational study (CERVELLO)
=================
<img src="https://img.shields.io/badge/Study%20Status-Repo%20Created-lightgray.svg" alt="Study Status: Repo Created">

- Analytics use case(s): **Characterization**
- Study type: **Clinical Application**
- Tags: **OHDSI, psychiatryWG, mental-health, covid-19**
- Study lead: **Carmen Olga Torre, MSc, IQVIA** //
              **Hao Luo, PhD, University of Hong Kong, Hong Kong**
- Study lead forums tag: **[CarmenOT](https://forums.ohdsi.org/u/carmenot)**, **[HaoLuo]**
- Study start date: **January 21, 2021**
- Study end date: **March 2021**
- Protocol: **[Word Doc](https://https://github.com/carmenOT/cervello/documents/Protocol.docx)**

**Objectives:**
Describe the baseline demographic, clinical characteristics, treatments and outcomes of interest among individuals with mental health conditions during the COVID-19 pandemic overall and stratified by sex, age, race and specific comorbidities.

### FAQ

#### *What do I need to do to run the package?*
OHDSI study  repos are designed to have information in the README.md (where you are now) to provide you with instructions on how to navigate the repo. This package has two major components:
1. [CohortDiagnostics](http://www.github.com/ohdsi/cohortDiagnostics) - an OHDSI R package used to perform diagnostics around the fitness of use of the study phenotypes on your CDM. By running this package you will allow study leads to understand: cohort inclusion rule attrition, inspect source code lists for a phenotype, find orphan codes that should be in a particular concept set but are not, compute incidnece across calendar years, age and gender, break down index events into specific concepts that triggered then, compute overlap of two cohorts and compute basic characteristics of selected cohorts. This package will be requested of all sites. It is run on all available data. This allows us to understand how the study phenotypes perform in your database and identify any potential gaps in the phenotype definitions.
2. RunStudy - the characterization package to evaluate Target-Stratum-Feature pairings computing cohort characteristics and creating tables/visualizations to summarize differences between groups.

#### *I don't understand the organization of this Github Repo.*
The study repo has the following major pieces:
- `R` folder = the folder which will provide the R library the scripts it needs to execute this study
- `documents` folder = the folder where you will find study documents (protocols, 1-sliders to explain the study, etc)
- `extras` folder = the folder where we store a copy of the instructions (called `CodeToRun.R`) below and other files that the study needs to do things like package maintenance or talk with the Shiny app. Aside from `CodeToRun.R`, you can largely ignore the rest of these files.
- `inst` folder = This is the "install" folder. It contains the most important parts of the study: the study cohort JSONs (analogous to what ATLAS shows you in the Export tab), the study settings, a sub-folder that contains information to the Shiny app, and the study cohort SQL scripts that [SqlRender](https://cran.r-project.org/web/packages/SqlRender/index.html) will use to translate these into your RDBMS.

Below you will find instructions for how to bring this package into your `R`/ `RStudio` environment. Note that if you are not able to connect to the internet in `R`/ `RStudio` to download pacakges, you will have to pull the [TAR file](https://github.com/carmenOT/cervello/archive/master.zip). 

#### *What should I do if I get an error when I run the package?*
If you have any issues running the package, please report bugs / roadblocks via [GitHub Issues](https://github.com/ohdsi-studies/Covid19CharacterizationCharybdis/issues) on this repo. Where possible, we ask you share error logs and snippets of warning messages that come up in your `R` console. You may also attach screenshots. Please include the RDMBS (aka your SQL dialect) you work on. If possible, run `traceback()` in your `R` and paste this into your error as well. The study leads will triage these errors with you.

#### *What should I do when I finish?*
If you finish running a study package, send the file ```diagnosticsExport/Results_<DatabaseId>.zip``` in the output folder to the study coordinator [Carmen O. Torre](mailto:carmenolga.torre@iqvia.com). The study team will be in touch within 24 hours to acknowledge receipt of your results and review results. If there are no issues, the results will be pushed to the RShiny app. If any errors occur in this process, the study lead will communicate with you and work to resolve this.

## Package Requirements
- A database in [Common Data Model version 5](https://github.com/OHDSI/CommonDataModel) in one of these platforms: SQL Server, Oracle, PostgreSQL, IBM Netezza, Apache Impala, Amazon RedShift, or Microsoft APS.
- R version 3.5.0 or newer
- On Windows: [RTools](http://cran.r-project.org/bin/windows/Rtools/)
- [Java](http://java.com)
- Suggested: 25 GB of free disk space

## How to Run the Study
1. In `R`, you will build an `.Renviron` file. An `.Renviron` is an R environment file that sets variables you will be using in your code. It is encouraged to store these inside your environment so that you can protect sensitive information. Below are brief instructions on how to do this:

````
# The code below makes use of R environment variables (denoted by "Sys.getenv(<setting>)") to 
# allow for protection of sensitive information. If you'd like to use R environment variables stored
# in an external file, this can be done by creating an .Renviron file in the root of the folder
# where you have cloned this code. For more information on setting environment variables please refer to: 
# https://stat.ethz.ch/R-manual/R-devel/library/base/html/readRenviron.html
#
# Below is an example .Renviron file's contents: (please remove)
# the "#" below as these too are interprted as comments in the .Renviron file:
#
#    DBMS = "postgresql"
#    DB_SERVER = "database.server.com"
#    DB_PORT = 5432
#    DB_USER = "database_user_name_goes_here"
#    DB_PASSWORD = "your_secret_password"
#    FFTEMP_DIR = "E:/fftemp"
#    USE_SUBSET = FALSE
#
# The following describes the settings
#    DBMS, DB_SERVER, DB_PORT, DB_USER, DB_PASSWORD := These are the details used to connect
#    to your database server. For more information on how these are set, please refer to:
#    http://ohdsi.github.io/DatabaseConnector/
#
#    FFTEMP_DIR = A directory where temporary files used by the FF package are stored while running.
#
#    USE_SUBSET = TRUE/FALSE. When set to TRUE, this will allow for runnning this package with a 
#    subset of the cohorts/features. This is used for testing. PLEASE NOTE: This is only enabled
#    by setting this environment variable.
#
# Once you have established an .Renviron file, you must restart your R session for R to pick up these new
# variables. 
````

2. To install the study package (which will build a new R library for you that is specifically for `Covid19CharacterizationCharybdis`), type the following into a new `R` script and run. You can also retrieve this code from `extras/CodeToRun.R`.

````
# Prevents errors due to packages being built for other R versions: 
Sys.setenv("R_REMOTES_NO_ERRORS_FROM_WARNINGS" = TRUE)
# 
# First, it probably is best to make sure you are up-to-date on all existing packages.
# Important: This code is best run in R, not RStudio, as RStudio may have some libraries
# (like 'rlang') in use.
#update.packages(ask = "graphics")

# When asked to update packages, select '1' ('update all') (could be multiple times)
# When asked whether to install from source, select 'No' (could be multiple times)
#install.packages("devtools")
#devtools::install_github("carmenOT/cervello")
````

*Note: When using this installation method it can be difficult to 'retrace' because you will not see the same folders that you see in the GitHub Repo. If you would prefer to have more visibility into the study contents, you may alternatively download the [TAR file](https://github.com/carmenOT/cervello/archive/master.zip) for this repo and bring this into your `R`/`RStudio` environment. An example of how to call ZIP files into your `R` environment can be found in the [The Book of OHDSI](https://ohdsi.github.io/TheBookOfOhdsi/PopulationLevelEstimation.html#running-the-study-package).*

3. Great work! Now you have set-up your environment and installed the library that will run the package. You can use the following `R` script to load in your library and configure your environment connection details:


