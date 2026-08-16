#projects
Vaultik is: 
-Single Node
-Concurrent
-Persistent
-In memory key-value store

##### HashMap : memory storage
HashMap<String, StoredValue>
![[Pasted image 20260816122814.png]]
-GET, SET and DELETE in O(1) average
-StoredValue is immutable and carries both the value and expiration timestamp
-immutable values are easier to deal with under concurrency
ReadWriteLock : concurrency
Pluggable LRU/LFU eviction
WAL + snapshots : durability and recovery
SpringBoot : REST APi

*Not a distributed cache like redis*

