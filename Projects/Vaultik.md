#projects
#### Vaultik is: 
- Single Node
- Concurrent
- Persistent
- In memory key-value store

#### HashMap : memory storage
HashMap<String, StoredValue>
![[Pasted image 20260816122814.png|525]]
-GET, SET and DELETE in O(1) average
-StoredValue is immutable and carries both the value and expiration timestamp
-immutable values are easier to deal with under concurrency
![[Pasted image 20260816124012.png|280]]
#### TTL(Time to Live)
SET user:1 shlok TTl=60
it doesn't delete after 60sec, only deleted when GET happens(lazy expiration)
no need for a background expiration thread
downside - expired keys that are never accessed remain in memory
![[Pasted image 20260816124012.png|268]]

#### Concurrency

Thread A : SET x = 10      Thread A: DELETE x
Thread B : GET x              Thread B: GET x
without synchronization, they interact unpredictably
###### ReadWriteLock
![[Pasted image 20260816134817.png|370]]
multiple readers can execute simultaneously
writers require exclusive access

==*why does GET sometimes require a write lock?*==
*GET may require a write lock because it can modify TTL state, eviction metadata, and statistics*
==*why not per key locks?*==
*it does increase parallelism but increasing complexity as well*

##### Eviction policy
capacity = 3
Already have A B C
SET D, now entries = 4
need eviction policy
- LRU (least recently used)
		A B C
		Access : A B A
		Recency : C->B->A
		evict C
		LRU uses HashMap + Doubly linked list
		O(1) eviction
- LFU (least frequently used)
		A → 10 accesses
		B → 2 accesses
		C → 7 accesses
		evict B
![[Pasted image 20260816140952.png|436]]
>A accessed 100 times yesterday
  B accessed 5 times in last minute
  LFU May keep A
  LRU May keep B

==LFU Tie Breaking==
A → frequency 2
B → frequency 2
RULE: 
1. lowest frequency
2. then LRU among ties

##### Persistence



