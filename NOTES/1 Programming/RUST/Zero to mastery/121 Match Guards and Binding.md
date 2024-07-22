Time Created: 22 July 2024 17:39

Tags: #rust/matchguardsandbinding #rust/match #rust/binding

Using Match guard and binding allow you for further filter your match expression to get the data you want within your match expressions.

keyword "@" is binding 
```rust
#[derive(Debug)]
enum Status {
    Error(i32),
    Info,
    Warn,
}

fn main() {
    let status = Status::Error(25);

    match status {
        Status::Error(s @ 3) => println!("error three"),
        Status::Error(s @ 5..=6) => println!("error 5 or 6: {}", s),
        Status::Error(s @ 4..=10) => println!("error three through 10: {}", s),
        Status::Error(s @ 18 | s @ 19) => println!("error 18 or 19"),
        Status::Error(s) => println!("error code: {}", s),
        Status::Info => println!("info"),
        Status::Warn => println!("warn"),
    }
}
```

matching on struct
```rust
#[derive(Debug)]
enum Species {
    Finch,
    Hawk,
    Parrot,
}

struct Bird {
    age: usize,
    species: Species,
}

fn main() {
    let hawk = Bird {
        age: 30,
        species: Species::Parrot,
    };

    match hawk {
        Bird { age: 4, .. } => println!("4 years old bird"),
        Bird {
            age: 4..=10 | 15..=20,
            ..
        } => println!("4-10 or 15-20 year old"),
        //Bird { .., species: Species::Finch } => println!("finch"),
        Bird {
            species: Species::Finch,
            ..
        } => println!("finch"),
        Bird { .. } => println!("Other Bird"),
    }
}
```

>[!note] Note!
> Match on struct will print the first one who will match other key and value will already be bypass

matching on Enum with one variable
```rust
#[derive(PartialEq)]
enum Difficulty {
    Easy,
    Normal,
    Hard,
}

fn main() {
    let stage = 20;
    let diff = Difficulty::Hard;

    match stage {
        s if (s == 5 && diff == Difficulty::Easy) => println!("Easy mode stage 5"),
        s if diff == Difficulty::Normal => println!("normal difficulty stage {}", s),
        s @ 10 | s @ 15 => println!("stage 10 or 15"),
        s => println!("stage {}", stage),
    }
}
```

activity
```rust
// Topic: Match guards & binding
//
// Summary:
// * A tile-based game requires different logic for different kinds
//   of tiles. Print different messages depending on the kind of
//   tile selected.
//
// Requirements:
// * Bricks:
//   * Colored bricks should print "The brick color is [color]"
//   * Other bricks should print "[Bricktype] brick"
// * Water:
//   * Pressure levels 10 and over should print "High water pressure!"
//   * Pressure levels under 10 should print "Water pressure level: [Pressure]"
// * Grass, Dirt, and Sand should all print "Ground tile"
// * Treasure Chests:
//   * If the treasure is Gold and the amount is at least 100, print "Lots of gold!"
// * Everything else shoud not print any messages
//
// Notes:
// * Use a single match expression utilizing guards to implement the program
// * Run the program and print the messages with at least 4 different tiles

#[derive(Debug)]
enum TreasureItem {
    Gold,
    SuperPower,
}

#[derive(Debug)]
struct TreasureChest {
    content: TreasureItem,
    amount: usize,
}

#[derive(Debug)]
struct Pressure(u16);

#[derive(Debug)]
enum BrickStyle {
    Dungeon,
    Gray,
    Red,
}

#[derive(Debug)]
enum Tile {
    Brick(BrickStyle),
    Dirt,
    Grass,
    Sand,
    Treasure(TreasureChest),
    Water(Pressure),
    Wood,
}

fn print_tile(tile: Tile) {
    use Tile::*;
    match tile {
        Brick(brick @ BrickStyle::Gray | brick @ BrickStyle::Red) => {
            println!("The brick color is {:?}", brick)
        }
        Brick(other) => println!("{:?} brick", other),
        Dirt | Grass | Sand => println!("Dirt, Grass and Sand"),
        Treasure(TreasureChest { amount, .. }) if amount >= 100 => println!("Lots of gold"),
        Water(pressure) if pressure.0 < 10 => println!("Water pressure level is {:?}", pressure.0),
        Water(pressure) if pressure.0 >= 10 => println!("High water pressure"),
        _ => (),
    }
}

fn main() {
    let tile = Tile::Brick(BrickStyle::Red);
    print_tile(tile);

    let tile = Tile::Sand;
    print_tile(tile);

    let tile = Tile::Treasure(TreasureChest {
        content: TreasureItem::Gold,
        amount: 200,
    });
    print_tile(tile);

    let tile = Tile::Water(Pressure(9));
    print_tile(tile);
}
```