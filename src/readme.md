# Game logic

## Rules
> There are more off course, but theses are the one I know there needs to be some more work to be done one currently. 

A tile can only merge once per turn. 

## Moves and merges

On a player move. Do the following. 

On move ***up** (e.g.)*
1. Start from the ***top*** cell.
1. Check if cell has a tile. If empty, store that fact and move to next cell.
1. **Find** incoming **tile** *It may stop before current cell, merge into or take its place.* If there is no incoming tile. ~~Continue to next cell.~~
1. Check if incoming tile has equal value. If so merge with curren tile.
1. ~~If current cell is empty, move incoming tile here.~~
1. If previous cell was empty, move current tile there. Store that current cell is empty.
1. Go back to step **2.**

### Test cases
``` javascript
let testColumn1 = [null,2,2,null]
should result to: 
[4,null,null,null]

let testColumn2 = [2,2,2,null]
should result to: 
[4,2,null,null]

let testColumn3 = [2,2,2,2]
should result to: 
[4,4,null,null]
```

