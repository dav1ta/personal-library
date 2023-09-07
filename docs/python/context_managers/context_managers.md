## EXAMPLE

```python
class ListTransaction:
    def __init__(self,thelist):
        self.thelist = thelist

    def __enter__(self):
        self.workingcopy = list(self.thelist)
        return self.workingcopy

    def __exit__(self,type,value,tb):
        if type is None:
            self.thelist[:] = self.workingcopy
        return False
```

## READING FILES USING CONTEXT MANAGER WITH CHUNKS

```python
with open('data.txt') as file:
    while (chunk := file.read(10000)):
        print(chunk, end='')
```
