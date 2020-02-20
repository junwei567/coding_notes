## Functions I wrote


### Transpose Matrix
```
def transpose_matrix(matrix):
    new_matrix = []
    n = 0
    for x in range(0,len(matrix[0])):
        new_list = []
        for i in matrix:
            new_list.append(i[n])
    
        n += 1
        new_matrix.append(new_list)
    return new_matrix
```

### Getting value from key in dictionary
```
def get_details(name, key, lst):
    for contact in lst:
        # contact is a dictionary
        if contact["name"] == name:
            info = key
            return contact[info]
```

### Getting key from value 
```
d = {0:1963, 1:2345, 2:3457, 3:9745}
lookup = 2345
key_lookup = None

for key, value in d.items():
    if value == lookup:
        key_lookup = key
        break
        
print(key_lookup)
```

### Getting most frequent value from dictionary
```
def most_frequent(lst):
    freq_dict = {}
    for i in lst: 
        if i not in freq_dict:
            freq_dict[i] = 1
        else:
            freq_dict[i] += 1
            
    max_value = max(freq_dict.values())
    key_lookup = []
    
    for key, value in freq_dict.items():
        if value == max_value:
            key_lookup.append(key)
            
    return key_lookup
```

###### to be *continued*...