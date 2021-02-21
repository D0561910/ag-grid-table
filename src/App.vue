<template>
  <div>
    <button @click="getSelectedRows()">Get Selected Rows</button>
    <button @click="nextpg()">Get Selected To Next Page</button>
    <br />
    <input type="text" id="quickFilter" placeholder="quick filter..." />
    <button @click="onQuickFilterChanged()">Search</button>

    <ag-grid-vue
      style="width: 500px; height: 500px"
      class="ag-theme-alpine"
      :columnDefs="columnDefs"
      :rowData="rowData"
      :gridOptions="gridOptions"
    >
    </ag-grid-vue>
    <br />
    <ag-grid-vue
      style="width: 500px; height: 500px"
      class="ag-theme-alpine"
      :columnDefs="columnDefs2"
      :rowData="rowData2"
      :gridOptions="gridOptions2"
    >
    </ag-grid-vue>
  </div>
</template>

<script>
import { AgGridVue } from "ag-grid-vue";

export default {
  name: "App",
  data() {
    return {
      columnDefs: null,
      rowData: null,
      gridOptions: null,
      columnDefs2: null,
      rowData2: null,
      gridOptions2: null,
    };
  },

  components: {
    AgGridVue,
  },

  methods: {
    onGridReady(params) {
      this.gridApi = params.api;
      this.columnApi = params.columnApi;
    },

    onGridReady2(params) {
      this.gridApi2 = params.api;
      this.columnApi2 = params.columnApi;
    },

    onQuickFilterChanged() {
      this.gridApi.setQuickFilter(document.getElementById("quickFilter").value);
    },

    getSelectedRows() {
      const selectedNodes = this.gridApi.getSelectedNodes();
      const selectedData = selectedNodes.map((node) => node.data);
      const selectedDataStringPresentation = selectedData
        .map((node) => `${node.make} ${node.model}`)
        .join(", ");
      alert(`Selected nodes: ${selectedDataStringPresentation}`);
    },

    nextpg() {
      console.log(new Date())
      const selectedNodes = this.gridApi.getSelectedNodes();
      const selectedData = selectedNodes.map((node) => node.data);
      this.gridApi2.setRowData(selectedData);
      this.gridApi2.forEachNode((node) => {
        node.setSelected(true);
      });
      console.log(new Date());
    },
  },

  beforeMount() {
    this.columnDefs = [
      {
        headerName: "Make",
        field: "make",
        checkboxSelection: true,
        headerCheckboxSelection: true,
        headerCheckboxSelectionFilteredOnly: true,
      },
      { headerName: "Model", field: "model" },
      { headerName: "Price", field: "price" },
    ];

    // https://www.ag-grid.com/example-assets/olympic-winners.json
    fetch("https://www.ag-grid.com/example-assets/row-data.json")
    // fetch("https://www.ag-grid.com/example-assets/olympic-winners.json")
      .then((result) => result.json())
      .then((rowData) => {
        let rowDatas = [];
        for (let j = 0; j < 15; j++) {
          for (let i in rowData) {
            rowDatas.push(rowData[i]);
          }
        }
        console.log(rowDatas.length);
        this.rowData = rowDatas;
      });

    this.gridOptions = {
      rowSelection: "multiple",
      onGridReady: (event) => this.onGridReady(event),
    };

    this.columnDefs2 = [
      {
        headerName: "Make",
        field: "make",
        checkboxSelection: true,
        headerCheckboxSelection: true,
        headerCheckboxSelectionFilteredOnly: true,
      },
      { headerName: "Model", field: "model" },
      { headerName: "Price", field: "price" },
    ];

    this.rowData2 = [];

    this.gridOptions2 = {
      rowSelection: "multiple",
      onGridReady: (event) => this.onGridReady2(event),
    };
  },
};
</script>

<style>
@import "../node_modules/ag-grid-community/dist/styles/ag-grid.css";
@import "../node_modules/ag-grid-community/dist/styles/ag-theme-alpine.css";

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
