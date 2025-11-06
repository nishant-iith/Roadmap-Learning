# Chapter 13: Indexing in DBMS 📚

## What is Indexing? 🔍

**Indexing is used to optimise the performance of a database by minimising the number of disk accesses required when a query is processed.**

**The index is a type of data structure. It is used to locate and access the data in a database table quickly.**

### Key Benefits:
- **Speeds up read operations** like SELECT queries, WHERE clause, etc.
- **Reduces disk I/O** operations
- **Improves query performance** significantly

### Index Components 📋

#### 1. Search Key
**Contains copy of primary key or candidate key of the table or something else.**

- Can be primary key, candidate key, or any attribute
- Used for searching and locating data

#### 2. Data Reference
**Pointer holding the address of disk block where the value of the corresponding key is stored.**

- Points to actual data location on disk
- Enables direct access to data records

### Important Notes 📝

- **Indexing is optional**, but increases access speed
- **Not the primary means to access tuples** - it's the secondary means
- **Index file is always sorted**
- **Trade-offs**: Faster reads vs. slower writes

---

## Indexing Methods 🗂️

### 1. Primary Index (Clustering Index) 🏗️

#### Definition
**A file may have several indices, on different search keys. If the data file containing the records is sequentially ordered, a Primary index is an index whose search key also defines the sequential order of the file.**

#### Key Characteristics:
- **Data file is sequentially ordered** on the search key
- **Search key defines file order**
- **NOTE**: The term primary index is sometimes used to mean an index on a primary key. However, such usage is nonstandard and should be avoided.

#### Important Point:
**All files are ordered sequentially on some search key. It could be Primary Key or non-primary key.**

---

### Dense And Sparse Indices 📊

#### 1. Dense Index 🔍
**The dense index contains an index record for every search key value in the data file.**

**Characteristics**:
- **Index record for EVERY search key value**
- Contains search-key value and pointer to first data record
- Rest of records with same search-key stored sequentially

**Visual Example**:
```
Data File (Sorted by Student_ID):
101 | John | CS
102 | Jane | EC
103 | Mike | CS
104 | Sara | IT

Dense Index:
101 → Pointer to Record 101
102 → Pointer to Record 102
103 → Pointer to Record 103
104 → Pointer to Record 104
```

**Pros and Cons**:
- ✅ **Fast searching**: Can locate any record directly
- ❌ **More space**: Needs space for every index record
- ❌ **Overhead**: Index records have search key + pointer for each record

#### 2. Sparse Index 🎯
**An index record appears for only some of the search-key values.**

**Characteristics**:
- **Index record for SOME search key values**
- Range of index columns stores same data block address
- When data needs retrieval, block address fetched

**Visual Example**:
```
Data File (Sorted by Student_ID):
101 | John | CS  | Block 1
102 | Jane | EC  | Block 1
103 | Mike | CS  | Block 2
104 | Sara | IT  | Block 2
105 | Tom  | ME  | Block 3

Sparse Index:
101 → Pointer to Block 1  (First record in block)
103 → Pointer to Block 2  (First record in block)
105 → Pointer to Block 3  (First record in block)
```

**Pros and Cons**:
- ✅ **Less space**: Fewer index records
- ✅ **Efficient**: Resolves dense indexing issues
- ❌ **Slower search**: May need to scan within block

---

### Primary Indexing Types 📚

#### Based on Key Attribute
**Data file is sorted w.r.t primary key attribute.**

**Characteristics**:
- **Primary Key used as search-key in Index**
- **Sparse Index will be formed**
- **Number of entries in index file = number of blocks in data file**

**Example**:
```
Data File (Sorted by Student_ID - Primary Key):
Block 1: 101, 102, 103
Block 2: 104, 105, 106
Block 3: 107, 108, 109

Primary Index (Sparse):
101 → Pointer to Block 1
104 → Pointer to Block 2
107 → Pointer to Block 3
```

#### Based on Non-Key Attribute
**Data file is sorted w.r.t non-key attribute.**

**Characteristics**:
- **Number of entries = unique non-key attribute values**
- **This is dense index** (all unique values have entry)
- **Clustering index for grouping**

**Example**:
```
Data File (Sorted by Department - Non-Key):
101 | John  | CS
103 | Mike  | CS
105 | Tom   | CS
102 | Jane  | EC
104 | Sara  | IT

Primary Index (Dense):
CS  → Pointer to first CS record (101)
EC  → Pointer to first EC record (102)
IT  → Pointer to first IT record (104)
```

**Real-World Example**:
**Let's assume a company recruited many employees in various departments. In this case, clustering indexing should be created for all employees who belong to the same dept.**

---

### 2. Multi-level Index 📊

#### Definition
**Index with two or more levels.**

#### Why Multi-level Index?
**If the single level index becomes so large that the binary search itself would take much time, we can break down indexing into multiple levels.**

**Visual Example**:
```
Level 1 (Top Level):
CS  → Pointer to Level 2 for CS
EC  → Pointer to Level 2 for EC
IT  → Pointer to Level 2 for IT

Level 2 (Second Level):
CS-101 → Pointer to Data Block
CS-103 → Pointer to Data Block
EC-102 → Pointer to Data Block
IT-104 → Pointer to Data Block
```

**Benefits**:
- **Reduces search time** for very large indexes
- **Hierarchical structure** like B+ trees
- **Efficient disk access** pattern

---

### 3. Secondary Index (Non-Clustering Index) 🔗

#### Definition
**Data file is unsorted. Hence, Primary Indexing is not possible.**

**Characteristics**:
- **Data file is unsorted**
- **Can be done on key or non-key attribute**
- **Called secondary indexing** because normally one indexing is already applied
- **Number of entries = number of records in data file**
- **It's an example of Dense index**

**Example**:
```
Unsorted Data File:
104 | Sara | IT
101 | John | CS
103 | Mike | CS
102 | Jane | EC

Secondary Index (Dense):
101 → Pointer to Record 101
102 → Pointer to Record 102
103 → Pointer to Record 103
104 → Pointer to Record 104
```

**When to Use**:
- When data file cannot be sorted
- When multiple search keys needed
- When flexible access patterns required

---

## Comparison of Index Types ⚖️

| Index Type | Data File Order | Index Density | Entries | Use Case |
|------------|----------------|----------------|----------|----------|
| **Primary (Key)** | Sorted by PK | Sparse | Number of blocks | Fast PK access |
| **Primary (Non-Key)** | Sorted by non-key | Dense | Unique values | Grouping/Clustering |
| **Secondary** | Unsorted | Dense | All records | Flexible access |
| **Multi-level** | Any | Multiple levels | Hierarchical | Large datasets |

| Feature | Dense Index | Sparse Index |
|---------|-------------|--------------|
| **Space Usage** | High | Low |
| **Search Speed** | Fast | Moderate |
| **Insert/Update** | Slower | Faster |
| **Best For** | Small tables | Large tables |
| **Block Access** | Direct | Block-level |

---

## Visual Examples 🎨

### Example 1: University Database - Primary Index on Student ID
```
Data File (Sorted by Student_ID):
Block 1: 101-John-CS, 102-Jane-EC, 103-Mike-CS
Block 2: 104-Sara-IT, 105-Tom-ME, 106-Bob-CS

Primary Index (Sparse):
101 → Pointer to Block 1
104 → Pointer to Block 2
```

### Example 2: Company Database - Primary Index on Department
```
Data File (Sorted by Department):
101-John-CS, 103-Mike-CS, 106-Bob-CS
102-Jane-EC
104-Sara-IT
105-Tom-ME

Primary Index (Dense):
CS  → Pointer to Record 101
EC  → Pointer to Record 102
IT  → Pointer to Record 104
ME  → Pointer to Record 105
```

### Example 3: E-commerce - Secondary Index on Product Name
```
Unsorted Product Data:
P004-Laptop-50000
P001-Mobile-20000
P003-Tablet-30000
P002-Headphones-5000

Secondary Index (Dense):
P001 → Pointer to Mobile record
P002 → Pointer to Headphones record
P003 → Pointer to Tablet record
P004 → Pointer to Laptop record
```

---

## Advantages and Limitations ⚖️

### Advantages of Indexing ✅

1. **Faster access and retrieval of data**
   - Reduces query execution time
   - Improves SELECT query performance
   - Enables efficient WHERE clause processing

2. **IO is less**
   - Fewer disk accesses required
   - Reduces disk I/O operations
   - Better resource utilization

3. **Supports sorting and grouping**
   - ORDER BY operations become faster
   - GROUP BY operations optimized
   - JOIN operations improved

4. **Enforces uniqueness**
   - Prevents duplicate values
   - Maintains data integrity
   - Supports foreign key constraints

### Limitations of Indexing ❌

1. **Additional space to store index table**
   - Index requires extra storage space
   - Large indexes consume significant disk space
   - Multiple indexes multiply space requirements

2. **Indexing decreases performance in INSERT, DELETE, and UPDATE queries**
   - Write operations become slower
   - Index must be updated for each data change
   - Multiple indexes impact performance significantly

3. **Maintenance overhead**
   - Indexes need to be updated regularly
   - Fragmentation can occur over time
   - Rebuilding indexes may be required

---

## Interview Questions 🎯

### Q1: What is the difference between Dense and Sparse indexes?
**Answer**:
- **Dense Index**: Contains index record for EVERY search key value, more space but faster access
- **Sparse Index**: Contains index record for SOME search key values, less space but slower access
- **Dense Example**: Every Student_ID has index entry
- **Sparse Example**: Only first Student_ID in each block has index entry

### Q2: When would you use a Secondary Index instead of Primary Index?
**Answer**:
- When data file cannot be sorted (unsorted data)
- When multiple search keys are needed
- When flexible access patterns are required
- When clustering is not important
- **Example**: Search by product name when data is sorted by product ID

### Q3: Why is Primary Index based on Primary Key always sparse?
**Answer**:
- Primary key values are unique and ordered
- Each block contains multiple primary key values
- Only need to point to first record in each block
- Number of index entries = number of blocks (much smaller than number of records)
- **Saves significant space** while maintaining search efficiency

### Q4: What is the purpose of Multi-level indexing?
**Answer**:
- Used when single-level index becomes too large
- Reduces search time for very large datasets
- Breaks down indexing into multiple levels
- Similar to B+ tree structure
- **Example**: Million-record table with primary index on ID

### Q5: How does indexing affect database performance?
**Answer**:
**Positive Effects**:
- Faster SELECT queries
- Efficient WHERE clause processing
- Improved JOIN operations
- Better sorting and grouping

**Negative Effects**:
- Slower INSERT, UPDATE, DELETE operations
- Additional storage space required
- Index maintenance overhead
- Potential fragmentation over time

### Q6: Explain clustering index with a real-world example
**Answer**:
**Example**: Company employees database sorted by department
- Data file sorted by department (non-key attribute)
- Primary index created on department (dense index)
- All employees from same department stored together
- **Benefit**: Fast retrieval of all employees from specific department
- **Use Case**: Generate department-wise reports efficiently

---

## Quick Reference Table 📋

| Index Type | Data Order | Index Type | Space | Search Speed | Best Use |
|------------|------------|------------|-------|--------------|----------|
| **Primary (Key)** | Sorted by PK | Sparse | Low | Fast | PK lookups |
| **Primary (Non-Key)** | Sorted by non-key | Dense | High | Fast | Grouping |
| **Secondary** | Unsorted | Dense | High | Moderate | Flexible access |
| **Multi-level** | Any | Multiple levels | Medium | Very Fast | Large datasets |

| Operation | With Index | Without Index |
|-----------|------------|---------------|
| **SELECT** | Fast | Slow (Full Scan) |
| **INSERT** | Slower | Faster |
| **UPDATE** | Slower | Faster |
| **DELETE** | Slower | Faster |
| **Storage** | More | Less |

---

## Key Takeaways 💡

1. **Indexing optimizes database performance** by minimizing disk accesses
2. **Primary Index** = Data file sorted, can be dense or sparse
3. **Secondary Index** = Data file unsorted, always dense
4. **Dense Index** = Entry for every value, more space, faster access
5. **Sparse Index** = Entry for some values, less space, moderate access
6. **Multi-level Index** = For very large datasets, hierarchical structure
7. **Trade-offs**: Faster reads vs. slower writes, more space vs. less space
8. **Choice depends on**: Data size, access patterns, query requirements

**Remember**: Indexing is a powerful optimization tool, but it's important to choose the right type of index based on your specific requirements and access patterns! 🎯