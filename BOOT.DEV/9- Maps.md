The zero value of maps is `nil`.

We can create a map using the `make()` function:

```go
ages := make(map[string]int)
ages["John"] = 37
ages["Mary"] = 24
ages["Mary"] = 21 // overwrites 24
```

Or by using literal:

```go
ages := map[string]int{
	"John": 37,
	"Mary": 21,
}
```

The `len()` works on maps, it returns the number of key/value pairs.

**Code Example:**

```go
func getUserMap(names []string, phoneNumbers []int) (map[string]user, error) {

	if len(names) != len(phoneNumbers) {
		return nil, errors.New("invalid sizes")
	}
	
	namesUsers := make(map[string]user)

	for i,username := range names {
		namesUsers[username] = user{
			name: username,
			phoneNumber: phoneNumbers[i],
		}
	}

	return namesUsers, nil
}
```

# Mutations

## Insert an Element

```go
m[key] = elem
```
## Get an Element

```go
elem = m[key]
```
## Delete an Element

```go
delete(m, key)
```

## Check If a Key Exists

```go
elem, ok := m[key]
```

- If `key` is in `m`, then `ok` is `true` and `elem` is the value as expected.
- If `key` is not in the map, then `ok` is `false` and `elem` is the zero value for the map's element type.

# Key Types

Slices, Maps, Functions cannot be used as a key for a map.

# Count Instances

You can combine an `if` statement with an assignment operation to use the variables inside the `if` block:

```go
names := map[string]int{}
missingNames := []string{}

if _, ok := names["Denna"]; !ok {
    // if the key doesn't exist yet,
    // append the name to the missingNames slice
    missingNames = append(missingNames, "Denna")
}
```

# Nested

Maps can contain maps, creating a nested structure. For example:

```go
map[string]map[string]int
map[rune]map[string]int
map[int]map[string]map[string]int
```

## Runes

Runes in Go represents the **Unicode Code** for a single character, it handles the individual characters including non-ASCII ones.

**Code Syntax:**

```go
var r rune = 'b'

word := "Hello"

runes := []rune(word)
r := runes[0]
```

---

When dealing with nested maps in Go, we have to initialize both the inner and outer map, for example if the inner map was `nil`, this will cause a panic.

**Code Example:**

```go
// Outer Key: runes, Inner Key: string, Value: int 
var nested map[rune]map[string]int

// Initialization (THE CRITICAL PART)
nested := make(map[rune]map[string]int)

// Before writing to Inner Map, check if it exists
if nested['b'] == nil {
	nested['b'] = make(map[string]int)
}
nested['b']["billy"]++;
```

**Full Code Example:**

```go
 package main

func getNameCounts(names []string) map[rune]map[string]int {

	nameCounts := make(map[rune]map[string]int)

	for _, name := range names {
		nameRune := []rune(name)
		if _,exists := nameCounts[nameRune[0]]; !exists {
			nameCounts[nameRune[0]] = make(map[string]int)
		}
		nameCounts[nameRune[0]][name]++
	}

	return nameCounts
}
```

```ad-info
We can use **strings.Fields("Your String Here")** to strip out white spaces in a string.

**Output:** ["Your", "String", "Here"]
```

**Code Example:**

```go
package main

import (
	"strings"
)

func countDistinctWords(messages []string) int {
	distinctWordsStruct := make(map[string]struct{})

	for _,message := range messages {
		message = strings.ToLower(message)
		filteredMessages := strings.Fields(message)

		for _,filteredMessage := range filteredMessages {
			distinctWordsStruct[filteredMessage] = struct{}{}
		}
	}

	return len(distinctWordsStruct)
	
}
```

