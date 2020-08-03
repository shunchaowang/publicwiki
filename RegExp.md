#### String Interpolate like javascript ES6 string liberal
* string template 'I have {name} of {value}.'
* variables: param[name]=Leo, param[value]=Allen
- `/\{([^}]+)\}/g` is the regex to match all {...} 
```
/       - delimiter
\{      - opening brace excaped because it is a special character used for quantifiers eg {1,3}
(       - start capturing
[^}]    - character class consisting of
    ^   - not
    }   - a closing brace (no escaping necessary because special characters in a character class are different)
+       - one or more of the character class
)       - end capturing
\}      - the closing literal brace
/       - delimiter
```

