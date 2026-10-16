# Data Literacy Field Notes & Terminal Log

**The scenario:** A climate-and-energy nonprofit wants to use this
country-level energy and emissions dataset to help decide where to direct
community-protection and climate-support efforts — but the file just
landed with zero documentation. Before anyone can trust a decision built
on top of it, you need to figure out what's actually in it — what each
column really holds, and anything that looks off. That's what this whole
file walks through: the real, everyday first pass any analyst runs on an
unfamiliar file, and the real answers you need before this data is safe
to act on.

Dataset: `../data/co2_emissions_2022.csv` (see `../data/SOURCE.md`). Each
section below describes what you're trying to find out — **figuring out
which command (and which flags) actually gets you there is part of what
this project tests**, so the exact command isn't given. Where a command
belongs, you'll see a `# TODO:` comment describing the goal instead of a
ready-to-run command; replace the comment with the real command you used.
Paste the **real output** you got underneath, then write your answer based
on what you actually saw, not what you'd expect. Work through this in
order; open the raw file before anything else.

Run all commands from the repo root — the top-level folder you land in
after cloning *your own* copy of this repo (`git clone ...` then `cd
your-repo-name`, see `README.md` Step 3; not this template) — or `cd` into
`data/` first and drop the `data/` prefix — your choice, just be
consistent.

**This one file is your whole record** — the commands you ran, their real
output, and your written answers, all together in the order you actually
did the work. There's no separate log file to keep in sync with this one;
whenever you write a command down anywhere in this file, use a fenced code
block tagged `bash`, like this:

````
```bash
git status
```
````

Three backticks, the word `bash`, your command, three backticks to close —
it renders with syntax highlighting and makes the command copy-pasteable.

## Setup log

Before jumping into the dataset, log the commands you actually ran to set
this repo up (see `README.md`) — `git init`/clone, creating your
`.gitignore`, your first commit. Add to this as you go, don't
reconstruct it from memory at the end.

```bash
git clone https://github.com/The-Marcy-Lab-School/DA_Module0_Project_Foundations_STUDENT.git
cd <repo>
```

## 1. First look at the raw file

Before trusting anything about this file's structure, you want a quick,
safe peek at it — not the whole 254-row file, just the **first 5 lines**,
enough to see the shape of it without scrolling past everything.

```bash
cd data
head -n 5 co2_emissions_2022.csv
```

**Output:**
 

country,iso_code,year,population,gdp,co2

Afghanistan,AFG,2022,40578847,53303471617,10.170

Africa,,2022,1445862695,,1459.114

Africa (GCP),G,2022,,,1459.103

Albania,ALB,2022,2827614,36171005594,4.498

**What I notice:** 

I noticed that Africa was missing some data values when looking at the first 5 rows, specifically the iso_code and GDP values. Also, on the 4th row, data for iso_code, population, and GDP are missing. How come these values aren't here? What are the differences between Africa and Africa (GCP)?

## 2. Column names

Next you want to see just the column names by themselves — one per line is
a lot easier to scan than one long comma-separated row.

```bash
head -n 1 co2_emissions_2022.csv

awk -F',' '$1 == "United States"' co2_emissions_2022.csv 
```

**Output:**
country,iso_code,year,population,gdp,co2

United States,USA,2022,341534041,19493170182843,5055.403

## 3. Column-by-column data type table

For each column, you want to preview real values — **the first 6 values**
of just that one column — without scrolling through every field on every
row. Column numbers for reference: `country`=1, `iso_code`=2, `year`=3,
`population`=4, `gdp`=5, `co2`=6.

```bash
 cut -d',' -f1 co2_emissions_2022.csv | head -n 6

cut -d',' -f2 co2_emissions_2022.csv | head -n 6

cut -d',' -f3 co2_emissions_2022.csv | head -n 6

cut -d',' -f4 co2_emissions_2022.csv | head -n 6

cut -d',' -f5 co2_emissions_2022.csv | head -n 6

cut -d',' -f6 co2_emissions_2022.csv | head -n 6
```

Fill in the table from what you actually saw — not a guess from the column
name alone:

| Column | Inferred type | How I can tell |
|---|---|---|
| Country | String | Value made up of words |
| iso_code | String | Values that only contains only letters |
| Poplulation | Intger | Values that is specifed as a whole number |
| GDP | Intger | Values are specifed as whole numbers without any decimals |
| Co2 | Float | Values that are numebers that contains a decimal value |

**In your own words:**
An array is a one-dimensional version of a table that holds a specific value type, such as a single row of a dataset or an entire column. Whereas a table is a structured dataset that contains both rows and columns. An example of an array is when we use the cut command to pull specific column data as output.    


 



## 4. Structured or unstructured?

This dataset is structured due to a constant data type within each column. Because there is no mix of different data types in the columns, we can reliably compare the different sets of values.


## 5. Before you trust this data

Your first look (see section 1) turned up a row with a suspiciously
**blank** `iso_code`. Before you build anything on this data, you want to
know exactly how many of the 254 rows share that blank — is it a one-off,
or a real pattern worth flagging before anyone draws conclusions from it?
You may need more than one command for this — piping (`|`) feeds one
command's output into the next as input.

```bash
cut -d',' -f2 co2_emissions_2022.csv | grep '^$' | wc -l
```

**Output:**
36

```bash
cut -d',' -f5 co2_emissions_2022.csv | grep '^$' | wc -l

```

**Output:**
89

List **at least 2** specific things you'd want to know about how this
dataset was collected, before trusting any conclusion drawn from it — ground
at least one in the counts you just found.

**My 2 concerns:**
1. Why is there missing population values for some countries?
2. How is the co2 consumption accounted for if we don't have a population value?

## 6. Data-quality question for the source

Take the **most specific and checkable** of the two concerns above, and
phrase it as a real question you'd send to the data's source/maintainer. A
good test: could they actually answer it with a concrete yes/no or fact —
not "is this data good?"

When inspecting this dataset, I realized that some rows don't have population values. When looking into CO2 values, should I disregard these incomplete rows, or are the CO2 values still accurate for countries that are missing this important data? 

## 7. Something you're actually curious about

Different question from #6 — that one was about checking the data's
trustworthiness. This one's about you: what's something you're genuinely
curious to explore using this data? Pick two specific countries you're
curious to compare, and look up their full rows.

```bash
awk -F',' '$1 == "United States"' co2_emissions_2022.csv

awk -F',' '$1 == "Thailand"' co2_emissions_2022.csv

OR

awk -F',' '$1 == "United States" || $1=="Thailand"' co2_emissions_2022.csv
```

**Output:**
Thailand,THA,2022,71735321,1124143758569,272.573

United States,USA,2022,341534041,19493170182843,5055.403

**My question:** How does the vast difference in GDP between the two countries correlate with their total carbon emissions?
## 8. AI-literacy reflection

Name **2 concrete risks** of trusting an AI assistant's output about this
dataset (or any data question) without checking it yourself. Then state
**one specific personal rule** for when you'll double-check an AI-provided
fact before relying on it professionally — specific enough that you could
actually follow it, not "I'll be careful."

**Risks:**
1. Over relying on AI can make you a complacent analyst, one who stops questioning results and loses the judgment needed to catch errors.
2. Being fed the wrong answer with AIs full confidence.

**My rule:** Always inspect the dataset yourself, without AI. Confirm the row count and work from a small random sample first. Only then use AI to scale the work to your needs, and verify the results after every step.

## 9. Something that went wrong (and how you figured it out)

Somewhere in this project you're going to run a command that either throws
an error or gives you output that isn't what you expected — everyone hits
this at least once. Paste the actual command and what actually happened
(not a paraphrase), then explain what that told you and how it led to the
fix — not "I googled it and it worked."

```bash
# TODO: a command that errored, or gave you unexpected results
```

**What happened:**
_TODO: the actual error message, or the unexpected output you got_

**What it told me and how I fixed it:** _TODO_
