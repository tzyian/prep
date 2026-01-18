
DynamoDB Deep Dive
https://www.youtube.com/watch?v=2X2SO3Y-af8 

ByteByteGo LSM Trees
https://www.youtube.com/watch?v=I6jB0nM9SKU

LSM Trees
![[Pasted image 20260118173124.png]]

1. Write to a MemTable
	1. for batched flushes
	2. in-memory data structure functioning as as an Append Only Log
2. Sorted and flushed to a SSTable (Sorted String Table)
	1. Tomestone values 
	2. New entries supersede old values
3. Find requires searching every level
4. Offline compaction of same keys to higher levels to reduce space
5. Bloom filters on every level to check if a value exists
6. 

![[Pasted image 20260118173447.png]]

Summary Tables to find the min and max value of a key
Bloom filter(s) for existence-checking (True Positives, False Positives, and True Negatives)