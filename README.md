# kvrocket

`KVRocket` is a lightweight persistent key value store for Python.

## How it Works

Under the hood, KVRocket is actually usinga Python Class object to set properties on, and 
then is serialized to disk using the `dill` library. The resulting binary data is then
written to disk. KVRocket provides a batch api for writing multiple entries at once to reduce
latency when persisting the changes to disk.

Initial test show that this is fairly effective for simple, lightweight projects.

## Examples

Here is a super basic "put" and "get" example.

```python
from kvrocket import KVRocket

kv = KVRocket("simple_kv")

kv.put("hello", "world")

print(kv.get("hello")) # world
```

Bulk Put:

```python
from kvrocket import KVRocket

# initialize our kv store
kv = KVRocket("simple_kv")

# create some data...
family_members = [
    {"person::dsikes::name": "Dan", "person::dsikes::age": 29},
    {"person::jsikes::name": "Jane", "person::jsikes::age": 25},
    {"person::tharpe::name": "Tyler", "person::tharpe::age": 23},
]

try:
    kv.put_batch(family_members)
except Exception as err:
    print("batch put error: {}".format(err))
    # TODO: handle this error...

# get the value of a key..
print(kv.get("person::dsikes::name")) # Dan


# scan for all "dsikes" keys
# NOTE: the scan method will return an empty list if no keys match the provided string
# NOTE: fnmatch is used under the hood, so unix file name syntax is supported.
# NOTE: https://docs.python.org/3/library/fnmatch.html
person_keys = kv.scan("person::dsikes::*") # ["person::dsikes::name", "person::dsikes::age"]
```

## TODO:

- Write Documentation
- Gather Feedback from Users