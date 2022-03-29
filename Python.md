# Python

These notes are based mostly on Reverse Engineering

## Function Hooking

Python is a dynamic language which makes it possible to replace the implementation of any function at runtime.

```py
import base64
original = base64.b64decode

def decode(s):
  print("Function Hooked")
  print(s)
  return original(s)

base64.b64decode = decode

print(base64.b64decode("ZW=="))
```



