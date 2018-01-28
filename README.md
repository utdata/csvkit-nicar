Csvkit, a command-line tool for reporters
=========================================

Ever struggled with large data sets? Or need to quickly join or merge data sets without benefit of a database? We will show you how to harness the awesome power of CSVKit to wrangle large datasets on the command line. It's easy to use, fast and powerful. It's a must in every data journalist's tool box.
 
This session is good for: People who want a solution for working with multiple​​ CSV files without having to open Excel or to join or merge files without a database.

Created for the 2018 CAR Conference March 8-11, 2018 in Chicago. By [Christian McDonald](http://github.com/critmcdonald).

This lecture assumes use of Macintosh with csvkit installed globally using Python 3.6.

## Our scenario

We have CSV files of [Mixed Beverage Gross Receipts](https://data.texas.gov/Government-and-Taxes/Mixed-Beverage-Gross-Receipts/naix-2893) for five different counties that make up the MSA that includes Austin, Texas. The data convers the December 2017 reporting period.

We want to write a story that illustrates the top alcohol sellers in each of the five counties, and some summaries for the area as a whole. But there is a catch ... the county name is not in the file. We do, however, have a lookup table to match county ID with a name.

We'll use the command-line tool [csvkit](https://csvkit.readthedocs.io) to do this work. It can handle quickly some tasks that might otherwise take many manual steps and be more prone to human error. It is NOT a complete data analysis package, but it can get you some basic answers if that is all you need.

### Our outline

- learn some basic command-line commands to get around: pwd, cd, ls
- inspect one of our files to learn about it
- merge the five files into one
- join the merged file with the counties lookup file
- pare down the file so we have just the columns we need
- get some summary stats
- order rows to see top sellers
- filter rows to get top sellers for specific counties
- [MABYE] csvquery for sums in counties?

## Getting around the terminal

For those unfamiliar with "the command line", Terminal is an application that uses a "Command Line Interface" (cli) to communicate with your computer. The language it is using is called Bash, even though the tool we are talking to mostly will be csvkit, which is built in Python.

Confused? Don't worry about it ... you'll do fine. And no, you won't destroy the computer. It'll be ok.

NOTE: If you are reading this in the Markdown file, I'm using text inside of tick marks like `do this` to indicate you are type it into the terminal. DON'T INCLUDE THE TICK MARKS. If viewing on github, the commands are in red type.

### Launch the Terminal app.

  - It may be in the applications bar: A black box with `>_` inside it.
  - You can type command-space to get a search bar, and type in Terminal. Hit return.
  - If the type is really small, you can to command-+ to make it bigger.

### pwd - print working directory

Type `pwd` in terminal, and it will respond with something like:

/Users/username/Documents/path_to/where_you_are

This is important, because it can get confusing about where you are on the computer. Each of those "/" is a folder, and they are EXACTLY the same as the ones you normally browse through on your computer.

### cd - change directory

Now we are going to change our working directory to where we need to be. Because this is being written before the computers were built for the conference, the instructor will correct this for you in class. But let's pretend we know where we are going.

Type this into your terminal:

`cd /Users/nicar/Desktop/hands_on/csvkit-nicar2018/`

The first two segments find the logged in user, "nicar" in this case. If you were logged in as "nicar" and folder surfing on your computer, you would go to your Desktop, double-click on the "hands_on" folder, which would contain the "csvkit-nicar2018" folder, which you would then open.

What we've done instead is to "change directory" to go inside this folder in our terminal. This is where we'll stay for remainder of the class.

### ls - list

Now let's set what is inside this folder. Type this into your terminal:

`ls`

You should get in return something like this:

105.csv       28.csv
11.csv        Mixed_Beverage_Layout.pdf
227.csv       README.md
246.csv       counties.csv

This lists all the files in the folder in two columns. There are five .csv files that start with numbers, one called counties.csv, a pdf and this README.md file.

Many cli tools like this take an argument, or flag, that changes or extends the behaivor of the command. Try:

`ls -l`

You should get something like:

total 616
-rw-r--r--@ 1 christian  staff   20730 Jan 26 23:54 105.csv
-rw-r--r--@ 1 christian  staff    5066 Jan 26 23:54 11.csv
-rw-r--r--@ 1 christian  staff  184491 Jan 26 23:55 227.csv
-rw-r--r--@ 1 christian  staff   40543 Jan 26 23:55 246.csv
-rw-r--r--@ 1 christian  staff    1717 Jan 28 14:06 28.csv
-rw-r--r--@ 1 christian  staff   28583 Jan 26 21:55 Mixed_Beverage_Layout.pdf
-rw-r--r--  1 christian  staff    6152 Jan 28 14:59 README.md
-rw-r--r--@ 1 christian  staff    3886 Jan 26 22:33 counties.csv

This is the "long" listing that includes file permissions, owners, size, date, etc.

### wc - word count

Before we use the "wc" command, let me tell you about tab completion. When in the terminal, you often don't have to type in entire names of files or folders. You type the beginning of a file name, then hit "tab" to complete the rest. Try it with the 11.csv file.

Type in `wc 11` then hit tab. It should finish it out as:

`wc 11.csv`

Hit return and you get back:

      25     285    5066 11.csv

- "25" is the number of lines
- "285" is the number of words
- "5006" is the number of bytes

Later, we'll use "wc" with an "-l" flag to get onlty the number of lines: `wc -l 11.csv`

### head

Now we know there are 25 lines in this file, let's look at a little of it.

`head 11.csv`

(Don't forget tab complete!)

Hit return, and you'll see the first 10 lines of the "11.csv" file run across your screen.

Scroll back and look at the top, and you can see the headers, but I have a better way below.

### More terminal

This is all the bash/terminal basics we'll cover, because csvkit is our focus. I do have a work-in-progress [cli-tools](https://github.com/utdata/cli-tools) lesson if you want further study later.

## csvkit

Now we'll concentrate the power of csvkit.

### csvcut -n

[csvcut](https://csvkit.readthedocs.io/en/1.0.2/tutorial/1_getting_started.html#csvcut-data-scalpel) allows you to select, delete and reorder the columns in your CSV.

It also has a special flag "-n" to get the header names. Let's use:

`csvcut -n 11.csv`

We get:

``` text
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

The names kinda speak for themselves. I have included the [Mixed_Beverage_Layout.pdf](Mixed_Beverage_Layout.pdf) file in case you really want to look further.

Suffice to say, the columns we are interested in are the Location columns and the Receipts columns.

### csvstack

Before we look at these further, take my word for it and know that all five numbered files have the same header, though there are more rows in some files than others. The number of the file corresponds to the county it came from, but the county name is not in the data.

We want to combine all the files into a single new file before we join to get the county names.

"csvstack" merges like files on top of each other, or "stacks" them. At it's easist, it just takes the file names as an argument. 

`csvstack 11.csv 28.csv 105.csv 227.csv 246.csv`

Do that, and it will stack all the text and print it to your screen. Not particurly useful. What we need to do is redirect the output to a file, which we do with the ">" command. Let's call our files "stacked.csv":

`csvstack 11.csv 28.csv 105.csv 227.csv 246.csv > stacked.csv`

- Do `ls` to see that "stacked.csv" was created.
- Do `wc -l 227.csv` to see how many lines were in the "227" file.
- Do `wc -l stacked.csv` to see that the "stacked" file has more.

Congrats, you just merged five files into one with a single command!

### redirect commands, csvcut -c and csvlook

Now that we have a 1127 line file, it might make since to just look at the top when we want to explore it. We redirect output with stacked into another file, but we can also redirect output into another command, and in fact string many commands together. That's what we'll work through here.

- start with `head stacked.csv` to see the first 10 lines.

"csvcut -c" allows us to select specific columns by their name or position. "csvlook" makes our output pretty. Let's work through them.

- hit the up arrow on your keyboard to get back that last command, then add to it as follows: `head stacked.csv | csvcut -c Location_Name,Location_County`

You end up with:

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

We can make this easier to read by adding on "csvlook":

- hit the up arrow to get the last command, then add on "| csvlook" like this:

`head stacked.csv | csvcut -c Location_Name,Location_County | csvlook`

Now it looks like this:

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

So, we proved the Location_County column sucks. Let's get some county names.

### csvjoin

Before we talk about the join, let's take a quick peek at the "counties.csv" file so we understand what we are doing.

`head -n 15 counties.csv | csvlook`

gives you this:

``` text
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

We passed the "-n 15" into "head" to change the number of lines to 15, so we could see the value for Bastrop, whose code is 11. This matches our "11.csv" filename, so we are in the right track.

Now we know that "Location_County" in "stacked.csv" matches "code" in the "counties.csv". Now we can use "csvjoin":

`csvjoin -c "Location_County,code" stacked.csv counties.csv > mixbev.csv`

Let's break that down: We use csvjoin and use -c to pass in the column names "Location_County,code". Make sure there is no space after the comma. Then we give it the two file names, in the same order as the column names. We then redirect theoutput to a file called "mixbev.csv".

Peak at the columns names:

`csvcut -n mixbev.csv`

We've added to columns at the end:

```
 25: county
 26: code
```


## Ideas

* use mixbev
  - needs clean
  - needs cut for austin
  - needs join with county

* use wc -l to see number of rows
* use csvcut -n to get names?
* use csvjoin to get county?
* add csvgrep to get Travis? This takes a LONG time. Maybe start with Travis and find Austin? Or MSA?
* create a new file for travis
* get stats
* check for dupes
* fixed-width file (maybe just explain and show? Could use BIRLS)
* csvstack for multiple files (hotels or schools?)
* can I try csvquery (like with sum?)

## Steps

### csvcut

See the headers
`csvcut -n bastrop.csv`

### csvstack

`csvstack bastrop.csv caldwell.csv hays.csv travis.csv williamson.csv > stacked.csv`

### csvjoin

- look at headers for counties

`csvjoin -c "Location_County,id" stacked.csv counties.csv > mixbev.csv`

### csvstat

### csvgrep and csvsort

`csvgrep -c county -m Bastrop mixbev.csv | csvsort -c Total_Receipts -r | csvcut -c Location_Name,Total_Receipts | csvlook`

## Things we won't do that's cool

You can turn an Excel spreadsheet into a csv with [in2csv](https://csvkit.readthedocs.io/en/1.0.2/tutorial/1_getting_started.html#in2csv-the-excel-killer)

## Running Python in a browser

* python anywhere?
* jupyter notebooks

## Resources

https://source.opennews.org/articles/eleven-awesome-things-you-can-do-csvkit/