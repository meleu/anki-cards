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