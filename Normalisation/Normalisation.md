Normalization is the process of removing redundancy in your database.

### Redundancy
Redundant data means data that already exists elsewhere and we'are duplicating it in two places

For example, if you have two tables
1. users
2. user_metadata

![[Pasted image 20251227101743.png]]
	If you notice, we've stored the name on the order in the Orders table, when it is already present in the Users table. This is what is `redundant` data.

### Non Full proof data
![[Pasted image 20251227102045.png]]
This data doesn’t have any relationship b/w Orders and users. This is just plain wrong. You can never tell the orders for a user (esp if 2 users can have the same name)
Normalisation is done on tables that are full proof to remove redundancy. 

### Types of relationships
Use case - Library management system

1. Users table

2. Library card table

3. Books table

4. Genre table

#### One to One 
Each user has a single `Library Card`
![[Pasted image 20251227103131.png]]

#### One to many
![[Pasted image 20251227103154.png]]

#### Many to one 
Opposite of the thing above

#### Many to Many
![[Pasted image 20251227103231.png]]

#### Final Graph
![[Pasted image 20251227103253.png]]

### Normalizing Data
Normalization in databases is a systematic approach of decomposing tables to eliminate data redundancy and improve data integrity.

The process typically progresses through several normal forms, each building on the last.

When you look at a schema, you can identify if it lies in one of the following categories of normalization

1. 1NF

2. 2NF

3. 3NF

4. BCNF

5. 4NF

6. 5NF

You aim to reach 3NF/BCNF usually. The lower you go, the more normalised your table is. But over normalization can lead to excessive joins

#### 1NF
 - A single cell must not hold more than one value(atomicity): This rule ensures that each column of a database table holds only atomic (indivisible) values, and multi valued attributes are split into separate columns.
![[Pasted image 20251227103845.png]]
- There must be a primary key for identification: Each table should have a primary key, which is a column(or set of columns) that uniquely identifies each row in a table.
- No duplicate Rows: TO ensure that the data in the table is organised properly and to uphold the integrity of the data, each row in the table should be unique.
- Each column must have only one value for each row in the table
	- example - ![[Pasted image 20251227104246.png]]

#### 2NF
1NF gets rid of repeating rows, 2NF gets rid of redundancy

A table is said to be 2NF if it meets the following criteria:
- is already 1NF
- Has 0 partial dependency
	- Partial dependency - This occurs when a non-primary key attribute is dependent on part of a composite primary key, rather than on the whole primary key. In simpler terms, if your table has a primary key made up of multiple columns, a partial dependency exists if an attribute in the table is dependent only on a subset of those columns that form the primary key. Example: Consider a table with the composite primary key (StudentID, CourseID) and other attributes like InstructorName and CourseName. If CourseName is dependent only on CourseID and not on the complete composite key (StudentID, CourseID), then CourseName has a partial dependency on the primary key. This violates 2NF.

##### Before Normalization
Enrollments Table
![[Pasted image 20251227105740.png]]

Can you spot the redundancy over here? The instructor name and course name are repeated in rows, even though the name of an instructor should be the same for a given courseID

Primary key of this table is (student_id, course_id)

CourseName and InstructorName have a `partial dependency` on `CourserID`

![[Pasted image 20251227105803.png]]

###### After Normalization
![[Pasted image 20251227105826.png]]

#### 3NF
WHen a table is in 2NF, it eliminates repeating groups and redundancy, but it does not eliminate transitive partial dependency.
- be in 2NF
- have not partial dependency
- A **transitive dependency** in a relational database occurs when one non-key attribute indirectly depends on the primary key through another non-key attribute.
- ![[Pasted image 20251227111520.png]]
	- `Department name` has a `transitive dependency` on the primary key (employee id).
- To normalise to 3NF, we need to do the following
	- ![[Pasted image 20251227111547.png]]