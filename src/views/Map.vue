<template>
  <div class="container is-fullhd" style="background-color: #EEEEEE;">
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-menu">
        <div class="navbar-start">
          <div class="navbar-item">
            <a
              class="button is-text is-myBack"
              @click="$router.push({name: 'select', params:{group:group, name:name, selected:item_url}})"
            >
              <span class="icon">
                <i class="fas fa-chevron-left"></i>
              </span>
            </a>
            <div class="navbar-brand">
              <a class="navbar-item">
                <h5 class="subtitle is-4 has-text-dark has-text-weight-bold">{{name}}</h5>
              </a>
            </div>
          </div>
        </div>
      </div>
    </nav>
    <br>
    <br>
		<br>

		<div id="drawframe" :style="'overflow-y: scroll; overflow-x: scroll; height:' + height_size + 'px; width:' + window.width + 'px'">
			<div id="drawing"></div>
		</div>

    <div class="columns">
      <div class="column columnPadding">
        <article class="message is-dark is-medium">
          <div class="message-header">
            <p>Your route </p>
					</div>

          <div class="maessage-body">
						
                <div v-for="(key, index) in ordered_items" :key="index">
									<br>
                  <p v-if="key.shelf != null">{{ key.name }}</p>
									<p v-else class="has-text-link has-text-weight-bold">{{ key.name }}</p>
									<p v-if="key.shelf != null" class="subtitle is-6">Shelf : {{ key.shelf }}</p>
									<i v-if="index != ordered_items.length - 1" class="fas fa-arrow-down"></i>
                </div>
              <br>
          </div>
        </article>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      group: "",
      name: "",
      items_string: "",
			items: [],
			itemId: [],
      keys: [],
      ordered_items: [],
      names: {},
      points: {},
      window: {
        width: 0,
        height: 0
			},
			canvas: {
				width: 976,
				height: 656
			}
    };
  },
  mounted() {
    // canvas init
		var draw = SVG("drawing").size(this.canvas.width, this.canvas.height);

		let color_line = '#001D4A'
		let color_point = '#68C5DB'
		let color_run = '#FF6100'
		
		// scroll to bottom right
		var elmnt = document.getElementById("drawframe");
		console.log(elmnt)
		elmnt.scrollLeft += 500;
		elmnt.scrollTop += 500;

		// init variable
    var group = this.group;
		var name = this.name;
		
		// draw image
    var image = draw
      .image("http://localhost:8000/stores/mapimage/" + group + "/" + name)
      .loaded(function(loader) {
        this.size(loader.width, loader.height);
      });

    this.$http.get("http://localhost:8000/stores/mappoint/" + group + "/" + name)
      .then(response => {
        // data : point of all nodes
        var data = response.data;
        this.points = data;

        this.$http.get("http://localhost:8000/stores/" + group + "/" + name + "/route/" + this.items_string)
          .then(response => {
						// order : item order in route
						var order = response.data.order;
						// path : path from output
						var path = response.data.path;
						// path_point : position of each path point
						var path_point = [];
						// set position 
            for (var i = 0; i < path.length; i++) {
              path_point.push([
                this.points[path[i]][0],
                this.points[path[i]][1]
              ]);
            }

						// keys : ID of all selected items
						var keys = this.items_string.split(";")



            this.$http.get("http://localhost:8000/stores/" + group + "/" + name)
              .then(response => {
								// items : {} of all items
								var items = response.data.data;
								// names : name of each ID [NodeName]
								var names = {}

								// set all name and key, key = Node Name
								for (var i = 0; i < items.length; i++) {
									names[items[i].Node] = items[i].Brand
								}

								// set ordered item
								for (var i = 0; i < order.length; i++) {
									var name = ''
									if (order[i] == 'START') {
										name = 'Entrance'
										this.ordered_items.push({name:name, shelf:null})
									} else if (order[i].substring(0, 4) == 'CASH') {
										name = 'Casheir ' + order[i].substring(4, 5)
										this.ordered_items.push({name:name, shelf:null})
									} else {
										name = names[order[i]]
										var shelf = ' '
										for (var j = 0; j < items.length; j++) {
											if (name == items[j].Brand) {
												shelf = items[j].Shelf
												break
											}
										}
										this.ordered_items.push({name:name, shelf:shelf})
									}
								}

								// change keys value from ID to Node
								for (var i = 0; i < keys.length; i++) {
									for (var j = 0; j < items.length; j++) {
										if (keys[i] == items[j].ID) {
											keys[i] = items[j].Node
											break
										}
									}
								}

								// draw small gray point
                for (var key in data) {
                  if (key in names) {
                    var x = data[key][0];
                    var y = data[key][1];
                    draw
                      .circle(10)
                      .attr({ fill: "#000", "fill-opacity": 0.15 })
                      .move(x - 5, y - 5);
                  }
								}

								// all keys in path 
                for (var i = 0; i < path.length; i++) {
									let node = path[i]
                  if (node in names) {
                    this.keys.push(node);
                  }
								}
							
								// draw line
                var polyline = draw.polyline(path_point);
                polyline.fill("none");
                polyline.stroke({color: color_line, width: 4, linecap: "round", linejoin: "round", opacity: 0.6});

								// draw all target points 
                for (var i = 0; i < keys.length; i++) {
									console.log("TESTTTT ------>>>> |"+keys[i]+"|")
									if (this.points[keys[i]]) {
										var x = this.points[keys[i]][0];
										var y = this.points[keys[i]][1];
										draw
											.circle(20)
											.attr({ fill: color_point, "fill-opacity": 1 })
											.center(x, y);
									}
								}
								
								// draw START and CASH point
                draw
                  .circle(25)
                  .attr({ fill: color_line, "fill-opacity": 1 })
                  .center(this.points[path[0]][0], this.points[path[0]][1]);
                draw
                  .circle(25)
                  .attr({ fill: color_line, "fill-opacity": 1 })
                  .center(this.points[path[path.length - 1]][0], this.points[path[path.length - 1]][1]);

                var path_string = "m " + path_point[0][0] + "," + path_point[0][1] + " ";
                for (var i = 1; i < path_point.length; i++) {
									path_string += path_point[i][0] - path_point[i - 1][0] + "," + 
																(path_point[i][1] - path_point[i - 1][1]) + " ";
                }

								// draw line
                var ppp = draw.path(path_string);
                ppp.fill("none");
                var length = ppp.length();

								// moving point
                var current = draw.circle(20).move(path_point[0][0] - 10, path_point[0][1] - 10);
                current.attr({ fill: color_run });

								// animate moving point
                current.animate(length * 5, "<>")
                  .during(function(pos, morph, eased) {
                    var p = ppp.pointAt(eased * length);
                    current.center(p.x, p.y);
                  }).loop();

								// draw all text
                for (var i = 0; i < keys.length; i++) {
									if (this.points[keys[i]]) {
										var x = this.points[keys[i]][0];
										var y = this.points[keys[i]][1];
										console.log(' >>>>>> ' + names[keys[i]])
										var text = draw.text(names[keys[i]]).move(x, y - 10);
										text.stroke({ color: "#fff", opacity: 0.9, width: 3 });
										draw.text(names[keys[i]]).move(x, y - 10);
									}
								}
								

              });
          });
      });
	},
	computed: {
		item_url() {
			if (this.itemId.length == 0) {
				return ' '
      }else {
				return this.itemId.join(';')
      }
    },
		height_size () {
			let h = this.window.height
			if (h > 1000) {
				return this.canvas.height
			} else {
				return this.window.height * 0.6
			}
		}
	},
  methods: {
		handleResize() {
			this.window.width = window.innerWidth;
      this.window.height = window.innerHeight;
    }
  },
  created() {
		this.group = this.$route.params.group;
    this.name = this.$route.params.name;
		this.items_string = this.$route.params.items;
		this.itemId = this.$route.params.items.split(";")
    window.addEventListener("resize", this.handleResize);
    this.handleResize();
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  }
};
</script>

<style scoped>
.navbar {
  background-color: #FDB849;
  height: 60px;
  position: fixed;
  overflow: hidden;
  width: 100%;
  top: 0;
}

.navbar-menu {
  margin-right: 0px !important;
  margin-left: 0px;
}

.navbar-start {
  padding-left: 12px;
}

.navbar-end {
  padding-right: 10px;
}

.has-text-centered {
  text-align: center;
}

.marginsTop {
  margin-top: 18px !important;
}
</style>
