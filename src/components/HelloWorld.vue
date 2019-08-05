<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h3>
      antv/g6
    </h3>
    <ul>
      <li><a href="https://antv.alipay.com/zh-cn/g6/3.x/demo/tree/tree-indented.html" target="_blank" rel="noopener">see original version of file sytem in antv/g6</a></li>
    </ul>
    <div id='an-unique-id'>
    </div>
  </div>
</template>

<script>
import G6 from '@antv/g6';
import Hierarchy from '@antv/hierarchy'
const graphID = 'an-unique-id'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted(){
      G6.registerNode('file-node', {
    draw: function draw(cfg, group) {
      var keyShape = group.addShape('rect', {
        attrs: {
          x: cfg.x - 4,
          y: cfg.y - 12,
          fill: '#fff',
          stroke: null
        }
      });
      var marker = void 0;
      if (cfg.data.collapsed) {
        marker = group.addShape('marker', {
          attrs: {
            symbol: 'triangle',
            x: cfg.x + 4,
            y: cfg.y - 2,
            r: 4,
            fill: '#666'
          }
        });
      } else if (cfg.data.children && cfg.data.children.length > 0) {
        marker = group.addShape('marker', {
          attrs: {
            symbol: 'triangle-down',
            x: cfg.x + 4,
            y: cfg.y - 2,
            r: 4,
            fill: '#666'
          }
        });
      }
      var shape = group.addShape('text', {
        attrs: {
          x: cfg.x + 15,
          y: cfg.y + 4,
          text: cfg.data.name,
          fill: '#666',
          fontSize: 16,
          textAlign: 'left'
        }
      });
      var bbox = shape.getBBox();
      keyShape.attr({
        width: bbox.width + 20,
        height: bbox.height + 4
      });
      return keyShape;
    }
  });
  G6.registerEdge('step-line', {
    getControlPoints: function getControlPoints(cfg) {
      var startPoint = cfg.startPoint;
      var endPoint = cfg.endPoint;
      return [{
        x: startPoint.x,
        y: endPoint.y
      }];
    }
  }, 'polyline');
  var graph = new G6.TreeGraph({
    container: graphID,
    width: window.innerWidth,
    height: window.innerHeight,
    pixelRatio: 2,
    linkCenter: true,
    modes: {
      default: [{
        type: 'collapse-expand',
        animate: false,
        onChange: function onChange(item, collapsed) {
          var data = item.get('model').data;
          data.collapsed = collapsed;
          return true;
        }
    }, 'drag-canvas', 'zoom-canvas']
    },
    edgeStyle: {
      default: {
        stroke: '#A3B1BF'
      }
    },
    layout: function layout(data) {
      return Hierarchy.indented(data, {
        isHorizontal: true,
        direction: 'LR',
        indent: 80,
        getHeight: function getHeight() {
          return 16;
        },
        getWidth: function getWidth() {
          return 16;
        }
      });
      }
    });
    var data = {
      "id": "1",
      "name": "src",
      "children": [{
        "id": "1-1",
        "name": "behavior",
        "children": []
    }, {
        "id": "1-3",
        "name": "graph",
        "children": [{
          "id": "1-3-1",
          "name": "controller",
          "children": []
      }]
    }, {
        "id": "1-5",
        "name": "item",
        "children": []
    }, {
        "id": "1-6",
        "name": "shape",
        "children": [{
          "id": "1-6-2",
          "name": "extend",
          "children": []
      }]
    }, {
        "id": "1-7",
        "name": "util",
        "children": []
    }]
    };
    graph.data(data);
    graph.render();
    graph.getNodes().forEach(function(node) {
      var model = node.get('model');
      model.shape = 'file-node';
      model.label = model.data.name;
    });
    graph.getEdges().forEach(function(edge) {
      edge.get('model').shape = 'step-line';
    });
    graph.refresh();
    graph.fitView();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
