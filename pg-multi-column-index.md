## How to build indexes in postgres correctly
### What index is better: two separate indexes or one multi-column index?
```sql
// multi-column index
CREATE INDEX ON table ("attr_1", "attr_2"); 
// separate indexes
CREATE INDEX ON table ("attr_1");
CREATE INDEX ON table ("attr_2");
```
There are a couple of variables in this question:
- table size
- column data types
- columns order in multi-index case

#### 1. When table is not big enough
Indexes will not be applied at all, so in case you know that a table is literally small - there is no reason to create indexes at all.

#### 2. When table size is has some not big amount of records and you try to filter by `FK`
Any other indexes can be skipped for this query because of performance reasons.

#### 3. When table is big enough to apply indexes for query
Multi-column index will perform better because of missing results merging with a `recheck` condition.
```sql
// multi-column index gives better index match: "Index Cond: ("attr_1"::text) = 'xxx'::text) AND ("attr_2" = 1))"
EXPLAIN SELECT * FROM table WHERE "attr_1" = @attr_1 AND "attr_2" = @attr_2;
// Index Cond: ("attr_1") = @attr_1) AND ("attr_2" = @attr_1))
```
#### Tip
Order your columns in such way to be reusable, the most relevant and usable column should go first:
```sql
CREATE INDEX ON table ("attr_1", "attr_2"); 
SELECT * FROM table WHERE "attr_1" = @attr_1; // hits the index
SELECT * FROM table WHERE "attr_2" = @attr_2; // does not hit the index
```
