# Examples

https://regexr.com/

## hex numbers
```bash
0[xX][0-9a-fA-F]+
```

## quoted strings

```bash
\".+\"
```

## until space
```bash
[^\s]+
```

# Concepts
## greedy search
```
# full match
<.+>
<em>Hello World</em>

# matches
<em>Hello World</em>
```

## lazy search
```
# full match
<.+?>
<em>Hello World</em>
# matches
<em>
</em>
```

## Negative lookahead
from [here](https://www.regular-expressions.info/lookaround.html)

* if you want to match something not followed by something else.

```bash
± cat test
iraq
iraq is a country
what the fuck
queens of the stone age
qaasi

± ack 'q(?=u)' test
queens of the stone age

± ack 'q(?!u)' test
iraq
iraq is a country
qaasi
```



## Positive lookahead

# Metacharacters

### `.` dot
any one character

### `[...]` character class
any character listed

### `[^...]` negated character class
any character not listed

### `^` caret
position at start of the line

### `$` dollar sign
position at end of the line

### `\<` backslash less-than
the position at the start of the word

### `\<` backslash greater-than
the position at the end of the word

### `|` or, bar
matches either expression it separates

### `(...)`
used to limit scope of `|`, and other stuff

### `+` plus
one or more of the immediately-preceding item

### `*` star
try to match it as many times as possible, but it’s OK to settle for nothing if need be

### `\w`
alphanumber or underscore characters
