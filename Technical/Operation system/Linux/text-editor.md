# Text Editor
## Vim
- To find a term with case-insensitive use backslash with `c`: `/{term}\c`.
- To go to line number use `:{line number}`.
## Without editor
### `echo`
- Use `echo >` will send the output of a command to a file.
- Use `echo >>` will append the new output to an existing file. 
### `cat`
```bash
cat << EOF > myfile  
> line one  
> line two  
> line three  
> EOF  
```

