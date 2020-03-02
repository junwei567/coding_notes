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
### Making a multiplication table
```
def multiplication_table(n):
    if n < 1:
        return None
    else:
        big_table = []
        counter = 1
        
        while counter <= n:
            small_table = []
            for i in range(1,n+1):
                small_table.append(i * counter)
            counter += 1
            big_table.append(small_table)
            
    return big_table
```

### Finding it a number is a palindrome
```
def is_palindrome(num):
    string = str(num)
    a = len(string)
    counter = 0
    decounter = -1
    while counter < a:
        if string[counter] != string[(decounter)]:
            return False
        counter += 1
        decounter -= 1
    return True
```

### Checking for a prime number
```
def is_prime(x):
    if x >= 2:
        for y in range(2,x):
            if not ( x % y ):
                return False
    else:
        return False
    return True
```

###### to be *continued*...