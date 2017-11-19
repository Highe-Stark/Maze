<template>
  <div id="maze">
    <h1 class="text-primary">{{msg}}</h1>
    <input id="mazeSize" class="form-control-plaintext" type="number" value=2>mazeSize</input>
    <!--<button type="submit" v-on:click="update" class="btn btn-primary">{{draw}}</button>-->
    <button class="btn btn-primary" v-on:click="reload">{{btn}}</button>
    </br>
    <canvas class="canvas" id="mazeboard" height="500px" width="500px" style="border:2px solid #42b983">Maze</canvas>
    <p>
      <span id="routes"></span>
    </p>
  </div>
</template>

<script>
export default {
  name: 'Maze',
  created () {
    this.msg = 'Welcome to play maze'
    this.btn = 'Refresh'
    this.route = 'Empty'
    this.draw = 'Draw Maze'
  },
  mounted () {
    var c = document.querySelector('canvas')
    var cxt = c.getContext('2d')
    cxt.strokeStyle = '#42b983'
    cxt.translate(0.5, 0.5)
    var l = 20
    var size = 20
    var maze = genMaze(size)

    /* var routeStr = ''
    for (let i = 0; i !== maze.length; ++i) {
      for (let j = 0; j !== maze[i].length; ++j) {
        routeStr += JSON.stringify(maze[i][j].parent) + ' --> '
        routeStr += '( ' + i + ', ' + j + ' )->'
        for (let k of maze[i][j].children) {
          routeStr += JSON.stringify(k) + '&nbsp &nbsp'
        }
        routeStr += '  </br>'
      }
    }
    document.getElementById('routes').innerHTML = routeStr */

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
            maze[i.x][i.y].parent = Object.assign({}, currNode)
          }
        }
        let adjcnt = Object.assign([], maze[currNode.x][currNode.y].children)
        while (adjcnt.length !== 0) {
          let idx = Math.round(Math.random() * Math.exp(5)) % adjcnt.length
          let copy = path
          copy.push(adjcnt[idx])
          stack.push(copy)
          visited.add(adjcnt[idx])
          adjcnt.splice(idx, 1)
        }
      }
      return maze
    }

    function drawMaze (maze, l) {
      for (let i = 0; i !== maze.length; i++) {
        for (let j = 0; j !== maze[i].length; j++) {
          let cell = genWall(maze, i, j)
          for (let wall of cell) {
            switch (wall) {
              case -2:
                cxt.moveTo(l * j, l * i)
                cxt.lineTo(l * (j + 1), l * i)
                cxt.stroke()
                break
              case -1:
                cxt.moveTo(l * j, l * i)
                cxt.lineTo(l * j, l * (i + 1))
                cxt.stroke()
                break
              case 2:
                cxt.moveTo(l * (j + 1), l * (i + 1))
                cxt.lineTo(l * j, l * (i + 1))
                cxt.stroke()
                break
              case 1:
                cxt.moveTo(l * (j + 1), l * (i + 1))
                cxt.lineTo(l * (j + 1), l * i)
                cxt.stroke()
            }
          }
        }
      }
    }

    function genWall (maze, i, j) {
      let walls = new Set()
      walls.add(-2)
      walls.add(-1)
      walls.add(1)
      walls.add(2)
      if (maze[i][j].parent !== null) {
        let prt = maze[i][j].parent
        let diff = 2 * (prt.x - i) + prt.y - j
        walls.delete(diff)
      } else {
        walls.delete(-2)
      }
      if (i === maze.length - 1 && j === maze.length - 1) {
        walls.delete(1)
      }
      for (let k = 0; k !== maze[i][j].children.length; ++k) {
        let kid = maze[i][j].children[k]
        let diff = 2 * (kid.x - i) + (kid.y - j)
        walls.delete(diff)
      }
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
  },
  methods: {
    reload: function () {
      window.location.reload()
    }
  }
}
</script>

<style>
#canvas {
  border: 2px, solid, #42b983;
}
#h1 {
  color:#42b983;
  text-align:center;
}
#p {
  text-align:left;
}
#input {
  border:0.6rem, solid, #42b983;
}
</style>
