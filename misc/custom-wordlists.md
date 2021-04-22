# Custom wordlists

[back](readme.md)

## Creating wordlist

Cewl will generate wordlist from contents of a webpage.

```text
cewl <url> >> wordlist.txt
```

```text
crunch <min_len> <max_len> -t <pattern>

pattern:
@ lowercase
, uppercase
% number
^ special
```

## Filtering wordlists

Below is a list of grep commands that will filter output, and write the filtered output to a file.

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



