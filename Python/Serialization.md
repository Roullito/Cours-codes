
# 🧊 Python - Serialization & Marshaling

## 🎯 Introduction

In modern software development, **serialization** and **marshaling** are essential techniques for converting data structures into formats that can be stored or transferred and later reconstructed. These mechanisms allow systems to save the **state of objects**, **send objects over a network**, or **store them in files**.

---

## 📦 1. What is Marshaling?

Marshaling is the **process of preparing data for transport or storage**, especially in binary form. This is common in low-level languages like **C** and systems using **remote procedure calls (RPC)**.

**In Python, marshaling is implemented in modules like:**
- `marshal` (low-level, used internally)
- `pickle` (preferred for Python objects)
- `json` (for human-readable interchange)

> 🔥 **Note**: `marshal` is unsafe for general use. It is specific to Python bytecode.

---

## 🔁 2. What is Serialization?

Serialization is the **conversion of data structures or object states into a storable/transferable format**, such as:
- Files (e.g. save data locally)
- Networks (e.g. API calls)
- Caches (e.g. Redis)

Deserialization is the **inverse** operation: loading the data back into memory.

---

## 🛠️ 3. Serialization Formats in Python

| Format   | Human-readable | Use-case examples         |
|----------|----------------|---------------------------|
| JSON     | ✅             | APIs, Web apps            |
| Pickle   | ❌             | Python object storage     |
| XML      | ✅             | Web configs, legacy APIs  |
| CSV      | ✅             | Tabular data              |
| marshal  | ❌             | Python internals only     |

---

## 🔍 4. JSON in Python

```python
import json

data = {"name": "Alice", "age": 25}
json_string = json.dumps(data)        # Serialize
print(json_string)                    # {"name": "Alice", "age": 25}

data_back = json.loads(json_string)   # Deserialize
```

### ✅ JSON is great for:
- Compatibility across languages
- REST APIs
- Config files

> 🔐 Warning: JSON does not support complex types like sets or custom classes by default.

---

## 🧪 5. Pickle: Serialize Python Objects

```python
import pickle

data = {"x": 42, "y": [1, 2, 3]}
with open("data.pkl", "wb") as f:
    pickle.dump(data, f)

with open("data.pkl", "rb") as f:
    restored = pickle.load(f)
```

### ⚠️ Notes:
- Python-only
- Not secure against untrusted input
- Can serialize any Python object

---

## 🧩 6. Use Cases in Real Life

| Use Case             | Description                                  |
|----------------------|----------------------------------------------|
| Save Game State      | Store game data for future sessions          |
| Web Cookies / JWT    | Serialize user data in tokens                |
| Microservices        | Exchange data across APIs (e.g., via JSON)   |
| Database Caching     | Store serialized queries in memory (Redis)   |
| ML Model Persistence | Save models to disk (e.g., with Pickle)      |

---

## 🧱 7. XML and CSV

### XML
```python
import xml.etree.ElementTree as ET

root = ET.Element("person")
ET.SubElement(root, "name").text = "Bob"
tree = ET.ElementTree(root)
tree.write("person.xml")
```

### CSV
```python
import csv

with open("data.csv", mode="w") as file:
    writer = csv.writer(file)
    writer.writerow(["name", "age"])
    writer.writerow(["Alice", 25])
```

---

## 🔥 8. Common Pitfalls & Debugging Tips

| Problem                            | Solution                                 |
|-----------------------------------|------------------------------------------|
| Can't serialize custom class      | Implement custom encoder/decoder         |
| Pickle load error                 | Check for version mismatch               |
| Security issues with Pickle       | Never load Pickle from untrusted source  |
| Non-serializable object (JSON)    | Convert manually to dict or str          |
| Binary vs Text confusion          | Use `rb` or `wb` modes for binary files  |

---

## 📚 Summary

- **Serialization** = Convert Python object → Storable format
- **Deserialization** = Restore object from format
- **JSON** = Universal, readable
- **Pickle** = Powerful, Python-specific
- **CSV/XML** = Structured formats, widely used

---

## 📎 References

- [Python JSON docs](https://docs.python.org/3/library/json.html)
- [Python Pickle docs](https://docs.python.org/3/library/pickle.html)
- [Automate the Boring Stuff: File I/O](https://automatetheboringstuff.com/)
- [Real Python: Working With JSON](https://realpython.com/python-json/)
