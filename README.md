go-astar
========

**A\* pathfinding implementation for Go**

[![Build Status](https://travis-ci.org/beefsack/go-astar.svg?branch=master)](https://travis-ci.org/beefsack/go-astar)

The [A\* pathfinding algorithm](http://en.wikipedia.org/wiki/A*_search_algorithm) is a pathfinding algorithm noted for its performance and accuracy and is commonly used in game development.  It can be used to find short paths for any weighted graph.

A fantastic overview of A\* can be found at [Amit Patel's Stanford website](http://theory.stanford.edu/~amitp/GameProgramming/AStarComparison.html).

Examples
--------

The following crude examples were taken directly from the automated tests.  Please see `path_test.go` for more examples.

### Key

*   `.` - Plain (movement Cost 1)
*   `~` - River (movement Cost 2)
*   `M` - Mountain (movement Cost 3)
*   `X` - Blocker, unable to move through
*   `F` - From / start position
*   `T` - To / goal position
*   `●` - Calculated path

### Straight line

```
.....~......      .....~......
.....MM.....      .....MM.....
.F........T.  ->  .●●●●●●●●●●.
....MMM.....      ....MMM.....
............      ............
```

### Around a mountain

```
.....~......      .....~......
.....MM.....      .....MM.....
.F..MMMM..T.  ->  .●●●MMMM●●●.
....MMM.....      ...●MMM●●...
............      ...●●●●●....
```

### Blocked path

```
............      
.........XXX
.F.......XTX  ->  No path
.........XXX
............
```

### Maze

```
FX.X........      ●X.X●●●●●●..
.X...XXXX.X.      ●X●●●XXXX●X.
.X.X.X....X.  ->  ●X●X.X●●●●X.
...X.X.XXXXX      ●●●X.X●XXXXX
.XX..X.....T      .XX..X●●●●●●
```

### Mountain climber

```
..F..M......      ..●●●●●●●●●.
.....MM.....      .....MM...●.
....MMMM..T.  ->  ....MMMM..●.
....MMM.....      ....MMM.....
............      ............
```

### River swimmer

```
.....~......      .....~......
.....~......      ....●●●.....
.F...X...T..  ->  .●●●●X●●●●..
.....M......      .....M......
.....M......      .....M......
```

Usage
-----

### Import the package

```go
import "github.com/beefsack/go-astar"
```

### Implement Pather interface

An example implementation is done for the tests in `path_test.go` for the Tile type.

The `PathNeighbours` method should return a slice of the direct neighbours.

The `PathNeighbourCost` method should calculate an exact movement Cost for direct neighbours.

The `PathEstimatedCost` is a heuristic method for estimating the distance between arbitrary tiles.  The examples in the test files use [Manhattan distance](http://en.wikipedia.org/wiki/Taxicab_geometry) to estimate orthogonal distance between tiles.

```go
type Tile struct{}

func (t *Tile) PathNeighbours() []astar.Pather {
	return []astar.Pather{
		t.Up(),
		t.Right(),
		t.Down(),
		t.Left(),
	}
}

func (t *Tile) PathNeighbourCost(to astar.Pather) float64 {
	return to.MovementCost
}

func (t *Tile) PathEstimatedCost(to astar.Pather) float64 {
	return t.ManhattanDistance(to)
}
```

### Call Path function

```go
// t1 and t2 are *Tile objects from inside the world.
path, distance, found := astar.Path(t1, t2)
if !found {
	log.Println("Could not find path")
}
// path is a slice of Pather objects which you can cast back to *Tile.
```

Authors
-------

Michael Alexander <beefsack@gmail.com>
Robin Ranjit Chauhan <robin@pathwayi.com>
