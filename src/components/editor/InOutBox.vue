<template>
  <div id="inoutbox" class="fsHide" v-bind:style="{ fontSize: this.$store.state.fontSize + 'px' }" v-show="this.$store.state.showInOutBox">
    <div class="panel-input panel-default">
      <div class="panel-heading">
        <span>Input</span>
        <label id="uploadInputFile" ><span class="fa fa-folder-open" style="margin-left: 5px" aria-hidden="true"></span>
          <input type="file" ref="inputFileUpload" style="display:none" @change="uploadInput">
        </label>
        <a v-on:click="onCopyInput" id="copy-input"> 
          <i class="fa fa-paperclip" />
        </a>
      </div>
      <textarea class="textbox" id="test-input" rows="2" wrap="off"
                placeholder="Specify Input" :value="this.$store.state.customInput"
                @change="customInputChange">
      </textarea>
    </div>
    <div class="panel-output panel-default">
      <div class="panel-heading">
        <span>Output</span>
        <button type="button" id="downloadOutput" class="btn btn-sm btn-menu" @click="downloadOutput()">
          <i class="fa fa-download" aria-hidden="true"></i>
        </button>
        <a v-on:click="onCopyOutput" id="copy-output"> 
          <i class="fa fa-paperclip"/>
        </a>
      </div>
      <pre id="output">{{this.$store.state.output}}</pre>
    </div>
  </div>
</template>

<script>
  import * as download from 'downloadjs'
  export default {
    name: 'inoutbox',
    mounted() {
      interact('#inoutbox')
        .resizable({
          edges: { top: true },
          restrictEdges: {
          outer: 'parent',
          endOnly: true,
        },
        restrictSize: {
          min: { height: 210 },
          max: { height: 510 }
        },
        inertia: true,
      })
      .on('resizemove', function (event) {
        const target = event.target,
          x = (parseFloat(target.getAttribute('data-x')) || 0),
          y = (parseFloat(target.getAttribute('data-y')) || 0)

        target.style.height = event.rect.height + 'px'

        target.style.webkitTransform = target.style.transform =
          'translate(' + x + 'px,' + y + 'px)'

        target.setAttribute('data-x', x)
        target.setAttribute('data-y', y)
      })
    },
    methods: {
      customInputChange(e) {
        this.$store.commit('changeCustomInput', e.target.value || e.target.result)
      },
      onCopyInput(e) {
        this.$copyText(this.$store.state.customInput).then((e) => {
          this.$notify({
            text: 'Input copied Successfully',
            type: 'success'
          })
        }, (e) => {
          this.$notify({
            text: 'Input could not be copied successfully',
            type: 'failure'
          })
          console.error(e)
        })
      },
      onCopyOutput(e) {
        this.$copyText(this.$store.state.output).then((e) => {
          this.$notify({
            text: 'Output copied Successfully',
            type: 'success'
          })
        }, (e) => {
          this.$notify({
            text: 'Output could not be copied successfully',
            type: 'failure'
          })
          console.error(e)
        })
      },
      uploadInput(e) {
        const files = e.target.files || e.dataTransfer.files
        if (!files.length) {
          return
        }
        const file = files[0]
        const reader = new FileReader()
        reader.onload = (e) => {
          console.log('Uploaded File: ' + file.name)
          this.$notify({
            text: 'Input Uploaded Successfully',
            type: 'success'
          })
          this.customInputChange(e)
          this.$refs.inputFileUpload.value = ""
        }
        reader.readAsText(file)
      },
      downloadOutput() {
        const output = this.$store.state.output;
        download(`data:text/plain;charset=utf-8,${encodeURIComponent(output)}`, "output.txt", 'text/plain')
      },
    }
  }
</script>

<style scoped>
  #inoutbox {
    position: fixed;
    width: 100vw;
    height: 210px;
    bottom: 0;
    left: 0;
    z-index: 20;
  }

  #output, #test-input {
    width: calc(50vw - 7px);
    border-radius: 0 !important;
    margin: 0;
    height: calc(100% - 50px);
    overflow: auto;
    background: #202020 !important;
    border: none;
    border-right: 1px solid #272727; 
    color: white !important;
  }

  #test-input, #output {
    resize: none;
    padding: 6px;
  }

  .panel-heading, .panel-input, .panel-output {
    width: calc(50vw - 7px);
    border-radius: 0;
    margin: 0;
  }

  .panel-heading {
    display: flex;
    align-items: center;
    height: 50px !important;
    padding: 8px 15px;
    background: #272727;
    color: #fff;
  }

  .panel-input, .panel-output {
    position: absolute;
    bottom: 0;
    display: inline-block;
    height: 100% !important;
    border-color: #202020;
  }

  .panel-output {
    right: 14px;
  }

   @media (max-width: 767px) {
    .panel-heading, .panel-input, .panel-output, #output, #test-input {
      width: calc(100vw - 14px);
    }

    .panel-input, .panel-output {
      position: static;
      height: 50% !important;
      border-color: #202020;
    }
  }

  i.fa:hover {
    cursor: pointer;
  }

  #uploadInputFile{
    margin: 0px 0px 0px auto;
    padding: 0 10px;
    cursor: pointer;
  } 
  #downloadOutput {
    margin-left: auto;
  }
</style>