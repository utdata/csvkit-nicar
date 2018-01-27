csvkit for journalists
======================

Created for CAR Conference 2018, Chicago

by [Christian McDonald](http://github.com/critmcdonald)

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

## Running Python in a browser

* python anywhere?
* jupyter notebooks

## Resources

https://source.opennews.org/articles/eleven-awesome-things-you-can-do-csvkit/