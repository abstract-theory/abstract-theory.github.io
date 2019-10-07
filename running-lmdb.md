
# Running LMDB (Lightning Memory-Mapped Database)
## April 4, 2019
### updated October 10, 2019


LMDB stands for Lightning Memory-Mapped Database. LMDB is a disk based key-value database that is relatively easy to use.

Originally, I had a web app with 12 data files. Soon 12 files became 100,000 files, and then 200,000. The web app was self-contained, easy to maintain, and horizontally scalable. Additionally, the app’s performance eclipsed that of the network (and the internet) it was running on. There was only one problem: A lot of systems seem to be get bogged down when 200,000 files are moved from one place to another. This makes it a pain to copy, sync, and upload, even if the files are tiny. Additionally, IDEs are very bad at keeping track of changes to source files when there are 200,000 of them. These problem can be solved by using a database such as LMDB.

Trying to run BerkleyDB produced errors. In contrast, LMDB ran out of the box with no configuration. There is not a lot written about LMDB, so below you will find a some example code.

Currently Redis is ridiculously fast and among the very top performing key/value databases. In benchmarks comparing LMDB to a persistent Redis DB, the write speed of LMDB was substantially faster. For reads, LMDB's performance was about half that of Redis.


```python
import random
import lmdb

randStr = lambda: ''.join(
    [chr(random.randrange(ord('a'), ord('a')+26)) for _ in range(8)]
)

N = 5
demo_db = lmdb.open('demo.lmdb')

keys = [None] * N
input_values = [None] * N
for n in range(N):
    keys[n] = randStr()
    input_values[n] = randStr()

# write values
with demo_db.begin(write=True) as db:
    for n in range(N):
        db.put(keys[n].encode(), input_values[n].encode())

# read values
output_values = [None] * N
with demo_db.begin() as db:
    for n in range(N):
        output_values[n] = db.get(keys[n].encode()).decode()

# compare input and output values
print("keys      input       output")
print("-" * 30)
for n in range(N):
    print("%s: %s == %s" % (keys[n], input_values[n], output_values[n]))
```

