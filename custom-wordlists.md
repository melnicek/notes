## Creating wordlist

Cewl will generate wordlist from contents of a webpage.

```
cewl <url> >> wordlist.txt
```

or this custom python script

```python
import re

with open("index.html", "r") as f:
    wordlist = set()
    for line in f.readlines():
        line = re.sub('[^0-9a-zA-Z]+', ' ', line)
        for l in line.split(" "):
            wordlist.add("admin:"+l)
    for word in wordlist:
        print(word)
```

## Filtering wordlists

As an example the command: `cat rockyou.txt | grep -o '[a-z]' > contains_lowercase.txt` would only keep lines of rockyou.txt that contain lowercase characters.

You can use these commands to alter rockyou.txt.

```text
grep -x '.{8,20}' > 8-20_length_wordlist
grep -o '[a-z]' > contains_lowercase.txt
grep -o '[A-Z]' > contains_uppercase.txt
grep -o '[0-9]' > contains_numbers.txt
grep -v "^[A-Za-z0-9]*$" > contains_special.txt
```

To append 123 to the end of each line: `sed -e 's/$/\123/'`

To replace the first character in a string with a capital version: `sed -e "s/\b(.)/\u\1/g"`



