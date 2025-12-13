# UnityTileSystems
i made a few tilesystems and i am offering them up for free. none of them come with stuff to spawn on it, but i will provide the functions to spawn something onto it. 

First 2D Version:
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
}
```
that constructor will help later. struct > class when your not dealing with anything like GameObject thats Unity Specific. This is optional, but helps with memory. To anyone who uses c++, no i DONT mean pointers. C# doesnt allow you to use pointers due to automatic memory management.

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
        obj.transform.parent = GetTileByID(whichTile).transform;
        obj.transform.localPosition = Vector3.zero; //sets to center of tile (0, 0, 0)
        return obj;
    }
```
returning as GameObject is optional but helps to debug.

<details>
<summary>Wanna add a function to spawn on a random tile?<summary>
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
    obj.transform.parent = randomTile.transform;
    obj.transform.localPosition = Vector3.zero; //sets to center of tile (0, 0, 0)
    return obj;
}
```

see how this function has the same name? thats possible because it doesnt have the same parameter(s) as the other spawn a prefab. All you gotta do to spawn on a random tile is to not provide the function with an ID.
</details>
now to add 
</details>