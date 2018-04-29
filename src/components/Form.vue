<template>
  <div>
    <h3>
      Create Pixel Canvas
    </h3>
    <div>
      <h4>Choose Size (Max 20x20)</h4>
      <form id="size">
        <div>
          <div>
            Height
          </div>
          <input v-model="height" type="number" max="20" />
        </div>
        <div>
          <div>
            Width
          </div>
          <input v-model="width" type="number" max="20" />
        </div>
      </form>
      <button v-on:click="handleSubmitSize" class="submit">
        Submit
      </button>
    </div>
    <div v-if="isCanvasVisible">
      <h3>
        Design Canvas
      </h3>
      <h4>Choose Color</h4>
      <input v-model="color" type="color" value="color" />
    </div>
    <div id="canvas-wrapper">
      <table id="tableCanvas"></table>
    </div>
    <button v-if="isCanvasVisible" v-on:click="handleDownloadSvg">Download .SVG</button>
  </div>
</template>

<script>
import domtoimage from 'dom-to-image'

const generateArray = value =>
  [...Array(Number(value))]

const removeChildren = (el) => {
  while (el.firstChild) {
    el.removeChild(el.firstChild);
  }
}

function getInitialState() {
  return {
    height: 5,
    width: 5,
    isCanvasVisible: false,
    color: '#00e500'
  }
}

function handleSubmitSize() {
  const tableCanvas = document.getElementById('tableCanvas')

  removeChildren(tableCanvas)

  generateArray(this.height)
    .forEach(() => {
      const newTableRow = document.createElement('tr')

      generateArray(this.width)
        .forEach(() => {
          const newTableData = createTableData.call(this)

          newTableRow.appendChild(newTableData)
        })

        tableCanvas
          .appendChild(newTableRow)
    })

  this.isCanvasVisible = true
}

function createTableData() {
  const newTableData = document.createElement('td')

  newTableData.className = 'cell'
  newTableData.addEventListener('click', handleCellClick.bind(this))
  newTableData.addEventListener('dblclick', handleCellDoubleClick.bind(this))

  return newTableData
}

function handleCellDoubleClick(e) {
  e.currentTarget.style.backgroundColor = 'white'
}

function handleCellClick(e) {
  e.currentTarget.style.backgroundColor = this.color
}

function handleDownloadSvg() {
  domtoimage.toSvg(document.getElementById('tableCanvas'))
    .then(function (dataUrl) {
      const link = document.createElement('a');
      link.download = 'pixel-art.svg';
      link.href = dataUrl;
      link.click();
    });
}

export default {
  name: 'Form',
  data: getInitialState,
  methods: {
    handleSubmitSize,
    handleDownloadSvg
  }
}
</script>

<style>
h3 {
  margin: 40px 0 0;
  text-decoration: underline;
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

#size {
  display: flex;
  justify-content: center;
}

#size > div {
  margin: 10px;
  display: flex;
  flex-direction: column
}

.submit {
  width: 100px
}

#canvas-wrapper {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.cell {
  width: 20px;
  height: 20px;
  border: 1px solid black;
  padding: 2px;
  cursor: pointer;
}
</style>
