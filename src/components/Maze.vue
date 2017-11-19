<template>
  <div id="maze">
    <h1 class="text-primary">Hello</h1>
    <canvas class="canvas" id="mazeboard" height="500px" width="500px" style="border:2px solid #f0f0f0">Maze</canvas>
  </div>
</template>

<script>
export default {
  name: 'Maze',
  mounted () {
    var c = document.querySelector('canvas')
    var cxt = c.getContext('2d')
    cxt.strokeStyle = '#42b983'
    cxt.translate(0.5, 0.5)
    var l = 20
    var size = 10
    var maze = genMaze(size)
    console.log(maze)
    drawMaze(maze, l)
    cxt.stroke()

    function initMaze (size) {
      let retMaze = []
      for (let i = 0; i !== size; ++i) {
        retMaze.push([])
        for (let j = 0; j !== size; ++j) {
          retMaze[i].push({parent: null, children: []})
        }
      }
      return retMaze
    }

    function genMaze (size) {
      let maze = initMaze(size)
      let root = {x: 0, y: 0}
      let path = []
      path.push(root)
      let stack = []
      stack.push(path)
      let visited = new Set()
      visited.add(root)
      while (stack.length !== 0) {
        path = stack.pop()
        let currNode = path[path.length - 1]
        let nei = neighbor(currNode.x, currNode.y, size)
        for (let i of nei) {
          let flag = false
          for (let j of visited) {
            if (j.x === i.x && j.y === i.y) flag = true
          }
          if (!flag) {
            maze[currNode.x][currNode.y].children.push(i)
            maze[i.x][i.y].parent = currNode
          }
        }
        let adjcnt = maze[currNode.x][currNode.y].children
        while (adjcnt.length !== 0) {
          let idx = Math.round(Math.random() * Math.exp(5)) % adjcnt.length
          // alert(idx)
          let copy = path
          copy.push(adjcnt[idx])
          console.log(adjcnt[idx])
          stack.push(copy)
          visited.add(adjcnt[idx])
          adjcnt.splice(idx, 1)
        }
      }
      return maze
    }

    function drawMaze (maze, l) {
      // var c = document.querySelector('canvas')
      // var cxt = c.getContext('2d')
      for (let i = 0; i !== maze.length; i++) {
        for (let j = 0; j !== maze[i].length; j++) {
          let cell = genWall(maze, i, j)
          for (let wall of cell) {
            switch (wall) {
              case -2:
                cxt.moveTo(l * i, l * j)
                cxt.lineTo(l * i, l * (j + 1))
                console.log('draw top border', '(', l * i, l * j, ')', '(', l * i, l * (j + 1), ')')
                cxt.stroke()
                break
              case -1:
                cxt.moveTo(l * i, l * j)
                cxt.lineTo(l * (i + 1), l * j)
                console.log('draw left border', '(', l * i, l * j, ')', '(', l * (i + 1), l * j, ')')
                cxt.stroke()
                break
              case 1:
                cxt.moveTo(l * (i + 1), l * (j + 1))
                cxt.lineTo(l * i, l * (j + 1))
                console.log('draw right border', '(', l * (i + 1), l * (j + 1), ')', '(', l * i, l * (j + 1), ')')
                cxt.stroke()
                break
              case 2:
                cxt.moveTo(l * (i + 1), l * (j + 1))
                cxt.lineTo(l * (i + 1), l * j)
                console.log('draw bottom border', '(', l * (i + 1), l * (j + 1), ')', '(', l * (i + 1), l * j, ')')
                cxt.stroke()
            }
          }
        }
      }
    }

    function genWall (maze, i, j) {
      let walls = new Set()
      walls.add(-2, -1, 1, 2)
      if (maze[i][j].parent !== null) {
        let prt = maze[i][j].parent
        let diff = prt.x - i + 2 * (prt.y - j)
        walls.delete(diff)
      }
      for (let k = 0; k !== maze[i][j].children.length; ++i) {
        let kid = maze[i][j].children[k]
        let diff = kid.x - i + 2 * (kid.y - j)
        walls.delete(diff)
      }
      console.log(walls)
      alert('wall')
      return walls
    }

    function neighbor (x, y, size) {
      let neighborhood = []
      if (x === 0) {
        neighborhood.push({x: x + 1, y: y})
        if (y !== size - 1) {
          neighborhood.push({x: x, y: y + 1})
        }
        if (y !== 0) {
          neighborhood.push({x: x, y: y - 1})
        }
      } else if (x === size - 1) {
        neighborhood.push({x: x - 1, y: y})
        if (y !== size - 1) {
          neighborhood.push({x: x, y: y + 1})
        }
        if (y !== 0) {
          neighborhood.push({x: x, y: y - 1})
        }
      } else {
        neighborhood.push({x: x - 1, y: y})
        neighborhood.push({x: x + 1, y: y})
        if (y !== size - 1) {
          neighborhood.push({x: x, y: y + 1})
        }
        if (y !== 0) {
          neighborhood.push({x: x, y: y - 1})
        }
      }
      return neighborhood
    }
  }
}
</script>

<style>
#canvas {
  border: 2px, solid, #42b983;
}
</style>
