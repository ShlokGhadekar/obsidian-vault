#projects
Vaultik is: 
-Single Node
-Concurrent
-Persistent
-In memory key-value store

##### HashMap : memory storage
HashMap<String, StoredValue>
![[Pasted image 20260816122814.png]]
GET, SET and DELETE in O(1) average
ReadWriteLock : concurrency
Pluggable LRU/LFU eviction
WAL + snapshots : durability and recovery
SpringBoot : REST APi

*Not a distributed cache like redis*

