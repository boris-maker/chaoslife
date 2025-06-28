<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>life.signal.sys</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      background: black;
      overflow: hidden;
    }
    canvas {
      display: block;
      image-rendering: pixelated;
      filter: contrast(300%) brightness(120%);
    }
  </style>
</head>
<body>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.min.js"></script>
  <script>
    let cols = 160;
    let rows = 90;
    let cellSize = 8;
    let grid;

    function setup() {
      createCanvas(cols * cellSize, rows * cellSize);
      noSmooth();
      grid = make2DArray(cols, rows);
      initGrid(grid);
      frameRate(20); // slower = clearer structure
    }

    function draw() {
      background(0);

      // draw grid
      for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
          if (grid[x][y] === 1) {
            fill(255);
            rect(x * cellSize, y * cellSize, cellSize, cellSize);
          }
        }
      }

      grid = computeNextGeneration(grid);
    }

    function make2DArray(cols, rows) {
      let arr = new Array(cols);
      for (let i = 0; i < cols; i++) {
        arr[i] = new Array(rows).fill(0);
      }
      return arr;
    }

    function initGrid(g) {
      for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
          g[x][y] = random() < 0.2 ? 1 : 0; // sparse seed
        }
      }
    }

    function computeNextGeneration(g) {
      let next = make2DArray(cols, rows);
      for (let x = 0; x < cols; x++) {
        for (let y = 0; y < rows; y++) {
          let neighbors = countAliveNeighbors(g, x, y);
          let current = g[x][y];

          if (current === 1 && (neighbors < 2 || neighbors > 3)) {
            next[x][y] = 0; // death
          } else if (current === 0 && neighbors === 3) {
            next[x][y] = 1; // birth
          } else {
            next[x][y] = current; // survival
          }
        }
      }
      return next;
    }

    function countAliveNeighbors(g, x, y) {
      let sum = 0;
      for (let dx = -1; dx <= 1; dx++) {
        for (let dy = -1; dy <= 1; dy++) {
          if (dx === 0 && dy === 0) continue;
          let nx = (x + dx + cols) % cols;
          let ny = (y + dy + rows) % rows;
          sum += g[nx][ny];
        }
      }
      return sum;
    }

    function windowResized() {
      resizeCanvas(cols * cellSize, rows * cellSize);
    }
  </script>
</body>
</html>

