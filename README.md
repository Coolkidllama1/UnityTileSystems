# UnityTileSystems
i made a few tilesystems and i am offering them up for free. none of them come with stuff to spawn on it, but i will provide the functions to spawn something onto it. 

Unity version used: 6.000.3.0f1

Dependencies: 

the project must be a URP (Universal Render Pipeline) (started it as a 3d URP and added the 2D package)

Must have new input system enabled

Must have TextMeshPro (TMP) (the package files should have them)

# First 2D Version:
This is the initial one i thought of before i made improvements to the design for my needs. I made this version again in case this will suit your needs. I am excluding the mistakes i made that made debugging impossible (like not adding a RowManager on each row)

4 rows, 6 columns. each row has 6 tiles. the lines you see are simply the space in-between the tiles.

<details>
<summary>Hierarchy</summary>

```
(each letter (A, B, etc) has the same numbers under it. ill only show for A for less repetitiveness)
Main Camera
Canvas
    TileHolder
        A
            1 (image)
            2 (image)
            3 (image)
            4 (image)
            5 (image)
            6 (image)
        B
        C
        D
```

</details>

ill list the things in the scripts in the order i make them in case you want a step-by-step.
<details>
<summary>Click To Expand</summary>
make 3 MonoBehavior scripts: TileManager and RowManager and Tile
make 1 empty C# script: ClassHolder
open the IDE (Visual Studio 2022 is what i use)

in ClassHolder put this anywhere below or above the ClassHolder class (dont delete the class):
```C#
[System.Serializable]
public enum Nums
{
    One, Two, Three, Four, Five, Six
}
[System.Serializable]
public enum Letters
{
    A, B, C, D
}
[System.Serializable]
public struct TileID
{
    public Letters letter;
    public Nums num;
    public TileID(Nums n, Letters l)
    {
        letter = l;
        num = n;
    }
    public TileID(Letters l, Nums n)
    {
        letter = l;
        num = n;
    }
}
```
one of the constructors will help later. struct > class when your not dealing with anything like GameObject thats Unity Specific. This is optional, but helps with memory. To anyone who uses c++, no i DONT mean pointers. C# doesnt allow you to use pointers due to automatic memory management.

I put [System.Serializable] to make it appear in the inspector. its required on all 3 of those for it to be in the inspector.

In Tile make a TileID variable.
```C#
public TileID id;
```

In RowManager make 6 Tile variables:
```C#
public Tile tileOne;
public Tile tileTwo;
public Tile tileThree;
public Tile tileFour;
public Tile tileFive;
public Tile tileSix;
```

In TileManager add 4 RowManager variables:
```C#
public RowManager a;
public RowManager b;
public RowManager c;
public RowManager d;
```

now lets add the logic.

In RowManager lets add the function to get the tile bu its NUM in its ID:
you can do either one of these ways of doing it. the second way is a modern way to do it i discovered recently when i was first making the second version of my tile system.
```C#
public Tile GetTileByNum(Nums num)
{
    switch (num)
    {
        case Nums.One:
            return tileOne;
        case Nums.Two:
            return tileTwo;
        case Nums.Three:
            return tileThree;
        case Nums.Four:
            return tileFour;
        case Nums.Five:
            return tileFive;
        case Nums.Six:
            return tileSix;
        default:
            return null;
    }
}

public Tile GetTileByNum(Nums num)
{
    return num switch
    {
        Nums.One => tileOne,
        Nums.Two => tileTwo,
        Nums.Three => tileThree,
        Nums.Four => tileFour,
        Nums.Five => tileFive,
        Nums.Six => tileSix,
        _ => null
    };
}
```
next lets add 
```C#
using System.Collections.Generic;
```
to the top.

now lets add:
```C#
public List<Tile> GetAllTiles()
{
    return new List<Tile> { tileOne, tileTwo, tileThree, tileFour, tileFive, tileSix };
}
```
List<> requires System.Collections.Generic to be able to use it.

if you dont understand that function heres a different way to do the same thing:
```C#
    public List<Tile> GetAllTiles()
    {
        List<Tile> tiles = new List<Tile>();
        tiles.Add(tileOne);
        tiles.Add(tileTwo);
        tiles.Add(tileThree);
        tiles.Add(tileFour);
        tiles.Add(tileFive);
        tiles.Add(tileSix);
        return tiles;
    }
```





In TileManager we do something else first then basically the same thing.
First add:
```C#
using System.Collections.Generic;
```
to the top.
then make a GetAll() for the Rows GetAll():
```C#
public List<Tile> GetAllTiles()
{
    List<Tile> tiles = new List<Tile>();
    tiles.AddRange(a.GetAllTiles());
    tiles.AddRange(b.GetAllTiles());
    tiles.AddRange(c.GetAllTiles());
    tiles.AddRange(d.GetAllTiles());
    return tiles;
}
```
AddRange() adds another list to a list. 

now lets add this:
```C#
    public Tile GetTileByID(TileID id)
    {
        RowManager row = null;
        switch (id.letter)
        {
            case Letters.A:
                row = a;
                break;
            case Letters.B:
                row = b;
                break;
            case Letters.C:
                row = c;
                break;
            case Letters.D:
                row = d;
                break;
        }
        if (row != null)
        {
            return row.GetTileByNum(id.num);
        }
        return null;
    }
```
its likely the IDE probably did the tab suggestion for you with this one if you have that feature enabled. It basically added a null check when i wasnt going to because there is no need.

heres a bonus optional function if you want to grab a column:
```C#
    public List<Tile> GetTilesByNum(Nums num)
    {
        List<Tile> tiles = new List<Tile>();
        tiles.Add(a.GetTileByNum(num));
        tiles.Add(b.GetTileByNum(num));
        tiles.Add(c.GetTileByNum(num));
        tiles.Add(d.GetTileByNum(num));
        return tiles;
    }
```


now back to the editor.
lets add the tile script to each tile. to make it easy, ill explain some cool keybinds. 
open up all of the rows.
click D6
hold shift
click D1
let go of shift
hold ctrl
click C6
let go of ctrl
hold shift
click C1

you know what to do from there.
after selecting them all, click "Add Component" and search for Tile.
after that, unselect everything. click D6 and use shift to select all of the tiles under D. 
set the Letter of the ID to D. You should see where im going with this. 

to set the numbers, use ctrl to select all the 2s and change their Nums value at the same time.
nums is One by default because its the first value in the enum. theres no need to ctrl select and edit the 1s.
<details>
<summary>Want to add something to TileID that will make it printable to the console easily?</summary>

instead of
```C#
Debug.Log(id.letter.ToString() + id.num.ToString())
```
which returns "DTwo"
instead we could call it like
```C#
Debug.Log(id);
```
which returns "D2"
no need for ToString() even because its automatically called anyway in a log statement.
wanna do it? here it is.
add this to the TileID declaration in ClassHolder:
```C#
private string GetNumString()
{
    return num switch
    {
        Nums.One => "1",
        Nums.Two => "2",
        Nums.Three => "3",
        Nums.Four => "4",
        Nums.Five => "5",
        Nums.Six => "6",
        _ => ""
    };
}
```
then we can do this:
```C#
public override string ToString()
{
    return letter.ToString() + GetNumString();
}
```
we are doing a ToString() here because this isnt a log statement. only log statements automatically call ToString().
</details>
now that we got the Tile set up with its ID, lets add RowManager to the rows.
assign all the tiles to it manually. 

now lets add the TileManager to the TileHolder (the parent of the rows):
assign the rows. now back to the IDE.

heres the final required piece.
in TileManager:
```C#
    public GameObject SpawnAPrefab(GameObject prefab, TileID whichTile)
    {
        GameObject obj = Instantiate(prefab);
        obj.transform.SetParent(GetTileByID(whichTile).transform);
        obj.transform.localPosition = Vector3.zero; //sets to center of tile (0, 0, 0)
        obj.transform.localScale = new Vector3(0.5f, 0.7f, 0); // or whatever scale you found to be good for your prefab before you made it a prefab
        return obj;
    }
```
returning as GameObject is optional but helps to debug.

<details>
<summary>Wanna add a function to spawn on a random tile?</summary>

first add this to TileManager:
```C#
public Tile GetRandomTile()
{
    List<Tile> allTiles = GetAllTiles();
    int randomIndex = Random.Range(0, allTiles.Count);
    return allTiles[randomIndex];
}
```

then we can easily add:
```C#
public GameObject SpawnAPrefab(GameObject prefab)
{
    Tile randomTile = GetRandomTile();
    GameObject obj = Instantiate(prefab);
    obj.transform.SetParent(randomTile.transform);
    obj.transform.localPosition = Vector3.zero; //sets to center of tile (0, 0, 0)
    obj.transform.localScale = new Vector3(0.5f, 0.7f, 0); // or whatever scale you found to be good for your prefab before you made it a prefab
    return obj;
}
```

see how this function has the same name? thats possible because it doesnt have the same parameter(s) as the other spawn a prefab. All you gotta do to spawn on a random tile is to not provide the function with an ID.
</details>

now to add a thing to spawn in the Editor rq to show you how this works.
right click on any tile
Create/UI/Image
set the Images source image to Knob. you can edit the color and scale and name. 
after editing make sure to set the position in the Rect Transform to (0, 0, 0).
drag it into a folder in the Project window to make it a prefab.

Lets make a new MonoBehavior script called SpawnManager. 
first we will add a TileManager variable before anything else.
```C#
public TileManager tm;
```
add this right under:
```C#
public GameObject spawnPrefab;
```
now:
```C#
void Start()
{
    tm.SpawnAPrefab(spawnPrefab, new TileID(Letters.C, Nums.Three));
    tm.SpawnAPrefab(spawnPrefab);
}
```

lets see if you were paying attention

<details>
<summary>What is the bug in my code that only sometimes shows by chance?</summary>

I didnt add a thing to detect if the tile was occupied already! this is something i never fixed with v1 but ill make a fix rn.

Lets add this to Tile:
```C#
[HideInInspector]
public bool isOccupied = false;
```

Add this to TileManager:
```C#
public bool IsTileOccupied(TileID id)
{
    Tile tile = GetTileByID(id);
    if (tile != null)
    {
        return tile.GetComponent<Tile>().isOccupied;
    }
    return false;
}
```
again, no need for a null check but its whatever.

now in SpawnManager lets make our own spawn functions to not clutter the tile manager.
```C#
public GameObject Spawn(GameObject o, TileID whichOne)
{
    if (tm.IsTileOccupied(whichOne))
    {
        Debug.LogWarning("Tile " + whichOne.ToString() + " is already occupied!");
        return null;
    } else
    {
        return tm.SpawnAPrefab(o, whichOne);
    }
}
public GameObject Spawn(GameObject o)
{
    bool occupied = true;
    GameObject spawnedObject = null;
    while (occupied)
    {
        Tile randomTile = tm.GetRandomTile();
        if (!randomTile.isOccupied)
        {
            occupied = false;
            spawnedObject =  tm.SpawnAPrefab(o, randomTile.id);
        }
    }
    return spawnedObject;
}
```
putting the occupied check in the SpawnManager will make it easier to debug later because not EVERYTHING is in the same script. 

Almost forgot. lets add to these a line or two to set the occupied to true.
before we update the new functions, lets add a SetOccupied function to TileManager.
```C#
public void SetTileOccupied(TileID id, bool occupied)
{
    Tile tile = GetTileByID(id);
    if (tile != null)
    {
        tile.GetComponent<Tile>().isOccupied = occupied;
    }
}
```

now lets update our functions in SpawnManager:
```C#
public GameObject Spawn(GameObject o, TileID whichOne)
{
    if (tm.IsTileOccupied(whichOne))
    {
        Debug.LogWarning("Tile " + whichOne.ToString() + " is already occupied!");
        return null;
    } else
    {
        GameObject spawnedObject = tm.SpawnAPrefab(o, whichOne);
        tm.SetTileOccupied(whichOne, true);
        return spawnedObject;
    }
}
public GameObject Spawn(GameObject o)
{
    bool occupied = true;
    GameObject spawnedObject = null;
    while (occupied)
    {
        Tile randomTile = tm.GetRandomTile();
        if (!randomTile.isOccupied)
        {
            occupied = false;
            spawnedObject =  tm.SpawnAPrefab(o, randomTile.id);
            tm.SetTileOccupied(randomTile.id, true);
        }
    }
    
    return spawnedObject;
}
```

update the Start():
```C#
void Start()
{
    Spawn(spawnPrefab, new TileID(Letters.C, Nums.Three));
    Spawn(spawnPrefab);
    Spawn(spawnPrefab, new TileID(Letters.C, Nums.Three)); // This should log a warning
}
```

</details>

now attach the Spawn Manager to EventSystem (or whatever you choose to put it on) and assign the prefab from the Project folder and the TileHolder because it has the TileManager script attached.

press play and watch the magic happen.

</details>

what you need to know:
i have an enum for the Letter
i have an enum for the Number
TileID is basically a class i made containing both of the enums. (letter first)
i would truly recommend seeing the madly long tutorial i spent hours typing up as i made this.

the SpawnManager has a great example of how to use it and how to not use the first function in start().
I have a custom scale set for the one prefab i made for this to make sure the scale is still correct. it sets the scale to something else when you spawn it. hold ctrl and click on SpawnAPrefab. you should do it for both versions. comment out my scale adjustment to see what it does without it and then go see the prefab and look at what the scale is. out of playtime i created an image as a child of a tile and i set its source image to something unity had called "knob". i then tried setting the scale to (0.5, 0.5, 0) but it wasnt a perfect circle. i changed it to (0.5, 0.7, 0) and it was great.

the spawn functions return a GameObject to help you debug what happens to the spawned object (how else will you grab it? naming a certain way in the spawn function and finding the object by name? too much work)

# Second 2D Version:
I have one big black image and white lines to make it look like seperate tiles.
the system is pretty much the same. 
the Tiles (empties with the Tile script attached to them) arent also images (because there is only one black image)
<details>
<summary>Heirarchy</summary>

```
Main Camera
    Canvas
        BackgroundImage
            Verticals
                vertical lines
            Horizontals
                Horizontal lines
        TileHolder
            A
                1
                2
                3
                4
                5
                6
            B (same for the rest)
            C
            D
    EventSystem
```

Spacing of vertical lines: 15
Spacing of horizontal lines: 25 (kinda)

</details>

# 3D Version

literally the same system as version 2.
although im able to use simple exact numbers for this one to make the positions look perfect.

for my 3d version i also have a 4x4 version of it
both of them also download a folder of materials i put together too.

the package for the 6x4 version has a bonus script because i had a funny idea with IENumerator.

<details>
<summary>4x4 version info</summary>

for a row the positions are:
```C#
1 - (-4.5, 0, 0)
2 - (-1.5, 0, 0)
3 - (1.5, 0, 0)
4 - (4.5, 0, 0)

THEY ARE THE CHILD OF AN EMPTY WITH THE ROW NAME
```

the positions of the rows:
```C#
A - (0, 0.5, 3)
B - (0, 0.5, 1)
C - (0, 0.5, -1)
D - (0, 0.5, -3)
```

</details>

<details>
<summary>6x4 version info</summary>

for a row the positions are:
```C#
1 - (-7.5, 0, 0)
2 - (-4.5, 0, 0)
3 - (-1.5, 0, 0)
4 - (1.5, 0, 0)
5 - (4.5, 0, 0)
6 - (7.5, 0, 0)

THEY ARE THE CHILD OF AN EMPTY WITH THE ROW NAME
```

the positions of the rows:
```C#
A - (0, 0.5, 3)
B - (0, 0.5, 1)
C - (0, 0.5, -1)
D - (0, 0.5, -3)
```

</details>