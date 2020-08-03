### yank multiple lines into a named register
- yank 5 lines into register a: `"a5yy`, paste register a under the cursor: `"ap`, paster register a before the cursor:
  `"aP`
- yank line 5 - 10 into register a: `:5,10y a`, paste register `a` under line 7: `:7pu a`
### copy paste with OS clipboard
vim has a register + for OS clipboard
1. copy to clipboard: `"+y`
2. paste from clipboard: `"+p`

