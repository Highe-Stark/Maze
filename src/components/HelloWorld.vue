<template>
  <div id="maze">
    <h1>{{ msg }}</h1>
    <canvas id="board" height="480px" width="480px" style="border:2px solid #42b983" class="canvas"></canvas>
    </br>
    <span>
      <p id="au">{{ bottom }} </p>
      <p id="po">{{ powered }}
        <img src="./../assets/logo.png" height="25px" width="25px"/>
      </p>
    </span>
  </div>
</template>

<!-- script for drawing maze -->
<script>
export default {
  name: 'Maze',
  data () {
    return {
      msg: 'Welcome to Maze world.',
      bottom: '------ Made By Higher Stark ------- ',
      powered: 'Powered By '
    }
  },
  mounted () {
    var c = document.querySelector('canvas')
    var cxt = c.getContext('2d')
    cxt.translate(0.5, 0.5)
    cxt.strokeStyle = '#42b983'

    var Node = {
      new: function (_x, _y, _parent = null) {
        let node = {
          x: _x,
          y: _y,
          parent: _parent,
          children: [],
          neighbor: function () {
            // if node is on the left border
            if (node.x === 0) {
              let subdot = Node.new(node.x + 1, node.y)
              node.children.push(subdot)
              // on left top corner
              if (node.y === 0) {
                subdot.x = 0
                subdot.y = 1
                node.children.push(subdot)
              } else if (node.y === c.width / 20 - 1) {
                // on left bottom corner
                subdot.x = 0
                subdot.y = node.y - 1
                node.children.push(subdot)
              } else {
                // on left border, not corner
                subdot.x = node.x
                subdot.y = node.y + 1
                node.children.push(subdot)
                subdot.y = node.y - 1
                node.childrenpush(subdot)
              }
            } else if (node.x === c.height / 20 - 1) {
              // on the right border
              let subdot = Node.new(node.x - 1, node.y)
              node.children.push(subdot)
              // on the right top corner
              if (node.y === 0) {
                subdot.x = node.x
                subdot.y = node.y + 1
                node.children.push(subdot)
              } else if (node.y === c.width / 20 - 1) {
                // on the right bottom corner
                subdot.x = node.x
                subdot.y = node.y - 1
                node.children.push(subdot)
              } else {
                // on the right border, not corner
                subdot.x = node.x
                subdot.y = node.y - 1
                node.children.push(subdot)
                subdot.y = node.y + 1
                node.children.push(subdot)
              }
            } else {
              // neither on left nor right border
              let subdot = Node.new(node.x - 1, node.y)
              node.children.push(subdot)
              subdot.x = node.x + 1
              node.children.push(subdot)
              // on top border
              if (node.y === 0) {
                subdot.x = node.x
                subdot.y = node.y + 1
                node.children.push(subdot)
              } else if (node.y === c.width / 20 - 1) {
                // on bottom border
                subdot.x = node.x
                subdot.y = node.y - 1
                node.children.push(subdot)
              } else {
                // at central part
                subdot.x = node.x
                subdot.y = node.y - 1
                node.children.push(subdot)
                subdot.y = node.y + 1
                node.children.push(subdot)
              }
            }
          }
        }

        return node
      }
    }

    var root = Node.new(0, 0)

    let stack = [root]
    let paths = [stack]
    let visited = new Set()
    visited.add(root)
    while (paths.length !== 0) {
      stack = paths.pop()
      let node = stack[stack.length - 1]

      console.log(node, node.x, node.y, node.parent)

      node.children = node.neighbor(node)
      if (node.children.length === 0) Error('node children is empty')
      let sub = []
      for (let nei = 0; nei < node.children.length;) {
        if (visited.has(node.children[nei])) {
          node.children.splice(nei, 1)
          continue
        }
        node.children[nei].parent = node
        sub.push(node.children[nei++])
      }

      console.log(node.children.length)

      while (sub.length !== 0) {
        let path = stack
        let idx = Math.random() % sub.length
        path.push(sub[idx])
        paths.push(path)
        visited.add(sub[idx])
        sub.splice(idx, 1)
      }
    }

    stack = [root]
    while (stack.length !== 0) {
      let curr = stack.pop()
      let walls = new Set()
      walls.add(-2)
      walls.add(-1)
      walls.add(1)
      walls.add(2)
      if (curr.parent !== null) {
        let diff = curr.parent.x - curr.x + 2 * (curr.parent.y - curr.y)
        walls.delete(diff)
      }
      for (let child = 0; child < curr.children.length; ++child) {
        stack.push(curr.children[child])
        let diff = curr.children[child].x - curr.x + 2 * (curr.children[child].y - curr.y)
        walls.delete(diff)
      }
      if (walls.has(-2)) {
        cxt.moveTo(20 * curr.x, 20 * curr.y)
        cxt.lineTo(20 * curr.x, 20 * curr.y + 20)
      }
      if (walls.has(-1) && !(curr.x === 0 && curr.y === 0)) {
        cxt.moveTo(20 * curr.x, 20 * curr.y)
        cxt.lineTo(20 * curr.x + 20, 20 * curr.y)
      }
      if (walls.has(1) && !(curr.x === 0 && curr.y === 0)) {
        cxt.moveTo(20 * curr.x + 20, 20 * curr.y + 20)
        cxt.lineTo(20 * curr.x, 20 * curr.y + 20)
      }
      if (walls.has(2)) {
        cxt.moveTo(20 * curr.x + 20, 20 * curr.y + 20)
        cxt.lineTo(20 * curr.x + 20, 20 * curr.y)
      }
    }

    cxt.stroke()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
  color:#42b983;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#au {
  text-align:center;
  color:aquamarine;
  font-size:1.4rem;
}
#po {
  text-align: right;
  color:#42b983;
  font-size:1.1rem;
}
</style>
