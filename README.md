# Lectute TWO 

## SQL 

###### Relational Model 

* Mathematical Abstraction 
* Langes to Query RMs 
* SQL 
* Relational Algebra 

###### Integrity Constraints 
* True over all instances of DB
* Specificed at init 
* Enforced by the DBMS 

###### Primay Key Constraints 
* Key for realtion 

###### Foreign Keys 
*  Relates to PK of another DB 

# Lectute THREE 

## SQL

###### Basic SQL Examples

```
SELECT S.sname 
FROM Sailors S
WHERE EXISTS (SELECT *
			  FROM Reserves R
			  WHERE R.bid=101 AND S.sid=R.sid);
```


Find sailors who have reserved all boats 

```
SELECT S.sname 
FROM Sailors S
WHERE NOT EXISTS ((SELECT B.bid 
				   FROM Boats B)
				  EXCEPT
				   (SELECT R.bid
				   	FROM Reserves R
				   	WHERE R.sid = S.sid)); 
```


###### Aggregate Ops 
* COUNT(*)
* COUNT([DISTINCT] A)
* SUM([DISTINCT] A)
* AVG([DISTINCT] A)
* MAX(A)
* MIN(A

*Followed by GROUP BY*



# Lecture FOUR 
## MORE SQL 

###### Null Values 
* Represents values unknown or inapplicable 
* Can specificy Columns as allowing them or not allowing them 

###### Outer Joins 
* Say we want Sailors and Reserves, but some sailors have not made a reservation 
* Can imagine wanting to reatins all Sailor tuples in the results 
* Left outer, right outer, or full outer

```
SELECT * 
FROM Sailots 
LEFT OUTER JOIN Reserves 
USING (sid);
```

###### SQL Summary 
* SELECT/FROM/WHERE
* Expressions and strings 
* Set operators 
* Nested queries 
* Aggregation 
* GROUP BY/ HAVING 
* Null alues and Outer Joins 
* ORDER BY, LIMIT 


## Relatioinal Algebra

A simple, powerful abstraction for representing SQL 

RELATION -> QUERY -> RELATION 

###### Operators 
* Input: One or more relatioins
* Output: One relation 

###### Unary Operatos 
* Select
	* Input: Relation (R)
	* Output: R' containing subset of tuples based on selection 
* Project
	* Output: R' containing subset of columns 
* Cross Product 
	* InputL two relations 
	* Output: Relatioin containing all pairs 

# Lecture 5 
## RA 

###### Operators
*Selectioin 
*Projection 
*Cross Product
*Join (cross - select)
*Join 
* Set ops: Union, Intersection, Set Diff. 

# Lecture 6 
## RA 

###### Division 
* Relational Division
	* R/B Is the largest Set T such that T X B is a subset of R

###### Proving Equivalence 
* Show every element on the LHS set is also in the RHS set 
* If t in LHS, then t in RHS 
* If t in RHS, then t in LHS 

###### Bag Relatioinal Algebra 
* Bag algebra DOES contain repeat -- not a set

###### RA Summary 
* Basic operators
	* Selection 
	* Projection 
	* Cross Product 
	* Union 
	* Intersection 
	* Difference 
* Derived Operators 
	* Join
	* Division 
* Equivalence and how to prove them 
* Bag RA 

# Lecture 10
## Tree-Structure Indexes

How to organizes pages of file: 
* Heap (random order)
* Sordted Files 
	* Can only sort on single attribute
* Indexes

###### Indexes 

Index contains collection of data entries. 
A data entry k* is information that can recover the record with key value k 

SO, in k* we can store: 
* The entrie data record with value k 
	* Acts as a file organization for data records
	* At most one index on a 
* <K, rid of data records with key k> 
	* Takes up a lot of space
* <K, list of rids of data records with search key k> 
	*  Variable sized entries 

Index key can contain multiple attributes 

Tree indexing supports RANGE and EQUALITY 

ISAM: static tree structure 
* Requires overlfow pages with to omany inserts 
B+ Tree: dynamic, adjusts gracefully with inserts and deletes 

###### B+ TREE
* Insert/delete at logF(N) F = fanout N = # leaf pages 
* Minimum 50% occupancy (except root)
* Each node contains d <= m <= 2d entires where d is the "order"

# Lecture 11
## Sorting

'run' is a sorted portion of the file 

Two-Way External Merge Sort: 
* Pass 0 
	* Read page at a time, sort it, write it 
* Pass 1,2,3...
	* Merge larger and larger runs 
	* Requires three buffer pages 

Total IO: 
Cost of Pass 0 : 2N (read and write every page)
Number of merge passes : (log2(N))
Cost of each mege pass : 2N
Total Cost 2N + 2N*(log2(N)) = 2N(log2(N) + 1)

General External Merge Sort 
Using B buffer pages 

* Pass 0 
	* Read B pages at a time, sort and then right them 
	* Length of each run : B Pages 
	* With N input pages : number of runs = N/B 
	* Cost : 2N
* Pass 1,2,3... 
	* Pass 1: produce runs length B(B-1)
	* Pass 2: produce runs length B(B-1)<sup>2</sup>
	* Pass P: produce runs length B(B-1)<sup>P</sup>

Merge fan in  number of runs merged in each pass 
Cost analysis: 

Cost of pass 0 : 2N 
Assume P merge passes, then B(B-1)<sup>P</sup> = N 
P = [log<sub>B-1</sub>(N/B)]
Cost of each pass : 2N 
Total Cost 2N([log<sub>B-1</sub>(N/B)] + 1)


Using B+ Trees for Sorting 
* Table to be sorted has a B+ tree index on sorting columns 
* Can retrieve records by traversing leaf 
* If clustered: good idea, if unclustered : BAD idea 

## Implementing RA Operators 

2 Relation: Sailors, Reserves 

Sailors : 50 byte tuples, 80 tuples/page, 500 pages 
Reserves : 40 byte tuples, 100 tuples/page, 1000 pages 

Consider the sql : 

```
SELECT * 
FROM Reserves R 
WHERE R.rname = 'Alice';
```

Three Possible Cases 
* 1: No index on any selectioin attributes 
* 2: Index on ALL selectioin attributes 
* 3: Index on some selectioin attributes 

Case 1
* Need to scan the entire R: 1000 IOs 
	* Plus cost to write result, but that's the case for all so ignore 
	* IF file is sorted, log cost : ~10I)s 
		* Plus cost to retrieve matching (0-1000 I/Os)

Case 2 (Using index)
* Tree Index cost: 
	* Traverse from root (once)
		* 2 - 3 IOs
	* Scan lead pages to find data entries 
	* Retrieve actual data records 
	* Final cost depends HEAVILY on clustured or unclustered 

* Has Index cost: 
	* Find bucket
		* 1 - 2 IOs 
	* Cost of retrieving the ctual data records (depends on # matching)

NEW EXAMPLE: 
```
SELECT * 
FROM Reserves R 
WHERE R.rname < 'C%'
```

Assume reasonable distribution of names (?) 10% of tuples: 10k tuples, 100 pages 

* Have clustured B+ tree on rname
	* Traverse to find first leaf : 1-2 IOs
	* Scan : 100 IOs

* Have unclustered B+ tree 
	* Worst case : seperate IO per tuples, 10k IOs 
	* Cheaper to just scan every page 
* Heuristic: scan chaper than unclusted index if retrieving more then 5%


# Lecture 13 

NEW EXAMPLE CODE: 
```
SELECT * 
FROM Sailor S 
WHERE S.Age = 25 AND S.Salary > 100k 
```
Have Hash Index on Age

Option 1: 
* Use available index on Age to get superset of relevant data 
* Retrieve the tuples corresponding to the set of data entries 
* APply remaining predicates on retrieved tuples 
* Return new tuples 

Option 2: 
* Sequential Scan 
* POssible better depending on selectivity 


ANOTHER EXAMPLE : 

Same query, we have hash index oin Age, and B+ tree on salary 

Option 1: 
* Choose most selective access path 
* Apply remaining predicates to satisfy selectioin 

Option 2: 
* Get RIDs od tuples using both indexes 
* Intersect those lists 

Option 3: 
* Sequential scan : always available 

###### Implementing Projectioin 

* In the general case, we simply scan the file. 
* Tricky is eliminating duplicates 
* Sort bases projectioin: 
	* Pass through tuples, retain desired
	* Sort the result and spit out non-duplicates 
	












