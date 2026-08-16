#projects
Vaultik is: 
-Single Node
-Concurrent
-Persistent
-In memory key-value store

##### HashMap : memory storage
HashMap<String, StoredValue>
![[Pasted image 20260816122814.png|525]]
-GET, SET and DELETE in O(1) average
-StoredValue is immutable and carries both the value and expiration timestamp
-immutable values are easier to deal with under concurrency
![[Pasted image 20260816124012.png|280]]
##### TTL(Time to Live)
SET user:1 shlok TTl=60
it doesn't delete after 60sec, only deleted when GET happens(lazy expiration)
no need for a background expiration thread
downside - expired keys that are never accessed remain in memory
![[Pasted image 20260816124012.png|268]]

#### Concurrency

Thread A : SET x = 10      Thread A: DELETE x
Thread B : GET x              Thread B: GET x
without synchronization, they interact unpredictably

ReadWriteLock : concurrency
Pluggable LRU/LFU eviction
WAL + snapshots : durability and recovery
SpringBoot : REST APi

*Not a distributed cache like redis*

