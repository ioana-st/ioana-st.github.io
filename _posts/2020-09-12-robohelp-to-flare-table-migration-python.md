---
layout: single
title: "RoboHelp to Flare: Migrating tables using Python"
date: 2020-09-12 17:41:28 +0300
classes: wide
categories: robohelp flare python
---
When we began migrating our large documentation set from RoboHelp to Flare, it was clear from the start that the main problem would be the migration of the tables.

## The problem
To start with, both RoboHelp and Flare handle tables in non-standard ways... but in _different_ non-standard ways.

* RoboHelp just doesn't insert `<thead>` and `<tbody>` tags at all. The header row is a `<tr>` with a different class.
* Flare-style tables are defined in a separate CSS file, use a specific syntax in the `<table>` tag, and the header row formatting cannot be applied if the table doesn't have a `<thead>`. (You can use standard HTML tables, but then you can't benefit from the Flare table editing GUI.) As an unwanted bonus, Flare tables don't support `nth-child`, so style definitions that would be 30 rows in a standard CSS have 600 rows in Flare... but at least Flare auto-applies all the classes in the Franken-CSS once the `<table>` is declared correctly. (This will be important.)

## The HTML analysis
I knew we had thousands and thousands of tables, so an automatic or at least semi-automatic solution was mandatory, so I planned to use the BeautifulSoup4 Python library to transform them into the required format.

We had documentation for several software products, each with its particularities, so I started by analyzing each of them.


| Product | Analysis | Solution |
| ------- | -------- | -------- | 
| Product 1 | Mostly a single table style used. <br/> <br/> 90% of the tables used the same style - a header row and banded body rows. The other 10% consisted of tables that didn't _need_ to be tables (they had been tables in FrameMaker, long time ago, and should have been converted to code blocks).| If the tables had proper HTML headers, we could use the **Apply to all tables** option in the Flare GUI to apply a table style to all tables. Because the rows looked the same in both table styles, a single Flare style would be enough (if a table doesn't have a `<thead>`, all rows, including the first, have body row formatting). <br/> <br/> The 10% tables-that-should-have-been-code-blocks would be handled manually, in a separate step. |
| Product 2 | Mostly two styles used. <br/> <br/> 70% of the tables used the style with headers and banded rows, while the remaining 30% used the same type of rows, but no header. | If the tables had proper HTML headers, we could use the **Apply to all tables** option in the Flare GUI to apply a table style to all tables. |
| Product 3 | A mix of several styles. <br/> <br/> 30% of the tables used the style with headers and banded rows, 30% of the tables were "invisible" (remnant of FrameMaker, used strictly for alignment), 20% of the tables were 1-row tables with a background, used to emphasize mathematical formulas, 10% of the tables used the style with banded rows and no header and 10% were random tables that probably should have been mapped to one of the other styles. <br/> <br/> Most tables had been badly imported from FrameMaker and all the formatting was inline. | More work was needed here. I decided to do it in three stages: <br/> <br/> 1. Identify each type of table based on its formatting.<br/> <br/> 2. Add `<thead>` (where needed) and `<tbody>` (on all). <br/> <br/> 3. Apply a specific Flare table style depending on the table class. |

## The battle plan
It was clear that Product 3 had the most complicated use case, and that its step 2 could easily be applied to the other products as well.

I ended up creating three Python scripts, one for each step. They are available [on GitHub](https://github.com/ioana-st/python_work/tree/master/robohelp_to_flare_table_migration), with comments. 
* The first one identifies each table through a series of conditions and applies the appropriate `class`.
* The second one adds headers and bodies:
    * It selects all tables with the `class` that indicates that they need  header rows, wraps the first row  in `<thead>`, replaces the `<td>` with `<th>` in the header row, and wraps the rest of the rows in `<tbody>`.
    * It selects all tables with the `class` that indicates that they don't need header rows and wraps them in `<tbody>`.
* The third one does the Flare-specific part: it creates the correct relative path to the Flare table CSS file (depending on the location of the topic in the folder hierarchy), then it inserts the correct Flare syntax into the `<table>` tag. Because Flare automatically applies all other classes to the rest of the table elements (`<td>`, `<tr>`), this replacement is enough - Flare will simply do the rest when you build the HTML5 target. 

## The results
The scripts ended up saving us many hours of work and we now have a solid foundation for the future, with no more inline formatting. 

They did fail to correctly identify some tables, but we decided it's an acceptable trade-off.

## The disclaimers
I am not a software developer and I am very new to Python, so the scripts did the job, but they are far from elegant. I made them available on GitHub partly as inspiration for others struggling with the same problem, partly as the "technical" part of my technical writing portfolio.

The scripts: 
* Don't work if there are any special characters in the source files. I tried to fix the encoding issues, failed, and decided to simply replace them all with standard Latin unicode characters before I ran the scripts. 
* Are not generic and only work on our specific document structure.
* Don't have any kind of error handling.

