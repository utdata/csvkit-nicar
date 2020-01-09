# Csvkit, a command-line tool for reporters

## These directions are at: <https://g.cmcdonald.com/csvkit-nicar>

Ever struggled with large data sets? Or need to quickly join or merge data sets without benefit of a database? We will show you how to harness the awesome power of CSVKit to wrangle large datasets on the command line. It's easy to use, fast and powerful. It's a must in every data journalist's tool box.

This session is good for: People who want a solution for working with multiple​​ CSV files without having to open Excel or to join or merge files without a database.

Originally created by [Christian McDonald](http://github.com/critmcdonald) for the [2018 CAR Conference](https://www.ire.org/conferences/nicar18/) in Chicago and updated for [2020 CAR Conference](https://www.ire.org/events-and-training/conferences/nicar-2020) in New Orleans.

This lecture assumes use of Macintosh with [csvkit](https://csvkit.rtfd.org) installed globally using Python 3.8, which is how the NICAR machines are set up. If you are seeing this at another time, you might [install miniconda](https://github.com/utdata/csvkit-nicar2018/blob/master/install_miniconda.md) or a python environment of your choice. Or follow the [csvkit instructions to install globally](https://csvkit.readthedocs.io/en/1.0.3/tutorial/1_getting_started.html#installing-csvkit).

In addition, if you are not on a NICAR machine, you will need the data. I recommend you download this repo, which will give you these directions as well as all the data. You will have to adjust directory path names from the instructions below since you folder structure won't the the same.

## Our scenario

We have CSV files of [Mixed Beverage Gross Receipts](https://data.texas.gov/Government-and-Taxes/Mixed-Beverage-Gross-Receipts/naix-2893) for five different counties that make up the Austin-Round Rock, TX Metropolitan Statistical Area. The data covers a one-month reporting period.

We want to write a story that illustrates the top alcohol sellers in each of the five counties, and some summaries for the area as a whole. But there are two catches: We have five files instead of one, and the county name is not in the file. We do, however, have a lookup table to match county ID with a name.

We'll use the command-line tool [csvkit](https://csvkit.readthedocs.io) to solve our challenges and get the answers we need for our story. It can quickly handle tasks that might otherwise be more prone to human error if done manually in Excel. It is NOT a complete data analysis package, but it can get you some basic answers if that is all you need.

### Our goals for this lesson

- Learn some basic command-line commands to get around: pwd, cd, ls
- Inspect one of our files to learn about it
- Merge multiple files into one
- Join the merged file with the counties lookup file
- Pare down the resulting file so we have just the columns we need
- Get some summary stats
- Order rows to see top sellers
- Filter rows to get top sellers for specific locations
- Get sums in counties and cities

## Getting around the terminal

For those unfamiliar with "the command line", Terminal is an application that uses a "Command Line Interface" (cli) to communicate with your computer. The language it is using is called Bash, even though the tool we are mostly using, csvkit, is written in Python.

Confused? Don't worry about it ... you'll do fine. And no, you won't destroy the computer. It'll be ok.

### About commands in this lesson

These directions are written in a syntax called Markdown. It looks pretty when we [look at it on github](https://github.com/utdata/csvkit-nicar2018/blob/master/README.md), but it might be confusing if you are looking at the README.md file in a text editor, and aren't familiar with Markdown.

- Code blocks are enclosed in backtick marks. This makes them stand out on Gitub. The backticks are not part of the command.
- To designate the input you type in vs output the computer spits out, I will use the `$` at the beginning to show you to type it in. But DON'T TYPE IN THE `$`. Or the backtick marks.

So, if `$ ls -l` is the command. Type just "ls -l" and then hit return.

### Launch the Terminal app

- It may be in the applications bar: A black box with `>_` inside it.
- Or you can click on the rocket-looking Launcher app in the applications bar and type in Terminal and then select it.
- Or, you can type command-space to get a search bar, and type in Terminal. Hit return.
- Or, you can look in the Applications folder, then inside the Utilities folder.

If you find the type is really small, you can to "command +" to make it bigger.

### pwd - print working directory

Type `$ pwd` in terminal, and it will respond with something like:

`/Users/username/`

This command is useful because it can get confusing about where you are on the computer when using Terminal. Each of those "/" designates a folder, and they are the same ones that you normally browse through on your computer.

### cd - change directory

Now we are going to change our working directory to where we need to be. 

Before we do this, let me tell you about tab completion. When in the terminal, you often don't have to type in entire names of files or folders. You can type the beginning of a file name, then hit "tab" to complete the rest. It saves you a lot of typing (and mistyping), especially when you have long directory names. We'll do this now:

Type this into your terminal:

`$ cd ~/Desk` and then hit tab on your keyboard. It should complete to:

`$ cd ~/Desktop`

The "~" takes you to the home directory of the logged in user. Then when we typed in `Desk` and hit tab, it completed the name of the only folder that matched the word, `Desktop`.

But, wait; there's more: Continue by adding `hands_` to the end and then tab:

`$ cd ~/Desktop/hands_`

Which will fill out `hands_on_classes/`. And finally add `csvkit` and hit tab.

`$ cd ~/Desktop/hands_on_classes/csvkit_a_command_line_tool_for_reporters_1160`

> The path above will be a little different as the class name and ID are updated for 2020.

Then hit return.

What we've done is "change directory" to go inside this folder in your terminal. This is where we'll work for remainder of the class.

This is the command-line version of going to your Desktop, double-clicking on the "hands_on_classes" folder, then again on the "csvkit_a_command_line_tool_for_reporters_1160" folder.

Now, let's make sure you really are where you should be. Use the `$ pwd` command and you should get are return similar to this:

```
$ pwd
/Users/Username/Desktop/hands_on_classes/csvkit_a_command_line_tool_for_reporters_1160/
```

### ls - list

Now let's set what is inside this folder. Execute this command in your terminal:

`$ ls`

You should get a return something like this:

``` txt
05.csv                   28.csv                    images
11.csv                   Mixed_Beverage_Layout.pdf install_miniconda.md
227.csv                  README.md
246.csv                  counties.csv
```

This lists all the files in the folder, but perhaps in multiple columns. There are five .csv files that start with numbers, one called counties.csv, a pdf and this README.md file.

Many cli tools like `ls` take an argument, or flag, that changes or extends the behavior of the command. Try:

`$ ls -l`

You should get something like:

``` txt
total 664
-rw-r--r--  1 christian  staff   20730 Mar  9 23:27 105.csv
-rw-r--r--  1 christian  staff    5066 Mar  9 23:27 11.csv
-rw-r--r--  1 christian  staff  184491 Mar  9 23:27 227.csv
-rw-r--r--  1 christian  staff   40543 Mar  9 23:27 246.csv
-rw-r--r--  1 christian  staff    1717 Mar  9 23:27 28.csv
-rw-r--r--  1 christian  staff   28583 Mar  9 23:27 Mixed_Beverage_Layout.pdf
-rw-r--r--  1 christian  staff   29075 Mar 10 07:43 README.md
-rw-r--r--  1 christian  staff    3886 Mar  9 23:27 counties.csv
drwxr-xr-x  3 christian  staff     102 Mar  9 23:27 images
-rw-r--r--  1 christian  staff    4576 Mar  9 23:27 install_miniconda.md
```

This is the "long" listing that includes file permissions, owners, size, date, etc.

### wc - word count

Type in `$ wc 11` then hit tab. It should finish it out as:

`$ wc 11.csv`

Hit return and you get back:

``` txt
      25     285    5066 11.csv
```

- "25" is the number of lines in the file
- "285" is the number of words in the file
- "5006" is the number of bytes in the file

We'll use this with the `-l` flag to see just the number of lines in files.

``` bash
$ wc -l 11.csv
      25 11.csv
```

### head

Now we know there are 25 lines in this file, let's look at the top of the file.

`$ head 11.csv`

(Use your tab complete!)

Hit return, and you'll see the first 10 lines of the "11.csv" file run across your screen.

Scroll back and look at the top of this return, and you can see the headers, but I have a better way below to see those.

### More terminal

This is all the bash/terminal basics we'll cover, because csvkit is our focus. I do have a  [cli-tools lesson](https://github.com/utdata/cli-tools) if you want further study later.

## csvkit

Now we'll concentrate the power of csvkit. If you want more information on any of the commands we use, read the documentation at <https://csvkit.readthedocs.io.>

### csvcut -n

[csvcut](https://csvkit.readthedocs.io/en/1.0.2/tutorial/1_getting_started.html#csvcut-data-scalpel) allows you to select, delete and reorder the columns by name or position.

It also has a special flag "-n" to get the header names. Let's use:

`$ csvcut -n 11.csv`

We get:

``` txt
  1: Taxpayer_Number
  2: Taxpayer_Name
  3: Taxpayer_Address
  4: Taxpayer_City
  5: Taxpayer_State
  6: Taxpayer_Zip
  7: Taxpayer_County
  8: Location_Number
  9: Location_Name
 10: Location_Address
 11: Location_City
 12: Location_State
 13: Location_Zip
 14: Location_County
 15: TABC_Permit_Number
 16: Inside/Outside_City_Limits
 17: Responsibility_Begin_Date
 18: Responsibility_End_Date
 19: Obligation_End_Date
 20: Liquor_Receipts
 21: Wine_Receipts
 22: Beer_Receipts
 23: Cover_Charge_Receipts
 24: Total_Receipts
```

The names of the columns kinda speak for themselves. I have included the [Mixed_Beverage_Layout.pdf](Mixed_Beverage_Layout.pdf) file in case you really want to look further.

Suffice to say, the columns we are interested here are the Location columns and the Receipts columns.

### csvstack

In the interest of time, take my word for it and know that all five numbered files are structured the same and have the same header, though there are more rows in some files than others. The number of the file corresponds to the county it came from, but the county name itself is not in the data.

We want to combine all the files into a single new file before we join with another file to get the county names.

The command [csvstack](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html#csvstack-combining-subsets) merges like files on top of each other, or "stacks" them. At its most basic, you just feed it filenames.

`$ csvstack 11.csv 28.csv 105.csv 227.csv 246.csv`

Do that, and it will stack all the text and print it to your screen. Not particularly useful. What we need to do is redirect the output to a file instead of our screen, which we do with the ">" command. Let's call our files "stacked.csv":

> Pro tip: You can use the up arrow in your Terminal to get your last command, and then edit it into a new command.

`$ csvstack 11.csv 28.csv 105.csv 227.csv 246.csv > stacked.csv`

- Do `$ ls` to see that "stacked.csv" was created.
- Do `$ wc -l 227.csv` to see how many lines were in the "227" file.
- Do `$ wc -l stacked.csv` to see that the "stacked" file has more: 1227 lines.

Congrats, you just merged five files into one with a single command!

NOTEWORTHY: If these files were named by their counties, like "bastrop.csv", you could use `csvstack -g bastrop.csv caldwell.csv > newfile.csv` to combine the files, and it would add a "group" column using the file names, so you would know where they came from.

### redirecting commands, csvcut -c and csvlook

Now that we have our one 1,227-line file, it might make sense to just look at the top when we want to explore it. Above you redirected output with "csvstack" into another file using `>`, but we can also redirect output into another command using `|`. In fact, we'll chain many commands together like this. That's what we'll work through here.

- start with `$ head stacked.csv` to see the first 10 lines.

Now let's talk about the command we'll add onto this. "csvcut -c" allows us to select specific columns by their name or position. "csvlook" makes our output pretty. Let's work through and add them one at a time.

- hit the up arrow on your keyboard to get back that last command, then add to it as follows:

`$ head stacked.csv | csvcut -c Location_Name,Location_County`

Hit return and you end up with:

``` text
Location_Name,Location_County
CABANA BEVERAGES INC,11
COPPER SHOT DISTILLERY,11
BONE SPIRITS LLC,11
LOBLOLLY SPIRITS,11
DERELICT AIRSHIP DISTILLERY,11
NEIGHBOR'S,11
URBAN COWBOY VENTURES,11
JOSEPH'S STEAKHOUSE,11
MORELIA MEXICAN CAFE,11
```

We can make this easier to read with "csvlook":

- hit the up arrow to get the last command, then add on "| csvlook" to get this:

`$ head stacked.csv | csvcut -c Location_Name,Location_County | csvlook`

Your output should be:

``` text
| Location_Name               | Location_County |
| --------------------------- | --------------- |
| CABANA BEVERAGES INC        |              11 |
| COPPER SHOT DISTILLERY      |              11 |
| BONE SPIRITS LLC            |              11 |
| LOBLOLLY SPIRITS            |              11 |
| DERELICT AIRSHIP DISTILLERY |              11 |
| NEIGHBOR'S                  |              11 |
| URBAN COWBOY VENTURES       |              11 |
| JOSEPH'S STEAKHOUSE         |              11 |
| MORELIA MEXICAN CAFE        |              11 |
```

So, we've made our output pretty enough to realize the Location_County column is near useless to us. Let's get some county names.

### csvjoin

Before we talk about csvjoin, let's take a quick peek at the "counties.csv" file so we understand what we are doing.

`$ head -n 15 counties.csv | csvlook`

gives you this:

``` txt
| id | county    | code |
| -- | --------- | ---- |
|  1 | Anderson  |    1 |
|  2 | Andrews   |    2 |
|  3 | Angelina  |    3 |
|  4 | Aransas   |    4 |
|  5 | Archer    |    5 |
|  6 | Armstrong |    6 |
|  7 | Atascosa  |    7 |
|  8 | Austin    |    8 |
|  9 | Bailey    |    9 |
| 10 | Bandera   |   10 |
| 11 | Bastrop   |   11 |
| 12 | Baylor    |   12 |
| 13 | Bee       |   13 |
| 14 | Bell      |   14 |
```

We passed the "-n 15" argument into "head" to change the number of lines to 15, so we could see the value for Bastrop, whose code is 11. I happen to know this matches our "11.csv" filename, so we are on the right track.

If we need to talk more about what a "join" is here, we can. If not, we'll move on.

Now we know that "Location_County" in "stacked.csv" matches "code" in the "counties.csv". Now we can use [csvjoin](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html#csvjoin-merging-related-data):

`$ csvjoin -c "Location_County,code" stacked.csv counties.csv > joined.csv`

Let's break that down: We use "csvjoin" with the "-c" to pass in the column names "Location_County,code", which are the two columns from each file we are joining on. Make sure there is no space after the comma. Then we give it the two file names, in the same order as the column names. We then redirect the output to a file called "joined.csv".

Peak at the columns names:

`$ csvcut -n joined.csv`

We've added two columns at the end:

``` txt
 25: county
 26: code
```

Take a look, using csvcut to get columns of interest:

`$ csvcut -c Location_Name,county joined.csv | csvlook`

At the bottom of the file we have the Williamson county name:

``` txt
| Y'ALL DOWN HOME CAFE                               | Williamson |
| LONE ROCK BAR                                      | Williamson |
| TUSK GRILL                                         | Williamson |
```

Congratulations, you've joined two files together without creating a database.

### csvcut -c for ranges

Let's show one last function with csvcut -- to select a range of columns to print.

If we look at all the column names:

`$ csvcut -n joined.csv`

We get this:

``` txt
  1: Taxpayer_Number
  2: Taxpayer_Name
  3: Taxpayer_Address
  4: Taxpayer_City
  5: Taxpayer_State
  6: Taxpayer_Zip
  7: Taxpayer_County
  8: Location_Number
  9: Location_Name
 10: Location_Address
 11: Location_City
 12: Location_State
 13: Location_Zip
 14: Location_County
 15: TABC_Permit_Number
 16: Inside/Outside_City_Limits
 17: Responsibility_Begin_Date
 18: Responsibility_End_Date
 19: Obligation_End_Date
 20: Liquor_Receipts
 21: Wine_Receipts
 22: Beer_Receipts
 23: Cover_Charge_Receipts
 24: Total_Receipts
 25: county
 26: code
```

Note the numbered columns. We really only need the Location_, Receipts_ and county column. Let's create a new file with just those columns, which we can do based on those column numbers. You can pick and choose rows by number, and/or string ranges together with a hyphen.

`$ csvcut -c 9-14,20-22,24-25 joined.csv > mixbev.csv`

That created our new file "mixbev.csv". Peek at the columns:

`$ csvcut -n mixbev.csv`

and you get this:

``` txt
  1: Location_Name
  2: Location_Address
  3: Location_City
  4: Location_State
  5: Location_Zip
  6: Location_County
  7: Liquor_Receipts
  8: Wine_Receipts
  9: Beer_Receipts
 10: Total_Receipts
 11: county

```

## Value of what you've learned so far

Those two major functions above, [csvstack](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html#csvstack-combining-subsets) and [csvjoin](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html#csvjoin-merging-related-data), can often be done in csvkit much quicker, easier and more precisely than in Excel. Especially when you have a spreadsheet with more than one sheet. I once [wrote a script](https://github.com/utdata/rwd-cli-tools/blob/master/lectures/csvkit/APD%20Demographics%20data%20pipeline.ipynb) to rip apart a dozen Excel files that each had eight sheets to combine them into one file. I never would've been able to copy/paste 96 sheets together without making an error. The script takes about 30 seconds to run.

There, you've gotten value from this class.

At this point, you might be able to open up "mixbev.csv" in Excel and do these next next steps faster, but sometimes you just need an answer or two and csvkit can do the trick, so let's explore more.

### csvstat - about your data

Now that we have our merged and joined file in "mixbev.csv", let's learn some more about it with [csvstat](http://csvkit.readthedocs.io/en/1.0.2/tutorial/2_examining_the_data.html#csvstat-statistics-without-code).

`$ csvstat mixbev.csv`

This will take a couple of seconds to run.

"csvstat" will give you basic information about the contents of each column in your data. It can help you learn about it, and can even give you some usable numbers for your story.

This is just a part of the output:

``` txt
10. "Total_Receipts"

  Type of data:          Number
  Contains null values:  False
  Unique values:         1109
  Smallest value:        0
  Largest value:         869,818
  Sum:                   70,354,620
  Mean:                  57,385.498
  Median:                35,139.5
  StDev:                 74,487.296
  Most common values:    0 (111x)
                         7,429 (2x)
                         9,467 (2x)
                         21,489 (2x)
                         42,057 (2x)
```

What can we learn from this?

- The sum of all alcohol sales in the Austin MSA in December 2017 was $70,354,620.
- The average was $57,385 and the median was $35,139. Though I'm not sure we would report those.
- This highest Total_Receipts value is "$869,818". We'll figure out who that is next.

### csvsort

Let's find out that top seller. [csvsort](http://csvkit.readthedocs.io/en/1.0.2/tutorial/2_examining_the_data.html#csvsort-order-matters) will sort your output to screen or redirect into another file, but it does not change the sort order of the original file.

Let's type in all in (or copy/paste) and run it, then break it down:

`$ csvsort -c Total_Receipts -r mixbev.csv | csvcut -c Location_Name,Location_Address,county,Total_Receipts | head | csvlook --max-column-width 20`

- "csvsort -c Total_Receipts -r" is our new command. "csvsort" is the command, and we are passing the "-c" flag and the name of a column to sort. The "-r" that follows says to reverse sort, so we have the largest number at the top. Journalists almost always want the largest number first. We have to do this step first (or at least before our "head" command) so it considers the whole file.
- We pipe this into "csvcut" passing in our name, address, county and receipts fields. This is just for nice output of fields we actually care about.
- We pass through to "head" to look at just the top 10 rows instead of all of them.
- We pass through to "csvlook" to make it pretty. I added the "--max-column-width 20" because some of the restaurant names are really long, and would break each row into multiple lines.

So, our result is this:
```
| Location_Name        | Location_Address     | county | Total_Receipts |
| -------------------- | -------------------- | ------ | -------------- |
| WLS BEVERAGE CO      | 110 E 2ND ST         | Travis |        869,818 |
| W HOTEL AUSTIN       | 200 LAVACA ST        | Travis |        581,798 |
| DH BEVERAGE LLC      | 604 BRAZOS ST        | Travis |        563,253 |
| ROSE ROOM/ 77 DEG... | 11500 ROCK ROSE AVE  | Travis |        527,145 |
| 400 BAR/CUCARACHA... | 400 E 6TH ST         | Travis |        466,017 |
| SAN JACINTO BEVER... | 98 SAN JACINTO BLVD  | Travis |        459,049 |
| THE ROOSEVELT ROO... | 307 W 5TH ST # 307B  | Travis |        436,238 |
| THE DOGWOOD DOMAIN   | 11420 ROCK ROSE A... | Travis |        419,467 |
| KUNG FU SALOON       | 11501 ROCK ROSE A... | Travis |        411,707 |
```

A quick [Google Maps search](https://goo.gl/maps/TdsMbkd3Jjm) tells me "110 E 2ND ST" is the J.W. Marriott hotel in downtown Austin. I can tell you they have led alcohol sales in the city of Austin each month since they opened in February 2015.

### csvgrep - filter rows of data through matching

All of the results above are in Travis County, which includes Austin. What about sales in the suburban counties (Bastrop, Caldwell, Hays, Williamson)?

We can use [csvgrep](http://csvkit.readthedocs.io/en/1.0.2/tutorial/2_examining_the_data.html#csvgrep-find-the-data-you-need) to filter rows based on a pattern or regular expression. We'll use a pattern, and we'll start with Bastrop.

We are basically using the same command as the last one (so you can arrow up to get that) and editing the beginning to include the "csvgrep" part. (Pro tip: After you arrow up to get the last command, you can then use control-a to put your cursor at the beginning of the command):

`$ csvgrep -c county -m "Bastrop" mixbev.csv | csvsort -c Total_Receipts -r | csvcut -c Location_Name,Location_Address,county,Total_Receipts | head | csvlook --max-column-width 20`

Let's break down the csvgrep part of this:

- "csvgrep" is the command.
- "county" is the column we want to search, so we pass that argument in with "-c county".
- We are using a pattern match, which is the "-m"  flag, and then then the word or phrase we are searching. It could be more than one word, like "MAIN ST".
- And of course the file we are searching.
- The rest is the same as the previous command.

To review all these commands in plain English, we are: Searching the mixbev.csv file by the county column for "Bastrop", then we sort on Total_Receipts in reverse order, then we cut for just the columns we need (and name them), then we get the top 10 results, then we make them look pretty, limiting the width of columns to 20 characters. Got it?

Now we have just Bastrop:

```
| Location_Name        | Location_Address     | county  | Total_Receipts |
| -------------------- | -------------------- | ------- | -------------- |
| LOST PINES BEVERA... | 575 HYATT LOST PI... | Bastrop |        290,195 |
| OLD TOWN RESTURAN... | 931 MAIN ST          | Bastrop |         94,519 |
| SP BASTROP THEATR... | 1600 CHESTNUT ST     | Bastrop |         43,273 |
| CHILI'S GRILL & BAR  | 734 HIGHWAY 71 W     | Bastrop |         42,059 |
| NEIGHBOR'S           | 601 CHESTNUT ST U... | Bastrop |         26,995 |
| RED ROCK STEAKHOU... | 101 S LENTZ ST       | Bastrop |         26,945 |
| VIEJO'S TACOS Y T... | 912 MAIN ST          | Bastrop |         23,349 |
| COACH Q'S SOCIAL ... | 303 MAIN ST          | Bastrop |         21,630 |
| MORELIA MEXICAN G... | 696 HIGHWAY 71 W ... | Bastrop |         20,665 |
```

We could exchange the word "Bastrop" for the other county names (or even part of the name, like "Will" to get Williamson).

But let's look at the data in another way. What are the top sellers outside of the city of Austin? The `csvgrep` also has `-i` which is the inverse of a search, i.e. everything but our match.

`$ csvgrep -c Location_City -m "AUSTIN" -i mixbev.csv | csvsort -c Total_Receipts -r | csvcut -c Location_Name,Location_Address,Location_City,Total_Receipts | head | csvlook --max-column-width 15`

```
| Location_Name   | Location_Add... | Location_City | Total_Receipts |
| --------------- | --------------- | ------------- | -------------- |
| LOST PINES B... | 575 HYATT LO... | LOST PINES    |        290,195 |
| CHUY'S          | 4911 183A TO... | CEDAR PARK    |        172,579 |
| CHUY'S ROUND... | 2320 N INTER... | ROUND ROCK    |        165,153 |
| JACK ALLEN'S... | 2500 HOPPE TRL  | ROUND ROCK    |        148,393 |
| MAVERICKS       | 1700 GRAND A... | PFLUGERVILLE  |        147,058 |
| THIRD BASE R... | 3107 S INTER... | ROUND ROCK    |        137,628 |
| RICK'S CABARET  | 3105 S INTER... | ROUND ROCK    |        137,563 |
| TWIN PEAKS R... | 100 LOUIS HE... | ROUND ROCK    |        134,566 |
| TRATTORIA LI... | 13308 FM 150 W  | DRIFTWOOD     |        121,814 |
```

We could write a sentence in our story like "Outside of Austin, the top alcohol sellers in the Austin MSA are the Hyatt Lost Pines resort, followed by the Chuy's restaurant chains in Cedar Park and Round Rock."


### csvsql - like peanut butter and chocolate

The [csvsql](http://csvkit.readthedocs.io/en/1.0.2/tutorial/3_power_tools.html?highlight=query#csvsql-and-sql2csv-ultimate-power) can help you import and export your csv to and from database formats. But it will also do a simple query of the data itself using the sqlite package built into csvkit. This is more just to show you can do it.

Let's sum the Receipt Totals sold by county:

`$ csvsql --query "select county,sum(Total_Receipts) as Total from mixbev group by 1;" mixbev.csv | csvlook`

Yields this result:

``` txt
| county     |      Total |
| ---------- | ---------- |
| Bastrop    |    720,659 |
| Caldwell   |     63,406 |
| Hays       |  3,274,053 |
| Travis     | 59,526,973 |
| Williamson |  6,769,529 |
```

or by city:

`$ csvsql --query "select Location_City,sum(Total_Receipts) as Total from mixbev group by 1;" mixbev.csv | csvlook`

Begets this:

``` txt
| Location_City    |      Total |
| ---------------- | ---------- |
| AUSTIN           | 58,165,637 |
| BASTROP          |    314,416 |
| BEE CAVE         |    407,434 |
| BEE CAVES        |    102,144 |
| BRIARCLIFF       |          0 |
| BUDA             |    398,563 |
| CEDAR PARK       |  1,739,899 |
| COUPLAND         |     14,760 |
| DEL VALLE        |    115,888 |
| DRIFTWOOD        |    234,303 |
| DRIPPING SPRINGS |    242,868 |
| ELGIN            |     41,419 |
| FLORENCE         |     21,512 |
| GEORGETOWN       |    913,469 |
| GRANGER          |     15,768 |
| HUTTO            |    105,223 |
| JONESTOWN        |          0 |
| KYLE             |    210,581 |
| LAGO VISTA       |     48,934 |
| LAKEWAY          |    723,034 |
| LEANDER          |    129,526 |
| LIBERTY HILL     |     76,366 |
| LOCKHART         |     63,386 |
| LOST PINES       |    290,195 |
| LULING           |         20 |
| MANOR            |     38,049 |
| PFLUGERVILLE     |    585,617 |
| RED ROCK         |     26,945 |
| ROLLINGWOOD      |     29,846 |
| ROUND ROCK       |  2,566,691 |
| SAN MARCOS       |  2,169,050 |
| SMITHVILLE       |     47,684 |
| SPICEWOOD        |    101,268 |
| SUNSET VALLEY    |    195,306 |
| TAYLOR           |     76,425 |
| VOLENTE          |      5,502 |
| WEST LAKE HILLS  |     91,059 |
| WIMBERLEY        |     45,833 |
```

## Things we didn't do

Believe it or not, we just scratched the surface of csvkit. The real power comes when you combine these functions together in scripts to do things you can't do by hand.

Some other uses we didn't really talk about:

- You can turn an Excel spreadsheet into a csv with [in2csv](https://csvkit.readthedocs.io/en/latest/tutorial/1_getting_started.html#in2csv-the-excel-killer)
- You can normalize files with [csvclean](https://csvkit.readthedocs.io/en/latest/scripts/csvclean.html).

And much more.

## Setting up your computer

At NICAR, the computers are set up for you. To run Python on your own computer, it is recommended you use a virtual environment, which is basically a sandbox where you can contain your Python code and not confuse your computer or other Python projects.

- I like [conda](https://conda.io/en/latest/) and I have basic directions to [install Miniconda here](install_miniconda.md). csvkit is a conda package, as is [agate](https://agate.rtfd.org), a related data analysis package.
- [pipenv](https://pipenv.readthedocs.io/en/latest/) is what is used on IRE computers and is a good choice. There are others.

## Running Python in a browser

We didn't get into here, but I like using [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) to run Python projects. There is a [bash kernel](http://slhogle.github.io/2017/bash_jupyter_notebook/) for Jupyter, which lets you run csvkit as if it were on the command line, but you don't have to do it one line at a time. That code is from 2017, so your mileage may vary.
