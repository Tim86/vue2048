# Game logic

## Rules
> There are more off course, but theses are the one I know there needs to be some more work to be done one currently. 

A tile can only merge once per turn. 

## Moves and merges

On a player move. Do the following. 

On move ***up** (e.g.)*
1. Start from the ***top***.
2. Don't touch the first cells tile. Store if it is empty or not.
3. Move to next tile.
4. Find incoming tile. That may stop before this, merge into or take its place.
5. Check incoming tiles value and compare to current. If they can merge, do so. 
6. If privous cell was empty, move current tile there.
7. Go back to step *3. Move next tile*

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

