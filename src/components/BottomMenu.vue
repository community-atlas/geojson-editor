<template>
  <Row id="bottomMenu">
    <ButtonGroup>
      <Button @click="saveToGeojson">Save</Button>
    </ButtonGroup>
  </Row>
</template>

<script>
import FileSaver from "file-saver";
import { topology } from "topojson-server";
import wkt from "wellknown";
import shape from "shp-write";

export default {
  name: "BottomMenu",
  data() {
    return {
      supportedFormats: [
        {
          label: "Shapefile",
          value: "shp"
        },
        {
          label: "TopoJSON",
          value: "topojson"
        },
        {
          label: "WKT",
          value: "wkt"
        }
      ]
    };
  },
  methods: {
    copy: function() {
      const el = document.createElement("textarea");
      el.value = this.$store.state.geojson;
      document.body.appendChild(el);
      el.select();
      document.execCommand("copy");
      document.body.removeChild(el);

      this.$Notice.open({
        title: "Copied to clipboard",
        duration: 2.5
      });
    },
    saveToGeojson: function() {
      function getUrlVars() {
        var vars = {};
        var parts = window.location.href.replace(
          /[?&]+([^=&]+)=([^&]*)/gi,
          function(m, key, value) {
            vars[key] = value;
          }
        );
        return vars;
      }

      // A hack to quickly look for starting location
      // @todo: Use VUE routes

      let vars = getUrlVars();
      let docId = vars.docId || 0;
      let featureId = encodeURIComponent(vars.featureId) || 0;      

      let url = `https://node-red.community-atlas.net/complex/${docId}/${featureId}`;
      fetch(url, {
        method: "POST", // *GET, POST, PUT, DELETE, etc.
        mode: "no-cors", // no-cors, *cors, same-origin
        cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
        headers: {
          "Content-Type": "application/json"
          // 'Content-Type': 'application/x-www-form-urlencoded',
        },
        redirect: "follow", // manual, *follow, error
        referrerPolicy: "no-referrer", // no-referrer, *client
        body: this.$store.state.geojsonString // body data type must match "Content-Type" header
      });
    }
  }
};
</script>

<style>
#bottomMenu {
  position: absolute;
  height: 50px;
  bottom: 0px;
  padding: 10px;
  background: #f3f3f3;
  width: 100%;
  text-align: right;
  z-index: 1000;
}
.pad {
  margin-right: 10px;
}
.ivu-notice {
  top: calc(100vh - 115px) !important;
  width: 200px;
}
.ivu-notice-with-normal:after {
  background: #bfc0c0;
}
</style>
