tvier.net

generate the site in the `docs` directory for it to be served by github
```
hugo -d docs
```

For renaming images from obsidian
```
for i in  "$remove"*;do mv "$i" "${i#"$remove"}";done
```
https://unix.stackexchange.com/questions/163864/how-to-remove-prefix-from-multiple-files-in-a-directory
