# vON

vON is a [serialization](http://s.vercas.com/definitionserialization) library for Lua, turning tables into strings and the other way around.

The purpose of vON is to facilitate persistent storage of Lua data as strings (in a file, in a database, etc.).

Initially, I made it as an alternative to GLON ( **G**arry’s Mod **L**ua **O**bject **N**otation ), which was slow, the code was terrible, it used unprintable characters (which aren’t ideal for absolutely everything), and it was eventually removed from the game.

The main target of vON is flexibility and speed.

## License

The license is specified in each code file. The terms of usage are pretty simple. Just read them.

## Specifications

As in Lua, the main data structure is the table. But, unlike Lua tables, they are visibly separated in two parts: the numeric (array) component and the key-value pairs (dictionary) component.
Tables start with *{* and end with *}*, and the two components are separated by a *~* (tilda) character, which may be absent if the table is a pure array.
**Examples**: Format: **{ … ~ … }** and data: **{“lolv”“maov”~“lolv”:”maov”}**, which would be **{“lol”,”mao”,lol=”mao”}** in Lua.

It must be mentioned that keys have no type restrictions. They can be booleans and even tables (or a Garry’s Mod-specific type).

The first table in the data, the chunk, has no initial and final characters (they are useless). This allows concatenating some vON code together and keeping it valid; sort of like using table.insert.
**Example**: **“lolv”"maov”~”lolv”:”maov”**, which means the same thing as the example above enclosed in *{ }*.

Also, for the sake of parsing speed, some types (such as boolean and numbers) are prefixed with a character. If no valid prefix character is found, the last type will be automatically used.
Inside tables, spaces, tabs and newlines are simply ignored when deserializing.

Numbers are currently declared like this: **n…** (… represents the value in base 10). They either end in *;*, *}*, *\n* (newline), *:* or *~*. (At least one must be present).
**Example**: **n1;2;3~4:4;** and **{n1;2;”intruder!v”n4}**.

Booleans are prefixed by **b** and are represented either by a **1** *(true)* or **0** *(false)*. They are represented by a single character so they don’t have a delimiter.
Boolean sequences usually look like this: **b101101001**.

Strings start and being with double quotes (*“*). Quotes inside strings are escaped with a \. Only quotes are escaped now.
A **v** is added at the end of every string to make sure the string doesn’t end in a \ (which would break the deserializer).
That’s *1* more character per string, but it whould be a fair sacrifice considering the speeds at which it deserializes strings. (especially strings of kilobytes in size!)

## Example

```lua
{
    1, -1337, -99.99, 2, 3, 100, 101, 121, 143, 144, "ma\"ra", "are", "mere",
    {
        500,600,700,800,900,9001,
        TROLOLOLOLOLOOOO = 666,
        [true] = false,
        [false] = "lol?",
        pere = true,
        [1997] = "vasile",
        [{ [true] = false, [false] = true }] = { [true] = "true", ["false"] = false }
    },
    true, false, false, true, false, true, true, false, true,
    [1337] = 1338,
    mara = "are",
    mere = false,
    [true] = false,
    [{ [true] = false, [false] = true }] = { [true] = "true", ["false"] = false }
}
```

… becomes …

    n1;-1337;-99.99;2;3;100;101;121;143;144;"ma\"rav""arev""merev"{n500;600;700;800;900;9001~"TROLOLOLOLOLOOOOv":n666;b1:0{~b0:11:0}:{~b1:"truev""falsev":b0}"perev":b1n1997:"vasilev"b0:"lol?v"}b100101101~1:0{~b0:11:0}:{~b1:"truev""falsev":b0}n1337:1338;"marav":"arev""merev":b0

That’s pretty.
