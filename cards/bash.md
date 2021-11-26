# bash

## Go through all lines of a file

```bash
while IFS= read -r line || [[ -n "${line}" ]]; do
  echo "${line}"
  # do something cool
done < "${file}"
```

## `echo "${filename}"` without the extension

```bash
echo "${fileName%.*}"
```


## `echo "${filename}"` to show only the extension

```bash
echo "${fileName##*.}
```


## Use quotes to assign values from a var to an array

Problematic code:
```bash
array=( $var )
```

Safer code for multiple lines:
```bash
mapfile -t array <<< "${var}"
```

Safer code for space separated values:
```bash
IFS=' ' read -ra array <<< "${var}"
```

Source: SC2206


## You can't export arrays in bash. There's a workaround though.

```bash
declare -A ARRAY
#- fill the array

#- "exporting" it:
declare -p ARRAY > /tmp/ARRAY

#- "importing" it:
source /tmp/ARRAY
```

## Best way to get the scriptDir

```bash
readonly scriptDir="$(
  cd "$(dirname "$0")" && pwd
)"
```
