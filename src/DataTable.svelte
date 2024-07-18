<script>
    import { onMount } from 'svelte';
    import 'ag-grid-community/styles/ag-grid.css';
    import 'ag-grid-community/styles/ag-theme-alpine.css';
    import { Grid } from 'ag-grid-community';
  
    export let title;
    export let columnDefs = [];
    export let rowData = [];
    export let onCalculate;
  
    let gridDiv;
    let gridOptions = {
      columnDefs: columnDefs,
      rowData: rowData,
      defaultColDef: {
        editable: true,
        resizable: true,
      },
      onCellValueChanged: handleCellValueChanged
    };
  
    function handleCellValueChanged(event) {
      const updatedRowData = [];
      gridOptions.api.forEachNode(node => updatedRowData.push(node.data));
      dispatch('updateCell', { updatedRowData });
    }
  
    onMount(() => {
      new Grid(gridDiv, gridOptions);
    });
  
    function handleCalculate() {
      const updatedData = [];
      gridOptions.api.forEachNode(node => updatedData.push(node.data));
      onCalculate(updatedData);
    }
  </script>
  

    <button class="btn btn-primary mb-3" on:click={handleCalculate}>Calculate {title}</button>
    <h2>{title}</h2>
    <div bind:this={gridDiv} class="ag-theme-alpine" style="height: 400px; width: 100%;"></div>

  
  <style>
    .container {
      width: 100%;
      height: var(--grid-height, 800px);
    }
  </style>
  