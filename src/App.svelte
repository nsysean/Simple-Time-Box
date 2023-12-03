<script>
  const size = [19, 31, 12];
  const pref = [5, 1, 1];

  let currentTab = 1;
  let template = {
    priorities: [],
    dump: "",
    schedule: JSON.parse(JSON.stringify(Array(31).fill({ 0: "", 30: "" }))),
  };
  let data = Array(3).fill(template);

  function changeTab(number) {
    currentTab = number;
  }

  function deleteEntry(record, index) {
    data[currentTab - 1][record].splice(index, 1);
    data[currentTab - 1][record] = data[currentTab - 1][record];
  }

  function newEntry(record) {
    data[currentTab - 1][record].push("");
    data[currentTab - 1][record] = data[currentTab - 1][record];
  }

  function resetTab() {
    while (data[currentTab - 1].priorities.length > 0) {
        data[currentTab - 1].priorities.splice(0, 1);
    }
    for (const key in template) {
      data[currentTab - 1][key] = JSON.parse(JSON.stringify(template[key]));
    }
  }

  if (localStorage.getItem("timebox_storage") != undefined) {
    changeTab(JSON.parse(localStorage.getItem("timebox_storage")).currentTab);
    data = JSON.parse(localStorage.getItem("timebox_storage")).data;
  }

  $: localStorage.setItem(
    "timebox_storage",
    JSON.stringify({ currentTab, data }),
  );
</script>

<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"
  integrity="sha512-DTOQO9RWCH3ppGqcWaEA1BIZOC6xxalwEsw9c2QQeAIftl+Vegovlnee1c9QX4TctnWMn13TZye+giMm8e2LwA=="
  crossorigin="anonymous"
  referrerpolicy="no-referrer"
/>
<div class="wrapper">
  <div class="header">Simple Time Box</div>
  <div class="tabs">
    <div>
      <button
        class={"btn " + (currentTab == 1 ? "active" : "")}
        on:click={() => changeTab(1)}
      >
        Daily
      </button>
      <button
        class={"btn " + (currentTab == 2 ? "active" : "")}
        on:click={() => changeTab(2)}
      >
        Monthly
      </button>
      <button
        class={"btn " + (currentTab == 3 ? "active" : "")}
        on:click={() => changeTab(3)}
      >
        Yearly
      </button>
    </div>
    <button class="btn" on:click={() => resetTab()}>
      Reset {(() => {
        switch (currentTab) {
          case 1:
            return "Daily";
          case 2:
            return "Monthly";
          case 3:
            return "Yearly";
        }
      })()}
    </button>
  </div>
  <div class="content">
    <div class="priorities">
      <div class="title">
        <span>Priorities</span>
        <button class="add" on:click={() => newEntry("priorities")}> + </button>
      </div>
      <ul class="list">
        {#each Object.entries(data[currentTab - 1].priorities) as [trash, text], index}
          <li class="item">
            <input
              type="text"
              class="text"
              bind:value={data[currentTab - 1].priorities[index]}
            />
            <button
              class="del"
              on:click={() => deleteEntry("priorities", index)}
            >
              Delete
            </button>
          </li>
        {/each}
      </ul>
    </div>
    <div class="dump">
      <div class="title">Brain dump</div>
      <textarea class="area" rows="10" bind:value={data[currentTab - 1].dump}
      ></textarea>
    </div>
    <div class="schedule">
      <div class="title">Schedule</div>
      <table class="table">
        <tr>
          {#if currentTab == 1}
          <th></th>
          <th>:00</th>
          <th>:30</th>
          {/if}
        </tr>
        {#each Object.entries(data[currentTab - 1].schedule.slice(0, size[currentTab - 1])) as [trash, elm], index}
          <tr>
            <td style="text-align: center">{index + pref[currentTab - 1]}</td>
            <td>
              <input
                type="text"
                class="text"
                bind:value={data[currentTab - 1].schedule[index][0]}
              />
            </td>
            {#if currentTab == 1}
            <td>
              <input
                type="text"
                class="text"
                bind:value={data[currentTab - 1].schedule[index][30]}
              />
            </td>
            {/if}
          </tr>
        {/each}
      </table>
    </div>
  </div>
</div>

<!-- CSS -->

<style>
  @import url("https://fonts.googleapis.com/css2?family=Ubuntu:wght@400;500&display=swap");

  * {
    font-family: "Ubuntu", sans-serif;
  }

  .wrapper {
    max-height: calc(100% - 50px);
    width: 100%;
    margin-block: 25px;
    overflow-y: scroll;
    -ms-overflow-style: none;
    scrollbar-width: none;
  }

  .wrapper::-webkit-scrollbar {
    display: none;
  }

  .wrapper .header {
    font-size: 30px;
    font-weight: 500;
    max-width: 920px;
    width: 90%;
    margin-inline: auto;
  }

  .wrapper .tabs {
    margin-block: 15px;
    width: 100%;
    display: flex;
    justify-content: space-between;
    overflow: hidden;
    max-width: 920px;
    width: 90%;
    margin-inline: auto;
  }

  .wrapper .tabs .btn {
    color: #37352fa6;
    background-color: transparent;
    border: none;
    font-size: 14px;
    padding: 7.5px;
    border-radius: 5px;
    position: relative;
  }

  .wrapper .tabs .btn:hover {
    cursor: pointer;
    background-color: #f3f3f3;
  }

  .wrapper .tabs .btn.active {
    color: black;
  }

  .wrapper .tabs .btn::before {
    width: 100%;
    height: 1px;
    position: absolute;
    bottom: -1px;
    left: 0;
    background-color: black;
    content: "";
    transform: scaleX(0);
    transition: all ease-in-out 150ms;
    z-index: 1;
  }

  .wrapper .tabs .btn::after {
    width: 99999px;
    height: 1px;
    position: absolute;
    bottom: -1px;
    left: 0;
    background-color: #d3d3d3;
    content: "";
  }

  .wrapper .tabs .active.btn::before {
    transform: scaleX(1);
  }

  .wrapper .content {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 10px;
    max-width: 920px;
    width: 90%;
    margin-inline: auto;
  }

  .wrapper .content .priorities .title {
    display: flex;
    justify-content: space-between;
    height: 18px !important;
  }

  .wrapper .content .priorities .title .add {
    border: none;
    height: 20px;
    width: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    cursor: pointer;
    background: none;
    color: #37352fa6;
  }

  .wrapper .content .priorities .list {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }

  .wrapper .content .priorities .list .item {
    border: 1px solid #e3e3e3;
    box-shadow: rgba(0, 0, 0, 0.05) 0px 1px 2px 0px;
    padding: 10px;
    border-radius: 5px;
    margin-block: 5px;
    font-size: 14px;
    font-weight: 300;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .wrapper .content .priorities .list .item:hover .del {
    display: block;
    background-color: #e3e3e3;
  }

  .wrapper .content .priorities .list .item .text {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  input {
    margin: 0 !important;
  }

  .wrapper .content .priorities .list .item .del {
    background: none;
    border: none;
    cursor: pointer;
    border-radius: 5px;
    display: none;
    color: #37352fa6;
    margin: 0;
  }

  .text {
    background: transparent;
    outline: none;
    width: 100%;
    border: none;
  }

  .wrapper .content .dump .area {
    width: 100%;
    outline: 1px solid #e3e3e3;
    box-shadow: rgba(0, 0, 0, 0.05) 0px 1px 2px 0px;
    border-radius: 5px;
    margin-block: 5px;
    outline: none;
    padding: 5px;
  }

  .wrapper .content .schedule .table {
    margin-block: 5px;
    width: 100%;
  }

  .wrapper .content .schedule td,
  th {
    border: 1px solid #dddddd;
  }
</style>
