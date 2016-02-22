# go-tablib: format-agnostic tabular dataset library

[![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)][license]
[![Go Documentation](http://img.shields.io/badge/go-documentation-blue.svg?style=flat-square)][godocs]
[![Go Report Card](https://goreportcard.com/badge/github.com/agrison/go-commons-lang)][goreportcard]

[license]: https://github.com/agrison/go-tablib/blob/master/LICENSE
[godocs]: https://godoc.org/github.com/agrison/go-tablib
[goreportcard]: https://goreportcard.com/report/github.com/agrison/go-tablib

Tablib is a format-agnostic tabular dataset library, written in Go.
This is a port of the famous Python's [tablib](https://github.com/kennethreitz/tablib) by Kenneth Reitz.

Output formats supported:

* JSON (Sets + Books)
* YAML (Sets + Books)
* XML (Sets + Books)
* TSV (Sets)
* CSV (Sets)

## Overview

### tablib.Dataset
A Dataset is a table of tabular data. It may or may not have a header row. They can be build and manipulated as raw Python datatypes (Lists of tuples|dictionaries). Datasets can be exported to JSON, YAML, CSV, TSV, and XML.

### tablib.Databook
A Databook is a set of Datasets. The most common form of a Databook is an Excel file with multiple spreadsheets. Databooks can be exported to JSON, YAML and XML.

## Usage

Creates a dataset and populate it:

```go
ds := NewDataset([]string{"firstName", "lastName"})
```

Add new rows:
```go
ds.Append([]interface{}{"John", "Adams"})
ds.AppendValues("George", "Washington")
```

Add new columns:
```go
ds.AppendColumn("age", []interface{}{90, 67})
ds.AppendColumnValues("sex", "male", "male")
```

Add a dynamic column, by passing a function which has access to the current row, and must
return a value:
```go
func lastNameLen(row []interface{}) interface{} {
	return len(row[1].(string))
}
ds.AppendDynamicColumn("lastName length", lastNameLen)
ds.CSV()
// >>
// firstName, lastName, age, sex, lastName length
// John, Adams, 90, male, 5
// George, Washington, 67, male, 10
```

Delete rows:
```go
ds.DeleteRow(1) // starts at 0
```

Delete columns:
```go
ds.DeleteColumn("sex")
```

## Filtering

You can add **tags** to rows by using a specific `Dataset` method. This allows you to filter your `Dataset` later. This can be useful to separate rows of data based on arbitrary criteria (e.g. origin) that you don’t want to include in your `Dataset`.
```go
ds := NewDataset([]string{"Maker", "Model"})
ds.AppendTagged([]interface{}{"Porsche", "911"}, "fast", "luxury")
ds.AppendTagged([]interface{}{"Skoda", "Octavia"}, "family")
ds.AppendTagged([]interface{}{"Ferrari", "458"}, "fast", "luxury")
ds.AppendValues("Citroen", "Picasso")
ds.AppendTagged([]interface{}{"Bentley", "Continental"}, "luxury")
```

Filtering the `Dataset` is possible by calling `Filter(column)`:
```go
luxuryCars := ds.Filter("luxury").CSV()
// >>>
// Maker,Model
// Porsche,911
// Ferrari,458
// Bentley,Continental
```

```go
fastCars := ds.Filter("fast").CSV()
// >>>
// Maker,Model
// Porsche,911
// Ferrari,458
```

## Sorting

Datasets can be sorted by a specific column.
```go
ds := NewDataset([]string{"Maker", "Model", "Year"})
ds.AppendValues("Porsche", "991", 2012)
ds.AppendValues("Skoda", "Octavia", 2011)
ds.AppendValues("Ferrari", "458", 2009)
ds.AppendValues("Citroen", "Picasso II", 2013)
ds.AppendValues("Bentley", "Continental GT"}, 2003)

ds.Sort("Year").CSV()
// >>
// Maker, Model, Year
// Bentley, Continental GT, 2003
// Ferrari, 458, 2009
// Skoda, Octavia, 2011
// Porsche, 991, 2012
// Citroen, Picasso II, 2013
```

## Loading

## JSON
```go
ds, _ := LoadJSON([]byte(`[
  {"age":90,"firstName":"John","lastName":"Adams"},
  {"age":67,"firstName":"George","lastName":"Washington"},
  {"age":83,"firstName":"Henry","lastName":"Ford"}
]`))
```

## YAML
```go
ds, _ := LoadYAML([]byte(`- age: 90
  firstName: John
  lastName: Adams
- age: 67
  firstName: George
  lastName: Washington
- age: 83
  firstName: Henry
  lastName: Ford`))
```

## Exports

### JSON
```go
json, _ := ds.JSON()
fmt.Printf("%s\n", json)
// >>>
// [{"age":90,"firstName":"John","lastName":"Adams"},{"age":67,"firstName":"George","lastName":"Washington"},{"age":83,"firstName":"Henry","lastName":"Ford"}]
```

### XML
```go
xml := ds.XML()
fmt.Printf("%s\n", xml)
// >>>
// <dataset>
//   <row>
//     <age>90</age>
//     <firstName>John</firstName>
//     <lastName>Adams</lastName>
//   </row>  <row>
//     <age>67</age>
//     <firstName>George</firstName>
//     <lastName>Washington</lastName>
//   </row>  <row>
//     <age>83</age>
//     <firstName>Henry</firstName>
//     <lastName>Ford</lastName>
//   </row>
// </dataset>
```

### CSV
```go
csv, _ := ds.CSV()
fmt.Printf("%s\n", csv)
// >>>
// firstName,lastName,age
// John,Adams,90
// George,Washington,67
// Henry,Ford,83
```

### TSV
```go
tsv, _ := ds.TSV()
fmt.Printf("%s\n", tsv)
// >>>
// firstName  lastName  age
// John Adams 90
// George Washington  67
// Henry  Ford  83
```

### YAML
```go
yaml, _ := ds.YAML()
fmt.Printf("%s\n", yaml)
// >>>
// - age: 90
//   firstName: John
//   lastName: Adams
// - age: 67
//   firstName: George
//   lastName: Washington
// - age: 83
//   firstName: Henry
//   lastName: Ford
```

## Installation

```bash
go get github.com/agrison/go-tablib
```

## TODO

* Loading
* Support DBF, XLS, HTML, LATEX, ...

## Contribute
PRs more than welcomed, come and join :)
