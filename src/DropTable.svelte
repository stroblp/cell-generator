<script>
    import { onMount } from 'svelte';
    let headers = [];
    let tableData = [];
  
    function handlePaste(event) {
      const text = event.clipboardData.getData('text');
      if (text) {
        const rows = text.trim().split('\n').map(row => row.split('\t'));
        if (rows.length > 0) {
          headers = rows[0]; // First row as headers
          tableData = rows.slice(1).map(row => {
            let rowData = {};
            headers.forEach((header, index) => {
              rowData[header] = row[index];
            });
            return rowData;
          });
        }
      }
    }
  
    onMount(() => {
      document.addEventListener('paste', handlePaste);
    });
  
    function copyTable() {
      const table = document.getElementById('dataTable');
      let range, selection;
      if (document.createRange) {
        range = document.createRange();
        range.selectNode(table);
        selection = window.getSelection();
        selection.removeAllRanges();
        selection.addRange(range);
        document.execCommand('copy');
        alert('Table copied to clipboard');
      }
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
    .single-line {
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  </style>
  
  <div class="container-fluid">
    <div class="row justify-content-center mt-3">
      <div class="col-10">
        <div class="paste-area">
          Paste copied Excel cells here
        </div>
      </div>
    </div>
  
    {#if tableData.length > 0}
      <div class="row justify-content-center">
        <div class="col-10">
          <button class="btn btn-primary mb-3" on:click={copyTable}>Copy Table</button>
          <div class="accordion" id="dataAccordion">
            <div class="card">
              <div class="card-header" id="headingOne">
                <h5 class="mb-0">
                  <button class="btn btn-link" type="button" data-toggle="collapse" data-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne">
                    Original Data
                  </button>
                </h5>
              </div>
  
              <div id="collapseOne" class="collapse show" aria-labelledby="headingOne" data-parent="#dataAccordion">
                <div class="card-body">
                  <div class="table-responsive">
                    <table id="dataTable" class="table table-striped table-bordered">
                      <thead>
                        <tr>
                          {#each headers as header}
                            <th class="single-line">{header}</th>
                          {/each}
                        </tr>
                      </thead>
                      <tbody>
                        {#each tableData as row}
                          <tr>
                            {#each headers as header}
                              <td class="single-line">{row[header]}</td>
                            {/each}
                          </tr>
                        {/each}
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    {/if}
  </div>
  