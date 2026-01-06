# Examples of `mini.surround` and `mini.ai`

## `mini.surround` Examples

### 1. Add Surrounding Brackets to a Word
- **Action**: Wrap the word `word` with parentheses `()`.
- **Command**:  
  `saiw(`
- **Result**:  
  `(word)`

### 2. Replace Existing Surrounding Characters
- **Action**: Change surrounding parentheses `()` around `text` to square brackets `[]`.
- **Command**:  
  `cs([`
- **Result**:  
  `[text]`

### 3. Remove Surrounding Quotes
- **Action**: Remove double quotes surrounding `"example"`.
- **Command**:  
  `sdq`
- **Result**:  
  `example`

### 4. Add a Function Call
- **Action**: Wrap `calculate` with a function call `func()`.
- **Command**:  
  `saiwfunc<CR>`
- **Result**:  
  `func(calculate)`

### 5. Wrap a Line with a Markdown Header
- **Action**: Wrap the line `Title` with `##` for a Markdown header.
- **Command**:  
  `saL##`
- **Result**:  
  `## Title ##`

### 6. Add Surrounding Tags
- **Action**: Surround `content` with `<div>` tags.
- **Command**:  
  `saiw<div>`
- **Result**:  
  `<div>content</div>`

### 7. Change Surrounding Symbols
- **Action**: Replace surrounding quotes in `"text"` with single quotes.
- **Command**:  
  `cs"'`
- **Result**:  
  `'text'`



## Textobjects
<leader>. or , swap params



## nvim tree open ctrl +x , and ctrl + v for splits

Next: [Software Security](../cybersecurity/softwaresecurity.md)
