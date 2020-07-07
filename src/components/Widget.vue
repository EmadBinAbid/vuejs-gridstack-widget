<template>
  <div class="theme-body">
    <div class="row">
      <div class="col-lg-2 col-md-2">
        <div class="form-group has-feedback">
          <label class="flex">Widget Url</label>
          <div class="input-group">
            <div class="input-group-prepend"></div>
            <input
              type="text"
              class="form-control"
              v-model="urlInputValue"
              @input="checkValidUrlString"
              placeholder="Website URL"
              required
            />
          </div>
        </div>
      </div>

      <div class="col-lg-2 col-md-2">
        <div class="form-group has-feedback">
          <label class="flex">Widget name</label>
          <div class="input-group">
            <div class="input-group-prepend"></div>
            <input
              type="text"
              class="form-control"
              v-model="widgetNameInputValue"
              placeholder="Widget name"
              required
            />
          </div>
        </div>
      </div>

      <div class="col-lg-2 col-md-2">
        <div class="form-group has-feedback">
          <label class="flex">Header Foreground Color</label>
          <div class="input-group">
            <div class="input-group-prepend"></div>
            <v-swatches
              v-model="headerFontColor"
              popover-x="right"
              popover-y="bottom"
              :swatch-size="20"
              swatches="text-advanced"
              :trigger-style="{ width: '20px', height: '20px', borderRadius: '5px' }"
            ></v-swatches>
          </div>
        </div>
      </div>

      <div class="col-lg-2 col-md-2">
        <div class="form-group has-feedback">
          <label class="flex">Header Background Color</label>
          <div class="input-group">
            <div class="input-group-prepend"></div>
            <v-swatches
              v-model="headerBackgroundColor"
              popover-x="right"
              popover-y="bottom"
              :swatch-size="20"
              swatches="text-advanced"
              :trigger-style="{ width: '20px', height: '20px', borderRadius: '5px' }"
            ></v-swatches>
          </div>
        </div>
      </div>

      <div class="col-lg-2 col-md-2">
        <div class="form-group has-feedback">
          <label class="flex">-</label>
          <div class="input-group">
            <div class="input-group-prepend"></div>
            <button
              class="alert btn btn-primary btn-outline ml-auto"
              type="button"
              v-on:click="addNewWidget()"
            >Add</button>
          </div>
        </div>
      </div>
    </div>

    <div class="grid-stack gs-area"></div>
  </div>
</template>

<script>
// External Stylesheets
import "../scripts/css/bootstrap.min.css";
import "../scripts/js/plugins/validator/validator.css";
import "../scripts/css/dripicon.css";
import "../scripts/css/style.css";
import "../scripts/css/responsive.css";

// External Scripts
import "../scripts/js/jquerymain_jquerytouch_jquerycookies.js";
import "../scripts/js/bootstrap_popper.min.js";
import "../scripts/js/plugins/toaster/jquery.toaster.js";
import "../scripts/js/plugins/validator/validator.min.js";
import "../scripts/js/script.js";

import { v4 as uuidv4 } from "uuid";
import VSwatches from "vue-swatches";

import "vue-swatches/dist/vue-swatches.css";

export default {
  name: "Widget",
  props: {
    msg: String
  },
  components: {
      VSwatches
  },
  data() {
    return {
      grid: null,
      widgets: {},
      urlInputValue: "https://",
      widgetNameInputValue: "",
      headerFontColor: "#000000",
      headerBackgroundColor: "#F0F0F0"
    };
  },
  methods: {
    checkValidUrlString() {
      if (this.urlInputValue.length <= 8) {
        this.urlInputValue = "https://";
      }
    },

    makeGridStackWidget(key, webViewUrl) {
      let widgetText = `<div style="min-width:320px !important;">
                    <div id="gsic${key}" class="console-panel grid-stack-item-content">
                      
                      <div id="header${key}" class="console-panel-header" style="background: ${this.widgets[key].headerBackgroundColor}; color: ${this.widgets[key].headerFontColor};">
                        <div class="cph-left">
                          <h4 id="headerTitle${key}">${this.widgets[key].name}</h4>
                        </div>
                        <div class="cph-right">
                          <ul class="top-header-btns">
                            <li><a id="reload${key}" title="Reload" data-rel="tooltip"> <i class="fa fa-refresh"></i></a></li>
                            <li><a class="switch-full" title="Expand Panel To Full Screen" data-rel="tooltip"> <i class="icon dripicons-expand-2"></i></a></li>
                    
                            <li>
                              <a id="headerSettings${key}" title="Panel Options" data-rel="tooltip"> <i class="icon dripicons-dots-3"></i></a>
                            </li>
                    
                            <li><a id="close${key}" title="Remove Panel" data-rel="tooltip"> <i class="icon dripicons-cross"></i> </a></li>
                          </ul>
                        </div>
                      </div>
                      
                      <div class="console-panel-body" >
                        <div style="width:100%;height:100%;">
                          <webview id="wv${key}" partition="electron2" src="${webViewUrl}" useragent="Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X) AppleWebKit/603.1.23 (KHTML, like Gecko) Version/10.0 Mobile/14E5239e Safari/602.1" autosize="on" style="width:100%;height:100%;">Hello Vue</webview>
                        </div>
                      </div>
                    </div>
                </div>`;

      this.grid.addWidget(widgetText, {
        x: this.widgets[key].x,
        y: this.widgets[key].y,
        width: this.widgets[key].w,
        height: this.widgets[key].h,
        minWidth: 3,
        minHeight: 7,
        autoPosition: true,
      });

      this.registerRemoveWidget(key);
      //   this.registerHeaderSettings(key);
    },

    removeWidget(_uuid) {
      this.grid.removeWidget(
        $("#" + `gsic${_uuid}`).closest(".grid-stack-item")
      );
      delete this.widgets[_uuid];
      //   this.saveAllWidgetsStateToServer();
      // localStorage.setItem("webleash-widgets", JSON.stringify(this.widgets));
    },

    registerRemoveWidget(_uuid) {
      document.getElementById(`close${_uuid}`).onclick = () => {
        this.removeWidget(_uuid);
      };
    },

    addNewWidget() {
      //   this.modalErrorMessage = "";
      this.urlInputValue = this.urlInputValue.trim();
      this.checkURLRegEx = /(https?:\/\/(?:www\.|(?!www))[a-zA-Z0-9][a-zA-Z0-9-]+[a-zA-Z0-9]\.[^\s]{2,}|www\.[a-zA-Z0-9][a-zA-Z0-9-]+[a-zA-Z0-9]\.[^\s]{2,}|https?:\/\/(?:www\.|(?!www))[a-zA-Z0-9]+\.[^\s]{2,}|www\.[a-zA-Z0-9]+\.[^\s]{2,})/;
      var regex = new RegExp(this.checkURLRegEx);

      if (
        !this.urlInputValue.match(regex) ||
        !this.urlInputValue.startsWith("https://")
      ) {
        this.modalErrorMessage = "Please enter a valid url.";
        return;
      }

      this.widgetNameInputValue = this.widgetNameInputValue.trim();
      if (this.widgetNameInputValue === "") {
        this.widgetNameInputValue = this.urlInputValue;
      }

      const _uuid = uuidv4();
      this.widgets[_uuid] = {
        x: 0,
        y: 0,
        w: 4,
        h: 4,
        url: this.urlInputValue,
        name: this.widgetNameInputValue,
        headerBackgroundColor: this.headerBackgroundColor,
        headerFontColor: this.headerFontColor
        // tabId: this.currentActiveTabId,
        // isDraggable: !this.isDraggable,
        // isResizable: !this.isResizable
      };

      console.log(this.urlInputValue);

      this.makeGridStackWidget(_uuid, this.urlInputValue);

      // Maintaining draggable and resizable properties on newly added item
      // this.grid.movable(".grid-stack-item", this.isDraggable);
      // this.grid.resizable(".grid-stack-item", this.isResizable);

      this.urlInputValue = "https://";
      this.widgetNameInputValue = "";
      //   this.isDraggable = true;
      //   this.isResizable = true;

      //   this.saveAllWidgetsStateToServer();
      // localStorage.setItem("webleash-widgets", JSON.stringify(this.widgets));

      //   this.$modal.hide("add-new-widget");
    }
  },
  mounted() {
    this.grid = GridStack.init();

    this.addNewWidget();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.dummywidget {
  color: var(--text-color);
  background: var(--bg-color);
  width: 100%;
  height: 50px;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}

.tab-parent {
  position: relative;
}

.tab {
  position: relative;
}

.tab-header {
  height: 100px;
  margin-top: 8px;
  margin-right: 8px;
  position: relative;
}

.tab-right {
  float: right;
  /*margin-right: 8px; */
  /* top: 10px; */
  /* position: absolute; */
}

.my-custom-style {
  width: 50px;
}

.header {
  background-color: #f7f7f7;
  padding: 5px;
}

.widget {
  background: #000000;
}

.button {
  margin: 2px;
  color: #297fca;
}

.orange-text {
  color: #f6b264;
}

.icon-left {
  float: left;
}

.icon-right {
  float: right;
}

.right-align-content {
  float: right;
}

/* required file for gridstack to work */
@import "https://cdn.jsdelivr.net/npm/gridstack@1.1.1/dist/gridstack.min.css";

/* Optional styles for demos */
.btn-primary {
  color: #fff;
  background-color: #007bff;
}

.btn {
  display: inline-block;
  padding: 0.375rem 0.75rem;
  line-height: 1.5;
  border-radius: 0.25rem;
}

a {
  text-decoration: none;
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.grid-stack {
  /* background: lightgoldenrodyellow; */
  margin-top: 100px;
}

.grid-stack-item-content {
  color: #2c3e50;
  text-align: center;
  background-color: #e4f1fd;
  /* border: 1px solid #ccf2ff; */
}

/*** EXAMPLE ***/
#content {
  width: 100%;
}

.vue-grid-layout {
  background: #eee;
}

.layoutJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.eventsJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
  height: 100px;
  overflow-y: scroll;
}

.columns {
  -moz-columns: 120px;
  -webkit-columns: 120px;
  columns: 120px;
}

/*.vue-resizable-handle {
    z-index: 5000;
    position: absolute;
    width: 20px;
    height: 20px;
    bottom: 0;
    right: 0;
    background: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=');
    background-position: bottom right;
    padding: 0 3px 3px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: se-resize;
}*/

.vue-grid-item:not(.vue-grid-placeholder) {
  /* background: #ff0000; */
  border: 1px solid black;
}

.vue-grid-item.resizing {
  opacity: 0.9;
}

.vue-grid-item.static {
  background: #cce;
}

.vue-grid-item .text {
  font-size: 24px;
  text-align: center;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
  height: 100%;
  width: 100%;
}

.vue-grid-item .no-drag {
  height: 100%;
  width: 100%;
}

.vue-grid-item .minMax {
  font-size: 12px;
}

.vue-grid-item .add {
  cursor: pointer;
}

.vue-draggable-handle {
  position: absolute;
  width: 20px;
  height: 20px;
  top: 0;
  left: 0;
  background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>")
    no-repeat;
  background-position: bottom right;
  padding: 0 8px 8px 0;
  background-repeat: no-repeat;
  background-origin: content-box;
  box-sizing: border-box;
  cursor: pointer;
}
</style>


