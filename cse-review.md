##lec14

1. link layer
   * 3-wire ready/acknowledge protocol
      + put data
      + put ready
      + see ready and read value, change ack
   * vco (voltage controlled oscillator 中继器) & manchester code (01->0110)

##lec 15

1. IP route table
   * entry = (network, interface)
   * routing
      + 1 hello to neighbor
      + 2 advertise: learn about other reachable nodes
      + 3 deter min cost route
   * 2 type
      + distance vector: to neighbor with all info, update route with bell-ford
      + link-state: to all with neighbor info, update route with dijkstra
   * 3 ways to scale
      + Path-vector Routing
         Advertisements include the path, to better detect routing loops
      + Hierarchy of Routing
         Route between regions, and then within aregion
      + Topological Addressing
         Assign addresses in contiguous blocks to make advertisements smaller
   * path vector exchange(include full path)
   * hierarchical address assignment
      + addr = region + station
      + 2 table: region forwarding + local forwarding
      + BGP is a path-vector protocol used across regions
      + give addr to regions in contiguous blocks, so that they can be specified succinctly via a particular notation (“CIDR” notation)
      + Keeps advertisements small(er than they would be otherwise)
      + 18.0.0.0 ... 18.0.0.255 -> 18.0.0.0/24
      + CIDR 将internet上创建附加地址，将路由集中，令IP代表骨干服务提供商；32位IP= prefix网络+主机，可另分子网，128.0.0.123/20前20位表示网络，后12位表示主机
2. ​BGP
   * customer / provider / peer
   * customer pays provider for transit(from provider to customer)
   * peer allow mutual access to each other's customers
   * Routing
      + HELLO protocol
         BGP sends KEEPALIVE messages to neighbors
      + Advertisements: Sent to neighbors
         BGP runs on top of TCP
      + Doesn‘t have to do periodic advertisements to handle failure
         Integration: via policies described above
         Failures: routes can be explicitly withdrawn in BGP if fail
      + Routing loops avoided because BGP is path-vector
3. Data Plane
   * Forwarding an ip packet(image)
      + Lookup packet’s DST in forwarding table
         If known, find the corresponding outgoing link
         If unknown, drop packet
      + Decrement TTL (Time To Live)
         Drop packet if TTL is zero
      + Update header checksum
      + Forward packet to outgoing port
      + Transmit packet onto link
4. NAT network addr trans
   * private === NAT router === public
   * image
   * ethernet map
   * ARP address resolution protocol
      + NETWORK_SEND (“where is M?”, 11, ARP, ENET, BROADCAST)
      + NETWORK_SEND (“M is at station 15”, 18, ARP, ENET, UNICAST)
      + L ask E’s address, E does not hear the broadcast, but the router at station 19 does, and it sends a suitable ARP response
      + other who mismatch the IP simply discard
   * how to connect
      + app-> tar MAC: / | src MAC: / | tar IP: Baidu | src IP: node-c -> OS
      + OS->  tar MAC: gateway | src MAC: node-c | tar IP: Baidu | src IP: node-c -> router(gateway)
      + router->  tar MAC: nexthop | src MAC: router | tar IP: Baidu | src IP: router -> next hop
      + nexthop->  tar MAC: baidu | src MAC: nexthop | tar IP: Baidu | src IP: router -> baidu
   * ARP spoofing(man in the middle attack)

printf lec16 17 24

## lec19#
1. shadow copy
2. rename function
   * newino = lookup(x), oldino = lookup(y)
   * incref(newino), change y's dirent to newino
   * decref(oldino), remove x's dirent, decref(newino)

## lec21#
1. Serializablity
   * Final-state serializability
        A schedule is final-state serializable if its final written state is equivalent to that of some serial schedule
   * Conflict serializability
      + the order of its conflicts == order of conflicts in sequential schedule
      + conflict serializable <-> an acyclic conflict graph
      + ​
   * View serializability
      + if the final written state as well as intermediate reads == in some serial schedule
   * FS s > V s > C s(final state+data dependency)
   * 2-phase locking
      + deadlock: global ordering on locks / abort some transactions
   * read-write locks
      + can release read lock after lock point(peak)
   * OCC optimistic concurrency control
      + Phase 1: Concurrent local processing
         Reads tuples into a read set
         Buffers writes into a write set
      + Phase 2: Validation in critical section
         Validates serializability: tuple in the read set been modified?
      + Phase 3: Commit the results in critical section or abort
         Aborts: aborts the transaction if validation fails
         Commits: installs the write set and commits the transaction
      + only allows serialized execution of T2, T1, but is not able to recognize serialized executionof T1 T2.
   * BCC BALANCED CC
   * 2 phase commit
   * ​

## lec22#
1. Strict Consistency
   * read always get latest version
2. Eventual Consistency
   * temporary  inconsistency, focus on performance& available
3. Cache Consistency
   * strict: write through / eventual
   * 1 volatile variable, marked as dont cache me
   * 2 timer expiration(dns)
   * 3 snoopy: a write to memory, write through cache 
      + Processor A writes to memory Write-through the cache, to memory by bus
      + Cache of B & C snoop on the bus and update the replica
      + Snoopy protocol: broadcast update to all CPUs / Directory scheme: multi-cast update to CPUs that have the value

##lec23##
1. Strict Consistency
2. Sequential Consistency
   * allow memory write lag & require write propagate at same time
3. Difference between Cache Coherence and Sequential Consistency
   * Difference
      + Sequential consistency requires a globally consistent view of memory operations
      + Cache coherence only requires a locally consistent view
   * Based on cache implementation
      + This couldn't happen with a snoopy-cache based scheme
      + But it certainly could with a directory-based scheme
4. Processor Consistency
   * Writes from a processor are received by all other processors in the issue order, But writes from different processors may be in a different order by different processors

## lec25#

1. guard model

   only way to access the resource involves the guard

   1. Authentication: is the principal who they claim to be?
      verified using pwd 
   2. Authorization: does principal have access to perform request on
      resource?
      consult access control list for resource
      sql injection

##lec26 authentication
1. password
   1. **mathod server** has copy of pwd
   2. with bufoverflow.
   3. timing attack & page fault
   4. **store hash**
   5. rainbow table & guess most popular pwd
   6. **hash with salt** hash = (hash(pwd|salt))
   7. **session cookies strawman** user,expir,H(server_key|user|expiration)
   8. phishing attack with legitimate-looking website
2. once you send a password to the server, it can impersonate you
   1. challenge response scheme
      * server sends a random R to client
      * client sends H(R + pwd) to server
      * **if server dont know**
   2. pwd to authenticate the server
      * client sends a random R to server
      * server sends H(R + pwd) to client
   3. server show some word/picture from client
      * fake server can talk to real server
   4. specific pwd
      * pwd specific to site, send H(servername + password)
   5. one-time passwords
      * 1. user send H(n-1)(salt + pwd)
      * server verifys x = H(token), then set x :=token
      * 2. include time into hash
   6. bind authentication with request
      * req={user, "write xxx", H(pwd+"write xxx")}
      * also can replace pwd with session
   7. FIDO replace the pwd
      * fingerprint bind with private-key.
      * userid bind with publish-key
3. oauth
   1. 用户打开resouce owner以后，客户端要求用户给予授权。
   2. 用户同意给予客户端授权。
   3. 客户端使用授权authorization grant，向认证服务器申请令牌。
   4. 认证服务器对客户端进行认证以后，发放access token。
   5. 客户端使用access token，向资源服务器申请获取资源。
   6. 资源服务器同意向客户端开放资源。
## lec 27 secure channel & local security
1. encrypt(key, msg)->text decrypt(key, text)->msg
2. case1:
   * A: c=encrypt(k,m); h=MAC(k,m) *token*
   * A send to B (c|h)
   * B: m = decrypt(k,c); MAC(k,m)==h?
   * if Eve get the (c|h) replay attack*to B*/reflection attack*to A*
3. case2: add seq num && use diff key
   * A:ca = encrypt(ka, ma | seqa); ha = MAC(ka, ma | seqa)
   * A send to B (ca|ha)
   * B:ma | seqa = decrypt(ka, ca); MAC(ka, ma | seqa) == ha ?
   * B:cb = encrypt(kb, mb | seqb); hb = MAC(kb, mb | seqb)
   * B send to A (cb|hb)
   * A:mb | seqb = decrypt(kb, cb); MAC(kb, mb | seqb) == hb ?
4. case3: 
   * A choose random a; send g^a mod p to B
   * B choose random b; send g^b mod p to A
   * A: (g^b)^a mod p
   * B: (g^a)^b mod p
   * Eve can choose a random e between A&B
5. RSM
   * public key <-> secret key
   * Certificate Authorities
   * TLS handshake??
6. Taint Tracking
   * Dynamic: if value come from input, change the taint status into true
   * add taint checking code into the original code
   * TaintSeed: Mark untrusted data as tainted
   * TaintTracker: Track each instruction, determine if result is tainted
   * TaintAssert: Check is tainted data is used dangerously

## Lec28 security ROP&CFI
1. stack overflow: 
   * add malicious code in buf
   * overwrite return addr 
2. defense: *DEP data execution prevention*
   * mark data areas as non-x
3. code reuse attack *ROP return-oriented programming*
   * find code gadgets in existed code base
   * push  gadgets on the stack
4. defense: Hide binary file & ASLR & Canary
   * ASLR change the layout of memory space
   * Canary embed canary in stack and verify their integrity piror to function return
5. defense: CFI: control-flow integrity
   * pre-determine control flow graph (CFG) of an application
   * use binary rewriting to instrument code with runtime checks
   * insert binary code before and after jmp. check if is legal jmp
       origin:
        jmp ecx
       patch: 
        cmp[ecx], 0x123123123
        jne errlabel
        jmp ecx
6. Blind Rop
   * atk: send request with shell code
   * def: stack non-X; ASLR
   * atk: ROP to get output
   * def: ASLR; canary
   * atk: BROP overwrite single byte everytime to get 
   * atk: find gadget: try ret addr every time; get stop/crash/useful gadget
   * atk: find PLT(procedure linking table)
7. attack order
   * dump bin
   * stack reading
   * find PLT
   * find BROP gadget
   * find strcmp
   * find write
   * ​


###question
s12-2 IIV 5
s15-1 VII 13 14
s15-2 VII 16
s14-1 16 2
s14-2 I 1b d
BGP Cross-site Scripting
SSL/TLS
s14-2 V
s13-1 VI 9(mayknow) 10

**snapshot isolation**
can see the old version during other transaction executing

**RAID 1**
RAID 1 consists of data mirroring, without parity or striping. Data is written identically to two drives, thereby producing a "mirrored set" of drives. Thus, any read request can be serviced by any drive in the set. If a request is broadcast to every drive in the set, it can be serviced by the drive that accesses the data first (depending on its seek time and rotational latency), improving performance. Sustained read throughput, if the controller or software is optimized for it, approaches the sum of throughputs of every drive in the set, just as for RAID 0. Actual read throughput of most RAID 1 implementations is slower than the fastest drive. Write throughput is always slower because every drive must be updated, and the slowest drive limits the write performance. The array continues to operate as long as at least one drive is functioning.[13]
**RAID 2**
RAID 2 consists of bit-level striping with dedicated Hamming-code parity. All disk spindle rotation is synchronized and data is striped such that each sequential bit is on a different drive. Hamming-code parity is calculated across corresponding bits and stored on at least one parity drive.[13] This level is of historical significance only; although it was used on some early machines (for example, the Thinking Machines CM-2),[20] as of 2014 it is not used by any commercially available system.[21]
**RAID 3**
RAID 3 consists of byte-level striping with dedicated parity. All disk spindle rotation is synchronized and data is striped such that each sequential byte is on a different drive. Parity is calculated across corresponding bytes and stored on a dedicated parity drive.[13] Although implementations exist,[22] RAID 3 is not commonly used in practice.
**RAID 4**
RAID 4 consists of block-level striping with dedicated parity. This level was previously used by NetApp, but has now been largely replaced by a proprietary implementation of RAID 4 with two parity disks, called RAID-DP.[23] The main advantage of RAID 4 over RAID 2 and 3 is I/O parallelism: in RAID 2 and 3, a single read/write I/O operation requires reading the whole group of data drives, while in RAID 4 one I/O read/write operation does not have to spread across all data drives. As a result, more I/O operations can be executed in parallel, improving the performance of small transfers.[3]
**RAID 5**
RAID 5 consists of block-level striping with distributed parity. Unlike RAID 4, parity information is distributed among the drives, requiring all drives but one to be present to operate. Upon failure of a single drive, subsequent reads can be calculated from the distributed parity such that no data is lost. RAID 5 requires at least three disks.[13] RAID 5 implementations are susceptible to system failures because of trends regarding array rebuild time and the chance of drive failure during rebuild (see "Increasing rebuild time and failure probability" section, below).[24] Rebuilding an array requires reading all data from all disks, opening a chance for a second drive failure and the loss of the entire array.[25] In August 2012, Dell posted an advisory against the use of RAID 5 in any configuration on Dell EqualLogic arrays and RAID 50 with "Class 2 7200 RPM drives of 1 TB and higher capacity" for business-critical data.[2