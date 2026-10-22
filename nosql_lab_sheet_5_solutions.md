# COER UNIVERSITY

### Course: BCA305P - NoSQL Database Design

**Lab Sheet - 5: Riak KV & Neo4j (Key-Value and Graph Databases) Solutions**

---

## Environment & Shell Initialization

### Riak KV
```bash
$ riak ping
<paste your output>
$ riak version
<paste your Riak version>
```

### Neo4j (`cypher-shell`)
```bash
$ cypher-shell --version
<paste your cypher-shell version>
```

---

## Question 1: Riak KV Installation & Student Bucket CRUD

**Problem:** Install and configure Riak KV on a Linux system. Create a bucket named `students` and perform basic key-value operations such as storing, retrieving, updating, and deleting student records.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 1: Basic key-value operations on bucket "students"
source ./config.sh

echo "--- check node is up ---"
curl -s $RIAK/ping; echo

echo "--- CREATE (store) student records ---"
curl -s -XPUT -H "$JSON" $RIAK/buckets/students/keys/s101 -d '{"name":"Ravi Kumar","course":"BCA","year":3,"cgpa":8.1}'
curl -s -XPUT -H "$JSON" $RIAK/buckets/students/keys/s102 -d '{"name":"Anjali Sharma","course":"BCA","year":3,"cgpa":8.7}'

echo "--- READ (retrieve) ---"
curl -s $RIAK/buckets/students/keys/s101; echo

echo "--- UPDATE (PUT again on same key overwrites value) ---"
curl -s -XPUT -H "$JSON" $RIAK/buckets/students/keys/s101 -d '{"name":"Ravi Kumar","course":"BCA","year":3,"cgpa":8.4}'
curl -s $RIAK/buckets/students/keys/s101; echo

echo "--- DELETE ---"
curl -s -XDELETE $RIAK/buckets/students/keys/s102
curl -s -o /dev/null -w "GET after delete -> HTTP %{http_code}\n" $RIAK/buckets/students/keys/s102
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 2: User Session Store in Riak KV

**Problem:** Create a `user_sessions` bucket to store user session information. Store session ID, user ID, login time, and session status, and retrieve session information using its key.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 2: Session store in bucket "user_sessions"
source ./config.sh

SID="sess_9f2a71"
curl -s -XPUT -H "$JSON" $RIAK/buckets/user_sessions/keys/$SID \
  -d "{\"session_id\":\"$SID\",\"user_id\":\"u_1001\",\"login_time\":\"$(date -u +%Y-%m-%dT%H:%M:%SZ)\",\"status\":\"active\"}"

echo "--- retrieve session by key ---"
curl -s $RIAK/buckets/user_sessions/keys/$SID; echo

echo "--- logout: mark session expired ---"
curl -s $RIAK/buckets/user_sessions/keys/$SID | jq '.status="expired"' | \
  curl -s -XPUT -H "$JSON" $RIAK/buckets/user_sessions/keys/$SID -d @-
curl -s $RIAK/buckets/user_sessions/keys/$SID; echo
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 3: User Profiles & Preferences in Riak KV

**Problem:** Design a Riak KV application for storing user profiles and preferences (name, email, city, language, theme, notification preferences). Demonstrate PUT and GET operations.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 3: User profiles + preferences (PUT and GET)
source ./config.sh

curl -s -XPUT -H "$JSON" $RIAK/buckets/user_profiles/keys/u_1001 -d '{
  "name":"Priya Singh","email":"priya@example.com","city":"Dehradun",
  "preferences":{"language":"Hindi","theme":"dark","notifications":{"email":true,"sms":false,"push":true}}
}'

curl -s -XPUT -H "$JSON" $RIAK/buckets/user_profiles/keys/u_1002 -d '{
  "name":"Arjun Verma","email":"arjun@example.com","city":"Roorkee",
  "preferences":{"language":"English","theme":"light","notifications":{"email":true,"sms":true,"push":false}}
}'

echo "--- GET profile ---"
curl -s $RIAK/buckets/user_profiles/keys/u_1001 | jq .

echo "--- GET only theme preference ---"
curl -s $RIAK/buckets/user_profiles/keys/u_1001 | jq -r '.preferences.theme'
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 4: Shopping Cart Application in Riak KV

**Problem:** Store a shopping cart for each user containing product IDs, quantities, and prices. Demonstrate adding, updating, retrieving, and removing products from the cart.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 4: Shopping cart, one key per user in bucket "carts"
source ./config.sh
U=cart_u_1001
put() { curl -s -XPUT -H "$JSON" $RIAK/buckets/carts/keys/$U -d @-; }

echo "--- create cart with 2 products ---"
echo '{"user":"u_1001","items":[{"product_id":"P10","qty":1,"price":499},{"product_id":"P22","qty":2,"price":150}]}' | put
curl -s $RIAK/buckets/carts/keys/$U | jq -c .

echo "--- add product P35 ---"
curl -s $RIAK/buckets/carts/keys/$U | jq '.items += [{"product_id":"P35","qty":1,"price":999}]' | put
curl -s $RIAK/buckets/carts/keys/$U | jq -c .

echo "--- update quantity of P22 to 5 ---"
curl -s $RIAK/buckets/carts/keys/$U | jq '(.items[] | select(.product_id=="P22") | .qty) = 5' | put
curl -s $RIAK/buckets/carts/keys/$U | jq -c .

echo "--- remove P10 ---"
curl -s $RIAK/buckets/carts/keys/$U | jq 'del(.items[] | select(.product_id=="P10"))' | put
curl -s $RIAK/buckets/carts/keys/$U | jq -c .

echo "--- remove whole cart ---"
curl -s -XDELETE $RIAK/buckets/carts/keys/$U
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 5: Structure of Data in a Key-Value Store

**Problem:** Store different types of values such as strings, JSON documents, and lists. Retrieve the stored values and explain the role of bucket, key, and value.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 5: Structure of data in a key-value store: bucket / key / value
source ./config.sh

# 1) plain string value
curl -s -XPUT -H "Content-Type: text/plain" $RIAK/buckets/demo/keys/greeting -d 'Hello Riak'
# 2) JSON document value
curl -s -XPUT -H "$JSON" $RIAK/buckets/demo/keys/book1 -d '{"title":"NoSQL Distilled","author":"Sadalage & Fowler","year":2012}'
# 3) list value (stored as a JSON array; Riak itself treats it as an opaque blob)
curl -s -XPUT -H "$JSON" $RIAK/buckets/demo/keys/fruits -d '["apple","mango","banana"]'

for k in greeting book1 fruits; do
  echo "--- GET demo/$k ---"; curl -s -i $RIAK/buckets/demo/keys/$k | sed -n '1p;/Content-Type/p;$p'; echo
done

cat <<'TXT'

Explanation
  Bucket : a namespace / logical group of keys (like a table, but with no schema). Here: "demo".
  Key    : unique identifier inside a bucket. The ONLY way to look a value up directly. Here: greeting, book1, fruits.
  Value  : the data itself. Riak does not interpret it; it can be text, JSON, list, image... (opaque blob + Content-Type).
  Full address of any object = bucket + key.
TXT
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 6: Consistency in Riak KV (Read/Write Quorum)

**Problem:** Demonstrate consistency in Riak KV by configuring suitable read and write quorum values. Perform read/write operations and explain how Riak handles consistency in a distributed environment.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 6: Consistency using N, R, W quorum values
source ./config.sh

echo "--- set bucket properties: n_val=3, r=2, w=2 (quorum = n/2+1 = 2) ---"
curl -s -XPUT -H "$JSON" $RIAK/buckets/accounts/props -d '{"props":{"n_val":3,"r":2,"w":2}}'
curl -s $RIAK/buckets/accounts/props | jq '.props | {n_val,r,w,pr,pw,allow_mult,last_write_wins}'

echo "--- write with w=2 (2 of 3 replicas must ack) ---"
curl -s -XPUT -H "$JSON" "$RIAK/buckets/accounts/keys/acc1?w=2" -d '{"owner":"Meena","balance":1000}'

echo "--- read with r=2 (2 of 3 replicas must respond) ---"
curl -s "$RIAK/buckets/accounts/keys/acc1?r=2"; echo

echo "--- strong-ish read: r=3, and weak read: r=1 ---"
curl -s "$RIAK/buckets/accounts/keys/acc1?r=3"; echo
curl -s "$RIAK/buckets/accounts/keys/acc1?r=1"; echo

cat <<'TXT'

Explanation
  Riak is an AP-style (eventually consistent) database inspired by Dynamo. Each object is stored on N replicas.
  W = replicas that must acknowledge a write, R = replicas that must answer a read.
  If R + W > N (2 + 2 > 3) a read always overlaps with at least one replica holding the latest write -> consistent reads.
  If R + W <= N (e.g. r=1, w=1) reads may return stale data but latency/availability is better.
  Conflicts (concurrent writes) are tracked with vector clocks / dotted version vectors and resolved by
  last-write-wins or by keeping siblings (allow_mult). Read-repair and active anti-entropy fix diverged replicas.
  Note: run this on a multi-node cluster (e.g. 3 nodes) to see the real effect; on a single node all replicas are local.
TXT
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 7: Scaling in Riak KV

**Problem:** Create multiple buckets and insert a large number of key-value records. Explain how Riak distributes data across nodes and supports horizontal scaling.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 7: Scaling - many buckets, many records, data distribution
source ./config.sh
COUNT=${1:-1000}

for b in orders products logs; do
  echo "inserting $COUNT records into bucket $b ..."
  for i in $(seq 1 $COUNT); do
    curl -s -XPUT -H "$JSON" $RIAK/buckets/$b/keys/${b}_$i -d "{\"id\":$i,\"bucket\":\"$b\",\"value\":$((RANDOM))}" >/dev/null
  done
done

echo "--- verify a few records ---"
curl -s $RIAK/buckets/orders/keys/orders_500; echo
curl -s $RIAK/buckets/logs/keys/logs_$COUNT; echo

echo "--- cluster / ring status (shows how partitions are spread across nodes) ---"
sudo riak-admin member-status
sudo riak-admin ring-status

cat <<'TXT'

Explanation
  Riak uses consistent hashing. hash(bucket + key) -> a position on a ring split into 64 partitions (vnodes) by default.
  Partitions are spread evenly over the physical nodes, so each node holds roughly (64 / number_of_nodes) vnodes.
  Adding a node: the ring is rebalanced and a few partitions are handed off to the new node automatically
  (sudo riak-admin cluster join / plan / commit), with no downtime and no manual sharding.
  This is horizontal scaling: more nodes = more storage and more read/write throughput, and each object has N copies on different nodes.
TXT
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 8: Transaction-like Scenario in Riak KV

**Problem:** Perform multiple related operations such as adding an item to a cart, updating its quantity, and updating the cart total. Discuss the limitations of transactions in a key-value database.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 8: Multi-operation "transaction-like" scenario for a shopping app
source ./config.sh
K=cart_u_2002
put() { curl -s -XPUT -H "$JSON" $RIAK/buckets/carts/keys/$K -d @-; }

echo "--- start: empty cart ---"
echo '{"user":"u_2002","items":[],"total":0}' | put

echo "--- step 1: add item ---"
curl -s $RIAK/buckets/carts/keys/$K | jq '.items += [{"product_id":"P77","qty":1,"price":300}]' | put

echo "--- step 2: update quantity ---"
curl -s $RIAK/buckets/carts/keys/$K | jq '(.items[] | select(.product_id=="P77") | .qty) = 3' | put

echo "--- step 3: recompute cart total ---"
curl -s $RIAK/buckets/carts/keys/$K | jq '.total = ([.items[] | .qty * .price] | add)' | put

curl -s $RIAK/buckets/carts/keys/$K | jq .

cat <<'TXT'

Limitations of transactions in a key-value store
  1. Riak has no ACID multi-key transactions: each PUT is atomic only for ONE key.
  2. If the client crashes between step 1 and step 3, the cart is left half-updated (item added but total old). No rollback.
  3. Read-modify-write is not safe under concurrency: two clients can overwrite each other (lost update) or create siblings.
  4. No locks, no isolation levels, no joins.
  Workarounds: keep the whole cart in ONE key (so one PUT = one atomic change, as in this script if steps are merged),
  use vector clocks / siblings with a merge function, use CRDT data types, or use a strongly consistent bucket type.
TXT
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 9: Query-by-Data in Riak KV

**Problem:** Store JSON documents containing user information and demonstrate how application-level logic can be used to locate users based on attributes such as city, age, or preferences.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 9: Query-by-data using application-level logic (no secondary index / Solr)
source ./config.sh

add() { curl -s -XPUT -H "$JSON" $RIAK/buckets/people/keys/$1 -d "$2"; }
add p1 '{"name":"Aman","city":"Dehradun","age":21,"interests":["cricket","coding"]}'
add p2 '{"name":"Sana","city":"Roorkee","age":23,"interests":["music","coding"]}'
add p3 '{"name":"Kabir","city":"Dehradun","age":25,"interests":["gaming"]}'
add p4 '{"name":"Isha","city":"Haridwar","age":22,"interests":["music","dance"]}'

# list keys (fine for a small demo; avoid listing keys on a large production cluster)
KEYS=$(curl -s "$RIAK/buckets/people/keys?keys=true" | jq -r '.keys[]')

# fetch every document once, put in one JSON array so we can filter it in the app
ALL=$(for k in $KEYS; do curl -s $RIAK/buckets/people/keys/$k | jq --arg id "$k" '. + {id:$id}'; done | jq -s .)

echo "--- users in Dehradun ---";        echo "$ALL" | jq -c '.[] | select(.city=="Dehradun")'
echo "--- users older than 22 ---";      echo "$ALL" | jq -c '.[] | select(.age>22)'
echo "--- users interested in coding ---"; echo "$ALL" | jq -c '.[] | select(.interests | index("coding"))'

cat <<'TXT'

Note: Riak KV can only fetch by key. To search by attribute we either scan (as above), or build our own index
(e.g. a key like "city:Dehradun" -> ["p1","p3"]), or enable secondary indexes / Riak Search (Solr) for real queries.
TXT
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 10: Set Operations in Riak KV

**Problem:** Create sets containing user interests or product categories and demonstrate adding elements, removing elements, checking membership, and finding common elements.

### Riak KV Execution Commands

```bash
#!/bin/bash
# Exp 10: Set operations using the Riak "set" CRDT data type
source ./config.sh

echo "--- one-time setup: create + activate a bucket type for sets ---"
sudo riak-admin bucket-type create sets '{"props":{"datatype":"set"}}'
sudo riak-admin bucket-type activate sets

S="$RIAK/types/sets/buckets/interests/datatypes"

echo "--- add elements ---"
curl -s -XPOST -H "$JSON" $S/user1 -d '{"add_all":["coding","music","cricket"]}'
curl -s -XPOST -H "$JSON" $S/user2 -d '{"add_all":["music","gaming","coding"]}'
curl -s $S/user1 | jq -c .value

echo "--- remove element (needs the causal context from a fetch) ---"
CTX=$(curl -s $S/user1 | jq -r .context)
curl -s -XPOST -H "$JSON" $S/user1 -d "{\"remove\":\"cricket\",\"context\":\"$CTX\"}"
curl -s $S/user1 | jq -c .value

echo "--- check membership: is 'music' in user1? ---"
curl -s $S/user1 | jq '.value | index("music") != null'

echo "--- common elements of user1 and user2 (intersection, done in the client) ---"
A=$(curl -s $S/user1 | jq -c .value); B=$(curl -s $S/user2 | jq -c .value)
jq -nc --argjson a "$A" --argjson b "$B" '$a - ($a - $b)'
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 11: Neo4j Installation & University Graph

**Problem:** Install and configure Neo4j. Create a graph containing `Student`, `Course`, and `Department` nodes with appropriate relationships. Insert sample data and visualize the resulting graph.

### Neo4j Execution Commands

```cypher
// Exp 11: Student - Course - Department graph
MATCH (n) DETACH DELETE n;

CREATE (cs:Department {name:'Computer Science'}), (ec:Department {name:'Electronics'});

CREATE (c1:Course {code:'BCA301', title:'NoSQL Database Design', credits:4}),
       (c2:Course {code:'BCA302', title:'Web Development',       credits:4}),
       (c3:Course {code:'EC201',  title:'Digital Electronics',   credits:3});

CREATE (s1:Student {roll:1, name:'Ravi'}),
       (s2:Student {roll:2, name:'Anjali'}),
       (s3:Student {roll:3, name:'Kabir'});

MATCH (c:Course {code:'BCA301'}), (d:Department {name:'Computer Science'}) CREATE (c)-[:OFFERED_BY]->(d);
MATCH (c:Course {code:'BCA302'}), (d:Department {name:'Computer Science'}) CREATE (c)-[:OFFERED_BY]->(d);
MATCH (c:Course {code:'EC201'}),  (d:Department {name:'Electronics'})      CREATE (c)-[:OFFERED_BY]->(d);

MATCH (s:Student {name:'Ravi'}),   (d:Department {name:'Computer Science'}) CREATE (s)-[:BELONGS_TO]->(d);
MATCH (s:Student {name:'Anjali'}), (d:Department {name:'Computer Science'}) CREATE (s)-[:BELONGS_TO]->(d);
MATCH (s:Student {name:'Kabir'}),  (d:Department {name:'Electronics'})      CREATE (s)-[:BELONGS_TO]->(d);

MATCH (s:Student {name:'Ravi'}),   (c:Course {code:'BCA301'}) CREATE (s)-[:ENROLLED_IN {grade:'A'}]->(c);
MATCH (s:Student {name:'Ravi'}),   (c:Course {code:'BCA302'}) CREATE (s)-[:ENROLLED_IN {grade:'B+'}]->(c);
MATCH (s:Student {name:'Anjali'}), (c:Course {code:'BCA301'}) CREATE (s)-[:ENROLLED_IN {grade:'A+'}]->(c);
MATCH (s:Student {name:'Kabir'}),  (c:Course {code:'EC201'})  CREATE (s)-[:ENROLLED_IN {grade:'B'}]->(c);

// Visualize (run in Neo4j Browser and take a screenshot):
MATCH p = (s:Student)-[]->(x) RETURN p;
// Extra queries
MATCH (s:Student)-[:ENROLLED_IN]->(c:Course {code:'BCA301'}) RETURN s.name;
MATCH (c:Course)-[:OFFERED_BY]->(d:Department) RETURN d.name, collect(c.title) AS courses;
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 12: Social Network Graph using Cypher

**Problem:** Create a social-network graph containing `Person` nodes and relationships such as `FRIENDS_WITH`, `FOLLOWS`, and `WORKS_AT`. Write queries to find a person's friends and followers.

### Neo4j Execution Commands

```cypher
// Exp 12: Social network
MATCH (n) DETACH DELETE n;

CREATE (a:Person {name:'Aman'}), (b:Person {name:'Bhavna'}), (c:Person {name:'Chirag'}),
       (d:Person {name:'Divya'}), (e:Person {name:'Esha'});
CREATE (g:Company {name:'TechNova'}), (h:Company {name:'DataWorks'});

MATCH (a:Person {name:'Aman'}),(b:Person {name:'Bhavna'}) CREATE (a)-[:FRIENDS_WITH]->(b), (b)-[:FRIENDS_WITH]->(a);
MATCH (a:Person {name:'Aman'}),(c:Person {name:'Chirag'}) CREATE (a)-[:FRIENDS_WITH]->(c), (c)-[:FRIENDS_WITH]->(a);
MATCH (b:Person {name:'Bhavna'}),(d:Person {name:'Divya'}) CREATE (b)-[:FRIENDS_WITH]->(d), (d)-[:FRIENDS_WITH]->(b);

MATCH (d:Person {name:'Divya'}),(a:Person {name:'Aman'}) CREATE (d)-[:FOLLOWS]->(a);
MATCH (e:Person {name:'Esha'}), (a:Person {name:'Aman'}) CREATE (e)-[:FOLLOWS]->(a);
MATCH (c:Person {name:'Chirag'}),(e:Person {name:'Esha'}) CREATE (c)-[:FOLLOWS]->(e);

MATCH (a:Person {name:'Aman'}),   (g:Company {name:'TechNova'}) CREATE (a)-[:WORKS_AT]->(g);
MATCH (b:Person {name:'Bhavna'}), (g:Company {name:'TechNova'}) CREATE (b)-[:WORKS_AT]->(g);
MATCH (d:Person {name:'Divya'}),  (h:Company {name:'DataWorks'}) CREATE (d)-[:WORKS_AT]->(h);

// Friends of Aman
MATCH (:Person {name:'Aman'})-[:FRIENDS_WITH]->(f:Person) RETURN f.name AS friends;
// Followers of Aman
MATCH (f:Person)-[:FOLLOWS]->(:Person {name:'Aman'}) RETURN f.name AS followers;
// Friends-of-friends (suggestions)
MATCH (:Person {name:'Aman'})-[:FRIENDS_WITH]->()-[:FRIENDS_WITH]->(s:Person)
WHERE s.name <> 'Aman' RETURN DISTINCT s.name AS suggestion;
// Colleagues
MATCH (:Person {name:'Aman'})-[:WORKS_AT]->(c)<-[:WORKS_AT]-(col) RETURN c.name, col.name;
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 13: E-commerce Graph in Neo4j

**Problem:** Design a shopping/e-commerce graph containing `Customer`, `Product`, `Category`, and `Order` nodes. Create appropriate relationships and write Cypher queries to find products purchased by a particular customer.

### Neo4j Execution Commands

```cypher
// Exp 13: E-commerce graph
MATCH (n) DETACH DELETE n;

CREATE (:Category {name:'Electronics'}), (:Category {name:'Books'});
CREATE (:Product {id:'P1', name:'Headphones', price:1999}),
       (:Product {id:'P2', name:'Keyboard',   price:1499}),
       (:Product {id:'P3', name:'NoSQL Distilled', price:650});
CREATE (:Customer {id:'C1', name:'Ravi'}), (:Customer {id:'C2', name:'Anjali'});
CREATE (:Order {id:'O1', date:date('2026-09-10')}), (:Order {id:'O2', date:date('2026-09-15')}),
       (:Order {id:'O3', date:date('2026-10-01')});

MATCH (p:Product {id:'P1'}),(c:Category {name:'Electronics'}) CREATE (p)-[:IN_CATEGORY]->(c);
MATCH (p:Product {id:'P2'}),(c:Category {name:'Electronics'}) CREATE (p)-[:IN_CATEGORY]->(c);
MATCH (p:Product {id:'P3'}),(c:Category {name:'Books'})       CREATE (p)-[:IN_CATEGORY]->(c);

MATCH (c:Customer {id:'C1'}),(o:Order {id:'O1'}) CREATE (c)-[:PLACED]->(o);
MATCH (c:Customer {id:'C1'}),(o:Order {id:'O3'}) CREATE (c)-[:PLACED]->(o);
MATCH (c:Customer {id:'C2'}),(o:Order {id:'O2'}) CREATE (c)-[:PLACED]->(o);

MATCH (o:Order {id:'O1'}),(p:Product {id:'P1'}) CREATE (o)-[:CONTAINS {qty:1}]->(p);
MATCH (o:Order {id:'O1'}),(p:Product {id:'P3'}) CREATE (o)-[:CONTAINS {qty:2}]->(p);
MATCH (o:Order {id:'O2'}),(p:Product {id:'P1'}) CREATE (o)-[:CONTAINS {qty:1}]->(p);
MATCH (o:Order {id:'O3'}),(p:Product {id:'P2'}) CREATE (o)-[:CONTAINS {qty:1}]->(p);

// Products purchased by customer Ravi
MATCH (:Customer {name:'Ravi'})-[:PLACED]->(:Order)-[r:CONTAINS]->(p:Product)
RETURN p.name AS product, sum(r.qty) AS total_qty;
// Recommendation: others who bought what Ravi bought also bought...
MATCH (:Customer {name:'Ravi'})-[:PLACED]->()-[:CONTAINS]->(p:Product)<-[:CONTAINS]-()<-[:PLACED]-(o:Customer)
MATCH (o)-[:PLACED]->()-[:CONTAINS]->(rec:Product) RETURN DISTINCT rec.name;
// Revenue per category
MATCH (:Order)-[r:CONTAINS]->(p:Product)-[:IN_CATEGORY]->(c:Category)
RETURN c.name, sum(r.qty * p.price) AS revenue;
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 14: Transactions, Consistency & Availability in Neo4j

**Problem:** Perform multiple `CREATE`, `MATCH`, `SET`, and `DELETE` operations inside a transaction and explain how transaction management maintains consistent graph data.

### Neo4j Execution Commands

```cypher
// Exp 14: Transactions in Neo4j. Run with cypher-shell (it supports :begin / :commit / :rollback)
//   cypher-shell -u neo4j -p <password> -f 14_transactions.cypher

MATCH (n:Acc) DETACH DELETE n;

// ---- Transaction 1: COMMIT (all-or-nothing) ----
:begin
CREATE (:Acc {id:'A1', owner:'Ravi',   balance:5000});
CREATE (:Acc {id:'A2', owner:'Anjali', balance:2000});
MATCH (a:Acc {id:'A1'}), (b:Acc {id:'A2'})
SET a.balance = a.balance - 1500, b.balance = b.balance + 1500
CREATE (a)-[:TRANSFERRED {amount:1500, at:datetime()}]->(b);
:commit

MATCH (a:Acc) RETURN a.id, a.owner, a.balance;      // A1=3500, A2=3500

// ---- Transaction 2: ROLLBACK (nothing is saved) ----
:begin
MATCH (a:Acc {id:'A1'}) SET a.balance = a.balance - 9999;
CREATE (:Acc {id:'TEMP', owner:'ghost', balance:0});
MATCH (a:Acc) RETURN a.id, a.balance;              // changes visible inside the transaction only
:rollback

MATCH (a:Acc) RETURN a.id, a.balance;              // back to the committed state, no TEMP node

// ---- Transaction 3: DELETE inside a transaction ----
:begin
MATCH (a:Acc {id:'A2'}) DETACH DELETE a;
:commit
MATCH (a:Acc) RETURN a.id;

// Explanation (also in README):
//  * Neo4j is fully ACID: Atomic (all or nothing), Consistent (constraints hold), Isolated (read-committed),
//    Durable (write-ahead transaction log).
//  * Availability: in a cluster, Neo4j uses Raft - a primary writes need a majority; secondaries serve reads,
//    so reads stay available if some members go down.
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---

## Question 15: Graph Database Application (Neo4j + Python)

**Problem:** Develop a small Graph Database application using Neo4j and Python. Implement CRUD operations and Cypher queries. Demonstrate relationship-based queries, scaling, availability, and suitable use cases such as social networks, recommendation systems, fraud detection, and knowledge graphs.

### Neo4j Execution Commands

```python
"""Exp 15: Small graph application using Neo4j + Python (CRUD + Cypher queries).

Run:
    pip install -r requirements.txt
    export NEO4J_URI=bolt://localhost:7687 NEO4J_USER=neo4j NEO4J_PASSWORD=yourpassword
    python app.py
"""
import os
from neo4j import GraphDatabase

URI = os.getenv("NEO4J_URI", "bolt://localhost:7687")
USER = os.getenv("NEO4J_USER", "neo4j")
PASSWORD = os.getenv("NEO4J_PASSWORD", "password")

driver = GraphDatabase.driver(URI, auth=(USER, PASSWORD))


def run(query, **params):
    with driver.session() as session:
        return [r.data() for r in session.run(query, **params)]


# ---------- CREATE ----------
def add_person(name, city):
    run("MERGE (p:Person {name:$name}) SET p.city=$city", name=name, city=city)

def add_product(name, category):
    run("MERGE (c:Category {name:$cat}) MERGE (p:Product {name:$name}) MERGE (p)-[:IN_CATEGORY]->(c)",
        name=name, cat=category)

def add_friend(a, b):
    run("MATCH (x:Person {name:$a}),(y:Person {name:$b}) "
        "MERGE (x)-[:FRIENDS_WITH]->(y) MERGE (y)-[:FRIENDS_WITH]->(x)", a=a, b=b)

def add_purchase(person, product):
    run("MATCH (x:Person {name:$p}),(y:Product {name:$prod}) MERGE (x)-[:BOUGHT]->(y)",
        p=person, prod=product)

# ---------- READ ----------
def get_person(name):
    return run("MATCH (p:Person {name:$name}) RETURN p.name AS name, p.city AS city", name=name)

def friends_of(name):
    return run("MATCH (:Person {name:$n})-[:FRIENDS_WITH]->(f) RETURN f.name AS friend", n=name)

def friend_suggestions(name):
    """relationship-based query: friends of friends who are not already friends"""
    return run("""MATCH (me:Person {name:$n})-[:FRIENDS_WITH]->()-[:FRIENDS_WITH]->(s:Person)
                  WHERE s <> me AND NOT (me)-[:FRIENDS_WITH]->(s)
                  RETURN DISTINCT s.name AS suggestion""", n=name)

def recommend_products(name):
    """recommendation: products bought by my friends that I have not bought"""
    return run("""MATCH (me:Person {name:$n})-[:FRIENDS_WITH]->(:Person)-[:BOUGHT]->(p:Product)
                  WHERE NOT (me)-[:BOUGHT]->(p)
                  RETURN p.name AS product, count(*) AS friends_bought ORDER BY friends_bought DESC""", n=name)

def shared_buyers():
    """fraud-detection style pattern: many people buying through the same product pair"""
    return run("""MATCH (a:Person)-[:BOUGHT]->(p:Product)<-[:BOUGHT]-(b:Person)
                  WHERE a.name < b.name
                  RETURN a.name AS a, b.name AS b, collect(p.name) AS common_products
                  ORDER BY size(common_products) DESC""")

# ---------- UPDATE ----------
def update_city(name, city):
    run("MATCH (p:Person {name:$name}) SET p.city=$city", name=name, city=city)

# ---------- DELETE ----------
def delete_person(name):
    run("MATCH (p:Person {name:$name}) DETACH DELETE p", name=name)


if __name__ == "__main__":
    run("MATCH (n) DETACH DELETE n")

    for n, c in [("Aman", "Dehradun"), ("Bhavna", "Roorkee"), ("Chirag", "Delhi"), ("Divya", "Haridwar")]:
        add_person(n, c)
    add_friend("Aman", "Bhavna"); add_friend("Bhavna", "Chirag"); add_friend("Bhavna", "Divya")

    for p, c in [("Laptop", "Electronics"), ("Headphones", "Electronics"), ("Python Book", "Books")]:
        add_product(p, c)
    add_purchase("Bhavna", "Laptop"); add_purchase("Bhavna", "Python Book")
    add_purchase("Chirag", "Headphones"); add_purchase("Aman", "Laptop")

    print("READ    :", get_person("Aman"))
    update_city("Aman", "Mussoorie")
    print("UPDATE  :", get_person("Aman"))
    print("Friends of Bhavna  :", friends_of("Bhavna"))
    print("Friend suggestions for Aman :", friend_suggestions("Aman"))
    print("Product recommendations for Aman :", recommend_products("Aman"))
    print("Shared buyers :", shared_buyers())
    delete_person("Divya")
    print("DELETE  : Divya removed ->", get_person("Divya"))
    driver.close()
```

### Terminal Output

```text
<paste the real output of this experiment here>
```

---
