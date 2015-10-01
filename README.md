## D3 Data Binding Issue

This Gist/[Blocks](http://bl.ocks.org/boeric/8489eeaf21a5f75f3a07) demonstrates a behavior difference in D3, when binding data using the original data object, vs. using a cloned version of the data object. In the example, the **updateTable** function is called from **update** function with the **original** data object drawing a table in the first div. It then is called with a **cloned** version of the data object, drawing this table in a second div.

The two tables are clearly different, as the second table (built using cloned data) does *NOT* contain the third row.

We first run the update function with the original data structure:

```
var data = [
  { table: "Table1", rows: [
      { table: "Table1", row: "Row1", data: "DataT1R1" },
      { table: "Table1", row: "Row2", data: "DataT1R2" }
    ]
  }
];

update(data);
```

**update** then runs **tableUpdate** twice, first with the original data in the top div, then with the cloned data in the lower div.

```
function update(data) {
  tableUpdate(data, tableDiv1, "Using Original Data"); // update first table
  tableUpdate(clone(data), tableDiv2, "Using Cloned Data"); // update second table
}
```


So far so good - both tables have the same contents (which can be verified by commenting out the next code segment and stopping there). 

We then add an array to the data structure (representing one new table row), and run the update function again:

```
data[0].rows.push({ table: "Table1", row: "Row3", data: "DataT1R3" });

update(data);
```

Again, as is evident from the output, the second table (which is fed the cloned data) does not update properly. It doesn't matter if the cloned data is created using a simple JSON.parse(JSON.stringify(data)) or a fancier algorithm. By the way, as is obvious from the source, there are no complex objects (like Date) in the data structure - just plain strings.

I believe the key functions are implemented properly. 

It appears that D3 somehow keeps track of the original data object and refuses to perform the data binding if the original object has changed (even though the data values are the same)?

Please see this Gist/Blocks [example](http://bl.ocks.org/boeric/e16ad218bc241dfd2d6e) that explores a more complete example of a dynamic array of tables (in this case the original data object is repeadedly used to modify the array of tables, not a cloned version of the data).
