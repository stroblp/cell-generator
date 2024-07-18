<script>
  import { onMount } from 'svelte';
  import DataTable from './DataTable.svelte';

  let headers = [];
  let columnDefs = [];
  let rowData = [];
  let lte700Data = [];
  let lte800Data = [];
  let lte1800Data = [];
  let lte2100Data = [];
  let lte2600Data = [];

  function handlePaste(event) {
    const text = event.clipboardData.getData('text');
    if (text) {
      const rows = text.trim().split('\n').map(row => row.split('\t'));
      if (rows.length > 0) {
        headers = rows[0]; // First row as headers
        columnDefs = headers.map(header => ({ headerName: header, field: header }));
        rowData = rows.slice(1).map(row => {
          let rowData = {};
          headers.forEach((header, index) => {
            rowData[header] = row[index];
          });
          return rowData;
        });
      }
    }
  }

  function calculateLTE700(updatedData) {
    lte700Data = updatedData.filter(row => row[headers[0]] % 2 === 0); // Example logic
  }

  function calculateLTE800(updatedData) {
    lte800Data = updatedData.filter(row => row[headers[0]] % 2 !== 0); // Example logic
  }

  function calculateLTE1800(updatedData) {
    lte1800Data = updatedData.filter(row => row[headers[0]] > 5); // Example logic
  }

  function calculateLTE2100(updatedData) {
    lte2100Data = updatedData.filter(row => row[headers[0]] < 5); // Example logic
  }

  function calculateLTE2600(updatedData) {
    lte2600Data = updatedData.filter(row => row[headers[0]] === 5); // Example logic
  }

  onMount(() => {
    document.addEventListener('paste', handlePaste);
  });

  function handleUpdateCell({ detail: { updatedRowData } }) {
    rowData = updatedRowData;
  }
</script>

<style>
  .paste-area {
    border: 2px dashed #ccc;
    padding: 20px;
    text-align: center;
    margin: 10px 0;
    cursor: pointer;
  }
  .full-height {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
  }
</style>

<div class="">
  <h1 class="text-center my-4">Cell Generator</h1>

  <div class="row justify-content-center">
    <div class="">
      <div class="paste-area" on:paste={handlePaste}>
        Paste cells here
      </div>
    </div>
  </div>

  {#if rowData.length > 0}
    <div class="row justify-content-center">
        <DataTable title="Original Data" {columnDefs} {rowData} onCalculate={(data) => rowData = data} on:updateCell={handleUpdateCell} />
    </div>
  {/if}

  <div class="row justify-content-center">

      <DataTable title="LTE700" {columnDefs} {rowData} onCalculate={calculateLTE700} />
      <DataTable title="LTE800" {columnDefs} {rowData} onCalculate={calculateLTE800} />
      <DataTable title="LTE1800" {columnDefs} {rowData} onCalculate={calculateLTE1800} />
      <DataTable title="LTE2100" {columnDefs} {rowData} onCalculate={calculateLTE2100} />
      <DataTable title="LTE2600" {columnDefs} {rowData} onCalculate={calculateLTE2600} />

  </div>
</div>
